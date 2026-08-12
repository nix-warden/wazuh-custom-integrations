# Wazuh Integration with DFIR-IRIS

Integration of **Wazuh SIEM/XDR** with **DFIR-IRIS** (Digital Forensics and Incident Response platform).

This module enables automatic forwarding of security alerts from the Wazuh Manager to the DFIR-IRIS platform via REST API for centralized incident management, event correlation, and tracking investigation progress.

This integration is built based on the official guide [Wazuh Blog: Enhancing incident response with Wazuh and DFIR-IRIS integration](https://wazuh.com/blog/enhancing-incident-response-with-wazuh-and-dfir-iris-integration/).

---

## 📐 Architecture & Workflow

```
+------------------+         Alert (JSON)        +-----------------------------------+
|  Wazuh Manager   | --------------------------> | /var/ossec/integrations/          |
|  (Rule Engine)   |                             | custom-wazuh_iris.py              |
+------------------+                             +-----------------------------------+
                                                                   |
                                                                   | HTTPS POST (Bearer Token)
                                                                   v
                                                 +-----------------------------------+
                                                 | DFIR-IRIS Platform                |
                                                 | REST API: /alerts/add             |
                                                 +-----------------------------------+
```

When an event matching specified filtering criteria (rule level, ID, or group) is triggered, the Wazuh Manager executes the `custom-wazuh_iris.py` integration script, passing the alert JSON file path, API key, and DFIR-IRIS endpoint URL. The script formats the data, maps the Wazuh alert level to IRIS severity, and dispatches an HTTP POST request.

---

## 📋 Requirements

- **Wazuh Manager** (v4.x or higher)
- **DFIR-IRIS** instance (running and network-accessible)
- Python `requests` module installed in the Wazuh Python environment (included by default at `/var/ossec/framework/python/bin/python3`)

---

## 🚀 Step-by-Step Setup

### Step 1. DFIR-IRIS Configuration

1. Log into your **DFIR-IRIS** web dashboard using an administrator account.
2. **User Permissions Setup:**
   - Navigate to **Advanced** > **Access control** and select the user account under which alerts will be ingested.
   - Go to the **Customers** tab, click **Manage**, add the target Customer account, and click **Save**.
   - *Note:* If using a non-admin account, ensure the user has `alert_read` and `alert_write` permissions to create new alerts via API.
3. **Obtain Customer ID:**
   - Navigate to **Advanced** > **Customers**.
   - Note the ID of your target customer displayed at the top of the page (by default, `IrisInitialClient` has an ID of `1`).
4. **Generate API Key:**
   - Click on your username in the top-right corner and select **My settings**.
   - Copy your **API Key**. Save it for the Wazuh Manager configuration step.

---

### Step 2. Deploy Integration Script on Wazuh Manager

1. Copy the integration script [custom-wazuh_iris.py](file:///Users/oleksandrtsyhanov/02_Areas/Git/wazuh-custom-integrations/custom-wazuh_iris.py) into `/var/ossec/integrations/` on your Wazuh Manager:

   ```bash
   cp custom-wazuh_iris.py /var/ossec/integrations/custom-wazuh_iris.py
   ```

2. Set proper ownership and permissions so the root user and wazuh group can execute it:

   ```bash
   chmod 750 /var/ossec/integrations/custom-wazuh_iris.py
   chown root:wazuh /var/ossec/integrations/custom-wazuh_iris.py
   ```

3. *(Optional)* Configure your Wazuh Dashboard URL and Customer ID inside the script:
   Open `custom-wazuh_iris.py` and modify the variables if needed:
   - `alert_source_link`: Set your Wazuh Dashboard IP/FQDN (default: `https://<IP ADDRESS>/app/wz-home`).
   - `alert_customer_id`: Default is `1`. Update this if your target IRIS customer ID is different.

---

### Step 3. Configure Wazuh Manager (`ossec.conf`)

Open `/var/ossec/etc/ossec.conf` on your Wazuh Manager and append the `<integration>` block inside `<ossec_config>`:

```xml
<ossec_config>

  <!-- DFIR-IRIS Integration -->
  <integration>
    <name>custom-wazuh_iris.py</name>
    <hook_url>https://<IRIS_IP_ADDRESS>/alerts/add</hook_url>
    <level>7</level>
    <api_key><IRIS_API_KEY></api_key>
    <alert_format>json</alert_format>
  </integration>

</ossec_config>
```

#### Configuration Options:

| Option | Description |
| :--- | :--- |
| `<name>` | The integration script file name (`custom-wazuh_iris.py`). |
| `<hook_url>` | DFIR-IRIS alert endpoint URL (e.g., `https://192.168.1.2/alerts/add` or `https://192.168.1.2:8000/alerts/add`). |
| `<level>` | Minimum Wazuh rule level (0–15) to trigger the integration (example: `7` and higher). |
| `<api_key>` | DFIR-IRIS user API key obtained in Step 1. |
| `<alert_format>` | Data payload format (must be `json`). |

> **Tip:** You can also restrict alert forwarding to specific rule groups or rule IDs using `<group>` or `<rule_id>` tags. Refer to the [Wazuh Integration Manual](https://documentation.wazuh.com/current/user-manual/manager/manual-integration.html) for more details.

---

### Step 4. Restart Wazuh Manager

Apply changes by restarting the Wazuh Manager service:

```bash
systemctl restart wazuh-manager
```

---

## 📊 Severity Mapping

The integration script maps Wazuh rule levels (0–15) to DFIR-IRIS severity IDs (1–6):

| Wazuh Rule Level | DFIR-IRIS Severity ID | DFIR-IRIS Severity Level |
| :---: | :---: | :--- |
| **0 – 4** | `2` | Low |
| **5 – 6** | `3` | Medium |
| **7 – 9** | `4` | High |
| **10 – 12** | `5` | Very High |
| **>= 13** | `6` | Critical |
| Other | `1` | Informational |

---

## 🔍 Verification & Troubleshooting

1. **Monitor Integration Logs:**
   To verify alert delivery or troubleshoot issues, inspect the Wazuh integrations log file:

   ```bash
   tail -f /var/ossec/logs/integrations.log
   ```

   Upon successful transmission, you will see log entries like:
   ```text
   YYYY-MM-DD HH:MM:SS INFO: Sent alert to IRIS. Response status code: 200
   ```

2. **Verify Alerts in DFIR-IRIS:**
   Open the **DFIR-IRIS** dashboard and navigate to the **Alerts** page. When a Wazuh alert with level `>= 7` fires, a new alert entry will appear with a status of **New**.

3. **Common Issues & Fixes:**
   - **`HTTP 401 / 403`**: Verify the API key in `ossec.conf` and confirm that the IRIS user has `alert_read` and `alert_write` permissions.
   - **`HTTP 400` / Invalid Customer ID**: Check `alert_customer_id` in `custom-wazuh_iris.py` and ensure the IRIS user is mapped to that customer.
   - **SSL Certificate Errors**: The script sets `verify=False` by default to allow connections with self-signed SSL certificates.

---

## 🔗 References

- [Wazuh Blog: Enhancing incident response with Wazuh and DFIR-IRIS integration](https://wazuh.com/blog/enhancing-incident-response-with-wazuh-and-dfir-iris-integration/)
- [DFIR-IRIS Documentation](https://dfir-iris.org/)
- [Wazuh Integrations Documentation](https://documentation.wazuh.com/)

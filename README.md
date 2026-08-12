# Wazuh Vendor Integrations Aggregator

A curated collection of verified third-party integrations for **Wazuh SIEM/XDR**, sourced from official vendor resources (the official [Wazuh Blog](https://wazuh.com/blog/), official documentation, and official repositories of integrated platforms such as MISP, DFIR-IRIS, etc.).

This repository acts as a structured hub for vendor-published integration scripts and step-by-step deployment guides. Each integration is self-contained within its own directory, featuring the integration script(s) and detailed documentation covering architecture, setup, configuration, and troubleshooting.

---

## 🎯 Scope & Guidelines

- **Vendor-Sourced Materials**: This repository aggregates integrations maintained either by the official Wazuh team or by the official maintainers of target security platforms (e.g., MISP Project).
- **No Unverified Custom Integrations**: Community or unofficial user scripts without official vendor backing or documentation are out of scope for this repository.
- **Community Contributions**: Pull requests adding new integrations from official Wazuh or vendor repositories are welcome.

---

## 📁 Available Integrations

| Integration | Category | Target Platform | Primary Source | Description | Guide |
| :--- | :--- | :--- | :--- | :--- | :--- |
| [`dfir-iris`](./dfir-iris/) | Incident Response | [DFIR-IRIS](https://dfir-iris.org/) | [Wazuh Blog](https://wazuh.com/blog/enhancing-incident-response-with-wazuh-and-dfir-iris-integration/) | Forward Wazuh alerts to DFIR-IRIS via REST API for incident management. | [README](./dfir-iris/README.md) |

---

## 📂 Repository Structure

Each integration is maintained in a dedicated directory:

```text
.
├── LICENSE
├── README.md                  # Root repository documentation
└── <integration-name>/        # Dedicated directory for an integration
    ├── README.md              # Detailed setup guide, architecture & workflow
    └── custom-<script_name>.py # Integration script (e.g., custom-wazuh_iris.py)
```

---

## 🤝 Contributing

Contributions that package official Wazuh or vendor-backed integrations are welcome!

### Contribution Rules:
1. **Official Source**: The integration MUST be based on an official article or repository from Wazuh or the target platform vendor.
2. **Directory Naming**: Use a lowercase, hyphenated folder name representing the target service (e.g., `dfir-iris`, `misp`, `shuffle`).
3. **Integration Code**: Include the verified integration script (e.g., `custom-<name>.py`).
4. **Documentation**: Each folder MUST contain a comprehensive `README.md` containing:
   - Link to the original blog article or official repository.
   - Architecture diagram & workflow description.
   - Prerequisites & step-by-step installation instructions.
   - `ossec.conf` configuration block examples.
   - Alert/field mapping table.
   - Troubleshooting and log verification steps.

---

## 📜 License

This repository is licensed under the [MIT License](LICENSE).


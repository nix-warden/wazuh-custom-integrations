# Wazuh Vendor Integrations Aggregator

A curated collection of official third-party integrations for **Wazuh SIEM/XDR**, sourced from the official [Wazuh Blog](https://wazuh.com/blog/).

This repository acts as a structured hub for vendor-published integration scripts and step-by-step deployment guides. Each integration is self-contained within its own directory, featuring the integration script(s) and detailed documentation covering architecture, setup, configuration, and troubleshooting.

---

## 🎯 Scope & Guidelines

- **Vendor-Sourced Materials**: This repository exclusively aggregates integrations published by the official Wazuh team on their blog.
- **No Custom Integrations**: Custom or non-official user integrations are out of scope for this repository.
- **Community Contributions**: Pull requests adding new integrations from official Wazuh blog posts are welcome.

---

## 📁 Available Integrations

| Integration | Category | Target Platform | Description | Guide |
| :--- | :--- | :--- | :--- | :--- |
| [`dfir-iris`](./dfir-iris/) | Incident Response | [DFIR-IRIS](https://dfir-iris.org/) | Forward Wazuh alerts to DFIR-IRIS via REST API for incident management. | [README](./dfir-iris/README.md) |

---

## 📂 Repository Structure

Each integration is maintained in a dedicated directory:

```text
.
├── LICENSE
├── README.md                  # Root repository documentation
└── <integration-name>/        # Dedicated directory for a blog integration
    ├── README.md              # Detailed setup guide, architecture & workflow
    └── custom-<script_name>.py # Integration script (e.g., custom-wazuh_iris.py)
```

---

## 🤝 Contributing

Contributions that package official Wazuh blog integrations are welcome!

### Contribution Rules:
1. **Official Source**: The integration MUST be based on an official article from the [Wazuh Blog](https://wazuh.com/blog/).
2. **Directory Naming**: Use a lowercase, hyphenated folder name representing the target service (e.g., `dfir-iris`, `shuffle`, `misp`).
3. **Integration Code**: Include the verified integration script (e.g., `custom-wazuh_<name>.py`).
4. **Documentation**: Each folder MUST contain a comprehensive `README.md` containing:
   - Link to the original Wazuh blog article.
   - Architecture diagram & workflow description.
   - Prerequisites & step-by-step installation instructions.
   - `ossec.conf` configuration block examples.
   - Alert severity mapping table.
   - Troubleshooting and log verification steps.

---

## 📜 License

This repository is licensed under the [MIT License](LICENSE).

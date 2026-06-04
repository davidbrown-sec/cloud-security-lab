# Cloud Security Lab

Hands-on cloud security projects across Azure and AWS, built as part of a structured cybersecurity curriculum. Each project documents real implementation work including configuration, integration, and validation.

---

## Projects

### Azure

| Project | Description | Skills |
|---|---|---|
| [Azure/Entra → Splunk Integration](./azure/entra-splunk-integration.md) | Stream Entra ID audit and sign-in logs to Splunk via Azure Event Hub | Event Hub, Diagnostic Settings, App Registrations, RBAC, Splunk Add-on |

### AWS

| Project | Description | Skills |
|---|---|---|
| [AWS IAM & Access Control](https://github.com/davidbrown-sec/AWS-IAM) | IAM policies, tag-based access control, least-privilege validation | IAM, EC2, Policy Simulator |
| [CloudTrail → Splunk Integration](https://github.com/davidbrown-sec/AWS-IAM/blob/main/splunk-cloudtrail-iam-setup.md) | Ingest CloudTrail management events into Splunk via S3 | CloudTrail, S3, IAM, Splunk AWS TA |

---

## Environment

These projects are built and tested in a home lab environment running:

- **SIEM:** Splunk Enterprise
- **Cloud:** Microsoft Azure / Entra ID, AWS
- **Supporting infrastructure:** Active Directory, Linux VMs, network traffic analysis (Malcolm/Zeek/Suricata)

---

## Related Repositories

- [AWS-IAM](https://github.com/davidbrown-sec/AWS-IAM) — AWS identity and access management projects
- [homelab](https://github.com/davidbrown-sec/homelab) *(private)* — Full lab infrastructure documentation

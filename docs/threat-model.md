# Orion Lab Threat Model

## Purpose and scope

Identify what could go wrong in the Orion Azure lab, explain how security controls reduce risk, and record what remains untested.

This assessment includes resources tested earlier and later delete to control costs. It does not imply that all resources remain deployed.

Only synthetic data and secrets were used.

## Assets

- Blob files and containers
- Key Vault secrets
- Managed identites and roles assignments
- Azure resource configurations
- Github documentation and screenshots

## Trust boundaries

Crossing a trust boundary requires access checks. Being insde a VNet does not automatically make a workload trustworthy.

Boundary                        |       Relevant controls
Internet to web subnet             NSG inbound rules
Web subnet to app subnet           NSG permits TCP 8080 from web
App subnet to data subnet          NSG permits TCP 1433 from app
Workload to Blob Storage           Private endpoint, public access disabled, identity and RBAC
User to Key Vault secrets          Network restrictions and data-pane RBAC
Local evidence to public GitHub    Review for secrets before publishing

## 1. Compromised application modifies stored files

**Threat:** An attacker controlling an application uses its identity to overwrite or delete blobs.

**Control:** Give a download-only application Storage Blob Data Reader, scope to the container it needs.

**Evidence:** The storage lab tested upload and download with a managed identity using Storage Blob Data Contributor. The separate RBAC lab proved that a Reader identity could read resource-group information but could not modify a tag.

**Limitation:** Container-cope Blob Data Reader was discussed as a design improvments, not implemented and tested in the storage lab. Resource-group Reader and Blob Data Reader are different roles.

**Residual risk:** A compromised application can still misuse its legitimate read access to streal files.

## 2. Secret exposed through GitHub

**Threat:** A real secret appears in a committed screenshot or file.

**Controls:** Use synthetic values, hide secret values in screenshots, and review changes before committing.

**Evidence:** The Key Vault exercise used a fake secret and screenshots were captured without exposing its value.

**Response to a real exposure:** Revoke or rotate the credential first, then remove exposed content fromm repository history.

**Residual risk: Delete a files in a new commit does not erase older commits or copies someone already downloaded.

## 3. Accidentatl or malicious secret deletion

**Threat:** Deletion makes a required secret unavailable to an application.

**Control:** Key Vault soft delete provides a recovery windows.

**Evidence:** A synthetic secret was deleted and recovered. The configured retention period was seven days.

**Limitation:** Purge protection was disabled for this disposable lab. No application outage or recovery time was tested.

**Residual risk:** An application may fail until recovery. Someone with purge permission could permanently delete the soft-deleted secrete.
Purge protection would prevent purging duing the rentention period.

## 4. Compromised web workload reaches the data tier

**Threat:** An attacker on a web VM attemps a direct connection to the data subnet on TCP 1433.

**Control:** nsg-data allows TCP 1433 from the app subnet at priority 100. Deny-All-Other-Inbound at priority 200 denies other inbound traffic, including direct web-to-data traffic

**Evidence:** The configured NSG rules are documented in the network lab.

**Limitation:** Luve web-to-app and app-to-database traffic tests were not performed. No real database was deployed

**Residua; risk:** A compromised app workload matches the allowed network path. Data base authentication and limited database permissions would also be needed.

## 5. Resources lack required environment tags

**Threat:** Missing tags make resource ownership, environment tracking, an d cleanup harder to manage.

**Controll:** A custome Azure Policy Audit assignment detects resources missing the environment tag in rg-orion-dev-eus-01

**Evidence:** Evaluation identified missing tags. A deliberate tag removal from the managed identity produced non-compliance. After correction, all eight evaluated resources were compliant

**Residual risk:** Audit does not block changes or automatically fix tags. This policy checks tag existence, not whether its value is valid.

## Evident references

- [Network design and storage tests](network-design.md)
- [Identity and RBAC tests](rbac-matrix.md)
- [Key Vault deletion and recovery](key-vault.md)
- [Azure Policy compliance tests](azure-policy.md)

## Overall limitations

This is a learning lab, not a production security assesment.

Controls reduce specific risks; they do not eliminate every attack.
A compliant tag policy does not prove that a resource is secure.
COnfiguration evidence is distinguised from live test results.

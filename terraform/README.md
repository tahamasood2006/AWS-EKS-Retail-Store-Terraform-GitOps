# Retail Store Terraform Infrastructure


## 📁 File Structure

```
terraform-organized/
├── main.tf                    # Primary infrastructure (VPC, EKS)
├── variables.tf               # Input variables
├── outputs.tf                 # Output values
├── versions.tf                # Provider requirements and configurations
├── locals.tf                  # Local values and data sources
├── security.tf                # Security groups and rules
├── addons.tf                  # EKS add-ons (NGINX, cert-manager)
├── argocd.tf                  # ArgoCD installation
└── README.md                  # This file
```

```bash
terraform destroy
```

**Note**: This will delete all resources including the EKS cluster and VPC. Make sure to backup any important data first.


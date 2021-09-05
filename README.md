# GitHub Action for Azure Web App certificate deployment

Deploy SSL certificate to Azure Web App (including Function App).

Supports multiple deployment slots (will be handled automatically) and multiple custom domains (make sure your certificate includes ALL custom domains in SAN).

# Usage

> If you need to issue SSL certificates automatically, you can use [my acme.sh action](https://github.com/marketplace/actions/issue-ssl-certificate).
>
> If you want to upload SSL certificates to Azure Key Vault, just write a one-liner `azure/CLI` command.

**Note**: Please login to Azure with `azure/login` before using this action. See [`azure/login`'s documentation](https://github.com/marketplace/actions/azure-login) for more info.

```yaml
jobs:
  deploy-to-webapp:
    name: Deploy certificate to Azure Web App
    runs-on: ubuntu-latest
    steps:
      - name: Check out
        uses: actions/checkout@v2
        with:
          # If you just commited and pushed your newly issued certificate to this repo in a previous job,
          # use `ref` to make sure checking out the newest commit in this job
          ref: ${{ github.ref }}
      - name: Login to Azure
        uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
      - uses: Menci/deploy-certificate-to-azure-web-app@beta-v1
        with:
          azcliversion: latest                               # Omit to use 'latest'.
          subscription: 123e4567-e89b-42d3-a456-556642440000 # Omit if you have set the default subscription.
          resource-group: MyResourceGroup                    # Omit if you have set the default resource group.
          webapp-name: my-awesome-webapp
          certificate-file: my/ssl/certificate.pfx
          certificate-password: ${{ secrets.PFX_PASSWORD }}
          delete-old-certificates: true                      # `false` to not deleting old certificates. Don't omit.
```

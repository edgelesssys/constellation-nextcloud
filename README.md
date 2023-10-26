# General

Deploys the nextcloud helm [chart](https://artifacthub.io/packages/helm/nextcloud/nextcloud).
Also sets up TLS and DNS via nginx, external-dns and Let's Encrypt (cert-manager).
*Warning:* This setup is not fit for production. It uses nextcloud's builtin SQLite DB that should only be used for testing: https://artifacthub.io/packages/helm/nextcloud/nextcloud#database-configurations.

# Deploy

1. Replace the values in `.env` with your Godaddy API credentials and a fitting owner ID. The owner ID is used by external-dns to differentiate the DNS entries from different clusters at your DNS provider (GoDaddy). So you should use a different value for every cluster you use.
2. Replace `your.domain` of `nextcloud.testing.your.domain` in `values.yaml` with a domain you own. You can also change the subdomain if you want.
3. Run:
```bash
source .env
helm dependency update ./nextcloud
helm upgrade nextcloud-infra ./nextcloud --install --namespace default --set apiKey=$GODADDY_API_KEY --set secretKey=$GODADDY_SECRET_KEY --set external-dns.txtOwnerID=$OWNER_ID --set infra.enabled=true

helm upgrade nextcloud ./nextcloud --install --namespace nextcloud --create-namespace --set nextcloud.nextcloud.password="somesecretadminpw" --set tlsCertEmail="you@example.com" --set app.enabled=true
```
4. Wait ~5min for everything to be setup.
5. Browse to `https://nextcloud.testing.your.domain`

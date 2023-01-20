---
title: Zone Lockdown
pcx_content_type: how-to
weight: 8
meta:
  title: Health Checks
---

# Zone Lockdown migration guide

Currently, any Cloudflare customer on a paid plan can configure Health Checks against any host or IP. Zone Lockdown specifies a list of one or more IP addresses, CIDR ranges, or networks that are the only IPs allowed to access a domain, subdomain, or URL. It allows multiple destinations in a single rule as well as IPv4 and IPv6 addresses. IP addresses not specified in the Zone Lockdown rule are denied access to the specified resources.

When a customer enables zone lockdown, any Health Checks targeting that zone regardless of ownership will still get through because Cloudflare's ASN is on an allow-list.

Customers may encounter that Cloudflare’s ASN is whitelisted for zone lockdown, resulting in Health Checks targeting that zone to fail. To mitigate that vulnerability, customers can create a firewall rule to bypass the zone lockdown.

## Bypass zone lockdown

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com) and select your account and domain.
2. Go to **Security** > **WAF** > **Firewall Rules**.
3. Select **Create firewall rule**.
4. Create a firewall rule matching on **user agent**.
5. Set the action to **Bypass** and the corresponding feature to **Zone Lockdown**.

Cloudflare Health Checks have a user agent of the following format: 
`Mozilla/5.0 (compatible;Cloudflare-Healthchecks/1.0;"+https://www.cloudflare.com/; healthcheck-id: XXX)` where `XXX` is replaced with the first 16 characters of the Health Check ID.

To allow a specific Health Check, verify if the user agent contains the first 16 characters of the Health Check ID.

### Via the API

```json

curl -X POST "https://api.cloudflare.com/client/v4/zones/023e105f4ecef8ad9ca31a8372d0c353/firewall/rules" \
     -H "X-Auth-Email: user@example.com" \
     -H "X-Auth-Key: c2547eb745079dac9320b638f5e225cf483cc5cfdda41" \
     -H "Content-Type: application/json" \
     --data '[{ "description": "bypass zone lockdown - specific healthcheck","action": "bypass","products": ["zoneLockdown"],"filter": {"expression": "(http.user_agent contains \"1234567890abcdef\")"}}]

``` 
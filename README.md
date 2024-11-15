# Synology NAS configuration

Checklist:

- Disable default admin and guest users.
- Create a new local admin user and set up MFA.
- Go to **Security Advisor** > **Advanced** > select `For work and business`.
- Configure the device as per the recommendations.

## OIDC SSO with Keycloak

I elected to stick with Keycloak and not use Synology SSO Service since the unavailability of a the NAS would hobble my whole homelab.

### Create the OIDC client

In Keycloak:

1. Select your realm > **Clients** > **Create client**:
   Name | Value
   -- | --
   Client type | `OpenID Connect`
   Client ID | `synology-nas`
   Root URL | `https://{NAS FQDN}:{NAS port}`
   Home URL | `https://{NAS FQDN}:{NAS port}`
   Valid redirect URIs | `https://{NAS FQDN}:{NAS port}`

### Configure SSO

On the Synology NAS:

1. Navigate to **Control Panel** > **Domain/LDAP** > **SSO Client**
1. Enable **SSO by default** and **OpenID Connect SSO service**
1. Click **OpenID Connect SSO Settings** and fill in the table as follows:
   Name | Value
   -- | --
   Profile | `OIDC`
   Account type | `Domain/LDAP/local`
   Name | Whatever you call your authentication domain
   Well-known URL | `https://{Keycloak FQDN}}/realms/{your realm name}/.well-known/openid-configuration`
   Application ID | `synology-nas`
   Application secret | Paste the value from Keycloak **Clients** > **synology** > **Credentials** > **Client secret**
   Redirect URI | `https://{NAS FQDN}:{NAS https port}`
   Authorization scope | `openid profile`
   Username claim | `preferred_username`

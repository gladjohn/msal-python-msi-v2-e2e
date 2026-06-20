# MSI v2 mTLS PoP — Python E2E Test

## Quick Start (on KeyGuard Azure VM)

`ash
pip install requests
python run_e2e.py
`

## What it does

1. Calls obtain_token() — creates KeyGuard key, gets IMDS cert, acquires mTLS PoP token
2. Returns inding_certificate (WindowsCertificate — like .NET X509Certificate2)
3. Uses SchannelSession (separate transport) to call AKV over mTLS
4. Verifies token binding (cnf.x5t#S256 matches cert)

## Environment Variables (optional)

- MSI_V2_RESOURCE — resource URI (default: https://vault.azure.net)
- MSI_V2_VAULT_URL — AKV URL (default: https://msidlabvault.vault.azure.net)
- MSI_V2_SECRET_NAME — secret to read (default: 	est-secret)

## Architecture

`
MSAL (obtain_token)
  → token + binding_certificate (WindowsCertificate)

App developer (SchannelSession — separate package)
  → downstream mTLS GET to AKV with same cert
`

MSAL never makes downstream calls. The cert handle is the developer's to use.

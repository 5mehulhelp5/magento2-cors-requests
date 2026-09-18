# Magento 2 CORS Requests — maintained fork (SISL)

Enables **CORS (Cross-Origin Resource Sharing)** for the Magento 2 API (REST / webapi), so a
headless / PWA app or an external frontend on a different domain can call the store API straight
from the browser. The module adds the `Access-Control-Allow-Origin`,
`Access-Control-Allow-Credentials` and `Access-Control-Max-Age` headers (plus
`AMP-Access-Control-Allow-Source-Origin`) and handles preflight `OPTIONS` requests.

This is a **maintained fork** of the abandoned `creatuity/magento-2-cors-requests` (last upstream
commit: 2023). The original declares `magento/framework: *` and `php: ^8.1` — it installs
"silently", but was never tested or supported on Magento **2.4.9 / PHP 8.4**. This fork is verified
on 2.4.9: `setup:di:compile` passes and the CORS headers are confirmed with a real request against
a live REST API.

## Compatibility
- Magento **2.4.4 – 2.4.9** (Open Source / Adobe Commerce)
- PHP **8.1 – 8.4**
- `magento/framework >=103.0.4 <104`

## Installation

```bash
composer require sisl-source/magento2-cors-requests
bin/magento module:enable Creatuity_CorsRequests
bin/magento setup:upgrade
bin/magento setup:di:compile   # production mode
```

## Configuration

**Stores → Configuration → General → Web → CORS Requests Configuration:**

| Field | Description |
|------|------|
| **CORS Origin Url** | `*` or a full URL without a trailing `/` (e.g. `https://headless.yourstore.com`). The value is sent in the `Access-Control-Allow-Origin` header. |
| **CORS Allow Credentials** | `Yes` → adds `Access-Control-Allow-Credentials: true` (cross-domain cookies). |
| **CORS Requests for AMP** | `Yes` → adds `AMP-Access-Control-Allow-Source-Origin`. |
| **CORS Request Max Age** | Number of seconds for the `Access-Control-Max-Age` header (preflight cache). |

After changing the configuration, flush the cache (`bin/magento cache:flush`).

> **Security:** `*` combined with `Allow Credentials` is rejected by browsers and is risky — in
> production set a specific origin, not a wildcard.

## How it works
- `Creatuity\CorsRequests\Plugin\CorsHeadersPlugin` — `beforeDispatch` on `Magento\Webapi\Controller\Rest`, adds the headers based on the configuration.
- `CorsRequestOptionsPlugin` — lets the `OPTIONS` method through (jQuery/fetch preflight).
- `CorsRequestMatchPlugin` — for an `OPTIONS` preflight returns a stand-in route instead of a 404 / "request method invalid" error.

## License
OSL-3.0 / AFL-3.0 (same as upstream). Fork maintained by [SISL](https://sisl.pl).

---

### Maintained by SISL

Maintained fork by **[SISL](https://sisl.pl)** — [Magento 2 development and modules](https://sisl.pl/moduly-magento). More self-hosted plugins: [SISL Marketplace](https://sisl.pl/sklep).
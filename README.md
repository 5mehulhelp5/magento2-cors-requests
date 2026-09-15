# Magento 2 CORS Requests — utrzymywany fork (SISL)

Włącza obsługę **CORS (Cross-Origin Resource Sharing)** dla API Magento 2 (REST / webapi),
żeby aplikacja headless / PWA / zewnętrzny front z innej domeny mógł wołać API sklepu
prosto z przeglądarki. Moduł dokłada nagłówki `Access-Control-Allow-Origin`,
`Access-Control-Allow-Credentials`, `Access-Control-Max-Age` (oraz `AMP-Access-Control-Allow-Source-Origin`)
i obsługuje żądania preflight `OPTIONS`.

To **utrzymywany fork** porzuconego `creatuity/magento-2-cors-requests` (ostatni commit
upstream: 2023). Oryginał deklaruje `magento/framework: *` i `php: ^8.1` — wchodzi „po cichu",
ale nie był testowany ani wspierany pod Magento **2.4.9 / PHP 8.4**. Ten fork jest zweryfikowany
na 2.4.9: `setup:di:compile` przechodzi, a nagłówki CORS potwierdzone realnym żądaniem do żywego
REST API.

## Zgodność
- Magento **2.4.4 – 2.4.9** (Open Source / Adobe Commerce)
- PHP **8.1 – 8.4**
- `magento/framework >=103.0.4 <104`

## Instalacja

```bash
composer require sisl-source/magento2-cors-requests
bin/magento module:enable Creatuity_CorsRequests
bin/magento setup:upgrade
bin/magento setup:di:compile   # tryb produkcyjny
```

## Konfiguracja

**Sklep → Konfiguracja → Ogólne → Web → CORS Requests Configuration:**

| Pole | Opis |
|------|------|
| **CORS Origin Url** | `*` albo pełny URL bez końcowego `/` (np. `https://headless.twojsklep.pl`). Ustawiana wartość trafia do nagłówka `Access-Control-Allow-Origin`. |
| **CORS Allow Credentials** | `Yes` → dokłada `Access-Control-Allow-Credentials: true` (ciasteczka między domenami). |
| **CORS Requests for AMP** | `Yes` → dokłada `AMP-Access-Control-Allow-Source-Origin`. |
| **CORS Request Max Age** | Liczba sekund do nagłówka `Access-Control-Max-Age` (cache preflightu). |

Po zmianie konfiguracji wyczyść cache (`bin/magento cache:flush`).

> **Bezpieczeństwo:** `*` przy jednoczesnym `Allow Credentials` jest odrzucane przez przeglądarki
> i ryzykowne — do produkcji podawaj konkretny origin, nie gwiazdkę.

## Jak to działa
- `Creatuity\CorsRequests\Plugin\CorsHeadersPlugin` — `beforeDispatch` na `Magento\Webapi\Controller\Rest`, dokłada nagłówki na podstawie konfiguracji.
- `CorsRequestOptionsPlugin` — przepuszcza metodę `OPTIONS` (preflight jQuery/fetch).
- `CorsRequestMatchPlugin` — dla preflightu `OPTIONS` zwraca zastępczą trasę zamiast błędu 404/„request method invalid".

## Licencja
OSL-3.0 / AFL-3.0 (jak oryginał). Fork utrzymywany przez [SISL](https://sisl.pl).

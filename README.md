# Dokumentacja Integracji - System Autoryzacji iFrame

## Spis treści
- [Wstęp](#wstęp)
- [Proces autoryzacji](#proces-autoryzacji)
- [Szczegóły techniczne](#szczegóły-techniczne)
- [Specyfikacja techniczna](#specyfikacja-techniczna)
- [Przykład implementacji](#przykład-implementacji)
- [Bezpieczeństwo](#bezpieczeństwo)
- [Obsługa błędów](#obsługa-błędów)
- [Wsparcie techniczne](#wsparcie-techniczne)

## Wstęp
Dokument opisuje proces integracji i autoryzacji dostępu do iFrame poprzez system tokenów sesji. Rozwiązanie zapewnia bezpieczny dostęp tylko dla autoryzowanych użytkowników pochodzących ze strony klienta.

## Proces autoryzacji
1. Pobranie tokenu sesji
2. Przekazanie tokenu w URL
3. Weryfikacja tokenu w aplikacji

## Szczegóły techniczne

### 1. Endpoint autoryzacyjny
- URL: https://europe-west3-vivid-mind.cloudfunctions.net/getSessionId
- Metoda: POST
- Content-Type: application/json

### 2. Format zapytania
```json
{
    "clientToken": "YOUR_CLIENT_TOKEN"
}
```
gdzie YOUR_CLIENT_TOKEN to unikalny token przypisany do Państwa firmy.

### 3. Format odpowiedzi
Sukces (HTTP 200):
```json
{
    "token": "SESSION_TOKEN"
}
```

Błąd (HTTP 400/401/500):
```json
{
    "error": "Error message"
}
```

### 4. Przekazywanie tokenu
Token sesji należy dołączyć do URL iFrame w następujący sposób:
```
https://iframe-url-address?session_token=SESSION_TOKEN
```

### Specyfikacja techniczna
- Czas ważności tokenu: 1 godzina
- Format tokenu: JWT (JSON Web Token)
- Wymagane nagłówki: Content-Type: application/json

### Przykład implementacji

```javascript
async function getSessionToken() {
    const response = await fetch('https://europe-west3-memeo-health.cloudfunctions.net/getSessionId', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            clientToken: 'YOUR_CLIENT_TOKEN'
        })
    });
    
    const data = await response.json();
    return data.token;
}


function createIframeUrl(sessionToken) {
    return `https://iframe-url-address?session_token=${sessionToken}`;
}
```

### Bezpieczeństwo
- Token klienta (clientToken) powinien być przechowywany w bezpieczny sposób
- Każdy token sesji jest ważny tylko przez 1 godzinę
- Tokeny są unikalne
- Weryfikacja tokenu odbywa się po stronie aplikacji

### Obsługa błędów
1. HTTP 400 - Nieprawidłowy format danych
2. HTTP 401 - Nieprawidłowy token klienta
3. HTTP 500 - Wewnętrzny błąd serwera

### Wsparcie techniczne
W przypadku problemów z integracją prosimy o kontakt z zespołem wsparcia technicznego:
[Email Address](mailto:contact@vividmind.health)

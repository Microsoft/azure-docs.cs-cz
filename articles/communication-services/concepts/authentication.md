---
title: Ověřování ve službě Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Seznamte se s různými způsoby, kterými se aplikace nebo služba může ověřit pro komunikační služby.
author: GrantMeStrength
manager: jken
services: azure-communication-services
ms.author: jken
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: e20c822c2e792c67ed655080385a3c90794d53fd
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545135"
---
# <a name="authenticate-to-azure-communication-services"></a>Ověřování ve službě Azure Communication Services

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Tento článek poskytuje informace o ověřování klientů do komunikačních služeb Azure pomocí *přístupových klíčů* a *tokenů přístupu uživatelů*. Všechny interakce klientů s komunikačními službami Azure musí být ověřené.

Následující tabulka popisuje, které možnosti ověřování jsou podporovány klientskými knihovnami služby Azure Communication Services:

| Klientská knihovna | Přístupový klíč    | Tokeny přístupu uživatele |
| -------------- | ------------- | ------------------ |
| Správa | Podporováno     | Nepodporuje se      |
| SMS            | Podporováno     | Nepodporuje se      |
| Chat           | Nepodporuje se | Podporováno          |
| Videohovory        | Nepodporuje se | Podporováno          |

Každá možnost autorizace je stručně popsána níže:

- **Přístupové ověřování klíčů** pro operace SMS a správy. Ověřování přístupového klíče je vhodné pro aplikace běžící v prostředí důvěryhodné služby. Pro ověřování pomocí přístupového klíče klient generuje [kód pro ověřování zpráv (HMAC) založený na hodnotě hash (HMAC)](https://en.wikipedia.org/wiki/HMAC) a obsahuje ho v `Authorization` HLAVIČCE jednotlivých požadavků HTTP. Další informace najdete v tématu [ověřování pomocí přístupového klíče](#authenticate-with-an-access-key).
- Ověřování **uživatelského přístupového tokenu** pro chat a volání. Tokeny přístupu uživatele umožňují klientským aplikacím ověřování přímo proti komunikačním službám Azure. Tyto tokeny se generují ve službě zřizování tokenů na straně serveru, kterou vytvoříte. Pak jsou k dispozici pro klientská zařízení, která používají token k inicializaci chatu a volání klientských knihoven. Další informace najdete v tématu [ověřování pomocí přístupového tokenu uživatele](#authenticate-with-a-user-access-token).

## <a name="authenticate-with-an-access-key"></a>Ověřování pomocí přístupového klíče

Ověřování pomocí přístupového klíče používá sdílený tajný klíč k vygenerování HMAC pro každý požadavek HTTP vypočítaný pomocí algoritmu SHA256 a pošle ho v `Authorization` hlavičce pomocí `HMAC-SHA256` schématu.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

Klientské knihovny služby Azure Communication Services, které používají ověřování pomocí přístupového klíče, by měly být inicializované pomocí připojovacího řetězce prostředku. Pokud nepoužíváte klientskou knihovnu, můžete programově generovat HMAC pomocí přístupového klíče prostředku. Další informace o připojovacích řetězcích najdete v [rychlém startu k zřizování prostředků](../quickstarts/create-communication-resource.md).

### <a name="sign-an-http-request"></a>Podepsat požadavek HTTP

Pokud nepoužíváte klientskou knihovnu k provádění požadavků HTTP na rozhraní REST API služby Azure Communication Services, budete muset programově vytvořit HMAC pro každý požadavek HTTP. Následující postup popisuje, jak vytvořit autorizační hlavičku:

1. Zadejte koordinovaný světový čas (UTC) pro požadavek v `x-ms-date` hlavičce nebo v hlavičce Standard http `Date` . Tato služba je ověřuje, aby se zabránilo určitým útokům na zabezpečení, včetně útoků na opakování.
1. Vyhodnotí tělo požadavku HTTP pomocí algoritmu SHA256 a pak ho předejte přes `x-ms-content-sha256` hlavičku.
1. Vytvořte řetězec, který bude podepsán zřetězením příkazu HTTP (např. `GET` nebo `PUT` ), cesty požadavku HTTP a hodnot `Date` hlaviček a `Host` `x-ms-content-sha256` protokolu HTTP v následujícím formátu:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Vygenerujte podpis HMAC-256 řetězce kódované v kódování UTF-8, který jste vytvořili v předchozím kroku. Potom zakódovat výsledky jako base64. Všimněte si, že je také nutné, abyste si přístupový klíč kódovaného kódu base64. Použijte následující formát (zobrazený jako pseudo kód):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_access_key>)))
    ```
1. Hlavičku autorizace zadejte následujícím způsobem:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Kde `<hmac-sha256-signature>` je počítáno v předchozím kroku jako HMAC.

## <a name="authenticate-with-a-user-access-token"></a>Ověřování pomocí přístupového tokenu uživatele

Tokeny přístupu uživatele umožňují klientským aplikacím ověřování přímo proti komunikačním službám Azure. K dosažení tohoto problému byste měli nastavit důvěryhodnou službu, která ověřuje vaše uživatele aplikace a vydává tokeny přístupu uživatele pomocí klientské knihovny pro správu. Další informace o našich možnostech architektury najdete v Koncepční dokumentaci [architektury klienta a serveru](./client-and-server-architecture.md) .

`CommunicationTokenCredential`Třída obsahuje logiku pro poskytnutí přihlašovacích údajů tokenu uživatele k klientským knihovnám a správě jejich životního cyklu.

### <a name="initialize-the-client-libraries"></a>Inicializace klientských knihoven

Pokud chcete inicializovat klientské knihovny Azure Communications, které vyžadují ověřování pomocí tokenu přístupu uživatele, musíte nejdřív vytvořit instanci `CommunicationTokenCredential` třídy a pak ji použít k inicializaci klienta rozhraní API.

Následující fragmenty kódu ukazují, jak inicializovat knihovnu klienta chatu pomocí přístupového tokenu uživatele:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance
var userCredential = new CommunicationTokenCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance with the AzureCommunicationTokenCredential class
const userCredential = new AzureCommunicationTokenCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance
let userCredential = try CommunicationTokenCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationTokenCredential instance
CommunicationTokenCredential userCredential = new CommunicationTokenCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Aktualizují se tokeny přístupu uživatele.

Tokeny přístupu uživatele jsou krátkodobé přihlašovací údaje, které je potřeba znovu vystavit, aby nedocházelo k přerušení služeb uživatelů. `CommunicationTokenCredential`Konstruktor přijímá funkci zpětného volání při aktualizaci, která umožňuje aktualizovat tokeny přístupu uživatele předtím, než vyprší jejich platnost. Toto zpětné volání byste měli použít k načtení nového přístupového tokenu uživatele z vaší důvěryhodné služby.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationTokenCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationTokenCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationTokenCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, token, true);
```
---

`refreshProactively`Možnost vám umožní určit, jak budete spravovat životní cyklus tokenů. Ve výchozím nastavení, když je token zastaralý, zpětné volání zablokuje požadavky rozhraní API a pokusí se ho aktualizovat. Když `refreshProactively` je nastaveno na `true` zpětné volání, je naplánováno a provedeno asynchronně před vypršením platnosti tokenu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření tokenů přístupu uživatele](../quickstarts/access-tokens.md)

Další informace najdete v následujících článcích:
- [Informace o architektuře klientů a serverů](../concepts/client-and-server-architecture.md)

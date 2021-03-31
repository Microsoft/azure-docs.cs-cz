---
title: Vstupní vazba služby Azure Functions Signal
description: Naučte se vracet adresu URL koncového bodu služby signalizace a přístupový token v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 3f3a99c83d4a18f3085419b91be947dd67f8eec4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763315"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Vstupní vazba služby Signal pro Azure Functions

Předtím, než se může klient připojit ke službě Azure Signal, musí načíst adresu URL koncového bodu služby a platný přístupový token. Vstupní vazba *SignalRConnectionInfo* vytvoří adresu URL koncového bodu služby signalizace a platný token, který se používá pro připojení ke službě. Vzhledem k tomu, že token je časově omezený a dá se použít k ověření konkrétního uživatele k připojení, neměli byste token ukládat do mezipaměti ani sdílet mezi klienty. Trigger HTTP pomocí této vazby můžou klienti použít k načtení informací o připojení.

Další informace o tom, jak se tato vazba používá k vytvoření funkce "Negotiate", kterou může využívat klientská sada SDK pro signalizaci, najdete v [článku Azure Functions vývoj a konfigurace](../azure-signalr/signalr-concept-serverless-development-config.md) v dokumentaci k koncepcím služby Signal.

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-signalr-service.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která získá informace o připojení k signalizaci pomocí vstupní vazby a vrátí ji přes HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje vstupní vazbu informace o připojení k signalizaci v *function.js* souboru a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu k vrácení informací o připojení.

Tady je vazba dat v *function.js* souboru:

Příklad function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Tady je kód skriptu jazyka C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vstupní vazbu informace o připojení k signalizaci v *function.js* souboru a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu k vrácení informací o připojení.

Tady je vazba dat v *function.js* souboru:

Příklad function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Tady je kód JavaScriptu:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje vstupní vazbu informace o připojení k signalizaci v *function.js* souboru a [funkci Pythonu](functions-reference-python.md) , která používá vazbu k vrácení informací o připojení.

Tady je vazba dat v *function.js* souboru:

Příklad function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Tady je kód Pythonu:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje [funkci jazyka Java](functions-reference-java.md) , která získá informace o připojení k signalizaci pomocí vstupní vazby a vrátí ji přes HTTP.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="authenticated-tokens"></a>Ověřené tokeny

Pokud je funkce aktivována ověřeným klientem, můžete do vygenerovaného tokenu přidat deklaraci ID uživatele. Do aplikace Function App můžete snadno přidat ověřování pomocí [App Service ověřování](../app-service/overview-authentication-authorization.md).

App Service Authentication nastaví hlavičky HTTP s názvem `x-ms-client-principal-id` a `x-ms-client-principal-name` , které obsahují ID a ID objektu zabezpečení klienta ověřeného uživatele v uvedeném pořadí.

# <a name="c"></a>[C#](#tab/csharp)

Vlastnost vazby můžete nastavit `UserId` na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}` .

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Vlastnost vazby můžete nastavit `userId` na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}` .

Příklad function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Tady je kód skriptu jazyka C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Vlastnost vazby můžete nastavit `userId` na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}` .

Příklad function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Tady je kód JavaScriptu:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Vlastnost vazby můžete nastavit `userId` na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}` .

Příklad function.js:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Tady je kód Pythonu:

```python
def main(req: func.HttpRequest, connectionInfo: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfo,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Vlastnost vazby můžete nastavit `userId` na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}` .

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

---

## <a name="next-steps"></a>Další kroky

- [Zpracování zpráv ze služby signalizace (aktivační vazba)](./functions-bindings-signalr-service-trigger.md)
- [Odeslat zprávy služby Signaler (výstupní vazba)](./functions-bindings-signalr-service-output.md) 

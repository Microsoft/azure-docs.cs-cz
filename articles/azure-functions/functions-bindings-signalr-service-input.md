---
title: Vstupní vazba služby Azure Functions SignalR Service
description: Naučte se vrátit adresu URL koncového bodu služby SignalR a přístupový token ve službě Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/20/2020
ms.author: cshoe
ms.openlocfilehash: 53d336aff3177a76c5e02266ffb8484bd9945119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530260"
---
# <a name="signalr-service-input-binding-for-azure-functions"></a>Vstupní vazba služby SignalR pro funkce Azure

Než se klient bude moci připojit ke službě Azure SignalR, musí načíst adresu URL koncového bodu služby a platný přístupový token. Vstupní vazba *SignalRConnectionInfo* vytváří adresu URL koncového bodu služby SignalR a platný token, který se používá k připojení ke službě. Vzhledem k tomu, že token je časově omezený a lze jej použít k ověření konkrétního uživatele připojení, neměli byste ukládat token do mezipaměti nebo jej sdílet mezi klienty. Aktivační událost HTTP používající tuto vazbu mohou klienti použít k načtení informací o připojení.

Další informace o tom, jak se tato vazba používá k vytvoření funkce "negotiate", kterou může využívat sada SDK klienta SignalR, naleznete v [článku vývoje a konfigurace azure functions](../azure-signalr/signalr-concept-serverless-development-config.md) v dokumentaci konceptů služby SignalR.

Informace o nastavení a konfiguraci naleznete v [přehledu](functions-bindings-signalr-service.md).

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [funkci Jazyka C#,](functions-dotnet-class-library.md) která získává informace o připojení SignalR pomocí vstupní vazby a vrátí ji přes HTTP.

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

Následující příklad ukazuje vstupní vazbu informací o připojení SignalR v souboru *function.json* a [funkci Skript jazyka C,,](functions-reference-csharp.md) která používá vazbu k vrácení informací o připojení.

Zde jsou vazby dat v souboru *function.json:*

Příklad function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Zde je kód skriptu C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje vstupní vazbu informací o připojení SignalR v souboru *function.json* a [funkci JavaScript,](functions-reference-node.md) která používá vazbu k vrácení informací o připojení.

Zde jsou vazby dat v souboru *function.json:*

Příklad function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Zde je kód JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje vstupní vazbu informací o připojení SignalR v souboru *function.json* a [funkci Pythonu,](functions-reference-python.md) která používá vazbu k vrácení informací o připojení.

Zde jsou vazby dat v souboru *function.json:*

Příklad function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Zde je kód Pythonu:

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

Následující příklad ukazuje [funkci Java,](functions-reference-java.md) která získává informace o připojení SignalR pomocí vstupní vazby a vrátí ji přes HTTP.

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

Pokud je funkce spuštěna ověřeným klientem, můžete do generovaného tokenu přidat deklaraci ID uživatele. Ověřování můžete snadno přidat do aplikace funkce pomocí [ověřování pomocí služby App Service .](../app-service/overview-authentication-authorization.md)

Ověřování služby App Service `x-ms-client-principal-id` `x-ms-client-principal-name` nastaví záhlaví PROTOKOLU HTTP s názvem a které obsahují ID a název objektu klienta ověřeného uživatele.

# <a name="c"></a>[C #](#tab/csharp)

Vlastnost vazby můžete nastavit na hodnotu z obou záhlaví `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): nebo . `UserId`

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

Vlastnost vazby můžete nastavit na hodnotu z obou záhlaví `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): nebo . `userId`

Příklad function.json:

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

Zde je kód skriptu C#:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Vlastnost vazby můžete nastavit na hodnotu z obou záhlaví `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): nebo . `userId`

Příklad function.json:

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

Zde je kód JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="python"></a>[Python](#tab/python)

Vlastnost vazby můžete nastavit na hodnotu z obou záhlaví `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): nebo . `userId`

Příklad function.json:

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

Zde je kód Pythonu:

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="java"></a>[Java](#tab/java)

Vlastnost vazby můžete nastavit na hodnotu z obou záhlaví `{headers.x-ms-client-principal-id}` `{headers.x-ms-client-principal-name}`pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): nebo . `userId`

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

- [Odeslat zprávy služby SignalR (výstupní vazba)](./functions-bindings-signalr-service-output.md) 

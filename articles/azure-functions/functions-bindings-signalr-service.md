---
title: Vazby služby Azure Functions Signal
description: Naučte se používat vazby služby signalizace s Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 208b5462efeb579e30550824bd7ba931db1825b2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925640"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Vazby služby SignalR pro Azure Functions

Tento článek vysvětluje, jak ověřit a odeslat zprávy v reálném čase klientům připojeným ke [službě Azure Signal](https://azure.microsoft.com/services/signalr-service/) pomocí vazeb služby signalizace v Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro službu Signal.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vazby služby signalizace jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) , verze 1. *. Zdrojový kód balíčku je v úložišti GitHub [Azure-Functions-signalrservice-Extension](https://github.com/Azure/azure-functions-signalrservice-extension) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

Podrobnosti o tom, jak nakonfigurovat a používat službu signalizace a Azure Functions společně, najdete v tématu [Azure Functions vývoj a konfigurace pomocí služby Azure Signal Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Knihovna poznámek (jenom Java)

Pokud chcete používat poznámky ke službě signalizace ve funkcích Java, musíte do souboru pom. XML přidat závislost do artefaktu *Azure-Functions-Java-Library-Signal* (verze 1,0 nebo vyšší).

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="input"></a>Vstup

Předtím, než se může klient připojit ke službě Azure Signal, musí načíst adresu URL koncového bodu služby a platný přístupový token. Vstupní vazba *SignalRConnectionInfo* vytvoří adresu URL koncového bodu služby signalizace a platný token, který se používá pro připojení ke službě. Vzhledem k tomu, že token je časově omezený a dá se použít k ověření konkrétního uživatele k připojení, neměli byste token ukládat do mezipaměti ani sdílet mezi klienty. Trigger HTTP pomocí této vazby můžou klienti použít k načtení informací o připojení.

Další informace o tom, jak se tato vazba používá k vytvoření funkce "Negotiate", kterou může využívat klientská sada SDK pro signalizaci, najdete v [článku Azure Functions vývoj a konfigurace](../azure-signalr/signalr-concept-serverless-development-config.md) v dokumentaci k koncepcím služby Signal.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která získá informace o připojení k signalizaci pomocí vstupní vazby a vrátí ji přes HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje vstupní vazbu informace o připojení k signalizaci v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu k vrácení informací o připojení.

Tady je vazba dat v souboru *Function. JSON* :

Příklad Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Tady je kód C# skriptu:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje vstupní vazbu informace o připojení k signalizaci v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu k vrácení informací o připojení.

Tady je vazba dat v souboru *Function. JSON* :

Příklad Function. JSON:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Následující příklad ukazuje vstupní vazbu informace o připojení k signalizaci v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu k vrácení informací o připojení.

Tady je vazba dat v souboru *Function. JSON* :

Příklad Function. JSON:

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

### <a name="authenticated-tokens"></a>Ověřené tokeny

Pokud je funkce aktivována ověřeným klientem, můžete do vygenerovaného tokenu přidat deklaraci ID uživatele. Do aplikace Function App můžete snadno přidat ověřování pomocí [App Service ověřování](../app-service/overview-authentication-authorization.md).

App Service Authentication nastaví hlavičky HTTP s názvem `x-ms-client-principal-id` a `x-ms-client-principal-name`, které obsahují ID a název objektu zabezpečení klienta ověřeného uživatele v uvedeném pořadí.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Vlastnost `UserId` vazby můžete nastavit na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`.

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

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Vlastnost `userId` vazby můžete nastavit na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`.

Příklad Function. JSON:

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

Tady je kód C# skriptu:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Vlastnost `userId` vazby můžete nastavit na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`.

Příklad Function. JSON:

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

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Vlastnost `userId` vazby můžete nastavit na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`.

Příklad Function. JSON:

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

# <a name="javatabjava"></a>[Java](#tab/java)

Vlastnost `userId` vazby můžete nastavit na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`.

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

## <a name="output"></a>Výstup

Pomocí výstupní vazby *signálu* můžete odeslat jednu nebo více zpráv pomocí služby Azure signaler. Můžete vysílat zprávu všem připojeným klientům nebo ji můžete vysílat jenom pro připojené klienty, kteří byli ověření pro daného uživatele.

Můžete ji také použít ke správě skupin, do kterých uživatel patří.

### <a name="broadcast-to-all-clients"></a>Všesměrové vysílání pro všechny klienty

Následující příklad ukazuje funkci, která odesílá zprávu pomocí výstupní vazby pro všechny připojené klienty. *Cíl* je název metody, která se má vyvolat u každého klienta. *Argumenty* jsou pole nula nebo více objektů, které mají být předány metodě klienta.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Tady je vazba dat v souboru *Function. JSON* :

Příklad Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Tady je kód C# skriptu:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Tady je vazba dat v souboru *Function. JSON* :

Příklad Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Tady je vazba dat v souboru *Function. JSON* :

Příklad Function. JSON:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Tady je kód Pythonu:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-user"></a>Odeslat uživateli

Můžete odeslat zprávu pouze do připojení, která byla ověřena uživateli, nastavením *ID uživatele* ve zprávě signalizace.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Příklad Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Tady je kód C# skriptu:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Příklad Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Tady je vazba dat v souboru *Function. JSON* :

Příklad Function. JSON:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Tady je kód Pythonu:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="send-to-a-group"></a>Odeslat do skupiny

Můžete poslat zprávu jenom na připojení, která se přidala do skupiny, a to tak, že nastavíte *název skupiny* ve zprávě signalizace.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

Příklad Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Tady je kód C# skriptu:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Příklad Function. JSON:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Tady je vazba dat v souboru *Function. JSON* :

Příklad Function. JSON:

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Tady je kód Pythonu:

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

---

### <a name="group-management"></a>Správa skupin

Služba Signal umožňuje přidat uživatele do skupin. Zprávy je pak možné odeslat do skupiny. Pomocí výstupní vazby `SignalR` můžete spravovat členství uživatele ve skupině.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

#### <a name="add-user-to-a-group"></a>Přidat uživatele do skupiny

Následující příklad přidá uživatele do skupiny.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Odebrání uživatele ze skupiny

Následující příklad odebere uživatele ze skupiny.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Aby bylo možné `ClaimsPrincipal` správně svázat, je nutné nakonfigurovat nastavení ověřování v Azure Functions.

# <a name="c-scripttabcsharp-script"></a>[C#Pravidel](#tab/csharp-script)

#### <a name="add-user-to-a-group"></a>Přidat uživatele do skupiny

Následující příklad přidá uživatele do skupiny.

Příklad *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Spustit. csx*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Odebrání uživatele ze skupiny

Následující příklad odebere uživatele ze skupiny.

Příklad *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Spustit. csx*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Aby bylo možné `ClaimsPrincipal` správně svázat, je nutné nakonfigurovat nastavení ověřování v Azure Functions.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="add-user-to-a-group"></a>Přidat uživatele do skupiny

Následující příklad přidá uživatele do skupiny.

Příklad *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Odebrání uživatele ze skupiny

Následující příklad odebere uživatele ze skupiny.

Příklad *Function. JSON*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

#### <a name="add-user-to-a-group"></a>Přidat uživatele do skupiny

Následující příklad přidá uživatele do skupiny.

Příklad *Function. JSON*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

#### <a name="remove-user-from-a-group"></a>Odebrání uživatele ze skupiny

Následující příklad odebere uživatele ze skupiny.

Příklad *Function. JSON*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init. py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

#### <a name="add-user-to-a-group"></a>Přidat uživatele do skupiny

Následující příklad přidá uživatele do skupiny.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Odebrání uživatele ze skupiny

Následující příklad odebere uživatele ze skupiny.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

---

## <a name="configuration"></a>Konfigurace

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `SignalRConnectionInfo` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**|| Musí být nastaveno na `signalRConnectionInfo`.|
|**direction**|| Musí být nastaveno na `in`.|
|**name**|| Název proměnné použitý v kódu funkce pro objekt s informacemi o připojení. |
|**hubName**|**HubName**| Tato hodnota musí být nastavena na název centra signalizace, pro které jsou vygenerovány informace o připojení.|
|**userId**|**UserId**| Volitelné: hodnota deklarace identity identifikátoru uživatele, která se má nastavit v tokenu přístupového klíče. |
|**connectionStringSetting**|**connectionStringSetting**| Název nastavení aplikace, které obsahuje připojovací řetězec služby signalizace (výchozí hodnota je "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `SignalR` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**|| Musí být nastaveno na `signalR`.|
|**direction**|| Musí být nastaveno na `out`.|
|**name**|| Název proměnné použitý v kódu funkce pro objekt s informacemi o připojení. |
|**hubName**|**HubName**| Tato hodnota musí být nastavena na název centra signalizace, pro které jsou vygenerovány informace o připojení.|
|**connectionStringSetting**|**connectionStringSetting**| Název nastavení aplikace, které obsahuje připojovací řetězec služby signalizace (výchozí hodnota je "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)

---
title: Vazby služby Azure Functions Signal
description: Naučte se používat vazby služby signalizace s Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
editor: ''
tags: ''
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: b4622321dc25025eb2f7752755490eb5bc105069
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741788"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Vazby služby SignalR pro Azure Functions

Tento článek vysvětluje, jak ověřit a odeslat zprávy v reálném čase klientům připojeným ke [službě Azure Signal](https://azure.microsoft.com/services/signalr-service/) pomocí vazeb služby signalizace v Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro službu Signal.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby služby signalizace jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) , verze 1. *. Zdrojový kód balíčku je v úložišti GitHub [Azure-Functions-signalrservice-Extension](https://github.com/Azure/azure-functions-signalrservice-extension) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Poznámky jazyka Java

Pokud chcete používat poznámky ke službě signalizace ve funkcích Java, musíte do souboru pom. XML přidat závislost do artefaktu *Azure-Functions-Java-Library-Signal* (verze 1,0 nebo vyšší).

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Chcete-li použít vazby služby signalizace v jazyce Java, ujistěte se, že používáte verzi 2.4.419 nebo vyšší z Azure Functions Core Tools (2.0.12332 hostitele verze).

## <a name="using-signalr-service-with-azure-functions"></a>Použití služby signalizace s Azure Functions

Podrobnosti o tom, jak nakonfigurovat a používat službu signalizace a Azure Functions společně, najdete v tématu [Azure Functions vývoj a konfigurace pomocí služby Azure Signal Service](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Vstupní vazba informací o připojení signálů

Předtím, než se může klient připojit ke službě Azure Signal, musí načíst adresu URL koncového bodu služby a platný přístupový token. Vstupní vazba *SignalRConnectionInfo* vytvoří adresu URL koncového bodu služby signalizace a platný token, který se používá pro připojení ke službě. Vzhledem k tomu, že token je časově omezený a dá se použít k ověření konkrétního uživatele k připojení, neměli byste token ukládat do mezipaměti ani sdílet mezi klienty. Trigger HTTP pomocí této vazby můžou klienti použít k načtení informací o připojení.

Podívejte se na příklad specifické pro jazyk:

* [2.x C#](#2x-c-input-examples)
* [2. x JavaScript](#2x-javascript-input-examples)
* [2. x Java](#2x-java-input-examples)

Další informace o tom, jak se tato vazba používá k vytvoření funkce "Negotiate", kterou může využívat klientská sada SDK pro signalizaci, najdete v [článku Azure Functions vývoj a konfigurace](../azure-signalr/signalr-concept-serverless-development-config.md) v dokumentaci k koncepcím služby Signal.

### <a name="2x-c-input-examples"></a>vstupní příklady 2 C# . x

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

#### <a name="authenticated-tokens"></a>Ověřené tokeny

Pokud je funkce aktivována ověřeným klientem, můžete do vygenerovaného tokenu přidat deklaraci ID uživatele. Do aplikace Function App můžete snadno přidat ověřování pomocí [App Service ověřování](../app-service/overview-authentication-authorization.md).

App Service Authentication nastaví hlavičky HTTP `x-ms-client-principal-id` s `x-ms-client-principal-name` názvem a, které obsahují ID a ID objektu zabezpečení klienta ověřeného uživatele v uvedeném pořadí. `UserId` Vlastnost vazby můžete nastavit na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-javascript-input-examples"></a>2. x – vstupní příklady JavaScriptu

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

#### <a name="authenticated-tokens"></a>Ověřené tokeny

Pokud je funkce aktivována ověřeným klientem, můžete do vygenerovaného tokenu přidat deklaraci ID uživatele. Do aplikace Function App můžete snadno přidat ověřování pomocí [App Service ověřování](../app-service/overview-authentication-authorization.md).

App Service Authentication nastaví hlavičky HTTP `x-ms-client-principal-id` s `x-ms-client-principal-name` názvem a, které obsahují ID a ID objektu zabezpečení klienta ověřeného uživatele v uvedeném pořadí. `userId` Vlastnost vazby můžete nastavit na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-java-input-examples"></a>2. x Java Input – příklady

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

#### <a name="authenticated-tokens"></a>Ověřené tokeny

Pokud je funkce aktivována ověřeným klientem, můžete do vygenerovaného tokenu přidat deklaraci ID uživatele. Do aplikace Function App můžete snadno přidat ověřování pomocí [App Service ověřování](../app-service/overview-authentication-authorization.md).

App Service Authentication nastaví hlavičky HTTP `x-ms-client-principal-id` s `x-ms-client-principal-name` názvem a, které obsahují ID a ID objektu zabezpečení klienta ověřeného uživatele v uvedeném pořadí. `UserId` Vlastnost vazby můžete nastavit na hodnotu z obou hlaviček pomocí [výrazu vazby](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`.

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

## <a name="signalr-output-binding"></a>Výstupní vazba signálu

Pomocí výstupní vazby *signálu* můžete odeslat jednu nebo více zpráv pomocí služby Azure signaler. Můžete vysílat zprávu všem připojeným klientům nebo ji můžete vysílat jenom pro připojené klienty, kteří byli ověření pro daného uživatele.

Můžete ji také použít ke správě skupin, do kterých uživatel patří.

Podívejte se na příklad specifické pro jazyk:

* [2.x C#](#2x-c-send-message-output-examples)
* [2. x JavaScript](#2x-javascript-send-message-output-examples)
* [2. x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2. x C# odeslat příklady výstupu zprávy

#### <a name="broadcast-to-all-clients"></a>Všesměrové vysílání pro všechny klienty

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která odesílá zprávu pomocí výstupní vazby pro všechny připojené klienty. `Target` Je název metody, která má být vyvolána na každém klientovi. `Arguments` Vlastnost je pole nula nebo více objektů, které mají být předány metodě klienta.

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

#### <a name="send-to-a-user"></a>Odeslat uživateli

Můžete odeslat zprávu pouze pro připojení, která byla ověřena uživateli, nastavením `UserId` vlastnosti zprávy signálu.

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

#### <a name="send-to-a-group"></a>Odeslat do skupiny

Můžete odeslat zprávu pouze do připojení přidaných do skupiny nastavením `GroupName` vlastnosti zprávy signálu.

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

### <a name="2x-c-group-management-output-examples"></a>Příklady výstupů C# správy skupin 2. x

Služba Signal umožňuje přidat uživatele do skupin. Zprávy je pak možné odeslat do skupiny. `SignalRGroupAction` Třídu`SignalR` s výstupní vazbou můžete použít ke správě členství uživatele ve skupině.

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
> Aby bylo možné získat `ClaimsPrincipal` správnou vazbu, je nutné nakonfigurovat nastavení ověřování v Azure Functions.

### <a name="2x-javascript-send-message-output-examples"></a>Příklady pro odeslání zprávy ve 2. x JavaScriptu

#### <a name="broadcast-to-all-clients"></a>Všesměrové vysílání pro všechny klienty

Následující příklad ukazuje výstupní vazbu signálu v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu k odeslání zprávy pomocí služby Azure Signal Service. Nastavte výstupní vazbu na pole jedné nebo více zpráv signalizace. Zpráva signalizace se skládá z `target` vlastnosti, která určuje název metody, která má být vyvolána na každém klientovi, `arguments` a vlastnost, která je pole objektů, které mají být předávány metodě klienta jako argumenty.

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

#### <a name="send-to-a-user"></a>Odeslat uživateli

Můžete odeslat zprávu pouze pro připojení, která byla ověřena uživateli, nastavením `userId` vlastnosti zprávy signálu.

*Function. JSON* zůstává stejný. Tady je kód jazyka JavaScript:

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

#### <a name="send-to-a-group"></a>Odeslat do skupiny

Můžete odeslat zprávu pouze do připojení přidaných do skupiny nastavením `groupName` vlastnosti zprávy signálu.

*Function. JSON* zůstává stejný. Tady je kód jazyka JavaScript:

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

### <a name="2x-javascript-group-management-output-examples"></a>dva příklady výstupu správy skupin JavaScriptu ve skupině x

Služba Signal umožňuje přidat uživatele do skupin. Zprávy je pak možné odeslat do skupiny. K řízení členství uživatele `SignalR` ve skupině můžete použít výstupní vazbu.

#### <a name="add-user-to-a-group"></a>Přidat uživatele do skupiny

Následující příklad přidá uživatele do skupiny.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
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

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
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

### <a name="2x-java-send-message-output-examples"></a>2. x Java poslat výstup zprávy příklady

#### <a name="broadcast-to-all-clients"></a>Všesměrové vysílání pro všechny klienty

Následující příklad ukazuje [funkci jazyka Java](functions-reference-java.md) , která odesílá zprávu pomocí výstupní vazby pro všechny připojené klienty. `target` Je název metody, která má být vyvolána na každém klientovi. `arguments` Vlastnost je pole nula nebo více objektů, které mají být předány metodě klienta.

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

#### <a name="send-to-a-user"></a>Odeslat uživateli

Můžete odeslat zprávu pouze pro připojení, která byla ověřena uživateli, nastavením `userId` vlastnosti zprávy signálu.

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

#### <a name="send-to-a-group"></a>Odeslat do skupiny

Můžete odeslat zprávu pouze do připojení přidaných do skupiny nastavením `groupName` vlastnosti zprávy signálu.

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

### <a name="2x-java-group-management-output-examples"></a>2. x Java – příklady výstupu správy skupin

Služba Signal umožňuje přidat uživatele do skupin. Zprávy je pak možné odeslat do skupiny. `SignalRGroupAction` Třídu`SignalROutput` s výstupní vazbou můžete použít ke správě členství uživatele ve skupině.

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

## <a name="configuration"></a>Konfiguraci

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `SignalRConnectionInfo` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**|| Musí být nastaveno na `signalRConnectionInfo`.|
|**direction**|| Musí být nastaveno na `in`.|
|**name**|| Název proměnné použitý v kódu funkce pro objekt s informacemi o připojení. |
|**hubName**|**HubName**| Tato hodnota musí být nastavena na název centra signalizace, pro které jsou vygenerovány informace o připojení.|
|**userId**|**UserId**| Volitelné: Hodnota deklarace identity identifikátoru uživatele, která se má nastavit v tokenu přístupového klíče |
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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)

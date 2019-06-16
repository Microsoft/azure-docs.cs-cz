---
title: Azure SignalR služba Functions vazby
description: Vysvětlení použití služby SignalR vazby s využitím Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
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
ms.openlocfilehash: f0d4a607676285ed4f0f91d8ce8c83ddf1313b89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511132"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Vazby služby SignalR pro Azure Functions

Tento článek vysvětluje, jak ověřovat a odesílání zpráv v reálném čase klientů připojených ke [služby Azure SignalR](https://azure.microsoft.com/services/signalr-service/) pomocí vazby služeb SignalR ve službě Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro služby SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Jsou součástí služby SignalR vazby [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) balíčku NuGet, 1.* verze. Zdrojový kód pro tento balíček je v [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java poznámky

Použití poznámek služby SignalR v funkcí v Javě, budete muset přidat závislost na *azure-functions-java knihovny signalr* artefaktu (verze 1.0 nebo vyšší), který se vaše pom.xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Použití služby SignalR vazby v Javě, ujistěte se, že používáte verzi 2.4.419 nebo vyšší nástrojů Azure Functions Core (verze hostitele 2.0.12332).

## <a name="using-signalr-service-with-azure-functions"></a>Pomocí služby SignalR službou Azure Functions

Podrobnosti o tom, jak nakonfigurovat a používat služby SignalR a Azure Functions společně [vývoj pro Azure Functions a konfigurace pomocí služby Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Vstupní vazba informace o připojení SignalR

Předtím, než se klient může připojit ke službě Azure SignalR, musí získat adresu URL koncového bodu služby a platným přístupovým tokenem. *SignalRConnectionInfo* vstupní vazby vytvoří adresu URL koncového bodu služby SignalR a platný token, který slouží k připojení ke službě. Protože token je časově omezený a můžete použít k ověření konkrétního uživatele na připojení, by neměly ukládat do mezipaměti token nebo sdílet mezi klienty. Aktivační událost HTTP používá tuto vazbu může používají klienti k načtení informací o připojení.

Podívejte se na příklad specifické pro jazyk:

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

Další informace o tom, jak tuto vazbu slouží k vytvoření "vyjednat" funkci, která mohou být spotřebovány sady SDK klienta SignalR, najdete v článku [článku vývoje a konfigurace Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) v koncepty služby SignalR dokumentace ke službě.

### <a name="2x-c-input-examples"></a>2.x C# vstup příklady

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který získá informace o připojení SignalR využitím vstupní vazby a vrátí ji přes protokol HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Ověření tokenů

Pokud ověřený klient se aktivuje funkci, můžete přidat deklarace identity ID uživatele pro vygenerovaný token. Můžete snadno přidat ověřování pro aplikaci funkcí s použitím [ověřování pomocí služby App Service](../app-service/overview-authentication-authorization.md).

Ověřování pomocí služby App Service nastaví hlavičky protokolu HTTP s názvem `x-ms-client-principal-id` a `x-ms-client-principal-name` obsahují ověřeného uživatele ID klienta instančního objektu a název, v uvedeném pořadí. Můžete nastavit `UserId` vlastnost vazby na hodnotu buď pomocí hlavičky [vazby výraz](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`. 

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

### <a name="2x-javascript-input-examples"></a>Příklady vstupní 2.x jazyka JavaScript

Následující příklad ukazuje SignalR připojení informace vstupní vazby v *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) vazby, který používá k vrácení informací o připojení.

Zde je vazba dat v *function.json* souboru:

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

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Ověření tokenů

Pokud ověřený klient se aktivuje funkci, můžete přidat deklarace identity ID uživatele pro vygenerovaný token. Můžete snadno přidat ověřování pro aplikaci funkcí s použitím [ověřování pomocí služby App Service](../app-service/overview-authentication-authorization.md).

Ověřování pomocí služby App Service nastaví hlavičky protokolu HTTP s názvem `x-ms-client-principal-id` a `x-ms-client-principal-name` obsahují ověřeného uživatele ID klienta instančního objektu a název, v uvedeném pořadí. Můžete nastavit `userId` vlastnost vazby na hodnotu buď pomocí hlavičky [vazby výraz](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`. 

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

Tady je kód jazyka JavaScript:

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>vstupní příkladů Java 2.x

Následující příklad ukazuje [Java funkce](functions-reference-java.md) , který získá informace o připojení SignalR využitím vstupní vazby a vrátí ji přes protokol HTTP.

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

#### <a name="authenticated-tokens"></a>Ověření tokenů

Pokud ověřený klient se aktivuje funkci, můžete přidat deklarace identity ID uživatele pro vygenerovaný token. Můžete snadno přidat ověřování pro aplikaci funkcí s použitím [ověřování pomocí služby App Service](../app-service/overview-authentication-authorization.md).

Ověřování pomocí služby App Service nastaví hlavičky protokolu HTTP s názvem `x-ms-client-principal-id` a `x-ms-client-principal-name` obsahují ověřeného uživatele ID klienta instančního objektu a název, v uvedeném pořadí. Můžete nastavit `UserId` vlastnost vazby na hodnotu buď pomocí hlavičky [vazby výraz](./functions-bindings-expressions-patterns.md): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`.

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

## <a name="signalr-output-binding"></a>Funkce SignalR výstupní vazby

Použití *SignalR* výstupní vazbu k odeslání jednu nebo více zpráv pomocí služby Azure SignalR. Můžete vysílat zprávy všem připojeným klientům, nebo vysílat je pouze pro připojené klienty, které byly ověřeny pro daného uživatele.

Můžete také použít ho ke správě skupin, které uživatel patří do.

Podívejte se na příklad specifické pro jazyk:

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.x C# odeslat zprávu výstup příklady

#### <a name="broadcast-to-all-clients"></a>Vysílání pro všechny klienty

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , která odešle zprávu pomocí výstupní vazbu na všechny připojené klienty. `Target` Je název metody, která má být volána v každém klientovi. `Arguments` Vlastnost je pole nula nebo více objektů, které mají být předány metodě klienta.

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

#### <a name="send-to-a-user"></a>Poslat uživateli

Můžete odeslat zprávu pouze k připojení, které byly ověřeny pro uživatele tak, že nastavíte `UserId` vlastnost zprávy SignalR.

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

Můžete odeslat zprávu pouze k připojení, které byly přidány do skupiny tak, že nastavíte `GroupName` vlastnost zprávy SignalR.

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

### <a name="2x-c-group-management-output-examples"></a>2.x C# příklady výstupní skupiny správy

Služby SignalR umožňuje uživatelům přidávat do skupin. Potom nelze odesílat zprávy do skupiny. Můžete použít `SignalRGroupAction` třídy s `SignalR` výstupní vazbu ke správě uživatele členství ve skupině.

#### <a name="add-user-to-a-group"></a>Přidání uživatele do skupiny

Následující příklad přidá uživatele do skupiny.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
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
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

### <a name="2x-javascript-send-message-output-examples"></a>Odeslat zprávu JavaScript 2.x výstup příklady

#### <a name="broadcast-to-all-clients"></a>Vysílání pro všechny klienty

Následující příklad ukazuje SignalR výstupní vazby ve *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) vazby, který používá k odeslání zprávy pomocí služby Azure SignalR. Nastavte výstupní vazbu na celou řadu jednu nebo více zpráv SignalR. Zpráva SignalR se skládá z `target` vlastnost, která určuje název metody, který má být vyvolán v každém klientovi a `arguments` vlastnost, která je pole objektů, které chcete předat metodě klienta jako argumenty.

Zde je vazba dat v *function.json* souboru:

Příklad function.json:

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

#### <a name="send-to-a-user"></a>Poslat uživateli

Můžete odeslat zprávu pouze k připojení, které byly ověřeny pro uživatele tak, že nastavíte `userId` vlastnost zprávy SignalR.

*Function.JSON* zůstala stejná. Tady je kód jazyka JavaScript:

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

Můžete odeslat zprávu pouze k připojení, které byly přidány do skupiny tak, že nastavíte `groupName` vlastnost zprávy SignalR.

*Function.JSON* zůstala stejná. Tady je kód jazyka JavaScript:

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

### <a name="2x-javascript-group-management-output-examples"></a>Příklady výstup 2.x JavaScript skupiny správy

Služby SignalR umožňuje uživatelům přidávat do skupin. Potom nelze odesílat zprávy do skupiny. Můžete použít `SignalR` výstupní vazbu ke správě uživatele členství ve skupině.

#### <a name="add-user-to-a-group"></a>Přidání uživatele do skupiny

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

### <a name="2x-java-send-message-output-examples"></a>Příklady výstup 2.x Javy odeslat zprávu

#### <a name="broadcast-to-all-clients"></a>Vysílání pro všechny klienty

Následující příklad ukazuje [Java funkce](functions-reference-java.md) , která odešle zprávu pomocí výstupní vazbu na všechny připojené klienty. `target` Je název metody, která má být volána v každém klientovi. `arguments` Vlastnost je pole nula nebo více objektů, které mají být předány metodě klienta.

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

#### <a name="send-to-a-user"></a>Poslat uživateli

Můžete odeslat zprávu pouze k připojení, které byly ověřeny pro uživatele tak, že nastavíte `userId` vlastnost zprávy SignalR.

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

Můžete odeslat zprávu pouze k připojení, které byly přidány do skupiny tak, že nastavíte `groupName` vlastnost zprávy SignalR.

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

### <a name="2x-java-group-management-output-examples"></a>Příklady výstup 2.x Java skupiny správy

Služby SignalR umožňuje uživatelům přidávat do skupin. Potom nelze odesílat zprávy do skupiny. Můžete použít `SignalRGroupAction` třídy s `SignalROutput` výstupní vazbu ke správě uživatele členství ve skupině.

#### <a name="add-user-to-a-group"></a>Přidání uživatele do skupiny

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

## <a name="configuration"></a>Konfigurace

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `SignalRConnectionInfo` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**|| Musí být nastaveno na `signalRConnectionInfo`.|
|**direction**|| Musí být nastaveno na `in`.|
|**name**|| Název proměnné používá v kódu funkce pro objekt informací o připojení. |
|**hubName**|**HubName**| Tato hodnota musí být nastavena na název rozbočovače SignalR, pro které se generuje informace o připojení.|
|**userId**|**UserId**| Volitelné: Nastavení přístupového klíče tokenu deklarací identity hodnota identifikátoru uživatele. |
|**connectionStringSetting**|**connectionStringSetting**| Název nastavení aplikace, které obsahuje připojovací řetězec služby SignalR (výchozí nastavení "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `SignalR` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**|| Musí být nastaveno na `signalR`.|
|**direction**|| Musí být nastaveno na `out`.|
|**name**|| Název proměnné používá v kódu funkce pro objekt informací o připojení. |
|**hubName**|**HubName**| Tato hodnota musí být nastavena na název rozbočovače SignalR, pro které se generuje informace o připojení.|
|**connectionStringSetting**|**connectionStringSetting**| Název nastavení aplikace, které obsahuje připojovací řetězec služby SignalR (výchozí nastavení "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)

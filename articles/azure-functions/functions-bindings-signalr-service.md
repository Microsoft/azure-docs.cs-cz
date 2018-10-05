---
title: Azure SignalR služba Functions vazby
description: Vysvětlení použití služby SignalR vazby s využitím Azure Functions.
services: functions
documentationcenter: na
author: anthonychu
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/23/2018
ms.author: antchu
ms.openlocfilehash: 2892481dca9ce62d96e954656341925b4c8110f9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802011"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Vazby služby SignalR pro Azure Functions

Tento článek vysvětluje, jak ověřovat a odesílání zpráv v reálném čase klientů připojených ke [služby Azure SignalR](https://azure.microsoft.com/services/signalr-service/) pomocí vazby služeb SignalR ve službě Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro služby SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Jsou součástí služby SignalR vazby [Microsoft.Azure.WebJobs.Extensions.SignalRService](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) balíčku NuGet, 1.0.0-preview1-* verze. Zdrojový kód pro tento balíček je v [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) úložiště GitHub.

> [!NOTE]
> Službě Azure SignalR je obecně dostupná. Ale vazby služeb SignalR pro službu Azure Functions jsou momentálně ve verzi preview.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>Vstupní vazba informace o připojení SignalR

Předtím, než se klient může připojit ke službě Azure SignalR, musí získat adresu URL koncového bodu služby a platným přístupovým tokenem. *SignalRConnectionInfo* vstupní vazby vytvoří adresu URL koncového bodu služby SignalR a platný token, který slouží k připojení ke službě. Protože token je časově omezený a můžete použít k ověření konkrétního uživatele na připojení, by neměly ukládat do mezipaměti token nebo sdílet mezi klienty. Aktivační událost HTTP používá tuto vazbu může používají klienti k načtení informací o připojení.

Podívejte se na příklad specifické pro jazyk:

* [2.x C#](#2x-c-input-example)
* [2.x JavaScript](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>Příklad vstupní 2.x C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , který získá informace o připojení SignalR využitím vstupní vazby a vrátí ji přes protokol HTTP.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Ověření tokenů

Pokud ověřený klient se aktivuje funkci, můžete přidat deklarace identity ID uživatele pro vygenerovaný token. Můžete snadno přidat ověřování do aplikace function app pomocí [ověřování pomocí služby App Service] (.. /App-Service/App-Service-Authentication-Overview.MD).

Ověřování pomocí služby App Service nastaví hlavičky protokolu HTTP s názvem `x-ms-client-principal-id` a `x-ms-client-principal-name` obsahují ověřeného uživatele ID klienta instančního objektu a název, v uvedeném pořadí. Můžete nastavit `UserId` vlastnost vazby na hodnotu buď pomocí hlavičky [vazby výraz](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>vstupní příklad v jazyce JavaScript 2.x

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
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>Ověření tokenů

Pokud ověřený klient se aktivuje funkci, můžete přidat deklarace identity ID uživatele pro vygenerovaný token. Můžete snadno přidat ověřování do aplikace function app pomocí [ověřování pomocí služby App Service] (.. /App-Service/App-Service-Authentication-Overview.MD).

Ověřování pomocí služby App Service nastaví hlavičky protokolu HTTP s názvem `x-ms-client-principal-id` a `x-ms-client-principal-name` obsahují ověřeného uživatele ID klienta instančního objektu a název, v uvedeném pořadí. Můžete nastavit `userId` vlastnost vazby na hodnotu buď pomocí hlavičky [vazby výraz](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` nebo `{headers.x-ms-client-principal-name}`. 

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
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>Funkce SignalR výstupní vazby

Použití *SignalR* výstupní vazbu k odeslání jednu nebo více zpráv pomocí služby Azure SignalR. Můžete vysílat zprávy všem připojeným klientům, nebo vysílat je pouze pro připojené klienty, které byly ověřeny pro daného uživatele.

Podívejte se na příklad specifické pro jazyk:

* [2.x C#](#2x-c-output-example)
* [2.x JavaScript](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>Příklad výstupu 2.x C#

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
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>2.x příklad výstupu JavaScriptu

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
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>Poslat uživateli

Můžete odeslat zprávu pouze k připojení, které byly ověřeny pro uživatele tak, že nastavíte `userId` vlastnost zprávy SignalR.

*Function.JSON* zůstala stejná. Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>Konfigurace

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `SignalRConnectionInfo` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**|| Musí být nastaveno na `signalRConnectionInfo`.|
|**direction**|| Musí být nastaveno na `in`.|
|**Jméno**|| Název proměnné používá v kódu funkce pro objekt informací o připojení. |
|**hubName**|**HubName**| Tato hodnota musí být nastavena na název rozbočovače SignalR, pro které se generuje informace o připojení.|
|**userId**|**ID uživatele**| Volitelné: V přístupového klíče tokenu deklarací hodnota identifikátoru uživatele. |
|**connectionStringSetting**|**connectionStringSetting**| Název nastavení aplikace, které obsahuje připojovací řetězec služby SignalR (výchozí nastavení "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `SignalR` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**|| Musí být nastaveno na `signalR`.|
|**direction**|| Musí být nastaveno na `out`.|
|**Jméno**|| Název proměnné používá v kódu funkce pro objekt informací o připojení. |
|**hubName**|**HubName**| Tato hodnota musí být nastavena na název rozbočovače SignalR, pro které se generuje informace o připojení.|
|**connectionStringSetting**|**connectionStringSetting**| Název nastavení aplikace, které obsahuje připojovací řetězec služby SignalR (výchozí nastavení "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)


---
title: Aktivační událost služby signalizace Azure Functions
description: Naučte se odesílat zprávy služby signalizace z Azure Functions.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763505"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Aktivační událost služby signalizace pro Azure Functions

Pomocí vazby triggeru *signálu* můžete reagovat na zprávy odesílané službou Azure Signal. Při aktivaci funkce se zprávy předané do funkce analyzují jako objekt JSON.

V režimu serveru služby signalizace služba signálu používá funkci [nadřazeného](../azure-signalr/concept-upstream.md) prvku k posílání zpráv z klienta na Function App. A Function App používá ke zpracování těchto zpráv vazbu triggeru služby signalizace. Obecná architektura je uvedená níže: :::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="Architektura triggeru signálu":::

Informace o nastavení a podrobnostech o konfiguraci najdete v tématu [Přehled](functions-bindings-signalr-service.md).

## <a name="example"></a>Příklad

Následující příklad ukazuje funkci, která přijímá zprávu pomocí aktivační vazby a zaprotokoluje zprávu.

# <a name="c"></a>[C#](#tab/csharp)

Aktivační událost služby signalizace pro jazyk C# má dva programovací modely. Model založený na třídě a tradiční model. Model založený na třídě může poskytovat konzistentní prostředí pro programování na straně serveru signalizace. A tradiční model poskytuje větší flexibilitu a podobnou ostatním vazbám funkcí.

### <a name="with-class-based-model"></a>Pomocí modelu založeného na třídě

Podrobnosti naleznete v tématu [model založený na třídě](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model) .

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>S tradičním modelem

Tradiční model dodržuje konvenci funkce Azure vyvinuté v jazyce C#. Pokud s ním nejste obeznámeni, můžete se dozvědět od [dokumentů](./functions-dotnet-class-library.md).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>`[SignalRParameter]`Zjednodušte použití atributu`ParameterNames`

Vzhledem k tomu, že je nenáročný k použití `ParameterNames` , `SignalRParameter` je k dispozici, abyste dosáhli stejného účelu.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Tady je vazba dat v *function.js* souboru:

Příklad function.js:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Tady je kód skriptu jazyka C#:

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Tady je vazba dat v *function.js* souboru:

Příklad function.js:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Tady je kód JavaScriptu:

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Tady je vazba dat v *function.js* souboru:

Příklad function.js:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Tady je kód Pythonu:

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Konfigurace

### <a name="signalrtrigger"></a>SignalRTrigger

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `SignalRTrigger` atribut.

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový**| Není k dispozici | Musí být nastaven na hodnotu `SignalRTrigger` .|
|**směr**| Není k dispozici | Musí být nastaven na hodnotu `in` .|
|**Jméno**| Není k dispozici | Název proměnné použitý v kódu funkce pro objekt kontextu vyvolání triggeru |
|**hubName**|**HubName**| Tato hodnota musí být nastavená na název centra signalizace pro funkci, která se má aktivovat.|
|**kategorií**|**Kategorie**| Tato hodnota musí být nastavena jako kategorie zpráv pro funkci, která má být aktivována. Kategorie může být jedna z následujících hodnot: <ul><li>**připojení**: včetně *připojených* a *odpojených* událostí</li><li>**zprávy**: včetně všech ostatních událostí kromě těch v kategorii *připojení*</li></ul> |
|**událostí**|**Událost**| Tato hodnota musí být nastavena jako událost zpráv pro funkci, která má být aktivována. Pro kategorii *zprávy* je událost *cílem* ve [zprávě vyvolání](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) , kterou klienti odesílají. Pro kategorii *připojení* se používá pouze *připojeno* a *Odpojeno* . |
|**parameterNames**|**ParameterNames**| Volitelné Seznam názvů, které se váže k parametrům. |
|**connectionStringSetting**|**ConnectionStringSetting**| Název nastavení aplikace, které obsahuje připojovací řetězec služby signalizace (výchozí hodnota je "AzureSignalRConnectionString") |

## <a name="payload"></a>Délka

Vstupní typ triggeru je deklarován jako `InvocationContext` nebo vlastní typ. Pokud se rozhodnete `InvocationContext` , že budete mít úplný přístup k obsahu žádosti. Pro vlastní typ se modul runtime pokusí analyzovat tělo požadavku JSON pro nastavení vlastností objektu.

### <a name="invocationcontext"></a>InvocationContext

InvocationContext obsahuje veškerý obsah ve zprávě odesílané ze služby signalizace.

|Vlastnost v InvocationContext | Description|
|------------------------------|------------|
|Argumenty| K dispozici pro kategorii *zpráv* . Obsahuje *argumenty* ve [zprávě vyvolání](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) .|
|Chyba| K dispozici pro *odpojenou* událost. Může být prázdné, pokud připojení bylo ukončeno bez chyby nebo obsahuje chybové zprávy.|
|Rozbočovač| Název centra, do kterého zpráva patří|
|Kategorie| Kategorie zprávy|
|Událost| Událost zprávy|
|ConnectionId| ID připojení klienta, které odesílá zprávu.|
|UserId| Identita uživatele klienta, který odesílá zprávu.|
|Hlavičky| Hlavičky požadavku|
|Dotaz| Dotaz na požadavek při připojení klientů ke službě.|
|Deklarace identit| Deklarace identity klienta.|

## <a name="using-parameternames"></a>Používání akce `ParameterNames`

Vlastnost `ParameterNames` v `SignalRTrigger` umožňuje navazovat argumenty vyvolání zpráv do parametrů funkcí. Název, který jste definovali, lze použít jako součást [výrazů vazby](../azure-functions/functions-bindings-expressions-patterns.md) v jiné vazbě nebo jako parametry v kódu. Poskytuje pohodlnější způsob přístupu k argumentům `InvocationContext` .

Řekněme, že máte klienta signalizace JavaScriptu, který se pokouší vyvolat metodu `broadcast` ve funkci Azure Functions se dvěma argumenty `message1` `message2` .

```javascript
await connection.invoke("broadcast", message1, message2);
```

Po nastavení `parameterNames` bude název, který jste definovali, odpovídat argumentům odesílaným na straně klienta. 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

Pak bude `arg1` obsahovat obsah `message1` a `arg2` bude obsahovat obsah `message2` .


### <a name="remarks"></a>Poznámky

Pro vazbu parametru se jedná o pořadí. Pokud používáte `ParameterNames` , pořadí `ParameterNames` odpovídá pořadí argumentů, které jste vyvolali v klientovi. Pokud používáte atribut `[SignalRParameter]` v jazyce C#, pořadí argumentů v metodách funkce Azure se shoduje s pořadím argumentů v klientech.

`ParameterNames` atribut a `[SignalRParameter]` **nelze** použít ve stejnou dobu, nebo se zobrazí výjimka.

## <a name="signalr-service-integration"></a>Integrace služby Signal

Služba Signal potřebuje adresu URL pro přístup k Function App při použití vazby triggeru triggeru služby signalizace. Adresa URL by měla být nakonfigurovaná v **nastavení pro odesílání** na straně služby signalizace. 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="Nadřazený portál":::

Při použití triggeru služby signalizace může být adresa URL jednoduchá a formátovaná, jak je znázorněno níže:

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

Najdete `Function_App_URL` ho na stránce s přehledem Function App a `API_KEY` vygeneruje se pomocí funkce Azure Functions. Můžete získat z okna `API_KEY` `signalr_extension` **klíče aplikace** Function App.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="Klíč rozhraní API":::

Pokud chcete použít více než jednu Function App společně s jednou službou Signalní, může mít nadřazený datový proud také složitá pravidla směrování. Další podrobnosti najdete v [nastavení pro odesílání dat](../azure-signalr/concept-upstream.md).

## <a name="step-by-step-sample"></a>Ukázka krok za krokem

Můžete postupovat podle ukázky v GitHubu a nasazovat chatovací místnost na Function App s vazbou triggeru aktivační události služby a funkcí pro odesílání dat: [Obousměrná konverzační místnost v ukázce](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

## <a name="next-steps"></a>Další kroky

* [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
* [Ukázka vazby triggeru služby Signal](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

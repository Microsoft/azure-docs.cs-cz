---
title: Aktivační událost služby signalizace Azure Functions
description: Naučte se odesílat zprávy služby signalizace z Azure Functions.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: e2651afbcdc3bae71bb531aa0e821f83264c295d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212582"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Aktivační událost služby signalizace pro Azure Functions

Pomocí vazby triggeru *signálu* můžete reagovat na zprávy odesílané službou Azure Signal. Při aktivaci funkce se zprávy předané do funkce analyzují jako objekt JSON.

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
|**textový**| neuvedeno | Musí být nastaven na hodnotu `SignalRTrigger` .|
|**směr**| neuvedeno | Musí být nastaven na hodnotu `in` .|
|**Jméno**| neuvedeno | Název proměnné použitý v kódu funkce pro objekt kontextu vyvolání triggeru |
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

Vlastnost `ParameterNames` v `SignalRTrigger` umožňuje navazovat argumenty vyvolání zpráv do parametrů funkcí. Poskytuje pohodlnější způsob přístupu k argumentům `InvocationContext` .

Řekněme, že máte klienta hlasového signálu s JavaScriptem, který se pokouší vyvolat metodu `broadcast` ve funkci Azure Functions se dvěma argumenty.

```javascript
await connection.invoke("broadcast", message1, message2);
```

K těmto dvěma argumentům můžete přistupovat z parametru a také k přiřazení typu parametru pro ně pomocí `ParameterNames` .

### <a name="remarks"></a>Poznámky

Pro vazbu parametru se jedná o pořadí. Pokud používáte `ParameterNames` , pořadí `ParameterNames` odpovídá pořadí argumentů, které jste vyvolali v klientovi. Pokud používáte atribut `[SignalRParameter]` v jazyce C#, pořadí argumentů v metodách funkce Azure se shoduje s pořadím argumentů v klientech.

`ParameterNames` atribut a `[SignalRParameter]` **nelze** použít ve stejnou dobu, nebo se zobrazí výjimka.

## <a name="send-messages-to-signalr-service-trigger-binding"></a>Odeslat zprávy do aktivační vazby služby Signal

Funkce Azure Functions vygeneruje adresu URL pro aktivační vazby služby Signaler a je naformátovaná takto:

```http
https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>
```

`API_KEY`Služba je vygenerovaná funkcí Azure Functions. Z Azure Portal můžete získat, `API_KEY` když používáte vazbu triggeru služby signalizace.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="Klíč rozhraní API":::

Tato adresa URL by měla být nastavená v `UrlTemplate` části nastavení nadřazeného služby signalizace.

## <a name="next-steps"></a>Další kroky

* [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
* [Ukázka vazby triggeru služby Signal](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples/bidirectional-chat)

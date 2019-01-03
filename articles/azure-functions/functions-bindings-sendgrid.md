---
title: Azure vazby služby SendGrid funkce
description: Referenční informace k vazeb Azure Functions SendGrid.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 3cee083096584d30fb979aaf58bfdc2edf2e6c4f
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971644"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure vazby služby SendGrid funkce

Tento článek vysvětluje, jak odesílat e-mailu pomocí [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) vazby ve službě Azure Functions. Služba Azure Functions podporuje výstupní vazby služby SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby služby SendGrid jsou součástí [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) balíčku NuGet, verze 2.x. Zdrojový kód pro tento balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby služby SendGrid jsou součástí [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) balíčku NuGet, verze 3.x. Zdrojový kód pro tento balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) úložiště GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Příklad:

Podívejte se na příklad specifické pro jazyk:

* [C#](#c-example)
* [C# skript (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Příklad jazyka C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) , že používá fronty Service Bus aktivovat a výstupní vazby služby SendGrid.

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

Nastavení atributu, můžete vynechat `ApiKey` vlastnosti, pokud jste svůj klíč rozhraní API v nastavení aplikace s názvem "AzureWebJobsSendGridApiKey".

### <a name="c-script-example"></a>Ukázkový skript jazyka C#

Následující příklad ukazuje SendGrid výstupní vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba.

Zde je vazba dat v *function.json* souboru:

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

[Konfigurace](#configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

### <a name="javascript-example"></a>Příklad v jazyce JavaScript

Následující příklad ukazuje SendGrid výstupní vazby ve *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba.

Zde je vazba dat v *function.json* souboru:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

[Konfigurace](#configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>Atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) atribut.

Informace o atribut vlastnosti, které můžete nakonfigurovat, najdete v tématu [konfigurace](#configuration). Tady je `SendGrid` příklad atributů v podpisu metody:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Kompletní příklad naleznete v tématu [příklad jazyka C#](#c-example).

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `SendGrid` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**|| Povinné – musí být nastavena na `sendGrid`.|
|**direction**|| Povinné – musí být nastavena na `out`.|
|**Jméno**|| Požadovaná: název této proměnné v kódu funkce žádosti nebo text žádosti. Tato hodnota je ```$return``` po pouze jeden návratovou hodnotu. |
|**apiKey**|**ApiKey**| Název nastavení aplikace, která obsahuje váš klíč rozhraní API. Pokud není nastaven, nastavení aplikace výchozí název je "AzureWebJobsSendGridApiKey".|
|**k**|**Komu**| příjemce e-mailovou adresu. |
|**z**|**z**| E-mailová adresa odesílatele. |
|**Předmět**|**Předmět**| Předmět e-mailu. |
|**Text**|**Text**| obsah e-mailu. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení Host.JSON

Tato část popisuje globální konfiguraci nastavení k dispozici pro tuto vazbu ve verzi 2.x. Příklad souboru host.json níže obsahuje pouze verzi 2.x nastavení pro tuto vazbu. Další informace o globální nastavení konfigurace ve verzi 2.x, naleznete v tématu [referenční materiály k host.json pro Azure Functions verze 2.x](functions-host-json.md).

> [!NOTE]
> Pro odkaz host.json ve funkcích 1.x, najdete v článku [referenční materiály k host.json pro Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|od|neuvedeno|E-mailová adresa odesílatele přes všechny funkce.| 


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

---
title: Azure sendgrid vám umožňuje funkce vazby
description: Azure odkaz funkce sendgrid vám umožňuje vazby.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/29/2017
ms.author: tdykstra
ms.openlocfilehash: 29f6b3e8b7d7d940da098953e8f9d3deaccf78dc
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure sendgrid vám umožňuje funkce vazby

Tento článek vysvětluje postup odesílání e-mailu pomocí [sendgrid vám umožňuje](https://sendgrid.com/docs/User_Guide/index.html) vazeb v Azure Functions. Azure Functions podporuje vazbu výstup pro sendgrid vám umožňuje.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Balíčky

Vazby sendgrid vám umožňuje jsou součástí [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) balíček NuGet. Zdrojový kód pro balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

## <a name="example"></a>Příklad:

Podívejte se na konkrétní jazyk příklad:

* [C#](#c-example)
* [C# skript (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) , používá fronty Service Bus aktivovat a sendgrid vám umožňuje výstupní vazby.

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
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

Nastavení atributu, můžete vynechat `ApiKey` vlastnost, pokud máte klíč rozhraní API v nastavení aplikace s názvem "AzureWebJobsSendGridApiKey".

### <a name="c-script-example"></a>Příklad skriptu jazyka C#

Následující příklad ukazuje výstup sendgrid vám umožňuje vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby.

Zde je vazba dat v *function.json* souboru:

```json 
{
    "bindings": [
        {
            "name": "message",
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

[Konfigurace](#configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

### <a name="javascript-example"></a>Příklad v jazyce JavaScript

Následující příklad ukazuje výstup sendgrid vám umožňuje vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby.

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

[Konfigurace](#configuration) část vysvětluje tyto vlastnosti.

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

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [sendgrid vám umožňuje](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) atribut.

Informace o vlastnostech atributů, které můžete konfigurovat, najdete v tématu [konfigurace](#configuration). Tady je `SendGrid` atribut příkladu podpis metody:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Úplný příklad najdete v tématu [příklad jazyka C#](#c-example).

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `SendGrid` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type**|| Vyžaduje - musí být nastavena na `sendGrid`.|
|**direction**|| Vyžaduje - musí být nastavena na `out`.|
|**Jméno**|| Požadovaná proměnná používá v kódu funkce pro požadavek nebo textu požadavku. Tato hodnota je ```$return``` po pouze jeden návratovou hodnotu. |
|**apiKey**|**apiKey**| Název nastavení aplikace, který obsahuje klíč rozhraní API. Pokud není nastaven, aplikace výchozí nastavení je název "AzureWebJobsSendGridApiKey".|
|**K**|**Komu**| příjemce e-mailovou adresu. |
|**Z**|**Z**| e-mailovou adresu odesílatele. |
|**Předmět**|**Předmět**| Předmět e-mailu. |
|**Text**|**Text**| obsah e-mailu. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
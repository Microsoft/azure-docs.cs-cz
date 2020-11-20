---
title: Azure Functions vazby SendGrid
description: Odkaz na Azure Functions SendGridch vazeb
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: b3d09ec4c4ab578a87f0d983c0f243bee2a84597
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991226"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions vazby SendGrid

Tento článek vysvětluje, jak odeslat e-maily pomocí vazeb [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) v Azure Functions. Azure Functions podporuje výstupní vazbu pro SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1. x

Vazby SendGrid jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) , verze 2. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vazby SendGrid jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) , verze 3. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [funkci jazyka C#](functions-dotnet-class-library.md) , která používá aktivační událost Service Bus Queue a výstupní vazbu SendGrid.

### <a name="synchronous"></a>Synchronní

```cs
using SendGrid.Helpers.Mail;
using System.Text.Json;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonSerializer.Deserialize<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>Asynchronní

```cs
using SendGrid.Helpers.Mail;
using System.Text.Json;

...

[FunctionName("SendEmail")]
public static async Task Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonSerializer.Deserialize<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

Nastavení vlastnosti atributu můžete vynechat, `ApiKey` Pokud máte klíč rozhraní API v nastavení aplikace s názvem "AzureWebJobsSendGridApiKey".

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje výstupní vazbu SendGrid v souboru *function.js* a [funkci skriptu jazyka C#](functions-reference-csharp.md) , která používá vazbu.

Tady jsou data vazby v *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu SendGrid v souboru v *function.js* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu.

Tady jsou data vazby v *function.js* souboru:

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Tady je kód JavaScriptu:

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

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje funkci aktivovanou protokolem HTTP, která pošle e-mail s použitím vazby SendGrid. V konfiguraci vazby můžete zadat výchozí hodnoty. Například e-mailová *adresa je nakonfigurována* v *function.jsna*. 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

Následující funkce ukazuje, jak můžete zadat vlastní hodnoty pro volitelné vlastnosti.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad používá `@SendGridOutput` anotaci z [běhové knihovny Functions jazyka Java](/java/api/overview/azure/functions/runtime) k odeslání e-mailu pomocí výstupní vazby SendGrid.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

# <a name="c"></a>[C#](#tab/csharp)

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) .

Informace o vlastnostech atributů, které můžete konfigurovat, najdete v tématu [Konfigurace](#configuration). Tady je `SendGrid` příklad atributu v signatuře metody:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Úplný příklad najdete v tématu [příklad v jazyce C#](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Skripty jazyka C# nepodporují atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

[SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) anotace umožňuje deklarativní konfiguraci vazby SendGrid poskytováním hodnot konfigurace. Další podrobnosti najdete v částech s [příkladem](#example) a [konfigurací](#configuration) .

---

## <a name="configuration"></a>Konfigurace

V následující tabulce jsou uvedeny vlastnosti konfigurace vazby, které jsou k dispozici v  *function.jspro* soubor a `SendGrid` atribut nebo anotaci.

| *function.js* vlastnost | Atribut nebo vlastnost anotace | Popis | Volitelné |
|--------------------------|-------------------------------|-------------|----------|
| typ |neuvedeno| Musí být nastaven na hodnotu `sendGrid` .| Ne |
| směr |neuvedeno| Musí být nastaven na hodnotu `out` .| Ne |
| name |neuvedeno| Název proměnné použitý v kódu funkce pro text žádosti nebo žádosti. Tato hodnota je `$return` , pokud je k dispozici pouze jedna návratová hodnota. | Ne |
| apiKey | ApiKey | Název nastavení aplikace, které obsahuje klíč rozhraní API. Pokud není nastavená, výchozí název nastavení aplikace je *AzureWebJobsSendGridApiKey*.| Ne |
| na| Záměr | E-mailová adresa příjemce | Yes |
| Výsledkem| Z | E-mailová adresa odesílatele |  Yes |
| závislosti| Předmět | Předmět e-mailu. | Yes |
| text| Text | Obsah e-mailu | Yes |

Volitelné vlastnosti mohou mít ve vazbě definovány výchozí hodnoty a buď přidány nebo přepsány programově.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsnastavení

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Příklad host.jsv souboru níže obsahuje pouze nastavení verze 2. x + pro tuto vazbu. Další informace o globálních nastaveních konfigurace ve verzích 2. x a novějších naleznete v tématu [host.jsv referenčních informacích pro Azure Functions](functions-host-json.md).

> [!NOTE]
> Odkaz na host.jspro ve funkcích 1. x naleznete v tématu [host.json reference for Azure Functions 1. x](functions-host-json-v1.md).

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
|Výsledkem|neuvedeno|E-mailová adresa odesílatele napříč všemi funkcemi.| 


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)

---
title: Vazby SendGrid funkcí Azure
description: Azure Functions SendGrid odkazy.
author: craigshoemaker
ms.topic: reference
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 9ed2b81c12c698822b9542bb6903189c865b572b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277463"
---
# <a name="azure-functions-sendgrid-bindings"></a>Vazby SendGrid funkcí Azure

Tento článek vysvětluje, jak odesílat e-maily pomocí [vazby SendGrid](https://sendgrid.com/docs/User_Guide/index.html) v Azure Functions. Funkce Azure podporuje výstupní vazbu pro SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky - Funkce 1.x

Vazby SendGrid jsou k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet verze 2.x. Zdrojový kód pro balíček je v úložišti [GitHub azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky - Funkce 2.x a vyšší

Vazby SendGrid jsou k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet verze 3.x. Zdrojový kód pro balíček je v úložišti [GitHub azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/)

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [funkci C#,](functions-dotnet-class-library.md) která používá aktivační událost fronty služby Service Bus a výstupní vazbu SendGrid.

### <a name="synchronous"></a>Synchronní

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

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

...

[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

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

Nastavení `ApiKey` vlastnosti atributu můžete vynechat, pokud máte klíč rozhraní API v nastavení aplikace s názvem "AzureWebJobsSendGridApiKey".

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje výstupní vazbu SendGrid v souboru *function.json* a [funkci skriptu Jazyka C#,](functions-reference-csharp.md) která vazbu používá.

Zde jsou data vazby v souboru *function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód skriptu C#:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje výstupní vazbu SendGrid v souboru *function.json* a [funkci JavaScriptu,](functions-reference-node.md) která vazbu používá.

Zde jsou data vazby v souboru *function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

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

Následující příklad ukazuje funkci aktivovanou protokolem HTTP, která odešle e-mail pomocí vazby SendGrid. V konfiguraci vazby můžete zadat výchozí hodnoty. Například *od* e-mailová adresa je nakonfigurován v *function.json*. 

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

Následující příklad používá `@SendGridOutput` poznámku z [knihovny runtime funkcí Jazyka Java](/java/api/overview/azure/functions/runtime) k odeslání e-mailu pomocí výstupní vazby SendGrid.

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

# <a name="c"></a>[C #](#tab/csharp)

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [SendGrid.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs)

Informace o vlastnostech atributů, které lze konfigurovat, naleznete v [tématu Configuration](#configuration). Zde je `SendGrid` příklad atributu v podpisu metody:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Úplný příklad naleznete v [příkladu jazyka C#](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Anotace [SendGridOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) umožňuje deklarativně konfigurovat vazbu SendGrid poskytnutím konfiguračních hodnot. Další podrobnosti najdete v [příkladu](#example) a v částech [konfigurace.](#configuration)

---

## <a name="configuration"></a>Konfigurace

V následující tabulce jsou uvedeny vlastnosti konfigurace vazby, `SendGrid` které jsou k dispozici v souboru *function.json,* a atribut/anotace.

| *vlastnost function.json* | Vlastnost Atribut/anotace | Popis | Nepovinné |
|--------------------------|-------------------------------|-------------|----------|
| type |neuvedeno| Musí být `sendGrid`nastavena na .| Ne |
| směr |neuvedeno| Musí být `out`nastavena na .| Ne |
| jméno |neuvedeno| Název proměnné použitý v kódu funkce pro text požadavku nebo požadavku. Tato hodnota `$return` je, když existuje pouze jedna vrácená hodnota. | Ne |
| apiKey | ApiKey | Název nastavení aplikace, která obsahuje klíč rozhraní API. Pokud není nastaveno, výchozí název nastavení aplikace je *AzureWebJobsSendGridApiKey*.| Ne |
| na| Akce | E-mailová adresa příjemce. | Ano |
| Z| From | E-mailová adresa odesílatele. |  Ano |
| Předmět| Subjekt | Předmět e-mailu. | Ano |
| text| Text | Obsah e-mailu. | Ano |

Volitelné vlastnosti mohou mít výchozí hodnoty definované ve vazbě a buď přidány nebo přepsány programově.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>nastavení host.json

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2.x a vyšších. Příklad souboru host.json níže obsahuje pouze nastavení verze 2.x+ pro tuto vazbu. Další informace o nastavení globální konfigurace ve verzích 2.x a dále naleznete [v odkazu host.json pro funkce Azure](functions-host-json.md).

> [!NOTE]
> Odkaz na host.json ve funkcích 1.x najdete v [tématu reference host.json pro Funkce Azure 1.x](functions-host-json-v1.md).

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
|Z|neuvedeno|E-mailová adresa odesílatele ve všech funkcích.| 


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách funkcí Azure](functions-triggers-bindings.md)

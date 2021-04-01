---
title: Notification Hubs vazby pro Azure Functions
description: Vysvětlení použití vazby centra oznámení Azure v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: c4198a1b73f76d61e39324befc85b55bd260e363
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212216"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Notification Hubs výstupní vazba pro Azure Functions

Tento článek vysvětluje, jak odesílat nabízená oznámení pomocí vazeb [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) v Azure Functions. Azure Functions podporuje výstupní vazby pro Notification Hubs.

Pro službu PNS (Platform Notification Service), kterou chcete použít, musí být nakonfigurována služba Azure Notification Hubs. Informace o tom, jak v klientské aplikaci získat nabízená oznámení z Notification Hubs, najdete v tématu [Začínáme s Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) a v rozevíracím seznamu v horní části stránky vyberte cílovou klientskou platformu.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google má [nepoužívané Google Cloud Messaging (GCM) ve prospěch Firebase Cloud Messaging (FCM)](https://developers.google.com/cloud-messaging/faq). Tato výstupní vazba nepodporuje FCM. K odesílání oznámení pomocí FCM použijte [rozhraní Firebase API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) přímo ve své funkci nebo použijte [oznámení šablon](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Balíčky – funkce 1. x

Vazby Notification Hubs jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) , verze 1. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Tato vazba není k dispozici ve funkcích 2. x a vyšší.

## <a name="example---template"></a>Příklad šablony

Oznámení, která odesíláte, můžou být nativní oznámení nebo [oznámení šablon](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Nativní oznámení cílí na konkrétní klientskou platformu, jak je nakonfigurovaná ve `platform` vlastnosti výstupní vazby. Oznámení šablony lze použít k zacílení na více platforem.   

Podívejte se na příklad konkrétního jazyka:

* [Parametr skriptu C# – výstup](#c-script-template-example---out-parameter)
* [Skript jazyka C# – asynchronní](#c-script-template-example---asynchronous)
* [Skript C# – JSON](#c-script-template-example---json)
* [Typy knihoven skriptů v jazyce C#](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Ukázkový parametr šablony skriptu jazyka C#

Tento příklad pošle oznámení pro [registraci šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , která obsahuje `message` zástupný symbol v šabloně.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>Příklad šablony skriptu C# – asynchronní

Používáte-li asynchronní kód, nejsou povoleny výstupní parametry. V tomto případě použijte `IAsyncCollector` k vrácení oznámení šablony. Následující kód je asynchronní příklad kódu výše. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>Příklad šablony skriptu C# – JSON

Tento příklad pošle oznámení pro [registraci šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , která obsahuje `message` zástupný symbol v šabloně pomocí platného řetězce JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Příklad šablony skriptu C# – typy knihoven

Tento příklad ukazuje, jak používat typy definované v [knihovně Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>Příklad šablony F #

Tento příklad pošle oznámení pro [registraci šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , která obsahuje `location` a `message` .

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Příklad šablony JavaScriptu

Tento příklad pošle oznámení pro [registraci šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , která obsahuje `location` a `message` .

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Příklad – Native APNS

Tento ukázkový skript v jazyce C# ukazuje, jak odeslat nativní oznámení APNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Příklad – WNS Native

Tento příklad skriptu jazyka C# ukazuje, jak použít typy definované v [knihovně Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) k odeslání nativního informačního oznámení WNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Atributy

V [knihovnách tříd jazyka C#](functions-dotnet-class-library.md)použijte atribut [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) .

Parametry a vlastnosti konstruktoru atributu jsou popsány v oddílu [Konfigurace](#configuration) .

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.jspro* soubor a `NotificationHub` atribut:

|function.jsvlastnost | Vlastnost atributu |Description|
|---------|---------|----------------------|
|**textový** |Není k dispozici| Musí být nastaven na hodnotu `notificationHub` . |
|**směr** |Není k dispozici| Musí být nastaven na hodnotu `out` . | 
|**Jméno** |Není k dispozici| Název proměnné použitý v kódu funkce pro zprávu centra oznámení |
|**tagExpression** |**TagExpression** | Výrazy značek umožňují určit, že oznámení budou doručena do sady zařízení, která jsou zaregistrovaná pro příjem oznámení, která odpovídají výrazu značky.  Další informace najdete v tématu [výrazy směrování a značek](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Název prostředku centra oznámení v Azure Portal. |
|**vázán** | **ConnectionStringSetting** | Název nastavení aplikace, které obsahuje připojovací řetězec Notification Hubs.  Připojovací řetězec musí být nastaven na hodnotu *DefaultFullSharedAccessSignature* vašeho centra oznámení. Viz [nastavení připojovacího řetězce](#connection-string-setup) dále v tomto článku.|
|**platformy** | **Platforma** | Vlastnost Platform označuje klientskou platformu, na kterou vaše oznámení cílí. Ve výchozím nastavení platí, že pokud je vlastnost Platform vynechána z výstupní vazby, lze pomocí oznámení šablon cílit na jakoukoli platformu nakonfigurovanou v centru oznámení Azure. Další informace o použití šablon obecně k odesílání oznámení pro různé platformy pomocí Centra oznámení Azure najdete v tématu [šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Při nastavení musí být **platforma** jedna z následujících hodnot: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Další informace o konfiguraci centra oznámení pro službu APN a přijetí oznámení v klientské aplikaci najdete v tématu [Posílání nabízených oznámení do systému iOS pomocí Azure Notification Hubs](../notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Další informace o konfiguraci centra oznámení pro ADM a přijetí oznámení v aplikaci Kindle najdete v tématu [Začínáme Notification Hubs pro aplikace Kindle](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Služba nabízených oznámení Windows](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) cílí na platformy Windows. WNS podporuje i Windows Phone 8,1 a novější. Další informace najdete v tématu [Začínáme se Notification Hubs pro aplikace Univerzální platformy Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Služba nabízených oznámení Microsoftu](/previous-versions/windows/apps/ff402558(v=vs.105)). Tato platforma podporuje Windows Phone 8 a starších Windows Phonech platforem. Další informace najdete v tématu [Posílání nabízených oznámení pomocí Azure Notification Hubs v Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Příklad souboru function.js

Tady je příklad vazby Notification Hubs v *function.js* souboru.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Nastavení připojovacího řetězce

Chcete-li použít výstupní vazbu centra oznámení, je nutné nakonfigurovat připojovací řetězec pro centrum. Můžete vybrat existující centrum oznámení nebo vytvořit nové přímo z karty *integrace* v Azure Portal. Připojovací řetězec můžete také nakonfigurovat ručně. 

Konfigurace připojovacího řetězce pro existující centrum oznámení:

1. V [Azure Portal](https://portal.azure.com)přejděte do centra oznámení, zvolte **zásady přístupu** a klikněte na tlačítko Kopírovat vedle zásady **DefaultFullSharedAccessSignature** . Tím se do centra oznámení zkopíruje připojovací řetězec pro zásady *DefaultFullSharedAccessSignature* . Tento připojovací řetězec umožňuje, aby vaše funkce odesílala zprávy s oznámením do centra.
    ![Zkopírování připojovacího řetězce centra oznámení](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. V Azure Portal přejděte do aplikace Function App, zvolte **nastavení aplikace**, přidejte klíč, jako je například **MyHubConnectionString**, vložte zkopírované *DefaultFullSharedAccessSignature*  pro vaše centrum oznámení jako hodnotu a potom klikněte na **Uložit**.

Název tohoto nastavení aplikace je to, co se nachází v nastavení připojení výstupní vazby v *function.jsna* nebo atributu .NET. Viz [část konfigurace](#configuration) výše v tomto článku.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Reference |
|---|---|
| Centrum oznámení | [Provozní příručka](/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách Azure Functions](functions-triggers-bindings.md)

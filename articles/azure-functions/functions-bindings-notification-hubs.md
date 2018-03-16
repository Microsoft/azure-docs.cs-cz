---
title: "Vazby centra oznámení pro Azure Functions"
description: "Pochopit, jak používat centra oznámení Azure vazby v Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Funkce Azure, funkce zpracování událostí, dynamické výpočetní architektura bez serveru"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 292c8295cbc2705c12365a20cee0e80b6da639a5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Centra oznámení výstup vazby pro Azure Functions

Tento článek vysvětluje, jak odesílat nabízená oznámení pomocí [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) vazeb v Azure Functions. Azure funkce podporuje výstup vazby pro centra oznámení.

Centra oznámení Azure musí být nakonfigurovaný pro služby platformy oznámení (PNS) chcete použít. Další informace o získání nabízená oznámení v aplikaci klienta z centra oznámení, najdete v tématu [Začínáme s Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) a vyberte svou cílovou platformu klienta z rozevíracího seznamu v horní části stránky.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Balíčky

Vazby centra oznámení jsou součástí [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) balíček NuGet. Zdrojový kód pro balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example---template"></a>Příklad – šablony

Oznámení můžete odesílat může být nativní oznámení nebo [šablony oznámení](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Nativní oznámení cílit na konkrétním klientovi platformu jako nakonfigurovaný v `platform` vlastnost vazby výstup. Šablona oznámení můžete použít pro více cílových platforem.   

Podívejte se na konkrétní jazyk příklad:

* [C# skript - parametr](#c-script-template-example---out-parameter)
* [C# skript - asynchronní](#c-script-template-example---asynchronous)
* [C# skript – JSON](#c-script-template-example---json)
* [C# skript - knihovny typů](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# skript příkladu šablony - vnější parametr

Tento příklad odešle oznámení [šablony registrace](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) obsahující `message` zástupný symbol v šabloně.

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

### <a name="c-script-template-example---asynchronous"></a>C# šablony příklad skriptu - asynchronní

Pokud používáte asynchronní kódu, výstupní parametry nejsou povoleny. V takovém případě použijte `IAsyncCollector` vrátit šablony oznámení. Následující kód je příkladem asynchronní výše uvedený kód. 

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

### <a name="c-script-template-example---json"></a>C# příklad šablony skriptu – JSON

Tento příklad odešle oznámení [šablony registrace](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) obsahující `message` zástupný symbol v šabloně pomocí platný řetězec formátu JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# příklad šablony skriptu - knihovny typů

Tento příklad ukazuje, jak používat typy definované v [knihovnu Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

Tento příklad odešle oznámení [šablony registrace](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) obsahující `location` a `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Příklad šablony v jazyce JavaScript

Tento příklad odešle oznámení [šablony registrace](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) obsahující `location` a `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
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

## <a name="example---apns-native"></a>Příklad: nativní služby APN

Tento příklad skriptu jazyka C# ukazuje, jak odeslat nativní oznámení APNS. 

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

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---gcm-native"></a>Příklad: nativní GCM

Tento příklad skriptu jazyka C# ukazuje, jak odeslat nativní oznámení GCM. 

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
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Příklad - WNS nativní

Tento příklad skriptu jazyka C# ukazuje způsob použití typy definované v [knihovnu Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) odeslat nativní oznámení WNS informační zprávy. 

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

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) atribut.

Konstruktor parametry a vlastnosti atributu jsou popsané [konfigurace](#configuration) části.

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `NotificationHub` atribut:

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** |neuvedeno| Musí být nastavena na "notificationHub". |
|**Směr** |neuvedeno| Musí být nastavena na "out". | 
|**name** |neuvedeno| Název proměnné používá v kódu funkce pro centra oznámení. |
|**tagExpression** |**TagExpression** | Výrazy značky umožňují určit doručit oznámení na sadu zařízení, která jste zaregistrovali k odběru oznámení, které odpovídají výrazu označení.  Další informace najdete v tématu [výrazy směrování a značky](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Název prostředku centra oznámení na portálu Azure. |
|**Připojení** | **ConnectionStringSetting** | Název nastavení aplikace, který obsahuje připojovací řetězec centra oznámení.  Připojovací řetězec musí být nastavena na *DefaultFullSharedAccessSignature* hodnotu pro vaše Centrum oznámení. V tématu [nastavení řetězce připojení](#connection-string-setup) dále v tomto článku.|
|**Platforma** | **Platforma** | Vlastnost platformy označuje klientské platformy vaše cíle oznámení. Ve výchozím nastavení pokud je vlastnost platformy vynechán z vazby výstup šablony oznámení lze cílit na libovolnou platformu nakonfigurované na do centra oznámení Azure. Další informace o použití šablony obecně pro různé platformy oznámení pomocí Azure Notification Hubs k odesílání najdete v tématu [šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Pokud nastavíte, **platformy** musí mít jednu z následujících hodnot: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Další informace o konfiguraci centra oznámení pro služby APN a příjem oznámení v aplikaci klienta najdete v tématu [odesílající nabízená oznámení iOS pomocí Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Službu Amazon Device Messaging](https://developer.amazon.com/device-messaging). Další informace o konfiguraci centra oznámení pro ADM a příjem oznámení ve Kindle aplikaci najdete v tématu [Začínáme s Notification Hubs pro aplikace Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, což je nová verze služby GCM, je také podporována. Další informace najdete v tématu [odesílající nabízená oznámení do systému Android pomocí Azure Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Službu nabízených oznámení Windows](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) cílení na platformy Windows. Windows Phone 8.1 a novější také podporuje WNS. Další informace najdete v tématu [Začínáme s centra oznámení pro univerzální platformu aplikace Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Microsoft služba nabízených oznámení](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Tato platforma podporuje Windows Phone 8 a starší operační systémy Windows Phone. Další informace najdete v tématu [odesílající nabízená oznámení pomocí Azure Notification Hubs ve Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Příklad souboru Function.JSON

Tady je příklad vazby v Notification Hubs *function.json* souboru.

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
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Nastavení připojení řetězce

Pokud chcete použít výstupu centra oznámení vazby, musíte nakonfigurovat připojovací řetězec pro rozbočovač. Můžete vybrat existující centru oznámení nebo vytvořit nový jeden přímo z *integrací* na portálu Azure. Připojovací řetězec můžete také nakonfigurovat ručně. 

Připojovací řetězec k centru oznámení existující konfigurace:

1. Přejděte do centra oznámení v [portál Azure](https://portal.azure.com), zvolte **zásady přístupu**a vyberte tlačítko Kopírovat do **DefaultFullSharedAccessSignature** zásad. Zkopíruje připojovací řetězec pro *DefaultFullSharedAccessSignature* zásad do vašeho centra oznámení. Tento připojovací řetězec umožňuje funkce zasílání oznámení k rozbočovači.
    ![Zkopírujte připojovací řetězec centra oznámení](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Přejděte do aplikace pro funkce na portálu Azure, zvolte **nastavení aplikace**, například přidá klíč **MyHubConnectionString**, vložte zkopírovaný *DefaultFullSharedAccessSignature* pro vaše Centrum oznámení jako hodnotu a pak klikněte na tlačítko **Uložit**.

Název nastavení této aplikace je v nastavení výstupní vazba připojení v co *function.json* nebo atribut rozhraní .NET. Najdete v článku [konfigurační oddíl](#configuration) výše v tomto článku.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Centrum oznámení | [Provozní příručka](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)


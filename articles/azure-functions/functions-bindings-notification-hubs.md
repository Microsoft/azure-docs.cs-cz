---
title: Vazby centra oznámení pro službu Azure Functions
description: Vysvětlení použití vazby centra oznámení Azure ve službě Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: d85c281f198e928be48af950c67c9f53a9d6e547
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086103"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Notification Hubs výstupní vazby pro službu Azure Functions

Tento článek vysvětluje, jak odesílat nabízená oznámení pomocí [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) vazby ve službě Azure Functions. Azure Functions podporuje výstupní vazby pro Notification Hubs.

Azure Notification Hubs, musí být nakonfigurovaný pro službu platformy oznámení (PNS) chcete použít. Další informace o získání nabízených oznámení do vaší klientské aplikace ze služby Notification Hubs, najdete v článku [Začínáme se službou Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) a vyberte svou klientskou platformu cílového z rozevíracího seznamu v horní části stránky.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby centra oznámení jsou k dispozici v [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) balíčku NuGet, verzi 1.x. Zdrojový kód pro tento balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Tato vazba není k dispozici ve funkcích 2.x.

## <a name="example---template"></a>Příklad – šablony

Oznámení můžete odesílat může být nativní oznámení nebo [šablony oznámení](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Podle konfigurace v nativní oznámení cílit na konkrétní klientskou platformu `platform` vlastnost výstupní vazbu. Šablona oznámení můžete použít pro více cílových platforem.   

Podívejte se na příklad specifické pro jazyk:

* [Skript jazyka C# – výstupní parametr](#c-script-template-example---out-parameter)
* [Skript jazyka C# - asynchronní](#c-script-template-example---asynchronous)
* [Skript jazyka C# – JSON](#c-script-template-example---json)
* [Skript jazyka C# – knihovny typů](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Příklad jazyka C# skript šablony - výstupní parametr

Tento příklad odešle oznámení [šablona registrace](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , která obsahuje `message` zástupný symbol v šabloně.

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

### <a name="c-script-template-example---asynchronous"></a>Příklad jazyka C# skript šablony - asynchronní

Pokud používáte asynchronní kód, výstupní parametry nejsou povoleny. V tomto případě použijte `IAsyncCollector` vrátit šablonu oznámení. Následující kód je příklad asynchronní výše uvedený kód. 

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

### <a name="c-script-template-example---json"></a>Příklad jazyka C# skript šablony - JSON

Tento příklad odešle oznámení [šablona registrace](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , která obsahuje `message` zástupný symbol v šabloně platný řetězec formátu JSON pomocí.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Příklad jazyka C# skript šablony – knihovny typů

Tento příklad ukazuje, jak používat typy definované v [knihovny Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

Tento příklad odešle oznámení [šablona registrace](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) obsahující `location` a `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Příklad šablony v jazyce JavaScript

Tento příklad odešle oznámení [šablona registrace](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) obsahující `location` a `message`.

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

## <a name="example---apns-native"></a>Příklad – nativní služby APN

Tento ukázkový skript jazyka C# ukazuje, jak posílat nativní oznámení APNS. 

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

## <a name="example---gcm-native"></a>Příklad – nativní GCM

Tento ukázkový skript jazyka C# ukazuje, jak posílat nativní oznámení GCM. 

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

## <a name="example---wns-native"></a>Příklad – nativní WNS

Tento ukázkový skript jazyka C# ukazuje, jak používat typy definované v [knihovny Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) odesílat nativní oznámení s informační zprávou nabízených oznámení Windows. 

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

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) atribut.

Vlastnosti a parametry konstruktoru atributu jsou popsané [konfigurace](#configuration) části.

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `NotificationHub` atribut:

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**type** |neuvedeno| Musí být nastavena na "notificationHub". |
|**direction** |neuvedeno| Musí být nastavena na "out". | 
|**Jméno** |neuvedeno| Název proměnné používá v kódu funkce pro centra oznámení. |
|**tagExpression** |**tagExpression** | Výrazy označení umožňují určit, že oznámení bude doručen do skupiny zařízení, které jste se zaregistrovali k přijímání oznámení, které odpovídají výrazu značky.  Další informace najdete v tématu [směrování a značky výrazy](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Název prostředku centra oznámení na webu Azure Portal. |
|**připojení** | **connectionStringSetting** | Název nastavení aplikace, které obsahuje připojovací řetězec centra oznámení.  Připojovací řetězec musí být nastavena *DefaultFullSharedAccessSignature* hodnotu pro vaše Centrum oznámení. Zobrazit [nastavení řetězce připojení](#connection-string-setup) dále v tomto článku.|
|**Platforma** | **Platforma** | Vlastnost platformy označuje klientská platforma, vaše cíle oznámení. Ve výchozím nastavení pokud vlastnost platformy je vynecháno z výstupní vazbu šablony oznámení je možné cílit na libovolnou platformu nakonfigurované v centru oznámení Azure. Další informace o použití šablon obecně pro odeslání pro různé platformy oznámení pomocí centra oznámení Azure, najdete v části [šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Pokud nastavíte, **platformy** musí být jedna z následujících hodnot: <ul><li><code>apns</code>&mdash;Služba nabízených oznámení společnosti Apple. Další informace o konfiguraci centra oznámení pro služby APN a přijímání oznámení v aplikaci klienta najdete v tématu [odesílání nabízených oznámení do iOS pomocí Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Další informace o konfiguraci centra oznámení pro správce a přijímání oznámení do aplikace kindle a využívá, najdete v části [Začínáme se službou Notification Hubs pro aplikace Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Firebase Cloud Messaging, což je nová verze služby GCM, je také podporována. Další informace najdete v tématu [odesílání nabízených oznámení do systému Android pomocí Azure Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Služby nabízených oznámení Windows](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) cílení na platformy Windows. Windows Phone 8.1 a novější také podporuje služby nabízených oznámení Windows. Další informace najdete v tématu [Začínáme se službou Notification Hubs pro Windows Universal Platform aplikace](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Microsoft služba nabízených oznámení](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Tato platforma podporuje starší platformy Windows Phone a Windows Phone 8. Další informace najdete v tématu [odesílání nabízených oznámení pomocí Azure Notification Hubs ve Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

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

### <a name="connection-string-setup"></a>Nastavení připojovací řetězce

Pokud chcete použít na výstup centra oznámení vazby, musíte nakonfigurovat připojovací řetězec centra. Můžete vybrat existující centrum oznámení nebo vytvořit nový jeden přímo ze *integrace* karta na portálu Azure portal. Připojovací řetězec můžete také nakonfigurovat ručně. 

Konfigurace připojovacího řetězce pro existující centrum oznámení:

1. Přejděte do svého centra oznámení v [webu Azure portal](https://portal.azure.com), zvolte **zásady přístupu**a vyberte vedle tlačítka pro kopírování **DefaultFullSharedAccessSignature** zásad. To zkopíruje připojovací řetězec pro *DefaultFullSharedAccessSignature* zásady pro vaše Centrum oznámení. Tento připojovací řetězec umožňuje funkci zasílání oznámení k rozbočovači.
    ![Zkopírujte připojovací řetězec centra oznámení](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Přejděte do vaší aplikace function app na webu Azure Portal, zvolte **nastavení aplikace**, jako například přidat klíč **MyHubConnectionString**, vložte zkopírovaný *DefaultFullSharedAccessSignature* pro vaše Centrum oznámení jako hodnotu a pak klikněte na tlačítko **Uložit**.

Název tohoto nastavení aplikace je co přejde v nastavení výstupní vazbu připojení v *function.json* nebo atribut .NET. Zobrazit [konfiguračního oddílu](#configuration) dříve v tomto článku.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Referenční informace |
|---|---|
| Centrum oznámení | [Provozní příručka](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)


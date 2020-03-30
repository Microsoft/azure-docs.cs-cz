---
title: Vazby centra oznámení pro funkce Azure
description: Zjistěte, jak používat vazby Azure Notification Hub v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 211f8c8a203b81a4df6a8e9515b403f99cec572a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277281"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Výstupní vazba centra oznámení pro funkce Azure

Tento článek vysvětluje, jak odesílat nabízená oznámení pomocí [vazby Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) v Azure Functions. Funkce Azure podporuje výstupní vazby pro centra oznámení.

Centra oznámení Azure musí být nakonfigurovaná pro službu oznámení platformy (PNS), kterou chcete použít. Informace o tom, jak dostávat nabízená oznámení v klientské aplikaci z center oznámení, najdete [v tématu Začínáme s centry oznámení](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) a vyberte cílovou klientskou platformu z rozevíracího seznamu v horní části stránky.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Společnost Google [zastarala službu Google Cloud Messaging (GCM) ve prospěch služby Firebase Cloud Messaging (FCM).](https://developers.google.com/cloud-messaging/faq) Tato výstupní vazba nepodporuje FCM. Chcete-li odesílat oznámení pomocí FCM, použijte [rozhraní Firebase API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) přímo ve vaší funkci nebo použijte [oznámení šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

## <a name="packages---functions-1x"></a>Balíčky - Funkce 1.x

Vazby centra oznámení jsou k dispozici v balíčku [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet verze 1.x. Zdrojový kód pro balíček je v úložišti [GitHub azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Balíčky - Funkce 2.x a vyšší

Tato vazba není k dispozici ve funkcích 2.x a vyšší.

## <a name="example---template"></a>Příklad - šablona

Odesílaná oznámení mohou být nativní oznámení nebo [oznámení šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Nativní oznámení cíl konkrétní klientskou `platform` platformu, jak je nakonfigurovánve vlastnosti výstupní vazby. Oznámení šablony lze použít k cílení na více platforem.   

Podívejte se na příklad pro konkrétní jazyk:

* [C# skript - out parametr](#c-script-template-example---out-parameter)
* [Skript Jazyka C# - asynchronní](#c-script-template-example---asynchronous)
* [C# skript - JSON](#c-script-template-example---json)
* [Skript Jazyka C# – typy knihoven](#c-script-template-example---library-types)
* [F #](#f-template-example)
* [Javascript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Příklad šablony skriptu jazyka C# - out parametr

Tento příklad odešle oznámení o [registraci šablony,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) která obsahuje zástupný `message` symbol v šabloně.

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

### <a name="c-script-template-example---asynchronous"></a>Příklad šablony skriptu jazyka C# - asynchronní

Pokud používáte asynchronní kód, nejsou povoleny parametry out. V takovém `IAsyncCollector` případě použijte k vrácení oznámení šablony. Následující kód je asynchronní příklad výše uvedeného kódu. 

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

### <a name="c-script-template-example---json"></a>Příklad šablony skriptu Jazyka C# - JSON

Tento příklad odešle oznámení o [registraci šablony,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) která obsahuje zástupný `message` symbol v šabloně pomocí platného řetězce JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Příklad šablony skriptu jazyka C# - typy knihovny

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

### <a name="f-template-example"></a>Příklad šablony F#

Tento příklad odešle oznámení o `location` [registraci šablony,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) která obsahuje a `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Příklad šablony JavaScriptu

Tento příklad odešle oznámení o `location` [registraci šablony,](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) která obsahuje a `message`.

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

## <a name="example---apns-native"></a>Příklad - APNS nativní

Tento příklad skriptu Jazyka C# ukazuje, jak odeslat nativní oznámení APNS. 

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

## <a name="example---wns-native"></a>Příklad - NAtivní wns

Tento příklad skriptu Jazyka C# ukazuje, jak používat typy definované v [knihovně center oznámení Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) k odeslání nativního informačního upozornění SLUŽBY WNS. 

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

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md)použijte atribut [NotificationHub.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs)

Parametry a vlastnosti konstruktoru atributu jsou popsány v části [konfigurace.](#configuration)

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `NotificationHub` v souboru *function.json* a atribut:

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
|**Typ** |neuvedeno| Musí být `notificationHub`nastavena na . |
|**direction** |neuvedeno| Musí být `out`nastavena na . | 
|**Jméno** |neuvedeno| Název proměnné použitý v kódu funkce pro zprávu centra oznámení. |
|**tagExpression** |**TagExpression** | Výrazy značek umožňují určit, že oznámení budou doručena do sady zařízení, která se zaregistrovala k přijímání oznámení, která odpovídají výrazu značky.  Další informace naleznete v [tématu Směrování a tag výrazy](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**název hubu** | **Název hubu** | Název prostředku centra oznámení na webu Azure Portal. |
|**Připojení** | **ConnectionStringSetting** | Název nastavení aplikace, která obsahuje připojovací řetězec Centra oznámení.  Připojovací řetězec musí být nastavenna na *hodnotu DefaultFullSharedAccessSignature* pro centrum oznámení. Viz [Nastavení připojovacího řetězce](#connection-string-setup) dále v tomto článku.|
|**Platforma** | **Platforma** | Vlastnost platformy označuje klientskou platformu cíle oznámení. Ve výchozím nastavení, pokud je vlastnost platformy vynechána z výstupní vazby, oznámení šablony lze použít k cílení na libovolnou platformu nakonfigurovanou v Centru oznámení Azure. Další informace o používání šablon obecně k odesílání oznámení napříč platformami pomocí Centra oznámení Azure najdete v [tématu Šablony](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Při nastavení musí být **platforma** jedna z následujících hodnot: <ul><li><code>apns</code>&mdash;Služba nabízených oznámení Apple. Další informace o konfiguraci centra oznámení pro APNS a přijímání oznámení v klientské aplikaci najdete v [tématu Odesílání nabízených oznámení do iOS pomocí Centra oznámení Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon zařízení Zasílání zpráv](https://developer.amazon.com/device-messaging). Další informace o konfiguraci centra oznámení pro ADM a přijímání oznámení v aplikaci Kindle najdete v [tématu Začínáme s centry oznámení pro aplikace Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>wns</code>&mdash;[Služba nabízených oznámení systému Windows](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) zaměřená na platformy Windows. Služba Windows Phone 8.1 a novější je také podporována službou WNS. Další informace naleznete [v tématu Začínáme s centry oznámení pro aplikace univerzální platformy Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Služba nabízených oznámení společnosti Microsoft](/previous-versions/windows/apps/ff402558(v=vs.105)). Tato platforma podporuje platformy Windows Phone 8 a starší windows phone. Další informace najdete [v tématu Odesílání nabízených oznámení pomocí Azure Notification Hubs ve Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>příklad souboru function.json

Tady je příklad vazby Centra oznámení v souboru *function.json.*

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

Chcete-li použít výstupní vazbu centra oznámení, musíte nakonfigurovat připojovací řetězec pro rozbočovač. Můžete vybrat existující centrum oznámení nebo vytvořit nové přímo z *karty Integrace* na webu Azure Portal. Připojovací řetězec můžete také nakonfigurovat ručně. 

Postup konfigurace připojovacího řetězce do existujícího centra oznámení:

1. Přejděte do centra oznámení na [webu Azure Portal](https://portal.azure.com), zvolte **zásady přístupu**a vyberte tlačítko kopírování vedle zásad **DefaultFullSharedAccessSignature.** Tím zkopírujete připojovací řetězec pro zásadu *DefaultFullSharedAccessSignature* do centra oznámení. Tento připojovací řetězec umožňuje funkci odesílat oznámení do centra.
    ![Kopírování připojovacího řetězce centra oznámení](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Přejděte do aplikace funkce na webu Azure Portal, zvolte **nastavení aplikace**, přidejte klíč, jako je **MyHubConnectionString**, vložte zkopírovaný *defaultFullSharedAccessSignature* pro centrum oznámení jako hodnotu a pak klikněte na **Uložit**.

Název tohoto nastavení aplikace je to, co jde v nastavení připojení výstupní vazby v *function.json* nebo .NET atribut. Viz [část Konfigurace](#configuration) dříve v tomto článku.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>Výjimky a návratové kódy

| Vazba | Odkaz |
|---|---|
| Centrum oznámení | [Průvodce provozem](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách funkcí Azure](functions-triggers-bindings.md)


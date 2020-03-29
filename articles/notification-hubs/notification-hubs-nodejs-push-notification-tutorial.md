---
title: Odesílání nabízených oznámení pomocí center oznámení Azure a node.js
description: Přečtěte si, jak pomocí center oznámení odesílat nabízená oznámení z aplikace Node.js.
keywords: push oznámení,nabízená oznámení,node.js push,ios push
services: notification-hubs
documentationcenter: nodejs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6e109c5a7f4911893c81c88ae84322fb962fff6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71213190"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Odesílání nabízených oznámení pomocí center oznámení Azure a node.js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, vytvořte si bezplatný zkušební účet během několika minut prostřednictvím [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Tato příručka ukazuje, jak odesílat nabízená oznámení pomocí Azure Notification Hubs přímo z aplikace [Node.js.](https://nodejs.org)

Zahrnuté scénáře zahrnují odesílání nabízených oznámení aplikacím na následujících platformách:

- Android
- iOS
- Univerzální platforma Windows
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs poskytují snadno použitelnou, škálovatelnou infrastrukturu s více platformami pro odesílání nabízených oznámení do mobilních zařízení. Podrobnosti o infrastruktuře služeb najdete na stránce [Centra oznámení Azure.](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx)

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js

Prvním krokem v tomto kurzu je vytvoření nové prázdné aplikace Node.js. Pokyny k vytvoření aplikace Node.js najdete v [tématu Vytvoření a nasazení aplikace Node.js na web Azure][nodejswebsite], [Node.js Cloud Service][Node.js Cloud Service] pomocí prostředí Windows PowerShell nebo web s [WebMatrix][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurace aplikace pro použití center oznámení

Chcete-li používat Centra oznámení Azure, musíte stáhnout a použít [balíček](https://www.npmjs.com/package/azure)Azure Node.js , který obsahuje integrovanou sadu pomocných knihoven, které komunikují se službami REST nabízených oznámení.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků uzlů (NPM).

1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows), **Terminál** (Mac) nebo **Bash** (Linux), a přejděte do složky, do které jste vytvořili prázdnou aplikaci.
2. Spusťte `npm install azure-sb` v příkazovém okně.
3. Příkaz `ls` nebo `dir` můžete spustit ručně a `node_modules` ověřit, zda byla vytvořena složka.
4. Uvnitř této složky najděte balíček **azure,** který obsahuje knihovny, které potřebujete pro přístup k Centru oznámení.

> [!NOTE]
> Můžete se dozvědět více o instalaci NPM na oficiálním [blogu NPM](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Import modulu
Pomocí textového editoru přidejte do `server.js` horní části souboru aplikace následující:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Nastavení připojení centra oznámení Azure

Objekt `NotificationHubService` umožňuje pracovat s centry oznámení. Následující kód vytvoří `NotificationHubService` objekt pro centrum `hubname`oznámení s názvem . Přidejte jej v `server.js` horní části souboru, po příkazu k importu modulu azure:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Získejte `connectionstring` hodnotu připojení z [portálu Azure] provedením následujících kroků:

1. V levém navigačním podokně klepněte na **tlačítko Procházet**.
2. Vyberte **Centra oznámení**a najděte centrum, které chcete použít pro ukázku. Pokud potřebujete pomoct s vytvořením nového centra oznámení, můžete se podívat na kurz Začínáme pro [Windows Store.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
3. Vyberte **Nastavení**.
4. Klikněte na **zásady přístupu**. Zobrazí se sdílené i úplné přístupové připojovací řetězce.

![Portál Azure – centra oznámení](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Připojovací řetězec můžete taky načíst pomocí rutiny **Get-AzureSbNamespace** poskytované [Azure PowerShell](/powershell/azureps-cmdlets-docs) nebo azure **sb obor zobrazení funkce s** [rozhraním Azure Command-Line Interface (Azure CLI).](../cli-install-nodejs.md)

## <a name="general-architecture"></a>Obecná architektura

Objekt `NotificationHubService` zveřejňuje následující instance objektu pro odesílání nabízených oznámení do konkrétních zařízení a aplikací:

- **Android** - `GcmService` použijte objekt, který je k dispozici na`notificationHubService.gcm`
- **iOS** - `ApnsService` použijte objekt, který je přístupný na`notificationHubService.apns`
- **Windows Phone** - `MpnsService` použijte objekt, který je k dispozici na`notificationHubService.mpns`
- **Univerzální platforma** Windows `WnsService` - použijte objekt, který je k dispozici na`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Postup: Odesílání nabízených oznámení do aplikací pro Android

Objekt `GcmService` poskytuje `send` metodu, která lze použít k odesílání nabízených oznámení do aplikací pro Android. Metoda `send` přijímá následující parametry:

- **Značky** - identifikátor značky. Pokud není k dispozici žádná značka, oznámení je odesláno všem klientům.
- **Datová část** - json zprávy nebo nezpracovaná datová část řetězce.
- **Zpětné volání** - funkce zpětného volání.

Další informace o formátu datové části naleznete v [dokumentaci k datové části](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload).

Následující kód používá `GcmService` instanci `NotificationHubService` vystavenou odeslat nabízené oznámení všem registrovaným klientům.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Postup: Odesílání nabízených oznámení aplikacím iOS

Stejně jako u aplikací pro `ApnsService` Android `send` popsaných výše, objekt poskytuje metodu, která lze použít k odesílání nabízených oznámení aplikacím iOS. Metoda `send` přijímá následující parametry:

- **Značky** - identifikátor značky. Pokud není k dispozici žádná značka, oznámení je odesláno všem klientům.
- **Datová část** - čtení JSON zprávy nebo řetězcové datové části.
- **Zpětné volání** - funkce zpětného volání.

Další informace o formátu datové části naleznete v části **Datové části oznámení** v dokumentu Local and Push Notification Programming [Guide.](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)

Následující kód používá `ApnsService` instanci `NotificationHubService` vystavenou oznámením k odeslání výstražné zprávy všem klientům:

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Postup: Odesílání nabízených oznámení do aplikací pro Windows Phone

Objekt `MpnsService` poskytuje `send` metodu, kterou lze použít k odesílání nabízených oznámení aplikacím pro Windows Phone. Metoda `send` přijímá následující parametry:

- **Značky** - identifikátor značky. Pokud není k dispozici žádná značka, oznámení je odesláno všem klientům.
- **Datová část** - datová část XML zprávy.
- **TargetName**  -  `toast` pro informační zprávy. `token`pro oznámení dlaždic.
- **NotificationClass** - priorita oznámení. Platné hodnoty naleznete v části **Prvky záhlaví PROTOKOLU HTTP** v [nabízeném oznámení ze serverového](https://msdn.microsoft.com/library/hh221551.aspx) dokumentu.
- **Možnosti** - volitelné hlavičky požadavku.
- **Zpětné volání** - funkce zpětného volání.

Seznam platných `TargetName` `NotificationClass` možností a možností záhlaví najdete v části Nabízená oznámení ze stránky [serveru.](https://msdn.microsoft.com/library/hh221551.aspx)

Následující ukázkový kód `MpnsService` používá instanci vystavenou `NotificationHubService` oznámení informační zprávy:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Postup: Odesílání nabízených oznámení do aplikací platformy USB (Universal Platform)

Objekt `WnsService` poskytuje `send` metodu, která lze použít k odesílání nabízených oznámení do aplikací univerzální platformy Windows.  Metoda `send` přijímá následující parametry:

- **Značky** - identifikátor značky. Pokud není k dispozici žádná značka, oznámení je odesláno všem registrovaným klientům.
- **Datová část** - datová část zprávy XML.
- **Typ** - typ oznámení.
- **Možnosti** - volitelné hlavičky požadavku.
- **Zpětné volání** - funkce zpětného volání.

Seznam platných typů a hlaviček požadavků naleznete v tématu [Push notification service request and response headers](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Následující kód používá `WnsService` instanci `NotificationHubService` vystavenou pomocí aplikace informační zprávy do aplikace UPW:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Další kroky

Ukázkové fragmenty výše umožňují snadno vytvářet infrastrukturu služeb pro doručování nabízených oznámení do široké škály zařízení. Teď, když jste se naučili základy používání center oznámení s node.js, postupujte podle těchto odkazů a přečtěte si další informace o tom, jak můžete tyto možnosti dále rozšířit.

- Podívejte se na odkaz MSDN pro [centra oznámení Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Další ukázky a podrobnosti implementace najdete v úložišti [Azure SDK for Node] na GitHubu.

[Azure SDK pro Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com

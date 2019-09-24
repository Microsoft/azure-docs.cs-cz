---
title: Posílání nabízených oznámení pomocí Notification Hubs Azure a Node. js
description: Naučte se používat Notification Hubs k odesílání nabízených oznámení z aplikace Node. js.
keywords: nabízené oznámení, nabízená oznámení, nabízená oznámení Node. js, nabízená oznámení iOS
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
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213190"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Posílání nabízených oznámení pomocí Notification Hubs Azure a Node. js

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)vytvořit bezplatný zkušební účet během několika minut.

V této příručce se dozvíte, jak odesílat nabízená oznámení pomocí Azure Notification Hubs přímo z aplikace [Node. js](https://nodejs.org) .

Mezi zahrnuté scénáře patří odesílání nabízených oznámení do aplikací na následujících platformách:

- Android
- iOS
- Univerzální platforma Windows
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs poskytují snadno použitelná škálovatelnou infrastrukturu pro více platforem pro posílání nabízených oznámení na mobilní zařízení. Podrobnosti o infrastruktuře služby najdete na stránce [Azure Notification Hubs](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node. js

Prvním krokem v tomto kurzu je vytvoření nové prázdné aplikace Node. js. Pokyny k vytvoření aplikace Node. js najdete v tématu [Vytvoření a nasazení aplikace Node. js do webu Azure][nodejswebsite], [cloudové služby Node. js][Node.js Cloud Service] pomocí prostředí Windows PowerShell nebo [webu s webmaticí][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurace aplikace pro použití Notification Hubs

Chcete-li použít Azure Notification Hubs, je nutné stáhnout a použít [balíček Azure](https://www.npmjs.com/package/azure)Node. js, který obsahuje integrovanou sadu pomocných knihoven, které komunikují se službami REST nabízených oznámení.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>K získání balíčku použijte Správce balíčků Node (NPM).

1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows), **terminál** (Mac) nebo **bash** (Linux), a přejděte do složky, ve které jste vytvořili prázdnou aplikaci.
2. Spustí `npm install azure-sb` se v příkazovém okně.
3. Můžete ručně spustit `ls` příkaz nebo `dir` a ověřit tak, že se `node_modules` vytvořila složka.
4. V této složce najděte balíček **Azure** obsahující knihovny, které potřebujete pro přístup k centru oznámení.

> [!NOTE]
> Další informace o instalaci NPM najdete na oficiálním [blogu npm](https://blog.npmjs.org/post/85484771375/how-to-install-npm).

### <a name="import-the-module"></a>Importovat modul
Pomocí textového editoru přidejte na začátek `server.js` souboru aplikace následující text:

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Nastavení připojení centra oznámení Azure

`NotificationHubService` Objekt vám umožní pracovat s centry oznámení. Následující kód vytvoří `NotificationHubService` objekt pro Centrum oznámení s názvem `hubname`. Přidejte ho poblíž horní části `server.js` souboru, po příkazu pro import modulu Azure:

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

Pomocí následujících kroků `connectionstring` Získejte hodnotu připojení z [Azure Portal] :

1. V levém navigačním podokně klikněte na **Procházet**.
2. Vyberte **Notification Hubs**a pak najděte rozbočovač, který chcete použít pro ukázku. Pokud potřebujete pomáhat s vytvořením nového centra oznámení, přečtěte si [kurz Windows Store Začínáme](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
3. Vyberte **nastavení**.
4. Klikněte na **zásady přístupu**. Zobrazí se oba řetězce připojení Shared i Full Access.

![Azure Portal – Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Připojovací řetězec můžete také načíst pomocí rutiny **Get-AzureSbNamespace** , kterou poskytuje [Azure PowerShell](/powershell/azureps-cmdlets-docs) , nebo pomocí příkazu pro **zobrazení oboru názvů Azure SB** v [rozhraní příkazového řádku Azure (Azure CLI)](../cli-install-nodejs.md).

## <a name="general-architecture"></a>Obecná architektura

`NotificationHubService` Objekt zpřístupňuje následující instance objektů pro posílání nabízených oznámení na konkrétní zařízení a aplikace:

- **Android** – použijte `GcmService` objekt, který je k dispozici na`notificationHubService.gcm`
- **iOS** – použijte `ApnsService` objekt, který je dostupný na`notificationHubService.apns`
- **Windows Phone** – použijte `MpnsService` objekt, který je k dispozici na`notificationHubService.mpns`
- **Univerzální platforma Windows** – použijte `WnsService` objekt, který je k dispozici na`notificationHubService.wns`

### <a name="how-to-send-push-notifications-to-android-applications"></a>Postup: Odesílání nabízených oznámení do aplikací pro Android

`GcmService` Objekt`send` poskytuje metodu, kterou lze použít k odesílání nabízených oznámení do aplikací pro Android. `send` Metoda přijímá následující parametry:

- **Tags** – identifikátor značky. Pokud není zadaná žádná značka, pošle se oznámení všem klientům.
- **Datová část** – datová část JSON nebo nezpracovaných řetězců zprávy.
- **Zpětné volání** – funkce zpětného volání.

Další informace o formátu datové části najdete v dokumentaci k [datové části](https://distriqt.github.io/ANE-PushNotifications/m.FCM-GCM%20Payload).

Následující kód používá `GcmService` instanci zveřejněnou `NotificationHubService` pro odeslání nabízeného oznámení všem registrovaným klientům.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Postup: Posílání nabízených oznámení do aplikací pro iOS

Stejně jako u aplikací pro Android popsaných `ApnsService` výše, objekt `send` poskytuje metodu, která se dá použít k odesílání nabízených oznámení do aplikací pro iOS. `send` Metoda přijímá následující parametry:

- **Tags** – identifikátor značky. Pokud není zadaná žádná značka, pošle se oznámení všem klientům.
- **Datová část** , JSON zprávy nebo datová část řetězce.
- **Zpětné volání** – funkce zpětného volání.

Další informace o formátu datové části najdete v části **datová část oznámení** v dokumentu [Průvodce programováním místních a nabízených oznámení](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) .

Následující kód používá `ApnsService` instanci zveřejněnou `NotificationHubService` pro odeslání zprávy upozornění všem klientům:

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

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Postup: Posílání nabízených oznámení do aplikací Windows Phone

`MpnsService` Objekt`send` poskytuje metodu, kterou lze použít k odesílání nabízených oznámení do aplikací Windows Phone. `send` Metoda přijímá následující parametry:

- **Tags** – identifikátor značky. Pokud není zadaná žádná značka, pošle se oznámení všem klientům.
- **Datová** část XML zprávy.
- Cílový_název -  prooznámeníinformačních`toast` zpráv `token`pro oznámení na dlaždici.
- **NotificationClass** – priorita oznámení Platné hodnoty najdete v části **elementy hlavičky protokolu HTTP** v [nabízených oznámeních z dokumentu serveru](https://msdn.microsoft.com/library/hh221551.aspx) .
- **Možnosti** – nepovinné hlavičky požadavku.
- **Zpětné volání** – funkce zpětného volání.

Seznam platných `TargetName` `NotificationClass` a hlavičkových možností najdete na stránce s [nabízenými oznámeními ze serveru](https://msdn.microsoft.com/library/hh221551.aspx) .

Následující vzorový kód používá `MpnsService` instanci zveřejněnou `NotificationHubService` pro odeslání informačního oznámení nabízeného oznámení:

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Postup: Posílání nabízených oznámení do aplikací Univerzální platforma Windows (UWP)

`WnsService` Objekt`send` poskytuje metodu, kterou lze použít k odesílání nabízených oznámení do aplikací Univerzální platforma Windows.  `send` Metoda přijímá následující parametry:

- **Tags** – identifikátor značky. Pokud není zadaná žádná značka, pošle se oznámení všem registrovaným klientům.
- **Datová část** – datová část zprávy XML.
- **Typ** – typ oznámení.
- **Možnosti** – nepovinné hlavičky požadavku.
- **Zpětné volání** – funkce zpětného volání.

Seznam platných typů a hlaviček požadavků najdete v [záhlaví žádostí a odpovědí služby nabízených oznámení](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Následující kód používá `WnsService` instanci zveřejněnou `NotificationHubService` pro odeslání informačního oznámení do aplikace UWP:

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>Další kroky

Výše uvedené ukázkové fragmenty kódu vám umožní snadno sestavit infrastrukturu služby, která zajistí doručování nabízených oznámení do široké škály zařízení. Teď, když jste se seznámili se základy použití Notification Hubs s Node. js, najdete na následujících odkazech, kde najdete další informace o tom, jak tyto možnosti rozšířit.

- Viz Referenční příručka MSDN pro [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
- Další ukázky a podrobnosti o implementaci najdete v sadě [Azure SDK pro Node] na GitHubu.

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

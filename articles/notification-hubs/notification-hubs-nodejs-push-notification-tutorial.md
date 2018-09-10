---
title: Odesílání nabízených oznámení pomocí Azure Notification Hubs a Node.js
description: Zjistěte, jak používat Notification Hubs k odesílání nabízených oznámení z aplikace Node.js.
keywords: nabízené oznámení, nabízené notifications,node.js nabízených oznámení, nabízené ios
services: notification-hubs
documentationcenter: nodejs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 49ee6b7fabe78c2328a2a772347c612ad38cfe7a
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091716"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Odesílání nabízených oznámení pomocí Azure Notification Hubs a Node.js
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Přehled
> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).
> 
> 

Tento průvodce vám ukáže, jak odesílat nabízená oznámení pomocí služby Azure Notification Hubs přímo z aplikace Node.js. 

Mezi popsané scénáře patří odesílání nabízených oznámení do aplikace na následujících platformách:

* Android
* iOS
* telefon se systémem Windows
* Univerzální platforma Windows 

Další informace o notification hubs najdete v článku [další kroky](#next) oddílu.

## <a name="what-are-notification-hubs"></a>Co jsou Notification Hubs?
Azure Notification Hubs poskytuje snadno použitelné, multiplatformní a škálovatelnou infrastrukturu pro posílání nabízených oznámení do mobilních zařízení. Podrobnosti o infrastruktuře služby, najdete v článku [Azure Notification Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) stránky.

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace v Node.js
Prvním krokem v tomto kurzu je vytvoření nové prázdné aplikace Node.js. Pokyny týkající se vytvoření aplikace Node.js najdete v tématu [vytvoření a nasazení aplikace Node.js na Azure web][nodejswebsite], [cloudové služby pro Node.js] [ Node.js Cloud Service] pomocí Windows Powershellu nebo [webu pomocí Webmatrixu][webmatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurace aplikace pro použití Notification Hubs
Pokud chcete používat Azure Notification Hubs, budete muset stáhnout a použít na Node.js [balíček azure](https://www.npmjs.com/package/azure), což zahrnuje integrované sada knihoven pomocné rutiny, které komunikují s REST služby nabízených oznámení.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Získat balíček pomocí Node Package Manager (NPM)
1. Pomocí rozhraní příkazového řádku, jako **PowerShell** (Windows), **terminálu** (Mac), nebo **Bash** (Linux) a přejděte do složky, ve které jste vytvořili aplikaci prázdné.
2. Typ **npm nainstalujte azure-sb** v příkazovém okně.
3. Můžete ručně spustit **ls** nebo **dir** příkazu ověřte, že **uzel\_moduly** složka byla vytvořena. V takové složce najít **azure** balíček, který obsahuje knihovny, budete potřebovat přístup k centru oznámení.

> [!NOTE]
> Další informace o instalaci NPM official je přínosné pro [NPM blogu](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 
> 
> 

### <a name="import-the-module"></a>Import modulu
Pomocí textového editoru, přidejte následující k hornímu okraji **server.js** souboru aplikace:

    var azure = require('azure');

### <a name="set-up-an-azure-notification-hub-connection"></a>Nastavit připojení k centru oznámení Azure
**NotificationHubService** objekt vám umožní pracovat s notification hubs. Následující kód vytvoří **NotificationHubService** objekt pro centra oznámení s názvem **hubname**. Přidejte do horní části **server.js** soubor po příkazu k importu modulu azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Připojení **connectionstring** hodnotu můžete získat [Azure Portal] provedením následujících kroků:

1. V levém navigačním podokně klikněte na tlačítko **Procházet**.
2. Vyberte **Notification Hubs**a pak vyhledejte centrum, které chcete použít pro ukázku. Můžete se podívat do [Windows Store Začínáme kurzu](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) Pokud potřebujete pomoc při vytvoření nového centra oznámení.
3. Vyberte **nastavení**.
4. Klikněte na **zásady přístupu**. Uvidíte oba připojovací řetězce sdílené a úplný přístup.

![Azure portal – Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Můžete také načíst připojovací řetězec pomocí **Get-AzureSbNamespace** rutiny poskytované [prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) nebo **zobrazit obor názvů azure sb** příkazů [Rozhraní příkazového řádku azure (Azure CLI)](../cli-install-nodejs.md).
> 
> 

## <a name="general-architecture"></a>Obecná architektura
**NotificationHubService** objekt poskytuje následující instance objektu pro odesílání nabízených oznámení na konkrétní zařízení a aplikací:

* **Android** -použít **GcmService** objektu, který je k dispozici na **notificationHubService.gcm**
* **iOS** -použít **ApnsService** objekt, který je přístupný na adrese **notificationHubService.apns**
* **Windows Phone** -použít **MpnsService** objektu, který je k dispozici na **notificationHubService.mpns**
* **Univerzální platforma Windows** -použít **WnsService** objektu, který je k dispozici na **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Postupy: odesílání nabízených oznámení do aplikací pro Android
**GcmService** objekt, který poskytuje **odeslat** metodu, která slouží k odesílání nabízených oznámení do aplikací pro Android. **Odeslat** metoda přijímá následující parametry:

* **Značky** -identifikátor značky. Pokud se neposkytne žádná značka oznámení odesláno všem klientům.
* **Datová část** -JSON nebo nezpracovaného řetězce datovou část zprávy.
* **Zpětné volání** – funkce zpětného volání.

Další informace o formátu datové části, najdete v článku **datové části** část [implementace serveru GCM](http://developer.android.com/google/gcm/server.html#payload) dokumentu.

Následující kód používá **GcmService** instance vystavené **NotificationHubService** k odesílání nabízených oznámení na všechny registrované klienty.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Postupy: odesílání nabízených oznámení do aplikací pro iOS
Shodná s aplikací pro Android je popsáno výše, **ApnsService** objekt, který poskytuje **odeslat** metodu, která slouží k odesílání nabízených oznámení do aplikací pro iOS. **Odeslat** metoda přijímá následující parametry:

* **Značky** -identifikátor značky. Pokud se neposkytne žádná značka oznámení odesláno všem klientům.
* **Datová část** -datovou část JSON nebo řetězec zprávy.
* **Zpětné volání** – funkce zpětného volání.

Formát datové části Další informace najdete v tématu **datová část oznámení** část [průvodci místních a nabízených oznámení programování](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentu.

Následující kód používá **ApnsService** instance vystavené **NotificationHubService** odeslat zprávu oznámení na všechny klienty:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Postupy: odesílání nabízených oznámení do aplikace Windows Phone
**MpnsService** objekt, který poskytuje **odeslat** metodu, která slouží k odesílání nabízených oznámení do aplikace Windows Phone. **Odeslat** metoda přijímá následující parametry:

* **Značky** -identifikátor značky. Pokud se neposkytne žádná značka oznámení odesláno všem klientům.
* **Datová část** -datovou část zprávy XML.
* **TargetName**  -  `toast` pro informační zprávy. `token` dlaždice oznámení.
* **NotificationClass** -prioritu oznámení. Najdete v článku **prvky hlavičky protokolu HTTP** část [nabízená oznámení ze serveru](http://msdn.microsoft.com/library/hh221551.aspx) dokumentu platné hodnoty.
* **Možnosti** – volitelné hlavičky požadavku.
* **Zpětné volání** – funkce zpětného volání.

Seznam platných **TargetName**, **NotificationClass** a možnosti záhlaví, podívejte se [nabízená oznámení ze serveru](http://msdn.microsoft.com/library/hh221551.aspx) stránky.

Následující ukázkový kód používá **MpnsService** instance vystavené **NotificationHubService** k odesílání nabízených oznámení:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Postupy: odesílání nabízených oznámení do aplikace univerzální platformy Windows (UPW)
**WnsService** objekt, který poskytuje **odeslat** metodu, která slouží k odesílání nabízených oznámení do aplikace univerzální platformy Windows.  **Odeslat** metoda přijímá následující parametry:

* **Značky** -identifikátor značky. Pokud se neposkytne žádná značka oznámení odesláno všem klientům registrovaný.
* **Datová část** -datovou část zprávy XML.
* **Typ** – typ oznámení.
* **Možnosti** – volitelné hlavičky požadavku.
* **Zpětné volání** – funkce zpětného volání.

Seznam platné typy a hlavičky požadavku najdete v tématu [nabízená oznámení hlavičky požadavku a odpovědi služby](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Následující kód používá **WnsService** instance vystavené **NotificationHubService** k odesílání nabízených oznámení do aplikace pro UPW:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });

## <a name="next-steps"></a>Další kroky
Výše uvedené ukázkové fragmenty umožňují snadno vytvářet služby infrastruktury pro doručování nabízených oznámení do širokou škálu zařízení. Teď, když jste se naučili základy používání Notification Hubs s využitím node.js, použijte tyto odkazy na další informace o tom, jak můžete rozšířit tyto další možnosti.

* Viz odkaz na webu MSDN pro [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
* Přejděte [Azure SDK pro Node] úložišti na Githubu pro další ukázky a podrobnosti implementace.

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
[SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com

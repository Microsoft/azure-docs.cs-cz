---
title: Odesílání nabízených oznámení pomocí Azure Notification Hubs a Node.js
description: Naučte se používat Notification Hubs k odesílání nabízených oznámení z aplikace Node.js.
keywords: nabízená oznámení, nabízené notifications,node.js nabízet, ios push
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
ms.openlocfilehash: 7463d41382c59e4f7f03b58dbcbc3f5c45e9d15c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778354"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Odesílání nabízených oznámení pomocí Azure Notification Hubs a Node.js
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Přehled
> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).
> 
> 

Tato příručka ukazuje, jak odesílat nabízená oznámení pomocí Azure Notification hubs přímo z aplikace Node.js. 

Pokryté scénáře zahrnují odesílání nabízených oznámení do aplikace na následujících platformách:

* Android
* iOS
* telefon se systémem Windows
* Univerzální platforma pro Windows 

Další informace o notification hubs najdete v tématu [další kroky](#next) části.

## <a name="what-are-notification-hubs"></a>Co jsou Notification Hubs?
Azure Notification Hubs poskytuje snadno použitelnou, více platformami, škálovatelné infrastruktury pro odesílání nabízených oznámení do mobilních zařízení. Podrobnosti o infrastrukturu služeb, najdete v článku [Azure Notification Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) stránky.

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Prvním krokem v tomto kurzu je vytvoření nové prázdné aplikace Node.js. Pokyny pro vytvoření aplikace Node.js, naleznete v části [vytvoření a nasazení aplikace Node.js na webu Azure][nodejswebsite], [Node.js Cloudová služba] [ Node.js Cloud Service] pomocí prostředí Windows PowerShell nebo [webu pomocí služby WebMatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Konfigurace aplikace pro použití služby Notification Hubs
Chcete-li používat Azure Notification Hubs, musíte stáhnout a použít Node.js [balíček azure](https://www.npmjs.com/package/azure), což zahrnuje integrovanou sadu knihoven pomocné rutiny, které komunikují s REST služeb nabízených oznámení.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Získat balíček pomocí uzlu balíček správce (NPM)
1. Pomocí rozhraní příkazového řádku, jako například **prostředí PowerShell** (Windows), **Terminálové** (Mac), nebo **Bash** (Linux) a přejděte do složky, které jste vytvořili aplikaci prázdné.
2. Typ **npm nainstalujte azure sb** v příkazovém okně.
3. Můžete ručně spustit **ls** nebo **dir** příkazu ověřte, že **uzlu\_moduly** složka byla vytvořena. V této složce najít **azure** balíček, který obsahuje knihovny, které potřebujete získat přístup k centru oznámení.

> [!NOTE]
> Další informace o instalaci NPM na oficiální [NPM blog](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 
> 
> 

### <a name="import-the-module"></a>Naimportujte modul
Pomocí textového editoru, přidejte následující do horní části **server.js** souboru aplikace:

    var azure = require('azure');

### <a name="set-up-an-azure-notification-hub-connection"></a>Nastavit připojení k Azure Notification Hubs
**NotificationHubService** objekt vám umožňuje spolupracovat s centry oznámení. Následující kód vytvoří **NotificationHubService** objekt pro centra oznámení s názvem **hubname**. Přidejte do horní části **server.js** souboru po příkazu k importu modulu azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Připojení **connectionstring** nelze získat hodnotu z [portál Azure] provedením následujících kroků:

1. V levém navigačním podokně klikněte na tlačítko **Procházet**.
2. Vyberte **Notification Hubs**a pak vyhledejte rozbočovače, které chcete použít pro vzorovou. Můžete se podívat do [Windows Store Začínáme kurzu](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) Pokud potřebujete pomoc, vytváření nového centra oznámení.
3. Vyberte **nastavení**.
4. Klikněte na **zásady přístupu**. Zobrazí oba připojovací řetězce sdílené a úplný přístup.

![Portál Azure – centra oznámení](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> Můžete také načíst připojovací řetězec pomocí **Get-AzureSbNamespace** rutiny poskytované [prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs) nebo **azure sb obor názvů zobrazit** s [Rozhraní příkazového řádku azure (Azure CLI)](../cli-install-nodejs.md).
> 
> 

## <a name="general-architecture"></a>Obecná architektura
**NotificationHubService** objekt poskytuje následující instance objektů pro odesílání nabízených oznámení pro konkrétní zařízení a aplikace:

* **Android** -použít **GcmService** objekt, který je k dispozici na **notificationHubService.gcm**
* **iOS** -použít **ApnsService** objekt, který je přístupná na **notificationHubService.apns**
* **Windows Phone** -použít **MpnsService** objekt, který je k dispozici na **notificationHubService.mpns**
* **Univerzální platformu Windows** -použít **WnsService** objekt, který je k dispozici na **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Postupy: odesílání nabízených oznámení do aplikace pro Android
**GcmService** objekt poskytuje **odeslat** metoda, která slouží k odesílání nabízených oznámení do aplikace pro Android. **Odeslat** metoda přijímá následující parametry:

* **Značky** -identifikátor značky. Pokud je k dispozici žádná značka, oznámení je odesláno všem klientům.
* **Datová část** -JSON nebo nezpracovaný řetězec datovou část zprávy.
* **Zpětné volání** – funkce zpětného volání.

Další informace o formátu datové části najdete v tématu **datové části** části [implementace serveru GCM](http://developer.android.com/google/gcm/server.html#payload) dokumentu.

Následující kód používá **GcmService** instance vystavené **NotificationHubService** k odesílání nabízených oznámení na všechny klientské počítače registrované.

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

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Postupy: odesílání nabízených oznámení do aplikace pro iOS
Stejné jako s popsané výše, aplikace pro Android **ApnsService** objekt poskytuje **odeslat** metoda, která slouží k odesílání nabízených oznámení do aplikace pro iOS. **Odeslat** metoda přijímá následující parametry:

* **Značky** -identifikátor značky. Pokud je k dispozici žádná značka, oznámení je odesláno všem klientům.
* **Datová část** -datovou část JSON nebo řetězec zprávy.
* **Zpětné volání** – funkce zpětného volání.

Formát datové části Další informace najdete v tématu **datová část oznámení** části [průvodci místních a nabízených oznámení programování](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentu.

Následující kód používá **ApnsService** instance vystavené **NotificationHubService** k odeslání zprávy upozornění pro všechny klienty:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Postupy: odesílání nabízených oznámení do aplikací Windows Phone
**MpnsService** objekt poskytuje **odeslat** metoda, která slouží k odesílání nabízených oznámení do aplikace Windows Phone. **Odeslat** metoda přijímá následující parametry:

* **Značky** -identifikátor značky. Pokud je k dispozici žádná značka, oznámení je odesláno všem klientům.
* **Datová část** -datovou část XML zprávy.
* **TargetName**  -  `toast` pro informační zprávy. `token` pro dlaždici oznámení.
* **NotificationClass** -prioritu oznámení. Najdete v článku **prvky záhlaví HTTP** části [nabízená oznámení ze serveru](http://msdn.microsoft.com/library/hh221551.aspx) dokumentu platné hodnoty.
* **Možnosti** – volitelné hlavičky požadavku.
* **Zpětné volání** – funkce zpětného volání.

Seznam platný **TargetName**, **NotificationClass** a možnosti hlaviček, podívejte se [nabízená oznámení ze serveru](http://msdn.microsoft.com/library/hh221551.aspx) stránky.

Následující ukázkový kód používá **MpnsService** instance vystavené **NotificationHubService** k odesílání nabízených oznámení:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Postupy: odesílání nabízených oznámení do aplikací pro univerzální platformu Windows (UWP)
**WnsService** objekt poskytuje **odeslat** metoda, která slouží k odesílání nabízených oznámení do aplikace pro univerzální platformu Windows.  **Odeslat** metoda přijímá následující parametry:

* **Značky** -identifikátor značky. Pokud je k dispozici žádná značka, oznámení se odešle klientům všechny registrované.
* **Datová část** -datovou část zprávy XML.
* **Typ** – typ oznámení.
* **Možnosti** – volitelné hlavičky požadavku.
* **Zpětné volání** – funkce zpětného volání.

Seznam platné typy a hlavičky požadavku, naleznete v části [nabízená oznámení hlavičkách žádostí a odpovědí služby](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Následující kód používá **WnsService** instance vystavené **NotificationHubService** odesílat nabízená oznámení do aplikace UWP:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });

## <a name="next-steps"></a>Další kroky
Výše uvedené ukázkové fragmenty umožňují snadno vytvořit infrastrukturu služby Doručovat nabízená oznámení k široké škále zařízení. Teď, když jste se naučili základy používání centra oznámení s node.js, postupujte podle následujících odkazech na další informace o tom, jak můžete rozšířit tyto další možnosti.

* Najdete v článku na webu MSDN odkazy [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
* Přejděte [Azure SDK pro uzel] úložišti na Githubu Další ukázky a podrobnosti implementace.

[Azure SDK pro uzel]: https://github.com/WindowsAzure/azure-sdk-for-node
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
[webu pomocí služby WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com

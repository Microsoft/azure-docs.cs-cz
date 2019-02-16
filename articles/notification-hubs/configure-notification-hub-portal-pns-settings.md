---
title: Nakonfigurovat nastavení systému oznámení platformy centra oznámení Azure | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat na webu Azure Portal s nastavením (PNS) systému oznámení platformy centra oznámení.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 7f7e4a4d75a8e118da6f026817bc4ecfcc7a60db
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314099"
---
# <a name="configure-an-azure-notification-hub-with-platform-notification-system-settings-in-the-azure-portal"></a>Konfigurace centra oznámení Azure pomocí nastavení systém oznámení platformy na webu Azure Portal 
Služba Azure Notification Hubs nabízí snadno použitelné, škálovatelné zasílací jádro, které umožňuje posílat oznámení libovolné platformě (iOS, Android, Windows, Kindle, Baidu atd.) z libovolného back-endu (cloudový nebo místní). Další informace týkající se služby najdete v tématu [co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

[Vytvoření centra oznámení Azure pomocí webu Azure portal](create-notification-hub-portal.md) Pokud jste tak již neučinili. V tomto rychlém startu se dozvíte, jak nakonfigurovat na webu Azure Portal s nastavením (PNS) systému oznámení platformy centra oznámení.

## <a name="apple-push-notification-service-apns"></a>Apple Push Notification Service (APNS)
1. Na **centra oznámení** stránky na webu Azure Portal, vyberte **Apple (APNS)** pod **nastavení** v nabídce vlevo.
2. Pokud vyberete **certifikát**, a proveďte následující akce:
    1. Vyberte **ikonu souboru**a vyberte **.p12** soubor k nahrání. 
    2. Zadejte **heslo**.
    3. Vyberte režim **Sandbox**. Používejte pouze režim **Výroba**, pokud chcete zasílat nabízená oznámení uživatelům, kteří si zakoupili aplikaci z obchodu s aplikacemi.

        ![Konfigurace certifikační služby APNS na webu Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)
3. Pokud vyberete **Token**a postupujte podle těchto kroků: 
    1. Zadejte hodnoty pro **klíče ID**, **ID sady**, **ID tenanta**, a **token**.
    2. Vyberte režim **Sandbox**. Používejte pouze režim **Výroba**, pokud chcete zasílat nabízená oznámení uživatelům, kteří si zakoupili aplikaci z obchodu s aplikacemi.

        ![Nakonfigurovat token služby APNS na webu Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Úplný kurz týkající se odesílání nabízených oznámení do zařízení s iOS pomocí Azure Notification Hubs a Apple Push Notification Service (APNS), najdete v části [v tomto kurzu](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Služby Google Firebase Cloud Messaging (FCM)
1. Na **centra oznámení** stránky na webu Azure Portal, vyberte **Google (GCM/FCM)** pod **nastavení** v nabídce vlevo. 
2. Vložit **klíč serveru** FCM projektu, který jste předtím uložili. 
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Azure Notification Hubs – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Zobrazí se zpráva ve výstrahách, notification hubs se úspěšně aktualizoval. **Uložit** je tlačítko neaktivní. 

Úplný kurz týkající se odesílání nabízených oznámení do zařízení s Androidem pomocí Azure Notification Hubs a služby Google Firebase Cloud Messaging, naleznete v tématu [v tomto kurzu](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service-wns"></a>Služba nabízených oznámení Windows (WNS)
1. Na **centra oznámení** stránky na webu Azure Portal, vyberte **Windows (WNS)** pod **nastavení** v nabídce vlevo.
2. Zadejte hodnoty pro **SID balíčku** a **klíč zabezpečení**.
3. Na panelu nástrojů vyberte **Uložit**.

    ![Pole SID balíčku a Klíč zabezpečení](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)


Úplný kurz týkající se odesílání nabízených oznámení do aplikace univerzální platformy Windows (UPW) běžící na zařízení s Windows, naleznete v tématu [v tomto kurzu](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="windows-phone---microsoft-push-notification-service"></a>Windows Phone – Microsoft služba nabízených oznámení
1. Na **centra oznámení** stránky na webu Azure Portal, vyberte **Windows Phone (MPNS)** pod **nastavení**.
2. Pokud chcete povolit neověřená nabízená oznámení, vyberte **povolit neověřená nabízená oznámení**a vyberte **Uložit** na panelu nástrojů.

    ![Azure Portal – Povolit neověřená nabízená oznámení](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)
3. Pokud chcete použít **ověření** push, postupujte podle těchto kroků:
    1. Vyberte **nahrát certifikát** na panelu nástrojů.
    2. Vyberte **ikonu souboru** a vyberte soubor certifikátu.
    3. Zadejte **heslo** pro certifikát. 
    4. Vyberte **OK** zavřete **nahrát certifikát** stránky. 
    5. Na **Windows Phone(MPNS)** stránce **Uložit** na panelu nástrojů.

Úplný kurz týkající se odesílání nabízených oznámení do aplikace pro Windows Phone 8 pomocí Microsoft Push Notification Service (MPNS), najdete v části [v tomto kurzu](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging-adm"></a>Amazon Device Messaging (ADM)
1. Na **centra oznámení** stránky na webu Azure Portal, vyberte **Amazon (ADM)** pod **nastavení** v nabídce vlevo.
2. Zadejte hodnoty pro **ID klienta** a **tajný kód klienta**.
3. Na panelu nástrojů vyberte **Uložit**.
    
    ![Azure Notification Hubs – ADM – nastavení](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Úplný kurz použití Azure Notification Hubs nabízených oznámení do aplikace Kindle, naleznete v tématu [v tomto kurzu](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)
1. Na **centra oznámení** stránky na webu Azure Portal, vyberte **Baidu (Android China)** pod **nastavení** v nabídce vlevo. 
2. Zadejte **klíč rozhraní API** , který získáte z konzoly Baidu, v projektu nabízených oznámení cloudu Baidu. 
3. Zadejte **tajný klíč** , který jste získali z konzoly Baidu, v projektu nabízených oznámení cloudu Baidu. 
4. Na panelu nástrojů vyberte **Uložit**. 

    ![Azure Notification Hubs – Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)
4. Zobrazí se zpráva ve výstrahách, notification hubs se úspěšně aktualizoval. **Uložit** je tlačítko neaktivní. 

Úplný kurz týkající se odesílání nabízených oznámení pomocí Azure Notification Hubs a nabízených oznámení cloudu Baidu, naleznete v tématu [v tomto kurzu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste zjistili, jak nakonfigurovat různých systémů oznámení platforem pro Centrum oznámení na webu Azure Portal. 

Podrobné pokyny pro odesílání nabízených oznámení na tyto různé platformy, najdete v kurzech v **kurzy** oddílu.

- [Nabízená oznámení do zařízení s iOS pomocí Azure Notification Hubs a Apple Push Notification Service (APNS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).
- [Nabízená oznámení do zařízení s Androidem pomocí Azure Notification Hubs a služby Google Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md).
- [Nabízená oznámení do aplikace univerzální platformy Windows (UPW), běžící na zařízeních Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
- [Nabízená oznámení do aplikace pro Windows Phone 8 pomocí Microsoft Push Notification Service (MPNS)](notification-hubs-windows-mobile-push-notifications-mpns.md).
- [Nabízená oznámení do aplikace Kindle](notification-hubs-kindle-amazon-adm-push-notification.md).
- [Nabízená oznámení pomocí Azure Notification Hubs a Baidu nabízených shluk](notification-hubs-baidu-china-android-notifications-get-started.md).

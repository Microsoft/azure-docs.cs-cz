---
title: Nastavení nabízených oznámení ve službě Azure Notification Hubs | Dokumentace Microsoftu
description: Zjistěte, jak nastavit službu Azure Notification Hubs na portálu Azure portal pomocí nastavení (PNS) systému oznámení platformy.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 04108c4b5a8ce04b971a0529393ea442cb1cf226
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480119"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Nastavení nabízených oznámení v centru oznámení na webu Azure Portal

Azure Notification Hubs poskytují, který se snadno používá a horizontálně navýší kapacitu, který modul nabízených oznámení. Pomocí Notification Hubs k odesílání oznámení na jakoukoliv platformu (iOS, Android, Windows, Kindle, Baidu) a z jakéhokoli back-endu (cloudu nebo místně). Další informace najdete v tématu [co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu použijete nastavení platformy oznámení systému (PNS) ve službě Notification Hubs nastavení nabízených oznámení na více platformách. Rychlý Start se dozvíte, kroky pro zajištění na webu Azure Portal.

Pokud jste ještě nevytvořili centra oznámení, vytvořte teď. Další informace najdete v tématu [vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Chcete-li nastavit nahoru Apple Push Notification Service (APNS):

1. Na webu Azure Portal na **centra oznámení** stránce **Apple (APNS)** v levé nabídce.

1. Pro **režim ověřování**, vyberte buď **certifikát** nebo **Token**.

   a. Pokud vyberete **certifikát**:
   * Vyberte ikonu souboru a pak vyberte *.p12* soubor, který chcete odeslat.
   * Zadejte heslo.
   * Vyberte režim **Sandbox**. Nebo k odesílání nabízených oznámení uživatelům, kteří si zakoupili aplikaci z obchodu, vyberte **produkční** režimu.

     ![Snímek obrazovky APNS certifikátu konfigurace na webu Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Pokud vyberete **Token**:

   * Zadejte hodnoty pro **ID klíče**, **ID sady prostředků**, **ID týmu**, a **Token**.
   * Vyberte režim **Sandbox**. Nebo k odesílání nabízených oznámení uživatelům, kteří si zakoupili aplikaci z obchodu, vyberte **produkční** režimu.

     ![Snímek obrazovky APNS token konfigurace na webu Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Další informace najdete v tématu [nabízená oznámení do iOS pomocí Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Google Firebase Cloud Messaging

Nastavení nabízených oznámení pro Google Firebase Cloud Messaging (FCM):

1. Na webu Azure Portal na **centra oznámení** stránce **Google (GCM/FCM)** v levé nabídce. 
2. Vložit **klíč rozhraní API** FCM projektu, který jste předtím uložili. 
3. Vyberte **Uložit**. 

   ![Snímek obrazovky, který ukazuje, jak nakonfigurovat Notification Hubs pro Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Po dokončení těchto kroků výstraha naznačuje, že Centrum oznámení se úspěšně aktualizoval. **Uložit** je tlačítko neaktivní. 

Další informace najdete v tématu [nabízená oznámení do zařízení s Androidem pomocí Notification Hubs a Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Služba nabízených oznámení Windows

Chcete-li nastavit nahoru Windows Push Notification Service (WNS):

1. Na webu Azure Portal na **centra oznámení** stránce **Windows (WNS)** v levé nabídce.
2. Zadejte hodnoty pro **SID balíčku** a **klíč zabezpečení**.
3. Vyberte **Uložit**.

   ![Snímek obrazovky zobrazující pole SID balíčku a klíč zabezpečení](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Informace najdete v tématu [odesílat oznámení do aplikací pro UWP pomocí Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft služba nabízených oznámení pro Windows Phone

Nastavení pro Windows Phone do Microsoft nabízené služby oznámení (MPNS): 

1. Na webu Azure Portal na **centra oznámení** stránce **Windows Phone (MPNS)** v levé nabídce.
1. Povolit neověřený nebo ověřený nabízených oznámení:

   a. Chcete-li povolit neověřená nabízená oznámení, vyberte **povolit neověřená nabízená oznámení** > **Uložit**.

      ![Snímek obrazovky, který ukazuje, jak povolit neověřená nabízená oznámení](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Pokud chcete povolit ověřené nabízených oznámení:
      * Na panelu nástrojů vyberte **nahrát certifikát**.
      * Vyberte ikonu souboru a potom vyberte soubor certifikátu.
      * Zadejte heslo pro certifikát.
      * Vyberte **OK**.
      * Na **Windows Phone (MPNS)** stránce **Uložit**.

Další informace najdete v tématu [nabízená oznámení do aplikací Windows Phone pomocí Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging"></a>Amazon Device Messaging

Nastavení nabízených oznámení pro zasílání zpráv zařízení Amazon (ADM):

1. Na webu Azure Portal na **centra oznámení** stránce **Amazon (ADM)** v levé nabídce.
2. Zadejte hodnoty pro **ID klienta** a **tajný kód klienta**.
3. Vyberte **Uložit**.
    
   ![ADM – snímek obrazovky nastavení na portálu Azure portal](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Další informace najdete v tématu [Začínáme s Notification Hubs pro aplikace Kindle](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Nastavení nabízených oznámení Baidu:

1. Na webu Azure Portal na **centra oznámení** stránce **Baidu (Android China)** v levé nabídce. 
2. Zadejte **klíč rozhraní Api** , který jste získali z konzoly Baidu v projektu nabízených oznámení cloudu Baidu. 
3. Zadejte **tajný klíč** , který jste získali z konzoly Baidu v projektu nabízených oznámení cloudu Baidu. 
4. Vyberte **Uložit**. 

    ![Snímek obrazovky z centra oznámení, který znázorňuje Baidu (Android China) konfiguraci pro nabízená oznámení](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Po dokončení těchto kroků výstraha naznačuje, že Centrum oznámení se úspěšně aktualizoval. **Uložit** je tlačítko neaktivní. 

Další informace najdete v tématu [Začínáme s Notification Hubs pomocí Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste zjistili, jak nakonfigurovat nastavení systému oznámení platformy centra oznámení na webu Azure Portal. 

Další informace o tom, jak nabízená oznámení pro různé platformy, najdete v následujících kurzech:

- [Nabízená oznámení do zařízení s Iosem pomocí Notification Hubs a služby APN](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Odesílání nabízených oznámení do zařízení s Androidem pomocí Notification Hubs a Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Odesílání nabízených oznámení do aplikace pro UPW spuštěných v zařízení s Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Nabízená oznámení do aplikace pro Windows Phone 8 pomocí MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Odesílání nabízených oznámení do aplikace Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)
- [Nabízená oznámení pomocí Notification Hubs a Baidu push cloudu](notification-hubs-baidu-china-android-notifications-get-started.md)

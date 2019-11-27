---
title: Nastavení nabízených oznámení v Azure Notification Hubs | Microsoft Docs
description: Přečtěte si, jak nastavit Azure Notification Hubs v Azure Portal pomocí nastavení systému PNS (Platform Notification System).
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 951f03f581906e45946ef75742421ba27d405267
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406984"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Nastavení nabízených oznámení v centru oznámení v Azure Portal

Azure Notification Hubs poskytuje modul nabízených oznámení, který se snadno používá a který se škáluje. Pomocí Notification Hubs můžete odesílat oznámení na libovolnou platformu (iOS, Android, Windows, Baidu) a z libovolného back-endu (v cloudu nebo místně). Další informace najdete v tématu [co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu použijete nastavení PNS (Platform Notification System) v Notification Hubs k nastavení nabízených oznámení na více platformách. V tomto rychlém startu se dozvíte o krocích, které je potřeba provést v Azure Portal.

Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho hned teď. Další informace najdete v tématu [vytvoření centra oznámení Azure v Azure Portal](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Nastavení služby Apple Push Notification Service (APNS):

1. V Azure Portal na stránce **centra oznámení** vyberte **Apple (APNs)** z nabídky vlevo.

1. V **režimu ověřování**vyberte buď **certifikát** , nebo **token**.

   a. Pokud vyberete možnost **certifikát**:
   * Vyberte ikonu souboru a potom vyberte soubor *. p12* , který chcete nahrát.
   * Zadejte heslo.
   * Vyberte režim **Sandbox**. Nebo pokud chcete odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu, vyberte režim **výroby** .

     ![Snímek obrazovky s konfigurací certifikátu APNS v Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Pokud vyberete **token**:

   * Zadejte hodnoty **ID klíče**, **ID sady**, **ID týmu**a **tokenu**.
   * Vyberte režim **Sandbox**. Nebo pokud chcete odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu, vyberte režim **výroby** .

     ![Snímek obrazovky s konfigurací tokenu APNS v Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Další informace najdete v tématu [nabízená oznámení do systému iOS pomocí služby Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Cloudové zasílání zpráv Google Firebase

Nastavení nabízených oznámení pro zasílání zpráv Google Firebase do cloudu (FCM):

1. V Azure Portal na stránce **centra oznámení** v nabídce vlevo vyberte **Google (GCM/FCM)** . 
2. Vložte **klíč rozhraní API** pro projekt FCM, který jste předtím uložili. 
3. Vyberte **Uložit**. 

   ![Snímek obrazovky, který ukazuje, jak nakonfigurovat Notification Hubs pro Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Po dokončení těchto kroků výstraha indikuje, že centrum oznámení bylo úspěšně aktualizováno. Tlačítko **Uložit** je zakázané. 

Další informace najdete v tématu [nabízená oznámení na zařízení s Androidem pomocí Notification Hubs a Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Služba nabízených oznámení Windows

Nastavení služby nabízených oznámení Windows (WNS):

1. V Azure Portal na stránce **centra oznámení** vyberte v nabídce vlevo možnost **Windows (WNS)** .
2. Zadejte hodnoty pro **SID balíčku** a **klíč zabezpečení**.
3. Vyberte **Uložit**.

   ![Snímek obrazovky zobrazující pole SID balíčku a klíč zabezpečení](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Informace najdete v tématu [posílání oznámení do aplikací pro UWP pomocí Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Služba nabízených oznámení společnosti Microsoft pro Windows Phone

Nastavení služby Microsoft Push Notification Service (MPNS) pro Windows Phone: 

1. V Azure Portal na stránce **centra oznámení** vyberte v nabídce vlevo možnost **Windows Phone (MPNS)** .
1. Povolit buď neověřené, nebo ověřené nabízené oznámení:

   a. Pokud chcete povolit neověřená nabízená oznámení, vyberte **Povolit neověřené nabízené** oznámení > **Uložit**.

      ![Snímek obrazovky, který ukazuje, jak povolit neověřená nabízená oznámení](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Povolení ověřených nabízených oznámení:
      * Na panelu nástrojů vyberte **Odeslat certifikát**.
      * Vyberte ikonu souboru a potom vyberte soubor certifikátu.
      * Zadejte heslo pro certifikát.
      * Vyberte **OK**.
      * Na stránce **Windows Phone (MPNS)** vyberte **Uložit**.

Další informace najdete v tématu [nabízená oznámení pro Windows Phone aplikace pomocí Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
      

## <a name="baidu-android-china"></a>Baidu (Android China)

Nastavení nabízených oznámení pro Baidu:

1. V Azure Portal na stránce **centra oznámení** vyberte v nabídce vlevo možnost **Baidu (Android China)** . 
2. Zadejte **klíč rozhraní API** , který jste získali z konzoly Baidu v projektu nabízená oznámení cloudu Baidu. 
3. Zadejte **tajný klíč** , který jste získali z konzoly Baidu v projektu nabízená oznámení cloudu Baidu. 
4. Vyberte **Uložit**. 

    ![Snímek obrazovky Notification Hubs zobrazující konfiguraci Baidu (Android China) pro nabízená oznámení](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Po dokončení těchto kroků výstraha indikuje, že centrum oznámení bylo úspěšně aktualizováno. Tlačítko **Uložit** je zakázané. 

Další informace najdete v tématu [Začínáme s Notification Hubs pomocí Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste zjistili, jak nakonfigurovat nastavení systému oznámení platformy pro Centrum oznámení v Azure Portal. 

Další informace o nabízených oznámeních na různých platformách najdete v těchto kurzech:

- [Nabízená oznámení na zařízení s iOS pomocí Notification Hubs a APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Nabízená oznámení na zařízení s Androidem pomocí Notification Hubs a Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Nabízená oznámení do aplikace UWP běžící na zařízení s Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Nabízená oznámení do aplikace pro Windows Phone 8 pomocí MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Nabízená oznámení pomocí Notification Hubs a nabízená oznámení cloudu Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)

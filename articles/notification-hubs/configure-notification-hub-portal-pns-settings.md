---
title: Nastavení nabízených oznámení v centrech oznámení Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak nastavit Azure Notification Hubs na webu Azure Portal pomocí nastavení systému oznámení platformy (PNS).
services: notification-hubs
author: sethmanheim
manager: femila
editor: dbradish-microsoft
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 78afb124ee1d1ab9b212197fb7a7140f88de9940
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349519"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Úvodní příručka: Nastavení nabízených oznámení v centru oznámení

Azure Notification Hubs poskytuje snadno použitelný modul pro nabízený tisk, který se škáluje. Pomocí center oznámení můžete odesílat oznámení na libovolnou platformu (iOS, Android, Windows, Baidu) a z libovolného back-endu (cloud nebo místní). Další informace najdete v tématu [Co je Centra oznámení Azure?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu použijete nastavení systému oznámení platformy (PNS) v centru oznámení k nastavení nabízených oznámení na více platformách. Rychlý start ukazuje kroky, které je třeba provést na webu Azure Portal.  [Google Firebase Cloud Messaging](/azure/notification-hubs/configure-notification-hub-portal-pns-settings?tabs=azure-cli#google-firebase-cloud-messaging-fcm) obsahuje pokyny pro použití veloháčového uživatelského příkazu Azure.

Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho nyní. Další informace najdete [v tématu Vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md) nebo Vytvoření centra oznámení Azure pomocí azure [cli](create-notification-hub-azure-cli.md)

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Nastavení služby nabízených oznámení Apple (APNS):

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte **apple (APNS)** z levé nabídky.

1. V **režimu ověřování**vyberte **certifikát** nebo **token**.

   a. Pokud vyberete **certifikát**:
   * Vyberte ikonu souboru a pak vyberte soubor *P12,* který chcete nahrát.
   * Zadejte heslo.
   * Vyberte režim **Izolovaný prostor**. Chcete-li odeslat nabízená oznámení uživatelům, kteří si aplikaci zakoupili v obchodě, vyberte **režim produkčního** prostředí.

     ![Snímek obrazovky s konfigurací certifikátu APNS na webu Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Pokud zvolíte **Token**:

   * Zadejte hodnoty pro **ID klíče**, **ID balíčku**, **ID týmu**a **token**.
   * Vyberte režim **Izolovaný prostor**. Chcete-li odeslat nabízená oznámení uživatelům, kteří si aplikaci zakoupili v obchodě, vyberte **režim produkčního** prostředí.

     ![Snímek obrazovky s konfigurací tokenu APNS na webu Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Další informace najdete v [tématu Nabízená oznámení do iOS pomocí Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Zasílání zpráv (FCM)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Nastavení nabízených oznámení pro Google FCM:

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte **Google (GCM/FCM)** v levé nabídce.
2. Vložte **klíč rozhraní API** pro projekt Google FCM, který jste uložili dříve.
3. Vyberte **Uložit**.

   ![Snímek obrazovky, který ukazuje, jak nakonfigurovat centra oznámení pro Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Po dokončení těchto kroků výstraha označuje, že centrum oznámení byla úspěšně aktualizována. Tlačítko **Uložit** je zakázáno.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites"></a>Požadavky

Než začnete, musíte mít následující:

* [Azure CLI](/cli/azure/install-azure-cli) verze 2.0.67 nebo novější.

* Rozšíření Azure CLI [pro centra oznámení](/cli/azure/ext/notification-hub/notification-hub).
* **Klíč ROZHRANÍ API** pro projekt zasílání zpráv cloudových zpráv Google Firebase (FCM).

### <a name="set-up-push-notifications-for-google-fcm"></a>Nastavení nabízených oznámení pro Google FCM

1. Pomocí příkazu [aktualizace gcm pověření centra oznámení az](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) přidejte klíč rozhraní GOOGLE API do centra oznámení.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Aplikace pro Android potřebuje připojovací řetězec pro připojení k centru oznámení.  Pomocí příkazu [az notification hub authorization-rule](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) list můžete uvést dostupné zásady přístupu.  Pomocí příkazu [az notification hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) získáte připojovací řetězce zásad přístupu.  Zadejte **primaryConnectionString** nebo **secondaryConnectionString** v parametru, `--query` chcete-li získat primární připojovací řetězec přímo.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Pomocí příkazu [az notification hub test-send](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) otestujte odesílání zpráv do aplikace pro Android.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Získejte odkazy na Azure CLI pro jiné platformy pomocí příkazu [přihlašovacích údajů centra oznámení az.](/cli/azure/ext/notification-hub/notification-hub/credential)

---

Další informace o nabízení oznámení do aplikace pro Android najdete v tématu [Odesílání nabízených oznámení do zařízení Android pomocí Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Služba nabízených oznámení systému Windows

Nastavení služby nabízených oznámení systému Windows (WNS):

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte **windows (WNS)** z levé nabídky.
2. Zadejte hodnoty pro **balíček SID** a **bezpečnostní klíč**.
3. Vyberte **Uložit**.

   ![Snímek obrazovky s okny Sid balíčku a bezpečnostního klíče](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Další informace najdete v tématu [Odesílání oznámení do aplikací UPW pomocí Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Služba nabízených oznámení Microsoft pro Windows Phone

Nastavení služby Microsoft Push Notification Service (MPNS) pro Windows Phone:

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte windows **phone (MPNS)** z levé nabídky.
1. Povolte neověřená nebo ověřená nabízená oznámení:

   a. Chcete-li povolit neověřená nabízená oznámení, vyberte **možnost Povolit neověřené nabízené** > **uložení**.

      ![Snímek obrazovky, který ukazuje, jak povolit neověřená nabízená oznámení](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Povolení ověřených nabízených oznámení:
      * Na panelu nástrojů vyberte **Nahrát certifikát**.
      * Vyberte ikonu souboru a pak vyberte soubor certifikátu.
      * Zadejte heslo pro certifikát.
      * Vyberte **OK**.
      * Na stránce **Windows Phone (MPNS)** vyberte **Uložit**.

Další informace najdete v tématu [Nabízená oznámení aplikacím pro Windows Phone pomocí center oznámení](notification-hubs-windows-mobile-push-notifications-mpns.md).

## <a name="baidu-android-china"></a>Baidu (Android Čína)

Nastavení nabízených oznámení pro Baidu:

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte **baidu (Android China)** v levé nabídce.
2. Zadejte **klíč api,** který jste získali z konzoly Baidu v projektu push cloud Baidu.
3. Zadejte **tajný klíč,** který jste získali z konzoly Baidu v projektu push cloud Baidu.
4. Vyberte **Uložit**.

    ![Snímek obrazovky s centry oznámení, který zobrazuje konfiguraci Baidu (Android China) pro nabízená oznámení](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Po dokončení těchto kroků výstraha označuje, že centrum oznámení byla úspěšně aktualizována. Tlačítko **Uložit** je zakázáno.

Další informace najdete [v tématu Začínáme s centry oznámení pomocí Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak nakonfigurovat nastavení systému oznámení platformy pro centrum oznámení na webu Azure Portal.

Další informace o nabízení oznámení na různých platformách najdete v těchto kurzech:

-[Nabízená oznámení pro zařízení se systémem iOS pomocí centrů oznámení a](notification-hubs-ios-apple-push-notification-apns-get-started.md)
-nabízených oznámení APNS do zařízení se systémem Android pomocí center oznámení a nabízených oznámení[Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
-[do aplikace UPW spuštěná na zařízení S Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
-Nabízená oznámení do aplikace pro Windows Phone 8 pomocí nabízených oznámení[MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
-[pomocí center oznámení a push cloud push Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)

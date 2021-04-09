---
title: Nastavení nabízených oznámení v Azure Notification Hubs | Microsoft Docs
description: Přečtěte si, jak nastavit Azure Notification Hubs v Azure Portal pomocí nastavení systému PNS (Platform Notification System).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5dd1044895ba55d1fbc6be7f4f4a2d7f615daa16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94887259"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Rychlý Start: nastavení nabízených oznámení v centru oznámení

Azure Notification Hubs poskytuje modul nabízených oznámení, který se snadno používá a který se škáluje. Pomocí Notification Hubs můžete odesílat oznámení na libovolnou platformu (iOS, Android, Windows, Baidu) a z libovolného back-endu (v cloudu nebo místně). Další informace najdete v tématu [co je Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

V tomto rychlém startu použijete nastavení PNS (Platform Notification System) v Notification Hubs k nastavení nabízených oznámení na více platformách. V tomto rychlém startu se dozvíte o krocích, které je potřeba provést v Azure Portal. [Zasílání zpráv v cloudu Google Firebase](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) zahrnuje pokyny k používání rozhraní příkazového řádku Azure CLI.

Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho hned teď. Další informace najdete v tématu [vytvoření centra oznámení Azure v Azure Portal](create-notification-hub-portal.md) nebo [vytvoření centra oznámení Azure pomocí Azure CLI](create-notification-hub-azure-cli.md).

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Nastavení služby Apple Push Notification Service (APNS):

1. V Azure Portal na stránce **centra oznámení** vyberte **Apple (APNs)** z nabídky vlevo.

1. V **režimu ověřování** vyberte buď **certifikát** , nebo **token**.

   a. Pokud vyberete možnost **certifikát**:
   * Vyberte ikonu souboru a potom vyberte soubor *. p12* , který chcete nahrát.
   * Zadejte heslo.
   * Vyberte režim **Izolovaný prostor**. Nebo pokud chcete odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu, vyberte režim **výroby** .

     ![Snímek obrazovky s konfigurací certifikátu APNS v Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Pokud vyberete **token**:

   * Zadejte hodnoty **ID klíče**, **ID sady**, **ID týmu** a **tokenu**.
   * Vyberte režim **Izolovaný prostor**. Nebo pokud chcete odesílat nabízená oznámení uživatelům, kteří si zakoupili vaši aplikaci ze Storu, vyberte režim **výroby** .

     ![Snímek obrazovky s konfigurací tokenu APNS v Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Další informace najdete v tématu [Posílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs](ios-sdk-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>FCM (Google Firebase Cloud Messaging)

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Nastavení nabízených oznámení pro Google FCM:

1. V Azure Portal na stránce **centra oznámení** v nabídce vlevo vyberte **Google (GCM/FCM)** .
2. Vložte **klíč rozhraní API** pro projekt Google FCM, který jste předtím uložili.
3. Vyberte **Uložit**.

   ![Snímek obrazovky, který ukazuje, jak nakonfigurovat Notification Hubs pro Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Po dokončení těchto kroků výstraha indikuje, že centrum oznámení bylo úspěšně aktualizováno. Tlačítko **Uložit** je zakázané.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pro projekt Google Firebase Cloud Messaging (FCM) budete potřebovat **klíč rozhraní API** .

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.67 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

### <a name="set-up-push-notifications-for-google-fcm"></a>Nastavení nabízených oznámení pro Google FCM

1. Pomocí příkazu [AZ Notification-hub Credential GCM Update](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) přidejte svůj klíč Google API do centra oznámení.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Aplikace pro Android potřebuje připojovací řetězec pro připojení k centru oznámení.  Pomocí příkazu [AZ Notification-hub Authorization-Rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) zobrazíte seznam dostupných zásad přístupu.  K získání připojovacích řetězců zásad přístupu použijte příkaz [AZ Notification-Center Authorization-Rule list-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) .  Chcete-  li  `--query` získat primární připojovací řetězec přímo, zadejte v parametru parametr primaryConnectionString nebo secondaryConnectionString.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Pomocí příkazu [AZ Notification-hub test-Send](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) otestujte odesílání zpráv do aplikace pro Android.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Reference k rozhraní příkazového řádku Azure CLI pro jiné platformy získáte pomocí příkazu [AZ Notification-hub Credential](/cli/azure/ext/notification-hub/notification-hub/credential) .

Další informace o odesílání oznámení do aplikace pro Android najdete v tématu [Posílání nabízených oznámení na zařízení s Androidem pomocí Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

---

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

   a. Pokud chcete povolit neověřená nabízená oznámení, vyberte **Povolit neověřené nabízené** oznámení  >  .

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

* [Posílání nabízených oznámení do aplikací pro iOS pomocí Azure Notification Hubs](ios-sdk-get-started.md)
* [Posílání oznámení na zařízení s Androidem pomocí Notification Hubs a Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Odesílání oznámení do aplikace pro UWP běžící na zařízení s Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [Posílání oznámení do aplikace pro Windows Phone 8 pomocí MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Odesílání oznámení pomocí Notification Hubs a nabízená oznámení cloudu Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)

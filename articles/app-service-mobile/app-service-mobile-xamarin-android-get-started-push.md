---
title: Přidání nabízených oznámení do aplikace Xamarin.Android | Dokumentace Microsoftu
description: Další informace o použití služby Azure App Service a Azure Notification Hubs k odesílání nabízených oznámení do aplikace Xamarin.Android
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: f2c58d64a662a6e2be355c1f672c01e5a679b3f8
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993221"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Přidání nabízených oznámení do aplikace Xamarin.Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení [rychlý start Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) projekt tak, aby na zařízení přijde nabízené oznámení pokaždé, když se vložení záznamu.

Pokud použijete stažený projekt rychlého spuštění serveru, budete potřebovat balíček rozšíření nabízené oznámení. Další informace najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) průvodce.

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje nastavení:

* Aktivní účet Google. Můžete se zaregistrovat pro účet Google na [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Komponenta klienta zasílání zpráv cloudu Google](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Povolení služby Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Konfigurace Azure k odesílání nabízených oznámení požadavků

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Aktualizovat projekt serveru k odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Konfigurace projektu klienta pro nabízená oznámení

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Přidejte kód nabízených oznámení do vaší aplikace

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Nabízená oznámení ve vaší aplikaci

Aplikaci můžete otestovat pomocí virtuálního zařízení se spustila v emulátoru. Existují další konfigurační kroky potřebné při spouštění v emulátoru.

1. Rozhraní Google API nastavit jako cíl v nástroji Android Virtual Device (AVD) manager musí mít virtuální zařízení.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Kliknutím přidejte účet Google na zařízení s Androidem **aplikace** > **nastavení** > **přidat účet**, postupujte podle pokynů.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Spuštění aplikace seznamu úkolů stejně jako dříve a vložit novou položku seznamu úkolů. Tentokrát ikonu oznámení se zobrazí v oznamovací oblasti. Je-li otevřít panel oznámení k zobrazení textu v plném znění oznámení.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/

---
title: Přidání nabízených oznámení do aplikace Xamarin.Android
description: Přečtěte si, jak pomocí Azure App Service a Azure Notification Hubs odesílat nabízená oznámení do aplikace Xamarin.Android.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5657be0dbaeb46f8f899a9b4a2f8ba9b4fe9ebaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249305"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Přidání nabízených oznámení do aplikace Xamarin.Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [rychlého startu Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) tak, aby nabízené oznámení je odeslána do zařízení při každém vložení záznamu.

Pokud nepoužíváte stažený projekt serveru rychlého startu, budete potřebovat balíček rozšíření nabízených oznámení. Další informace najdete v [tématu práce s .NET back-end server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) průvodce.

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje nastavení:

* Aktivní účet Google. Účet Google si můžete zaregistrovat na [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Klientská komponenta Google Cloud Messaging](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="enable-firebase-cloud-messaging"></a><a id="register"></a>Povolení služby Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Konfigurace Azure pro odesílání nabízených požadavků

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>Aktualizace projektu serveru pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>Konfigurace klientského projektu pro nabízená oznámení

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="add-push-notifications-code-to-your-app"></a><a id="add-push"></a>Přidání kódu nabízených oznámení do aplikace

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test-push-notifications-in-your-app"></a><a name="test"></a>Testování nabízených oznámení v aplikaci

Aplikaci můžete otestovat pomocí virtuálního zařízení v emulátoru. Existují další kroky konfigurace potřebné při spuštění v emulátoru.

1. Virtuální zařízení musí mít rozhraní API Google nastavena jako cíl ve správci virtuálního zařízení Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Přidejte účet Google do zařízení Android kliknutím na**Nastavení** >  **aplikací** > **Přidat účet**a postupujte podle pokynů.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Spusťte aplikaci todolist jako dříve a vložte novou položku úkolů. Tentokrát se v oznamovací oblasti zobrazí ikona oznámení. Můžete otevřít zásuvku oznámení a zobrazit úplný text oznámení.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/

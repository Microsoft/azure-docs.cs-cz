---
title: Přidání nabízených oznámení do vaší aplikace Xamarin. Android | Microsoft Docs
description: Naučte se používat Azure App Service a Azure Notification Hubs k odesílání nabízených oznámení do vaší aplikace Xamarin. Android.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 25e0196fec94acb363757e74fa71f666a4c9d11c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388490"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Přidání nabízených oznámení do aplikace Xamarin.Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) dnes.

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [Xamarin. Android pro rychlý Start](app-service-mobile-windows-store-dotnet-get-started.md) , takže nabízené oznámení se odešle do zařízení při každém vložení záznamu.

Pokud nepoužíváte stažený projekt serveru pro rychlý Start, budete potřebovat balíček rozšíření nabízených oznámení. Další informace najdete v tématu [práce se serverovou sadou .NET back-end SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) průvodce.

## <a name="prerequisites"></a>Předpoklady

Tento kurz vyžaduje nastavení:

* Aktivní účet Google. Účet Google si můžete zaregistrovat na adrese [accounts.Google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud Messaging součásti klienta](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Povolení zasílání zpráv Firebase do cloudu

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Konfigurace Azure pro odesílání nabízených žádostí

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Aktualizace projektového serveru pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Konfigurace klientského projektu pro nabízená oznámení

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Přidání kódu nabízených oznámení do aplikace

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Testování nabízených oznámení ve vaší aplikaci

Aplikaci můžete testovat pomocí virtuálního zařízení v emulátoru. Při spuštění v emulátoru jsou vyžadovány další kroky konfigurace.

1. Virtuální zařízení musí mít ve Správci AVD (Android Virtual Device) nastavené rozhraní Google API jako cíl.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Kliknutím na **aplikace**@no__t**nastavení**-1  > **Přidat účet**a potom postupujte podle pokynů, přidejte do zařízení s Androidem účet Google.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Spusťte aplikaci ToDoList jako dřív a vložte novou položku todo. Tentokrát se v oznamovací oblasti zobrazí ikona oznámení. K zobrazení úplného textu oznámení můžete otevřít zásuvku oznámení.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/

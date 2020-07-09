---
title: Posílání nabízených oznámení do aplikací flutter pomocí Azure Notification Hubs přes back-end službu | Microsoft Docs
description: Přečtěte si, jak nabízet oznámení flutter aplikacím, které používají službu Azure Notification Hubs, prostřednictvím back-endu.
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: miparker
ms.openlocfilehash: 5fa753a6b8b1284c4f8fcd046f74fabcbae3f8fb
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171002"
---
# <a name="tutorial-send-push-notifications-to-flutter-apps-using-azure-notification-hubs-via-a-backend-service"></a>Kurz: posílání nabízených oznámení do aplikací flutter pomocí Azure Notification Hubs přes back-end službu  

[![Stažení ukázky ](./media/notification-hubs-backend-service-flutter/download.png) stažení ukázky](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

V tomto kurzu použijete [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) k nabízení oznámení do aplikace [flutter](https://flutter.dev) cílené na **Android** a **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Tento kurz vás provede následujícími kroky:

> [!div class="checklist"]
>
> * [Nastavte službu nabízených oznámení a Azure Notification Hubs.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Vytvořte back-end aplikaci ASP.NET Core webového rozhraní API.](#create-an-aspnet-core-web-api-backend-application)
> * [Vytvořte aplikaci flutter pro různé platformy.](#create-a-cross-platform-flutter-application)
> * [Nakonfigurujte nativní projekt pro Android pro nabízená oznámení.](#configure-the-native-android-project-for-push-notifications)
> * [Nakonfigurujte nativní projekt iOS pro nabízená oznámení.](#configure-the-native-ios-project-for-push-notifications)
> * [Otestujte řešení.](#test-the-solution)

## <a name="prerequisites"></a>Požadavky

Pokud chcete postup sledovat, budete potřebovat:

* [Předplatné Azure](https://portal.azure.com) , kde můžete vytvářet a spravovat prostředky.
* Sada [flutter](https://flutter.dev/docs/get-started/install) Toolkit (spolu s jejími požadavky).
* [Visual Studio Code](https://code.visualstudio.com) s nainstalovanými [moduly plug-in flutter a DART](https://flutter.dev/docs/get-started/editor?tab=vscode) .
* Možnost spouštět aplikaci na zařízeních s **Androidem** (fyzickou nebo emulátorovou) nebo **iOS** (jenom pro fyzická zařízení).

V případě Androidu potřebujete:

* Vývojář odemkl fyzické zařízení nebo emulátor *(spouští se rozhraní API 26 a vyšší s nainstalovanou služby Google Play)*.

Pro iOS musíte mít:

* Aktivní [vývojářský účet Apple](https://developer.apple.com).
* Fyzické zařízení s iOS, které je [zaregistrované ve vašem vývojářském účtu (na](https://help.apple.com/developer-account/#/dev40df0d9fa) *kterém běží iOS 13,0 a vyšší)*.
* [Vývojový certifikát](https://help.apple.com/developer-account/#/dev04fd06d56) **. p12** nainstalovaný v **řetězci klíčů** vám umožní [Spustit aplikaci na fyzickém zařízení](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).
* [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation) nainstalované pro správu závislostí knihoven.

> [!NOTE]
> Simulátor iOS nepodporuje vzdálená oznámení, takže při zkoumání této ukázky v iOS se vyžaduje fyzické zařízení. Pro dokončení tohoto kurzu ale nemusíte spouštět aplikaci pro **Android** i **iOS** .

Můžete postupovat podle kroků v tomto příkladě tohoto příkladu bez předchozího prostředí. Je ale výhodné se seznámit s následujícími aspekty.

* [Portál pro vývojáře Apple](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Konzola Google Firebase](https://console.firebase.google.com/u/0/)
* [Flutter](https://flutter.dev) a [DART](https://dart.dev) pro vývoj pro různé platformy
* [Kotlin](https://kotlinlang.org) a [SWIFT](https://developer.apple.com/swift) pro nativní vývoj pro Android a iOS

Výše uvedené kroky jsou specifické pro [MacOS](https://developer.apple.com/macos). V [systému Windows](https://www.microsoft.com/windows) je možné postupovat tím, že se přeskočí aspekty **iOS** .

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Nastavení služeb nabízených oznámení a centra oznámení Azure

V této části nastavíte **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** a **[službu APNs (Apple Push Notification Services)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)**. Pak vytvoříte a nakonfigurujete centrum oznámení pro práci s těmito službami.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Vytvoření back-endové aplikace ASP.NET Core webového rozhraní API

V této části vytvoříte back-end [ASP.NET Core webového rozhraní API](https://dotnet.microsoft.com/apps/aspnet/apis) pro zpracování [registrace zařízení](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) a odesílání oznámení do mobilní aplikace flutter.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-flutter-application"></a>Vytvoření aplikace flutter pro různé platformy

V této části vytvoříte mobilní aplikaci [flutter](https://flutter.dev) , která implementuje nabízená oznámení v různých způsobech pro různé platformy.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Flutter application](../../includes/notification-hubs-backend-service-sample-app-flutter.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Konfigurace nativního projektu pro Android pro nabízená oznámení

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-flutter-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Konfigurace nativního projektu iOS pro nabízená oznámení

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-flutter-ios.md)]

## <a name="test-the-solution"></a>Testování řešení

Nyní můžete testovat odesílání oznámení prostřednictvím back-endu služby.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Další kroky

Teď byste měli mít základní aplikaci flutter, která je připojená k centru oznámení prostřednictvím back-end služby a může odesílat a přijímat oznámení.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Poradce při potížích

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Související odkazy

* [Přehled služby Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Instalace flutter na macOS](https://flutter.dev/docs/get-started/install/macos)
* [Instalace flutter ve Windows](https://flutter.dev/docs/get-started/install/windows)
* [Sada SDK Notification Hubs pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Notification Hubs SDK na GitHubu](https://github.com/Azure/azure-notificationhubs)
* [Registrace s využitím back-endu aplikací](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Správa registrací](notification-hubs-push-notification-registration-management.md)
* [Práce s značkami](notification-hubs-tags-segment-push-message.md)
* [Práce s vlastními šablonami](notification-hubs-templates-cross-platform-push-messages.md)

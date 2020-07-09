---
title: Posílání nabízených oznámení, která reagují na nativní aplikace s využitím služby Azure Notification Hubs prostřednictvím back-endu | Microsoft Docs
description: Přečtěte si, jak nabízet oznámení pro reakci nativních aplikací, které používají službu Azure Notification Hubs prostřednictvím back-endu.
author: alexeystrakh
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/11/2020
ms.author: alstrakh
ms.openlocfilehash: 007be386b7b64fd3461fa508d35a4ef9be377c1f
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170904"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>Kurz: odeslání nabízených oznámení, která reagují na nativní aplikace s využitím Azure Notification Hubs přes back-end službu  

[![Stažení ukázky ](./media/notification-hubs-backend-service-react-native/download.png) stažení ukázky](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

V tomto kurzu použijete [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) k odesílání oznámení do nativní aplikace s [reakci](https://reactnative.dev/) , která cílí na **Android** a **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Tento kurz vás provede následujícími kroky:

> [!div class="checklist"]
>
> * [Nastavte službu nabízených oznámení a Azure Notification Hubs.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Vytvořte back-end aplikaci ASP.NET Core webového rozhraní API.](#create-an-aspnet-core-web-api-backend-application)
> * [Vytvoření nativní aplikace reagující na různých platformách](#create-a-cross-platform-react-native-application)
> * [Nakonfigurujte nativní projekt pro Android pro nabízená oznámení.](#configure-the-native-android-project-for-push-notifications)
> * [Nakonfigurujte nativní projekt iOS pro nabízená oznámení.](#configure-the-native-ios-project-for-push-notifications)
> * [Otestujte řešení.](#test-the-solution)

## <a name="prerequisites"></a>Požadavky

Pokud chcete postup sledovat, budete potřebovat:

* [Předplatné Azure](https://portal.azure.com) , kde můžete vytvářet a spravovat prostředky.
* Mac s nainstalovaným [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) (nebo počítač se sadou [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s **vývojem mobilních aplikací s .NET** ).
* Možnost spouštět aplikaci na zařízeních s **Androidem** (fyzickou nebo emulátorovou) nebo **iOS** (jenom pro fyzická zařízení).

V případě Androidu potřebujete:

* Vývojář odemkl fyzické zařízení nebo emulátor *(spouští se rozhraní API 26 a vyšší s nainstalovanou služby Google Play)*.

Pro iOS musíte mít:

* Aktivní [vývojářský účet Apple](https://developer.apple.com).
* Fyzické zařízení s iOS, které je [zaregistrované ve vašem vývojářském účtu (na](https://help.apple.com/developer-account/#/dev40df0d9fa) *kterém běží iOS 13,0 a vyšší)*.
* [Vývojový certifikát](https://help.apple.com/developer-account/#/dev04fd06d56) **. p12** nainstalovaný v **řetězci klíčů** vám umožní [Spustit aplikaci na fyzickém zařízení](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).

> [!NOTE]
> Simulátor iOS nepodporuje vzdálená oznámení, takže při zkoumání této ukázky v iOS se vyžaduje fyzické zařízení. Pro dokončení tohoto kurzu ale nemusíte spouštět aplikaci pro **Android** i **iOS** .

Můžete postupovat podle kroků v tomto příkladě tohoto příkladu bez předchozího prostředí. Je ale výhodné se seznámit s následujícími aspekty.

* [Portál pro vývojáře Apple](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Konzola Google Firebase](https://console.firebase.google.com/u/0/)
* [React Native](https://reactnative.dev/docs/getting-started)

Výše uvedené kroky jsou určené pro [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) a [Visual Studio Code](https://code.visualstudio.com/download) , ale je možné postupovat i s použitím sady [Visual Studio 2019](https://visualstudio.microsoft.com/vs).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Nastavení služeb nabízených oznámení a centra oznámení Azure

V této části nastavíte **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** a **[službu APNs (Apple Push Notification Services)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)**. Pak vytvoříte a nakonfigurujete centrum oznámení pro práci s těmito službami.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Vytvoření back-endové aplikace ASP.NET Core webového rozhraní API

V této části vytvoříte back-end [ASP.NET Core webového rozhraní API](https://dotnet.microsoft.com/apps/aspnet/apis) pro zpracování [registrace zařízení](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) a odesílání oznámení do nativní mobilní aplikace s reagující na reakci.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>Vytvoření nativní aplikace reagující na různých platformách

V této části vytvoříte [reakci nativní](https://reactnative.dev/) mobilní aplikace, která implementuje nabízená oznámení v různých platformách.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](../../includes/notification-hubs-backend-service-sample-app-reactnative.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Konfigurace nativního projektu pro Android pro nabízená oznámení

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-reactnative-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Konfigurace nativního projektu iOS pro nabízená oznámení

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-reactnative-ios.md)]

## <a name="test-the-solution"></a>Testování řešení

Nyní můžete testovat odesílání oznámení prostřednictvím back-endu služby.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Další kroky

Teď byste měli mít základní aplikaci s reakci, která je připojená k centru oznámení prostřednictvím back-end služby a může odesílat a přijímat oznámení.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Poradce při potížích

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Související odkazy

* [Přehled služby Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Instalace sady Visual Studio pro Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Instalace Visual Studio Code](https://code.visualstudio.com/download)
* [Nastavení reakce nativního vývojového prostředí](https://reactnative.dev/docs/environment-setup)
* [Sada SDK Notification Hubs pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Notification Hubs SDK na GitHubu](https://github.com/Azure/azure-notificationhubs)
* [Registrace s využitím back-endu aplikací](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Správa registrací](notification-hubs-push-notification-registration-management.md)
* [Práce s značkami](notification-hubs-tags-segment-push-message.md)
* [Práce s vlastními šablonami](notification-hubs-templates-cross-platform-push-messages.md)

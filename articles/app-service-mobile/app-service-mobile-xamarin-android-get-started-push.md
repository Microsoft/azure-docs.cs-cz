---
title: Přidání nabízených oznámení do aplikace Xamarin.Android | Microsoft Docs
description: Naučte se používat Azure App Service a Azure Notification Hubs k odesílání nabízených oznámení do aplikace Xamarin.Android
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
ms.openlocfilehash: a6cdff68d63859c6a6612b606664d3e1fbaae375
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29118271"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Přidání nabízených oznámení do aplikace Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled
V tomto kurzu přidáte nabízená oznámení [rychlý start Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) projektu tak, aby nabízených oznámení se odešle do zařízení pokaždé, když vložení záznamu.

Pokud použijete serverový projekt stažené rychlé spuštění, budete potřebovat balíček rozšíření nabízená oznámení. Další informace najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) průvodce.

## <a name="prerequisites"></a>Požadavky
Tento kurz vyžaduje instalaci:

* Aktivní účet Google. Můžete si zaregistrovat účet Google na webu [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Komponenta klienta zasílání zpráv cloudu Google](http://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Konfigurace centra oznámení
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Povolit Firebase cloudu zasílání zpráv
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Konfigurace Azure na odesílání žádostí push
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Aktualizace serverový projekt k odesílání nabízených oznámení
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Konfigurace projektu klienta pro nabízená oznámení
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Přidání kódu nabízených oznámení do aplikace
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Nabízená oznámení v aplikaci
Aplikaci můžete otestovat pomocí virtuálního zařízení v emulátoru. Existují další konfigurační kroky potřebné při spuštění v emulátoru.

1. Virtuální zařízení musí mít nastavenou jako cíl ve Správci virtuální zařízení Android (AVD) rozhraní API Google.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. Kliknutím na Přidat účet Google do zařízení s Androidem **aplikace** > **nastavení** > **přidejte účet**, postupujte podle pokynů.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Spusťte aplikaci seznamu úkolů jako před a vložit novou položku úkolů. Tentokrát ikonu oznámení se zobrazí v oznamovací oblasti. Můžete otevřít panel oznámení k zobrazení textu v plném znění oznámení.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

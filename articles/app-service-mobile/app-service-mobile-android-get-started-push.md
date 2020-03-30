---
title: Přidání nabízených oznámení do aplikace pro Android
description: Přečtěte si, jak pomocí mobilních aplikací odesílat nabízená oznámení do aplikace pro Android.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6fec85c028e992c15fb9503ffb599023e668c58f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459935"
---
# <a name="add-push-notifications-to-your-android-app"></a>Přidání nabízených oznámení do aplikace pro Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [rychlého startu Android] tak, aby nabízené oznámení je odeslána do zařízení při každém vložení záznamu.

Pokud nepoužíváte stažený projekt serveru rychlého startu, potřebujete balíček rozšíření nabízených oznámení. Další informace naleznete [v tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Požadavky

Potřebujete následující:

* IDE, v závislosti na back-endu projektu:

  * [Android Studio,](https://developer.android.com/sdk/index.html) pokud tato aplikace má Back-end Node.js.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) nebo novější, pokud má tato aplikace back-end Microsoft .NET.
* Android 2.3 nebo novější, revize úložiště Google 27 nebo novější a služby Google Play 9.0.2 nebo novější pro Služby Firebase Cloud Messaging.
* Dokončete [rychlý start androida].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Vytvoření projektu, který podporuje službu Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Povolení nabízených oznámení pro projekt serveru

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Přidání nabízených oznámení do aplikace

V této části aktualizujete klientskou aplikaci pro Android tak, aby zpracovávala nabízená oznámení.

### <a name="verify-android-sdk-version"></a>Ověření verze sady Android SDK

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Dalším krokem je instalace služeb Google Play. Firebase Cloud Messaging má některé minimální požadavky na úrovni rozhraní API pro vývoj a testování, které **minSdkVersion** vlastnost v manifestu musí odpovídat.

Pokud testujete se starším zařízením, podívejte se do aplikace Android Project na přidání Firebase a [zjistěte,] jak nízkou můžete tuto hodnotu nastavit, a nastavte ji odpovídajícím způsobem.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Přidání cloudového zasílání zpráv Firebase do projektu

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Přidat kód

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testování aplikace proti publikované mobilní službě

Aplikaci můžete otestovat přímým připojením telefonu Android pomocí kabelu USB nebo pomocí virtuálního zařízení v emulátoru.

## <a name="next-steps"></a>Další kroky

Nyní, když jste dokončili tento kurz, zvažte pokračování na jednom z následujících kurzů:

* [Přidejte ověřování do aplikace pro Android](app-service-mobile-android-get-started-users.md).
  Přečtěte si, jak přidat ověřování do projektu rychlého startu todoseznamu v systému Android pomocí podporovaného poskytovatele identity.
* [Povolte offline synchronizaci pro aplikaci pro Android](app-service-mobile-android-get-started-offline-data.md).
  Přečtěte si, jak do aplikace přidat podporu offline pomocí back-endu mobilních aplikací. Při offline synchronizaci mohou uživatelé&mdash;pracovat se zobrazením,&mdash;přidáváním nebo úpravou dat v mobilní aplikaci, i když neexistuje žádné síťové připojení.

<!-- URLs -->
[Android rychlý start]: app-service-mobile-android-get-started.md
[Přidání Firebase do projektu Android]:https://firebase.google.com/docs/android/setup

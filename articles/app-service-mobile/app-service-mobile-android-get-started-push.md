---
title: Přidání nabízených oznámení do aplikace pro Android
description: Naučte se používat Mobile Apps k odesílání nabízených oznámení do vaší aplikace pro Android.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ce4ebe9e8874e779b8da16e9c30fcfc3ca46754e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668615"
---
# <a name="add-push-notifications-to-your-android-app"></a>Přidání nabízených oznámení do aplikace pro Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete do vaší mobilní aplikace integrovat cloudové služby, ještě dnes se zaregistrujte do služeb [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu s [Rychlý Start pro Android] , takže nabízené oznámení se pošle do zařízení pokaždé, když se záznam vloží.

Pokud nepoužíváte stažený projekt serveru pro rychlý Start, budete potřebovat balíček rozšíření nabízených oznámení. Další informace najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Předpoklady

Potřebujete následující:

* Integrované vývojové prostředí (IDE) v závislosti na back-endu vašeho projektu:

  * [Android Studio](https://developer.android.com/sdk/index.html) , jestli má tato aplikace back-end Node. js.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) nebo novější, pokud má tato aplikace back-end Microsoft .NET.
* Android 2,3 nebo novější, Revize úložiště Google 27 nebo novější a Služby Google Play 9.0.2 nebo novější pro zasílání zpráv Firebase do cloudu.
* Dokončete [Rychlý Start pro Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Vytvoření projektu, který podporuje službu Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Povolit nabízená oznámení pro serverový projekt

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Přidání nabízených oznámení do aplikace

V této části aktualizujete klientskou aplikaci pro Android tak, aby zpracovávala nabízená oznámení.

### <a name="verify-android-sdk-version"></a>Ověřit verzi Android SDK

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Dalším krokem je instalace služby Google Play Services. Firebase Cloud Messaging má některé minimální požadavky na úroveň rozhraní API pro vývoj a testování, které musí splňovat vlastnost **hodnotu minsdkversion** v manifestu.

Pokud testujete pomocí staršího zařízení, přečtěte si článek [Přidání Firebase do projektu pro Android] , kde zjistíte, jak nízká hodnota se dá nastavit, a nastavte ji odpovídajícím způsobem.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Přidání Firebase cloudového zasílání zpráv do projektu

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Přidat kód

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testování aplikace proti publikované mobilní službě

Aplikaci můžete otestovat tak, že přímo připojíte telefon s Androidem pomocí kabelu USB, nebo pomocí virtuálního zařízení v emulátoru.

## <a name="next-steps"></a>Další kroky

Nyní, když jste dokončili tento kurz, zvažte pokračování v jednom z následujících kurzů:

* [Přidejte do svojí aplikace pro Android ověřování](app-service-mobile-android-get-started-users.md).
  Naučte se, jak přidat ověřování do projektu rychlého startu ToDoList v Androidu s použitím podporovaného zprostředkovatele identity.
* [Povolte offline synchronizaci vaší aplikace pro Android](app-service-mobile-android-get-started-offline-data.md).
  Naučte se přidat do aplikace podporu offline pomocí Mobile Apps back-endu. Při offline synchronizaci můžou uživatelé komunikovat s mobilní aplikací&mdash;zobrazení, přidání nebo úprava dat&mdash;i v případě, že není k dispozici žádné síťové připojení.

<!-- URLs -->
[Rychlý Start pro Android]: app-service-mobile-android-get-started.md
[Přidání Firebase do projektu pro Android]: https://firebase.google.com/docs/android/setup

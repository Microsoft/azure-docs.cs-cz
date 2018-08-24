---
title: Přidání nabízených oznámení do aplikace pro Android s funkcí Mobile Apps | Dokumentace Microsoftu
description: Naučte se využívat Mobile Apps k odesílání nabízených oznámení do aplikace pro Android.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: conceptdev
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/17/2017
ms.author: crdun
ms.openlocfilehash: 557f6f6a6d4925ec167760455dfc67449582c05c
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811666"
---
# <a name="add-push-notifications-to-your-android-app"></a>Přidání nabízených oznámení do aplikace pro Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení [Android úvodní] projekt tak, aby na zařízení přijde nabízené oznámení pokaždé, když se vložení záznamu.

Pokud použijete serverový projekt stažené rychlý start, je nutné balíček rozšíření nabízené oznámení. Další informace najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Požadavky

Budete potřebovat následující:

* Integrované vývojové prostředí, v závislosti na back-endu vašeho projektu:

  * [Android Studio](https://developer.android.com/sdk/index.html) Pokud tato aplikace má back-end Node.js.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) nebo novější, pokud tato aplikace má back-end Microsoft .NET.
* Android 2.3 nebo novější, úložiště Google verze 27 nebo novější a služby Google Play 9.0.2 nebo novější pro službu Firebase Cloud Messaging.
* Dokončení [Android úvodní].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Vytvoření projektu, který podporuje službu Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure k odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Povolte nabízená oznámení pro server project

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Přidání nabízených oznámení do aplikace

V této části aktualizujete Android klientské aplikace pro zpracování nabízených oznámení.

### <a name="verify-android-sdk-version"></a>Ověřit verzi sady SDK pro Android

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

Dalším krokem je instalace služby Google Play. Firebase Cloud Messaging některé minimální API požadavky na úroveň pro vývoj a testování, který má **minSdkVersion** musí odpovídat vlastnosti v manifestu.

Pokud testujete s starší zařízení, projděte si [přidat Firebase do vašeho projektu Android] určíte, jak nízké může nastavte tuto hodnotu a odpovídajícím způsobem nastavit.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Přidejte do projektu Firebase Cloud Messaging

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Přidejte kód

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testování aplikací proti publikované mobilní služby

Aplikaci můžete otestovat přímo připojením telefon s Androidem pomocí kabelu USB, nebo pomocí virtuálního zařízení se spustila v emulátoru.

## <a name="next-steps"></a>Další postup

Teď, když jste dokončili tento kurz, vezměte v úvahu pokračováním jednu z následujících kurzů:

* [Přidání ověřování do aplikace pro Android](app-service-mobile-android-get-started-users.md).
  Zjistěte, jak přidat ověřování do seznamu úkolů projektu na Android pomocí zprostředkovatele identity podporovaná.
* [Povolení offline synchronizace u aplikace pro Android](app-service-mobile-android-get-started-offline-data.md).
  Zjistěte, jak přidat do aplikace podporu offline režimu pomocí back-end Mobile Apps. Offline synchronizace, mohou uživatelé komunikovat s mobilní aplikací&mdash;zobrazení, přidání nebo úprava dat&mdash;i v případě, že není žádné síťové připojení.

<!-- URLs -->
[Android úvodní]: app-service-mobile-android-get-started.md
[přidat Firebase do vašeho projektu Android]:https://firebase.google.com/docs/android/setup

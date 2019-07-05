---
title: Přidání nabízených oznámení do aplikace s Azure Mobile Apps pro iOS
description: Zjistěte, jak používat Azure Mobile Apps k odesílání nabízených oznámení do aplikace pro iOS.
services: app-service\mobile
documentationcenter: ios
manager: crdun
editor: ''
author: elamalani
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 5ab968e88331f888dfcecd2cc30a658b0b0f53ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445362"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Přidání nabízených oznámení do aplikace pro iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center investuje do nové a integrované služby, které jsou centrální při vývoji mobilních aplikací. Vývojáři mohou použít **sestavení**, **testovací** a **rozmístit** služby vytvořit kanál pro průběžnou integraci a doručování. Po nasazení aplikace se můžou vývojáři monitorovat stav a využití své aplikace pomocí **Analytics** a **diagnostiky** služeb a Zaujměte uživatele, kteří používají **Push** Služba. Vývojáři mohou využít i **Auth** k ověření uživatelů a **Data** službu zachovat, synchronizaci dat aplikací v cloudu. Podívejte se na [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-push) ještě dnes.
>

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení [Rychlý start pro iOS] projekt tak, aby na zařízení přijde nabízené oznámení pokaždé, když se vložení záznamu.

Pokud použijete stažený projekt rychlého spuštění serveru, budete potřebovat balíček rozšíření nabízené oznámení. Další informace najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) průvodce.

[Simulátoru iOS nepodporuje nabízená oznámení](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Potřebujete na fyzickém zařízení iOS a [členství v programu Apple Developer](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registrace aplikace pro nabízená oznámení

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure k odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Aktualizovat back-endu k odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Přidání nabízených oznámení do aplikace

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Nabízená oznámení

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Více

* Šablony poskytují flexibilitu pro odesílání nabízených oznámení napříč platformami a lokalizovaných nabízených oznámení. [Jak iOS použijte klientskou knihovnu pro Azure Mobile Apps](app-service-mobile-ios-how-to-use-client-library.md#templates) ukazuje, jak zaregistrovat šablony.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Rychlý start pro iOS]: app-service-mobile-ios-get-started.md

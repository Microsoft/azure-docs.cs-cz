---
title: Přidání nabízených oznámení do iOS
description: Přečtěte si, jak pomocí mobilních aplikací Azure odesílat nabízená oznámení do aplikace pro iOS.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461500"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Přidání nabízených oznámení do aplikace pro iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [rychlého startu iOS] tak, aby nabízené oznámení je odeslána do zařízení při každém vložení záznamu.

Pokud nepoužíváte stažený projekt serveru rychlého startu, budete potřebovat balíček rozšíření nabízených oznámení. Další informace najdete v [tématu Práce s back-endový server .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) průvodce.

[Simulátor iOS nepodporuje nabízená oznámení](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Potřebujete fyzické iOS zařízení a [členství v Programu pro vývojáře Apple](https://developer.apple.com/programs/ios/).

## <a name="configure-notification-hub"></a><a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-app-for-push-notifications"></a><a id="register"></a>Registrace aplikace pro nabízená oznámení

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-backend-to-send-push-notifications"></a><a id="update-server"></a>Aktualizace back-endu pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="add-push-notifications-to-app"></a><a id="add-push"></a>Přidání nabízených oznámení do aplikace

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a name="test-push-notifications"></a><a id="test"></a>Testovací nabízená oznámení

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a name="more"></a><a id="more"></a>Víc

* Šablony poskytují flexibilitu při odesílání nabízených oznámení napříč platformami a lokalizovaných nabízených oznámení. [Jak používat klientskou knihovnu iOS pro Mobilní aplikace Azure,](app-service-mobile-ios-how-to-use-client-library.md#templates) ukazuje, jak registrovat šablony.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Rychlý start iOS]: app-service-mobile-ios-get-started.md

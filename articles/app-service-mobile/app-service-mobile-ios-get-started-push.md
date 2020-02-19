---
title: Přidání nabízených oznámení do systému iOS
description: Naučte se používat Azure Mobile Apps k odesílání nabízených oznámení do vaší aplikace pro iOS.
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: ad58ee158a2e1842c1b41db281bdd9f04b9b2ca4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461500"
---
# <a name="add-push-notifications-to-your-ios-app"></a>Přidání nabízených oznámení do aplikace pro iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do projektu [rychlý Start pro iOS] , takže se do zařízení pošle nabízené oznámení pokaždé, když se do něj vloží záznam.

Pokud nepoužíváte stažený projekt serveru pro rychlý Start, budete potřebovat balíček rozšíření nabízených oznámení. Další informace najdete v tématu [práce se sadou SDK back-end serveru .NET pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) Guide.

[Simulátor iOS nepodporuje nabízená oznámení](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Potřebujete fyzické zařízení se systémem iOS a [členství v programu Apple Developer](https://developer.apple.com/programs/ios/).

## <a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registrovat aplikaci pro nabízená oznámení

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Konfigurace Azure pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a id="update-server"></a>Aktualizace back-endu pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Přidání nabízených oznámení do aplikace

[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Test nabízených oznámení

[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a id="more"></a>Aktuálnější

* Šablony vám poskytnou flexibilitu při odesílání nabízených oznámení mezi platformami a lokalizovaných nabízených oznámení. Jak [používat klientskou knihovnu iOS pro Azure Mobile Apps](app-service-mobile-ios-how-to-use-client-library.md#templates) ukazuje, jak registrovat šablony.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[rychlý Start pro iOS]: app-service-mobile-ios-get-started.md

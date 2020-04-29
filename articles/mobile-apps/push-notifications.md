---
title: Důležitost nabízených oznámení v mobilních aplikacích pomocí Visual Studio App Center a Azure Notification Hubs
description: Přečtěte si o službách, jako je Visual Studio App Center, které můžete použít k zapojení uživatelů vaší mobilní aplikace.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80235329"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Zapojení uživatelů aplikace pomocí odesílání nabízených oznámení

Bez ohledu na to, zda vytváříte aplikace pro zákazníky nebo podnikové aplikace, chcete, aby uživatelé mohli aktivně používat svou aplikaci. K dispozici jsou často časy, kdy chcete sdílet novinky, aktualizace her, Skvělé nabídky, aktualizace produktů nebo jakékoli jiné důležité informace s uživateli. Chcete-li zvýšit zapojení s vašimi uživateli a vrátit se do vaší aplikace, budete potřebovat způsob, jak komunikovat s uživateli v reálném čase.

Nabízená oznámení poskytují rychlý a efektivní způsob, jak komunikovat s uživateli vaší aplikace. Můžete se obrátit na uživatele ve správný čas a upozorňovat na požadované informace, obvykle v místní nabídce nebo dialogovém okně v mobilním zařízení bez ohledu na to, co dělají.

## <a name="value-of-push-notifications"></a>Hodnota nabízených oznámení
Nabízená oznámení poskytují uživatelům a firmám hodnotu.

Firmy můžou:
- Posílání zpráv na podporovaných platformách ve správnou dobu komunikuje přímo s uživateli.
- Zvyšte zapojení uživatelů tím, že do uživatelů odešlete aktualizace a připomenutí v reálném čase, a povzbuzovat, aby se s aplikací pravidelně zapojily.
- Zachovejte uživatele a znovu se zapojte pomocí neaktivních uživatelů, kteří aplikaci stáhli, ale nepoužívají ji pro opětovné zprovoznění.
- Zvyšte míry převodu analýzou chování uživatelů, segmentace uživatelů a využitím kampaní na základě mobilních nabízených oznámení.
- Propagujte produkty a služby odesláním nabízených zpráv a včasných aktualizací uživatelům.
- Zacílení uživatelů odesláním individuálních nabízených zpráv.

Pro uživatele aplikace nabízená oznámení:
- Zadejte hodnotu a informace v reálném čase.
- Připomínat uživatelům používání aplikace.

Pomocí následujících služeb můžete povolit nabízená oznámení ve vašich mobilních aplikacích.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Pomocí [App Center push](/appcenter/push/)můžete posílat cílené zprávy uživatelům iOS, Androidu a Windows, aniž byste museli spravovat proces odesílání oznámení do zařízení pomocí služby nabízených oznámení (PNS). Tato služba je postavená na Azure Notification Hubs, eliminuje složitosti spojené s doručováním oznámení ručně tím, že poskytuje výkonný řídicí panel.

**Klíčové funkce**
- Posílání nabízených oznámení do mobilních zařízení napříč různými platformami.
- Pomocí oznámení můžete odesílat data do aplikace, zobrazit uživateli zprávu nebo aktivovat akci aplikací.
- Použít cíle oznámení na: 
    - Vysílat zprávy na všechna registrovaná zařízení.
    - Odešlete oznámení do cílových skupin na základě informací o zařízení a vlastních vlastností.
    - Odesílat oznámení konkrétním uživatelům.
    - Odesílat oznámení na konkrétní zařízení.
- Využijte bohatou telemetrii na nabízených oznámeních, zařízeních a chybách, které jsou k dispozici na portálu App Center.
- Získejte podporu platforem pro iOS, Android, macOS, Xamarin, reagovat na nativní, Unity a Cordova.

**Odkazy**
- [Zaregistrujte se Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s App Center push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) poskytuje snadno použitelný a Škálovatelný modul nabízených oznámení. Můžete ji použít k posílání oznámení na libovolnou platformu a z libovolného back-endu v cloudu nebo v místním prostředí.

**Klíčové funkce**
- Posílání nabízených oznámení na libovolnou platformu z libovolného back-endu v cloudu nebo v místním prostředí.
- Rychlé vysílání nabízených oznámení do milionů mobilních zařízení pomocí jediného volání rozhraní API.
- Přizpůsobení nabízených oznámení podle zákazníka, jazyka a místa.
- Dynamické definování a oznamování zákaznických segmentů.
- Škálujte okamžitě na miliony mobilních zařízení.
- Získejte podporu platforem pro iOS, Android, Windows, Kindle a Baidu.
        
**Odkazy**
- [portál Azure](https://portal.azure.com) 
- [Začínáme s Azure Notification Hubs](/azure/notification-hubs/)
- [Rychlé starty](/azure/notification-hubs/create-notification-hub-portal)
- [ukázky](/azure/notification-hubs/samples)

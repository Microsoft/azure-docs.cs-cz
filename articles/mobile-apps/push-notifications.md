---
title: Důležitost nabízených oznámení v mobilních aplikacích s Centrem aplikací Visual Studio a Centry oznámení Azure
description: Přečtěte si o službách, jako je Visual Studio App Center, které můžete použít k interakci s uživateli mobilních aplikací.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235329"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Komunikujte s uživateli aplikace odesíláním nabízených oznámení

Ať už vytváříte spotřebitelské nebo podnikové aplikace, chcete, aby uživatelé vaši aplikaci aktivně používali. Často se stává, že chcete sdílet nejnovější zprávy, aktualizace her, vzrušující nabídky, aktualizace produktů nebo jiné relevantní informace s uživateli. Chcete-li zvýšit zájem o uživatele a přimět je, aby se vrátili do vaší aplikace, potřebujete způsob, jak komunikovat s uživateli v reálném čase.

Nabízená oznámení poskytují rychlý a efektivní způsob komunikace s uživateli aplikace. Můžete se s uživateli spojit ve správný čas a upozornit je na požadované informace, obvykle v automaticky otevírané položce nebo dialogovém okně na mobilním zařízení, bez ohledu na to, co dělají.

## <a name="value-of-push-notifications"></a>Hodnota nabízených oznámení
Nabízená oznámení poskytují hodnotu uživatelům a firmám.

Firmy mohou:
- Komunikujte přímo s uživateli odesíláním zpráv na podporovaných platformách ve správný čas.
- Zvyšte zapojení uživatelů odesíláním aktualizací a připomenutí v reálném čase uživatelům a povzbuzujte je k pravidelnému zapojení do aplikace.
- Uchovávejte uživatele a znovu se spojte s neaktivními uživateli, kteří stáhli aplikaci, ale nepoužívejte ji k opětovnému aktivnímu.
- Zvyšte míru konverze analýzou chování uživatelů, segmentací uživatelů a využitím kampaní založených na mobilních nabízených oznámeních.
- Propagujte produkty a služby odesíláním nabízených zpráv a včasných aktualizací uživatelům.
- Zacilte na uživatele odesíláním přizpůsobených nabízených zpráv.

Pro uživatele aplikací nabízená oznámení:
- Poskytněte hodnotu a informace v reálném čase.
- Připomeňte uživatelům, aby používali aplikaci.

Pomocí následujících služeb můžete v mobilních aplikacích povolit nabízená oznámení.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Pomocí [služby Push centra aplikací](/appcenter/push/)můžete odesílat cílené zprávy uživatelům iOS, Android u Windows, aniž byste museli spravovat proces odesílání oznámení do zařízení pomocí služeb nabízených oznámení (PNS). Tato služba, postavená na azure notification hubech, eliminuje složitosti spojené s ručním odesíláním oznámení tím, že poskytuje výkonný řídicí panel.

**Klíčové funkce**
- Posílejte nabízená oznámení do mobilních zařízení na různých platformách.
- Pomocí oznámení můžete odeslat data do aplikace, zobrazit zprávu uživateli nebo spustit akci aplikace.
- Pomocí cílů oznámení můžete: 
    - Vysměrovávat zprávy do všech registrovaných zařízení.
    - Posílejte oznámení cílovým skupinám na základě informací o zařízení a vlastních vlastností.
    - Odesílat oznámení konkrétním uživatelům.
    - Odesílat oznámení konkrétním zařízením.
- Využijte bohatou telemetrii na nabízených oznámeních, zařízeních a chybách, které jsou dostupné na portálu App Center.
- Získejte podporu platformy pro iOS, Android, macOS, Xamarin, React Native, Unity a Cordova.

**Odkazy**
- [Registrace pomocí Centra aplikací Visual Studia](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s pushem centra aplikací](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Centra oznámení](/azure/notification-hubs/notification-hubs-push-notification-overview) poskytují snadno použitelný a škálovaný tlačný modul. Můžete ji použít k odesílání oznámení na libovolnou platformu a z libovolného back-endu v cloudu nebo v místním prostředí.

**Klíčové funkce**
- Odesílejte nabízená oznámení na libovolnou platformu z libovolného back-endu, v cloudu nebo místně.
- Rychlé vysílání se posouvá na miliony mobilních zařízení pomocí jediného volání rozhraní API.
- Přizpůsobte nabízená oznámení podle zákazníka, jazyka a lokality.
- Dynamicky definujte a upozorňujte segmenty zákazníků.
- Okamžitě škálujte na miliony mobilních zařízení.
- Získejte podporu platformy pro iOS, Android, Windows, Kindle a Baidu.
        
**Odkazy**
- [Portál Azure](https://portal.azure.com) 
- [Začínáme s Azure Notification Hubs](/azure/notification-hubs/)
- [Rychlé starty](/azure/notification-hubs/create-notification-hub-portal)
- [ukázky](/azure/notification-hubs/samples)

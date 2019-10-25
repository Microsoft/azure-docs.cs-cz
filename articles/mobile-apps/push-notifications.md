---
title: Důležitost nabízených oznámení v mobilních aplikacích pomocí Visual Studio App Center a Azure Notification Hubs
description: Přečtěte si o službách, jako je App Center, které vám umožní zapojit uživatele z mobilních aplikací.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795910"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Zapojení uživatelů aplikace pomocí odesílání nabízených oznámení 

Ať už vytváříte aplikace pro zákazníky nebo podnikové aplikace, chcete, aby ji uživatelé aktivně používali. K dispozici jsou často časy, kdy chcete sdílet novinky, aktualizace her, Skvělé nabídky, aktualizace produktu nebo jakékoli relevantní informace s vašimi uživateli. Aby bylo možné zvýšit zapojení s vašimi uživateli a nechat si je vracet do vaší aplikace, budete potřebovat způsob, jak komunikovat s uživateli v reálném čase.

Nabízená oznámení poskytují rychlý a efektivní způsob, jak komunikovat s uživateli vaší aplikace. Můžete se obrátit na uživatele ve správné době a může jim upozorňovat na požadované informace, obvykle v místním nebo dialogovém okně na mobilním zařízení bez ohledu na to, co dělají.

## <a name="value-of-push-notifications"></a>Hodnota nabízených oznámení
Nabízená oznámení poskytují koncovým uživatelům i firmám hodnotu.

Firmy můžou:
- **Komunikujte přímo s uživateli** tím, že jim pošlete zprávy na podporované platformě ve správnou dobu.
- **Zvyšte zapojení uživatelů** tím, že všem uživatelům odešlete aktualizace a připomenutí v reálném čase, požádejte je, aby se do vaší aplikace pravidelně zapojili.
- **Zachovejte uživatele** a znovu se připojte k neaktivním uživatelům, kteří si aplikaci stáhli, ale nepoužívejte ji k opětovnému zprovoznění.
- **Zvyšte míry převodu** analýzou chování koncových uživatelů, segmentace koncových uživatelů a využití kampaní na základě mobilních nabízených oznámení.
- **Propagujte produkty a služby** odesláním nabízených zpráv a včasných aktualizací uživatelům.
- **Zacílení uživatelů** odesláním individuálních nabízených zpráv.

Pro uživatele aplikace nabízená oznámení:
- **Zadejte hodnotu a informace** v reálném čase.
- **Připomínat uživatelům** používání aplikace.

Pomocí následujících služeb můžete povolit nabízená oznámení ve vašich mobilních aplikacích.

## <a name="visual-studio-app-center"></a>Centrum aplikací Visual Studio
Služba [nabízených oznámení App Center](/appcenter/push/) umožňuje zapojení uživatelů tím, že je posílá do uživatelů iOS, Androidu a Windows, aniž by museli spravovat proces odesílání oznámení do zařízení pomocí služby nabízených oznámení (PNS). Tato služba je postavená na Azure Notification Hubs, eliminuje složitosti spojené s doručováním oznámení ručně tím, že poskytuje výkonný řídicí panel.

**Klíčové funkce**
- **Posílání nabízených oznámení do mobilních zařízení** napříč různými platformami.
- Pomocí oznámení můžete odesílat data do aplikace, zobrazit uživateli zprávu nebo aktivovat akci aplikací.
- Cíle oznámení: 
    - Odesílat zprávy všesměrového vysílání na **Všechna registrovaná zařízení**.
    - Posílání oznámení do **cílových skupin** vytvořených na základě informací o zařízení a vlastních vlastností
    - Odesílat oznámení **konkrétním uživatelům**.
    - Odesílat oznámení na **konkrétní zařízení**.
- **Bohatá telemetrie** na vyžádání, zařízení, chyba je k dispozici na portálu App Center.
- **Podpora platforem** – iOS, Android, MacOS, Xamarin, reakce nativních, Unity, Cordova.

**Odkazy**
- [Zaregistrujte se App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Začínáme s App Center push](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) poskytují snadno použitelný a Škálovatelný modul nabízených oznámení, který umožňuje odesílat oznámení na libovolnou platformu a ze všech back-endu (v cloudu nebo v místním prostředí).

**Klíčové funkce**
- **Posílání nabízených oznámení** na libovolnou platformu z libovolného back-endu v cloudu nebo v místním prostředí.
- **Rychlé vysílání** nabízených oznámení do milionů mobilních zařízení pomocí jediného volání rozhraní API.
- Přizpůsobení nabízených oznámení podle zákazníka, jazyka a místa.
- Dynamické definování a oznamování **zákaznických segmentů**.
- **Škálujte okamžitě** na miliony mobilních zařízení.
- **Podpora platforem** – iOS, Android, Windows, Kindle, Baidu.
        
**Odkazy**
- [Azure Portal](https://portal.azure.com) 
- [Začínáme s Azure Notification Hubs](/azure/notification-hubs/)   
- [Rychlé starty](/azure/notification-hubs/create-notification-hub-portal)
- [Ukázky](/azure/notification-hubs/samples)

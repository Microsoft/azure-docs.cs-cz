---
title: Koncepty SMS v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o konceptech služby Communication Services pro SMS.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 7a02acb01e72f594f6c0fe601c4aeea25591c0e4
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487521"
---
# <a name="sms-concepts"></a>Koncepce SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Služba Communications Services Azure umožňuje odesílat a přijímat textové zprávy SMS pomocí klientských knihoven služby Communication Services SMS. Tyto klientské knihovny lze použít k podpoře scénářů služeb zákazníkům, připomenutí schůzky, dvojúrovňového ověřování a dalších požadavků na komunikaci v reálném čase. Služba Communications Services SMS vám umožní spolehlivě odesílat zprávy a vystavovat přehledy o dodávkách a odpovědích na vaše kampaně.

Mezi klíčové funkce klientských knihoven SMS služby Azure Communications patří:

-  **Jednoduché** prostředí pro instalaci pro přidání funkce SMS do aplikací.
- Podpora zpráv s **vysokou rychlostí** prostřednictvím bezplatných telefonních čísel pro A2P (aplikace pro osobu) případy použití v USA.
- **Obousměrné** konverzace k podpoře scénářů, jako jsou zákaznické podpory, výstrahy a připomenutí událostí.
- **Spolehlivé doručování** se zprávami o doručení v reálném čase pro zprávy odesílané z vaší aplikace.
- **Analýza** ke sledování vašich způsobů využití a zapojení zákazníků.
- Podpora zpracování **výslovných** výjimek pro automatické zjišťování a respektování výslovných odsouhlasení s čísly bez poplatků. Výslovný souhlas s telefonními čísly pro USA – bezplatné počty jsou pověřené a vyžádané provozovateli USA.
  - ZASTAVIT – Pokud se příjemce textové zprávy chce odhlásit, může poslat "STOP" na bezplatné telefonní číslo. Přepravce pošle následující výchozí odpověď pro STOP: *"Síťová zpráva: odpověděli jste se slovem stop, který blokuje všechny texty odeslané z tohoto čísla. Vrátí text zpět, aby se zprávy znovu přijímaly.*
  - SPUSTIT/zrušit – pokud si chce příjemce znovu přihlásit k odběru textových zpráv z bezplatného čísla, může poslat "START" nebo "zrušit zastavení na bezplatné číslo. Přepravce pošle následující výchozí odpověď pro spuštění/zrušení zastavení: *"Síťová zpráva: odpověděli jste na zastavit a začne znovu přijímat zprávy od tohoto čísla."*
  - Služba Azure Communication Services zjistí zprávu o zastavení a zablokuje všem dalším zprávám příjemce. Zpráva o doručení indikuje neúspěšné doručení se stavovou zprávou "odesílatel zablokován pro daného příjemce".
  - Zprávy o zastavení, zastavení a spuštění budou předávány zpět na vás. Komunikační služby Azure vám pomohou monitorovat a implementovat tyto odhlašování, aby se zajistilo, že se žádné další pokusy o odeslání zprávy neodešlou na příjemce, kteří si nevyjádřili oznámení.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s odesíláním SMS](../../quickstarts/telephony-sms/send.md)

Následující dokumenty můžou být zajímavé:

- Seznamte se s [klientskou knihovnou SMS](../telephony-sms/sdk-features.md)
- Získání [telefonního čísla](../../quickstarts/telephony-sms/get-phone-number.md) s možností SMS
- [Typy telefonních čísel v komunikačních službách Azure](../telephony-sms/plan-solution.md)

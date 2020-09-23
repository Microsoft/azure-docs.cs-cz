---
title: Koncepty SMS v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o konceptech služby Communication Services pro SMS.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946853"
---
# <a name="sms-concepts"></a>Koncepty SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Služba Communications Services Azure umožňuje odesílat a přijímat textové zprávy SMS pomocí klientských knihoven služby Communication Services SMS. Tyto klientské knihovny lze použít k podpoře scénářů služeb zákazníkům, připomenutí schůzky, dvojúrovňového ověřování a dalších požadavků na komunikaci v reálném čase. Služba Communications Services SMS vám umožní spolehlivě odesílat zprávy a vystavovat přehledy o dodávkách a odpovědích na vaše kampaně.

Mezi klíčové funkce klientských knihoven SMS služby Azure Communications patří:

-  **Jednoduché** prostředí pro instalaci pro přidání funkce SMS do aplikací.
- Podpora zpráv s **vysokou rychlostí** prostřednictvím bezplatných telefonních čísel pro A2P (aplikace pro osobu) případy použití v USA.
- **Obousměrné** konverzace k podpoře scénářů, jako jsou zákaznické podpory, výstrahy a připomenutí událostí.
- **Spolehlivé doručování** se zprávami o doručení v reálném čase pro zprávy odesílané z vaší aplikace.
- **Analýza** ke sledování vašich způsobů využití a zapojení zákazníků.
- Podpora zpracování **výslovných** výjimek pro automatické zjišťování a respektování výslovných odsouhlasení s čísly bez poplatků. Komunikační služby rozpoznává zprávy o zastavení a spuštění a odesílají následující výchozí reakce koncovým uživatelům: 
  - STOP- *"úspěšně jste odhlásili odběr zpráv od tohoto čísla. Odpověď začíná znovu přihlašovat.*
  - SPUSTIT – *úspěšně jste se znovu přihlásili k odběru zpráv od tohoto čísla. Pro zrušení odběru odpovědi odpovězte. "*
  - Zprávy o zastavení a spuštění budou předávány zpět na vás. Komunikační služby Azure vám pomohou monitorovat a implementovat tyto možnosti, aby bylo zajištěno, že příjemcům, kteří se odhlásili od vaší komunikace, nebudou odesílány žádné další zprávy.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s odesíláním SMS](../../quickstarts/telephony-sms/send.md)

Následující dokumenty můžou být zajímavé:

- Seznamte se s [klientskou knihovnou SMS](../telephony-sms/sdk-features.md)
- Získání [telefonního čísla](../../quickstarts/telephony-sms/get-phone-number.md) s možností SMS
- [Plánování řešení SMS](../telephony-sms/plan-solution.md)

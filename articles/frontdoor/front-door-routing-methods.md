---
title: Přední dvířka Azure – metody směrování provozu | Microsoft Docs
description: Tento článek vám pomůže pochopit různé metody směrování provozu používané předními dvířky.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 2bc056620ff964747dfd83e7525cb5bfd2eb8e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449140"
---
# <a name="front-door-routing-methods"></a>Metody směrování front-dveří

Přední dvířka Azure podporují různé druhy metod směrování provozu, které určují, jak směrovat provoz HTTP/HTTPS do různých koncových bodů služby. Když si klient vyžádá připojení k frontě, použije se nakonfigurovaná Metoda směrování, aby se zajistilo, že se požadavky předají do nejlepší instance back-endu. 

Existují čtyři metody směrování provozu, které jsou k dispozici na front-dveřích:

* **[Latence](#latency):** Směrování na základě latence zajišťuje, aby se požadavky odesílaly do nejnižší latence back-endu přijatelné v rozsahu citlivosti. V podstatě se požadavky uživatelů odesílají do nejbližší sady back-endu v souvislosti s latencí sítě.
* **[Priorita](#priority):** Pokud chcete nakonfigurovat primární back-end pro provoz všech přenosů, můžete přiřadit priority k back-endu. Sekundární back-end může být záloha pro případ, že primární back-end nebude k dispozici.
* **[Vážená](#weighted):** Váhy můžete přiřadit k back-endu, pokud chcete distribuovat provoz napříč sadou back-endu. Bez ohledu na to, zda chcete rovnoměrně rozmístit nebo podle váhy.
* **[Spřažení relace](#affinity):** Můžete nakonfigurovat spřažení relace pro hostitele nebo domény front-endu, aby bylo zajištěno, že se požadavky od stejného koncového uživatele odesílají do stejného back-endu.

Všechny konfigurace služby Front Door zahrnují monitorování stavu back-endu a automatické okamžité globální převzetí služeb při selhání. Další informace najdete v tématu [monitorování back-endu na předních dveřích](front-door-health-probes.md). Vaše přední dveře můžou pracovat na základě jediné metody směrování. V závislosti na potřebách vaší aplikace můžete také kombinovat více metod směrování a vytvořit tak optimální topologii směrování.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Nejnižší provoz na základě latencí – směrování

Nasazení back-endu ve dvou nebo více umístěních po celém světě může zlepšit rychlost odezvy vašich aplikací směrováním provozu do cílového umístění, které je nejblíže koncovým uživatelům. Výchozí metoda směrování provozu pro konfiguraci front-dveří přesměruje požadavky od koncových uživatelů do nejbližšího back-endu prostředí front-endu, které obdrží požadavek. Tento přístup se v kombinaci s architekturou libovolného vysílání pro přední vrátka Azure zajišťuje tím, že každý z vašich koncových uživatelů získá maximální individuální přizpůsobení výkonu na základě jejich umístění.

Back-end není nutně nejbližší k měření geografickou vzdáleností. Místo toho určuje nejbližší back-endy na základě měření latence sítě. Přečtěte si další informace o [architektuře směrování front-dveří](front-door-routing-architecture.md). 

Níže je uveden celkový rozhodovací tok:

| Dostupná back-endy | Priorita | Signál latence (na základě sondy stavu) | Váhu |
|-------------| ----------- | ----------- | ----------- |
| Nejdřív vyberte všechny back-endy, které jsou povolené a vracené v pořádku (200 OK) pro sondu stavu. Pokud existuje šest back-endy a, B, C, D, E a F a mezi nimi není v pořádku a E je zakázaná. Seznam dostupných back-endu je A, B, D a F.  | V dalším kroku jsou vybrána nejvyšší priorita back-endy mezi dostupnými. Pokud má back-end A, B a D prioritu 1 a back-end F má prioritu 2. Vybrané back-endy pak budou a, B a D.| Vyberte back-endy s rozsahem latence (nejnižší citlivost & latence v MS). Pokud je back-end A 15 MS, B je 30 MS a 60 D v prostředí front-endu, kde se vyložila žádost, a citlivost latence je 30 ms, pak nejnižší fond latencí se skládá z back-endu a a B, protože D je více než 30 ms od nejbližšího back-endu, který je. | A konečně, přední dvířka dokončí dotazování provozu mezi konečným vybraným fondem back-endu v poměru určených vah. Řekněme, že pokud back-end A má váhu 5 a back-endu B s váhou 8, provoz se rozdělí v poměru 5:8 mezi back-endy a a B. |

>[!NOTE]
> Ve výchozím nastavení je vlastnost citlivosti na latenci nastavená na 0 MS, tj. vždycky předejte požadavek na nejrychlejší dostupný back-end.

## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Provoz založený na prioritách – směrování

Organizace často chce poskytovat vysokou dostupnost pro své služby nasazením více než jedné zálohovací služby pro případ, že primární verze bude ukončena. V celém odvětví se tato topologie označuje také jako aktivní/pohotovostní nebo aktivní/pasivní topologie nasazení. Metoda "Priorita" přenosu dat umožňuje zákazníkům Azure snadno implementovat tento vzor převzetí služeb při selhání.

Výchozí přední dveře obsahují seznam back-endu s stejnou prioritou. Ve výchozím nastavení odesílají přední dveře provoz pouze do back-endu nejvyšší priority (nejnižší hodnota priority), která je primární sadou back-endu. Pokud primární back-endy není k dispozici, přední dveře směrují provoz do sekundární sady back-endu (druhá nejnižší hodnota priority). Pokud primární i sekundární back-endy nejsou k dispozici, provoz směřuje na třetí atd. Dostupnost back-endu vychází z nakonfigurovaného stavu (povoleného nebo zakázaného) a stavu probíhajícího back-endu, který je stanovený sondou stavu.

### <a name="configuring-priority-for-backends"></a>Konfigurace priority pro back-endy

Každý back-end ve fondu back-end v konfiguraci frontových dveří má vlastnost s názvem priority, která může být číslo mezi 1 a 5. S předními dvířky Azure nakonfigurujete prioritu back-endu explicitně pomocí této vlastnosti pro každý back-end. Tato vlastnost je hodnota v rozmezí od 1 do 5. Nižší hodnoty reprezentují vyšší prioritu. Back-endy můžou sdílet prioritní hodnoty.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Vážená Metoda směrování provozu
Metoda "váženého" přenosu dat umožňuje distribuci provozu rovnoměrně nebo pro použití předem definovaného vážení.

V rámci vážené metody směrování provozu přiřadíte každému back-endu váhu v konfiguraci front back-endu. Váha je celé číslo od 1 do 1000. Tento parametr používá výchozí váhu "50".

Díky seznamu dostupných back-endy, které mají přijatelnou citlivost na latenci, se provoz distribuuje pomocí mechanizmu kruhového dotazování s využitím určeného poměru vah. Pokud se citlivost latence nastaví na 0 milisekund, tato vlastnost se neprojeví, pokud neexistují dvě back-endy se stejnou latencí sítě. 

Vážená metoda umožňuje několik užitečných scénářů:

* **Postupný upgrade aplikace**: poskytuje procentuální podíl provozu na nový back-end a postupně zvyšují zatížení v čase, aby se vynesla hodnota s jinými back-endy.
* **Migrace aplikace do Azure**: Vytvořte back-end fond s Azure i s externími back-endy. Upravte váhu back-endy tak, aby dávala nové back-endy. Tuto možnost můžete kdykoli nastavit počínaje tím, že zaškrtnete nové back-endy a pak jim přiřadíte nejnižší váhu, pomalu ji zvýšíte na úrovně, kde přebírají většinu provozu. Nakonec můžete zakázat méně upřednostňovaná back-endy a odebrat je z fondu.  
* Rozšiřování **cloudů pro další kapacitu**: rychlé rozšíření místního nasazení do cloudu tak, že ho umístíte za přední dveře. Pokud potřebujete dodatečnou kapacitu v cloudu, můžete přidat nebo povolit další back-endy a určit, jakou část provozu přechází na jednotlivé back-end.

## <a name="session-affinity"></a><a name = "affinity"></a>Spřažení relací
Ve výchozím nastavení bez spřažení relací předávají přední dveře požadavky pocházející ze stejného klienta na různé back-endy. Některé stavové aplikace nebo v určitých scénářích vyplývajících z žádostí stejného uživatele dávají přednost stejnému back-endu, který zpracoval původní požadavek. Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném back-endu. Pomocí spravovaných souborů cookie můžou přední dveře Azure směrovat provoz z uživatelské relace do stejného back-endu ke zpracování.

Spřažení relací je možné povolit na úrovni hostitele front-endu, to znamená pro všechny nakonfigurované domény (nebo subdomény). Po povolení přidá služba Front Door k relaci uživatele soubor cookie. Spřažení relací na základě souborů cookie umožňuje službě Front Door identifikovat různé uživatele i v případě, že jsou skryti za stejnou IP adresou. Díky tomu je možné rovnoměrněji distribuovat provoz mezi různé back-endy.

Doba života souboru cookie je stejná jako doba života relace uživatele, protože služba Front Door v současné době podporuje pouze soubor cookie relace. 

> [!NOTE]
> Veřejné proxy servery můžou kolidovat s spřažením relací. Důvodem je to, že vytvoření relace vyžaduje před přidáním souboru cookie spřažení relace do odpovědi, kterou nelze provést v případě, že je odpověď v mezipaměti, protože by došlo k poškození souborů cookie jiných klientů požadujících stejný prostředek. Pro zajištění ochrany proti **tomu nebude přidružení** relace navázáno, pokud back-end pošle odpověď s možnou odezvou, když se pokusí. Pokud již byla relace navázána, nezáleží na tom, zda je možné odpověď z back-endu ukládat do mezipaměti.
> Spřažení relace bude stanoveno za následujících okolností, **Pokud** odpověď nemá stavový kód HTTP 304:
> - Odpověď má nastavené konkrétní hodnoty pro ```Cache-Control``` záhlaví, které brání ukládání do mezipaměti, jako je například Private nebo No-Store.
> - Odpověď obsahuje ```Authorization``` záhlaví, které nevypršelo.
> - Odpověď má stavový kód HTTP 302.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

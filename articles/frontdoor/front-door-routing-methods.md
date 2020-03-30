---
title: Azure Front Door – metody směrování provozu | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit různé metody směrování provozu používané frontdoor
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 69ef68dafc2385eb5614179c3d04265250383104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471536"
---
# <a name="front-door-routing-methods"></a>Metody směrování předních dveří

Azure Front Door podporuje různé metody směrování provozu k určení, jak směrovat přenosy HTTP/HTTPS do různých koncových bodů služby. S každým z vašich požadavků klienta dosažení front door, nakonfigurovaná metoda směrování se použije k zajištění požadavky jsou předány do nejlepší back-end instance. 

V předních dveřích jsou k dispozici čtyři hlavní koncepty dopravních tras:

* ** [Latence](#latency):** Směrování založené na latenci zajišťuje, že požadavky jsou odesílány do back-endů s nejnižší latencí přijatelných v rozsahu citlivosti. V podstatě jsou vaše požadavky uživatelů odesílány do "nejbližší" sady back-endů s ohledem na latenci sítě.
* ** [Priorita](#priority):** Priority můžete přiřadit různým back-endům, pokud chcete použít primární back-end služby pro veškerý provoz, a poskytnout zálohy v případě, že primární nebo záložní back-endy nejsou k dispozici.
* ** [Vážený](#weighted):** Váhy můžete přiřadit různým back-endům, pokud chcete distribuovat provoz mezi sadu back-endů, a to buď rovnoměrně, nebo podle koeficientů hmotnosti.
* **Spřažení relace:** Spřažení relací pro front-endové hostitele nebo domény můžete nakonfigurovat, pokud chcete, aby následné požadavky od uživatele byly odeslány do stejného back-endu, dokud je relace uživatele stále aktivní a instance back-endu stále hlásí stav na základě sond stavu. 

Všechny konfigurace služby Front Door zahrnují monitorování stavu back-endu a automatické okamžité globální převzetí služeb při selhání. Další informace naleznete v tématu [Monitorování back-endu předních dveří](front-door-health-probes.md). Přední dveře lze nakonfigurovat tak, aby fungovaly na základě jedné metody směrování, a v závislosti na potřebách aplikace můžete použít více nebo všechny tyto metody směrování v kombinaci k vytvoření optimální topologie směrování.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Směrování provozu s nejnižší latencí na základě

Nasazení back-endů ve dvou nebo více umístěních po celém světě může zlepšit odezvu mnoha aplikací směrováním provozu do umístění, které je "nejblíže" koncovým uživatelům. Výchozí metoda směrování provozu pro konfiguraci frontových dveří předává požadavky od koncových uživatelů k nejbližšímu back-endu z prostředí Front Door, který požadavek obdržel. V kombinaci s architekturou Anycast azure front door tento přístup zajišťuje, že každý z vašich koncových uživatelů získat maximální výkon přizpůsobené na základě jejich umístění.

"Nejbližší" back-end není nutně nejbližší, měřeno podle zeměpisné vzdálenosti. Místo toho front door určuje nejbližší back-endy měřením latence sítě. Přečtěte si více o [architektuře směrování předních dveří](front-door-routing-architecture.md). 

Níže je uveden celkový tok rozhodnutí:

| Dostupné back-endy | Priorita | Signál latence (na základě sondy stavu) | Hmotnosti |
|-------------| ----------- | ----------- | ----------- |
| Nejprve vyberte všechny back-endy, které jsou povoleny a vráceny v pořádku (200 OK) pro sondu stavu. Řekněme, že existuje šest backends A, B, C, D, E a F a mezi nimi C je nezdravé a E je zakázáno. Takže seznam dostupných back-endů je A, B, D a F.  | Dále jsou vybrány back-endy s nejvyšší prioritou mezi dostupnými. Řekněme, back-end A, B a D mají prioritu 1 a back-end F má prioritu 2. Takže vybrané back-endy budou A, B a D.| Vyberte back-endy s rozsahem latence (nejmenší latence & citlivost latence v ms zadané). Řekněme, pokud A je 15 ms, B je 30 ms a D je 60 ms od prostředí předních dveří, kde požadavek přistál a citlivost latence je 30 ms, pak nejnižší latence fondu se skládá z back-endu A a B, protože D je více než 30 ms od nejbližšíback-end, který je A. | A konečně, Přední dveře zaokrouhlí robin provoz mezi konečným vybraným fondem back-endů v poměru zadaných hmotností. Řekněme, pokud back-end A má váhu 5 a back-end B má hmotnost 8, pak provoz bude distribuován v poměru 5:8 mezi backendy A a B. |

>[!NOTE]
> Ve výchozím nastavení je vlastnost citlivosti latence nastavena na 0 ms, to znamená, že vždy přepošlete požadavek nejrychlejšímu dostupnému back-endu.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Směrování provozu podle priority

Organizace chce často poskytovat spolehlivost svých služeb nasazením jedné nebo více služeb zálohování v případě, že jejich primární služba klesne. V celém odvětví se tato topologie označuje také jako topologie aktivního/pohotovostního režimu nebo aktivního/pasivního nasazení. Metoda směrování provozu "Priority" umožňuje zákazníkům Azure snadno implementovat tento vzor převzetí služeb při selhání.

Výchozí přední dveře obsahují seznam stejné priority back-endů. Ve výchozím nastavení front door odesílá provoz pouze do back-endů s nejvyšší prioritou (nejnižší hodnota pro prioritu), což je primární sada back-endů. Pokud primární back-endy nejsou k dispozici, front door směruje provoz na sekundární sadu back-endů (druhá nejnižší hodnota pro prioritu). Pokud primární i sekundární back-end nejsou k dispozici, provoz přejde na třetí a tak dále. Dostupnost back-endu je založena na nakonfigurovaném stavu (povoleném nebo zakázaném) a průběžném stavu back-endu určeném sondami stavu.

### <a name="configuring-priority-for-backends"></a>Konfigurace priority pro back-endy

Každý back-end ve vašem back-endfondu v rámci konfigurace front door má vlastnost s názvem "Priorita", což může být číslo mezi 1 a 5. S Azure Front Door nakonfigurujete prioritu back-endu explicitně pomocí této vlastnosti pro každý back-end. Tato vlastnost je hodnota mezi 1 a 5. Nižší hodnoty představují vyšší prioritu. Back-endy mohou sdílet hodnoty priority.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Metoda váženého směrování provozu
Metoda "Vážené" směrování provozu umožňuje rovnoměrně distribuovat provoz nebo použít předem definované vážení.

V metodě směrování vážených přenosů přiřadíte váhu každému back-endu v konfiguraci frontových dveří back-endového fondu. Váha je celé číslo od 1 do 1000. Tento parametr používá výchozí hmotnost "50".

Mezi seznam dostupných back-endů v rámci citlivosti přijaté latence (jak je uvedeno), provoz získá distribuovány v mechanismu kruhovédotazování v poměru váhy zadané. Pokud je citlivost latence nastavena na 0 milisekund, pak se tato vlastnost neprojeví, pokud neexistují dva back-endy se stejnou latencí sítě. 

Vážená metoda umožňuje některé užitečné scénáře:

* **Postupný upgrade aplikace**: Přidělte procento provozu na trasu do nového back-endu a postupně zvyšujte provoz v průběhu času, aby byl na stejné úrovni jako ostatní back-endy.
* **Migrace aplikací do Azure**: Vytvořte back-endový fond s Azure i externími back-endy. Upravte hmotnost back-endů tak, aby upřednostňovala nové back-endy. Můžete to postupně nastavit počínaje tím, že budou zakázány nové back-endy, pak jim přiřadíte nejnižší váhy a pomalu je zvýšíte na úroveň, kde berou největší provoz. Nakonec zakázat méně upřednostňované back-endy a jejich odebrání z fondu.  
* **Cloud-bursting pro další kapacitu:** Rychle rozšířit místní nasazení do cloudu tím, že ho za přední dveře. Když potřebujete další kapacitu v cloudu, můžete přidat nebo povolit další back-endy a určit, jaká část provozu bude pro každý back-end.

## <a name="session-affinity"></a><a name = "affinity"></a>Spřažení relace
Ve výchozím nastavení bez spřažení relací front door předává požadavky pocházející ze stejného klienta do různých back-endů na základě konfigurace vyrovnávání zatížení, zejména proto, že se změní latence různých back-endů nebo pokud se změní různé požadavky od stejného uživatel přistane na jiném prostředí předních dveří. Pro některé stavové aplikace nebo v určitých jiných scénářích je však žádoucí, aby další požadavky od stejného uživatele přicházely do stejného back-endu, který zpracoval počáteční požadavek. Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném back-endu. Pomocí front door spravované cookies, Azure Front Door můžete přímé následné provoz z relace uživatele do stejného back-endu pro zpracování tak dlouho, dokud back-end je v pořádku a relace uživatele nevypršela. 

Spřažení relací je možné povolit na úrovni hostitele front-endu, to znamená pro všechny nakonfigurované domény (nebo subdomény). Po povolení přidá služba Front Door k relaci uživatele soubor cookie. Spřažení relací na základě souborů cookie umožňuje službě Front Door identifikovat různé uživatele i v případě, že jsou skryti za stejnou IP adresou. Díky tomu je možné rovnoměrněji distribuovat provoz mezi různé back-endy.

Doba života souboru cookie je stejná jako doba života relace uživatele, protože služba Front Door v současné době podporuje pouze soubor cookie relace. 

> [!NOTE]
> Veřejné proxy servery mohou zasahovat do spřažení relace. Je to proto, že vytvoření relace vyžaduje, aby front door přidalsoubor cookie spřažení relace k odpovědi, což nelze provést, pokud je odpověď uložitelná do mezipaměti, protože by narušila soubory cookie jiných klientů požadujících stejný prostředek. Chcete-li chránit proti tomuto, spřažení relace **nebude** vytvořena, pokud back-end odešle odpověď, kterou lze uložit do mezipaměti, když se o to pokusíte. Pokud relace již byla vytvořena, nezáleží na tom, zda je odpověď z back-endu cache.
> Spřažení relací bude vytvořeno za následujících okolností, **pokud** odpověď nemá stavový kód HTTP 304:
> - Odpověď má specifické hodnoty ```Cache-Control``` nastavené pro záhlaví, které zabraňuje ukládání do mezipaměti, například "soukromé" nebo no-store".
> - Odpověď obsahuje ```Authorization``` záhlaví, jehož platnost nevypršela.
> - Odpověď má stavový kód HTTP 302.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

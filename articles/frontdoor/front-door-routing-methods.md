---
title: Služba Azure branou – metody směrování provozu | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit metody směrování provozu různých používané branou
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
ms.openlocfilehash: bd1278db43ba31ed78f13a826a330e16c3bc8d57
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820840"
---
# <a name="front-door-routing-methods"></a>Metody směrování branou

Služba Azure branou podporuje různé metody směrování provozu a zjistěte, jak směrovat provoz protokolu HTTP/HTTPS do různých koncových bodů služby. S každou žádostí klienta dosažení branou metodu směrování nakonfigurovanou uplatňovat Ujistěte se, že žádosti jsou předávány nejlepší instance back-endu. 

Ve vstupní brána k dispozici jsou čtyři hlavní koncepty, které směrování provozu:

* **[Latence](#latency):** Směrování na základě latence zajistí, že jsou odesílány požadavky na nejnižší latenci, back-EndY přijatelné rozsahu citlivosti. V podstatě uživatelských požadavků se posílají "nejbližší" sadu back-EndY s ohledem na latenci sítě.
* **[Priorita](#priority):** Váš různé back-end můžete přiřadit priority, pokud chcete použít primární služby back-endu pro veškerý provoz a poskytují zálohování v případě, že nejsou k dispozici primární nebo zálohování back-EndY.
* **[Váha](#weighted):** Váhu můžete přiřadit váš různé back-end, pokud chcete za účelem distribuce provozu mezi sadu back-EndY, rovnoměrně nebo podle váhy koeficienty.
* **Spřažení relace:** Můžete nakonfigurovat spřažení pro front-endu hostitelů nebo doménách, pokud chcete, následné žádosti od uživatele se odesílají do stejného back-endu, dokud je pořád aktivní relaci uživatele ukončí a instance back-endu stále hlásí v pořádku podle sond stavu relace. 

Všechny konfigurace služby Front Door zahrnují monitorování stavu back-endu a automatické okamžité globální převzetí služeb při selhání. Další informace najdete v tématu [branou back-endu monitorování](front-door-health-probes.md). Vaše vstupní brána lze nakonfigurovat buď pro práci na základě toho, jedinou metodu směrování a v závislosti na vaší aplikace potřebuje slouží několik nebo všechny tyto metody směrování v kombinaci vytvářet optimální směrování topologie.

## <a name = "latency"></a>Směrování provozu na základě nejnižší latence

Nasazení back-endy ve dvou nebo více míst po celém světě můžete rychlejší odezvu u mnoha aplikací můžete provoz nasměrovat na umístění, která je nejblíž koncovým uživatelům. Výchozí metody směrování provozu pro vaši konfiguraci branou předává požadavky koncovým uživatelům na nejbližší back-end z prostředí branou, který přijal požadavek. V kombinaci s architekturou Anycast branou služby Azure, tento přístup zajišťuje, že každý koncovým uživatelům získat maximální výkon individuální na základě jejich umístění.

"Nejbližší" back-end není nutně co nejblíž koncovým měřený podle geografické vzdálenosti. Místo toho branou určuje nejbližší back-EndY, na základě měření latence sítě. Další informace o [architekturu směrování přední dveře](front-door-routing-architecture.md). 

Následuje Celkový tok rozhodnutí:

| K dispozici back-EndY | Priorita | Latence signál (podle sonda stavu) | Váhy |
|-------------| ----------- | ----------- | ----------- |
| Za prvé, vyberte všechny back-endů, které jsou povolené a vráceny v pořádku (200 OK) pro sondu stavu. Řekněme, existuje šest back-EndY A, B, C, D, E, F a a mezi nimi C není v pořádku a je zakázaný E. Takže seznam dostupných back-EndY je A, B, D a F.  | Dále jsou vybrané back-EndY nejvyšší prioritu mimo těch, které jsou k dispozici. Řekněme back-endu A, B a D priority 1 a back-endu F obsahující s prioritou 2. Tak bude vybraný back-EndY A B a D.| Vyberte back-end s latencí rozsah (nejnižší latenci & citlivosti čekací doba v ms zadaný). Například pokud je 15 ms, je 30 ms B a D je 60 ms od branou prostředí, kde žádost dostali a citlivosti čekací doba je 30 ms, pak nejnižší latenci fondu se skládá z back-endu A a B, protože D je nad rámec 30 ms od nejbližší back-end, který je A. | A konečně branou se kruhové dotazování provoz mezi konečné vybraný fond back-EndY poměr váhy zadané. Řekněme, pokud má back-endu A váhu 5 a back-endu B má váhu 8, pak provoz distribuovány v poměru 5:8 mezi back-EndY A a B. |

>[!NOTE]
> Ve výchozím nastavení vlastnost citlivosti latence nastavena na 0 ms, to znamená, vždy předat požadavek na nejrychlejší k dispozici back-endu.


## <a name = "priority"></a>Směrování provozu na základě priority

Organizace často chce zajistit spolehlivost pro své služby a nasadit jeden nebo více služeb zálohování v případě, že jejich primární služba přestane fungovat. Mezi odvětvími Tato topologie se také označuje jako aktivní/pohotovostní nebo topologii nasazení aktivní/pasivní vysokou dostupnost. Metody směrování provozu "Priority" umožňuje zákazníkům Azure snadno implementace tohoto modelu převzetí služeb při selhání.

Výchozí branou obsahuje seznam back-EndY stejnou prioritu. Ve výchozím nastavení, odešle branou provoz pouze do back-EndY nejvyšší prioritu (nejnižší hodnotou priority) to znamená, že primární sadu back-EndY. Pokud nejsou k dispozici primární back-EndY, branou provoz směruje do sekundární sadu back-EndY (druhou nejnižší hodnotou priority). Pokud primární a sekundární EndY nejsou k dispozici, provoz směrován na třetí a tak dále. Dostupnost služby back-end je založená na nakonfigurované stav (povoleno nebo zakázáno) a sondy stavu probíhající back-endu podle stavu.

### <a name="configuring-priority-for-backends"></a>Konfigurace priority pro back-EndY

Každá z back-endy ve vašem fondu back-endu v rámci konfigurace branou obsahuje vlastnost s názvem "Priority", což může být číslo mezi 1 a 5. S branou služby Azure nakonfigurujte back-endu prioritu explicitně pomocí této vlastnosti pro každý back-end. Tato vlastnost je hodnota mezi 1 a 5. Nižší hodnoty, tím vyšší priorita. Back-EndY můžou sdílet hodnoty priority.

## <a name = "weighted"></a>Metody váženého směrování provozu
Metody směrování provozu 'Váženého' umožňuje rovnoměrně distribuovat provoz nebo použít předem definované váhu.

V metody váženého směrování provozu můžete přiřadit váhu jednotlivých back-endu v konfiguraci branou back-endového fondu. Váha je celé číslo od 1 do 1000. Tento parametr používá výchozí tloušťka '50'.

Mezi seznamu k dispozici back-endů v rámci přijatelných latence citlivosti (jak je uvedeno) získá distribuovat provoz do kruhové dotazování mechanismu poměr váhy zadané. Pokud latence citlivosti je nastavena na 0 milisekund, pak tato vlastnost se projeví pouze v případě dva back-endů pomocí stejné latence sítě. 

Metody váženého umožňuje některé užitečné scénáře:

* **Postupné zavádění upgradu**: Přidělení procenta provoz směrovat na nový back-end a postupně zvyšovat provoz tak, aby pamětích jiných back-EndY.
* **Migrace aplikací do Azure**: Vytvořte fond back-end s Azure a externí back-EndY. Nastavte váhu back-EndY preferovat nový back-EndY. Můžete postupně nastavit to počínaje nový back-EndY zakázáno, s přiřazením je nejnižší váhy pomalu zvýšení úrovně, kde přebírají největším provozem. Potom nakonec zakázání méně upřednostňované back-EndY a odebírá z fondu.  
* **Shlukování cloudu pro další kapacitu**: Vložením za branou rychle rozbalte místní nasazení do cloudu. Pokud potřebujete víc kapacity v cloudu, můžete přidat nebo povolit další back-EndY a určit, jaká část provoz směrován na každý back-end.

## <a name = "affinity"></a>Spřažení relace
Ve výchozím nastavení, aniž by spřažení relace na branou směruje požadavky pocházejí ze stejného klienta na různé back-EndY podle zvlášť, jak změnit latenci na různé back-EndY, nebo pokud konfiguraci Vyrovnávání zatížení různé požadavky od stejného uživatele území na jiné prostředí branou. Pro některé stavové aplikace nebo v určitých jiných scénářích je však žádoucí, aby další požadavky od stejného uživatele přicházely do stejného back-endu, který zpracoval počáteční požadavek. Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném back-endu. Pomocí souborů cookie spravovaných branou branou služby Azure může směrovat následný provoz z uživatelské relace do stejného back-endu ke zpracování jako back-end je v pořádku a nevypršela platnost relace uživatele. 

Spřažení relací je možné povolit na úrovni hostitele front-endu, to znamená pro všechny nakonfigurované domény (nebo subdomény). Po povolení přidá služba Front Door k relaci uživatele soubor cookie. Spřažení relací na základě souborů cookie umožňuje službě Front Door identifikovat různé uživatele i v případě, že jsou skryti za stejnou IP adresou. Díky tomu je možné rovnoměrněji distribuovat provoz mezi různé back-endy.

Doba života souboru cookie je stejná jako doba života relace uživatele, protože služba Front Door v současné době podporuje pouze soubor cookie relace. 

> [!NOTE]
> Veřejné servery proxy může kolidovat s spřažení relace. Je to proto, že vytvoření relace vyžaduje vstupní brána k přidání souboru cookie spřažení relace na odpověď, nejde provést, pokud odpověď je možné ukládat do mezipaměti, protože by to narušit soubory cookie z jiných klientů, žádosti o stejný prostředek. Abyste tomu předešli, spřažení relace bude **není** navázat back-endu odešle odpověď možné ukládat do mezipaměti při pokusu o to. Pokud relace už bylo navázáno, není důležitá, pokud odpověď z back-end je možné ukládat do mezipaměti.
> V následujících případech se vytvoří spřažení relace na **Pokud** odpověď má kód stavu HTTP 304:
> - Odpověď má nastavit pro konkrétní hodnoty ```Cache-Control``` záhlaví, které zabrání ukládání do mezipaměti, jako je například "privátní" nebo no-store ".
> - Odpověď obsahuje ```Authorization``` hlavičku, která nevypršela platnost.
> - Odpověď má kód stavu HTTP 302.

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).

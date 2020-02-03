---
title: Příprava dat pro ML Studio (Classic) – vědecký proces týmového zpracování dat
description: Předběžně zpracovat a vyčistit data, aby byl připravený k dá efektivně využít pro machine learning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720040"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Úlohy k přípravě dat pro rozšířené strojové učení
Data před zpracováním a čištěním jsou důležité úkoly, které je nutné provést před tím, než bude možné použít datovou sadu pro školení modelů. Nezpracovaná data se často aktivní nebo nespolehlivé a může být chybějící hodnoty. Pomocí těchto dat pro modelování můžete vytvářet zavádějící výsledky. Tyto úlohy jsou součástí nástroje zpracování týmových dat vědy (TDSP) a obvykle postupujte podle počáteční zkoumání použít ke zjištění a plánování předběžného zpracování požadované datové sady. Podrobnější pokyny k procesu TDSP najdete v krocích uvedených v rámci [vědeckého procesu týmového zpracování dat](overview.md).

Úlohy předběžného zpracování a čištění, jako je třeba úloha zkoumání dat, se dají provádět v nejrůznějších prostředích, jako je SQL nebo podregistr nebo Azure Machine Learning Studio (Classic), a s různými nástroji a jazyky, jako je R nebo Python, v závislosti na tom, kde se data je uložený a jak je formátovaný. Protože je iterativní ze své podstaty TDSP, tyto úlohy může proběhnout na různé kroky v procesu pracovního postupu.

V tomto článku se seznámíte s různými koncepty zpracování dat a úlohami, které je možné provést před nebo po ingestování dat do Azure Machine Learning Studio (Classic).

Příklad zkoumání a předběžného zpracování dat v Azure Machine Learning Studio (Classic) najdete v tématu [předběžné zpracování datových](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videí.

## <a name="why-pre-process-and-clean-data"></a>Proč předběžně zpracovat a vyčistit data?
Shromáždění dat reálného světa z různých zdrojů a procesy a může obsahovat nesrovnalosti nebo poškozená data tím bylo narušeno kvalitu datové sady. Problémy s kvalitou typické dat, které vznikají jsou:

* **Nedokončeno**: data postrádá atributy nebo obsahují chybějící hodnoty.
* **Vysokou úroveň šumu**: data obsahují chybné záznamy nebo odlehlé hodnoty.
* **Nekonzistentní**: data obsahují konfliktní záznamy nebo nesrovnalosti.

Kvalita dat je předpokladem pro kvalitu prediktivních modelů. Vyhněte se "uvolňování paměti v uvolňování paměti na" a zlepšovat kvalitu dat a proto modelování výkonu, je nutné provádět obrazovku stavu dat pro přímé problémy datového již v rané fázi a rozhodnout o odpovídající zpracovávaných dat a čisticí kroky.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Co jsou některé typické data stavu obrazovky, které se použijí?
Obecné kvality dat můžete zkontrolovat tak, že zkontrolujete:

* Počet **záznamů**.
* Počet **atributů** (nebo **funkcí**).
* **Typy dat** atributů (nominální, ordinální nebo průběžné).
* Počet **chybějících hodnot**.
* Data **ve správném formátu** .
  * Pokud jsou data v TSV nebo sdíleného svazku clusteru, zkontrolujte, že oddělovače sloupců a oddělovače řádků vždy správně oddělení sloupců a řádků.
  * Pokud jsou data ve formátu HTML nebo XML, zkontrolujte, zda data je ve správném závislosti na jejich příslušné standardy.
  * Analýza kódu může být také nutné k extrakci strukturovaných informací z částečně strukturovaná nebo Nestrukturovaná data.
* **Nekonzistentní datové záznamy**. Kontroly rozsahu hodnot jsou povoleny. Pokud například data obsahují dohody o zakázkách studenta (průměr bodu úrovně), podívejte se, jestli je v určeném rozsahu údaj GPA, řekněme 0 ~ 4.

Když najdete problémy s daty, jsou nezbytné **kroky zpracování** , což často zahrnuje čištění chybějících hodnot, normalizaci dat, nalezených diskretizační, zpracování textu pro odebrání nebo nahrazení vložených znaků, které mohou ovlivnit zarovnání dat, smíšené datové typy v běžných polích a další.

**Azure Machine Learning spotřebovává tabulková data ve správném formátu**.  Pokud jsou data již v tabulkovém formátu, lze předběžné zpracování dat provést přímo s Azure Machine Learning Studio (Classic) v Machine Learning.  Pokud data nejsou ve formě tabulky, Řekněme, který je ve formátu XML, analýza kódu může být potřeba, abyste mohli data převést do formátu tabulky.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jaké jsou některé z hlavních úloh v předběžné zpracování dat?
* **Čištění dat**: Vyplňte chybějící hodnoty, vyhledáte a odeberte data o hlučnosti a odlehlé hodnoty.
* **Transformace dat**: normalizuje data pro snížení rozměrů a šumu.
* **Redukce dat**: ukázkové záznamy dat nebo atributy pro snazší zpracování dat.
* **Data nalezených diskretizační**: převod souvislých atributů na atributy kategorií pro snadné použití s určitými metodami strojového učení.
* **Čištění textu**: odstranit vložené znaky, které mohou způsobit chybné zarovnání dat, například vložené karty v datovém souboru odděleném tabulátory, vložené nové řádky, které mohou přerušit záznamy, například.

Následující části podrobně popisují některé z těchto kroků zpracování dat.

## <a name="how-to-deal-with-missing-values"></a>Jak zacházet s chybějící hodnoty?
Vypořádat s chybějícími hodnotami, doporučujeme nejdřív zjistit příčinu pro chybějící hodnoty pro lepší zvládání problém. Typické chybí hodnota zpracování metody jsou následující:

* **Odstranění**: odebrání záznamů s chybějícími hodnotami
* **Fiktivní substituce**: Nahraďte chybějící hodnoty zástupnou hodnotou: například *Unknown* pro kategorií nebo 0 pro číselné hodnoty.
* **Střední náhrada**: Pokud jsou chybějící data číselná, nahraďte chybějící hodnoty průměrem.
* **Častá náhrada**: Pokud jsou chybějící data kategorií, nahraďte chybějící hodnoty nejčastější položkou
* **Nahrazování regrese**: pomocí regresní metody nahraďte chybějící hodnoty hodnotami vrácenými hodnotami.  

## <a name="how-to-normalize-data"></a>Jak k normalizaci dat?
Normalizace dat mění škálované číselné hodnoty na zadaný rozsah. Mezi oblíbenými datovými normalizace metody patří:

* **Min-max normalizace**: lineárně transformuje data do rozsahu, například 0 až 1, kde je minimální hodnota škálovaná na 0 a maximální hodnota na 1.
* **Normalizace skóre**v: Škálujte data na základě středníku a směrodatné odchylky: rozdíl mezi daty a průměrem podle směrodatné odchylky.
* **Desetinné měřítko**: Škálujte data posunutím desetinné čárky hodnoty atributu.  

## <a name="how-to-discretize-data"></a>Jak k diskretizaci dat?
Data můžete diskretizovat převedením průběžné hodnoty na nominální atributy nebo intervalech. Jsou některé způsoby, jak to provést:

* **Binningu se stejnou šířkou**: rozdělte rozsah všech možných hodnot atributu na N skupiny stejné velikosti a přiřaďte hodnoty, které spadají do přihrádky s číslem přihrádky.
* **Binningu se stejnou výškou**: rozdělte rozsah všech možných hodnot atributu do skupin N, z nichž každý obsahuje stejný počet instancí, a potom přiřaďte hodnoty, které spadají do přihrádky s číslem přihrádky.  

## <a name="how-to-reduce-data"></a>Jak dosáhnout snížení dat?
Existují různé metody pro snížení velikosti dat pro snadnější práci s daty. V závislosti na velikosti dat a doméně můžete použít následující metody:

* **Vzorkování záznamu**: vzorkování datových záznamů a výběr pouze reprezentativní podmnožiny dat.
* **Vzorkování atributů**: z dat vyberte pouze podmnožinu nejdůležitějších atributů.  
* **Agregace**: rozdělte data do skupin a uložte čísla pro každou skupinu. Měsíční výnosy ke snížení množství dat může například agregovat denní počty výnosy řetězec restaurací v průběhu posledních 20 let.  

## <a name="how-to-clean-text-data"></a>Jak vyčistit textová data?
**Textová pole v tabulkových datech** můžou obsahovat znaky, které ovlivňují zarovnání sloupců nebo hranice záznamů. Například vložené tabulátory v souboru odděleném tabulátory způsobí chybné zarovnání sloupce a vložené znaky nového řádku zalomit řádky záznamu. Nesprávné zpracování kódování textu při psaní nebo čtení textu vede k úniku informací, nechtěnému zavedení nečitelných znaků (jako jsou hodnoty null) a může také ovlivnit analýzu textu. Pozor, analýze a úpravy může být potřeba, abyste mohli vyčistit textová pole pro správné zarovnání a/nebo k extrahování strukturovaná data z textu nestrukturovaná nebo semistrukturovaná data.

**Zkoumání dat** nabízí včasnou pohled na data. Počet problémů dat může nepokrytý během tohoto kroku a odpovídající metody lze použít pro vyřešení těchto problémů.  Je důležitá klást otázky, jako jsou novinky příčinu problému a jak tento problém může vzniknout. Tento proces také pomáhá při rozhodování o krocích zpracování dat, které je třeba provést, aby je bylo možné vyřešit. Určením konečných případů použití a osoby lze také použít k určení priorit při zpracování dat.

## <a name="references"></a>Odkazy
> *Dolování dat: koncepce a techniky*, třetí edice, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber a Jian Pei
> 
> 


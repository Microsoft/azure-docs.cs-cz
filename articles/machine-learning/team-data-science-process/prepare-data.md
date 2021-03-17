---
title: Příprava dat pro ML Studio (Classic) – vědecký proces týmového zpracování dat
description: Předzpracování a vyčištění dat, která připravují, aby se pro strojové učení efektivně používala.
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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012403"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Úlohy k přípravě dat pro rozšířené strojové učení
Data před zpracováním a čištěním jsou důležité úkoly, které je nutné provést před tím, než bude možné použít datovou sadu pro školení modelů. Nezpracovaná data jsou často v nespolehlivém a nespolehlivá a můžou mít chybějící hodnoty. Použití takových dat pro modelování může způsobit zavádějící výsledky. Tyto úlohy jsou součástí vědeckého procesu týmového zpracování dat (TDSP) a obvykle se řídí počátečním zkoumáním datové sady, která se používá ke zjišťování a naplánování požadovaných předběžného zpracování. Podrobnější pokyny k procesu TDSP najdete v krocích uvedených v rámci [vědeckého procesu týmového zpracování dat](overview.md).

Úlohy předběžného zpracování a čištění, jako je třeba úloha zkoumání dat, se dají provádět v nejrůznějších prostředích, jako je SQL nebo podregistr nebo Azure Machine Learning Studio (Classic), a s různými nástroji a jazyky, jako je R nebo Python, v závislosti na tom, kde jsou data uložená a jak se dají formátovat. Vzhledem k tomu, že TDSP je iterativní v podstatě, můžou tyto úlohy probíhat v různých krocích pracovního postupu procesu.

V tomto článku se seznámíte s různými koncepty zpracování dat a úlohami, které je možné provést před nebo po ingestování dat do Azure Machine Learning Studio (Classic).

Příklad zkoumání a předběžného zpracování dat v Azure Machine Learning Studio (Classic) najdete v tématu [předběžné zpracování datových](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videí.

## <a name="why-pre-process-and-clean-data"></a>Proč předběžně zpracovat a vyčistit data?
Data z reálného světa se shromažďují z různých zdrojů a procesů a můžou obsahovat nedovolené nebo poškozené údaje, které mají vliv na kvalitu datové sady. K běžným problémům s kvalitou dat dojde:

* **Nedokončeno**: data postrádá atributy nebo obsahují chybějící hodnoty.
* **Vysokou úroveň šumu**: data obsahují chybné záznamy nebo odlehlé hodnoty.
* **Nekonzistentní**: data obsahují konfliktní záznamy nebo nesrovnalosti.

Data kvality jsou předpokladem pro prediktivní modely kvality. Aby se zabránilo "uvolňování paměti", vystavování paměti a zlepšení kvality dat, a tím i modelu výkonu, je nezbytné provést obrazovku stavu dat, aby se včas zobrazovaly problémy s daty, a rozhodnout o odpovídajících krocích zpracování a čištění dat.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Jaké jsou některé typické obrazovky stavu dat, které jsou zaměstnané?
Pomocí kontroly můžete zkontrolovat obecnou kvalitu dat:

* Počet **záznamů**.
* Počet **atributů** (nebo **funkcí**).
* **Typy dat** atributů (nominální, ordinální nebo průběžné).
* Počet **chybějících hodnot**.
* Data **ve správném formátu** .
  * Pokud jsou data v souboru TSV nebo CSV, ověřte, že oddělovače sloupců a oddělovače řádků vždycky správně oddělují sloupce a řádky.
  * Pokud jsou data ve formátu HTML nebo XML, ověřte, jestli jsou data ve správném formátu na základě příslušných standardů.
  * Pro extrakci strukturovaných informací z částečně strukturovaných nebo nestrukturovaných dat může být potřeba analyzovat i analýzu.
* **Nekonzistentní datové záznamy**. Kontroluje rozsah hodnot, které jsou povoleny. Pokud například data obsahují dohody o zakázkách studenta (průměr bodu úrovně), podívejte se, jestli je v určeném rozsahu údaj GPA, řekněme 0 ~ 4.

Když najdete problémy s daty, jsou nezbytné **kroky zpracování** , což často zahrnuje čištění chybějících hodnot, normalizaci dat, nalezených diskretizační, zpracování textu pro odebrání nebo nahrazení vložených znaků, které mohou ovlivnit zarovnání dat, smíšené datové typy v běžných polích a další.

**Azure Machine Learning spotřebovává tabulková data ve správném formátu**.  Pokud jsou data již v tabulkovém formátu, lze předběžné zpracování dat provést přímo s Azure Machine Learning Studio (Classic) v Machine Learning.  Pokud data nejsou v tabulkovém formátu, řekněme, že je v kódu XML, analýza je možná nutná k převedení dat do tabulkového formátu.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jaké jsou některé z hlavních úloh předběžného zpracování dat?
* **Čištění dat**: Vyplňte chybějící hodnoty, vyhledáte a odeberte data o hlučnosti a odlehlé hodnoty.
* **Transformace dat**: normalizuje data pro snížení rozměrů a šumu.
* **Redukce dat**: ukázkové záznamy dat nebo atributy pro snazší zpracování dat.
* **Data nalezených diskretizační**: převod souvislých atributů na atributy kategorií pro snadné použití s určitými metodami strojového učení.
* **Čištění textu**: odstranit vložené znaky, které mohou způsobit chybné zarovnání dat, například vložené karty v datovém souboru odděleném tabulátory, vložené nové řádky, které mohou přerušit záznamy, například.

Následující části podrobně popisují některé z těchto kroků zpracování dat.

## <a name="how-to-deal-with-missing-values"></a>Jak se zabývat chybějícími hodnotami?
Chcete-li se zabývat chybějícími hodnotami, je nejlepší určit důvod chybějících hodnot pro lepší zpracování problému. Typické metody zpracování chybějících hodnot jsou:

* **Odstranění**: odebrání záznamů s chybějícími hodnotami
* **Fiktivní substituce**: Nahraďte chybějící hodnoty zástupnou hodnotou: například *Unknown* pro kategorií nebo 0 pro číselné hodnoty.
* **Střední náhrada**: Pokud jsou chybějící data číselná, nahraďte chybějící hodnoty průměrem.
* **Častá náhrada**: Pokud jsou chybějící data kategorií, nahraďte chybějící hodnoty nejčastější položkou
* **Nahrazování regrese**: pomocí regresní metody nahraďte chybějící hodnoty hodnotami vrácenými hodnotami.  

## <a name="how-to-normalize-data"></a>Jak normalizovat data?
Normalizace dat mění škálované číselné hodnoty na zadaný rozsah. Mezi oblíbené metody normalizace dat patří:

* **Min-max normalizace**: lineárně transformuje data do rozsahu, například 0 až 1, kde je minimální hodnota škálovaná na 0 a maximální hodnota na 1.
* **Normalizace skóre** v: Škálujte data na základě středníku a směrodatné odchylky: rozdíl mezi daty a průměrem podle směrodatné odchylky.
* **Desetinné měřítko**: Škálujte data posunutím desetinné čárky hodnoty atributu.  

## <a name="how-to-discretize-data"></a>Jak diskretizaci data?
Data je možné diskretizovaný převodem souvislých hodnot na nominální atributy nebo intervaly. To je několik způsobů, jak to provést:

* **Binningu se stejnou šířkou**: rozdělte rozsah všech možných hodnot atributu na N skupiny stejné velikosti a přiřaďte hodnoty, které spadají do přihrádky s číslem přihrádky.
* **Binningu se stejnou výškou**: rozdělte rozsah všech možných hodnot atributu do skupin N, z nichž každý obsahuje stejný počet instancí, a potom přiřaďte hodnoty, které spadají do přihrádky s číslem přihrádky.  

## <a name="how-to-reduce-data"></a>Jak snížit data?
K dispozici jsou různé metody pro snížení velikosti dat pro snazší zpracování dat. V závislosti na velikosti dat a doméně lze použít následující metody:

* **Vzorkování záznamu**: vzorkování datových záznamů a výběr pouze reprezentativní podmnožiny dat.
* **Vzorkování atributů**: z dat vyberte pouze podmnožinu nejdůležitějších atributů.  
* **Agregace**: rozdělte data do skupin a uložte čísla pro každou skupinu. Například denní objemy tržeb řetězce restaurace za posledních 20 let se dají agregovat k měsíčním výnosům, aby se snížila velikost dat.  

## <a name="how-to-clean-text-data"></a>Jak vyčistit textová data?
**Textová pole v tabulkových datech** můžou obsahovat znaky, které ovlivňují zarovnání sloupců nebo hranice záznamů. Například vložené tabulátory v souboru odděleném tabulátory způsobí chybné zarovnání sloupce a vložené znaky nového řádku zalomit řádky záznamu. Nesprávné zpracování kódování textu při psaní nebo čtení textu vede k úniku informací, nechtěnému zavedení nečitelných znaků (jako jsou hodnoty null) a může také ovlivnit analýzu textu. Aby bylo možné vyčistit textová pole ke správnému zarovnání nebo extrakci strukturovaných dat z nestrukturovaných nebo částečně strukturovaných textových dat, může být nutné provést pečlivou analýzu a úpravy.

**Zkoumání dat** nabízí včasnou pohled na data. V průběhu tohoto kroku může být zjištěna řada problémů s daty a je možné použít odpovídající metody pro řešení těchto problémů.  Je důležité klást otázky, jako je například zdroj problému a způsob, jakým je možné problém zavádět. Tento proces také pomáhá při rozhodování o krocích zpracování dat, které je třeba provést, aby je bylo možné vyřešit. Určením konečných případů použití a osoby lze také použít k určení priorit při zpracování dat.

## <a name="references"></a>Reference
> *Dolování dat: koncepce a techniky*, třetí edice, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber a Jian Pei
> 
> 


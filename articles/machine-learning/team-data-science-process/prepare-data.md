---
title: Čištění a příprava dat pro Azure Machine Learning – vědecké zpracování týmových dat
description: Předběžně zpracovat a vyčistit data, aby byl připravený k dá efektivně využít pro machine learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a926edc3409290a0e8cd89fd909427833f9e1427
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134365"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Úlohy k přípravě dat pro rozšířené strojové učení
Předběžné zpracování a čištění dat jsou důležité úkoly, které obvykle musí být provedeny před datové sady se dá efektivně využít pro machine learning. Nezpracovaná data se často aktivní nebo nespolehlivé a může být chybějící hodnoty. Pomocí těchto dat pro modelování můžete vytvářet zavádějící výsledky. Tyto úlohy jsou součástí nástroje zpracování týmových dat vědy (TDSP) a obvykle postupujte podle počáteční zkoumání použít ke zjištění a plánování předběžného zpracování požadované datové sady. Další podrobné pokyny týkající se procesu TDSP, najdete v článku podle kroků uvedených v [vědecké zpracování týmových dat](overview.md).

Předběžné zpracování a čištění úkoly, jako je úloha zkoumání dat, lze provádět v nejrůznějších prostředích, jako je SQL nebo Hive nebo Azure Machine Learning Studio a různé nástroje a jazyky, jako je R nebo Python, v závislosti, kde se vaše data uložená a jak je formátováno. Protože je iterativní ze své podstaty TDSP, tyto úlohy může proběhnout na různé kroky v procesu pracovního postupu.

Tento článek představuje různé koncepty zpracování dat a úlohy, které mohou být provedeny před nebo po ingestovat data do Azure Machine Learning.

Příklad zkoumání dat a předběžné zpracování v rámci Azure Machine Learning studio, najdete v článku [předběžného zpracování dat v Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videa.

## <a name="why-pre-process-and-clean-data"></a>Proč předběžně zpracovat a vyčistit data?
Shromáždění dat reálného světa z různých zdrojů a procesy a může obsahovat nesrovnalosti nebo poškozená data tím bylo narušeno kvalitu datové sady. Problémy s kvalitou typické dat, které vznikají jsou:

* **Neúplné**: Data neobsahují atributy nebo obsahují chybějící hodnoty.
* **Hlučného**: Data obsahují chybné záznamy nebo odlehlé hodnoty.
* **Nekonzistentní**: Data obsahují konfliktní záznamy nebo nedostatky.

Kvalita dat je předpokladem pro kvalitu prediktivních modelů. Vyhněte se "uvolňování paměti v uvolňování paměti na" a zlepšovat kvalitu dat a proto modelování výkonu, je nutné provádět obrazovku stavu dat pro přímé problémy datového již v rané fázi a rozhodnout o odpovídající zpracovávaných dat a čisticí kroky.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Co jsou některé typické data stavu obrazovky, které se použijí?
Obecné kvality dat můžete zkontrolovat tak, že zkontrolujete:

* Počet **záznamy**.
* Počet **atributy** (nebo **funkce**).
* Atribut **datové typy** (nominální, pořadí nebo průběžné).
* Počet **chybějící hodnoty**.
* **Správnosti** data.
  * Pokud jsou data v TSV nebo sdíleného svazku clusteru, zkontrolujte, že oddělovače sloupců a oddělovače řádků vždy správně oddělení sloupců a řádků.
  * Pokud jsou data ve formátu HTML nebo XML, zkontrolujte, zda data je ve správném závislosti na jejich příslušné standardy.
  * Analýza kódu může být také nutné k extrakci strukturovaných informací z částečně strukturovaná nebo Nestrukturovaná data.
* **Nekonzistentní záznamy**. Kontroly rozsahu hodnot jsou povoleny. Například pokud data obsahují GPA studentů, zkontrolujte, jestli je GPA v oblasti určené 0 vyslovit ~ 4.

Pokud narazíte na problémy s daty, **kroky zpracování** jsou nezbytné, což často zahrnuje vyčištění chybějících hodnot, data normalizace, diskretizace, zpracování textu můžete odebrat nebo nahradit vložené znaky, které může mít vliv na data v běžných zarovnání, různé datové typy, pole a další.

**Využívá Azure Machine Learning ve správném formátu tabulková data**.  Pokud už data ve formě tabulky, předběžného zpracování dat můžete provést přímo s Azure Machine Learning v nástroji Machine Learning Studio.  Pokud data nejsou ve formě tabulky, Řekněme, který je ve formátu XML, analýza kódu může být potřeba, abyste mohli data převést do formátu tabulky.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jaké jsou některé z hlavních úloh v předběžné zpracování dat?
* **Čištění dat**: vyplnit nebo chybějící hodnoty, zjistit a odebrat hlučného dat a odlehlé hodnoty.
* **Transformace dat**: normalizovat data a snižuje tak dimenzí a šumu.
* **Redukce dat**: ukázkový atributy pro snazší zpracování dat nebo datových záznamů.
* **Data diskretizace**: pomocí některé metody machine learning převádět souvislé atributy zařazené do kategorií atributy pro snadné použití.
* **Čištění text**: odebrat vložené znaky, které může způsobit, že chybné zarovnání dat, například vložený karty v oddělené tabulátorem datový soubor, vložit nové řádky, které mohou přestat fungovat záznamy atd.

Následující části podrobně popisují některé z těchto kroků zpracování dat.

## <a name="how-to-deal-with-missing-values"></a>Jak zacházet s chybějící hodnoty?
Vypořádat s chybějícími hodnotami, doporučujeme nejdřív zjistit příčinu pro chybějící hodnoty pro lepší zvládání problém. Typické chybí hodnota zpracování metody jsou následující:

* **Odstranění**: odebrat záznamy s chybějící hodnoty
* **Zástupný nahrazení**: chybějící hodnoty nahradit fiktivní hodnoty: například *neznámý* zařazené do kategorií nebo 0 pro numerické hodnoty.
* **Znamenají nahrazení**: Pokud chybí data jsou číselná, chybějící hodnoty nahradit střední.
* **Časté nahrazení**: Pokud je chybějící data zařazená do kategorií, chybějící hodnoty nahradit nejčastěji se vyskytujících položky
* **Nahrazení regrese**: chybějící hodnoty nahraďte nižším hodnoty použijte metodu regrese.  

## <a name="how-to-normalize-data"></a>Jak k normalizaci dat?
Normalizace data znovu škáluje číselné hodnoty do zadaného rozsahu. Mezi oblíbenými datovými normalizace metody patří:

* **Normalizace maximálních**: Lineárně transformace dat na rozsah, Řekněme, že mezi 0 a 1, pokud je minimální hodnota škálovaný na hodnotu 0 a maximální velikosti na 1.
* **Normalizace skóre Z**: škálování dat na základě střední a směrodatná odchylka: dělení rozdíl mezi daty a střední směrodatnou odchylku.
* **Desetinné škálování**: škálování dat přesunutím desetinnou hodnotu atributu.  

## <a name="how-to-discretize-data"></a>Jak k diskretizaci dat?
Data můžete diskretizovat převedením průběžné hodnoty na nominální atributy nebo intervalech. Jsou některé způsoby, jak to provést:

* **Rovná-Width Binning**: rozsah všech možných hodnot atributu rozdělit do skupin v síti stejné velikosti a přiřadit hodnoty, které spadají do přihrádky číslem bin.
* **Výška rovná Binning**: rozdělují rozsah všech možných hodnot atributu do skupin v síti, každá obsahuje stejný počet instancí a pak přiřadit hodnoty, které spadají do přihrádky číslem bin.  

## <a name="how-to-reduce-data"></a>Jak dosáhnout snížení dat?
Existují různé metody pro snížení velikosti dat pro snadnější práci s daty. V závislosti na velikosti dat a doméně můžete použít následující metody:

* **Zaznamenejte vzorkování**: ukázkový záznamů dat a pouze reprezentativní podmnožinu vybírat data.
* **Atribut vzorkování**: vyberte pouze podmnožinu vašich nejdůležitějších atributů data.  
* **Agregace**: data rozdělit do skupin a uložit čísla pro každou skupinu. Měsíční výnosy ke snížení množství dat může například agregovat denní počty výnosy řetězec restaurací v průběhu posledních 20 let.  

## <a name="how-to-clean-text-data"></a>Jak vyčistit textová data?
**Textová pole v tabulkových dat** může obsahovat znaky, které ovlivňují sloupce hranicích zarovnání a/nebo záznam. Například synchronizace souboru oddělené tabulátorem příčina sloupec součástí karty a vložené znaky nového řádku přerušení řádky záznamů. Text nesprávné kódování zpracování při zápisu/čtení textu vede k ztráty informací, zvyšuje ochranu před nechtěnými Úvod nečitelné znaky, například Null a může také vliv text analýza kódu. Pozor, analýze a úpravy může být potřeba, abyste mohli vyčistit textová pole pro správné zarovnání a/nebo k extrahování strukturovaná data z textu nestrukturovaná nebo semistrukturovaná data.

**Zkoumání dat** nabízí předčasné pohled na data. Počet problémů dat může nepokrytý během tohoto kroku a odpovídající metody lze použít pro vyřešení těchto problémů.  Je důležitá klást otázky, jako jsou novinky příčinu problému a jak tento problém může vzniknout. To také vám pomůže při rozhodování o zpracování dat kroky, které je třeba provést k jejich řešení. Druh přehledy, které jeden si klade za cíl odvozovat z dat lze také určit prioritu zpracování dat úsilí.

## <a name="references"></a>Odkazy
> *Dolování dat: Koncepty a techniky*, třetí vydání, Nováková Kaufmann, 2011, Jiawei Han, Micheline Kamber a Jian Pei
> 
> 


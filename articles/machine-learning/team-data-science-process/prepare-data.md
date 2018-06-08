---
title: Vyčistěte a připravit data pro Azure Machine Learning | Microsoft Docs
description: Předběžně zpracovat a vyčistit data připravit pro machine learning.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: bdf659ec-4881-4324-8b9c-747cbfa0c3cd
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 127c51b9a2617c6b8520d972a3cd4b6c3bbcddd1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837690"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Úlohy k přípravě dat pro rozšířené strojové učení
Předběžné zpracování a vyčištění dat jsou důležité úkoly, které obvykle musí být provedeny před datové sady je možné efektivně pro machine learning. Nezpracovaná data, je často aktivní nebo nespolehlivé a může být chybějící hodnoty. Pomocí těchto údajů pro modelování může vytvářet zavádějící výsledky. Tyto úlohy jsou součástí nástroje Team Data vědecké účely procesu (TDSP) a obvykle postupujte podle počáteční zkoumání použito k vyhledávání a plánování předběžné zpracování požadované datové sady. Podrobné pokyny k procesu TDSP, najdete v části podle kroků uvedených v [proces vědecké účely dat Team](overview.md).

Předběžné zpracování a čištění úlohy, jako například úloha zkoumání dat, lze provádět v celé řadě prostředí, jako je například SQL nebo Hive nebo Azure Machine Learning Studio a pomocí různých nástrojů a jazyky, jako je R nebo Python, v závislosti, kde jsou data uložena a jejich formátování. Vzhledem k tomu, že se předpokládá několikeré ve své podstatě TDSP, tyto úlohy můžete provádět v jednotlivých kroků v procesu pracovního postupu.

Tento článek představuje různé zpracování dat koncepty a úlohy, které lze provádět před nebo po příjem dat do Azure Machine Learning.

Příklad zkoumání dat a předběžné zpracování provést uvnitř Azure Machine Learning studio, najdete v článku [předběžné zpracování dat v Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videa.

## <a name="why-pre-process-and-clean-data"></a>Proč předběžně zpracovat a vyčistit data?
Shromáždění skutečných dat z různých zdrojů a procesy a může obsahovat nesrovnalostí nebo poškozená data ohrožení kvality datovou sadu. Problémy kvality typické dat, které vznikají jsou:

* **Nedokončené**: dat chybí atributy nebo obsahující chybějící hodnoty.
* **Aktivní**: Data obsahují chybné záznamy nebo odlehlé hodnoty.
* **Nekonzistentní**: Data obsahují konfliktní záznamy nebo nesrovnalostí.

Kvality dat je předpokladem pro prediktivní modely kvality. Vyhněte se "uvolňování paměti v paměti se" a zlepšení kvality dat a proto modelu výkonu, je nutné ke stavu obrazovky dat již v rané fázi přímé problémy dat a rozhodnout, na odpovídající zpracování dat a čisticí kroky.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Jaké jsou některé obrazovky stavu typické dat, které budou použity?
Kontrolou jsme můžete zkontrolovat obecné kvality dat:

* Počet **záznamy**.
* Počet **atributy** (nebo **funkce**).
* Atribut **datové typy** (nominální, pořadí nebo souvislé).
* Počet **chybějící hodnoty**.
* **Správnosti** data.
  * Pokud jsou data v TSV nebo sdílený svazek clusteru, zkontrolujte, že oddělovačů sloupce a řádku oddělovačů vždy správně jednotlivé sloupce a řádky.
  * Pokud jsou data ve formátu HTML nebo XML, zkontrolujte, zda data je ve správném formátu závislosti na jejich příslušné standardy.
  * Analýza může být také nezbytné, aby extrahovat z částečně strukturovaných nebo nestrukturovaných dat strukturovaných informace.
* **Zaznamenává nekonzistentní data**. Zkontrolujte, jsou povolené rozsah hodnot. Například pokud data obsahují student GPA, zkontrolujte, zda je GPA v určené oblasti vyslovení 0 ~ 4.

Pokud narazíte na problémy s daty, **kroky zpracování** jsou nezbytné, což často zahrnuje vyčištění chybějících hodnot, data normalizaci, diskretizační, zpracování textu odeberte nebo nahraďte vložených znaků, což může mít vliv na data společné zarovnání různé datové typy, pole a dalších.

**Azure Machine Learning spotřebuje ve správném formátu tabulková data**.  Pokud již data ve formě tabulky, předběžné zpracování dat lze provést přímo pomocí Azure Machine Learning v nástroji Machine Learning Studio.  Pokud data nejsou ve formě tabulky, indikované, který je v XML, může být analýza vyžaduje, aby bylo možné převést data na formě tabulky.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jaké jsou některé z hlavních úloh v předběžné zpracování dat?
* **Čištění dat.**: vyplňte nebo chybějící hodnoty detekovat a odstraňovat aktivní data a extrémních.
* **Transformace dat**: normalizaci dat a snižuje tak dimenzí a šumu.
* **Data snížení**: ukázková data záznamy nebo atributy pro snazší manipulaci s daty.
* **Data diskretizační**: převést souvislé atributy kategorií atributy pro snadné použití pomocí metod určité machine learning.
* **Text čištění**: odebrat vložené znaky, které může způsobit, že chybné zarovnání dat, například embedded karty v souboru tabulátorem data vložených nové řádky, které může rozdělit záznamy atd.

Následující části obsahují podrobnosti některé z těchto kroků zpracování dat.

## <a name="how-to-deal-with-missing-values"></a>Řešení problémů s chybějící hodnoty?
Jak nakládat s chybějící hodnoty, je vhodné nejdříve identifikovat důvod chybějící hodnoty a lepší popisovač problém. Typické chybí hodnota zpracování metody jsou následující:

* **Odstranění**: odebrat záznamy s chybějící hodnoty
* **Fiktivní nahrazení**: chybějící hodnoty nahraďte fiktivní hodnoty: např, *neznámé* kategorií nebo 0 pro číselné hodnoty.
* **Znamenat nahrazení**: Pokud chybějící data číselné, nahraďte chybějící hodnoty střední.
* **Časté nahrazení**: Pokud chybějící data kategorií, nahraďte chybějící hodnoty nejčastěji se vyskytující položku
* **Nahrazení regrese**: použijte metodu regrese nahradit chybějící hodnoty který poklesl hodnoty.  

## <a name="how-to-normalize-data"></a>Jak můžete normalizovat dat?
Data normalizaci znovu škáluje číselné hodnoty pro zadaný rozsah. Metody normalizaci oblíbených dat patří:

* **Min-Max normalizaci**: Lineárně transformovat data do rozsahu, můžete mezi 0 a 1, kde je minimální hodnota škálovat na 0 a maximální hodnotu 1.
* **Z – score normalizaci**: škálování dat na základě střední a směrodatnou odchylku: dělit rozdíl mezi daty a střední směrodatnou odchylku.
* **Decimal škálování**: škálování dat jejich přesunutím desetinnou hodnotu atributu.  

## <a name="how-to-discretize-data"></a>Jak diskretizaci dat?
Data můžete diskrétní převedením průběžné hodnoty na nominální atributy nebo intervaly. Některé z mnoha možností to jsou:

* **Přihrádkování rovná-Width**: rozdělení rozsah všech možných hodnot atributu na N skupiny stejnou velikost a přiřadit hodnoty, které spadají do přihrádky číslem Koš.
* **Výška rovná Přihrádkování**: rozdělení rozsah všech možných hodnot atributu do skupiny, každý by měl obsahovat stejný počet instancí a potom přiřadit hodnoty, které spadají do přihrádky číslem Koš.  

## <a name="how-to-reduce-data"></a>Jak k omezení dat?
Existují různé metody pro snížení velikosti dat pro snazší data zpracování. V závislosti na velikosti dat a doméně můžete použít následující metody:

* **Zaznamenejte vzorkování**: ukázkové datových záznamů a pouze reprezentativní podmnožinu vybírat data.
* **Atribut vzorkování**: vyberte pouze podmnožinu nejdůležitější atributů data.  
* **Agregace**: rozdělení dat do skupin a uložit čísla pro každou skupinu. Například denní výnosy čísla řetězu restaurace v posledních letech 20 můžete agregovat do měsíčního výnosy ke snížení velikosti dat.  

## <a name="how-to-clean-text-data"></a>Postup vyčištění textová data?
**Textová pole v tabulkovém data** může obsahovat znaky, které ovlivňují sloupce zarovnání nebo záznam hranice. Například vložených karty v synchronizace souboru tabulátorem příčina sloupce a vložených znaky nového řádku rozdělit záznamů řádků. Nesprávné text kódování zpracování při zápisu nebo čtení textu vede ke ztrátě informací, nechtěnému Úvod nečitelné znaky, například hodnoty Null, a může také vliv text analýzu. Pozor, analýze a úpravy mohou být vyžadovány Chcete-li vyčistit textových polí pro správné zarovnání a/nebo extrakce strukturovaná data z částečně strukturovaných nebo nestrukturovaných textová data.

**Zkoumání dat** nabízí časná pohled na data. Počet problémy dat může neodkrytých během tohoto kroku a odpovídající metody lze použít k řešení těchto problémů.  Je důležité k nim máte nějaké otázky, jako jsou novinky zdroj problému a jak tento problém může mít zavedený. To také vám pomůže rozhodnout o zpracování dat kroky, které je třeba přijmout jejich řešení. Druh statistiky, které jeden chtít odvozena z dat lze také nastavit prioritu zpracování dat úsilí.

## <a name="references"></a>Odkazy
> *Dolování dat: Konceptech a technikách*, Third Edition, Nováková Kaufmann, 2011, Hanu Jiawei, Micheline Kamber a Jian Pei
> 
> 


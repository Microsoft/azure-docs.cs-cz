---
title: Fáze modelování životního cyklu procesu vědeckého zpracování dat týmu
description: Cíle, úkoly a dodávky pro fázi modelování vašich projektů pro datové vědy
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
ms.openlocfilehash: 3845c5e5d7cf6bb372744fb3c740c44aa2b94236
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305606"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Fáze modelování životního cyklu procesu vědeckého zpracování dat týmu

Tento článek popisuje cíle, úlohy a dodávky spojené se fází modelování vědeckého zpracování týmových dat (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů pro vědu. Životní cyklus popisuje hlavní fáze, které se obvykle spouštějí projekty, často iterativní:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Tady je vizuální znázornění životního cyklu TDSP:

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Určete optimální datové funkce pro model strojového učení.
* Vytvořte informativní model strojového učení, který předpovídá cíl přesně.
* Vytvořte model strojového učení, který je vhodný pro produkční prostředí.

## <a name="how-to-do-it"></a>Jak to provést
V této fázi jsou řešeny tři hlavní úlohy:

  * **Technické funkce** : vytváření datových funkcí z nezpracovaných dat pro usnadnění školení modelů.
  * **Školení modelů** : Najděte model, který přesně odpovídá otázce, porovnáním jejich metrik úspěšnosti.
  * Zjistěte, jestli je model **vhodný pro produkční prostředí.**

### <a name="feature-engineering"></a>Příprava atributů
Konstrukce funkcí zahrnuje zahrnutí, agregaci a transformaci nezpracovaných proměnných pro vytvoření funkcí používaných při analýze. Pokud chcete získat přehled o tom, co řídí model, pak potřebujete pochopit, jak vzájemně souvisí tyto funkce a jak se tyto funkce používají v algoritmech strojového učení. 

Tento krok vyžaduje tvůrčí kombinaci odbornosti domény a přehledy získané z kroku pro zkoumání dat. Inženýrské funkce je vyvážením zákonem o hledání a zahrnutí informativních proměnných, ale zároveň se snažíte se vyhnout příliš mnoha nesouvisejícím proměnným. Informativní proměnné zlepšují svůj výsledek; nesouvisející proměnné představují zbytečný šum v modelu. Tyto funkce je také potřeba vygenerovat pro všechna nová data získaná během bodování. V důsledku toho generování těchto funkcí může záviset jenom na datech, která jsou k dispozici v době hodnocení. 

Technické pokyny pro strojírenství funkcí při využívání různých datových technologií Azure najdete v tématu Technické informace o [funkcích v procesu datové vědy](create-features.md). 

### <a name="model-training"></a>Trénování modelu
V závislosti na typu otázky, na kterou se pokoušíte odpovědět, je k dispozici mnoho algoritmů modelování. Pokyny k výběru algoritmů najdete v tématu [Jak zvolit algoritmy pro Microsoft Azure Machine Learning](../how-to-select-algorithms.md). I když tento článek používá Azure Machine Learning, pokyny, které poskytuje, jsou užitečné pro všechny projekty strojového učení. 

Proces pro školení modelů zahrnuje následující kroky: 

   * Náhodné **rozdělení vstupních dat** pro modelování do sady dat školení a testovací sady dat.
   * **Sestavujte modely** pomocí sady školicích dat.
   * **Vyhodnoťte** školení a sadu testovacích dat. Používejte řadu konkurenčních algoritmů strojového učení spolu s různými přidruženými parametry ladění (označovanými jako *úklid parametrů* ), které jsou zaměřené na zodpovězení otázky zájmu s aktuálními daty.
   * **Určete nejlepší řešení** pro zodpovězení otázky porovnáním metrik úspěšnosti mezi alternativními metodami.

> [!NOTE]
> **Vyhněte se únikům** : únik dat můžete způsobit, pokud zahrnete data mimo sadu školicích dat, která umožňuje model nebo algoritmus strojového učení, aby se nerealisticky předpovědi. Netěsnost je běžným důvodem pro to, proč se datové odborníky dostanou do nervu, když získají prediktivní výsledky, které se jeví jako nepravdivé. Tyto závislosti můžou být obtížné detekovat. Aby se zabránilo úniku často, je třeba provést iteraci mezi sestavením sady dat analýzy, vytvořením modelu a vyhodnocením přesnosti výsledků. 
> 
> 

Poskytujeme [automatizovaný nástroj pro modelování a vytváření sestav](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) s TDSP, které je možné spustit pomocí několika algoritmů a Sweep parametrů a vytvořit základní model. Vytvoří také sestavu modelování standardních hodnot, která shrnuje výkon jednotlivých kombinací modelů a parametrů, včetně důležitosti proměnné. Tento proces je také iterativní, protože může řídit další inženýry funkcí. 

## <a name="artifacts"></a>Artifacts
Artefakty vytvořené v této fázi zahrnují:

   * [Sady funkcí](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): funkce vyvinuté pro modelování jsou popsané v části **sady funkcí** sestavy **definice dat** . Obsahuje ukazatele na kód pro generování funkcí a popis způsobu, jakým byla funkce vygenerována.
   * [Sestava modelu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): pro každý vyzkoušený model je vytvořena standardní sestava založená na šablonách, která poskytuje podrobnosti o každém experimentu.
   * **Rozhodnutí kontrolního bodu** : Vyhodnoťte, jestli model funguje dostatečně pro produkční prostředí. Některé klíčové otázky vám pomohou:
     * Odpovídá model otázce dostatečná důvěra na data testů? 
     * Měli byste vyzkoušet jakékoli alternativní přístupy? Měli byste shromažďovat další data, dělat další funkce a experimentovat s jinými algoritmy?

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na jednotlivé kroky v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme kompletní podrobné návody, které znázorňují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Návody ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, naleznete v tématu [použití TDSP s Azure Machine Learning](./index.yml).
---
title: Modelování fáze životního cyklu vědecké zpracování týmových dat
description: Cíle, úkoly a úkoly pro fázi modelování vašich projektů datové vědy
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
ms.openlocfilehash: 1d3cd61ea3da88c4c5231f22c0e127508591fb8d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720465"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modelování fáze životního cyklu vědecké zpracování týmových dat

Tento článek popisuje cíle, úkoly a úkoly, které jsou přidružené k modelování fáze nástroje zpracování týmových dat vědy (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Obchodní porozumění**
   2. **Získávání a porozumění dat**
   3. **Situací**
   4. **Nasazení**
   5. **Přijetí zákazníkovi**

Zde je vizuální znázornění životního cyklu TDSP:

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Určení funkcí optimální dat pro model strojového učení.
* Vytvoření informativní modelu strojového učení, který bude předpovídat cíl nejpřesněji.
* Vytvořte model strojového učení, která je vhodná pro produkční prostředí.

## <a name="how-to-do-it"></a>Jak na to
Existují tři hlavní úkoly zákazníky a vyřešené v této fázi:

  * **Technické funkce**: vytváření datových funkcí z nezpracovaných dat pro usnadnění školení modelů.
  * **Školení modelů**: Najděte model, který přesně odpovídá otázce, porovnáním jejich metrik úspěšnosti.
  * Zjistěte, jestli je model **vhodný pro produkční prostředí.**

### <a name="feature-engineering"></a>Návrh funkcí
Součástí vytváření funkcí je zahrnutí, agregace a transformaci nezpracovaných proměnné k vytvoření funkce použitého při analýze. Pokud chcete přehled o tom, co ovlivňuje modelu, je potřeba pochopit, jak funkce vzájemně souvisí a jak se používají tyto funkce algoritmů strojového učení. 

Tento krok vyžaduje creative kombinace odborné znalosti domény a přehledů získaných z kroku zkoumání dat. Vytváření funkcí je rovnováhu hledání a včetně informativní proměnných, ale současně pokouší vyhnout příliš mnoha nesouvisejících proměnných. Informativní proměnné zlepšit váš výsledek; nesouvisejících proměnné zavést zbytečně hlučné do modelu. Také budete muset vygenerovat tyto funkce pro žádná nová data zjišťovala během vyhodnocování. V důsledku toho generování těchto funkcí mohou záviset pouze na data, která je k dispozici v době vyhodnocení. 

Technické pokyny pro strojírenství funkcí při využívání různých datových technologií Azure najdete v tématu Technické informace o [funkcích v procesu datové vědy](create-features.md). 

### <a name="model-training"></a>Trénování modelu
V závislosti na typu otázku, na kterou se snažíte odpovědět k dispozici mnoho algoritmů modelování. Pokyny k výběru algoritmů najdete v tématu [Jak zvolit algoritmy pro Microsoft Azure Machine Learning](../studio/algorithm-choice.md). I když tento článek používá Azure Machine Learning, je užitečné pro všechny projekty strojového učení pokyny, které poskytuje. 

Proces k tréninku modelu zahrnuje následující kroky: 

   * Náhodné **rozdělení vstupních dat** pro modelování do sady dat školení a testovací sady dat.
   * **Sestavujte modely** pomocí sady školicích dat.
   * **Vyhodnoťte** školení a sadu testovacích dat. Používejte řadu konkurenčních algoritmů strojového učení spolu s různými přidruženými parametry ladění (označovanými jako *úklid parametrů*), které jsou zaměřené na zodpovězení otázky zájmu s aktuálními daty.
   * **Určete nejlepší řešení** pro zodpovězení otázky porovnáním metrik úspěšnosti mezi alternativními metodami.

> [!NOTE]
> **Vyhněte se únikům**: únik dat můžete způsobit, pokud zahrnete data mimo sadu školicích dat, která umožňuje model nebo algoritmus strojového učení, aby se nerealisticky předpovědi. Únik je běžným důvodem, proč data, která odborníci přes získat nervové, pokud se jim zobrazí prediktivní výsledky, které vypadá to, že snad ani na hodnotu true. Tyto závislosti může být obtížné rozpoznat. Aby se zabránilo úniku často vyžaduje iterace mezi sestavení analýze datové sady, vytvoření modelu a vyhodnocení přesnost výsledků. 
> 
> 

Poskytujeme [automatizovaný nástroj pro modelování a vytváření sestav](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) s TDSP, které je možné spustit pomocí několika algoritmů a Sweep parametrů a vytvořit základní model. Také vytvoří směrný plán modelování sestavu, která shrnuje výkon každá kombinace modelu a parametr včetně proměnných význam. Tento proces je iterativní také jako jeho jednotky vytváření dalších funkcí. 

## <a name="artifacts"></a>Artefakty
Artefakty vytvořené v této fázi patří:

   * [Sady funkcí](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): funkce vyvinuté pro modelování jsou popsané v části **sady funkcí** sestavy **definice dat** . Obsahuje odkazy na kód ke generování funkcí a popis, jak se vygeneroval funkci.
   * [Sestava modelu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): pro každý vyzkoušený model je vytvořena standardní sestava založená na šablonách, která poskytuje podrobnosti o každém experimentu.
   * **Rozhodnutí kontrolního bodu**: Vyhodnoťte, jestli model funguje dostatečně pro produkční prostředí. Jsou některé klíčové otázky:
     * Model odpovědět na otázku, s dostatečnou důvěru zadaný testovací data? 
     * Můžete zkusit všechny přístupy? By měl můžete shromažďovat další data, proveďte další vytváření funkcí nebo vyzkoušet ostatní algoritmy?

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Obchodní porozumění](lifecycle-business-understanding.md)
   2. [Získávání a porozumění dat](lifecycle-data.md)
   3. [Situací](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníkovi](lifecycle-acceptance.md)

Poskytujeme úplného začátku do konce návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, naleznete v tématu [použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 

---
title: Fáze modelování životního cyklu procesu vědecké ho procesu týmových dat
description: Cíle, úkoly a výstupy pro fázi modelování vašich projektů datové vědy
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720465"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Fáze modelování životního cyklu procesu vědecké ho procesu týmových dat

Tento článek popisuje cíle, úkoly a dodávky spojené s fází modelování procesu vědecké vědy o týmových datech (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů datové vědy. Životní cyklus popisuje hlavní fáze, které projekty obvykle provádějí, často iterativně:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Zde je vizuální znázornění životního cyklu TDSP:

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Určete optimální datové funkce pro model strojového učení.
* Vytvořte informativní model strojového učení, který přesněji předpovídá cíl.
* Vytvořte model strojového učení, který je vhodný pro výrobu.

## <a name="how-to-do-it"></a>Jak na to
V této fázi jsou řešeny tři hlavní úkoly:

  * **Technické funkce**: Vytvoření datových prvků z nezpracovaných dat pro usnadnění trénování modelu.
  * **Model školení**: Najít model, který odpovídá na otázku nejpřesněji porovnáním jejich úspěch metriky.
  * Zjistěte, zda je váš model **vhodný pro výrobu.**

### <a name="feature-engineering"></a>Návrh funkcí
Funkce inženýrství zahrnuje zahrnutí, agregace a transformace nezpracovaných proměnných k vytvoření funkcí použitých v analýze. Pokud chcete nahlédnout do toho, co je hnací model, pak je třeba pochopit, jak funkce se vztahují k sobě navzájem a jak algoritmy strojového učení jsou k použití těchto funkcí. 

Tento krok vyžaduje kreativní kombinaci odborných znalostí domény a poznatků získaných z kroku zkoumání dat. Funkce inženýrství je vyvažování akt hledání a včetně informativní proměnné, ale zároveň se snaží vyhnout příliš mnoho nesouvisejících proměnných. Informativní proměnné zlepšit svůj výsledek; nesouvisející proměnné zavádějí do modelu zbytečný šum. Je také nutné generovat tyto funkce pro všechna nová data získaná během vyhodnocování. V důsledku toho generování těchto funkcí může záviset pouze na datech, která jsou k dispozici v době vyhodnocování. 

Technické pokyny k inženýrství funkcí při využití různých datových technologií Azure najdete [v tématu Funkce inženýrství v procesu datové vědy](create-features.md). 

### <a name="model-training"></a>Trénování modelu
V závislosti na typu otázky, na kterou se pokoušíte odpovědět, je k dispozici mnoho algoritmů modelování. Pokyny k výběru algoritmů najdete v tématu [Jak vybrat algoritmy pro Microsoft Azure Machine Learning](../studio/algorithm-choice.md). I když tento článek používá Azure Machine Learning, pokyny, které poskytuje, je užitečné pro všechny projekty strojového učení. 

Proces pro školení modelu zahrnuje následující kroky: 

   * **Rozdělte vstupní data** náhodně pro modelování do trénovací datové sady a testovací datové sady.
   * **Vytvořte modely** pomocí trénovací datové sady.
   * **Vyhodnoťte** trénování a sadu testovacích dat. Použijte řadu konkurenčních algoritmů strojového učení spolu s různými přidruženými parametry ladění (označované jako *parametr sweep),* které jsou zaměřeny na zodpovězení otázky zájmu s aktuálními daty.
   * **Určete "nejlepší" řešení** odpovědět na otázku porovnáním metriky úspěchu mezi alternativní metody.

> [!NOTE]
> **Vyhněte se úniku**: Můžete způsobit únik dat, pokud zahrnete data z mimo trénovací datové sady, která umožňuje modelu nebo algoritmu strojového učení, aby nerealisticky dobré předpovědi. Únik je častým důvodem, proč jsou datoví vědci nervózní, když dostanou prediktivní výsledky, které se zdají být příliš dobré na to, aby to byla pravda. Tyto závislosti může být obtížné zjistit. Aby se zabránilo úniku často vyžaduje iterace mezi vytváření množiny dat analýzy, vytvoření modelu a vyhodnocení přesnosti výsledků. 
> 
> 

Poskytujeme [automatizovaný modelovací a vykazovací nástroj](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) s TDSP, který je schopen projít více algoritmů a parametry sweeps k vytvoření základního modelu. Vytvoří také zprávu o modelování směrného plánu, která shrnuje výkon jednotlivých modelů a kombinací parametrů včetně proměnné důležitosti. Tento proces je také iterativní, protože může řídit další funkce inženýrství. 

## <a name="artifacts"></a>Artefakty
Artefakty vyrobené v této fázi zahrnují:

   * [Sady prvků](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): Funkce vyvinuté pro modelování jsou popsány v části **Sady funkcí** v sestavě **definice dat.** Obsahuje ukazatele na kód pro generování funkcí a popis, jak byla funkce generována.
   * [Sestava modelu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): Pro každý model, který je vyzkoušen, je vytvořena standardní sestava založená na šablonách, která obsahuje podrobnosti o každém experimentu.
   * **Rozhodnutí kontrolního bodu**: Vyhodnoťte, zda model provádí dostatečně pro výrobu. Některé klíčové otázky, které je třeba položit, jsou:
     * Odpovídá model na otázku s dostatečnou jistotou vzhledem k údajům o zkoušce? 
     * Měli byste zkusit nějaké alternativní přístupy? Měli byste shromažďovat další data, dělat více funkce inženýrství, nebo experimentovat s jinými algoritmy?

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme úplné kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Ukázkový [článek obsahuje](walkthroughs.md) seznam scénářů s odkazy a popisy miniatur. Návody ilustrují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu k vytvoření inteligentní aplikace. 

Příklady kroků v TDSP, které používají Azure Machine Learning Studio, najdete [v tématu Použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 

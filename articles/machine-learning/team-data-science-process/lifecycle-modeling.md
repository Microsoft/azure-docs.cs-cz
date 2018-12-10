---
title: Modelování fáze životního cyklu vědecké zpracování týmových dat
description: Cíle, úkoly a úkoly pro fázi modelování vašich projektů datové vědy
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: dac686878ff128e3d556c0dbd7e9a2d51ac1756d
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139943"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modelování fáze životního cyklu vědecké zpracování týmových dat

Tento článek popisuje cíle, úkoly a úkoly, které jsou přidružené k modelování fáze nástroje zpracování týmových dat vědy (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Schválení zákazníkem**

Zde je vizuální znázornění životního cyklu TDSP:

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Určení funkcí optimální dat pro model strojového učení.
* Vytvoření informativní modelu strojového učení, který bude předpovídat cíl nejpřesněji.
* Vytvořte model strojového učení, která je vhodná pro produkční prostředí.

## <a name="how-to-do-it"></a>Jak na to
Existují tři hlavní úkoly zákazníky a vyřešené v této fázi:

  * **Konstrukce funkcí**: vytvoření datové funkce z nezpracovaných dat pro usnadnění cvičení modelu.
  * **Model školení**: najít model, který nejpřesněji odpovědět otázku porovnáním jejich měřítek úspěšnosti.
  * Určení, zda je model **vhodná pro produkční prostředí.**

### <a name="feature-engineering"></a>Návrh funkcí
Součástí vytváření funkcí je zahrnutí, agregace a transformaci nezpracovaných proměnné k vytvoření funkce použitého při analýze. Pokud chcete přehled o tom, co ovlivňuje modelu, je potřeba pochopit, jak funkce vzájemně souvisí a jak se používají tyto funkce algoritmů strojového učení. 

Tento krok vyžaduje creative kombinace odborné znalosti domény a přehledů získaných z kroku zkoumání dat. Vytváření funkcí je rovnováhu hledání a včetně informativní proměnných, ale současně pokouší vyhnout příliš mnoha nesouvisejících proměnných. Informativní proměnné zlepšit váš výsledek; nesouvisejících proměnné zavést zbytečně hlučné do modelu. Také budete muset vygenerovat tyto funkce pro žádná nová data zjišťovala během vyhodnocování. V důsledku toho generování těchto funkcí mohou záviset pouze na data, která je k dispozici v době vyhodnocení. 

Technické informace o funkci při analýze provést pomocí různých technologií dat Azure, najdete v článku [konstrukce funkcí v vědecké zpracování dat](create-features.md). 

### <a name="model-training"></a>Cvičení modelu
V závislosti na typu otázku, na kterou se snažíte odpovědět k dispozici mnoho algoritmů modelování. Rady týkající se algoritmy, naleznete v tématu [jak zvolit algoritmy pro Microsoft Azure Machine Learning](../studio/algorithm-choice.md). I když tento článek používá Azure Machine Learning, je užitečné pro všechny projekty strojového učení pokyny, které poskytuje. 

Proces k tréninku modelu zahrnuje následující kroky: 

   * **Rozdělit vstupní data** náhodně pro modelování do trénovací datové sady a testovací datové sady.
   * **Vytváření modelů** pomocí trénovací datové sady.
   * **Vyhodnocení** školení a datové sady testů. Používat řadu konkurenční algoritmů strojového učení spolu s různými přidružené parametry (označované jako *Uklízení parametrů*), které jsou cílené primárně na odpovídání na otázku, která vás zajímá s aktuálními údaji.
   * **Určit "doporučené" řešení** odpověď na otázku porovnáním měřítek úspěšnosti mezi alternativních metod.

> [!NOTE]
> **Vyhněte se úniku**: může způsobit úniky dat chránit, zadáte-li data z mimo trénovací datové sady, která umožňuje algoritmus strojového učení pro predikci unrealistically dobré a model. Únik je běžným důvodem, proč data, která odborníci přes získat nervové, pokud se jim zobrazí prediktivní výsledky, které vypadá to, že snad ani na hodnotu true. Tyto závislosti může být obtížné rozpoznat. Aby se zabránilo úniku často vyžaduje iterace mezi sestavení analýze datové sady, vytvoření modelu a vyhodnocení přesnost výsledků. 
> 
> 

Poskytujeme [automatizované modelování a vytváření sestav nástroje](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) s TDSP, aby bylo možné spustit pomocí několika algoritmů a parametrů změny k vytvoření základního modelu. Také vytvoří směrný plán modelování sestavu, která shrnuje výkon každá kombinace modelu a parametr včetně proměnných význam. Tento proces je iterativní také jako jeho jednotky vytváření dalších funkcí. 

## <a name="artifacts"></a>Artefakty
Artefakty vytvořené v této fázi patří:

   * [Sady funkcí](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): funkce vyvinuté pro modelování jsou popsány v **sady funkcí** část **definici dat** sestavy. Obsahuje odkazy na kód ke generování funkcí a popis, jak se vygeneroval funkci.
   * [Model sestavy](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): pro každý model, ke které se pokusili, což je standard je vytvořen na základě šablony sestavu, která poskytuje podrobné informace o jednotlivých experimentu.
   * **Kontrolní bod rozhodnutí**: vyhodnocení, zda model dostatečně dobře provede nasazení do produkčního systému. Jsou některé klíčové otázky:
     * Model odpovědět na otázku, s dostatečnou důvěru zadaný testovací data? 
     * Můžete zkusit všechny přístupy? By měl můžete shromažďovat další data, proveďte další vytváření funkcí nebo vyzkoušet ostatní algoritmy?

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Schválení zákazníkem](lifecycle-acceptance.md)

Poskytujeme úplného začátku do konce návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady toho, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio najdete v tématu [TDSP pomocí služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 

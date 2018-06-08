---
title: Modelování fáze životního cyklu proces vědecké účely dat Team - Azure | Microsoft Docs
description: Cíle, úlohy a úkoly pro modelování fázi projekty vědecké zpracování dat
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: ff7434e84473fd68153be8116899ecdfb6a6a831
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837476"
---
# <a name="modeling"></a>Modelování

Tento článek popisuje cíle, úlohy a úkoly spojené s fázi modelování z tým datové vědy procesu (TDSP). Tento proces zajišťuje doporučené životního cyklu, který můžete použít pro struktury vědecké zpracování dat projekty. Životní cyklus popisuje hlavní fází, které projekty obvykle provést, často interaktivně:

   1. **Pochopení obchodních**
   2. **Získávání dat a principy**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníka**

Zde je vizuální reprezentace životního cyklu TDSP:

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Určení funkcí optimální dat pro model machine learning.
* Vytvoření modelu informativní strojové učení, který nejpřesněji předpovídá cíl.
* Vytvořte model machine learning, který je vhodný pro produkční prostředí.

## <a name="how-to-do-it"></a>Jak to udělat
Existují tři hlavní úlohy v této fázi řešit:

  * **Konstruování**: vytvoření funkce data z původní data, která usnadňují modelu školení.
  * **Model školení**: najít model, který odpovídá na otázku nejvíce přesně tak, že porovnáte jejich metriky úspěchu.
  * Určí, zda váš model **vhodná pro produkční prostředí.**

### <a name="feature-engineering"></a>Návrh funkcí
Funkce inženýrství zahrnuje zahrnutí, agregace a transformaci nezpracovaná proměnných pro vytvoření funkce použité v analýzy. Pokud chcete, aby aspekty faktory určující model, musíte pochopit, jak funkce související se vzájemně k sobě a jak jsou algoritmy strojové učení k používání těchto funkcí. 

Tento krok vyžaduje tvůrčí kombinací domény znalosti a statistiky získat z kroku zkoumání dat. Funkce inženýrství je vyrovnávání act hledání a včetně informativní proměnné, ale současně pokusu vyhnout příliš mnoho nesouvisejícími proměnné. Informativní proměnné zlepšení vaší výsledek; nesouvisejícími proměnné zavést jako zbytečný rušivý element do modelu. Také budete muset vygenerovat tyto funkce pro žádná nová data získané při vyhodnocování. V důsledku toho generování tyto funkce lze pouze závisí na data, která je k dispozici v době vyhodnocování. 

Technické informace o funkci technici, kdy je pomocí různých technologií dat Azure najdete v tématu [konstruování v procesu vědecké účely dat](create-features.md). 

### <a name="model-training"></a>model školení
V závislosti na typu otázku, kterou se pokoušíte odpovědět existuje mnoho modelování algoritmy. Pokyny k výběru algoritmy, najdete v části [jak zvolit algoritmy pro Microsoft Azure Machine Learning](../studio/algorithm-choice.md). I když tento článek používá Azure Machine Learning, pokyny, které poskytuje je užitečné pro všechny projekty strojové učení. 

Proces pro trénování modelu zahrnuje následující kroky: 

   * **Rozdělení vstupních dat** náhodně pro modelování do trénovací datové sady a testovací datové sady.
   * **Vytvářet modely** pomocí trénovací datové sady.
   * **Vyhodnocení** školení a testovací datové sady. Použijte řadu konkurenční algoritmů machine learningu spolu s různými přidružené parametry (označované jako *parametr oblouku*), jsou mají za cíl zajistit odpovědi na otázky týkající se s aktuálními daty.
   * **Určit "doporučené" řešení** odpověď na otázku tak, že porovnáte metriky úspěchu mezi alternativní metody.

> [!NOTE]
> **Zabránilo úniku**: může způsobit úniku dat, je-li zahrnout data z mimo trénovací datové sady, který umožňuje modelu nebo strojové učení algoritmu aby unrealistically dobrý předpovědi. Únikům je běžným důvodem, proč data, která vědců získat nervové, když získají prediktivní výsledky, které se zdají příliš výhodné mít hodnotu true. Tyto závislosti může být obtížné zjistit. Aby se zabránilo úniku často vyžaduje iterace mezi vytváření sadu analýzy dat, vytvoření modelu a vyhodnocení přesné výsledky. 
> 
> 

Poskytujeme [automatizované modelování a vytváření sestav nástroje](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) s TDSP, který se bude moct spustit prostřednictvím více algoritmů a parametr změny k vytvoření základního modelu. Vytváří také směrný plán modelování sestavu, která shrnuje výkon kombinace každý model a parametr včetně proměnné význam. Tento proces je iterativní také jako ho můžete drive inženýrství další funkce. 

## <a name="artifacts"></a>Artefakty
Artefakty vytvořeného v této fázi patří:

   * [Funkce sady](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): funkce vytvořených pro modelování jsou popsány v **sady funkcí** části **definice dat** sestavy. Obsahuje odkazy na kód pro generování funkcí a popis generování funkci.
   * [Model sestavy](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): pro každý model, který se pokus o, standard, se vytváří na základě šablon sestav, která poskytuje podrobnosti o každém pokusu.
   * **Kontrolní bod rozhodnutí**: vyhodnocení, zda model dostatečně a provede ji nasadit do produkčního systému. Jsou některé klíčové otázky:
     * Model odpovědět na otázku s dostatečnou důvěru zadané testovací data? 
     * Vyzkoušejte všechny přístupy? By měl můžete shromažďovat další data, provést další funkce inženýrství nebo experimentovat s jiné algoritmy?

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Pochopení obchodních](lifecycle-business-understanding.md)
   2. [Získávání dat a principy](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníka](lifecycle-acceptance.md)

Poskytujeme návody úplného začátku do konce, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, najdete v části [pomocí Azure Machine Learning TDSP](http://aka.ms/datascienceprocess). 

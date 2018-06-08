---
title: Obchodní principy fáze životního cyklu proces vědecké účely dat Team - Azure | Microsoft Docs
description: Cíle, úlohy a úkoly pro fázi Principy obchodní projekty vědecké zpracování dat
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
ms.openlocfilehash: 6d8eedbbf4a682443e73ecb9cf9496f3cdd1cd9d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837197"
---
# <a name="business-understanding"></a>Principy podniku

Tento článek popisuje cíle, úlohy a úkoly spojené s fázi Principy obchodní z tým datové vědy procesu (TDSP). Tento proces zajišťuje doporučené životního cyklu, který můžete použít pro struktury vědecké zpracování dat projekty. Životní cyklus popisuje hlavní fází, které projekty obvykle provést, často interaktivně:

   1. **Pochopení obchodních**
   2. **Získávání dat a principy**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníka**

Zde je vizuální reprezentace životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Zadejte, že klíče proměnné, které jsou sloužit jako model cíle a jehož související metriky používají zjistili úspěšnost projektu.
* Identifikujte zdroje relevantních dat, které má přístup k firmy nebo je potřeba získat.

## <a name="how-to-do-it"></a>Jak to udělat
Existují dvě hlavní úlohy v této fázi řešit: 

   * **Definování cílů**: práce s vašich zákazníků a dalších zúčastněných osob, pochopit a identifikovat obchodních problémů. Formulovali otázky, které definují obchodní cíle, které mohou být určeny techniky vědecké účely data.
   * **Identifikaci zdroje dat**: najít relevantní data, která vám pomůže odpovědět na otázky, které definují cíle projektu.

### <a name="define-objectives"></a>Definování cílů
1. Ústředním cílem tohoto kroku je identifikace klíče obchodní proměnné, které analýza potřebuje k předpovědi. Označujeme tyto proměnné, jako *modelu cíle*, a abyste zjistili úspěšnost projektu používáme metriky s nimi spojených. Dva příklady takových cíle jsou prognózy prodeje nebo pravděpodobnost pořadí se podvodné.

2. Definování cílů projektu žádostí a upřesnění "ostré" otázky, které jsou relevantní, konkrétní a jednoznačné. Vědecké zpracování dat je proces, který využívá názvů a čísel k odpovědi na tyto otázky. Další informace o klást otázky sharp najdete v tématu [postup vědecké zpracování dat](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blogu. Obvykle použijete, vědecké zpracování dat nebo strojového učení k odpovědi na pět typů otázek:
 
   * Kolik nebo kolik? (regrese)
   * Které kategorie? (klasifikace)
   * Které skupiny? (clustery)
   * Je to divné? (detekce anomálií)
   * Možnosti, kterou by měla být provedena? (doporučení)

   Určete, který tyto dotazy vás nemůže ověřit a jak volaného dosahuje svých cílů firmy.

3. Definujte projektový tým zadáním rolích a zodpovědnostech svých členů. Vytvořte plán vysoké úrovně milník, který iterovat, co zjistíte další informace. 

4. Definujte metriky úspěchu. Například můžete chtít dosáhnout předpovědi změn zákazníka. Budete potřebovat přesnost míra "x" procent na konci tohoto projektu tři měsíce. S těmito daty můžete nabízet, že zákazník povýšení ke snížení změn. Musí být metriky **INTELIGENTNÍ**: 

   * **S**určitá 
   * **M**easurable
   * **A**chievable 
   * **R**elevant 
   * **T**vázané na editoru ime 

### <a name="identify-data-sources"></a>Identifikaci zdroje dat
Identifikujte zdroje dat, které obsahují známé příklady odpovědi na otázky sharp. Vyhledejte následující data:

* Data, která se týkají na otázku. Máte míry cíl a funkce, které se vztahují k cíli?
* Data, která je přesné měření cílových modelu a funkce, které vás zajímají.

Například je možné, že existující systémy nutné shromažďovat a protokolovat další typy dat potíže vyřešit a dosáhnout cíle projektu. V takovém případě můžete hledat externích zdrojů dat. nebo aktualizovat vaše systémy shromažďovat nová data.

## <a name="artifacts"></a>Artefakty
Zde jsou dodávky v této fázi:

   * [Dokument titulů](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): standardní šablona je součástí definice struktury TDSP projektu. Dokument titulů je dokument životností. Můžete aktualizovat šablonu v rámci projektu jako provedete nové zjišťování a jako obchodní požadavky se změní. Klíč je k iteraci na tento dokument, přidání další podrobnosti, jako průběh procesu zjišťování. Ponechte zákazníka a ostatní účastníci součástí provádění požadovaných změn a zřetelněji sdělují důvody pro změny k nim.  
   * [Zdroje dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): **zdroje dat ve formátu Raw** části **definice dat** sestavu, která se nachází v projektu TDSP **sestavu dat** složka obsahuje data zdroje. V této části Určuje původní a cílové umístění nezpracovaná data. Ve fázích novější je zadat další podrobnosti, třeba skripty pro přesun dat do prostředí analýzy.  
   * [Slovník dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Tento dokument obsahuje popis dat, která je zadaných klientem. Tyto popisy obsahují informace o schématu (datové typy a informace o ověřovacích pravidel, pokud existuje) a entity-relation diagramy, pokud je k dispozici.

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Pochopení obchodních](lifecycle-business-understanding.md)
   2. [Získávání dat a principy](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníka](lifecycle-acceptance.md)

Poskytujeme návody úplného začátku do konce, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, najdete v části [pomocí Azure Machine Learning TDSP](http://aka.ms/datascienceprocess).

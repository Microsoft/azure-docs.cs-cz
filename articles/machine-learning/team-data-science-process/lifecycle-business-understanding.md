---
title: Obchodní vysvětlení fázi životní cyklus vědeckého zpracování týmových dat – Azure | Dokumentace Microsoftu
description: Cíle, úkoly a úkoly pro fázi obchodní vysvětlení vašich projektů datové vědy
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 3d2a6bf5a7e4766ca6205c413dd27fa9a69c16b7
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446717"
---
# <a name="business-understanding"></a>Principy podniku

Tento článek popisuje cíle, úkoly a úkoly spojené s fázi Principy obchodní aplikace zpracování týmových dat vědy (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Schválení zákazníkem**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Zadejte klíč proměnné, které mají sloužit jako model cíle a jejichž související metriky se používají určit úspěch projektu.
* Určení zdroje relevantní data, která má přístup k podnikání nebo je potřeba získat.

## <a name="how-to-do-it"></a>Jak na to
Existují dva hlavní úkoly zákazníky a vyřešené v této fázi: 

   * **Definování cílů**: práce se zákazníkem a další zainteresované uživatele, pochopit a identifikaci obchodních problémů. Mohli formulovat otázky, které definují cílených techniky data science obchodních cílů.
   * **Určení zdrojů dat**: najít relevantní data, která vám pomůže odpovědět na otázky, které definují cíle projektu.

### <a name="define-objectives"></a>Definování cílů
1. Ústředním cílem tohoto kroku je identifikace klíčových obchodních proměnné, které analýzy, které potřebujete k předpovědi. Budeme odkazovat na tyto proměnné jako *model cíle*, a jak určit úspěch projektu používáme metriky související s nimi. Dva příklady takových cílů jsou prognózy prodeje nebo pravděpodobnost pořadí se podvodné.

2. Definování cílů projektu s dotazem a upřesnění "ostrých" dotazy, které jsou relevantní, konkrétní a jednoznačný. Pro datové vědy je proces, který využívá názvů a čísel k odpovědi na tyto otázky. Další informace o sharp zeptat, najdete v článku [postup datové vědy](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) blogu. Obvykle použijete pro datové vědy a strojové učení k zodpovězení pěti typů otázek:
 
   * Kolik nebo kolik? (regrese)
   * Které kategorii? (klasifikace)
   * Které skupiny? (clustery)
   * Je to divné? (detekce anomálií)
   * Která možnost je potřeba vzít? (doporučení)

   Určete, které z těchto otázek budete s dotazem a jak volaného dosahuje vašich obchodních cílů.

3. Definujte týmový projekt tak, že zadáte role a zodpovědnosti z jejích členů. Vývoj plánu vysoké úrovně milník, který budete procházet hotový, co zjistíte další informace. 

4. Definice metriky úspěchu. Můžete například chtít dosáhnout předpověď výpovědi zákazníka. Potřebujete objemem přesnosti "x" procent na konci tohoto projektu tři měsíce. S těmito daty můžete nabízet, že reklamní akce zákazníka ke snížení četnosti změn. Metriky musí být **INTELIGENTNÍ**: 

   * **S**určitá 
   * **M**easurable
   * **A**chievable 
   * **R**elevant 
   * **T**vázané na editoru ime 

### <a name="identify-data-sources"></a>Určení zdrojů dat
Určení zdrojů dat obsahujících známých příkladů odpovědi na své otázky sharp. Vyhledejte následující data:

* Data, která je relevantní pro dotaz. Máte míry cíle a funkce, které se vztahují k cíli?
* Data, která je přesné míra v cílovém modelu a funkce, které vás zajímají.

Například můžete zjistit, že je potřeba stávajících systémů shromažďovat a protokolovat další druhy dat k vyřešení problému a dosáhnout cílů projektu. V takovém případě můžete chtít vyhledat externím zdrojům dat nebo aktualizaci vašich systémů shromažďovat nová data.

## <a name="artifacts"></a>Artefakty
Tady jsou výstupy v této fázi:

   * [Další směrování dokumentu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): standardní šablony je součástí definice struktury TDSP projektu. Další směrování dokumentu je živých dokument. Jak vytvořit nové zjišťování a jako obchodní požadavky se změní aktualizaci šablony v celém projektu. Klíč je pro iteraci na tento dokument, přidání více podrobností, během postupu v procesu zjišťování. Zachovat zákazníka a další zainteresované uživatele zahrnuta v provedení změn a jasně sdělit důvody pro změny k nim.  
   * [Zdroje dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): **nezpracovaná data zdroje** část **definice dat** sestavu, která se nachází v projektu TDSP **sestavu dat** složka obsahuje data zdroje. Tato část určuje původní a cílové umístění pro nezpracovaná data. V pozdějších fázích si vyplňte další podrobnosti, jako jsou skripty pro přesun dat do vašeho analytického prostředí.  
   * [Data slovníky](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Tento dokument obsahuje popis dat, která pochází od klienta. Tyto popisy obsahují informace o schématu (datové typy a informace o ověřovacích pravidel, pokud existuje) a diagramy relace entity, pokud je k dispozici.

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Schválení zákazníkem](lifecycle-acceptance.md)

Poskytujeme úplného začátku do konce návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady toho, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio najdete v tématu [TDSP pomocí služby Azure Machine Learning](https://aka.ms/datascienceprocess).

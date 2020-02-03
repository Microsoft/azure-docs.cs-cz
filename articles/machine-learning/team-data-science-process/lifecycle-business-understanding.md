---
title: Principy podniku v vědecké zpracování týmových dat
description: Cíle, úkoly a úkoly pro fázi obchodní vysvětlení vašich projektů datové vědy v vědecké zpracování týmových dat.
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
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710329"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Obchodní vysvětlení fáze životní cyklus vědeckého zpracování týmových dat

Tento článek popisuje cíle, úkoly a úkoly spojené s fázi Principy obchodní aplikace zpracování týmových dat vědy (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Obchodní porozumění**
   2. **Získávání a porozumění dat**
   3. **Situací**
   4. **Nasazení**
   5. **Přijetí zákazníkovi**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Zadejte klíč proměnné, které mají sloužit jako model cíle a jejichž související metriky se používají určit úspěch projektu.
* Určení zdroje relevantní data, která má přístup k podnikání nebo je potřeba získat.

## <a name="how-to-do-it"></a>Jak na to
Existují dva hlavní úkoly zákazníky a vyřešené v této fázi: 

   * **Definování cílů**: práce se zákazníkem a dalšími zúčastněnými stranami pro pochopení a identifikaci obchodních problémů. Mohli formulovat otázky, které definují cílených techniky data science obchodních cílů.
   * **Identifikace zdrojů dat**: Najděte relevantní data, která vám pomohou zodpovědět otázky definující cíle projektu.

### <a name="define-objectives"></a>Definování cílů
1. Ústředním cílem tohoto kroku je identifikace klíčových obchodních proměnné, které analýzy, které potřebujete k předpovědi. Tyto proměnné odkazujeme jako *cíle modelu*a k určení úspěchu projektu používáme metriky, které jsou k nim přidružené. Dva příklady takových cílů jsou prognózy prodeje nebo pravděpodobnost pořadí se podvodné.

2. Definování cílů projektu s dotazem a upřesnění "ostrých" dotazy, které jsou relevantní, konkrétní a jednoznačný. Pro datové vědy je proces, který využívá názvů a čísel k odpovědi na tyto otázky. Obvykle použijete pro datové vědy a strojové učení k zodpovězení pěti typů otázek:
 
   * Kolik nebo kolik? (regrese)
   * Které kategorii? (klasifikace)
   * Které skupiny? (clustery)
   * Je to divné? (detekce anomálií)
   * Která možnost je potřeba vzít? (doporučení)

   Určete, které z těchto otázek budete s dotazem a jak volaného dosahuje vašich obchodních cílů.

3. Definujte týmový projekt tak, že zadáte role a zodpovědnosti z jejích členů. Vývoj plánu vysoké úrovně milník, který budete procházet hotový, co zjistíte další informace. 

4. Definice metriky úspěchu. Můžete například chtít dosáhnout předpověď výpovědi zákazníka. Potřebujete objemem přesnosti "x" procent na konci tohoto projektu tři měsíce. S těmito daty můžete nabízet, že reklamní akce zákazníka ke snížení četnosti změn. Metriky musí být **inteligentní**: 

   * **S**určitá 
   * Easurable **M**
   * **Chievable** 
   * Elevant jazyka **R** 
   * **T**– vázaný s editorem IME 

### <a name="identify-data-sources"></a>Určení zdrojů dat
Určení zdrojů dat obsahujících známých příkladů odpovědi na své otázky sharp. Vyhledejte následující data:

* Data, která je relevantní pro dotaz. Máte míry cíle a funkce, které se vztahují k cíli?
* Data, která je přesné míra v cílovém modelu a funkce, které vás zajímají.

Například můžete zjistit, že je potřeba stávajících systémů shromažďovat a protokolovat další druhy dat k vyřešení problému a dosáhnout cílů projektu. V takovém případě můžete chtít vyhledat externím zdrojům dat nebo aktualizaci vašich systémů shromažďovat nová data.

## <a name="artifacts"></a>Artefakty
Tady jsou výstupy v této fázi:

   * [Dokument o pronájmu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): v definici struktury projektu TDSP je k dispozici standardní šablona. Další směrování dokumentu je živých dokument. Jak vytvořit nové zjišťování a jako obchodní požadavky se změní aktualizaci šablony v celém projektu. Klíč je pro iteraci na tento dokument, přidání více podrobností, během postupu v procesu zjišťování. Zachovat zákazníka a další zainteresované uživatele zahrnuta v provedení změn a jasně sdělit důvody pro změny k nim.  
   * [Zdroje dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): část **nezpracované zdroje dat** v sestavě **definice dat** , která se nachází ve složce **sestavy dat** projektu TDSP, obsahuje zdroje dat. Tato část určuje původní a cílové umístění pro nezpracovaná data. V pozdějších fázích si vyplňte další podrobnosti, jako jsou skripty pro přesun dat do vašeho analytického prostředí.  
   * [Datové slovníky](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): v tomto dokumentu najdete popis dat poskytovaných klientem. Tyto popisy obsahují informace o schématu (datové typy a informace o ověřovacích pravidel, pokud existuje) a diagramy relace entity, pokud je k dispozici.

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Obchodní porozumění](lifecycle-business-understanding.md)
   2. [Získávání a porozumění dat](lifecycle-data.md)
   3. [Situací](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníkovi](lifecycle-acceptance.md)

Poskytujeme kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

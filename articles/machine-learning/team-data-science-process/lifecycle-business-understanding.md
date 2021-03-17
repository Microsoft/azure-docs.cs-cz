---
title: Obchodní porozumění v procesu vědeckého zpracování týmových dat
description: Cíle, úkoly a dodávky pro obchodní porozumění pro přípravu vašich projektů pro datové vědy v rámci vědeckého zpracování týmových dat.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76710329"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Fáze životního cyklu pracovního procesu vědeckého zpracování dat týmu

Tento článek popisuje cíle, úlohy a dodávky spojené se fází obchodního porozumění v rámci vědeckého zpracování týmových dat (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů pro vědu. Životní cyklus popisuje hlavní fáze, které se obvykle spouštějí projekty, často iterativní:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Tady je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Zadejte klíčové proměnné, které mají sloužit jako cíle modelu a jejichž pomocí se používají související metriky, které určují úspěch projektu.
* Identifikujte relevantní zdroje dat, ke kterým má podnik přístup, nebo potřebujete získat.

## <a name="how-to-do-it"></a>Jak to provést
V této fázi jsou řešeny dva hlavní úkoly: 

   * **Definování cílů**: práce se zákazníkem a dalšími zúčastněnými stranami pro pochopení a identifikaci obchodních problémů. Formulujte otázky, které definují obchodní cíle, které mohou techniky pro datové vědy cílit.
   * **Identifikace zdrojů dat**: Najděte relevantní data, která vám pomohou zodpovědět otázky definující cíle projektu.

### <a name="define-objectives"></a>Definování cílů
1. Hlavním cílem tohoto kroku je určit klíčové obchodní proměnné, které analýza potřebuje k předpovědi. Tyto proměnné odkazujeme jako *cíle modelu*a k určení úspěchu projektu používáme metriky, které jsou k nim přidružené. Dva příklady takových cílů jsou předpovědi prodeje nebo pravděpodobnost, že je objednávka falešná.

2. Definujte cíle projektu tak, že si vyžádáte a Projasněte "ostré" otázky, které jsou relevantní, specifické a jednoznačné. Data vědu je proces, který používá názvy a čísla k zodpovězení těchto otázek. K zodpovězení pěti typů otázek obvykle používáte datové vědy nebo strojové učení:
 
   * Kolik a kolik? nevýhody
   * Kterou kategorii? Mazal
   * Kterou skupinu? Clustering
   * Je tento divné? (detekce anomálií)
   * Jakou možnost byste měli vzít? základě

   Určete, které z těchto otázek budete klást, a jak odpovídáte za dosažení vašich obchodních cílů.

3. Definujte projektový tým zadáním rolí a odpovědností jeho členů. Vytvořte plán milníku vysoké úrovně, na kterém se při zjišťování více informací chystáte. 

4. Definujte metriky úspěchu. Můžete například chtít dosáhnout předpovědi změn zákazníků. Na konci tohoto měsíčního projektu budete potřebovat míru přesnosti "x" procent. Pomocí těchto dat můžete nabídnout propagační akce zákazníků a snížit tak případné změny. Metriky musí být **inteligentní**: 

   * **S**určitá 
   * Easurable **M**
   * **Chievable** 
   * Elevant jazyka **R** 
   * **T**– vázaný s editorem IME 

### <a name="identify-data-sources"></a>Identifikace zdrojů dat
Identifikujte zdroje dat, které obsahují známé příklady odpovědí na vaše ostré otázky. Vyhledejte následující data:

* Data, která jsou pro otázku relevantní. Máte míry cíle a funkcí, které se vztahují k cíli?
* Data, která jsou přesná míra cíle modelu a funkce, které vás zajímají.

Můžete například zjistit, že stávající systémy potřebují shromažďovat a Protokolovat další druhy dat pro vyřešení problému a dosažení cílů projektu. V takové situaci můžete chtít vyhledat externí zdroje dat nebo aktualizovat systémy a shromažďovat nová data.

## <a name="artifacts"></a>Artifacts
Tady jsou dodávky v této fázi:

   * [Dokument o pronájmu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): v definici struktury projektu TDSP je k dispozici standardní šablona. Dokument Chart je živý dokument. Šablonu aktualizujete v rámci projektu při provádění nových zjišťování a změnách obchodních požadavků. Klíč se má iterovat na tomto dokumentu a při průběhu procesu zjišťování přidat další podrobnosti. Zachovejte zákazníkům a dalším zúčastněným účastníkům, kteří mají v úmyslu provádět změny a jasně sdělit důvody, proč se tyto změny týkají.  
   * [Zdroje dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): část **nezpracované zdroje dat** v sestavě **definice dat** , která se nachází ve složce **sestavy dat** projektu TDSP, obsahuje zdroje dat. Tato část určuje původní a cílová umístění pro nezpracovaná data. V pozdějších fázích vyplníte další podrobnosti, například skripty pro přesun dat do vašeho analytického prostředí.  
   * [Datové slovníky](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): v tomto dokumentu najdete popis dat poskytovaných klientem. Tyto popisy obsahují informace o schématu (typy dat a informace o pravidlech ověřování, pokud existují) a diagramy vztahů mezi entitami, pokud jsou k dispozici.

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na jednotlivé kroky v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Návody ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 

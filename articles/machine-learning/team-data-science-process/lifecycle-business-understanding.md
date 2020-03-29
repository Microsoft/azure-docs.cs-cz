---
title: Obchodní porozumění v procesu vědecké ho svědecké ho spoje
description: Cíle, úkoly a výstupy pro fázi obchodního porozumění vašich projektů datových věd v procesu vědecké ho procesu týmových dat.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710329"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Fáze obchodního porozumění životního cyklu procesu teamdata science

Tento článek popisuje cíle, úkoly a dodávky spojené s obchodní fázi porozumění procesu vědecké ho procesu týmových dat (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů datové vědy. Životní cyklus popisuje hlavní fáze, které projekty obvykle provádějí, často iterativně:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Určete klíčové proměnné, které mají sloužit jako cíle modelu a jejichž související metriky se používají určit úspěch projektu.
* Určete příslušné zdroje dat, ke kterým má podnik přístup nebo k nim potřebuje získat.

## <a name="how-to-do-it"></a>Jak na to
V této fázi jsou řešeny dva hlavní úkoly: 

   * **Definujte cíle**: Spolupracujte se zákazníkem a dalšími zúčastněnými stranami na pochopení a identifikaci obchodních problémů. Formulovat otázky, které definují obchodní cíle, na které mohou techniky datové vědy cílit.
   * **Identifikace zdrojů dat**: Najděte příslušná data, která vám pomohou odpovědět na otázky, které definují cíle projektu.

### <a name="define-objectives"></a>Definování cílů
1. Hlavním cílem tohoto kroku je identifikovat klíčové obchodní proměnné, které musí analýza předvídat. Označujeme tyto proměnné jako *cíle modelu*a používáme metriky s nimi spojené k určení úspěchu projektu. Dva příklady takových cílů jsou prognózy prodeje nebo pravděpodobnost podvodného příkazu.

2. Definujte cíle projektu tím, že se zeptáte a zpřesníte "ostré" otázky, které jsou relevantní, konkrétní a jednoznačné. Datová věda je proces, který používá názvy a čísla k zodpovězení těchto otázek. Obvykle používáte datové vědy nebo strojové učení odpovědět na pět typů otázek:
 
   * Kolik nebo kolik? (regrese)
   * Do které kategorie? (klasifikace)
   * Která skupina? (shlukování)
   * Je to divné? (detekce anomálií)
   * Jaká možnost by měla být přijata? (doporučení)

   Určete, které z těchto otázek se ptáte a jak odpovídání na ně dosahuje vašich obchodních cílů.

3. Definujte projektový tým určením rolí a odpovědností jeho členů. Vytvořte plán milníků na vysoké úrovni, který itetujte, když objevíte další informace. 

4. Definujte metriky úspěšnosti. Například můžete chtít dosáhnout předpovědi změn zákazníka. Do konce tohoto tříměsíčního projektu potřebujete míru přesnosti "x" procent. S těmito daty můžete nabídnout propagační akce zákazníků, abyste snížili konve. Metriky musí být **SMART**: 

   * **S**pecific 
   * **M**easurable
   * **A**Chievable 
   * **R**elevant 
   * **T**ime-vázané 

### <a name="identify-data-sources"></a>Identifikace zdrojů dat
Identifikujte zdroje dat, které obsahují známé příklady odpovědí na ostré otázky. Vyhledejte následující údaje:

* Data, která jsou relevantní pro otázku. Máte měřítka cíle a funkce, které souvisejí s cílem?
* Data, která jsou přesným měřítkem cíle modelu a funkcí, které vás zajímají.

Můžete například zjistit, že stávající systémy potřebují shromažďovat a protokolovat další druhy dat k řešení problému a dosažení cílů projektu. V takovém případě můžete chtít vyhledat externí zdroje dat nebo aktualizovat systémy shromažďovat nová data.

## <a name="artifacts"></a>Artefakty
Zde jsou výstupy v této fázi:

   * [Dokument charty](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): Standardní šablona je uvedena v definici struktury projektu TDSP. Listina je živý dokument. Šablonu můžete aktualizovat v celém projektu při nových zjišťováních a při změně obchodních požadavků. Klíčem je iterát na tento dokument, přidání další podrobnosti, jak postupovat v procesu zjišťování. Udržujte zákazníka a další zúčastněné strany zapojené do provádění změn a jasně sdělte důvody změn.  
   * [Zdroje dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): Část **Zdroje nezpracovaných dat** v sestavě Definice **dat,** která se nachází ve složce **sestavy dat** projektu TDSP, obsahuje zdroje dat. Tato část určuje původní a cílové umístění nezpracovaných dat. V pozdějších fázích vyplníte další podrobnosti, jako jsou skripty pro přesunutí dat do analytického prostředí.  
   * [Datové slovníky](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Tento dokument obsahuje popis dat poskytovaných klientem. Tyto popisy zahrnují informace o schématu (datové typy a informace o ověřovacích pravidlech, pokud existuje) a diagramy vztahů s entitami, pokud jsou k dispozici.

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme úplné návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Ukázkový [článek obsahuje](walkthroughs.md) seznam scénářů s odkazy a popisy miniatur. Návody ilustrují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu k vytvoření inteligentní aplikace. 

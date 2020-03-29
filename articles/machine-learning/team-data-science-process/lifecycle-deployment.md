---
title: Fáze nasazení životního cyklu procesu vědecké ho procesu týmových dat
description: Cíle, úkoly a výstupy pro fázi nasazení vašich projektů datové vědy
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
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720482"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Fáze nasazení životního cyklu procesu vědecké ho procesu týmových dat

Tento článek popisuje cíle, úkoly a dodávky spojené s nasazením procesu vědecké vědy o týmových datech (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů datové vědy. Životní cyklus popisuje hlavní fáze, které projekty obvykle provádějí, často iterativně:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cíl
Nasaďte modely s datovým kanálem do produkčního nebo produkčního prostředí pro přijetí konečným uživatelem. 

## <a name="how-to-do-it"></a>Jak na to
Hlavní úkol řešený v této fázi:

**Zprovoznit model**: Nasazení modelu a kanálu do produkčního nebo produkčního prostředí pro spotřebu aplikací.

### <a name="operationalize-a-model"></a>Zprovoznění modelu
Poté, co máte sadu modelů, které fungují dobře, můžete je zprovoznit pro ostatní aplikace využívat. V závislosti na obchodnípožadavky předpovědi jsou prováděny buď v reálném čase nebo na základě dávky. Chcete-li nasadit modely, vystavit je s otevřeným rozhraním API. Rozhraní umožňuje model snadno spotřebovávat z různých aplikací, jako jsou:

   * Online webové stránky
   * Tabulky 
   * Řídicí panely
   * Obchodní aplikace 
   * Back-endové aplikace 

Příklady operacení modelu s webovou službou Azure Machine Learning najdete v [tématu Nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md). Je osvědčeným postupem k vytvoření telemetrie a monitorování do produkčního modelu a datového kanálu, který nasadíte. Tento postup pomáhá s následným hlášením stavu systému a odstraňováním potíží.  

## <a name="artifacts"></a>Artefakty

* Stavový řídicí panel, který zobrazuje stav systému a klíčové metriky
* Závěrečná sestava modelování s podrobnostmi o nasazení
* Dokument o architektuře konečného řešení


## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a porozumění dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme úplné návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Ukázkový [článek obsahuje](walkthroughs.md) seznam scénářů s odkazy a popisy miniatur. Návody ilustrují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu k vytvoření inteligentní aplikace. 

Příklady kroků v TDSP, které používají Azure Machine Learning Studio, najdete [v tématu Použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

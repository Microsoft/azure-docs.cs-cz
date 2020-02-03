---
title: Fáze nasazení životní cyklus vědeckého zpracování týmových dat
description: Cíle, úkoly a úkoly pro fázi nasazení vašich projektů datové vědy
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720482"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Fáze nasazení životní cyklus vědeckého zpracování týmových dat

Tento článek popisuje cíle, úkoly a úkoly spojené s nasazením nástroje zpracování týmových dat vědy (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Obchodní porozumění**
   2. **Získávání a porozumění dat**
   3. **Situací**
   4. **Nasazení**
   5. **Přijetí zákazníkovi**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cíl
Modely s datovým kanálem nasazení do produkčního prostředí nebo prostředí podobném produkci pro přijetí konečného uživatele. 

## <a name="how-to-do-it"></a>Jak na to
Hlavního úkolu zákazníky a vyřešené v této fázi:

**Zprovoznění model**: nasazení modelu a kanálu do výrobního nebo produkčního prostředí pro spotřebu aplikací.

### <a name="operationalize-a-model"></a>Zprovoznění modelu
Až budete mít sadu modely, které dobře fungují, můžete je pro jiné aplikace při využívání provozu. V závislosti na obchodních požadavcích předpovědi probíhají v reálném čase nebo na základě služby batch. Pokud chcete nasadit modely, umístěte je otevřené rozhraní API. Rozhraní umožňuje modelu snadno využít v různých aplikacích, jako například:

   * Online websites
   * Tabulky 
   * Řídicí panely
   * Obchodní aplikace 
   * Back endovým aplikacím 

Příklady provozování modelů pomocí Azure Machine Learning webové služby najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md). Je osvědčeným postupem je vytvoření telemetrie a monitorování do produkčního prostředí modelu a datový kanál, který nasadíte. Tento postup pomáhá se stavem systému následné hlášení a řešení potíží.  

## <a name="artifacts"></a>Artefakty

* Řídicí panel stav, který zobrazuje metriky systému stavu a klíč
* Poslední modelování sestavu s podrobnostmi o nasazení
* Architektura dokumentu konečné řešení


## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Obchodní porozumění](lifecycle-business-understanding.md)
   2. [Získávání a porozumění dat](lifecycle-data.md)
   3. [Situací](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníkovi](lifecycle-acceptance.md)

Poskytujeme kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, naleznete v tématu [použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

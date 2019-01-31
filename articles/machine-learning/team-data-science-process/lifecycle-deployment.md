---
title: Fáze nasazení životní cyklus vědeckého zpracování týmových dat
description: Cíle, úkoly a úkoly pro fázi nasazení vašich projektů datové vědy
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 00710183828892c81d3ea887e4394237288eb6bb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452511"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Fáze nasazení životní cyklus vědeckého zpracování týmových dat

Tento článek popisuje cíle, úkoly a úkoly spojené s nasazením nástroje zpracování týmových dat vědy (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Schválení zákazníkem**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cíl
Modely s datovým kanálem nasazení do produkčního prostředí nebo prostředí podobném produkci pro přijetí konečného uživatele. 

## <a name="how-to-do-it"></a>Jak na to
Hlavního úkolu zákazníky a vyřešené v této fázi:

**Zprovoznění modelu**: Nasazení modelu a kanál do produkčního prostředí nebo prostředí podobném produkci za využití aplikace.

### <a name="operationalize-a-model"></a>Zprovoznění modelu
Až budete mít sadu modely, které dobře fungují, můžete je pro jiné aplikace při využívání provozu. V závislosti na obchodních požadavcích předpovědi probíhají v reálném čase nebo na základě služby batch. Pokud chcete nasadit modely, umístěte je otevřené rozhraní API. Rozhraní umožňuje modelu snadno využít v různých aplikacích, jako například:

   * Online websites
   * Tabulky 
   * Řídicí panely
   * Obchodní aplikace 
   * Back endovým aplikacím 

Příklady operacionalizace modelu pomocí webové služby Azure Machine Learning, najdete v článku [nasazení webové služby Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). Je osvědčeným postupem je vytvoření telemetrie a monitorování do produkčního prostředí modelu a datový kanál, který nasadíte. Tento postup pomáhá se stavem systému následné hlášení a řešení potíží.  

## <a name="artifacts"></a>Artefakty

* Řídicí panel stav, který zobrazuje metriky systému stavu a klíč
* Poslední modelování sestavu s podrobnostmi o nasazení
* Architektura dokumentu konečné řešení


## <a name="next-steps"></a>Další postup

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Schválení zákazníkem](lifecycle-acceptance.md)

Poskytujeme úplného začátku do konce návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady toho, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio najdete v tématu [TDSP pomocí služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

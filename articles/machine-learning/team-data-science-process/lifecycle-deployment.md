---
title: Fáze nasazení životního cyklu procesu vědeckého zpracování dat týmu
description: Cíle, úkoly a dodávky pro fázi nasazení projektů pro vědu pro data
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
ms.openlocfilehash: c3bf8e5f81ae7bf35ff34039fa1e81c9fd4a406b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324547"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Fáze nasazení životního cyklu procesu vědeckého zpracování dat týmu

Tento článek popisuje cíle, úlohy a dodávky spojené s nasazením vědeckého procesu týmového zpracování dat (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů pro vědu. Životní cyklus popisuje hlavní fáze, které se obvykle spouštějí projekty, často iterativní:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Tady je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cíl
Nasaďte modely s datovým kanálem do produkčního nebo produkčního prostředí pro konečné přijetí uživateli. 

## <a name="how-to-do-it"></a>Jak to provést
Hlavní úkol řešený v této fázi:

**Zprovoznění model**: nasazení modelu a kanálu do výrobního nebo produkčního prostředí pro spotřebu aplikací.

### <a name="operationalize-a-model"></a>Zprovoznění modelu
Až budete mít k dispozici sadu modelů, které budou dobře fungovat, můžete je zprovozněníovat pro jiné aplikace, které chcete spotřebovat. V závislosti na podnikových požadavcích se předpovědi provádí buď v reálném čase, nebo na základě dávky. Chcete-li nasadit modely, vystavte je pomocí rozhraní API Open. Rozhraní umožňuje, aby byl model snadno spotřebovaný z různých aplikací, jako například:

   * Online weby
   * Tabulky 
   * Řídicí panely
   * Obchodní aplikace 
   * Back-endové aplikace 

Příklady provozování modelů pomocí Azure Machine Learning webové služby najdete v tématu [nasazení webové služby Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md). Osvědčeným postupem je sestavit telemetrii a monitorování do produkčního modelu a datového kanálu, který nasadíte. Tento postup pomáhá s následným vytvářením zpráv o stavu systému a odstraňování potíží.  

## <a name="artifacts"></a>Artifacts

* Řídicí panel stavu, který zobrazuje stav systému a klíčové metriky
* Finální sestava modelování s podrobnostmi o nasazení
* Konečný dokument architektury řešení


## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na jednotlivé kroky v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a porozumění dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Návody ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, naleznete v tématu [použití TDSP s Azure Machine Learning](./index.yml).
---
title: Fáze přijetí zákazníků v životním cyklu procesu vědeckého zpracování dat týmu
description: Cíle, úkoly a dodávky pro fázi přijetí zákazníků vašich projektů pro vědu pro data
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
ms.openlocfilehash: 7224a7bb26ef491915df9fcb9b6b84ff171a9fc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76720516"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Fáze přijetí zákazníků v životním cyklu procesu vědeckého zpracování dat týmu

Tento článek popisuje cíle, úlohy a dodávky spojené se zákaznickou fází pro vědecké zpracování týmových dat (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů pro vědu. Životní cyklus popisuje hlavní fáze, které se obvykle spouštějí projekty, často iterativní:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Tady je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cíl
**Finalizace dodávek projektu**: Ověřte, že kanál, model a jejich nasazení v produkčním prostředí odpovídají cílům zákazníka.

## <a name="how-to-do-it"></a>Jak to provést
V této fázi jsou řešeny dva hlavní úkoly:

   * **Ověření systému**: Ověřte, že nasazený model a kanál splňují požadavky zákazníka.
   * **Projektový ruka-vypnuto**: projekt je možné předat do entity, ve které se spouští systém v produkčním prostředí.

Zákazník by měl ověřit, že systém splňuje své obchodní potřeby a že odpoví na otázky s přijatelnou přesností pro nasazení systému do produkčního prostředí pro použití jejich aplikací klienta. Všechna dokumentace se dokončuje a přezkoumávají. Projekt je předán do entity zodpovědné za operace. Tato entita může být například tým pro odborníky na IT nebo zákazníky nebo agent zákazníka, který je zodpovědný za provoz systému v produkčním prostředí. 

## <a name="artifacts"></a>Artifacts
Hlavním artefaktem vytvořeným v této konečné fázi je **Zpráva o ukončení projektu pro zákazníka**. Tato technická sestava obsahuje všechny podrobnosti projektu, které jsou užitečné pro získání informací o tom, jak systém provozovat. TDSP poskytuje šablonu [sestavy ukončení](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) . Šablonu můžete použít tak, jak je, nebo ji můžete přizpůsobit pro konkrétní potřeby klienta. 


## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na jednotlivé kroky v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Návody ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, naleznete v tématu [použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

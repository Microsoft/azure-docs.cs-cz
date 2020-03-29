---
title: Fáze přijetí zákazníkem v životním cyklu procesu teamových datových věd
description: Cíle, úkoly a výstupy pro fázi přijímání vašich projektů datových věd
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720516"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Fáze přijetí zákazníkem v životním cyklu procesu teamových datových věd

Tento článek popisuje cíle, úkoly a dodávky spojené s fází přijetí zákazníka procesu vědecké vědy o týmových datech (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů datové vědy. Životní cyklus popisuje hlavní fáze, které projekty obvykle provádějí, často iterativně:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cíl
**Dokončení výstupů projektu**: Potvrďte, že kanál, model a jejich nasazení v produkčním prostředí splňují cíle zákazníka.

## <a name="how-to-do-it"></a>Jak na to
V této fázi jsou řešeny dva hlavní úkoly:

   * **Ověření systému**: Potvrďte, že nasazený model a kanál splňují potřeby zákazníka.
   * **Předání projektu**: Předá projekt entitě, která bude systém ve výrobě spouštět.

Zákazník by měl ověřit, že systém splňuje jejich obchodní potřeby a že odpovídá na otázky s přijatelnou přesností pro nasazení systému do produkčního prostředí pro použití aplikací svého klienta. Veškerá dokumentace je dokončena a přezkoumána. Projekt je předán subjektu odpovědnému za operace. Tato entita může být například it nebo zákaznický datový vědecký tým nebo agent zákazníka, který je zodpovědný za spuštění systému v produkčním prostředí. 

## <a name="artifacts"></a>Artefakty
Hlavním artefaktem vytvořeným v této konečné fázi je **výstupní zpráva projektu pro zákazníka**. Tato technická zpráva obsahuje všechny podrobnosti o projektu, které jsou užitečné pro učení o tom, jak systém provozovat. TDSP poskytuje šablonu [sestavy Ukončení.](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) Šablonu můžete použít tak, jak je, nebo ji můžete přizpůsobit pro konkrétní potřeby klienta. 


## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme úplné návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Ukázkový [článek obsahuje](walkthroughs.md) seznam scénářů s odkazy a popisy miniatur. Návody ilustrují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu k vytvoření inteligentní aplikace. 

Příklady kroků v TDSP, které používají Azure Machine Learning Studio, najdete [v tématu Použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

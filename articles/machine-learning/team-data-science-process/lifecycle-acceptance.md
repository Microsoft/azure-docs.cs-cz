---
title: Fáze přijetí zákazníky životního cyklu vědecké zpracování týmových dat
description: Cíle, úkoly a úkoly pro zákazníka fáze přijetí vašich projektů datové vědy
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720516"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Fáze přijetí zákazníky životního cyklu vědecké zpracování týmových dat

Tento článek popisuje cíle, úkoly a úkoly spojené s fáze přijetí zákazníka z týmu Data Science procesu (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Obchodní porozumění**
   2. **Získávání a porozumění dat**
   3. **Situací**
   4. **Nasazení**
   5. **Přijetí zákazníkovi**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cíl
**Finalizace dodávek projektu**: Ověřte, že kanál, model a jejich nasazení v produkčním prostředí odpovídají cílům zákazníka.

## <a name="how-to-do-it"></a>Jak na to
Existují dva hlavní úkoly zákazníky a vyřešené v této fázi:

   * **Ověření systému**: Ověřte, že nasazený model a kanál splňují požadavky zákazníka.
   * **Projektový ruka-vypnuto**: projekt je možné předat do entity, ve které se spouští systém v produkčním prostředí.

Zákazník měli ověřit, že systém splňuje svých obchodních potřeb a odpovídá dotazy s přijatelnou přesnost k nasazení systému ve své klientské aplikace do produkčního prostředí pro použití. V dokumentaci se dokončí a zkontrolovat. Projekt je předat schválení v entitě za operace. Tato entita může být například IT nebo zákaznický tým pro datové vědy nebo agenta, který je zodpovědný za spouštění systému v produkčním prostředí zákazníka. 

## <a name="artifacts"></a>Artefakty
Hlavním artefaktem vytvořeným v této konečné fázi je **Zpráva o ukončení projektu pro zákazníka**. Tato technická sestava obsahuje všechny podrobnosti o projektu, které jsou užitečné pro získání informací o tom, jak pracovat v systému. TDSP poskytuje šablonu [sestavy ukončení](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) . Šablonu je můžete použít nebo si můžete přizpůsobit potřebám konkrétního klienta. 


## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Obchodní porozumění](lifecycle-business-understanding.md)
   2. [Získávání a porozumění dat](lifecycle-data.md)
   3. [Situací](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníkovi](lifecycle-acceptance.md)

Poskytujeme kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, naleznete v tématu [použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

---
title: Fáze přijetí zákazníka životní cyklus vědeckého zpracování týmových dat – Azure | Dokumentace Microsoftu
description: Cíle, úkoly a úkoly pro zákazníka fáze přijetí vašich projektů datové vědy
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 9dd3ab8c9451ecfe6b095b52f5af083a7c7e9550
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232755"
---
# <a name="customer-acceptance"></a>Přijetí zákazníky

Tento článek popisuje cíle, úkoly a úkoly spojené s fáze přijetí zákazníka z týmu Data Science procesu (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Schválení zákazníkem**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cíl
**Dokončení výstupy projektu**: Ověřte, že kanál, model a jejich nasazení v produkčním prostředí splňovat cíle zákazníka.

## <a name="how-to-do-it"></a>Jak na to
Existují dva hlavní úkoly zákazníky a vyřešené v této fázi:

   * **Ověření systému**: potvrďte, že nasazený model a kanál vyhovět potřebám zákazníků.
   * **Projekt odevzdání**: projekt přebírají entity, která bude k provozování systému v produkčním prostředí.

Zákazník měli ověřit, že systém splňuje svých obchodních potřeb a odpovídá dotazy s přijatelnou přesnost k nasazení systému ve své klientské aplikace do produkčního prostředí pro použití. V dokumentaci se dokončí a zkontrolovat. Projekt je předat schválení v entitě za operace. Tato entita může být například IT nebo zákaznický tým pro datové vědy nebo agenta, který je zodpovědný za spouštění systému v produkčním prostředí zákazníka. 

## <a name="artifacts"></a>Artefakty
Hlavní artefakt vytvořen v této fázi konečné je **ukončit sestavu projektu pro zákazníka**. Tato technická sestava obsahuje všechny podrobnosti o projektu, které jsou užitečné pro získání informací o tom, jak pracovat v systému. Poskytuje TDSP [ukončit sestavy](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) šablony. Šablonu je můžete použít nebo si můžete přizpůsobit potřebám konkrétního klienta. 


## <a name="next-steps"></a>Další postup

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Schválení zákazníkem](lifecycle-acceptance.md)

Poskytujeme úplného začátku do konce návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady toho, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio najdete v tématu [TDSP pomocí služby Azure Machine Learning](https://aka.ms/datascienceprocess).

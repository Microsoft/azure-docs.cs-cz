---
title: Zákazník přijetí fáze životního cyklu proces vědecké účely dat Team - Azure | Microsoft Docs
description: Cíle, úlohy a úkoly pro fázi přijetí zákaznické projekty vědecké zpracování dat
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 0030698bafc3d92a1c6daf944ba496421080912c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="customer-acceptance"></a>Přijetí zákazníky

Tento článek popisuje cíle, úlohy a úkoly spojené s fází zákazníka přijetí nástroje Team Data vědecké účely procesu (TDSP). Tento proces zajišťuje doporučené životního cyklu, který můžete použít pro struktury vědecké zpracování dat projekty. Životní cyklus popisuje hlavní fází, které projekty obvykle provést, často interaktivně:

   1. **Pochopení obchodních**
   2. **Získávání dat a principy**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníka**

Zde je vizuální reprezentace životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cíl
**Dokončení dodávky projektu**: Ověřte, že kanál, model a jejich nasazení v produkčním prostředí splňují cíle zákazníka.

## <a name="how-to-do-it"></a>Jak to udělat
Existují dvě hlavní úlohy v této fázi řešit:

   * **Ověřování systému**: Zkontrolujte, zda nasazené modelu a kanálu splňují požadavky zákazníka.
   * **Projekt ruční vypnout**: přebírají projektu na typ entity, která přechází k provozování systému v produkčním prostředí.

Zákazník musí ověřit, zda systém splňuje jejich obchodních potřeb a že odpovídá na dotazy s přijatelnou přesnost k nasazení systému do produkčního prostředí pro použití ve své klientské aplikace. Veškerá dokumentace je dokončen a zkontrolovat. Projekt je předat odhlášení na starost operations entitu. Tato entita může být například IT nebo tým vědecké zpracování dat zákazníka nebo agenta zákazníka, která je zodpovědná za spuštění systému v produkčním prostředí. 

## <a name="artifacts"></a>Artefakty
Hlavní artefaktů vytvořeného v této konečné fázi je **ukončete sestavy projektu pro zákazníka**. Tato sestava technické obsahuje všechny podrobností projektu, které jsou užitečné pro získání informací o tom, jak pracovat v systému. Poskytuje TDSP [ukončete sestavy](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) šablony. Šablonu, protože je můžete použít nebo si můžete přizpůsobit pro konkrétního klienta potřeby. 


## <a name="next-steps"></a>Další postup

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Pochopení obchodních](lifecycle-business-understanding.md)
   2. [Získávání dat a principy](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníka](lifecycle-acceptance.md)

Poskytujeme návody úplného začátku do konce, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, najdete v části [pomocí Azure Machine Learning TDSP](http://aka.ms/datascienceprocess).

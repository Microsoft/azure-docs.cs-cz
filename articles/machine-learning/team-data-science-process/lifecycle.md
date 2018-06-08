---
title: Životní cyklus proces vědecké účely dat Team – Azure | Microsoft Docs
description: Kroky potřebné k provedení projekty vědecké zpracování dat
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: dca71a13db33f44fed551e4791625c5667cfdc21
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837493"
---
# <a name="the-team-data-science-process-lifecycle"></a>Životní cyklus procesu Team dat vědecké účely

Proces pro vědecké účely Data Team (TDSP) poskytuje doporučené životního cyklu, který můžete použít pro struktury vědecké zpracování dat projekty. Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění. Pokud používáte jiné životního cyklu vědecké zpracování dat, jako je například křížové oborový Standard proces pro dolování dat [(OSTRÉ-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), zjišťování znalostní báze v databázích [(KDD)](https://wikipedia.org/wiki/Data_mining#Process), nebo vlastní proces vaší organizace , můžete dál používat TDSP založený na úlohách. 

Tohoto životního cyklu je určená pro projekty vědecké zpracování dat, které jsou určeny k dodávají jako součást inteligentní aplikace. Tyto aplikace nasadit machine learning nebo umělé intelligence modely pro prediktivní analýzy. Projekty nahodilého vědecké zpracování dat a ad hoc analytics projekty mohou také těžit z používání tohoto procesu. Ale pro tyto projekty některé z uvedeného postupu nemusí být potřeba. 

TDSP životní cyklus se skládá z pěti hlavních fází, které jsou spouštěny interaktivně. K těmto fázím patří:

   1. [Pochopení obchodních](lifecycle-business-understanding.md)
   2. [Získávání dat a principy](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníka](lifecycle-acceptance.md)

Zde je vizuální reprezentace životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Životní cyklus TDSP je modelovaná jako pořadí vstupní kroků, které poskytují pokyny na úlohách, které jsou potřeba k použití prediktivní modely. Můžete nasazovat prediktivní modely v provozním prostředí, které chcete použít k vytvoření inteligentní aplikace. Cílem tohoto životního cyklu proces je nadále přesunout vědecké zpracování dat projektu směrem k zrušte zapojení koncového bodu. Vědecké zpracování dat je cvičení pro výzkum a zjišťování. Schopnost komunikovat úlohy pro váš tým a vašich zákazníků s využitím dobře definované sadě artefaktů, které využívají standardizovaných šablon pomáhá zamezit nedorozuměním. Tyto šablony také zvyšuje šance úspěšné dokončení komplexní vědecké zpracování dat projektu.

Pro každou fázi jsme zadejte následující informace:

   * **Cíle**: specifické cíle.
   * **Jak to udělat**: Přehled konkrétní úlohy a pokyny k jejich dokončení.
   * **Artefakty**: dodávky a podporu k jejich vytvoření.

## <a name="next-steps"></a>Další postup

Poskytujeme návody úplného začátku do konce, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, najdete v části [pomocí Azure Machine Learning TDSP](http://aka.ms/datascienceprocess).

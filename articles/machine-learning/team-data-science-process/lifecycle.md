---
title: Životní cyklus vědeckého zpracování týmových dat – Azure | Dokumentace Microsoftu
description: Kroky potřebné ke spuštění vašich projektů datové vědy
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
ms.openlocfilehash: 611793297559c99ffa46f7c0266ed6865eb650fd
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234268"
---
# <a name="the-team-data-science-process-lifecycle"></a>Životní cyklus vědeckého zpracování týmových dat

Zpracování vědy dat (TDSP) týmu poskytuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění. Pokud používáte jiné životního cyklu datové vědy, jako je například standardní oboru pro různé proces pro dolování dat [(OSTRÉ-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), Knowledge Discovery v databázích [(KDD)](https://wikipedia.org/wiki/Data_mining#Process), nebo vaše vlastní proces, který vlastní organizace , můžete dál používat TDSP založené na úlohách. 

Tento životní cyklus je určená pro projekty pro datové vědy, které mají za cíl dodávají jako součást inteligentní aplikace. Tyto aplikace nasadit modely strojového učení a umělé inteligence pro prediktivní analýzy. Projekty průzkumné datové vědy a ad hoc analytics projekty mohou také těžit z používání tohoto procesu. Ale pro projekty, některé z kroků, je zde popsáno, nemusí být potřeba. 

TDSP životní cyklus se skládá z pěti hlavních fází, které jsou spouštěny zavádět postupně. K těmto fázím patří:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Schválení zákazníkem](lifecycle-acceptance.md)

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Životní cyklus TDSP je modelovaná jako posloupnost iterovaném kroky, které poskytují pokyny na úkoly, které jsou nutné k využití prediktivních modelů. Můžete nasazovat prediktivní modely v produkčním prostředí, který chcete použít k sestavování inteligentních aplikací. Cílem tohoto životního cyklu procesu je dál přecházejí projektu datové vědy směrem k vymazat zapojení koncového bodu. Pro datové vědy je cvičení pro výzkum a zjišťování. Schopnost komunikovat úkoly pro váš tým a vaše zákazníky prostřednictvím kvalitně definované sady artefakty, které využívají standardizovaných šablon pomáhá vyhnout se nedorozuměním. Pomocí těchto šablon také zvyšuje šanci na úspěšné dokončení projektu komplexní datové vědy.

Pro každou fázi jsme zadejte následující informace:

   * **Cíle**: specifické cíle.
   * **Jak na to**: Přehled konkrétní úlohy a pokyny pro jejich provedení.
   * **Artefakty**: výstupy a podporu k jejich vytvoření.

## <a name="next-steps"></a>Další postup

Poskytujeme úplného začátku do konce návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady toho, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio najdete v tématu [TDSP pomocí služby Azure Machine Learning](https://aka.ms/datascienceprocess).

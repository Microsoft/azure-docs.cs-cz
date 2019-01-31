---
title: Životní cyklus vědeckého zpracování týmových dat
description: Kroky potřebné ke spuštění vašich projektů datové vědy
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
ms.openlocfilehash: 40d1af6e6258b5026853532f7963a76d4fc389db
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469222"
---
# <a name="the-team-data-science-process-lifecycle"></a>Životní cyklus vědeckého zpracování týmových dat

Zpracování vědy dat (TDSP) týmu poskytuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění. Pokud používáte jiné životního cyklu datové vědy, jako je například standardní oboru pro různé proces pro dolování dat [(OSTRÉ-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), Knowledge Discovery v databázích [(KDD)](https://wikipedia.org/wiki/Data_mining#Process), nebo vaše vlastní proces, který vlastní organizace , můžete dál používat TDSP založené na úlohách. 

Tento životní cyklus je určená pro projekty pro datové vědy, které mají za cíl dodávají jako součást inteligentní aplikace. Tyto aplikace nasadit modely strojového učení a umělé inteligence pro prediktivní analýzy. Projekty průzkumné datové vědy a ad hoc analytics projekty mohou také těžit z používání tohoto procesu. Ale pro projekty, některé z kroků, je zde popsáno, nemusí být potřeba. 

## <a name="five-lifecycle-stages"></a>Pět fází životního cyklu

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

   * **Cíle**: Specifické cíle.
   * **Jak na to**: Přehled konkrétní úlohy a pokyny pro jejich provedení.
   * **Artefakty**: Výstupy a podporu k jejich vytvoření.

## <a name="next-steps"></a>Další postup

Poskytujeme úplného začátku do konce návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady toho, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio najdete v tématu [TDSP pomocí služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

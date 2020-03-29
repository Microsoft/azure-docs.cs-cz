---
title: Životní cyklus procesu týmové datové vědy
description: Proces vědecké ho procesu týmových dat (TDSP) poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů datové vědy.
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
ms.openlocfilehash: a043a1655950f3ed7688e59352f8a912146e12c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720448"
---
# <a name="the-team-data-science-process-lifecycle"></a>Životní cyklus procesu týmové datové vědy

Proces vědecké ho procesu týmových dat (TDSP) poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů datové vědy. Životní cyklus popisuje úplné kroky, které úspěšné projekty následují. Pokud používáte jiný životní cyklus datové vědy, například proces mezioborového standardu pro dolování dat [(CRISP-DM),](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)zjišťování znalostí v databázích [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)nebo vlastní proces vaší organizace, můžete stále používat tdsp založený na úlohách. 

Tento životní cyklus je určen pro projekty datové vědy, které jsou určeny k odeslání jako součást inteligentních aplikací. Tyto aplikace nasazují modely strojového učení nebo umělé inteligence pro prediktivní analýzu. Využití tohoto procesu mohou těžit také průzkumné projekty v oblasti datových věd a improvizované analytické projekty. Ale pro tyto projekty, některé z kroků popsaných zde nemusí být potřeba. 

## <a name="five-lifecycle-stages"></a>Pět fází životního cyklu

Životní cyklus TDSP se skládá z pěti hlavních fází, které jsou prováděny iterativně. Mezi tyto fáze patří:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Životní cyklus TDSP je modelován jako posloupnost iterovaných kroků, které poskytují pokyny k úkolům potřebným k použití prediktivních modelů. Nasadíte prediktivní modely v produkčním prostředí, které chcete použít k sestavení inteligentních aplikací. Cílem tohoto životního cyklu procesu je pokračovat v přesunu projektu datové vědy směrem k jasnému koncovému bodu zapojení. Datová věda je cvičení ve výzkumu a objevování. Schopnost komunikovat úkoly s týmem a zákazníky pomocí dobře definované sady artefaktů, které používají standardizované šablony, pomáhá zabránit nedorozuměním. Použití těchto šablon také zvyšuje pravděpodobnost úspěšného dokončení komplexního projektu datové vědy.

Pro každou fázi poskytujeme následující informace:

   * **Cíle**: Specifické cíle.
   * **Jak na to**: Přehled konkrétních úkolů a pokyny, jak je dokončit.
   * **Artefakty**: Výstupy a podpora jejich výroby.

## <a name="next-steps"></a>Další kroky

Poskytujeme úplné kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Ukázkový [článek obsahuje](walkthroughs.md) seznam scénářů s odkazy a popisy miniatur. Návody ilustrují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu k vytvoření inteligentní aplikace. 

Příklady kroků v TDSP, které používají Azure Machine Learning Studio, najdete [v tématu Použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

---
title: Životní cyklus procesu vědeckého zpracování dat týmu
description: Vědecké zpracování týmových dat (TDSP) poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů pro vědu.
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
ms.openlocfilehash: 00efe89314d4a1a5c3302e820b8609adf194aa59
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053244"
---
# <a name="the-team-data-science-process-lifecycle"></a>Životní cyklus procesu vědeckého zpracování dat týmu

Vědecké zpracování týmových dat (TDSP) poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů pro vědu. Životní cyklus popisuje kroky od začátku do konce, které projekty obvykle následují při jejich spuštění. Pokud použijete jiný životní cyklus pro datové vědy, jako je například standardní proces pro dolování dat [(špičkový DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), zjišťování znalostí v databázích [(konference KDD)](https://wikipedia.org/wiki/Data_mining#Process)nebo vlastní vlastní proces vaší organizace, můžete i nadále používat TDSP založenou na úlohách. 

Tento životní cyklus je určený pro projekty pro datové vědy, které slouží k dodávání v rámci inteligentních aplikací. Tyto aplikace nasazují modely strojového učení nebo uměle Intelligence pro prediktivní analýzy. Používání tohoto procesu může být výhodné i pro průzkumné projekty pro datové vědy a projekty ad hoc analýzy. U těchto projektů ale některé kroky popsané tady nemusí být potřeba. 

## <a name="five-lifecycle-stages"></a>Pět fází životního cyklu

Životní cyklus TDSP se skládá z pěti hlavních fází, které se spouštějí iterativním. Mezi tyto fáze patří:

   1. [Obchodní porozumění](lifecycle-business-understanding.md)
   2. [Získávání a porozumění dat](lifecycle-data.md)
   3. [Situací](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníkovi](lifecycle-acceptance.md)

Tady je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


Životní cyklus TDSP je modelován jako posloupnost iterací kroků, které poskytují pokyny k úlohám, které jsou potřeba k používání prediktivních modelů. Prediktivní modely nasadíte do produkčního prostředí, které plánujete použít k vytváření inteligentních aplikací. Cílem tohoto životního cyklu procesu je pokračovat v přesunutí projektu pro datové vědy směrem k jasnému koncovému bodu zapojení. Datové vědy je cvičení v oblasti výzkumu a zjišťování. Schopnost komunikovat úkoly týmu a zákazníkům pomocí dobře definované sady artefaktů, které využívají standardizované šablony, pomáhají vyhnout se nedorozuměním. Použití těchto šablon také zvyšuje možnost úspěšného dokončení komplexního projektu vědeckého zpracování dat.

Pro každou fázi poskytujeme následující informace:

   * **Cíle**: konkrétní cíle.
   * **Jak to udělat**: Osnova konkrétních úkolů a pokyny k jejich dokončení.
   * **Artefakty**: dodávky a podpora pro jejich vyprodukování.

## <a name="next-steps"></a>Další kroky

Poskytujeme kompletní podrobné návody, které znázorňují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Návody ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, naleznete v tématu [použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

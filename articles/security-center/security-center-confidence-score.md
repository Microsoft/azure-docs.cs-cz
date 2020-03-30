---
title: Skóre spolehlivosti v Azure Security Center | Dokumenty společnosti Microsoft
description: Security Center generuje skóre spolehlivosti, které pomáhá vašemu týmu určit, zda je hrozba legitimní a jak trialovat a upřednostňovat výstrahy.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 8aa17f473c550c99d91862754e6a746575aca202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604381"
---
# <a name="alert-confidence-score-preview"></a>Skóre spolehlivosti upozornění (náhled)

Azure Security Center poskytuje přehled o prostředcích, které spouštěte v Azure, a upozorní vás, když zjistí potenciální problémy. Objem výstrah může být náročné pro operační tým zabezpečení individuálně řešit a je nutné stanovit prioritu, které výstrahy prozkoumat. Zkoumání výstrah může být složité a časově náročné a v důsledku toho jsou některé výstrahy ignorovány.

Skóre spolehlivosti (aktuálně ve verzi Preview) v Centru zabezpečení může pomoci týmu tažit a určit prioritu výstrah. Security Center automaticky aplikuje osvědčené postupy v oboru, inteligentní algoritmy a procesy používané analytiky k určení, zda je hrozba legitimní, a poskytuje vám smysluplné poznatky ve formě skóre spolehlivosti.

## <a name="how-the-confidence-score-is-triggered"></a>Jak se aktivuje skóre spolehlivosti

Výstrahy jsou generovány při zjištění podezřelých procesů spuštěných na virtuálních počítačích. Security Center kontroluje a analyzuje tyto výstrahy na virtuálních počítačích s Windows spuštěných v Azure. Provádí automatizované kontroly a korelace pomocí pokročilých algoritmů napříč různými entitami a zdroji dat v celé organizaci a ve všech vašich prostředcích Azure a představuje skóre spolehlivosti, které je měřítkem toho, jak je Security Center jisté. že záznam je skutečný a je třeba jej prošetřit.

## <a name="understanding-the-confidence-score"></a>Pochopení skóre spolehlivosti

Skóre spolehlivosti se pohybuje mezi 1 a 100 a představuje důvěru Security Center má, že výstraha je třeba prozkoumat. Čím vyšší je skóre, tím jistější centrum zabezpečení je, že výstraha označuje skutečnou škodlivou aktivitu. Skóre spolehlivosti obsahuje seznam hlavních důvodů, proč výstraha obdržela své skóre spolehlivosti. Skóre spolehlivosti usnadňuje bezpečnostním analytikům upřednostnit jejich reakci na výstrahy a řešit nejnaléhavější útoky jako první, což nakonec zkrátí dobu, kterou je zapotřebí k reakci na útoky a porušení.

Chcete-li zobrazit skóre spolehlivosti:
- Na portálu Centra zabezpečení otevřete okno Výstrahy zabezpečení.
-  Výstrahy a incidenty jsou uspořádány od nejvyšší po nejnižší, což znamená, že čím jistější centrum zabezpečení je, že výstraha představuje hrozbu, tím blíže je k horní části stránky. 


 ![Skóre spolehlivosti][1]

Zobrazení dat, která přispěla k důvěře Centra zabezpečení v výstrahu:
- V okně výstrahy zabezpečení v části **Důvěra**zobrazte pozorování, která přispěla ke skóre spolehlivosti, a získejte přehledy související s výstrahou. To vám poskytne další přehled o povaze aktivit, které způsobily výstrahu.

  ![Podezřelé skóre spolehlivosti][2]

Pomocí skóre spolehlivosti Centra zabezpečení můžete určit prioritu třídění výstrah ve vašem prostředí. Skóre spolehlivosti vám ušetří čas a úsilí tím, že automaticky zkoumáte výstrahy, aplikujete osvědčené postupy v oboru a inteligentní algoritmy a působíte jako virtuální analytik, abyste zjistili, které hrozby jsou skutečné a kde je třeba zaměřit svou pozornost.


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png

---
title: Skóre spolehlivosti v Azure Security Center | Microsoft Docs
description: " Naučte se pracovat s Azure Security Centerm skóre spolehlivosti. "
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
ms.openlocfilehash: 0f0380ca4a285d9cb4a8472a40c77130a8f63035
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202381"
---
# <a name="alert-confidence-score"></a>Skóre spolehlivosti výstrah 

Azure Security Center vám poskytne přehled o prostředcích, které spouštíte v Azure, a upozorní vás, když zjistí možné problémy. Objem výstrah může být náročný na to, že tým provozu zabezpečení individuálně adresuje a že je potřeba stanovit prioritu, které výstrahy se mají prozkoumat. Zkoumání výstrah může být složité a časově náročné a v důsledku toho se některé výstrahy ignorují.

Skóre spolehlivosti v Security Center může vašemu týmu pomáhat při třídění a určování priorit výstrah. Security Center automaticky používá osvědčené postupy, inteligentní algoritmy a procesy používané analytiky k určení, jestli je hrozba legitimní a poskytuje smysluplné přehledy ve formě skóre spolehlivosti.

## <a name="how-the-confidence-score-is-triggered"></a>Způsob aktivace skóre spolehlivosti

Výstrahy se generují při zjištění podezřelých procesů spuštěných na virtuálních počítačích. Security Center zkontroluje a analyzuje tyto výstrahy na virtuálních počítačích s Windows, které běží v Azure. Provádí automatizované kontroly a korelace pomocí pokročilých algoritmů napříč různými entitami a zdroji dat v celé organizaci a všech vašich prostředků Azure a představuje hodnocení spolehlivosti, které je mírou toho, jak jsou Security Center. Tato výstraha je pravá a je nutné ji prozkoumat.

## <a name="understanding-the-confidence-score"></a>Princip hodnocení spolehlivosti

Skóre spolehlivosti v rozsahu od 1 do 100 a představuje Security Center spolehlivosti, že výstraha musí být prověřena. Čím vyšší je skóre, tím Security Centerější znamená, že výstraha indikuje skutečnou škodlivou aktivitu. Hodnocení spolehlivosti zahrnuje seznam hlavních důvodů, proč výstraha obdržela své skóre spolehlivosti. Skóre spolehlivosti usnadňuje analytikům v zabezpečení určit prioritu reakcí na výstrahy a vyřešit většinu útoků jako první a nakonec zkrátit dobu potřebnou k reakci na útoky a porušení.

Postup zobrazení skóre spolehlivosti:
- Na portálu Security Center otevřete okno výstrahy zabezpečení.
-  Výstrahy a incidenty jsou seřazené od nejvyšší po nejnižší, což znamená, že se Security Center znamená, že výstraha představuje hrozbu, blíže k hornímu okraji stránky. 


 ![Skóre spolehlivosti][1]

Zobrazení dat, která přispěla k obavám Security Center v upozornění:
- V okně Výstraha zabezpečení můžete v části **důvěrné**zobrazit pozorování, která přispěla ke skóre spolehlivosti, a získat přehledy týkající se výstrahy. Získáte tak další informace o povaze aktivit, které výstrahu způsobily.

  ![Podezřelé skóre spolehlivosti][2]

Pomocí hodnocení spolehlivosti Security Center můžete určit prioritu třídění výstrah ve vašem prostředí. Skóre spolehlivosti šetří čas a úsilí tím, že automaticky prozkoumá výstrahy, uplatní osvědčené postupy a inteligentní algoritmy a působí jako virtuální analytik a určí, které hrozby jsou reálné a kde je potřeba zaměřit pozornost.


## <a name="next-steps"></a>Další kroky
Tento článek vysvětluje, jak používat hodnocení spolehlivosti k určení priorit šetření výstrah. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png

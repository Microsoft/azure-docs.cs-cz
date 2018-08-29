---
title: Skóre spolehlivosti ve službě Azure Security Center | Dokumentace Microsoftu
description: " Zjistěte, jak pracovat s Azure Security Center skóre spolehlivosti. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: d2998e9fe264d7919c8f74a98aaf8da6d701c0da
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43132794"
---
# <a name="alert-confidence-score"></a>Skóre spolehlivosti 

Azure Security Center vám poskytuje přehled napříč prostředky, které běží na Azure a vás upozorní, když zjistí potenciální problémy. Objem výstrah může být náročné pro tým operace zabezpečení k řešení jednotlivě a bude nutné nastavení priority výstrah, které chcete prozkoumat. Prošetřování výstrah může být složité a časově náročné a v důsledku toho některé výstrahy jsou ignorovány.

Skóre spolehlivosti ve službě Security Center může pomoci vašeho týmu třídění a určit prioritu výstrah. Security Center automaticky použije doporučené postupy, inteligentní algoritmy a procesy používané modulem analytici určit, zda je legitimní hrozbu a nabízí smysluplné přehledy v podobě skóre spolehlivosti.

## <a name="how-the-confidence-score-is-triggered"></a>Jak se aktivuje skóre spolehlivosti

Výstrahy jsou generovány při zjištění podezřelých procesů běžících na virtuálních počítačích. Security Center kontroluje a analyzuje tyto výstrahy virtuálních počítačích s Windows v Azure. Provádí automatizované kontroly a korelací pomocí pokročilých algoritmů pro různé entity a zdroje dat v organizaci a všech vašich prostředků Azure a nabídne s jistotou skóre, které je míra důvěru Security Center je, že výstraha pravý a měly prozkoumat, je potřeba.

## <a name="understanding-the-confidence-score"></a>Principy skóre spolehlivosti

Skóre spolehlivosti v rozsahu 1 až 100 a představuje získáte jistotu potřebnou má služba Security Center, že výstraha by měl se měl prozkoumat. Čím vyšší je toto skóre, větší jistotu Security Center je, že upozornění informuje o originální škodlivých aktivit. Skóre spolehlivosti obsahuje seznam hlavních důvodů, proč výstrahy přijaté jeho skóre spolehlivosti. Skóre spolehlivosti usnadňuje analytikům zabezpečení a upřednostnit jejich reakce na výstrahy a řešit většinu nejprve klávesy útoky, takže v konečném důsledku snížení množství čas potřebný na útoky a porušení.

Chcete-li zobrazit skóre spolehlivosti:
- Na portálu Security Center otevřete okno výstrahy zabezpečení.
-  Výstrah a incidentů seřazené od nejvyšší k nejnižší, což znamená, že si větší jistotu Security Center je, že výstraha představuje hrozbu, čím blíže, že je do horní části stránky. 


 ![Skóre spolehlivosti][1]

Chcete-li zobrazit data, která přispívají k Security Center důvěru ve výstraze:
- Na zabezpečení upozorní okno, v části **spolehlivosti**, zobrazit poznámky, které přispěly k skóre spolehlivosti a získání přehledů souvisejících s danou výstrahou. To vám poskytuje podobné informace o povaze aktivity, které výstrahu způsobil.

 ![Skóre spolehlivosti podezřelé][2]

Skóre spolehlivosti pomocí Security Center k určení priority výstrah třídění ve vašem prostředí. Skóre spolehlivosti vám šetří čas a úsilí automaticky prošetřování výstrah, použitím doporučené postupy a inteligentním algoritmům provedou a funguje jako virtuální analytik a zjistěte jaké hrozby jsou skutečné a které je potřeba zaměřit vaši pozornost.


## <a name="next-steps"></a>Další postup
Tento článek vysvětlil, jak používat skóre spolehlivosti k určení priority výstrahy vyšetřování. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png

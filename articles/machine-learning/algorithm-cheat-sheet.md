---
title: Algoritmus strojového učení Cheat Sheet
titleSuffix: Azure Machine Learning
description: Tisknutelný algoritmus strojového učení Cheat Sheet vám pomůže vybrat správný algoritmus pro prediktivní model v Návrháři Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: 85fbb1c1d26f71903adab2eb96b0c1dd3bf74c33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328607"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Cheatový list algoritmu strojového učení pro návrháře Azure Machine Learning

**Cheat sheet algoritmu s algoritmem strojového učení Azure** vám pomůže vybrat správný algoritmus pro model prediktivní analýzy.

Azure Machine Learning má velkou knihovnu algoritmů z ***klasifikace***, ***doporučující systémy***, ***clustering***, ***detekce anomálií***, ***regrese***a ***analýzy textu rodiny.*** Každý z nich je navržen tak, aby řešil jiný typ problému strojového učení.

Další pokyny naleznete v tématu [Jak vybrat algoritmy](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Ke stažení: Algoritmus strojového učení Cheat Sheet

**Stáhněte si tahák zde: [Machine Learning Algorithm Cheat Sheet (11x17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Algoritmus strojového učení Cheat Sheet: Naučte se, jak si vybrat algoritmus strojového učení.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Stáhněte si a vytiskněte algoritmus strojového učení Cheat Sheet v bulvární velikosti, aby to po ruce a získat pomoc při výběru algoritmu.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Jak používat algoritmus strojového učení Cheat Sheet

Návrhy nabízené v tomto algoritmu tahák jsou přibližné pravidla-of-palec. Některé mohou být ohnuté a některé mohou být flagrantně porušeny. Tento tahák má navrhnout výchozí bod. Nebojte se spustit soutěž mezi několika algoritmy na vašich datech. Tam je prostě žádná náhrada za pochopení principy každého algoritmu a systému, který generoval vaše data.

Každý algoritmus strojového učení má svůj vlastní styl nebo indukční předpojatost. Pro konkrétní problém může být vhodné několik algoritmů a jeden algoritmus může být vhodnější než ostatní. Ale to není vždy možné vědět předem, který je nejvhodnější. V případech, jako jsou tyto, několik algoritmů jsou uvedeny společně v taháku. Vhodnou strategií by bylo vyzkoušet jeden algoritmus, a pokud výsledky ještě nejsou uspokojivé, zkuste ostatní. 

Další informace o algoritmech v Azure Machine Learning, přejděte na [algoritmus a odkaz na modul](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Druhy strojového učení

Existují tři hlavní kategorie strojového učení: *učení pod dohledem*, *učení bez dohledu*a *posilování učení*.

### <a name="supervised-learning"></a>Učení pod dohledem

Při učení pod dohledem je každý datový bod označen nebo spojen s kategorií nebo hodnotou zájmu. Příkladem kategorického popisku je přiřazení obrázku jako "kočka" nebo "pes". Příkladem popisku hodnoty je prodejní cena spojená s ojetým vozem. Cílem učení pod dohledem je studovat mnoho označených příkladů, jako jsou tyto, a pak být schopen dělat předpovědi o budoucích datových bodů. Například identifikace nových fotografií se správným zvířetem nebo přiřazení přesných prodejních cen jiným ojetým vozům. Jedná se o populární a užitečný typ strojového učení.

### <a name="unsupervised-learning"></a>Učení bez dozoru

Při učení bez dozoru nemají datové body žádné popisky, které by byly s nimi spojeny. Místo toho cílem algoritmu učení bez dohledu je uspořádat data nějakým způsobem nebo popsat jejich strukturu. Učení bez dozoru seskupuje data do clusterů, jak to dělá K-means, nebo najde různé způsoby pohledu na složitá data tak, aby se jevila jednodušeji.

### <a name="reinforcement-learning"></a>Zpětnovazební učení

Při učení výztuže algoritmus vybere akci v reakci na každý datový bod. Jedná se o běžný přístup v robotice, kde je sada senzorových hodnot v jednom okamžiku datovým bodem a algoritmus musí zvolit další akci robota. Je to také přirozené přizpůsobení aplikacím Internetu věcí. Algoritmus učení také obdrží signál odměny krátce později, což naznačuje, jak dobré bylo rozhodnutí. Na základě tohoto signálu algoritmus upravuje svou strategii, aby dosáhl nejvyšší odměny. 

## <a name="next-steps"></a>Další kroky

* Další pokyny [k výběru algoritmů](how-to-select-algorithms.md)

* [Další informace o studiu v Azure Machine Learning a na webu Azure Portal](overview-what-is-azure-ml.md).

* [Kurz: Vytvoření modelu předpovědi v návrháři Azure Machine Learning](tutorial-designer-automobile-price-train-score.md).

* [Další informace o hlubokém učení vs. strojové učení](concept-deep-learning-vs-machine-learning.md).

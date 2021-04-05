---
title: Tahák – Návrhář tabulky a Machine Learningového algoritmu
titleSuffix: Azure Machine Learning
description: Tahák list s tisknutelným Machine Learning algoritmem vám pomůže vybrat správný algoritmus pro prediktivní model v Návrháři Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
adobe-target: true
ms.openlocfilehash: 730fcf3fb552b92a02b255ad654f64d5ff7a92fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100367052"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Tahákový list algoritmu Machine Learning pro návrháře Azure Machine Learning

**Tahákový list s algoritmem Azure Machine Learning** vám pomůže vybrat správný algoritmus z návrháře pro model prediktivní analýzy.

Azure Machine Learning má rozsáhlou knihovnu algoritmů z třídy ***klasifikace** _, _*_doporučované systémy_*_, _*_clustering_*_, _*_detekce anomálií_*_, _*_regrese_*_ a _ *_Text Analytics_** rodiny. Každá z nich je navržena tak, aby se vyřešil jiný typ problému strojového učení.

Další pokyny najdete v tématu [postup výběru algoritmů](how-to-select-algorithms.md) .

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Stažení: Tahákický list s algoritmem Machine Learning

**Stáhněte si tahák list sem: [Machine Learning tahák Algorithm list (11x17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Tahákový list s algoritmem Machine Learning: Naučte se, jak zvolit Machine Learning algoritmus.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Stáhněte a vytiskněte Tahákový list s algoritmem Machine Learning v Tabloid velikosti, abyste ho zachovali a mohli získat pomoc s výběrem algoritmu.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Jak používat Tahákový list s algoritmem Machine Learning

Návrhy nabízené v tomto tahák listu algoritmu jsou přibližné. Některé mohou být ohnuty a některé mohou být flagrantly porušeny. Tento tahák list je určen pro návrh počátečního bodu. Nemusíte být Nebojte, aby se spouštěla soutěž vedoucí na hlavu mezi několika algoritmy na vašich datech. Neexistuje žádná náhrada za porozumění principům každého algoritmu a systému, který vygeneroval vaše data.

Každý algoritmus strojového učení má svůj vlastní styl nebo inductiveový posun. V případě konkrétního problému může být vhodné několik algoritmů a jeden algoritmus může být lepší, než ostatní. Není ale vždy možné znát, který z nich nejlépe vyhovuje. V podobných případech je v listu tahák uvedeno několik algoritmů. Vhodnou strategií by bylo vyzkoušet jeden algoritmus a pokud výsledky ještě nejsou uspokojivé, zkuste jiné. 

Pokud chcete získat další informace o algoritmech v Návrháři Azure Machine Learning, přečtěte si [odkaz na algoritmus a modul](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Druhy strojového učení

Existují tři hlavní kategorie strojového učení: *dohled nad dohledem*, *vzdělávání bez dohledu* a *posílení učení*.

### <a name="supervised-learning"></a>Vzdělávání pod dohledem

V dohledovém učení je každý datový bod označený nebo přidružený ke kategorii nebo hodnotě, které vás zajímají. Příkladem popisku kategorií je přiřazení obrázku jako "Cat" nebo "pes". Příkladem popisku hodnoty je prodejní cena spojená s použitým automobilem. Cílem učení pod dohledem je prozkoumat mnoho příkladů s označením, jako jsou tyto, a pak předpovědi o budoucích datových bodech. Můžete například identifikovat nové fotografie se správným zvířetem nebo přiřadit přesné prodejní ceny jiným využitým automobily. Toto je oblíbený a užitečný typ strojového učení.

### <a name="unsupervised-learning"></a>Vzdělávání bez dohledu

V bezdohledovém učení se k datovým bodům nevztahují žádné popisky. Místo toho je cílem nesledovaného výukového algoritmu způsob, jak uspořádat data nějakým způsobem nebo popsat její strukturu. Data studijních skupin, která nejsou v režimu pod dohledem, jsou v clusterech, stejně jako to znamená, nebo naleznou různé způsoby prohlížení složitých dat tak, aby se zjednodušilo.

### <a name="reinforcement-learning"></a>Zpětnovazební učení

V procesu posílení učení se algoritmus při reakci na každý datový bod vybere jako akce. Jedná se o běžný přístup v robotech, kde je sada čtení snímačů v určitém bodě v čase datovým bodem a algoritmus musí zvolit další akci robota. Je to také přirozené přizpůsobení Internet věcí aplikacím. Výukový algoritmus také obdrží krátkou dobu a později indikuje, jak dobrá bylo rozhodnutí. Na základě tohoto signálu algoritmus mění svoji strategii, aby dosáhl nejvyšší odměny. 

## <a name="next-steps"></a>Další kroky

* Podívejte se na další pokyny k [výběru algoritmů](how-to-select-algorithms.md)

* [Přečtěte si o studiu v Azure Machine Learning a Azure Portal](overview-what-is-azure-ml.md).

* [Kurz: sestavení modelu předpovědi v návrháři Azure Machine Learning](tutorial-designer-automobile-price-train-score.md).

* [Seznamte se s hloubkovým učením a strojovým učením](concept-deep-learning-vs-machine-learning.md).

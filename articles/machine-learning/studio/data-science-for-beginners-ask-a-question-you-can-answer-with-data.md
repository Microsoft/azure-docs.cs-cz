---
title: Položit otázku, na kterou mohou data odpovědět
titleSuffix: ML Studio (classic) - Azure
description: Přečtěte si, jak formulovat otázku ostré datové vědy v datové vědě pro začátečníky 3. Zahrnuje porovnání klasifikace a regresní otázky.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 26837337b49d79a26404fd6709b036f6907720f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838836"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Položení otázky, na kterou lze odpovědět pomocí dat
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Datové vědy pro začátečníky série
Naučte se formulovat problém datové vědy do otázky v datové vědě pro začátečníky video 3. Toto video obsahuje porovnání otázek pro klasifikaci a regresní algoritmy.

Chcete-li ze série vytěžit maximum, sledujte je všechny. [Přejděte na seznam videí.](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Datová věda pro začátečníky* je rychlý úvod do datové vědy v pěti krátkých videích.

* Video 1: [Odpovědi na 5 otázek datové vědy](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sec)*
* Video 2: [Jsou vaše data připravena pro datovou vědu?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
* Video 3: Položte otázku, na kterou můžete odpovědět pomocí dat
* Video 4: [Předvídejte odpověď pomocí jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Video 5: [Kopírování práce jiných lidí pro datovou vědu](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Přepis: Zeptejte se na otázku, na kterou můžete odpovědět pomocí dat
Vítejte na třetí video v sérii "Data Science pro začátečníky."  

V tomto dostanete několik tipů pro formulaci otázky, na kterou můžete odpovědět pomocí dat.

Můžete získat více z tohoto videa, pokud se poprvé podíváte na dvě předchozí videa v této sérii: "5 otázek, na které může datová věda odpovědět" a "Jsou vaše data připravena pro datovou vědu?"

## <a name="ask-a-sharp-question"></a>Položit ostrou otázku
Mluvili jsme o tom, jak je datová věda procesem používání názvů (nazývaných také kategorie nebo popisky) a čísel k předvídání odpovědi na otázku. Ale to nemůže být jen tak nějaká otázka; Musí to být *ostrá otázka.*

Vágní otázka nemusí být zodpovězena jménem nebo číslem. Ostrá otázka musí.

Představte si, že jste našli kouzelnou lampu s džinem, který pravdivě zodpoví jakoukoli otázku, kterou položíte. Ale je to zlomyslný džin, který se bude snažit, aby jejich odpověď byla tak vágní a matoucí, jak se mohou dostat pryč. Chcete je přišít otázkou tak neprůstřelnou, že si nemůžou pomoct, ale řeknou vám, co chcete vědět.

Pokud byste se zeptat vágní otázku, jako je "Co se stane s mými zásobami?", džin by mohl odpovědět: "Cena se změní". To je pravdivá odpověď, ale moc to nepomáhá.

Ale pokud jste se zeptat na ostrou otázku, jako je "Co bude moje akcie prodejní cena bude příští týden?", džin nemůže pomoci, ale dá vám konkrétní odpověď a předpovědět prodejní cenu.

## <a name="examples-of-your-answer-target-data"></a>Příklady vaší odpovědi: Cílová data
Jakmile zformulujete otázku, zkontrolujte, zda máte v datech příklady odpovědi.

Pokud naše otázka zní: "Jaká bude prodejní cena mých akcií příští týden?" pak se musíme ujistit, že naše data zahrnují historii cen akcií.

Pokud naše otázka zní: "Které auto v mém vozovém parku selže jako první?" pak se musíme ujistit, že naše údaje obsahují informace o předchozích selháních.

![Cílová data - příklady vaší odpovědi. Formulovat otázku datové vědy.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Tyto příklady odpovědí se nazývají cíl. Cíl je to, co se snažíme předpovědět o budoucích datových bodech, ať už se jedná o kategorii nebo číslo.

Pokud nemáte žádná cílová data, budete je muset získat. Bez ní na svou otázku neodpovíte.

## <a name="reformulate-your-question"></a>Přeformulovat svůj dotaz
Někdy můžete přeformulovat svou otázku, abyste získali užitečnější odpověď.

Otázka "Je tento datový bod A nebo B?" předpovídá kategorii (nebo název nebo popisek) něčeho. Abychom na ni odpověděli, používáme *klasifikační algoritmus*.

Otázka "Kolik?" nebo "Kolik?" předpovídá částku. Chcete-li odpovědět, používáme *regresní algoritmus*.

Chcete-li vidět, jak můžeme transformovat tyto, podívejme se na otázku, "Který zpravodajský příběh je nejzajímavější pro tohoto čtenáře?" Žádá o předpověď jediné volby z mnoha možností - jinými slovy "Je to A nebo B nebo C nebo D?" - a použil by klasifikační algoritmus.

Ale tato otázka může být jednodušší odpovědět, pokud jste přeformulovat jako "Jak zajímavý je každý příběh na tomto seznamu tohoto čtenáře?" Nyní můžete dát každému článku číselné skóre a pak je snadné identifikovat článek s nejvyšším skóre. Jedná se o přeformulování klasifikační otázky do regresní otázky nebo Kolik?

![Přeformulovat svou otázku. Otázka klasifikace vs. regresní otázka.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Jak se ptáte, je vodítko, na které algoritmus vám může dát odpověď.

Zjistíte, že některé rodiny algoritmů - jako ty v našem příkladu zpravodajského příběhu - jsou úzce příbuzné. Můžete přeformulovat svou otázku použít algoritmus, který vám dává nejužitečnější odpověď.

Ale co je nejdůležitější, zeptejte se na tuto ostrou otázku - otázku, kterou můžete odpovědět s daty. A ujistěte se, že máte správná data, abyste na ně odpověděli.

Hovořili jsme o některých základních principech pro položení otázky, na kterou můžete odpovědět pomocí dat.

Nezapomeňte se podívat na další videa v "Datové vědy pro začátečníky" z Microsoft Azure Machine Learning Studio (klasické).

## <a name="next-steps"></a>Další kroky
* [Vyzkoušejte první experiment datové vědy se Machine Learning Studio (klasický)](create-experiment.md)
* [Získejte úvod do Machine Learningu v Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)

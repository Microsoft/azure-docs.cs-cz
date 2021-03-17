---
title: Položte dotaz na data, která můžou odpovídat na ML Studio (Classic) – Azure
description: Naučte se formulovat ostrost vědeckých otázek v oblasti datových věd pro začátečníky – video 3. Obsahuje porovnání otázek klasifikace a regrese.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 2ff440801db7ba8c5bfc1785fc7a399a1d773b73
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517021"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Položení otázky, na kterou lze odpovědět pomocí dat
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: řada pro datové vědy pro začátečníky
Naučte se formulovat problém s datovou vědy na otázky v oblasti datové vědy pro začátečníky – video 3. Toto video obsahuje porovnání otázek pro klasifikaci a regresní algoritmy.

Pokud chcete získat maximum z řady, Sledujte je vše. [Přejít na seznam videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Data vědy pro začátečníky* je rychlý Úvod k datové vědy v pěti krátkých videích.

* Video 1: [5 otázek pro datové vědy](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min. 14 sec)*
* Video 2: [jsou vaše data připravená pro datové vědy?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min. 56 s)*
* Video 3: Položte otázku, na kterou můžete odpovědět pomocí dat
* Video 4: [předpověď odpovědi pomocí jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Video 5: [kopírování práce jiných lidí do oblasti datové vědy](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min. 18 sec)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Přepis: Položte otázku, na kterou můžete odpovědět pomocí dat.
Vítá vás třetí video v řadě "datové vědy pro začátečníky".  

V tomto případě získáte několik tipů pro formulaci otázky, na kterou můžete odpovědět s daty.

Při prvním sledování těchto dvou předchozích videí v této sérii můžete dostat více z tohoto videa: "5 otázek pro datové vědy může odpovídat" a "vaše data jsou připravená na datovou vědu?"

## <a name="ask-a-sharp-question"></a>Zeptat se na ostřejší dotaz
Mluvilii jsme, jak je datová věda proces použití názvů (označovaných také jako kategorie nebo popisky), a čísla k předpovědi odpovědi na otázku. Ale nemůže to být jenom žádné otázky; musí se jednat o *ostřejší dotaz.*

Otázka Vague nemusí být zodpovězena s názvem nebo číslem. Musí se jednat o ostřejší dotaz.

Představte si, že jste narazili na Genie, který bude pravdivě odpovídat na všechny otázky, které požadujete. Ale jedná se o mischievous Genie, který se pokusí učinit svou odpověď jako Vague a je matoucí, protože vám může dodržet. Chcete je připnout s otázkou, aby vám Airtight, že vám nemůžou poznat, co chcete vědět.

Pokud byste se chtěli zeptat na Vague otázku, třeba "co se stane s mým zásobou", Genie může odpovídat "cena se změní". To je věrné odpověď, ale není velmi užitečná.

Pokud byste ale položili ostřejší dotaz, třeba "co by cena za cenu akcií byla na příští týden?", Genie nemůže pomoc, ale poskytne vám konkrétní odpověď a předpovědět prodejní cenu.

## <a name="examples-of-your-answer-target-data"></a>Příklady vaší odpovědi: cílová data
Po formulaci otázky zkontrolujte, jestli máte v datech příklady odpovědi.

Pokud je naše otázka "Co znamená, že je cena za prodej v mém skladu příští týden?" pak musíme zajistit, aby naše data obsahovala historii cen akcií.

Pokud se jedná o otázku, kterou auto v mém loďstvu napřed chybou? " pak musíme zajistit, aby naše data obsahovala informace o předchozích chybách.

![Cílová data – příklady vaší odpovědi. Formulujte otázku pro datové vědy.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Tyto příklady odpovědí se nazývají cíle. Cílem je, že se snažíme předpovědět budoucí datové body, ať už se jedná o kategorii nebo číslo.

Pokud nemáte žádná cílová data, budete je muset získat. Nebudete moct odpovědět na svoji otázku bez něj.

## <a name="reformulate-your-question"></a>Přeformulování otázky
V některých případech můžete své otázky přeformulovat, abyste získali užitečnější odpověď.

Otázka "je tento datový bod A nebo B?" předpovídá kategorii (nebo název nebo popisek) nějakého typu. K zodpovězení používáme *klasifikační algoritmus*.

Otázka "kolik věcí?" nebo "kolik?" předpovídá částku. K zodpovězení používáme *regresní algoritmus*.

Pokud chcete zjistit, jak můžeme tyto funkce transformovat, Podívejme se na otázku "který příběh je nejzajímavější pro tento čtenář?" Žádá o předpověď jedné volby z mnoha možností – jinými slovy je toto A nebo B nebo C nebo D? -a by používal algoritmus klasifikace.

Ale tato otázka může být jednodušší, pokud ji předáte jako "jak zajímavá je každý příběh v tomto seznamu pro tento čtenář?" Nyní můžete každému článku poskytnout číselné skóre a potom snadno identifikovat článek s nejvyšším hodnocením. Toto je změna formulace otázky klasifikace na otázku regrese nebo kolik?

![Přeformulujte svůj dotaz. Otázka klasifikace vs. regresní otázka](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Dotaz na otázku je formulace, na kterou vám může poskytnout odpověď.

Zjistíte, že některé rodiny algoritmů, jako jsou ty v našem příkladu zprávy, jsou úzce propojené. Můžete znovu formulovat svou otázku a použít algoritmus, který vám poskytne nejužitečnější odpověď.

Ale nejdůležitějším požádáme o ostrost dotazů – otázka, kterou můžete na data odpovědět. A ujistěte se, že máte správná data pro zodpovězení.

Mluvilii jsme se o některých základních principech pro dotazování na otázku, na kterou můžete odpovědět pomocí dat.

Nezapomeňte se podívat na další videa v oblasti "data vědy pro začátečníky" z Microsoft Azure Machine Learning Studio (Classic).

## <a name="next-steps"></a>Další kroky
* [Vyzkoušejte si první experiment pro datové vědy s Machine Learning Studio (Classic)](create-experiment.md)
* [Seznámení s Machine Learningem Microsoft Azure](../overview-what-is-azure-ml.md)
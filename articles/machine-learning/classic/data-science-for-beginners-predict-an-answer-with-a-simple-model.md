---
title: 'ML Studio (Classic): předpověď odpovědí pomocí regresních modelů – Azure'
description: Jak vytvořit jednoduchý regresní model pro předpověď ceny v oblasti datové vědy pro začátečníky – video 4. Zahrnuje lineární regresi s cílovými daty.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: c6d78b10b98abe33744673459b3d31d8941aac89
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520404"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Předpovídání odpovědi pomocí jednoduchého modelu


## <a name="video-4-data-science-for-beginners-series"></a>Video 4: řada pro datové vědy pro začátečníky
Naučte se vytvořit jednoduchý regresní model pro předpověď ceny kosočtverce v oblasti datové vědy pro začátečníky – video 4. Nakreslíme regresní model s cílovými daty.

Pokud chcete získat maximum z řady, Sledujte je vše. [Přejít na seznam videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Data vědy pro začátečníky* je rychlý Úvod k datové vědy v pěti krátkých videích.

* Video 1: [5 otázek pro datové vědy](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min. 14 sec)*
* Video 2: [jsou vaše data připravená pro datové vědy?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min. 56 s)*
* Video 3: [Položte otázku, na kterou můžete odpovědět](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 minuty 17 s)* .
* Video 4: předpověď odpovědi pomocí jednoduchého modelu
* Video 5: [kopírování práce jiných lidí do oblasti datové vědy](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min. 18 sec)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Přepis: předpověď odpovědi pomocí jednoduchého modelu
Vítejte ve čtvrtém videu v řadě "datové vědy pro začátečníky". V tomto případě vytvoříme jednoduchý model a vytvoříme předpovědi.

*Model* je zjednodušený příběh o našich datech. Ukážem to, co jsem chtěl.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Shromáždění relevantního, přesného, připojeného a dostatečného množství dat
Řekněme, že se mi má koupit pro kosočtverec. Mám prstenec, který patří k mému jméno s nastavením pro kosočtverec 1,35 kosočtverce, a chci získat představu o tom, kolik to bude mít náklady. Převezmem Poznámkový blok a pero do Jewelry Storu a zapíšete cenu všech diamantů v případu a to, kolik je v carats. Počínaje prvním kosočtvercem – je 1,01 carats a $7 366.

Teď procházím a uděláte to pro všechny ostatní diamanty ve Storu.

![Sloupce Diamond data](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Všimněte si, že náš seznam má dva sloupce. Každý sloupec má jinou váhu atributu v carats a Price a každý řádek je jedním datovým bodem, který představuje jeden kosočtverec.

V tomto příkladu jsme vytvořili malou datovou sadu – tabulku. Všimněte si, že splňuje naše kritéria pro kvalitu:

* Data jsou **relevantní** – v tuto dobu se jednoznačně vztahují ceny.
* Je to **přesné** – dvakrát jsme kontrolovali ceny, které zapisujeme.
* Je **připojený** – v žádném z těchto sloupců nejsou žádné prázdné mezery.
* A jak uvidíme, je k **dispozici dostatek** dat pro zodpovězení naší otázky.

## <a name="ask-a-sharp-question"></a>Zeptat se na ostřejší dotaz
Naši otázku teď vyplníme ostrým způsobem: "kolik bude při nákupu 1,35 kosočtverce kosočtverec?"

Náš seznam nemá v tomto seznamu 1,35 kosočtverce kosočtverec, takže abychom dostali odpověď na otázku, budeme muset použít zbytek našich dat.

## <a name="plot-the-existing-data"></a>Vykreslit existující data
První věc, kterou budeme dělat, je nakreslit vodorovnou číselnou čáru, která se označuje jako osa, aby se váhy vykreslily. Rozsah závaží je 0 až 2, proto vykreslíme řádek, který pokrývá tento rozsah a značku pro každou polovinu kosočtverce.

V dalším kroku nakreslíme svislou osu pro záznam ceny a připojíme ji k ose horizontální váhy. To bude v jednotkách v dolarech. Teď máme sadu souřadnic osy.

![Váha a cenové osy](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Tato data teď provedeme a zařadíme do *bodového grafu*. Toto je skvělý způsob, jak vizualizovat číselné datové sady.

Pro první datový bod jsme eyeballi svislou čáru na 1,01 carats. Pak jsme eyeballi horizontální linii na $7 366. Tam, kde splňují, nakreslíme tečku. To představuje náš první kosočtverec.

Nyní procházíme každý kosočtverec v tomto seznamu a provedeme stejnou věc. V takovém případě se dostanete jako spousta teček, jednu pro každý kosočtverec.

![Bodový graf](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Nakreslete model prostřednictvím datových bodů.
Teď, když se podíváte na tečky a squint, kolekce vypadá jako FAT, Přibližná čára. Můžeme absolvovat značku a nakreslit rovnou čáru.

Nakreslením čáry jsme vytvořili *model*. Tohle si můžete představit jako z reálného světa a vytvořit zjednodušenýou jeho verzi. Kresba není nyní správná – řádek neprojde všemi datovými body. Ale jedná se o užitečné zjednodušení.

![Čára lineární regrese](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Skutečnost, že všechny tečky nejdou přesně přes řádek, je OK. Vědečtí data tyto odborníky vysvětlují, že se jedná o model – to je řádek a pak každá tečka má k sobě nějaký určitý *šum* nebo *odchylku* . Existuje základní dokonalý vztah a pak je Gritty reálného světa, který zvyšuje šum a nejistotu.

Vzhledem k tomu, že se snažíme odpovědět na otázku, *Jak velká je?* Toto se nazývá *regrese*. A protože používáme rovnou čáru, jedná se o *lineární regresi*.

## <a name="use-the-model-to-find-the-answer"></a>Vyhledejte odpověď pomocí modelu.
Teď máme model a požádáme ho o naši otázku: kolik bude 1,35 kosočtverceových nákladů na Diamond?

K zodpovězení naší otázky jsme Eyeball 1,35 carats a nakreslili svislou čáru. Tam, kde přechází čáru modelu, Eyeball vodorovnou čáru na osu dolaru. Narazí přímo na 10 000. Bác! Jedná se o odpověď: 1,35 kosočtverce Diamond – náklady na $10 000.

![Najít odpověď na modelu](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Vytvoření intervalu spolehlivosti
Je přirozenější, aby na tom, jak přesně je tato předpověď. Je vhodné zjistit, zda bude 1,35 kosočtverce kosočtverec velmi blízko $10 000 nebo vyšší nebo nižší. K tomuto obrázku nakreslíme obálku kolem regresní přímky, která obsahuje většinu teček. Tato obálka se označuje jako náš *interval spolehlivosti*: máme v podstatě jistotu, že ceny spadají do této obálky, protože v minulosti většina z nich. Můžeme vykreslit dvě další vodorovné čáry, ze kterých 1,35 kosočtverce řádek protíná horní a dolní část této obálky.

![Interval spolehlivosti](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nyní můžeme něco o tomto intervalu spolehlivosti: řekněme, že cena 1,35 kosočtverce Diamond má asi $10 000, ale může to být tak nízké jako $8 000 a může to být tak vysoké jako $12 000.

## <a name="were-done-with-no-math-or-computers"></a>Jsme hotovi, bez matematických ani počítačů.
Provedli jsme to, co se pro odborníky přes data vyplatilo, a my jsme ho právě vykreslit:

* Položili jsme otázku, že můžeme odpovědět na data.
* Sestavili jsme *model* pomocí *lineární regrese* .
* Provedli jsme *předpovědi* a dokončili jste *interval spolehlivosti* .

A k tomu jsme nepoužili matematické ani počítače.

Teď, když máme další informace, třeba...

* vyjmutí kosočtverce
* variace barev (jak blízko má být kosočtverec bílá)
* počet zahrnutí ve čtverečku

... pak by měl být více sloupců. V takovém případě by matematika mohla být užitečná. Pokud máte více než dva sloupce, je těžké nakreslit tečky na papír. Matematický vám umožní přizpůsobit si tento řádek nebo tuto rovinu vašim datům.

Kromě toho, pokud místo jenom několik kosočtverců, jsme měli 2000 nebo 2 000 000, a díky tomu můžete tuto práci mnohem rychleji udělat v počítači.

Dnes jsme se mluvilii o tom, jak provést lineární regresi a jsme vytvořili předpověď pomocí dat.

Nezapomeňte se podívat na další videa v oblasti "data vědy pro začátečníky" z Microsoft Azure Machine Learning Studio (Classic).

## <a name="next-steps"></a>Další kroky
* [Vyzkoušejte si první experiment pro datové vědy s Machine Learning Studio (Classic)](create-experiment.md)
* [Seznámení s Machine Learningem Microsoft Azure](../overview-what-is-azure-ml.md)
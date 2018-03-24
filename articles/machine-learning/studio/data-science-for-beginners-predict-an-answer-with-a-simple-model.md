---
title: Předpověď odpověď s jednoduché regresní model - Azure Machine Learning | Microsoft Docs
description: Jak vytvořit jednoduché regresní model předpovídat cenu v vědecké zpracování dat pro začátečníky video 4. Zahrnuje lineární regrese s cílová data.
keywords: Vytvoření modelu, jednoduchého modelu, prediction ceníku, jednoduché regresní model
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cjgronlund
ms.assetid: a28f1fab-e2d8-4663-aa7d-ca3530c8b525
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: 019f3a94e81d0ae581414f62c5e0389c9c2b6813
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="predict-an-answer-with-a-simple-model"></a>Předpovídání odpovědi pomocí jednoduchého modelu
## <a name="video-4-data-science-for-beginners-series"></a>Video 4: Vědecké zpracování dat pro začátečníky řady
Zjistěte, jak vytvořit jednoduché regresní model předpovídat cenu kosočtverec v vědecké zpracování dat pro začátečníky video 4. Jsme budete zakreslit regresní model se cílová data.

Získejte maximum z řady, můžete sledujte všechny. [Přejděte do seznamu videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa z této série
*Vědecké zpracování dat pro začátečníky* je rychlý úvod do vědecké zpracování dat v pěti krátké videa.

* Video 1: [5 otázky, odpovědi vědecké účely data](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 14 s min.)*
* Video 2: [vašich dat je připravený pro vědecké zpracování dat?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 56 sekundu min.)*
* Video 3: [zeptejte se vám pomůže odpovědět s daty](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 17 sekundu min.)*
* Video 4: Odpověď s jednoduchého modelu předpovědi
* Video 5: [zkopírujte jiní lidé práce uděláte vědecké zpracování dat](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 18 sekundu min.)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Přepis: Předpovědi odpověď s jednoduchého modelu
Vítá vás čtvrtý video v "datové vědy pro začátečníky" řady. V této jeden jsme sestavíte jednoduchého modelu a předpovědím.

A *modelu* je zjednodušený scénář o našich datech. I budete vám ukážou, I znamenat.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Shromažďovat relevantní, přesný, připojené, dostatek dat.
Řekněme, že chcete obchod pro kosočtverec. Je nutné prstenec, který patří do mé babičky s nastavením pro 1.35 karátová kosočtverec a chcete získat představu o tom, jak bude cena. Trvat Poznámkový blok a pera do úložiště špercích a I zapište cena všech diamantů v případě a kolik naváží v carats. Počínaje první kosočtverec - 1.01 carats a 7,366 $.

Teď I projít a to pro všechny ostatní diamanty v úložišti.

![Sloupce kosočtverec dat](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Všimněte si, že naše seznam obsahuje dva sloupce. Každý sloupec má jiný atribut – váhy v carats a cena - a každý řádek je jeden datový bod, který představuje jednu kosočtverec.

Ve skutečnosti vytvořili jsme malá sada dat zde - tabulku. Všimněte si, že splňuje naše kritéria pro kvalitu:

* Data jsou **relevantní** -váhy výborný souvisí ceny
* Má **přesné** -jsme double-checked ceny, které jsme zapište
* Má **připojené** -nejsou žádné mezery v některém z těchto sloupců
* A protože uvidíme, má **dostatek** dat k odpovědi na našem otázku

## <a name="ask-a-sharp-question"></a>Zeptejte se sharp
Nyní jsme budete sharp způsobem představovat naše otázku: "jak se cena koupit 1.35 karátová kosočtverec?"

Našich seznamu nemá 1.35 karátová kosočtverec v, takže jsme budete muset použít zbytek naše data získat odpověď na otázku.

## <a name="plot-the-existing-data"></a>Vykreslení stávajících dat
První věcí, kterou jsme to udělat je kreslení na vodorovném řádku číslo osu grafu vah se musí volat. Rozsah vah je 0 až 2, takže jsme budete kreslení čáry, které pokrývá, rozsah a umístí rysky pro každou půlhodinu karátová.

Potom jsme budete zakreslit svislou osu pro záznam ceny a připojte ho k váhy vodorovné osy. To bude v jednotkách dolarů. Máme teď sadu souřadnic osy.

![Váha a cena osy](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vytvoříme nyní trvat tato data a aktivujte ji do *bodové vykreslení*. Toto je skvělý způsob, jak vizualizovat číselné datové sady.

Pro první bod, data jsme eyeball svislice 1.01 carats. Potom jsme eyeball na vodorovném řádku v 7,366 $. Kde splňují, jsme zakreslit tečku. To představuje naše první kosočtverec.

Nyní přejděte prostřednictvím každý kosočtverec v tomto seznamu a stejnou věc udělat. Když jsme prostřednictvím, jedná se nám získat: bunch teček, jednu pro každý kosočtverec.

![Bodový graf](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Kreslení modelu pomocí datových bodů
Nyní když se podíváte na tečky a squint, kolekce vypadá jako fat, přibližné řádku. Můžeme provést naše značky a kreslení úsečky jejím prostřednictvím.

Pomocí kreslení čáry, jsme vytvořili *modelu*. Představit jako trvá reálném světě a provádění zneužívající vlastností prohlížeče Kreslený vtip verzi ho. Nyní Kreslený vtip je špatně – řádek není spojen datových bodů. Ale je užitečné zjednodušení.

![Lineární regrese řádku](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Skutečnost, že se tečkami Nepřipojujte se přesně pomocí řádku je v pořádku. To datových vědců vysvětlují podle oznamující, že je model - je na řádku - a potom jednotlivých bodů má některé *šumu* nebo *odchylky* s ním spojená. Je základní ideální vztah, a pak je krupičnaté, skutečné world, který přidá šumu a nejistoty.

Protože jsme se snažíte odpověď na otázku *kolik?* tomu se říká *regrese*. A protože používáme přímku, je *lineární regrese*.

## <a name="use-the-model-to-find-the-answer"></a>Použití modelu najít odpověď
Máme modelu a jeho požádáme naše otázku teď: jaké budou 1.35 karátová kosočtverec náklady?

Odpověď naše otázku, jsme oka 1.35 carats a kreslení svislé čáry. Kde je protne řádku modelu jsme eyeball na vodorovném řádku dolaru osy. Volání vpravo na 10 000. Výložníku! To je odpověď: 1.35 karátová kosočtverec stojí přibližně 10 000.

![Najít odpověď na modelu](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Vytvořit interval spolehlivosti
Je přirozené zajímat, jak přesné je tento předpovědi. Je dobré vědět, zda kosočtverec 1.35 karátová bude velmi brzy bude dosaženo, 10 000 nebo mnohem vyšší nebo nižší. Chcete-li tento limit, můžeme zakreslit obálku kolem regresní přímky, který obsahuje většinu bodů. Je volána tuto obálku naše *interval spolehlivosti*: nám poměrně jisti, že ceny spadal do této obálky, protože v posledních většina z nich. Z kde řádku 1.35 karátová protne horní a dolní části této obálky jsme můžete zakreslit dvě více vodorovné čáry.

![Interval spolehlivosti](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nyní budeme moct něco o našem interval spolehlivosti: budeme moct mohli bez obav, cenu 1.35 karátová kosočtverec je o $ 10 000 – ale může být co nejnižší $ 8 000 a může být až 12 000 $.

## <a name="were-done-with-no-math-or-computers"></a>Máme Hotovo, bez matematické nebo počítače
Jsme nebyla, jaké datových vědců získat placené udělat a jsme nebyla pouhým kreslení:

* Jsme neptal že jsme může odpovědět s daty
* Využili jsme *modelu* pomocí *lineární regrese*
* Jsme provedli *předpovědi*, kompletní sadou *interval spolehlivosti*

A jsme nepoužili matematické nebo počítače to udělat.

Nyní když jsme měl měli další informace, jako je třeba...

* Vyjmout kosočtverec
* Barva varianty (jak zavřít kosočtverec je právě bílé)
* počet zahrnutí v kosočtverec

.. .potom jsme by měli více sloupců. V takovém případě se změní matematické užitečné. Pokud máte více než dva sloupce, je obtížné kreslení tečky v dokumentu. Výpočty umožňuje velmi vhodně podle daného řádku nebo že roviny ke svým datům.

Navíc pokud místo jen někteří z nich diamanty, jsme měli dva tisíce nebo 2 000 a potom můžete provést které pracují mnohem rychleji s počítačem.

Dnes jste už jsme mluvili o tom, jak provést lineární regrese a jsme provedli předpovědi pomocí data.

Ujistěte se, podívejte se na ostatní videa v "Datové vědy pro začátečníky" z Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Další postup
* [Zkuste prvního experimentu vědecké účely data nástroje Machine Learning Studio](create-experiment.md)
* [Získejte Úvod do Machine Learning v Microsoft Azure](what-is-machine-learning.md)

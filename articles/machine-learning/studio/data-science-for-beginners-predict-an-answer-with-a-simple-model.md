---
title: Předvídání odpovědí pomocí regresních modelů
titleSuffix: ML Studio (classic) - Azure
description: Jak vytvořit jednoduchý regresní model předpovědět cenu v datové vědě pro začátečníky video 4. Zahrnuje lineární regresi s cílovými daty.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 6ad9c7912eee6c3f5ec55b9cd7ab340bc79c9db7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837773"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Předpovídání odpovědi pomocí jednoduchého modelu
## <a name="video-4-data-science-for-beginners-series"></a>Video 4: Datové vědy pro začátečníky série
Naučte se, jak vytvořit jednoduchý regresní model pro předpověď ceny diamantu v datové vědě pro začátečníky 4. Nakreslíme regresní model s cílovými daty.

Chcete-li ze série vytěžit maximum, sledujte je všechny. [Přejděte na seznam videí.](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Datová věda pro začátečníky* je rychlý úvod do datové vědy v pěti krátkých videích.

* Video 1: [Odpovědi na 5 otázek datové vědy](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sec)*
* Video 2: [Jsou vaše data připravena pro datovou vědu?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
* Video 3: [Zeptejte se na otázku, na kterou můžete odpovědět pomocí dat](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec)*
* Video 4: Předvídejte odpověď pomocí jednoduchého modelu
* Video 5: [Kopírování práce jiných lidí pro datovou vědu](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Přepis: Předvídejte odpověď pomocí jednoduchého modelu
Vítejte na čtvrtém videu ze série "Data Science for Beginners". V tomto, budeme stavět jednoduchý model a učinit předpověď.

*Model* je zjednodušený příběh o našich datech. Ukážu ti, co tím myslím.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Shromažďujte relevantní, přesné, propojené a dostatečné množství dat
Řekni, že chci koupit diamant. Mám prsten, který patřil mé babičce s nastavením na 1,35 karátový diamant, a chci získat představu o tom, kolik to bude stát. Vezmu poznámkový blok a pero do klenotnictví a zapíšu cenu všech diamantů v pouzdře a kolik váží v karátech. Počínaje prvním diamantem - je to 1,01 karátů a 7.366 dolarů.

Teď to projdu a udělám to pro všechny ostatní diamanty v obchodě.

![Sloupce diamantových dat](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Všimněte si, že náš seznam má dva sloupce. Každý sloupec má jiný atribut - hmotnost v karátech a cena - a každý řádek je jeden datový bod, který představuje jeden kosočtverec.

Vytvořili jsme zde malou datovou sadu - tabulku. Všimněte si, že splňuje naše kritéria pro kvalitu:

* Údaje jsou **relevantní** - hmotnost rozhodně souvisí s cenou
* Je to **přesné** - dvakrát jsme zkontrolovali ceny, které zapíšeme
* Je **připojen** - v žádném z těchto sloupců nejsou žádné mezery
* A jak uvidíme, je to **dost** dat odpovědět na naši otázku

## <a name="ask-a-sharp-question"></a>Položit ostrou otázku
Nyní budeme představovat naši otázku ostrým způsobem: "Kolik to bude stát koupit 1,35 karátový diamant?"

Náš seznam nemá 1,35 karátový diamant v něm, takže budeme muset použít zbytek našich dat získat odpověď na otázku.

## <a name="plot-the-existing-data"></a>Vykreslení existujících dat
První věc, kterou uděláme, je nakreslit vodorovnou číselnou čáru, nazývanou osa, abychom zmapovali váhy. Rozsah závaží je 0 až 2, takže nakreslíme čáru, která pokrývá tento rozsah a položíme klíšťata pro každou polovinu karátu.

Dále nakreslíme svislou osu, abychom zaznamenali cenu a připojili ji k vodorovné ose hmotnosti. To bude v jednotkách dolarů. Teď máme sadu souřadnicových seker.

![Hmotnost ní a cenové osy](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vezmeme tato data a uděláme z toho *rozptylový zápletku.* To je skvělý způsob, jak vizualizovat číselné datové sady.

Pro první datový bod, jsme oko svislou čáru na 1,01 karátů. Pak jsme oko horizontální linii na 7.366 dolarů. Tam, kde se setkají, nakreslíme tečku. Tohle představuje náš první diamant.

Teď projdeme každý diamant na tomto seznamu a uděláme to samé. Až do konce, dostaneme tohle: hromadu temene, jednu za každý diamant.

![Bodový graf](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Kreslení modelu přes datové body
Když se podíváte na tečky a mžouravé, kolekce vypadá jako tlustá, chlupatá čára. Můžeme vzít naši značku a nakreslit přes něj přímku.

Nakreslením čáry jsme vytvořili *model*. Myslete na to, jak s reálným světem a dělat zjednodušující karikatura verzi. Nyní je karikatura špatná - čára neprochází všemi datovými body. Ale je to užitečné zjednodušení.

![Lineární regresní přímka](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Skutečnost, že všechny tečky nejdou přesně přes čáru, je v pořádku. Datoví vědci to vysvětlují tím, že říkají, že je tu model - to je čára - a pak každá tečka má nějaký *šum* nebo *odchylku* s ní spojenou. Je tu základní dokonalý vztah, a pak je tu odvážný, skutečný svět, který přidává hluk a nejistotu.

Protože se snažíme odpovědět na otázku *Kolik?* *regression* A protože používáme přímku, je to *lineární regrese*.

## <a name="use-the-model-to-find-the-answer"></a>Použití modelu k nalezení odpovědi
Nyní máme model a ptáme se ho na naši otázku: Kolik bude 1,35 karátový diamant náklady?

Chcete-li odpovědět na naši otázku, jsme oko 1,35 karátů a nakreslit svislou čáru. Tam, kde prochází modelovou čárou, sledujeme vodorovnou čáru k ose dolaru. Trefí se přesně na 10 000. Boom! To je odpověď: 1,35 karátový diamant stojí asi 10.000 dolarů.

![Najít odpověď na modelu](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Vytvoření intervalu spolehlivosti
Je přirozené se divit, jak přesná je tato předpověď. Je užitečné vědět, zda 1,35 karátový diamant bude velmi blízko k $ 10,000, nebo mnohem vyšší nebo nižší. Chcete-li na to přijít, nakreslete obálku kolem regresní čáry, která obsahuje většinu tečů. Tato obálka se nazývá naše *důvěra interval*: Jsme docela jisti, že ceny spadají do této obálky, protože v minulosti většina z nich mají. Můžeme nakreslit další dvě vodorovné čáry, odkud 1,35 karátová čára protíná horní a spodní část obálky.

![Interval spolehlivosti](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nyní můžeme říci něco o našem intervalu důvěry: Můžeme s jistotou říci, že cena 1,35 karátového diamantu je asi 10.000 dolarů - ale to by mohlo být tak nízké, jak 8.000 dolarů a to by mohlo být tak vysoké, jak 12.000 dolarů.

## <a name="were-done-with-no-math-or-computers"></a>Skončili jsme, bez matematiky nebo počítačů.
Udělali jsme to, za co jsou datoví vědci placeni, a udělali jsme to jen kreslením:

* Položili jsme otázku, na kterou bychom mohli odpovědět
* Model jsme vytvořili *pomocí* *lineární regrese*
* Udělali jsme *předpověď*, kompletní s *intervalem spolehlivosti*

A nepoužili jsme k tomu matematiku ani počítače.

Kdybychom měli víc informací, jako...

* řez diamantu
* barevné variace (jak blízko je diamant k tomu, aby byl bílý)
* počet inkluzí v diamantu

... pak bychom měli více sloupců. V takovém případě se matematika stává užitečnou. Pokud máte více než dva sloupce, je těžké kreslit tečky na papíře. Matematika vám umožní, aby se vešly, že linka nebo že letadlo do vašich dat velmi pěkně.

Také, pokud místo jen hrst diamantů, jsme měli dva tisíce nebo dva miliony, pak můžete dělat, že práce mnohem rychleji s počítačem.

Dnes jsme mluvili o tom, jak udělat lineární regrese, a udělali jsme předpověď pomocí dat.

Nezapomeňte se podívat na další videa v "Datové vědy pro začátečníky" z Microsoft Azure Machine Learning Studio (klasické).

## <a name="next-steps"></a>Další kroky
* [Vyzkoušejte první experiment datové vědy se Machine Learning Studio (klasický)](create-experiment.md)
* [Získejte úvod do Machine Learningu v Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)

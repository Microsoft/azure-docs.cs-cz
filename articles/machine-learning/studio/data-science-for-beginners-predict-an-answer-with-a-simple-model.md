---
title: Předpovídání odpovědi pomocí jednoduchého regresní model - Azure Machine Learning | Dokumentace Microsoftu
description: Jak vytvořit jednoduchý regresní model k predikci cenu v datová věda pro začátečníky č. 4. Zahrnuje lineární regrese s daty cíl.
keywords: Vytvoření modelu, jednoduchého modelu, predikcí cena, jednoduché regresní model
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cjgronlund
ms.assetid: a28f1fab-e2d8-4663-aa7d-ca3530c8b525
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: d1e7667d30eecab2e1a3328fdc68ef528823e695
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824196"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Předpovídání odpovědi pomocí jednoduchého modelu
## <a name="video-4-data-science-for-beginners-series"></a>Video 4: Datová věda pro začátečníky řady
Zjistěte, jak vytvořit jednoduchý regresní model odhadnout cenu kosočtverce v datová věda pro začátečníky č. 4. Jsme budete nakreslete regresní model s daty cíl.

Využijte naplno řady, můžete sledujte všechny. [Přejít na seznam videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Datová věda pro začátečníky* článek obsahuje rychlý úvod do datových věd v pěti krátkých videích.

* Video 1: [5 otázky, které odpovídá vědecké zkoumání dat](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek)*
* Video 2: [jsou vaše data připravená pro vědecké zpracování dat?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: [položit dotaz lze odpovědět pomocí dat](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: Předpověď odpovědi pomocí jednoduchého modelu
* Video 5: [kopírování práce jiných lidí pro vědecké zkoumání dat](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Přepis: Předpověď odpovědi pomocí jednoduchého modelu
Vítá vás čtvrtý videa "datové vědě pro začátečníky" řady. Do tohoto objektu za jsme sestavíte jednoduchého modelu a vytvoří předpověď.

A *modelu* je zjednodušený scénář o našich datech. Ukážeme vám co mám na mysli to.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Shromažďovat relevantní, přesné, připojeno, dostatek dat.
Řekněme, že chci nakupovat kosočtverce. Mám aktualizační kanál, který patřil Moje celé s nastavením pro kosočtverce 1.35 ikonu kosočtverce a chci získat představu o tom, kolik to stojí. Do úložiště špercích vezmu Poznámkový blok a pera a jsem zapište si cena diamanty – v případě a kolik naváží v carats. Počínaje prvním kosočtverce - 1.01 carats a 7,366 $.

Teď můžu projít a udělat to pro všechny ostatní diamanty v úložišti.

![Sloupce dat kosočtverec](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Všimněte si, že náš seznam obsahuje dva sloupce. Každý sloupec má jiný atribut – váha v carats a cena - a každý řádek je jeden datový bod, který představuje jeden kosočtverec.

Ve skutečnosti jsme vytvořili malý tady – sada dat tabulky. Všimněte si, že splňují naše kritéria kvality:

* Data jsou **relevantní** – váha jednoznačně týkající se ceny
* Má **přesné** -jsme double-checked ceny, které jsme zapište
* Má **připojené** -nejsou žádné mezery v některém z těchto sloupců
* A jak uvidíme, má **dostatek** dat pro potřeby odpovědí naše otázku

## <a name="ask-a-sharp-question"></a>Zeptejte se sharp
Nyní jsme sharp způsobem budete představovat naše otázku: "kolik vás to bude stát koupit kosočtverce 1.35 ikonu kosočtverce?"

Náš seznam nemá kosočtverce 1.35 ikonu kosočtverce v ní, tak nám budete muset používat rest naše data odpověď na otázku.

## <a name="plot-the-existing-data"></a>Vykreslení stávajících dat
První věc, kterou uděláme je kreslení vodorovné čáry číslo, volá osy, do grafu váhy. Rozsah vah je 0 až 2, takže jsme budete nakreslit čáru, která zahrnuje, která v rozsahu a vložit značky pro každý poloviční ikonu kosočtverce.

Dále jsme budete nakreslete svislou osu pro záznam cena a připojte ho k váha vodorovné osy. Bude jím v jednotkách dolarů. Teď máme sady souřadnic osy.

![Váha a cena osy](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

My budeme udělejte tato data a komerčně *korelačního diagramu*. To je skvělý způsob, jak vizualizovat číselné datových sad.

Pro první datový bod jsme eyeball svislice 1.01 carats. Potom jsme eyeball vodorovnou čáru na 7,366 $. Pokud nebudou splňovat jsme nakreslete tečku. To představuje naši první kosočtverec.

Nyní jsme projít každý kosočtverce v tomto seznamu a stejnou věc udělat. Když jsme prostřednictvím, toto je jsme získali: spoustu tečky, jeden pro každý kosočtverec.

![Bodové vykreslení](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Nakreslit prostřednictvím datových bodů
Když se podíváte na tečky a squint, kolekce vypadat řádku fat, přibližných shod. Můžete využít naše značky a nakreslení rovné čáry přes něj.

Kreslením řádku jsme vytvořili *modelu*. Představte si to jako trvá reálného světa a provádění komiksový zjednodušenou verzi souboru. Nyní komiksový nesprávný – řádku neprojde, všechny datové body. Ale to je užitečné zjednodušení.

![Lineární regrese řádku](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Skutečnost, že všechny body nechcete procházet přesně řádku je v pořádku. Odborníci přes data vysvětlují to chci říct, že je model –, který je na řádku - a potom každé tečce má některé *šumu* nebo *variance* s ním spojená. Je základní ideální relace a nejsou k dispozici na světě krupičnaté, real, která přidá šumu a nejistoty.

Protože ale My se snažíme odpověď na otázku *kolik?* tento postup se nazývá *regrese*. A protože používáme rovné čáry, jde *lineární regrese*.

## <a name="use-the-model-to-find-the-answer"></a>Použití modelu odpovědi
Teď máme modelu a požádáme ji naši Otázka: kolik stojí kosočtverce 1.35 ikonu kosočtverce?

Odpověď naše otázku, jsme oka 1.35 carats a kreslení svislé čáry. Pokud překročí řádku modelu jsme eyeball vodorovná čára na ose dolar. Volání přímo na 10 000. Vida! To je odpověď: kosočtverce 1.35 ikonu kosočtverce stojí přibližně 10 000 USD.

![Najít odpověď na modelu](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Vytvoření interval spolehlivosti
Je přirozené zajímat, jak přesně je tato predikce. To je užitečné vědět, jestli kosočtverce 1.35 ikonu kosočtverce bude velmi blízko 10 000 USD, nebo mnohem vyšší nebo nižší. Chcete-li to zjistit, nakreslíme obálku kolem řádku regrese, který obsahuje většinu bodů. Tato obálky se nazývá naše *interval spolehlivosti*: jsme hodně jistotu, že ceny spadají do obálky, protože v posledních většina z nich mít. Dva více vodorovné čáry jsme můžete čerpat kde protíná řádku 1.35 ikonu kosočtverce horní a dolní část této obálky.

![Interval spolehlivosti](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Teď budeme moct říct: něco o našich interval spolehlivosti: budeme moct říct s jistotou, že cena kosočtverce 1.35 ikonu kosočtverce je asi $ 10 000 – ale může být v rozsahu od 8 000 $ a může být až 12 000 $.

## <a name="were-done-with-no-math-or-computers"></a>Máme Hotovo, bez matematické nebo počítače
Jsme to udělali jaké odborníci přes data získat placenou provedete a událo pouhým vykreslování:

* Společnost Microsoft položil dotaz, že jsme mohli odpovědět pomocí dat
* Sestavili jsme *modelu* pomocí *lineární regrese*
* Provedli jsme *předpovědi*, s *interval spolehlivosti*

A jsme k tomu nepoužili matematické nebo počítače.

Teď Pokud jsme měli informace, jako je...

* vyjmutí kosočtverec
* variace barev (jak blízko kosočtverce je právě bílé)
* počet zahrnutí v kosočtverec

.. .then jsme by měli více sloupců. V takovém případě se změní matematické užitečné. Pokud máte více než dva sloupce, je těžké nakreslit tečky na papír. Výpočty umožňuje velmi krásně podle daného řádku nebo že roviny ke svým datům.

Také pokud namísto pouze několik diamanty, jsme měli dva tisíce nebo dvou miliónů a potom tuto práci můžete provést mnohem rychleji, s počítačem.

V současné době už jsme mluvili o tom, jak provádět lineární regrese a jsme provedli předpověď na základě data.

Nezapomeňte se podívat na další videa v "Datové vědě pro začátečníky" ze služby Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Další postup
* [Zkuste prvního experimentu vědy data s Machine Learning Studio](create-experiment.md)
* [Úvod do strojového učení v Microsoft Azure](what-is-machine-learning.md)

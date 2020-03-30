---
title: Vyhodnocení údajů
titleSuffix: ML Studio (classic) - Azure
description: Čtyři kritéria, která musí vaše data splňovat, aby byla připravena na datovou vědu. Toto video má konkrétní příklady, které vám pomohou se základním vyhodnocením dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: ccc422dfb3105fd1e12569a84a4ebfd22182b225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837804"
---
# <a name="is-your-data-ready-for-data-science"></a>Jsou data připravená pro vědecké zkoumání?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Datové vědy pro začátečníky série
Přečtěte si, jak vyhodnotit data, abyste se ujistili, že splňují základní kritéria, aby byla připravena na datovou vědu.

Chcete-li ze série vytěžit maximum, sledujte je všechny. [Přejděte na seznam videí.](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Datová věda pro začátečníky* je rychlý úvod do datové vědy v pěti krátkých videích.

* Video 1: [Odpovědi na 5 otázek datové vědy](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sec)*
* Video 2: Jsou vaše data připravena pro datovou vědu?
* Video 3: [Zeptejte se na otázku, na kterou můžete odpovědět pomocí dat](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec)*
* Video 4: [Předvídejte odpověď pomocí jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Video 5: [Kopírování práce jiných lidí pro datovou vědu](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Přepis: Jsou vaše data připravena pro datovou vědu?
Vítá vás "Jsou vaše data připravena pro datové vědy?" druhé video v sérii *Data Science pro začátečníky*.  

Předtím, než vám datová věda může poskytnout požadované odpovědi, musíte jí dát některé vysoce kvalitní suroviny, se kterými můžete pracovat. Stejně jako dělat pizzu, tím lepší ingredience začnete, tím lepší konečný produkt. 

## <a name="criteria-for-data"></a>Kritéria pro údaje
V datové vědě existují určité složky, které musí být staženy dohromady, včetně:

* Příslušné
* Připojeno
* Přesné
* Dost pro práci s

## <a name="is-your-data-relevant"></a>Jsou vaše údaje relevantní?
Takže první přísada - potřebujete data, která jsou relevantní.

![Relevantní údaje vs. irelevantní údaje - vyhodnocení dat](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Na levé straně, tabulka představuje hladinu alkoholu v krvi sedm lidí testovány mimo bar Boston, Red Sox odpalování průměr v jejich poslední hře, a cena mléka v nejbližším samoobsluze.

To vše jsou naprosto legitimní data. Je to jen chyba je, že to není relevantní. Mezi těmito čísly není žádný zřejmý vztah. Pokud vám někdo dal současnou cenu mléka a průměr odpalování Red Sox, neexistuje způsob, jak byste mohli odhadnout jejich obsah alkoholu v krvi.

Teď se podívej na stůl napravo. Tentokrát byla měřena tělesná hmotnost každého člověka, stejně jako počet nápojů, které měli.  Čísla v každém řádku jsou nyní vzájemně relevantní. Kdybych ti dal svou tělesnou hmotnost a počet margarit, které jsem měl, mohl bys odhadnout obsah alkoholu v krvi.

## <a name="do-you-have-connected-data"></a>Máte připojená data?
Další složkou jsou připojená data.

![Připojená data vs. odpojená data - kritéria dat, data připravena](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Zde jsou některé relevantní údaje o kvalitě hamburgerů: teplota grilu, hmotnost patty a hodnocení v místním časopise potravin. Ale všimněte si mezer v tabulce vlevo.

Ve většině datových sad chybí některé hodnoty. Je běžné mít díry, jako je tento, a existují způsoby, jak je obejít. Ale pokud jich chybí příliš mnoho, vaše data začnou vypadat jako švýcarský sýr.

Když se podíváte na stůl vlevo, je tu tolik chybějících dat, že je těžké přijít s jakýmkoliv vztahem mezi teplotou grilu a hmotností patty. Tento příklad ukazuje odpojená data.

Tabulka vpravo je však plná a úplná - příklad připojených dat.

## <a name="is-your-data-accurate"></a>Jsou vaše údaje přesné?
Další složkou je přesnost. Zde jsou čtyři cíle zasáhnout.

![Přesná data vs. nepřesná data - kritéria údajů](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Podívejte se na cíl v pravém horním. Tam je těsné seskupení přímo kolem býčí oko. To je samozřejmě přesné. Kupodivu, v jazyce datové vědy, výkon na cíl přímo pod ním je také považován za přesný.

Pokud jste zmapovali střed těchto šípů, viděli byste, že je to velmi blízko býčího oka. Šípy jsou rozprostřeny kolem cíle, takže jsou považovány za nepřesné, ale jsou soustředěny kolem býčího oka, takže jsou považovány za přesné.

Teď se podívejte na levý horní cíl. Zde šípy zasáhly velmi blízko u sebe, těsné seskupení. Jsou přesné, ale nepřesné, protože střed je mimo býci. Šipky v levém dolním cíli jsou nepřesné a nepřesné. Tenhle lučištník potřebuje víc cvičit.

## <a name="do-you-have-enough-data-to-work-with"></a>Máte dostatek dat pro práci s?
A konečně, #4 složky jsou dostatečné údaje.

![Máte dostatek dat pro analýzu? Vyhodnocení údajů](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Představte si, že každý datový bod v tabulce je tahem štětce v obraze. Pokud máte jen několik z nich, obraz může být fuzzy - je těžké říct, co to je.

Pokud přidáte další tahy štětcem, pak váš obraz začne být trochu ostřejší.

Když máte sotva dost tahů, vidíte jen dost, abyste učinili nějaká široká rozhodnutí. Je to místo, kde bych tě chtěl navštívit? Vypadá to jasné, že vypadá jako čistá voda - ano, to je místo, kde jsem se jít na dovolenou.

Jak přidáte další data, obraz se stává jasnější a můžete provádět podrobnější rozhodnutí. Nyní se můžete podívat na tři hotely na levém břehu. Můžete si všimnout architektonických prvků toho v popředí. Můžete se dokonce rozhodnout zůstat ve třetím patře kvůli výhledu.

S daty, která jsou relevantní, propojená, přesná a dostatečná, máte všechny ingredience potřebné k tomu, abyste mohli udělat vysoce kvalitní datovou vědu.

Nezapomeňte se podívat na další čtyři videa v *datové vědě pro začátečníky* z Microsoft Azure Machine Learning Studio (klasika).

## <a name="next-steps"></a>Další kroky
* [Vyzkoušejte první experiment datové vědy se Machine Learning Studio (klasický)](create-experiment.md)
* [Získejte úvod do Machine Learningu v Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)

---
title: 'ML Studio (Classic): vyhodnocení dat – Azure'
description: Čtyři kritéria, která vaše data musí splňovat, aby byla připravená pro datové vědy. Toto video obsahuje konkrétní příklady, které vám pomůžou se základním hodnocením dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 07db1f1c8021759fbd5702ad9353f74fcd1524c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516970"
---
# <a name="is-your-data-ready-for-data-science"></a>Jsou data připravená pro vědecké zkoumání?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: řada pro datové vědy pro začátečníky
Naučte se vyhodnocovat vaše data, abyste měli jistotu, že splňují základní kritéria, která jsou připravená pro datové vědy.

Pokud chcete získat maximum z řady, Sledujte je vše. [Přejít na seznam videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Data vědy pro začátečníky* je rychlý Úvod k datové vědy v pěti krátkých videích.

* Video 1: [5 otázek pro datové vědy](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min. 14 sec)*
* Video 2: jsou vaše data připravená pro datové vědy?
* Video 3: [Položte otázku, na kterou můžete odpovědět](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 minuty 17 s)* .
* Video 4: [předpověď odpovědi pomocí jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Video 5: [kopírování práce jiných lidí do oblasti datové vědy](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min. 18 sec)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Přepis: jsou vaše data připravená pro datové vědy?
Vítá vás "jsou vaše data připravená pro datové vědy?" druhé video v *datové vědy řady pro začátečníky*.  

Než vám datové vědy poskytnou požadované odpovědi, musíte mu poskytnout některé vysoce kvalitní surové materiály, se kterými chcete pracovat. Stejně jako v případě, že je Pizza, lepšími složkami, které začnete, je lepší konečný produkt. 

## <a name="criteria-for-data"></a>Kritéria pro data
V oblasti datové vědy jsou některé složky, které je třeba provést společně, včetně:

* Významný
* Připojeno
* Způsobují
* Dostatek pro práci s

## <a name="is-your-data-relevant"></a>Jsou vaše data relevantní?
Takže první složka vyžaduje relevantní data.

![Relevantní data vs. nerelevantní data – vyhodnocování dat](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Vlevo je v tabulce uvedena hladina krevního alkoholu sedmi lidí testovaných mimo Boston, průměr červeného Soxu batting v poslední hře a cenu mléka v nejbližším obchodě s nejpohodlnější.

To je naprosto legitimní data. Je to jenom chyba, že není relevantní. Mezi těmito čísly není zřejmé vztah. Pokud vám někdo zavedl aktuální cenu mléka a průměrně červeného Soxu batting, neexistuje způsob, jak byste mohli odhadnout obsah krevního alkoholu.

Teď se podívejte na tabulku na pravé straně. Tato doba se měří na velké objemy textu jednotlivých uživatelů a také na počet nápojů, které měly.  Čísla v každém řádku jsou teď vzájemně relevantní. Pokud jsem mi dal své tělo a počet Margaritas, které jsem měl, můžete v obsahu krevního alkoholu provést odhad.

## <a name="do-you-have-connected-data"></a>Máte připojená data?
Další složkou jsou připojená data.

![Připojená data vs. odpojená data – kritéria dat, připravena data](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Tady je několik relevantních dat o kvalitě Hamburg: teplota mřížky, pattyá váha a hodnocení v místním katalogu potravin. Všimněte si ale mezer v tabulce vlevo.

U většiny datových sad chybí některé hodnoty. To je běžné, že mají otvory podobné tomuto a existují způsoby, jak je obejít. Pokud ale chybí příliš mnoho, vaše data začnou vypadat jako švýcarský sýr.

Pokud se podíváte na levou stranu v tabulce, máte tolik chybějících dat, je těžké se zavázat s jakýmkoli typem relace mezi teplotou mřížky a pattyou váhou. Tento příklad ukazuje odpojená data.

Tabulka na pravé straně je ale plná a kompletní – příkladem připojených dat.

## <a name="is-your-data-accurate"></a>Jsou vaše data přesná?
Další složkou je přesnost. Tady jsou čtyři cíle, které se mají vysáhnout.

![Přesné data a nepřesná data – kritéria dat](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Podívejte se na cíl v pravém horním rohu. Kolem oka býků je těsné seskupení přímo. To je samozřejmě přesné. Oddly v jazyce pro datové vědy, výkonu na cíli přímo pod ním se považuje také za přesný.

Pokud jste namapovali střed z těchto šipek, vidíte, že je velmi blízko k oko zápasy. Šipky se rozšíří vše kolem cíle, takže se považují za nepřesné, ale zacentrují se na základě očí zápasy, takže se považují za přesné.

Teď se podívejte na levý horní cíl. Tady se šipky blíží blízko sebe a těsné seskupení. Jsou přesné, ale jsou nepřesné, protože střed je v rámci očí. Šipky v cíli vlevo dole jsou nepřesné a nepřesné. Tento Archer potřebuje více postupů.

## <a name="do-you-have-enough-data-to-work-with"></a>Máte k dispozici dostatek dat pro práci s?
A konečně, #4 složek je dostatečné množství dat.

![Máte k analýze dostatek dat? Vyhodnocení dat](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Každý datový bod v tabulce si můžete představit jako tah štětce při malování. Pokud máte jen několik z nich, vykreslování může být neostré – je obtížné zjistit, co je.

Pokud přidáte další tahy štětce, začne vaše Malování trochu ostřejší.

Až budete mít zlomek dostatek tahů, stačí vám dodávat dostatečně široké rozhodnutí. Je někde možné navštívit? Vypadá to jasně, vypadá to, že se jedná o čistou vodu – Ano, kde mám na dovolenou.

Když přidáváte další data, může se obrázek vyjasnit a můžete vytvořit podrobnější rozhodnutí. Nyní se můžete podívat na tři hotely v levém bankě. V popředí si můžete všimnout funkcí architektury. Můžete dokonce zvolit, že se má na třetím patře zůstat, protože je zobrazení.

S daty, která jsou relevantní, připojená, přesná a dostatečná, máte k dispozici všechny složky potřebné k provedení některé z vysoce kvalitních datových věd.

Nezapomeňte se podívat na další čtyři videa v *oblasti datové vědy pro začátečníky* z Microsoft Azure Machine Learning Studio (Classic).

## <a name="next-steps"></a>Další kroky
* [Vyzkoušejte si první experiment pro datové vědy s Machine Learning Studio (Classic)](create-experiment.md)
* [Seznámení s Machine Learningem Microsoft Azure](../overview-what-is-azure-ml.md)
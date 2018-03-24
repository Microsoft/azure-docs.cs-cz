---
title: Jsou data připravená pro vědecké zkoumání? Zkušební data - Azure Machine Learning | Microsoft Docs
description: Čtyři kritéria, která musí splnit, aby připravený pro vědecké zpracování dat vaše data. Toto video má konkrétní příklady usnadní vyhodnocení základní data.
keywords: relevantní data vyhodnotit data, připravte dat, data kritéria, data připravena
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: 2d9c66d89b82c63561b147f3d2537ba6ad07c511
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="is-your-data-ready-for-data-science"></a>Jsou data připravená pro vědecké zkoumání?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Vědecké zpracování dat pro začátečníky řady
Zjistěte, jak vyhodnotit vaše data a ujistěte se, že splňuje základní kritéria bude připravená pro vědecké zpracování dat.

Získejte maximum z řady, můžete sledujte všechny. [Přejděte do seznamu videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa z této série
*Vědecké zpracování dat pro začátečníky* je rychlý úvod do vědecké zpracování dat v pěti krátké videa.

* Video 1: [5 otázky, odpovědi vědecké účely data](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 14 s min.)*
* Video 2: Je vaše data připravená pro vědecké zpracování dat?
* Video 3: [zeptejte se vám pomůže odpovědět s daty](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 17 sekundu min.)*
* Video 4: [předpovědi odpověď s jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 42 sekundu min.)*
* Video 5: [zkopírujte jiní lidé práce uděláte vědecké zpracování dat](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 18 sekundu min.)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Přepis: Je vaše data připravená pro vědecké zpracování dat?
Vítá vás "Je dat připravené pro vědecké zpracování dat?" druhý video v řadě *vědecké zpracování dat pro začátečníky*.  

Než odpovědi, které chcete, můžete udělit vědecké zpracování dat, musíte jí přidělit suroviny některé vysoce kvalitní pro práci s. Stejně jako provedení pizza, tím lépe složek, které se začíná s, tím lepší konečné produktu. 

## <a name="criteria-for-data"></a>Kritéria pro data
V vědecké zpracování dat existují určité složky, které musí být vyžádány společně včetně:

* Relevantní
* Připojeno
* Přesná
* Dost pro práci s

## <a name="is-your-data-relevant"></a>Je relevantní data?
Proto je první složka – třeba data, která jsou relevantní.

![Relevantní data oproti nahromadění irelevantních dat – vyhodnocení dat.](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Na levé straně uvede tabulky úroveň alkohol krve sedm osoby testována mimo řádku Boston, Red Sox průměr batting v jejich poslední hře a cenu mléka v úložišti nejbližší pohodlí.

Toto jsou všechny perfektně legitimní data. Pouze chyby je, že není relevantní. Není žádný zřejmé vztah mezi tato čísla. Pokud někdo Dal aktuální cena mléka a průměr batting Red Sox, neexistuje způsob jejich krve obsahu může uhodnout.

Nyní podívejte se na tabulky na pravé straně. Každá osoba textu v tuto chvíli se měří velkokapacitních spolu s počtem nápojů jste měly.  Čísla v jednotlivých řádcích jsou nyní relevantní pro sebe navzájem. Pokud vám I Dal Moje textu velkokapacitních a počet Margaritas I jste měli, můžete dokonce vytvářet odhad v mé krve alkohol obsahu.

## <a name="do-you-have-connected-data"></a>Připojení dat?
Další složky jsou připojené data.

![Připojení dat oproti odpojené data – data kritéria data připravena](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Tady jsou některé relevantní data na kvalitu hamburgers: gril teploty, patty váhy a hodnocení v místní jídlo katalogu. Ale Všimněte si, mezer v tabulce na levé straně.

Většina datové sady chybí některé hodnoty. Se běžně používají díry takto a existují způsoby, jak je obejít. Ale pokud je příliš mnoho chybí, začne vypadat mezi sýr vaše data.

Pokud se podíváte na tabulky na levé straně, je mnoho chybějící data, je pevný spolu s jakýmkoli vztah mezi mřížkou teploty a patty váhy. Tento příklad ukazuje odpojené data.

V tabulce na pravé straně, ale je plný a dokončete – příklad připojené data.

## <a name="is-your-data-accurate"></a>Je přesná data?
Další složka je přesnost. Tady jsou čtyři cíle narazí.

![Přesná data oproti nepřesných dat. - kritéria dat](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Podívejte se na cíl v pravém horním rohu. Není pravé kolem oka býků úzkou seskupení. Který je samozřejmě přesná. Oddly v jazyce vědecké zpracování dat, výkon na pravé straně cíl pod ním také považuje za přesná.

Pokud jste namapovali se na centrum tyto šipek, zobrazí se, se velmi nachází blízko oko býků. Šipky jsou všechny kolem cíl, šíření, se považují za nepřesný, ale budou se soustředí na oko býků, se považují za přesná.

Nyní se podívejte na levém cíl. Zde šipky dosáhl velmi blízko sebe úzkou seskupení. Jsou to přesné, ale jsou nesprávné, protože je mimo oko býků centru. Šipky v okraje v levém dolním cíl jsou nesprávné a nepřesný. Tato archer potřebuje další postup.

## <a name="do-you-have-enough-data-to-work-with"></a>Máte dostatek dat pro práci s?
Nakonec složka #4 je dostatečné údaje.

![Máte dostatek dat pro analýzu? Vyhodnocování dat](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Představte si každý datový bod v tabulce, že je tahu štětce v malování. Pokud máte pouze několik z nich, může být Malování přibližné – je obtížné zjistit, co je.

Pokud přidáte některé další tahy štětce, vaše Malování spustí získat trochu ostřejší.

Pokud máte dostatek sotva tahy, zobrazí jenom tehdy, dost široké rozhodnutí. Je někde, který může chcete navštívit? Vypadá to, jasně, která vypadá jako čisté horních – Ano, který je, kde bude na dovolenou.

Při přidávání více dat, obrázek bude jasnější a je možné provádět podrobnější rozhodnutí. Teď můžete prohlédnout tři hotels na levém bank. Můžete si všimnete architektury funkce v popředí. Dokonce můžete zvolit Zůstaňte na třetí podlaží z důvodu zobrazení.

Data, která jsou relevantní, připojené, přesný a dostatečně, můžete všechny složky, musel provést některé vědecké zpracování dat vysoké kvality.

Nezapomeňte si projděte si další čtyři videa v *vědecké zpracování dat pro začátečníky* z Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Další postup
* [Zkuste prvního experimentu vědecké účely data nástroje Machine Learning Studio](create-experiment.md)
* [Získejte Úvod do Machine Learning v Microsoft Azure](what-is-machine-learning.md)

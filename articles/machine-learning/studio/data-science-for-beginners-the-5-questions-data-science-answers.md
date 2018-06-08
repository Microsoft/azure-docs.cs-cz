---
title: Vědecké zpracování 5 dat otázky - datové vědy pro začátečníky - Azure Machine Learning | Microsoft Docs
description: Vědecké zpracování dat pro začátečníky je se dozvíte, jaké základní koncepce při 5 krátké videa, počínaje The 5 otázky datové vědy odpovědi. Z Azure Machine Learning.
keywords: provádění vědecké zpracování dat, data vědecké účely začátečníka, vědecké zpracování dat pro začátečníka, datové vědy základy, otázky ohledně dat vědecké účely, datové vědy video, datové vědy Úvod
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cjgronlund
ms.assetid: a01f93ee-01eb-4afe-abbd-cfa035c119b0
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: 1d004eb16fcac13d6ba7592cbe432cbeac0401e8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834147"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Vědecké zkoumání dat pro začátečníky – Video 1: 5 otázek, na které odpovídá vědecké zkoumání dat
Rychlý úvod k vědecké zpracování dat z *vědecké zpracování dat pro začátečníky* v pěti krátké videa z vědecký pracovník hlavní data. Tyto videa jsou základní ale užitečná, ať už vás zajímá provádění vědecké zpracování dat nebo práci s datových vědců.

O typech otázky, které může odpovědět vědecké zpracování dat je toto první video. Získejte maximum z řady, můžete sledujte všechny. [Přejděte do seznamu videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa z této série
*Vědecké zpracování dat pro začátečníky* je rychlý úvod do dat vědecké účely trvá asi 25 minut celkový. Podívejte se na všechny pět videa:

* Video 1: 5 otázky, odpovědi data vědecké účely
* Video 2: [vašich dat je připravený pro vědecké zpracování dat?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 56 sekundu min.)*
* Video 3: [zeptejte se vám pomůže odpovědět s daty](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 17 sekundu min.)*
* Video 4: [předpovědi odpověď s jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 42 sekundu min.)*
* Video 5: [zkopírujte jiní lidé práce uděláte vědecké zpracování dat](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 18 sekundu min.)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Přepis: 5 otázky, odpovědi data vědecké účely
Ahoj! Vítá vás série videí *vědecké zpracování dat pro začátečníky*.

Vědecké zpracování dat může být první pohled poněkud složité, tak I budete zavádět základy zde bez jakýchkoli vzorce nebo programování žargonu počítače.

V tomto videu první budeme mluvit o "5 otázky, odpovědi vědecké účely data."

Vědecké zpracování dat používá názvy (také označované jako kategorie nebo popisky) a čísla k předvídání odpovědi na otázky.

Ho může ať vás, překvapí ale *existují pouze pět otázky této datové vědy odpovědi*:

* Je toto A nebo B?
* Je to divné?
* Kolik – nebo – kolik?
* Toto uspořádání?
* Co dalšího mám udělat?

Každé z nich tyto dotazy odpovídá zapínání samostatnou rodinu machine learning metod, názvem algoritmy.

Je vhodné zamyslet o algoritmu jako recept a data podle složek. Algoritmus informuje, jak kombinovat a kombinovat data, aby bylo možné získat odpověď. Počítače jsou stejné jako mixéru. Udělají většinu práce pevný algoritmus pro vás a jejich do poměrně krátkou dobu.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Otázka č. 1: Je tento A nebo B? používá klasifikaci algoritmy
Začneme Otázka: je tento A nebo B?

![Algoritmy klasifikace: je tento A nebo B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Tuto řadu algoritmů nazývá two-class klasifikace.

Je vhodné pro všechny otázky, který má jenom dvě možné odpovědi.

Příklad:

* V další 1000 miles selže tento můžete zadat: Ano nebo ne?
* Která přináší u více zákazníků: $5 kupóny nebo 25 % sleva?

Tento dotaz může být také rephrased zahrnout více než dvě možnosti: je tento A nebo B nebo C nebo D, atd.?  To se označuje jako více třídami klasifikaci a jeho užitečné, pokud máte několik – nebo několika tisíc – možné odpovědi. Více třídami klasifikace zvolí nejpravděpodobnější jeden.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Otázka č. 2: Je to divné? používá algoritmy detekce anomálií
Další otázka dokáže odpovědět vědecké zpracování dat se: je tento divné? Tento dotaz zodpovězen řady algoritmů názvem detekce anomálií.

![Algoritmy detekce anomálií: je tento divné?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Pokud máte platební karty, jste využívali již detekce anomálií. Společnost platební karty analyzuje vašim vzorům nákupu tak, aby se vás upozorní na možný podvod. Poplatky, které jsou "divné" může být nákup na úložiště, kde není nakupovat normálně nebo kdybyste kupovali neobvykle nákladných položku.

Tento dotaz může být užitečné v mnoha různými způsoby. Například:

* Pokud máte automobilu s přetížení měřidla, můžete chtít znát: je tohoto měřidla přetížení čtení normální?
* Pokud jste monitorování Internetu, chcete vědět: Tato zpráva z Internetu je typické?

Detekce anomálií příznaky neočekávané nebo neobvyklé události nebo chování. Kde hledat problémy nabízí různá vodítka.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Otázka č. 3: Kolik? jak mnoho? používá regresní algoritmy
Machine learning můžete také předpovědi odpověď jak mnohem? jak mnoho? Rodina algoritmus, který odpoví na tuto otázku se nazývá regrese.

![Regrese algoritmy: kolik? jak mnoho?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regrese algoritmy provádět číselné předpovědi, jako například:

* Co teplota bude další úterý?  
* Co bude Moje čtvrté čtvrtletí prodej?

Pomáhají odpověď na všechny otázku, která požaduje zadání číslo.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Otázka č. 4: Toto uspořádání? používá clustering algoritmy
Na posledních dvou otázky, které jsou nyní chvilku pokročilejší.

Někdy chcete pochopit strukturu datové sady – to uspořádání? Pro tuto otázku nemáte příklady, které již znáte výsledky.

Existuje mnoho způsobů, jak tease si strukturu dat. Jeden ze způsobů je clusteringu. Ji odděluje data do přirozené "seskupí," pro snazší interpretaci. Clustering, už není žádná jeden pravé odpovědi.

![Clustering algoritmy: Toto uspořádání?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Běžné clustering otázek příkladů:

* Které prohlížeče jako stejné typy filmy?
* Které modely tiskáren selhání stejným způsobem jako?

Porozuměním uspořádání dat, můžete lépe pochopit - a předpovědi - chování a události.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Otázka č. 5: Co mám dělat nyní? používá posílení učení algoritmy
Otázka – poslední, co mám dělat? – používá řadu algoritmy názvem posílení učení.

Posílení learning byl INSPIROVANÉ jak mozky potkanů a člověka reakce na trestání a výnosu. Tyto algoritmy dozvědět se od výsledky a rozhodnout, na další akce.

Posílení learning obvykle je vhodné pro automatizované systémy, které mají být velké množství malých rozhodnutí bez lidského pokyny.

![Posílení učení algoritmy: Co dalšího mám udělat?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Otázky, které odpovídá jsou vždy o jakou akci by měla být provedena – obvykle na počítači nebo robot. Mezi příklady patří:

* Pokud jsem systému řízení teploty domu: Upravit teplota nebo nechat tam, kde je?  
* Vám Samoobslužná řízení car: žlutý indikátor brzdových nebo urychlit?  
* Pro vakuu robot: zachovat vacuuming, nebo přejděte zpět na plnících stanice?

Algoritmy učení posílení shromažďování dat jako přejít, učení ze zkušební verze a chyby.

Tak, aby se jeho - vědecké zpracování dat pro dotazy 5 může odpovědět.

## <a name="next-steps"></a>Další postup
* [Zkuste prvního experimentu vědecké účely data nástroje Machine Learning Studio](create-experiment.md)
* [Získejte Úvod do Machine Learning v Microsoft Azure](what-is-machine-learning.md)

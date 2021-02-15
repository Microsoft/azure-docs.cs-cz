---
title: 'ML Studio (Classic): datové vědy pro začátečníky – Azure'
description: Data vědy pro začátečníky nabízí základní koncepty v pěti krátkých videích, od 5 otázek pro datové vědy. Z Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: e9bbadc95be3479da82d91e9a4a4f712fbc32c57
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520387"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Vědecké zkoumání dat pro začátečníky – Video 1: 5 otázek, na které odpovídá vědecké zkoumání dat

Získejte rychlý Úvod k datovému vědy z *oblasti datové vědy pro začátečníky* v pěti krátkých videích od horního odborníka na data. Tato videa jsou základní, ale užitečná, ať už se zajímáte o práci s daty nebo pracujete s odborníky na data.

Toto první video se týká druhů otázek, které může data vědy zodpovědět. Pokud chcete získat maximum z řady, Sledujte je vše. [Přejít na seznam videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Data vědy pro začátečníky* představuje rychlý Úvod k datovému vědy s celkovým počtem 25 minut. Podívejte se na všechna pět videí:

* Video 1:5 otázek pro datové vědy
* Video 2: [jsou vaše data připravená pro datové vědy?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min. 56 s)*
* Video 3: [Položte otázku, na kterou můžete odpovědět](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 minuty 17 s)* .
* Video 4: [předpověď odpovědi pomocí jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Video 5: [kopírování práce jiných lidí do oblasti datové vědy](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min. 18 sec)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Přepis: 5 otázek pro datové vědy
Hi! Vítá vás *datové vědy řady videí pro začátečníky*.

Datové vědy můžou být zastrašování, takže sem zavedeme základy bez jakýchkoli rovnic nebo počítačového programování žargonu.

V tomto prvním videu budeme mluvit o "5 otázek pro datové vědy".

Pro účely předpovědi odpovědí na otázky používá datová věda čísla a názvy (označují se také jako kategorie nebo popisky).

Může se stát, že vám to neklade, ale *jenom pět otázek, na které data vědy odpovídá*:

* Je to A nebo B?
* Je tento divné?
* Kolik – nebo – kolik?
* Jak je to organizováno?
* Co mám dělat pak?

Každá z těchto otázek je zodpovězená samostatnou řadou metod strojového učení, které se nazývají algoritmy.

Je vhodné si představit jako recept a data jako komponenty. Algoritmus popisuje, jak kombinovat a kombinovat data, aby bylo možné získat odpověď. Počítače jsou jako Blend. Provádějí většinu tvrdosti algoritmu pro vás a jsou poměrně rychlé.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Otázka 1: je to A nebo B? používá klasifikační algoritmy
Pojďme začít s otázkou: je to A nebo B?

![Algoritmy klasifikace: je to A nebo B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Tato rodina algoritmů se nazývá klasifikace se dvěma třídami.

Je vhodný pro všechny otázky, které mají jenom dvě možné odpovědi.

Příklad:

* Netire se toto selhání za dalších 1 000 mil: Ano nebo ne?
* Který přináší větší zákazníky: $5 kupón nebo 25% sleva?

Tato otázka může být také přeformulovaná, aby zahrnovala více než dvě možnosti: je to A nebo B nebo C nebo D atd.?  Tato možnost se nazývá klasifikace s více třídami a je užitečná, když máte několik (nebo několik tisíc) možných odpovědí. Klasifikace s více třídami volí nejpravděpodobnější.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Otázka 2: je to divné? používá algoritmy detekce anomálií.
Na příští otázky data vědy může odpovědět: je to divné? Tato otázka je zodpovězena řadou algoritmů nazývaných detekci anomálií.

![Algoritmy detekce anomálií: Jedná se o tento divné?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Pokud máte platební kartu, už jste využili výhod detekce anomálií. Vaše společnost platební karty analyzuje vaše vzory nákupu, aby vás upozornila na případné podvody. Poplatky, které jsou "divné", můžou být nákupy v obchodě, kde obvykle nekoupíte nebo nekupujete neobvykle cenovou položku.

Tato otázka může být užitečná v mnoha různých ohledech. Například:

* Pokud máte automobil s měřiči tlaku, možná budete chtít znát: Jedná se o tento měřič tlaků běžný?
* Pokud sledujete Internet, měli byste znát: je to obvyklá zpráva z Internetu?

Detekce anomálií označuje neočekávané nebo neobvyklé události nebo chování. Poskytuje podobu, kde hledat problémy.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Otázka 3: jak spousta? nebo kolik? používá regresní algoritmy
Machine Learning může také odhadnout odpověď na to, kolik máte? nebo kolik? Rodina algoritmů, která odpovídá této otázce, se nazývá regrese.

![Regresní algoritmy: jak velký? nebo kolik?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regresní algoritmy vytvářejí číselné předpovědi, například:

* Jaká je teplota v příštím úterý?  
* Co bude mít prodej ve čtvrtém čtvrtletí?

Pomůžou odpovědět na všechny otázky, které se na číslo zobrazí.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Otázka 4: jak je to organizováno? používá algoritmy clusteringu.
Poslední dva dotazy jsou teď trochu pokročilejší.

Někdy chcete pochopit strukturu datové sady – jak je to organizováno? Pro tuto otázku nemáte příklady, které už znáte s výsledky.

Existuje spousta způsobů, jak vyhledáníovat strukturu dat. Jedním z přístupů je clustering. Odděluje data na přirozené "clumps", aby bylo snazší interpretovat. Při clusteringu neexistuje žádná pravá odpověď.

![Algoritmy clusteringu: jak je to organizováno?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Mezi běžné příklady otázek clusteringu patří:

* Kteří uživatelé mají stejný typ filmů?
* Které modely tiskárny selžou stejným způsobem?

Pochopením způsobu uspořádání dat můžete lépe pochopit a předpovídat – chování a události.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Otázka 5: co mám dělat? používá sledovací algoritmy pro posílení.
Poslední otázka – co mám dělat? – používá řadu algoritmů označovaných jako posílení učení.

Posílení učení bylo nechte inspirovat způsobem, jakým mozki potkanů a lidí reagují na trest a odměny. Tyto algoritmy se učí od výsledků a rozhodují se o další akci.

Pro automatizované systémy, které musí provádět velké množství malých rozhodnutí bez použití lidských pokynů, je obvykle lepší posílit vzdělávání.

![Posílení výukových algoritmů: co dál mám udělat?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Otázky, na které odpovíte, jsou vždycky informace o tom, jaká akce by se měla provést – většinou se jedná o počítač nebo robot. Příklady:

* Pokud jsem systém kontroly teploty pro dům: Upravte teplotu nebo ponechte ji tam, kde je?  
* Pokud jsem vlastní hnací auto: žluté světlo, brzdu nebo zrychlit?  
* Pro vakuový robot: ponechte si pokračovat v vaku nebo se vraťte na stanici zpoplatnění?

Posílení výukových algoritmů shromažďujících data při jejich přechodu, učení ze zkušební verze a chyby.

Takže to je 5 otázek, na které se data vědy může zodpovědět.

## <a name="next-steps"></a>Další kroky
* [Vyzkoušejte si první experiment pro datové vědy s Machine Learning Studio (Classic)](create-experiment.md)
* [Seznámení s Machine Learningem Microsoft Azure](../overview-what-is-azure-ml.md)
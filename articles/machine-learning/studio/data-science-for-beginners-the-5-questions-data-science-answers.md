---
title: Vědecké zpracování dat pro začátečníky
titleSuffix: ML Studio (classic) - Azure
description: Datová věda pro začátečníky učí základní pojmy v 5 krátkých videích, počínaje 5 otázkami Odpovědi na datové vědy. Z Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: af0611e121228a587e159bd4e6529c807132d16d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204439"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Vědecké zkoumání dat pro začátečníky – Video 1: 5 otázek, na které odpovídá vědecké zkoumání dat
Získejte rychlý úvod do datové vědy z *datové vědy pro začátečníky* v pěti krátkých videích od špičkového datového vědce. Tato videa jsou základní, ale užitečná, ať už máte zájem o datovou vědu nebo pracujete s datovými vědci.

Toto první video je o typech otázek, na které může datová věda odpovědět. Chcete-li ze série vytěžit maximum, sledujte je všechny. [Přejděte na seznam videí.](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Datová věda pro začátečníky* je rychlý úvod do datové vědy, která trvá celkem asi 25 minut. Podívejte se na všech pět videí:

* Video 1: 5 otázek data vědy odpovědi
* Video 2: [Jsou vaše data připravena pro datovou vědu?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 s)*
* Video 3: [Zeptejte se na otázku, na kterou můžete odpovědět pomocí dat](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec)*
* Video 4: [Předvídejte odpověď pomocí jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 s)*
* Video 5: [Kopírování práce jiných lidí pro datovou vědu](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 s)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Přepis: 5 otázek data vědy odpovědi
Hi! Vítejte na video série *Data Science pro začátečníky*.

Data Science může být zastrašující, takže vám představím základy bez rovnic nebo žargonu počítačového programování.

V tomto prvním videu budeme hovořit o "5 otázek, na které datové vědy odpovídá".

Datová věda používá čísla a názvy (označované také jako kategorie nebo popisky) k předpovídání odpovědí na otázky.

Mohlo by vás to překvapit, ale *existuje pouze pět otázek, které data science odpovědi*:

* Je to A nebo B?
* Je to divné?
* Kolik - nebo - Kolik?
* Jak je to organizováno?
* Co mám dělat pak?

Každá z těchto otázek je zodpovězena samostatnou rodinou metod strojového učení, nazývaných algoritmy.

Je užitečné přemýšlet o algoritmu jako o receptu a o vašich datech jako o složkách. Algoritmus říká, jak kombinovat a kombinovat data s cílem získat odpověď. Počítače jsou jako mixér. Dělají většinu tvrdé práce algoritmu pro vás a dělají to docela rychle.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Otázka 1: Je to A nebo B? používá klasifikační algoritmy
Začněme s otázkou: Je to A nebo B?

![Klasifikační algoritmy: Je to A nebo B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Tato rodina algoritmů se nazývá klasifikace dvou tříd.

Je to užitečné pro každou otázku, která má jen dvě možné odpovědi.

Například:

* Selhat tato pneumatika v příštích 1000 mil: Ano nebo ne?
* Což přináší více zákazníků: 5 dolarů kupón nebo 25% sleva?

Tuto otázku lze také přeformulovat tak, aby zahrnovala více než dvě možnosti: Je to A nebo B nebo C nebo D atd.?  To se nazývá klasifikace více tříd a je užitečné, když máte několik – nebo několik tisíc – možné odpovědi. Klasifikace více tříd zvolí nejpravděpodobnější.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Otázka 2: Je to divné? používá algoritmy detekce anomálií
Další otázka, na kterou může datová věda odpovědět, zní: Je to divné? Na tuto otázku odpovídá rodina algoritmů nazývaných detekce anomálií.

![algoritmy detekce anomálií: Je to divné?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Pokud máte kreditní kartu, už jste měli prospěch z detekce anomálií. Vaše společnost vydávající kreditní karty analyzuje vaše nákupní vzorce, aby vás mohla upozornit na možné podvody. Poplatky, které jsou "divné", mohou být nákupem v obchodě, kde obvykle nenakupujete nebo nekupujete neobvykle drahý předmět.

Tato otázka může být užitečná v mnoha ohledech. Například:

* Pokud máte auto s manometrem, možná budete chtít vědět: Je to tlakoměr čtení normální?
* Pokud sledujete internet, chtěli byste vědět: Je tato zpráva z internetu typická?

Detekce anomálií příznaky neočekávané nebo neobvyklé události nebo chování. To dává vodítka, kde hledat problémy.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Otázka 3: Kolik? nebo Kolik? používá regresní algoritmy
Strojové učení může také předpovědět odpověď na Kolik? nebo Kolik? Rodina algoritmu, která odpovídá na tuto otázku, se nazývá regrese.

![Regresní algoritmy: Kolik? nebo Kolik?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regresní algoritmy, aby numerické předpovědi, jako například:

* Jaká bude teplota příští úterý?  
* Jaké budou prodeje za čtvrté čtvrtletí?

Pomáhají odpovědět na jakoukoli otázku, která žádá o číslo.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Otázka 4: Jak je to organizováno? používá clustering ové algoritmy
Poslední dvě otázky jsou trochu pokročilejší.

Někdy chcete pochopit strukturu datové sady - Jak je to organizováno? Pro tuto otázku nemáte příklady, pro které již znáte výsledky.

Existuje mnoho způsobů, jak dráždit strukturu dat. Jedním z přístupů je clustering. Odděluje data do přirozených "shluků", pro snadnější interpretaci. S clustering, neexistuje nikdo správnou odpověď.

![Clustering algoritmy: Jak je to organizováno?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Běžné příklady shlukování otázek jsou:

* Kteří diváci mají rádi stejné typy filmů?
* Které modely tiskáren selhávají stejným způsobem?

Pochopením toho, jak jsou data uspořádána, můžete lépe pochopit a předvídat chování a události.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Otázka 5: Co mám dělat teď? používá algoritmy výztuže učení
Poslední otázka - Co mám dělat teď? – používá rodinu algoritmů s názvem posilování učení.

Posilovací učení bylo inspirováno tím, jak mozky krys a lidí reagují na tresty a odměny. Tyto algoritmy se učí z výsledků a rozhodují o další akci.

Obvykle, posílení učení je vhodný pro automatizované systémy, které mají dělat spoustu malých rozhodnutí bez lidského vedení.

![Algoritmy posilování učení: Co mám dělat dál?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Otázky, na které odpovídá, jsou vždy o tom, jaké kroky by měl y přijmout - obvykle stroj nebo robot. Můžete například:

* Pokud jsem systém regulace teploty pro dům: Nastavte teplotu nebo ji nechte tam, kde je?  
* Pokud jsem auto s vlastním pohonem: Při žlutém světle, brzdit nebo zrychlit?  
* Pro vakuum robota: Pokračujte ve vysávání nebo se vraťte do nabíjecí stanice?

Algoritmy posilování učení shromažďují data za chodu a učí se z pokusů a omylů.

Tak to je ono - 5 otázek datové vědy může odpovědět.

## <a name="next-steps"></a>Další kroky
* [Vyzkoušejte první experiment datové vědy se Machine Learning Studio (klasický)](create-experiment.md)
* [Získejte úvod do Machine Learningu v Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)

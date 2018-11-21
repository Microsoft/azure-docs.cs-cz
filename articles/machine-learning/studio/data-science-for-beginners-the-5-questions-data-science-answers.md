---
title: 5 datových vědách souvisejících s dotazy – datová věda pro začátečníky – Azure Machine Learning | Dokumentace Microsoftu
description: Vědecké zpracování dat pro začátečníky je vás naučí základní koncepce při 5 krátkých videí, počínaje The 5 odpovídá vědecké zkoumání dat pro dotazy. Ze služby Azure Machine Learning.
keywords: provádění datových věd, začátečníky datové vědy, datové vědy pro začátečníky, základní informace o datové vědy, data science dotazy, data science video, datové vědy Úvod
services: machine-learning
documentationcenter: na
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 2e3d8805bfbe5b55aed111090cf1f7c02acb6a76
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262036"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Vědecké zkoumání dat pro začátečníky – Video 1: 5 otázek, na které odpovídá vědecké zkoumání dat
Rychlý úvod do datových věd z *datová věda pro začátečníky* v pěti krátkých videích z hlavní vědec. Tato videa jsou užitečné, ale základní, ať už vás zajímá datových věd nebo pracujete s odborníky přes data.

Je toto první video o druzích otázek, které dokáže odpovědět pro datové vědy. Využijte naplno řady, můžete sledujte všechny. [Přejít na seznam videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa v této sérii
*Datová věda pro začátečníky* článek obsahuje rychlý úvod do data science trvá přibližně 25 minut celkový počet. Prostudujte si všechna videa pět:

* Video 1: 5 otázky, které odpovídá vědecké zkoumání dat
* Video 2: [jsou vaše data připravená pro vědecké zpracování dat?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sek)*
* Video 3: [položit dotaz lze odpovědět pomocí dat](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: [předpověď odpovědi pomocí jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek)*
* Video 5: [kopírování práce jiných lidí pro vědecké zkoumání dat](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Přepis: 5 otázky, které odpovídá vědecké zkoumání dat
Ahoj! Vítá vás série videí *datová věda pro začátečníky*.

Vědecké zpracování dat může být vám nemusí nahánět strach, tak I budete zavádět základy zde bez jakýchkoli rovnice nebo počítač programování žargonu.

V tomto videu první budeme mluvit o "5 otázky, které odpovídá vědecké zkoumání dat."

Vědecké zpracování dat používá pro předpověď odpovědi na otázky názvy (označované také jako kategorií nebo popisky) a čísla.

To může překvapí vás, ale *nějaké otázky, zobrazuje se jenom pět této odpovídá vědecké zkoumání dat*:

* Je tento A nebo B?
* Je to divné?
* Kolik – nebo – kolik?
* Toto uspořádání?
* Co mám dělat další?

Jedna z těchto otázek je zodpoví samostatné řady metod machine learning, neboli algoritmy.

Je vhodné uvažovat o algoritmu jako recept a vaše data způsobem složek. Algoritmus, který určuje, jak zkombinovat a kombinovat data, aby bylo možné získat odpověď. Počítače jsou podobné blender. Většina těžkou práci algoritmu dělají za vás a dělají to hodně rychlé.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Otázka 1: Je tento A nebo B? používá klasifikaci algoritmy
Začněme Otázka: je tento A nebo B?

![Algoritmy klasifikace: je tento A nebo B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Tato řada algoritmy se nazývá dvěma třídami klasifikace.

To je užitečné pro libovolný dotaz, který má jenom dvě možné odpovědi.

Příklad:

* V dalším 1 000 mil selže tato můžete zadat: Ano nebo ne?
* Což přináší větší počet zákazníků: 5 USD kupónu nebo 25 % slevu?

Tento dotaz může být také rephrased zahrnout více než dvě možnosti: je tento A nebo B nebo C nebo D, atd.?  Tento postup se nazývá klasifikace víc tříd a je užitečné, pokud máte několik, nebo několik tisíc – je to možné odpovědi. Klasifikace víc tříd vybere ten největší pravděpodobností.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Otázka 2: Je to divné? používá algoritmy detekce anomálií
Další otázky pro datové vědy můžete odpovědět: je tento divně? Tento dotaz zodpovězen podle řady algoritmů volá detekce anomálií.

![Algoritmy detekce anomálií: je tento divně?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Pokud máte platební kartu, jste využívali již detekce anomálií. Vaše společnost platební karty analyzuje vaše postupy nákupní tak, aby se vám upozornění na možný podvod. Náklady, které jsou "divné" může být nákup v úložišti, kde není nakupování obvykle nebo nákupu neobvykle nákladných položky.

Tento dotaz může být užitečné v mnoha způsoby. Příklad:

* Pokud máte automobilu s měřidla přetížení, můžete chtít vědět: je tohoto měřidla tlak čtení normální?
* Pokud monitorujete Internetu, byste měli vědět: je typické, tato zpráva od Internetu?

Detekce anomálií příznaky neočekávaná nebo neobvyklá událostí nebo chování. Poskytuje příčiny, kde hledat problémů.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>3 Otázka: Kolik? nebo jak mnoho? používá algoritmy regrese
Machine learning můžete také předpověď odpovědi jak mnohem? nebo jak mnoho? Algoritmus skupina, která odpovídá na tuto otázku se nazývá regrese.

![Regrese, algoritmy: kolik? nebo jak mnoho?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regrese, algoritmy predikci číselné, jako například:

* Jaké teplota budou další úterý?  
* Co bude svoje prodeje na čtvrté čtvrtletí

Pomáhají odpovědět na každou otázku, která vyzve k zadání čísla.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Otázka 4: Toto uspořádání? použití clusteringu s algoritmy
Poslední dva dotazy jsou nyní bit rozšířené.

Někdy chcete podrobněji seznámit se strukturou datové sady – toto uspořádání? Pro tento dotaz nemáte příklady, které už znáte výsledky.

Existuje mnoho způsobů, jak tease struktury data. Jedním z přístupů je clustering. Rozděluje data do fyzické "seskupí," pro snazší výkladu. S clusteringem, neexistuje žádná správná odpověď jeden.

![Clustering algoritmy: Toto uspořádání?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Mezi běžné příklady clusteringu dotazy jsou:

* Které prohlížeče, jako jsou stejné typy filmy?
* Které modely tiskáren selhání stejným způsobem?

Když porozumíte tomu, jakým způsobem je organizována data, můžete lépe pochopit – a předpovídat - chování a události.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>5 Otázka: Co mám teď? používá učení se supervizí zpětnovazebnému
Otázka – poslední, co mám dělat, nyní? – používá řadu algoritmy volá zpětnovazebnému učení.

Zpětnovazebnému učení se inspirovat jak mozky potkanů a lidé reagují na smrti a odměny. Tyto algoritmy Učte se od výsledky a rozhodnout o další akci.

Zpětnovazebnému učení je obvykle vhodné pro automatizované systémy, které se musí provést velké množství malých rozhodnutí bez lidské pokyny.

![Učení se supervizí zpětnovazebnému: Co mám dělat pak?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Otázky, které odpovídá jsou vždy o jaká akce se má vzít – obvykle na počítači nebo robot. Můžete například:

* Pokud jsem systému řízení teploty domu: teplotu, nebo nechat tam, kde je?  
* Pokud jsem samořídicí automobilu: žlutý indikátor brzdových nebo zrychlení?  
* Pro takový robot: zachovat vacuuming nebo přejděte zpět na nabíjení stanice?

Algoritmy zpětnovazební učení shromažďování dat, jako jsou go, učit se z omyl a.

Tak je to – 5 otázek datové vědy můžete odpovědět.

## <a name="next-steps"></a>Další postup
* [Zkuste prvního experimentu vědy data s Machine Learning Studio](create-experiment.md)
* [Úvod do strojového učení v Microsoft Azure](what-is-machine-learning.md)

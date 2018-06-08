---
title: Požádejte otázku dat může odpovědět - data vědecké účely úlohy - Azure Machine Learning | Microsoft Docs
description: Naučte se formulovali dotaz vědecké účely sharp data v vědecké zpracování dat pro začátečníky video 3. Obsahuje porovnání klasifikace a regrese otázky.
keywords: datové vědy problémy, vědecké účely otázky ohledně dat, formulovali otázky, regrese otázky, klasifikace otázky, sharp otázku
services: machine-learning
documentationcenter: na
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cjgronlund
ms.assetid: 5b9501e3-9964-417a-8ffc-8913103da77b
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: 2078f7e84522a40cf6fcf6dff860d24307084467
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833797"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Položení otázky, na kterou lze odpovědět pomocí dat
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Vědecké zpracování dat pro začátečníky řady
Naučte se formulovali problému vědecké účely dat do svůj dotaz vědecké zpracování dat pro začátečníky video 3. Toto video obsahuje porovnání otázky pro klasifikaci a regrese algoritmy.

Získejte maximum z řady, můžete sledujte všechny. [Přejděte do seznamu videí](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Další videa z této série
*Vědecké zpracování dat pro začátečníky* je rychlý úvod do vědecké zpracování dat v pěti krátké videa.

* Video 1: [5 otázky, odpovědi vědecké účely data](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 14 s min.)*
* Video 2: [vašich dat je připravený pro vědecké zpracování dat?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 56 sekundu min.)*
* Video 3: Položte dotaz, který vám pomůže odpovědět s daty
* Video 4: [předpovědi odpověď s jednoduchého modelu](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 42 sekundu min.)*
* Video 5: [zkopírujte jiní lidé práce uděláte vědecké zpracování dat](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 18 sekundu min.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Přepis: Položte dotaz, který vám pomůže odpovědět s daty
Vítá vás třetí video v řadě "Vědecké zpracování dat pro začátečníky."  

V této jeden získáte tipy pro formulování dotaz, který vám pomůže odpovědět s daty.

Další využití toto video, může získat, pokud nejprve sledovat dvě starší videa z této série: "vědecké zpracování dat 5 otázky může odpovědět na" a "Je vaše data jsou připravena k vědecké zpracování dat?"

## <a name="ask-a-sharp-question"></a>Zeptejte se sharp
Jste už jsme mluvili o tom, jak vědecké zpracování dat pomocí názvů (také nazývané kategorie nebo popisky) a čísel k předvídání odpověď na otázku. Ale nemůže být jen maskou pro jakýkoli otázku; je třeba *sharp otázku.*

Nepřesných dotaz nemusí odpovídat názvu nebo číslem. Musí být sharp otázku.

Představte si, že jste našli magic svítilny s genie, kdo bude pravdivě zodpovědět všechny otázku. Ale je mischievous genie a mohl budete pokusí provést jeho odpovědí jako nepřesných a matoucí, jak si můžete rychle získat s. Chcete připnout mu s vzduchotěsným proto mu nelze pomoci ale zjistit, co chcete vědět, dotaz.

Pokud byste chtěli zeptejte nepřesných, jako jsou "Co se má stát s Moje stock?", může odpovědět genie, "změní za cenu". Který je pravdivou odpovědí, ale je velmi užitečné.

Ale pokud byste chtěli sharp zeptejte, jako je "Co Moje stock prodej ceny budou příští týden?", nelze genie pomoci ale získáte konkrétní odpovědět a předpovídat cenu prodej.

## <a name="examples-of-your-answer-target-data"></a>Příklady odpověď: cílová data
Jakmile jste formulovali dotaz, zkontrolujte, zda je nutné příklady odpověď ve vašich datech.

Pokud je naše otázku "Co Moje stock prodej cena bude příští týden?" potom máme Ujistěte se, že naše data zahrnují cenu akcií historie.

Pokud je naše otázku "které car v mé firemního vozového bude nejprve nezdaří?" potom máme Ujistěte se, že naše data zahrnují informace o předchozích chybách.

![Cílová data - příklady odpověď. Formulovali dotaz vědecké účely data.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Tyto příklady odpovědi se označují jako cíl. Cílem je, co se pokoušíme předpovídat budoucí datové body, jestli je kategorii nebo číslo.

Pokud nemáte k dispozici žádná data target, budete muset získat některé. Nebudete moci odpovídající vaší otázce bez ní.

## <a name="reformulate-your-question"></a>Byla znovu formulována váš dotaz
Někdy může změna znění váš dotaz získat užitečnější odpovědí.

Na otázku "Je tento datový bod A nebo B?" předpovídá kategorie (nebo název nebo popisek) určitého objektu. K hovor, používáme *klasifikační algoritmus*.

Otázka "Kolik?" nebo "Kolik?" předpovídá dobu. K hovor používáme *regresní algoritmus*.

Informace o tom, jak tyto můžete převést jsme, podíváme se na otázku, "které zprávy scénáře je nejvíce zajímavé pro tento čtečky?" Zjišťuje předpovědi jednu volbu z mnoha možností – jinými slovy "Je tento A nebo B nebo C nebo D?" - a využije klasifikační algoritmus.

Ale touto otázkou mohou být snazší s dotazem, zda Změna znění jej jako "jak zajímavé je každý článek v tomto seznamu k této čtečky?" Teď můžete udělit jednotlivých článků číselné skóre a pak je snadné identifikovat článku nejvyšší vyhodnocování. Toto je změnit formulaci otázky klasifikace do regrese otázku nebo kolik?

![Byla znovu formulována svůj dotaz. Klasifikace otázka oproti regrese otázku.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Jak požádat, že dotaz je potvrzením, na který algoritmus můžou dát odpověď.

Zjistíte, že jsou některé rodiny algoritmů – jako jsou v našem příkladu scénáře news - úzce související. Byla znovu formulována svůj dotaz, použít algoritmus, který vám dává nejužitečnější odpověď.

Ale, nejdůležitější požádejte tuto sharp otázku - otázku, která vám pomůže odpovědět s daty. A zkontrolujte, zda že máte správná data na hovor.

Jste už jsme mluvili o některých základních zásad pro dotaz s dotazem, že vám pomůže odpovědět s daty.

Ujistěte se, podívejte se na ostatní videa v "Datové vědy pro začátečníky" z Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Další postup
* [Zkuste prvního experimentu vědecké účely data nástroje Machine Learning Studio](create-experiment.md)
* [Získejte Úvod do Machine Learning v Microsoft Azure](what-is-machine-learning.md)

---
title: Lepší přesnost vize & klasifikace modelů počítače v Azure Machine Learning
description: Naučte se zlepšit přesnost klasifikace obrázek vize počítač, objekt detekce a modely podobnosti bitovou kopii pomocí Azure Machine Learning balíčku pro počítač vize.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: e134e1e544c51d6756d5021fef8c049fe7d8afb0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788771"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Zajištění přesnosti modely vize počítače

S **Azure Machine Learning balíček pro počítač vize**, můžete sestavit a nasazení bitové kopie klasifikace objektu detekce a bitové kopie podobnosti modelů. Další informace o tomto balíčku a k jeho instalaci.

V tomto článku zjistěte, jak a systém doladit těchto modelů, zvýšit jeho přesnost. 

## <a name="accuracy-of-image-classification-models"></a>Přesnost klasifikace modelů bitové kopie

Balíček vize počítače se zobrazí poskytují dobrý výsledky pro širokou škálu datové sady. Ale stejně jako u většiny machine learning projekty, získávání nejlepší možný výsledky pro nová datová sada vyžaduje pečlivě parametr ladění a vyhodnocení různých rozhodnutí. V následujících částech najdete pokyny o tom, jak zlepšit přesnost v dané datové sadě, to znamená, jaké parametry jsou nejslibnějším k optimalizaci nejprve, jaké hodnoty pro tyto parametry jednu akci a jaké nástrahy, aby se zabránilo.

Obecně řečeno školení hloubkové Learning modely se dodává s kompromis mezi čas školení a přesnosti modelu. Balíček vize počítač má předem nastavené výchozí parametry (viz první řádek v tabulce níže), které soustředit na rychlost rychlé školení při obvykle vytváření modelů vysokou přesnost. Tato přesnost je zlepšit často další, například pomocí vyšší rozlišení obrázku nebo hlubší modely, ale za cenu zvýšení školení čas faktorem 10 x nebo víc.

Ho je doporučujeme, abyste nejdřív spolupracujete výchozí parametry, trénování modelu, zkontrolujte výsledky, opravte základů pravdivosti poznámky podle potřeby a pouze opakujte parametry, které zpomalit školení čas (viz následující hodnoty parametrů navrhované tabulce). Doporučujeme ale představu o těchto parametrů při to technicky vzato není nezbytné.


### <a name="best-practices-and-tips"></a>Osvědčené postupy a tipy

* Data quality: učení a testovací sady by měla být vysoké kvality. To znamená, že jsou bitové kopie poznámkou správně, odebrány nejednoznačný obrázky (třeba kde je jasné k lidské oko Pokud obrázek ukazuje tenis míč nebo Citronový) a atributy se vzájemně vylučují (to znamená, každé bitové kopie patří do přesně jeden atribut).

* Před upřesnění DNN, by Trénink třídění SVM pomocí předem vyškolení a pevné DNN jako featurizer. To je podporován v balíčku vize počítači a nevyžaduje dlouho cvičení vzhledem k tomu, že se nemění DNN sám sebe. Tento jednoduchý přístup často dosahuje dobrý přesnostmi a proto představuje silné směrného plánu. Dalším krokem je pak Upřesnit DNN, který měl dát lepší přesnosti.

* Pokud objekt-vás zajímají malý obrázek, jsou známé přístupy klasifikace Image nebude fungovat správně. V takových případech zvažte použití přístup zjišťování objektu, například počítač vize balíček rychlejší R-CNN podle Tensorflow.

* Další Cvičná data tím lépe. Jako pravidlo z – Flash jeden musí mít nejméně 100 příklady pro každou třídu, který je 100 bitové kopie pro "pes", 100 bitových kopií pro "kočka", atd. Cvičení modelu pomocí méně bitových kopií je možné, ale také nemusí vracet správné výsledky.

* Obrázky školení muset uloženy místně na počítači s GPU a být na jednotku SSD (ne HDD). Latence ve čtení bitové kopie, jinak hodnota může výrazně zjednodušit školení rychlost (faktorem i 100 x).


### <a name="parameters-to-optimize"></a>Parametry pro optimalizaci

Hledání optimální hodnoty pro tyto parametry je důležité a můžete často zdokonalit výrazně přesnost:
* Učení rychlost (`lr_per_mb`): pravděpodobně nejdůležitější parametr získat správné. Pokud přesnost na školení nastavit po DNN upřesnění výše ~ 5 %, pak s největší pravděpodobností rychlost učení je buď příliš vysoké nebo počet školení epoch příliš nízká. Zejména s malé datové sady obvykle DNN přepsání vejít na Cvičná data, ale v praxi, které to povede k funkčním modely na test nastavené. Obvykle používáme 15 epoch kde rychlost počáteční učení se snižuje dvakrát; v některých případech může školení pomocí více epoch zlepšit výkon.

* Vstupní řešení (`image_dims`): výchozí rozlišení obrázku je 224 x 224 pixelů. Pomocí vyšší image řešení, například 500 x 500 pixelů nebo 1 000 × 1000 pixelů může výrazně zlepšit přesnost, ale zpomaluje DNN vylepšení. Balíček vize počítače očekává vstupní řešení jako řazené kolekce členů (kanály barvu, šířku obrázku, výška obrázku), například (3, 224, 224), kde počet kanálů barva má být nastavena na 3 (červená-zelená-modrá pásma).

* Modelování architektury (`base_model_name`): použijte hlubší DNNs například ResNet 34 nebo ResNet – 50 místo výchozího ResNet 18 modelu. Model Resnet – 50 není pouze hlubší, ale jeho výstup předposlední vrstvy je velikost 2048 obtékaných objektů (vs. 512 obtékaných objektů ResNet 18 a ResNet 34 modely). Tato zvýšená dimenzionalitu může být obzvláště užitečné, když zachování DNN fixed a místo toho třídění SVM školení.

* Velikost minibatch (`mb_size`): Vysoká minibatch velikosti povede k školení rychlejší ale za cenu vyšší spotřebu paměti DNN. Proto při výběru hlubší modely (například ResNet – 50 versus ResNet 18) nebo vyšší rozlišení obrázku (500\*500 pixelů versus 224\*224 pixelů), obvykle je nutné snížit velikost minibatch tak, aby zabránil chybám při nedostatku paměti. Při změně velikosti minibatch, často i rychlost učení musí být upravena, jak je vidět v následující tabulce.
* Míra rozevírací out (`dropout_rate`) a L2 regularizer (`l2_reg_weight`): přečerpání přizpůsobování DNN lze snížit pomocí Odpadlík míra 0,5 (výchozí hodnota je 0,5 v počítači vize balíčku) nebo víc a zvýšením váhy regularizer (výchozí hodnota je 0.0005 v počítači vize Balíček). Mějte, zejména s malé datové sady DNN přečerpání přizpůsobování je obtížné často znemožňuje, aby se zabránilo.


### <a name="parameter-definitions"></a>Definicemi parametrů

- **Učení míra**: krok velikost použitých během přechodu klesání učení. Pokud sada příliš nízkou pak model bude trvat mnoho epoch ke cvičení, pokud sada je příliš vysoká. pak model neprovede sblížení s dobrým řešením. Plán se zpravidla používá, kde se po překročení určitého počtu epoch snižuje rychlost učení. Příklad E.For výukové míra plán `[0.05]*7 + [0.005]*7 + [0.0005]` odpovídající pomocí nejprve sedm epoch rychlost učení počáteční hodnotu 0,05, následuje 10 x rychlost snížené učení 0,005 pro jiné sedm epoch a nakonec doladění modelu pro jeden epoch při 100 x snižuje rychlost učení 0.0005.

- **Velikost minibatch**: grafickými procesory může zpracovat více bitových kopií paralelně pro urychlení výpočtu. Tyto paralelní zpracování Image se taky označují jako minibatch. Čím minibatch velikost rychlejší školení bude, ale za cenu vyšší spotřebu paměti DNN.

### <a name="recommended-parameter-values"></a>Doporučené parametr hodnoty

Následující tabulka poskytuje sady různých parametrů, které byly zobrazeny k vytvoření vysokou přesnost modely na širokou škálu úloh klasifikace bitové kopie. Optimální parametry závisí na konkrétní datové sady a na přesný GPU používá, proto v tabulce, měla by se zobrazit pouze obecně. Po pokusu o těchto parametrů, zvažte také rozlišení obrázku více než 500 x 500 pixelů nebo hlubší modely jako Resnet 101 nebo Resnet 152.

První řádek v tabulce odpovídá výchozí parametry, které jsou nastaveny v počítači vize balíčku. Trvat delší dobu cvičení ale (uvedené v první sloupec), všech ostatních řádků v výhodou vyšší přesnosti (viz druhý sloupec průměrná přesnost přes tři vnitřní datové sady). Například parametrů v poslední řádek trvat déle, než se cvičení, 5-15 x, ale výsledkem vyšší přesnosti (zprůměrovanou) na tři interní testovacích sad z 82.6 % 92.8 %.

Hlubší modely a vyšší vstupní řešení vyžadují více paměti DNN, a proto musí velikost minibatch snížení s vyšší modelu složitost tak, aby zabránil na více systémů z paměti chybám. Jak je vidět v následující tabulce, je vhodné vždy, když snížení minibatch velikost ve stejné násobitel snižte rychlost učení dvakrát. Velikost minibatch pravděpodobně nutné získat snížit další na grafickými procesory s menší množství paměti.

| Školení čas (odhad) | Příklad přesnost | Velikost minibatch (*mb_size*) | Učení rychlost (*lr_per_mb*) | Obrázek řešení (*image_dims*) | Architektura DNN (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 x (referenční dokumentace) | 82.6 % | 32 | [hodnotu 0,05] \*7 + [0,005]\*7 + [0.0005]  | (3, 224 224.) | ResNet18_ImageNet_CNTK |
| 2 až 5 x    | 90.2 % | 16 | [0.025] \*7 + [0.0025]\*7 + [0.00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2 až 5 x    | 87,5 % | 16 | [0.025] \*7 + [0.0025]\*7 + [0.00025] | (3, 224 224.) | ResNet50_ImageNet_CNTK |
| 5-15 x        | 92.8 % |  8 | [0,01] \*7 + [0,001]\*7 + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Další postup

Informace o balíčku Azure Machine Learning pro počítač vizi:
+ Podívejte se na referenční dokumentaci k nástroji

+ Další informace o [další balíčky Python pro Azure Machine Learning](reference-python-package-overview.md)
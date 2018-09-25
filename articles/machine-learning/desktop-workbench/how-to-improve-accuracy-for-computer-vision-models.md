---
title: Vyšší přesnost modelů počítačového zpracování obrazu a klasifikace ve službě Azure Machine Learning
description: Zjistěte, jak zlepšit přesnost klasifikace obrázků počítače pro zpracování obrazu, zjišťování objektů a image podobnosti modely s využitím balíček Azure Machine Learning pro počítačové zpracování obrazu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 6d6c540cd8711ae89784cdc797ca56d312522a83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989307"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Zvyšte přesnost modelů počítačového zpracování obrazu

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

S **Azure Machine Learning balíček pro počítačové zpracování obrazu**, můžete vytvářet a nasazovat klasifikace obrázků, zjišťování objektů a obrázek modely podobnosti. Další informace o tento balíček a jeho instalaci.

V tomto článku se dozvíte, jak vyladit tyto modely a zvýšit jeho přesnost. 

## <a name="accuracy-of-image-classification-models"></a>Přesnost modelů klasifikace obrázků

Balíček pro zpracování obrazu počítače se zobrazí poskytnout dobré výsledky pro širokou škálu datových sad. Ale stejně jako u většiny machine learning projekty, získávání nejlepší možné výsledky pro nová datová sada vyžaduje pečlivé parametr ladění a vyhodnocení různých rozhodnutí. Následující části obsahují pokyny o tom, jak zlepšit přesnost na danou sadu dat, to znamená, jaké parametry jsou nejvíce slibně optimalizovat nejprve, jaké hodnoty těchto parametrů jednu akci a jakým nástrahám, aby se zabránilo.

Obecně řečeno modely obsáhlého learningu školení součástí kompromis mezi časem školení a přesnost modelu. Balíček pro zpracování obrazu počítač má předem nastavený výchozí parametry (první řádek v tabulce níže najdete v článku), která se zaměřují na rychlé školení rychlosti při obvykle vytváří přesnější modely. Tato přesnost může zlepšit často další, například pomocí vyšší rozlišení obrázku nebo hlubší modely, ale za cenu zvýšení školení čas faktorem 10 x nebo více.

Doporučuje se, že nejprve pracujete s výchozími parametry, trénování modelu, kontrolu výsledků, opravte základu poznámky pravdivých informací, podle potřeby a teprve pak zkuste parametry, které zpomalit školení čas (viz tabulka níže hodnoty navrhované parametrů). Doporučujeme však znalost těchto parametrů, přestože to technicky vzato není nezbytné.


### <a name="best-practices-and-tips"></a>Osvědčené postupy a tipy

* Kvalita dat: učení a testovací sady musí být ve vysoké kvalitě. To znamená, je anotována Image správně, odebrány nejednoznačný obrázky (například pokud nejasné pohledu uživatele Pokud obrázek znázorňuje tenis míč nebo Citronový) a atributy se vzájemně vylučují (to znamená, že každá image patří do právě jeden atribut).

* Před upřesnění DNN, by Trénink SVM třídění pomocí předem vytrénovaných a pevné DNN jako featurizer. To je podporována v balíčku pro zpracování obrazu počítači a nevyžaduje dlouhé tak moct trénovat, protože není upraven DNN samotný. Tento jednoduchý přístup často dosahuje dobré přesností a proto představuje silné směrného plánu. Dalším krokem je pak k upřesnění DNN, která by měla poskytnout větší přesnost.

* Pokud je objekt-vás zajímají malý obrázek, jsou známé přístupy klasifikace obrázků nebude pracovat dobře. V takovém případě zvažte metodiky zjišťování objektů, jako je balíček pro zpracování obrazu počítač rychleji R-CNN podle Tensorflow.

* Další trénovacích dat lepší. Jako pravidlo sady jezdce jeden by měl mít aspoň 100 příklady pro každou třídu, to znamená 100 bitových kopií pro "pes", 100 bitových kopií pro "cat" atd. Trénování modelu s menším počtem Image je možné, ale nemusí vracet správné výsledky.

* Trénovacích obrázků musí být místně na počítači s GPU a že na jednotce SSD (není pevný disk). V opačném případě latence čtení image může výrazně omezit školení rychlost (faktorem i 100 x).


### <a name="parameters-to-optimize"></a>Parametry pro optimalizaci

Vyhledání optimální hodnot těchto parametrů je důležité a můžete často zdokonalit výrazně přesnost:
* Rychlost učení (`lr_per_mb`): pravděpodobně nejdůležitější parametr, chcete-li získat správný. Pokud přesnost na školení nastavit po vylepšení DNN je vyšší než 5 %, pak pravděpodobně rychlost učení je buď příliš velká, nebo počet školení epoch příliš nízká. Zejména s malé datové sady obvykle DNN over-pass-the vejít na trénovací data, ale v praxi to povede k dobré modely testovacího nastavení. Obvykle používáme 15 epoch kde dvakrát; snížit rychlost učení v některých případech může výuku s využitím více epoch zlepšit výkon.

* Vstupní rozlišení (`image_dims`): výchozí rozlišení obrázku je 224 x 224 pixelů. Pomocí větší obrázek rozlišení, například 500 x 500 pixelů nebo 1000 × 1000 pixelů může výrazně zlepšit přesnost, ale může zpomalit DNN vylepšení. Balíček pro zpracování obrazu počítače očekává, že vstupní rozlišení bude řazené kolekce členů (barevného kanálu, obrázek šířku, výšku obrázku), například (3, 224, 224), kde počet barevného kanálu musí být nastavená na 3 (červená zelená modré pruhy).

* Modelování architektury (`base_model_name`): použijte místo výchozí model modelem ResNet-18 hlubší dopředné například modelem ResNet-34 nebo modelem ResNet-50. Model modelem Resnet-50 není pouze hlubší, ale výstup předposlední vrstvy je velikost 2048 float (vs. 512 float modelů modelem ResNet-18 a modelem ResNet-34). Tato vyšší dimenzionalitu může být zvláště užitečné při zachování DNN opraveno a místo toho školení SVM třídění.

* Velikost minibatch (`mb_size`): Vysoká minibatch velikosti povede k trénování rychlejší ale za cenu zvýšení paměťové nároky DNN. Proto při výběru hlubší modely (například modelem ResNet-50 srovnání s modelem ResNet-18) a/nebo vyšší rozlišení obrázku (500\*500 pixelů a 224\*224 pixelů), jedna má obvykle ke zmenšení velikosti minibatch, aby nedocházelo k chybám na více instancí z důvodu nedostatku paměti. Při změně velikosti minibatch, často také rychlost učení je potřeba upravit, jak je vidět v následující tabulce.
* Míra rozevírací out (`dropout_rate`) a L2 regularizer (`l2_reg_weight`): přizpůsobování DNN typu over-pass-the lze snížit pomocí Odpadlík sazba 0,5 (výchozí hodnota je 0,5 v balíčku pro zpracování obrazu počítače) nebo více a zvýšením váha regularizer (výchozí hodnota je 0.0005 v počítačové zpracování obrazu Balíček). Nezapomeňte, zejména s malé datové sady DNN typu over-pass-the přizpůsobování je složitá a často nemožné, aby se zabránilo.


### <a name="parameter-definitions"></a>Parametr definice

- **Rychlost učení**: krok velikost použitou při učení sestupu. Pokud sada příliš nízká. pak model bude mít mnoho epoch tak moct trénovat, když sada je příliš vysoká. pak model neprovede sblížení, k dobrým řešením. Plán se obvykle používá, pokud se po určitém počtu epoch snižuje rychlost učení. Ohodnotit E.For příklad studijní plán `[0.05]*7 + [0.005]*7 + [0.0005]` odpovídá pomocí kurzu učení 0,05 pro prvních sedm epoch, za nímž následuje 10 x rychlost učení sníženou 0,005 pro jiné sedm epoch a nakonec pro jeden epocha vyladění modelu 100 x snížit rychlost učení z 0.0005.

- **Velikost minibatch**: GPU může zpracovat více bitových kopií paralelně a urychlit tak výpočet. Tyto Image paralelní zpracování jsou také označovány jako minibatch. Vyšší velikost minibatch rychlejší školení bude, ale za cenu zvýšení paměťové nároky DNN.

### <a name="recommended-parameter-values"></a>Doporučené parametr hodnoty

Následující tabulka obsahuje sady různých parametrů, které byly k vytvářet modely s vysokou přesností na širokou škálu obrázek úlohy klasifikace zobrazeny. Optimální parametry závisí na konkrétní datové sady a na přesné GPU používá, proto měla by se zobrazit tabulku jako vodítko pouze. Po vyzkoušení tyto parametry, vezměte v úvahu také rozlišení obrázku více než 500 x 500 pixelů nebo hlubší modely, jako je třeba modelem Resnet-101 nebo modelem Resnet-152.

První řádek v tabulce odpovídá výchozí parametry, které jsou nastaveny uvnitř balíčku pro zpracování obrazu počítače. Všechny ostatní řádky trvat déle tak moct trénovat (uvedené v prvním sloupci) ale na výhody vyšší přesnost (viz druhý sloupec průměrné přesnost přes tři interní datové sady). Jako příklad může posloužit parametry v posledním řádku 5 – 15 x delší tak moct trénovat, ale za následek vyšší přesnost (průměrné) na tři interní testovací sady z 82.6 % 92.8 %.

Lepší modely a vyšší vstupního rozlišení zabrat více paměti DNN a proto je potřeba velikost minibatch sníží se zvýšenou modelu složitosti a vyhněte se výstup z paměti chyby. Jak je vidět v následující tabulce, je vhodné snížit rychlost učení faktorem dvou pokaždé, když se minibatch snížení velikosti ve stejném multiplikátor. Může být nutné získat snížit velikost minibatch další na GPU s menší množství paměti.

| Školení čas (hrubý odhad) | Příklad přesnost | Velikost minibatch (*mb_size*) | Rychlost učení (*lr_per_mb*) | Obrázek řešení (*image_dims*) | Architektura DNN (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 x (referenční dokumentace) | 82.6 % | 32 | [0,05] \*7 + [0,005]\*7 + [0.0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | 90.2 % | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | % 87,5 | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5 – 15 x        | 92.8 % |  8 | [0.01] \*7 + [0,001]\*7 + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Další postup

Informace o balíčku Azure Machine Learning pro počítačové zpracování obrazu:
+ Projděte si referenční dokumentaci

+ Další informace o [další balíčky Python pro Azure Machine Learning](reference-python-package-overview.md)
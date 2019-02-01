---
title: Ladění modelu
titleSuffix: Azure Machine Learning Studio
description: Jak ladit chyby, vytvořené moduly Train Model a Score Model v Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: a6708a1c414277cd4ee9b90851e19a94ea86c55b
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491667"
---
# <a name="debug-your-model-in-azure-machine-learning-studio"></a>Ladění modelu ve službě Azure Machine Learning Studio

Při spuštění modelu, můžete narazit následující chyby:

* [Train Model] [ train-model] modulu dojde k chybě 
* [Score Model] [ score-model] modulu produkuje nesprávné výsledky 

Tento článek vysvětluje možné příčiny pro tyto chyby.


## <a name="train-model-module-produces-an-error"></a>Modul trénování modelu dojde k chybě

![image1](./media/debug-models/train_model-1.png)

[Train Model] [ train-model] modul očekávají dva vstupy:

1. Typ modelu strojového učení z kolekce modelů poskytované Azure Machine Learning.
2. Trénovacích dat pomocí zadaného popisku sloupec, který určuje proměnnou k předpovědi (další sloupce, které jsou považovány za funkce).

Tento modul může vést k chybě v následujících případech:

1. Popisek sloupce je nesprávně zadán. To může nastat, pokud jako popisek byl vybrán více než jednoho sloupce nebo je vybrané nesprávné sloupce indexu. Druhém případě by například platí v případě, že index sloupce 30 se používá s vstupní datovou sadu, která má jen 25 sloupců.

2. Datová sada neobsahuje žádné sloupce. Například pokud vstupní datová sada obsahuje pouze jeden sloupec, který je označený jako sloupec popisek, by existovat žádné funkce, pomocí kterého se má sestavení modelu. V takovém případě [Train Model] [ train-model] modulu dojde k chybě.

3. Vstupní datové sady (funkce nebo popisku) obsahuje jako hodnotu nekonečno.

## <a name="score-model-module-produces-incorrect-results"></a>Modul určení skóre modelu produkuje nesprávné výsledky

![image2](./media/debug-models/train_test-2.png)

V typické experimentu trénování a testování pro učení [rozdělení dat] [ split] modulu rozděluje původní datové sady do dvou částí: jednu část se používá pro trénování modelu a jedné části slouží ke stanovení skóre jak také provádí trénovaného modelu. Trénovaného modelu je potom použít ke stanovení skóre testovacích dat, po jejímž uplynutí jsou výsledky vyhodnocován pro určení přesnost modelu.

[Score Model] [ score-model] modul vyžaduje dva vstupy:

1. Výstup trénovaného modelu z [Train Model] [ train-model] modulu.
2. Vyhodnocení sady dat se liší od datové sady pro trénování modelu.

Je možné, že i když podaří experiment, [Score Model] [ score-model] modulu produkuje nesprávné výsledky. Několik scénářů může způsobit, že tento problém, která se provede:

1. Pokud zadaným popiskem je zařazená do kategorií a regresní model se trénuje na data, by bylo vytvořeno nesprávný výstup, tak [Score Model] [ score-model] modulu. Je to proto, že regresní vyžaduje proměnnou průběžné odpovědi. V takovém případě by bylo vhodnější použít model klasifikace. 

2. Podobně pokud model klasifikace jsou trénovaná na datovou sadu s plovoucí desetinnou čárkou ve sloupci popisek, může způsobit nežádoucí výsledky. Je to proto, že klasifikace vyžaduje proměnnou diskrétní odpověď, která povoluje jenom hodnoty tohoto rozsahu konečná a malé, sadu tříd.

3. Pokud bodování datová sada neobsahuje všechny funkce využívají k tréninku modelu, [Score Model] [ score-model] dojde k chybě.

4. Pokud řádek v hodnoticí datová sada obsahuje chybějící hodnota nebo nekonečnou hodnotu pro některou z jejích funkcí [Score Model] [ score-model] nevytváří žádný výstup odpovídající na daném řádku.

5. [Score Model] [ score-model] mohou mít identické výstupy pro všechny řádky v hodnoticí datové sadě. Tato situace může nastat například při pokusu o klasifikaci pomocí rozhodnutí doménovými strukturami, pokud je minimální počet vzorků za uzel typu list je zvolená být vyšší než počet dostupných příkladů školení.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/


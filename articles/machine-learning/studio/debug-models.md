---
title: Ladění modelu - Azure Machine Learning Studio | Dokumentace Microsoftu
description: Jak ladit chyby, vytvořené moduly Train Model a Score Model v Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.openlocfilehash: 0464dec9e747e57e4b95a691aeb5a0992cf8d9cc
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268882"
---
# <a name="debug-your-model-in-azure-machine-learning-studio"></a>Ladění modelu ve službě Azure Machine Learning Studio

Tento článek vysvětluje potenciální důvody, proč některý z následujících dvou chyby můžou nastat při spuštění modelu:

* [Train Model] [ train-model] modulu dojde k chybě 
* [Score Model] [ score-model] modulu produkuje nesprávné výsledky 



## <a name="train-model-module-produces-an-error"></a>Modul trénování modelu dojde k chybě

![image1](./media/debug-models/train_model-1.png)

[Train Model] [ train-model] modul očekávají dva vstupy:

1. Typ modelu strojového učení z kolekce modelů poskytované Azure Machine Learning.
2. Trénovacích dat s zadaný sloupec popisek, který určuje proměnnou k předpovědi (další sloupce, které jsou považovány za funkce).

Tento modul může vést k chybě v následujících případech:

1. Popisek sloupce je nesprávně zadán. To může nastat, pokud jako popisek byl vybrán více než jednoho sloupce nebo je vybrané nesprávné sloupce indexu. Druhém případě by například platí v případě, že index sloupce 30 se používá s vstupní datovou sadu, která má jen 25 sloupce.

2. Datová sada neobsahuje žádné sloupce. Například pokud vstupní datová sada obsahuje pouze jeden sloupec, který je označený jako sloupec popisek, by existovat žádné funkce, pomocí kterého se má sestavení modelu. V takovém případě [Train Model] [ train-model] modulu dojde k chybě.

3. Vstupní datové sady (funkce nebo popisku) obsahuje jako hodnotu nekonečno.

## <a name="score-model-module-produces-incorrect-results"></a>Modul určení skóre modelu produkuje nesprávné výsledky

![image2](./media/debug-models/train_test-2.png)

V typické experimentu trénování a testování pro učení [rozdělení dat] [ split] modulu rozděluje původní datové sady do dvou částí: jednu část se používá pro trénování modelu a jedné části slouží ke stanovení skóre jak také provádí trénovaného modelu. Trénovaného modelu je potom použít ke stanovení skóre testovacích dat, po jejímž uplynutí jsou výsledky vyhodnocován pro určení přesnost modelu.

[Score Model] [ score-model] modul vyžaduje dva vstupy:

1. Výstup trénovaného modelu z [Train Model] [ train-model] modulu.
2. Vyhodnocení sady dat se liší od datové sady pro trénování modelu.

Je možné, že i když podaří experiment, [Score Model] [ score-model] modulu produkuje nesprávné výsledky. Několik scénářů může způsobit, že k tomu dojde:

1. Pokud zadaným popiskem je zařazená do kategorií a regresní model se trénuje na data, by bylo vytvořeno nesprávný výstup, tak [Score Model] [ score-model] modulu. Je to proto, že regresní vyžaduje proměnnou průběžné odpovědi. V takovém případě by bylo vhodnější použít model klasifikace. 

2. Podobně pokud model klasifikace jsou trénovaná na datovou sadu s plovoucí desetinnou čárkou ve sloupci popisek, může způsobit nežádoucí výsledky. Je to proto, že klasifikace vyžaduje proměnnou diskrétní odpověď, která povoluje jenom hodnoty tohoto rozsahu v rámci omezené a obvykle poměrně malé sady tříd.

3. Pokud bodování datová sada neobsahuje všechny funkce využívají k tréninku modelu, [Score Model] [ score-model] dojde k chybě.

4. Pokud řádek v hodnoticí datová sada obsahuje chybějící hodnota nebo nekonečnou hodnotu pro některou z jejích funkcí [Score Model] [ score-model] nevytvoří žádný výstup odpovídající na daném řádku.

5. [Score Model] [ score-model] mohou mít identické výstupy pro všechny řádky v hodnoticí datové sadě. Tato situace může nastat například při pokusu o klasifikaci pomocí rozhodnutí doménovými strukturami, pokud je minimální počet vzorků za uzel typu list je zvolená být vyšší než počet dostupných příkladů školení.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/


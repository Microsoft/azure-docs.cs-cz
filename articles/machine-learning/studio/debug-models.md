---
title: Ladění modelu v Azure Machine Learning | Microsoft Docs
description: Postup ladění chyby, vytvořené na moduly Train Model a Score Model v Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.openlocfilehash: bca5ebbda2d46270cc0a996df5fc46f201931343
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="debug-your-model-in-azure-machine-learning"></a>Ladění modelu ve službě Azure Machine Learning

Tento článek vysvětluje potenciální z důvodů, proč některý z následujících dvou selhání může být došlo při spuštění modelu:

* [Train Model] [ train-model] modulu vytvoří chybu 
* [Score Model] [ score-model] modulu produkuje nesprávné výsledky 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>Modulu Train Model vytvoří chybu

![image1](./media/debug-models/train_model-1.png)

[Train Model] [ train-model] modulu očekává dva vstupy:

1. Typ model strojového učení z kolekce modelů, které poskytuje Azure Machine Learning.
2. Školení dat pomocí zadaného sloupce popisek, který určuje proměnnou k předvídání (ostatních sloupců se předpokládá, že funkce).

Tento modul může vést k chybě v následujících případech:

1. Popisek sloupce je nesprávně zadán. To může dojít, pokud je vybrána více než jeden sloupec jako popisek nebo je vybrán nesprávný sloupec indexu. Druhém případě byste například použít, pokud index sloupce 30 používá s vstupní datové sady, která má pouze 25 sloupce.

2. Datová sada neobsahuje žádné sloupce funkce. Například pokud vstupní datové sady obsahuje jen jeden sloupec, který je označen jako popisek sloupce, by žádné funkce pro sestavení modelu. V takovém případě [Train Model] [ train-model] modulu vytvoří chybu.

3. Vstupní datové sady (funkce nebo popis) obsahuje Infinity jako hodnotu.

## <a name="score-model-module-produces-incorrect-results"></a>Modul určení skóre modelu produkuje nesprávné výsledky

![image2](./media/debug-models/train_test-2.png)

V typické experimentu školení/testování pro učení se supervizí [rozdělení dat] [ split] modulu rozděluje původní datové sady do dvou částí: jednou ze součástí se používá pro trénování modelu a jednou ze součástí slouží ke stanovení skóre jak dobře provede naučeného modelu. Pro cvičný model se pak použije skóre pro testovací data, po jejímž uplynutí se vyhodnocují výsledky k určení přesnosti modelu.

[Score Model] [ score-model] modulu vyžaduje dva vstupy:

1. Výstup trained model z [Train Model] [ train-model] modulu.
2. Vyhodnocování datovou sadu, která se liší od datovou sadu použity při cvičení modelu.

Je možné, že i když úspěšné experiment, [Score Model] [ score-model] modulu produkuje nesprávné výsledky. Může způsobit několik scénářů, k tomu dojít:

1. Pokud je kategorií zadaným popiskem a regresní model je trénink na data, nesprávné výstup podle [Score Model] [ score-model] modulu. To je proto regrese vyžaduje proměnnou průběžné odpovědi. V takovém případě by bylo vhodnější použít model klasifikace. 

2. Podobně pokud na datovou sadu s plovoucí desetinnou čárkou ve sloupci popisek cvičení modelu klasifikace, může způsobit nežádoucí výsledky. To je proto klasifikace vyžaduje diskrétní odpovědi proměnné, která umožňuje pouze hodnoty rozsahu v rámci omezené a obvykle poněkud malé sady tříd.

3. Pokud vyhodnocování datová sada neobsahuje všechny funkce, které jsou použity při cvičení modelu [Score Model] [ score-model] vytvoří chybu.

4. Pokud v řádku v vyhodnocování datová sada obsahuje hodnotu chybí, nebo hodnotu nekonečné jednotlivých funkcí, [Score Model] [ score-model] nebude vytvoření jakéhokoli výstupu odpovídající na příslušném řádku.

5. [Score Model] [ score-model] může vytvořit identické výstupy pro všechny řádky v vyhodnocování datové sadě. Tato situace může nastat, například při pokusu o zařazení pomocí rozhodnutí doménové struktury, pokud je minimální počet vzorků na uzel typu list zvolena být vyšší než počet školení příklady, které jsou k dispozici.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/


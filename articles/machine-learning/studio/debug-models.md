---
title: Ladění modelu
titleSuffix: ML Studio (classic) - Azure
description: Jak ladit chyby vytvořené moduly Train Model a Score Model v Azure Machine Learning Studio (classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 910e788830ec55b610a9234a8c8ac75dda1ea189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218102"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Ladění modelu v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Při spuštění modelu můžete narazit na následující chyby:

* V modulu [Model vlaku][train-model] se vytvoří chyba 
* Modul [Model skóre][score-model] vytváří nesprávné výsledky 

Tento článek vysvětluje možné příčiny těchto chyb.


## <a name="train-model-module-produces-an-error"></a>Modul modelu vlaku vytvoří chybu

![image1](./media/debug-models/train_model-1.png)

Modul [modelu vlaku][train-model] očekává dva vstupy:

1. Typ modelu strojového učení z kolekce modelů poskytovaných Azure Machine Learning Studio (klasické).
2. Trénovací data se zadaným sloupcem Label, který určuje proměnnou k předvídání (ostatní sloupce jsou považovány za funkce).

Tento modul může způsobit chybu v následujících případech:

1. Sloupec Popisek je zadán nesprávně. K tomu může dojít, pokud je vybrán o více než jeden sloupec jako Popisek nebo je vybrán nesprávný index sloupce. Například druhý případ by použít, pokud index sloupce 30 se používá se vstupní datovou sadou, která má pouze 25 sloupců.

2. Datová sada neobsahuje žádné sloupce Funkce. Například pokud vstupní datová sada má pouze jeden sloupec, který je označen jako Label sloupec, by žádné funkce, s nimiž by se vytvořit model. V tomto případě train [model][train-model] modulu vytvoří chybu.

3. Vstupní datová sada (Funkce nebo Popisek) obsahuje infinity jako hodnotu.

## <a name="score-model-module-produces-incorrect-results"></a>Modul modelu skóre vytváří nesprávné výsledky

![image2](./media/debug-models/train_test-2.png)

V typickéškolení/testování experiment pro učení pod [dohledem, split data][split] modul rozdělí původní datové sady do dvou částí: jedna část se používá k trénování modelu a jedna část se používá ke skóre, jak dobře trénovaný model provádí. Trénovaný model se pak používá ke skóre testovacích dat, po kterém jsou výsledky vyhodnoceny k určení přesnosti modelu.

Modul [Model skóre][score-model] vyžaduje dva vstupy:

1. Výstup trénovaného modelu z modulu [Train Model.][train-model]
2. Datová sada hodnocení, která se liší od datové sady použité k trénování modelu.

Je možné, že i když experiment uspěje, modul [Model skóre][score-model] vytvoří nesprávné výsledky. Několik scénářů může způsobit tento problém:

1. Pokud je zadaný label kategorický a regresní model je trénovaný na datech, nesprávný výstup by byl vytvořen modulem [Model skóre.][score-model] Důvodem je, že regrese vyžaduje proměnnou nepřetržité odezvy. V tomto případě by bylo vhodnější použít klasifikační model. 

2. Podobně pokud je klasifikační model trénován na datové sadě s čísly s plovoucí desetinnou tázkou ve sloupci Popisek, může vést k nežádoucím výsledkům. Je to proto, že klasifikace vyžaduje diskrétní proměnnou odezvy, která umožňuje pouze hodnoty, které se pohybují přes omezenou a malou sadu tříd.

3. Pokud datová sada hodnocení neobsahuje všechny funkce použité k trénování modelu, [model skóre][score-model] vytvoří chybu.

4. Pokud řádek v datové sadě hodnocení obsahuje chybějící hodnotu nebo nekonečnou hodnotu pro některý z jeho funkcí, [model skóre][score-model] nevytváří žádný výstup odpovídající tomuto řádku.

5. [Model skóre][score-model] může produkovat identické výstupy pro všechny řádky v datové sadě hodnocení. K tomu může dojít například při pokusu o klasifikaci pomocí rozhodovací chlesů, pokud je minimální počet vzorků na uzel listu vybrán jako větší než počet dostupných příkladů školení.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/


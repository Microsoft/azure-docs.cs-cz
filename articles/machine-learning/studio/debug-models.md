---
title: Ladění modelu
titleSuffix: ML Studio (classic) Azure
description: Jak ladit chyby vytvářené modelem výukového modelu a modelem skóre v Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 0ab7f041da63731706742547a53df47462bc584a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619444"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Ladění modelu v Azure Machine Learning Studio (Classic)

Při spuštění modelu můžete spustit následující chyby:

* modul [vlakového modelu][train-model] vytvoří chybu. 
* modul [skóre modelu][score-model] vytváří nesprávné výsledky. 

Tento článek vysvětluje možné příčiny těchto chyb.


## <a name="train-model-module-produces-an-error"></a>Modul vlakového modelu vytvoří chybu.

![image1](./media/debug-models/train_model-1.png)

Modul [vlakového modelu][train-model] očekává dva vstupy:

1. Typ modelu strojového učení z kolekce modelů, které poskytuje Azure Machine Learning Studio (Classic).
2. Školicí data se zadaným sloupcem popisku, který určuje proměnnou pro předpověď (u ostatních sloupců se předpokládá, že jsou funkce).

Tento modul může způsobit chybu v následujících případech:

1. Sloupec popisku je nesprávně zadán. K tomu může dojít, pokud je vybráno více než jeden sloupec jako popisek nebo je vybrán nesprávný index sloupce. Druhý případ by například měl platit, pokud se používá index sloupce 30 se vstupní datovou sadou, která má pouze 25 sloupců.

2. Datová sada neobsahuje žádné sloupce funkce. Například pokud má vstupní datová sada jenom jeden sloupec, který je označený jako sloupec popisku, neexistují žádné funkce, pomocí kterých by bylo možné model sestavit. V takovém případě modul [vlakového modelu][train-model] vytvoří chybu.

3. Vstupní datová sada (funkce nebo popisek) obsahuje nekonečno jako hodnotu.

## <a name="score-model-module-produces-incorrect-results"></a>Modul určení skóre modelu vytváří nesprávné výsledky.

![image2](./media/debug-models/train_test-2.png)

V typických experimentech pro školení a testování se v režimu pod dohledem naučí modul [rozdělení dat][split] rozdělit původní datovou sadu na dvě části: jedna část se používá ke výukě modelu a jedna část se používá ke zjištění, jak dobře je vyškolený model vykonává. Vyškolený model se pak použije k vyhodnocení testovacích dat, po kterém jsou výsledky vyhodnocovány k určení přesnosti modelu.

Modul [skóre modelu][score-model] vyžaduje dva vstupy:

1. Vyškolený výstup modelu z modulu [vlakového modelu][train-model] .
2. Datová sada bodování, která se liší od datové sady použité pro výuku modelu.

Je možné, že i když je experiment úspěšný, modul [skóre modelu][score-model] vytvoří nesprávné výsledky. K tomuto problému může dojít z několika scénářů:

1. Pokud je zadaný popisek kategorií a je-li na datech vyškolený model regrese, bude modul určení [skóre][score-model] vytvořen nesprávným výstupem. Je to proto, že regrese vyžaduje proměnnou průběžné odezvy. V takovém případě by bylo vhodnější použít klasifikační model. 

2. Podobně pokud je model klasifikace vyškolený pro datovou sadu, která má čísla s plovoucí desetinnou čárkou ve sloupci Label, může to způsobit nežádoucí výsledky. Důvodem je, že klasifikace vyžaduje samostatnou proměnnou odpovědi, která umožňuje pouze hodnoty, které mají rozsah přes konečnou a malou sadu tříd.

3. Pokud datová sada bodování neobsahuje všechny funkce používané pro výuku modelu, [model skóre][score-model] vytvoří chybu.

4. Pokud řádek v datové sadě bodování obsahuje chybějící hodnotu nebo nekonečnou hodnotu pro žádnou z jeho funkcí, [model skóre][score-model] nevytvoří žádný výstup odpovídající tomuto řádku.

5. [Model skóre][score-model] může pro všechny řádky v datové sadě bodování vydávat stejné výstupy. K tomu může dojít například při pokusu o klasifikaci pomocí rozhodovacích doménových struktur, pokud je zvolený minimální počet vzorků na uzel na úrovni listu větší než počet dostupných příkladů školení.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/


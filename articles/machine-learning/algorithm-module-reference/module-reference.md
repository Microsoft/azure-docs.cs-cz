---
title: Referenční informace k modulům a algoritmům
titleSuffix: Azure Machine Learning service
description: Další informace o modulech, které jsou k dispozici v Azure Machine Learningm vizuálním rozhraní
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2d81c407c17c50c34afd15a99d2f8ac2f8c5361e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692842"
---
# <a name="algorithm--module-reference-overview"></a>Přehled referencí modulu & algoritmu

Tento referenční obsah poskytuje technické informace o každém algoritmech strojového učení a modulech, které jsou k dispozici ve vizuálním rozhraní (Preview) služby Azure Machine Learning.

Každý modul představuje sadu kódu, který může běžet nezávisle a provede úlohu strojového učení s ohledem na požadované vstupy. Modul může obsahovat konkrétní algoritmus nebo provést úkol, který je důležitý ve strojovém učení, jako je například chybějící hodnota, nebo statistická analýza.

> [!TIP]
> V jakémkoli kanálu v rámci vizuálního rozhraní můžete získat informace o konkrétním modulu. Vyberte modul a potom v podokně **rychlá ochrana** vyberte odkaz **Další** .

## <a name="modules"></a>Moduly

Moduly jsou uspořádány podle funkcí:

| Funkce | Popis | Modul |
| --- |--- | ---- |
| Převody formátů dat | Převod dat mezi různými formáty souborů používaných ve strojovém učení, | [Převést na sdílený svazek clusteru](convert-to-csv.md) |
| Vstup a výstup dat | Přesuňte data z cloudových zdrojů do experimentu. Zápis výsledků nebo mezilehlých dat do Azure Storage, databáze SQL nebo podregistru, při spuštění experimentu nebo použití cloudového úložiště k výměně dat mezi experimenty.  | [Importovat data](import-data.md)<br/>[Exportovat data](export-data.md)<br/>[Zadat data ručně](enter-data-manually.md) |
| Transformace dat | Operace s daty, která jsou jedinečná pro strojové učení, jako je například normalizace nebo binningu dat, výběr funkcí a snížení rozměrů.| [Výběr sloupců v datové sadě](select-columns-in-dataset.md) <br/> [Upravit metadata](edit-metadata.md) <br/> [Vyčistit chybějící data](clean-missing-data.md) <br/>  [Hodnoty hash funkcí](feature-hashing.md) <br/>  [Extrahovat N gram funkcí z textu](extract-n-gram-features-from-text.md) <br/> [Přidat sloupce](add-columns.md) <br/> [Přidat řádky](add-rows.md) <br/> [Odebrat duplicitní řádky](remove-duplicate-rows.md) <br/> [Text předběžného zpracování](preprocess-text.md) <br/> [Spojit data](join-data.md) <br/> [Rozdělit data](split-data.md) <br/> [Normalizovat data](normalize-data.md) <br/> [Oddíl a ukázka](partition-and-sample.md) <br/> [Oříznout hodnoty](clip-values.md) <br/> [Použít transformaci SQL](apply-sql-transformation.md)|
| Moduly Pythonu a R | Napište kód a vložte ho do modulu pro integraci Pythonu a R s vaším experimentem. | [Spustit skript jazyka Python](execute-python-script.md)   <br/> [Vytvoření modelu Pythonu](create-python-model.md) <br/> [Spustit skript jazyka R](execute-r-script.md)
|  | **Algoritmy strojového učení**: | |
| Classification | Předpověď třídy.  Vyberte si binární (dvě třídy) nebo algoritmy s více třídami.| [Více tříd – rozhodovací doménová struktura](multiclass-decision-forest.md) <br/> [Více tříd pro rozhodovací strom s více třídami](multiclass-boosted-decision-tree.md) <br/> [Mikrotřída logistické regrese](multiclass-logistic-regression.md)  <br/> [Neuronové síť pro více tříd](multiclass-neural-network.md)  <br/>  [Logistická regrese dvou tříd](two-class-logistic-regression.md)  <br/>[Průměrná hodnota Perceptron se dvěma třídami](two-class-averaged-perceptron.md) <br/> [@No__t_2Decision &nbsp;Boosted se dvěma třídami &nbsp;Tree](two-class-boosted-decision-tree.md)  <br/> [Rozhodovací doménová struktura se dvěma třídami](two-class-decision-forest.md)  <br/> [Neuronové síť se dvěma třídami](two-class-neural-network.md)  <br/> [Dvě&#8209;třídy &nbsp;Support &nbsp;Vector &nbsp;Machine](two-class-support-vector-machine.md) 
| Clustering | Seskupte data dohromady.| [K-znamená clustering](k-means-clustering.md)
| Nevýhody | Předpověď hodnoty. | [Lineární regrese](linear-regression.md)  <br/> [Regrese sítě neuronové](neural-network-regression.md)  <br/> [Regrese rozhodovací doménové struktury](decision-forest-regression.md)  <br/> [Zvýšení &nbsp;Decision &nbsp;Tree &nbsp;Regression](boosted-decision-tree-regression.md)
|  | **Sestavování a vyhodnocování modelů**: | |
| Trénování   | Spusťte data pomocí algoritmu. | [Model výuky](train-model.md)  <br/> [Analýza modelu clusteringu](train-clustering-model.md)    |
| Vyhodnocení modelu | Změřte přesnost vyučeného modelu. |  [Vyhodnotit model](evaluate-model.md)
| Hodnocení | Získejte předpovědi z modelu, který jste právě pronaučili. | [Použít transformaci](apply-transformation.md)<br/>[Přiřadit &nbsp;Data &nbsp;to &nbsp;Clusters](assign-data-to-clusters.md) <br/>[Model skóre](score-model.md)
| Statistické funkce | Poskytněte širokou škálu číselných metod, které se týkají vědeckého zpracování dat. | [Použít matematickou operaci](apply-math-operation.md) <br/> [Shrnout data](summarize-data.md)|

## <a name="error-messages"></a>Chybové zprávy

Seznamte se s [chybovými zprávami a kódy výjimek](machine-learning-module-error-codes.md) , se kterými se můžete setkat pomocí modulů v rozhraní jazyka Azure Machine Learning služby.

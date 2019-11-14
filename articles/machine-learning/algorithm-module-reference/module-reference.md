---
title: Referenční informace k modulům a algoritmům
description: Seznamte se s moduly dostupnými v Návrháři Azure Machine Learning (Preview).
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 11/11/2019
ms.openlocfilehash: 21577936f2487313683ac763b0c6c661c6c1f958
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039324"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Reference k modulu & algoritmu pro návrháře Azure Machine Learning

Tento referenční obsah poskytuje technické informace o každém algoritmu a modulech strojového učení, které jsou dostupné v Azure Machine Learning designeru (Preview).

Každý modul představuje sadu kódu, který může běžet nezávisle a provede úlohu strojového učení s ohledem na požadované vstupy. Modul může obsahovat konkrétní algoritmus nebo provést úkol, který je důležitý ve strojovém učení, jako je například chybějící hodnota, nebo statistická analýza.

> [!TIP]
> V jakémkoli kanálu v Návrháři můžete získat informace o konkrétním modulu. Vyberte modul a potom v podokně **rychlá ochrana** vyberte odkaz **Další** .

## <a name="modules"></a>Moduly

Moduly jsou uspořádány podle funkcí:

| Funkce | Popis | Modul |
| --- |--- | ---- |
| Vstup a výstup dat | Přesuňte data z cloudových zdrojů do vašeho kanálu. Zápis výsledků nebo mezilehlých dat do Azure Storage, databáze SQL nebo podregistru, při spuštění kanálu nebo použití cloudového úložiště k výměně dat mezi kanály.  | [Importovat data](import-data.md) <br/> [Zadat data ručně](enter-data-manually.md) <br/>[Exportovat data](export-data.md) |
| Transformace dat | Operace s daty, která jsou jedinečná pro strojové učení, jako je normalizace nebo binningu dat, snižování dimenzionálního omezení a převod dat mezi různými formáty souborů.| [Přidat sloupce](add-columns.md) <br/> [Přidat řádky](add-rows.md) <br/> [Vyčistit chybějící data](clean-missing-data.md) <br/> [Převést na sdílený svazek clusteru](convert-to-csv.md) <br/> [Převést na datovou sadu](convert-to-dataset.md) <br/> [Upravit metadata](edit-metadata.md) <br/> [Spojit data](join-data.md) <br/> [Normalizovat data](normalize-data.md) <br/> [Odebrat duplicitní řádky](remove-duplicate-rows.md) <br/> [Transformovat sloupce pro výběr](select-columns-transform.md) <br/> [Výběr sloupců v datové sadě](select-columns-in-dataset.md) |
| Vzorkování | Rozdělte data do jedné nebo více podmnožin podmnožin a Naučte se testovat modely strojového učení.  | [Model vzájemného ověřování](cross-validate-model.md) <br/> [Oddíl a ukázka](partition-and-sample.md) <br/> [SMOTE](smote.md) <br/> [Rozdělit data](split-data.md) |
| Výběr funkcí | Vyberte podmnožinu relevantních užitečných funkcí, které se použijí při sestavování analytického modelu. | [Výběr funkcí založených na filtrech](filter-based-feature-selection.md) <br/> [Funkce permutace – důležitost](permutation-feature-importance.md) |
| Python a R | Napište kód a vložte ho do modulu pro integraci Pythonu a R s vaším kanálem. | [Vytvoření modelu Pythonu](create-python-model.md) <br/> [Spustit skript jazyka Python](execute-python-script.md)   <br/>  [Spustit skript jazyka R](execute-r-script.md)
| Analýza textu | Poskytněte specializované výpočetní nástroje pro práci s strukturovaným i nestrukturovaným textem. | [Extrahovat N gram funkcí z textu](extract-n-gram-features-from-text.md) <br/> [Hodnoty hash funkcí](feature-hashing.md) <br/> [Text předběžného zpracování](preprocess-text.md) |
|  | **Algoritmy strojového učení**: | |
| Classification | Předpověď třídy.  Vyberte si binární (dvě třídy) nebo algoritmy s více třídami.| [Více tříd – rozhodovací doménová struktura](multiclass-decision-forest.md) <br/> [Více tříd pro rozhodovací strom s více třídami](multiclass-boosted-decision-tree.md) <br/> [Mikrotřída logistické regrese](multiclass-logistic-regression.md)  <br/> [Neuronové síť pro více tříd](multiclass-neural-network.md) <br/> [Jedna oproti všem více třídám](one-vs-all-multiclass.md) <br/>  [Logistická regrese dvou tříd](two-class-logistic-regression.md)  <br/>[Průměrná hodnota Perceptron se dvěma třídami](two-class-averaged-perceptron.md) <br/> [Zvýšení rozhodovacího stromu se dvěma třídami](two-class-boosted-decision-tree.md)  <br/> [Rozhodovací doménová struktura se dvěma třídami](two-class-decision-forest.md)  <br/> [Neuronové síť se dvěma třídami](two-class-neural-network.md) <br/> [Podpora dvou tříd – vektorový počítač](two-class-support-vector-machine.md) | 
| Clustering | Seskupte data dohromady.| [K-znamená clustering](k-means-clustering.md)
| Regrese | Předpověď hodnoty. | [Zvýšila se regrese rozhodovacího stromu.](boosted-decision-tree-regression.md) <br/> [Regrese rozhodovací doménové struktury](decision-forest-regression.md) <br/> [Lineární regrese](linear-regression.md)  <br/> [Regrese sítě neuronové](neural-network-regression.md)  <br/> |
| Doporučené | Modely doporučení pro sestavení. | [Vyhodnotit doporučeného](evaluate-recommender.md) <br/> [Doporučení pro skóre SVD](score-svd-recommender.md) <br/> [Doporučení pro výuku SVD](train-SVD-recommender.md) |
|  | **Sestavování a vyhodnocování modelů**: | |
| Trénování   | Spusťte data pomocí algoritmu. | [Model výuky](train-model.md)  <br/> [Analýza modelu clusteringu](train-clustering-model.md) <br/>  [Ladit parametry modelu](tune-model-hyperparameters.md) |
| Vyhodnocení modelu | Změřte přesnost vyučeného modelu. |  [Vyhodnotit model](evaluate-model.md) |
| Skóre | Získejte předpovědi z modelu, který jste právě pronaučili. | [Použít transformaci](apply-transformation.md)<br/>[Přiřazení dat clusterům](assign-data-to-clusters.md) <br/>[Model skóre](score-model.md) |
| Statistické funkce | Poskytněte širokou škálu statistických metod, které se týkají datové vědy. | [Použít matematickou operaci](apply-math-operation.md) <br/> [Shrnout data](summarize-data.md)|

## <a name="error-messages"></a>Chybové zprávy

Seznamte se s [chybovými zprávami a kódy výjimek](machine-learning-module-error-codes.md) , se kterými se můžete setkat pomocí modulů v Návrháři Azure Machine Learning.

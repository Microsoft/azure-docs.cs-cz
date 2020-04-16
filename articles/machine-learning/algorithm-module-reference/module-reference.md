---
title: Referenční informace k modulům a algoritmům
description: Další informace o modulech dostupných v návrháři Azure Machine Learning (preview)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: 7fcfc7c15f94d7b569629e53534d731126172260
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399066"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Odkaz na modul & algoritmu pro návrháře Azure Machine Learning (preview)

Tento referenční obsah poskytuje technické informace o jednotlivých algoritmech a modulech strojového učení, které jsou dostupné v návrháři Azure Machine Learning (preview).

Každý modul představuje sadu kódu, který lze spustit nezávisle a provádět úlohu strojového učení, vzhledem k požadované vstupy. Modul může obsahovat určitý algoritmus nebo provést úlohu, která je důležitá ve strojovém učení, jako je například chybějící nahrazení hodnoty nebo statistická analýza.

Nápovědu k výběru algoritmů naleznete v tématu 
* [Postup při výběru algoritmů](../how-to-select-algorithms.md)
* [Cheatovací list algoritmu s algoritmem strojového učení Azure](../algorithm-cheat-sheet.md)

> [!TIP]
> V libovolném kanálu v návrháři můžete získat informace o konkrétním modulu. Vyberte modul a v podokně **Rychlá nápověda** vyberte další odkaz **nápovědy.**

## <a name="data-preparation-modules"></a>Moduly pro přípravu dat


| Funkce | Popis | Modul |
| --- |--- | --- |
| Vstup a výstup dat | Přesuňte data z cloudových zdrojů do kanálu. Zapište výsledky nebo zprostředkující data do Azure Storage, databáze SQL nebo Hive, při spuštění kanálu, nebo použijte cloudové úložiště k výměně dat mezi kanály.  | [Ruční zadávání dat](enter-data-manually.md) <br/> [Exportovat data](export-data.md) <br/> [Import dat](import-data.md) |
| Transformace dat | Operace s daty, která jsou jedinečná pro strojové učení, jako je normalizace nebo binning dat, snížení dimenzionality a převod dat mezi různými formáty souborů.| [Přidání sloupců](add-columns.md) <br/> [Přidání řádků](add-rows.md) <br/> [Použití matematických operací](apply-math-operation.md) <br/> [Použití transformace SQL](apply-sql-transformation.md) <br/> [Vyčištění chybějících dat](clean-missing-data.md) <br/> [Oříznutí hodnot](clip-values.md) <br/> [Převod na C](convert-to-csv.md) <br/> [Převod na datovou sadu](convert-to-dataset.md) <br/> [Převod na hodnoty indikátoru](convert-to-indicator-values.md) <br/> [Úpravy metadat](edit-metadata.md) <br/> [Spojení dat](join-data.md) <br/> [Normalizace dat](normalize-data.md) <br/> [Rozdělení a ukázky](partition-and-sample.md)  <br/> [Odebrání duplicitních řádků](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Transformace pro výběr sloupců](select-columns-transform.md) <br/> [Výběr sloupců v datové sadě](select-columns-in-dataset.md) <br/> [Rozdělení dat](split-data.md) |
| Výběr funkcí | Vyberte podmnožinu relevantních užitečných funkcí, které se použijí při vytváření analytického modelu. | [Výběr funkcí na základě filtrů](filter-based-feature-selection.md) <br/> [Důležitost funkce permutací](permutation-feature-importance.md) |
| Statistické funkce | Poskytují širokou škálu statistických metod souvisejících s datovou vědou. | [Sumarizace dat](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmy strojového učení

| Funkce | Popis | Modul |
| --- |--- | --- |
| Regrese | Předpovědět hodnotu. | [Posílená regrese rozhodovacího stromu](boosted-decision-tree-regression.md) <br/> [Regrese rozhodovacího lesa](decision-forest-regression.md) <br/> [Lineární regrese](linear-regression.md)  <br/> [Regrese neurální sítě](neural-network-regression.md)  <br/> |
| Clustering | Seskupte data dohromady.| [Clustering K-Means](k-means-clustering.md)
| Classification | Předpovědět třídu.  Vyberte si z binárních (dvoutřídových) nebo vícetřídových algoritmů.| [Posílený rozhodovací strom s několika třídami](multiclass-boosted-decision-tree.md) <br/> [Rozhodovací les s více třídami](multiclass-decision-forest.md) <br/> [Logistická regrese s více třídami](multiclass-logistic-regression.md)  <br/> [Neurální síť s více třídami](multiclass-neural-network.md) <br/> [Jeden vs. Všechny multiclass](one-vs-all-multiclass.md) <br/> [Zprůměrovaný perceptron se dvěma třídami](two-class-averaged-perceptron.md) <br/>  [Posílený rozhodovací strom se dvěma třídami](two-class-boosted-decision-tree.md)  <br/> [Rozhodovací les se dvěma třídami](two-class-decision-forest.md) <br/>  [Logistická regrese se dvěma třídami](two-class-logistic-regression.md) <br/> [Neurální síť se dvěma třídami](two-class-neural-network.md) <br/> [Support Vector Machine (SVM) se dvěma třídami](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduly pro vytváření a vyhodnocování modelů

| Funkce | Popis | Modul |
| --- |--- | --- |
| Trénování modelu | Spusťte data přes algoritmus. |  [Trénování modelu clusteringu](train-clustering-model.md) <br/> [Model vlaku](train-model.md)  <br/> [Ladění hyperparametrů modelů](tune-model-hyperparameters.md) |
| Hodnocení a hodnocení modelů | Změřte přesnost trénovaného modelu. | [Použití transformace](apply-transformation.md) <br/> [Přiřazení dat do clusterů](assign-data-to-clusters.md) <br/> [Křížové ověření modelu](cross-validate-model.md) <br/> [Vyhodnotit model](evaluate-model.md) <br/> [Model skóre](score-model.md) |
| Jazyk Python | Napište kód a vložte jej do modulu pro integraci Pythonu s vaším kanálem. | [Vytvoření modelu Pythonu](create-python-model.md) <br/> [Spouštění skriptů Pythonu](execute-python-script.md) |
| Jazyk R | Napište kód a vložte jej do modulu pro integraci R s vaším kanálem. | [Spouštění skriptů R](execute-r-script.md) |
| Analýza textu | Poskytněte specializované výpočetní nástroje pro práci se strukturovaným i nestrukturovaným textem. | [Extrakce funkcí n-gramů z textu](extract-n-gram-features-from-text.md) <br/> [Hashování funkcí](feature-hashing.md) <br/> [Předzpracování textu](preprocess-text.md) <br/> [Latentní Dirichlet ovace](latent-dirichlet-allocation.md) |
| Doporučení | Sestavení modelů doporučení. | [Vyhodnocení doporučovacího systému](evaluate-recommender.md) <br/> [Určení skóre doporučovacího systému SVD](score-svd-recommender.md) <br/> [Trénování doporučovacího systému SVD](train-SVD-recommender.md) |
| Detekce anomálií | Vytvářejte modely detekce anomálií. | [Detekce anomálií založených na PCA](pca-based-anomaly-detection.md) <br/> [Model detekce anomálií vlaku](train-anomaly-detection-model.md) |


## <a name="error-messages"></a>Chybové zprávy

Přečtěte si o [chybových zprávách a kódech výjimek,](designer-error-codes.md) se kterými se můžete setkat pomocí modulů v návrháři Azure Machine Learning.

## <a name="next-steps"></a>Další kroky

* [Kurz: Sestavte model v návrháři předpovědět ceny automobilů](../tutorial-designer-automobile-price-train-score.md)

---
title: Referenční informace k modulům a algoritmům
description: Seznamte se s moduly dostupnými v Návrháři Azure Machine Learning (Preview).
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 1996f607d05cb84a80acbb105504ce0b5d3bb0c1
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856901"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Reference k modulu & algoritmu pro návrháře Azure Machine Learning (Preview)

Tento referenční obsah poskytuje technické informace o každém algoritmu a modulech strojového učení, které jsou dostupné v Azure Machine Learning designeru (Preview).

Každý modul představuje sadu kódu, který může běžet nezávisle a provede úlohu strojového učení s ohledem na požadované vstupy. Modul může obsahovat konkrétní algoritmus nebo provést úkol, který je důležitý ve strojovém učení, jako je například chybějící hodnota, nebo statistická analýza.

Nápovědu k výběru algoritmů najdete v tématu. 
* [Postup při výběru algoritmů](../how-to-select-algorithms.md)
* [Tahákový list s algoritmem Azure Machine Learning](../algorithm-cheat-sheet.md)

> [!TIP]
> V jakémkoli kanálu v Návrháři můžete získat informace o konkrétním modulu. Když na modul najedete myší v seznamu modul nebo v pravém podokně modulu, vyberte odkaz další **informace** na kartě modul.

## <a name="data-preparation-modules"></a>Moduly pro přípravu dat


| Funkce | Description | Modul |
| --- |--- | --- |
| Vstup a výstup dat | Přesuňte data z cloudových zdrojů do vašeho kanálu. Zápis výsledků nebo mezilehlých dat do Azure Storage, SQL Database nebo podregistru, při spuštění kanálu nebo použití cloudového úložiště k výměně dat mezi kanály.  | [Ruční zadávání dat](enter-data-manually.md) <br/> [Export dat](export-data.md) <br/> [Import dat](import-data.md) |
| Transformace dat | Operace s daty, která jsou jedinečná pro strojové učení, jako je normalizace nebo binningu dat, snižování dimenzionálního omezení a převod dat mezi různými formáty souborů.| [Přidání sloupců](add-columns.md) <br/> [Přidání řádků](add-rows.md) <br/> [Použití matematických operací](apply-math-operation.md) <br/> [Použití transformace SQL](apply-sql-transformation.md) <br/> [Vyčištění chybějících dat](clean-missing-data.md) <br/> [Oříznutí hodnot](clip-values.md) <br/> [Převod na C](convert-to-csv.md) <br/> [Převod na datovou sadu](convert-to-dataset.md) <br/> [Převod na hodnoty indikátoru](convert-to-indicator-values.md) <br/> [Úpravy metadat](edit-metadata.md) <br/> [Seskupení dat do přihrádek](group-data-into-bins.md) <br/> [Spojení dat](join-data.md) <br/> [Normalizace dat](normalize-data.md) <br/> [Rozdělení a ukázky](partition-and-sample.md)  <br/> [Odebrání duplicitních řádků](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Transformace pro výběr sloupců](select-columns-transform.md) <br/> [Výběr sloupců v datové sadě](select-columns-in-dataset.md) <br/> [Rozdělení dat](split-data.md) |
| Výběr funkcí | Vyberte podmnožinu relevantních užitečných funkcí, které se použijí při sestavování analytického modelu. | [Výběr funkcí na základě filtrů](filter-based-feature-selection.md) <br/> [Důležitost funkce permutací](permutation-feature-importance.md) |
| Statistické funkce | Poskytněte širokou škálu statistických metod, které se týkají datové vědy. | [Sumarizace dat](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algoritmy strojového učení

| Funkce | Description | Modul |
| --- |--- | --- |
| Regrese | Předpověď hodnoty. | [Posílená regrese rozhodovacího stromu](boosted-decision-tree-regression.md) <br/> [Regrese rozhodovacího lesa](decision-forest-regression.md) <br/> [Lineární regrese](linear-regression.md)  <br/> [Regrese neurální sítě](neural-network-regression.md)  <br/> |
| Clustering | Seskupte data dohromady.| [Clustering K-Means](k-means-clustering.md)
| Classification | Předpověď třídy.  Vyberte si binární (dvě třídy) nebo algoritmy s více třídami.| [Posílený rozhodovací strom s několika třídami](multiclass-boosted-decision-tree.md) <br/> [Rozhodovací les s více třídami](multiclass-decision-forest.md) <br/> [Logistická regrese s více třídami](multiclass-logistic-regression.md)  <br/> [Neurální síť s více třídami](multiclass-neural-network.md) <br/> [One vs. All Multiclass](one-vs-all-multiclass.md) <br/> [Zprůměrovaný perceptron se dvěma třídami](two-class-averaged-perceptron.md) <br/>  [Posílený rozhodovací strom se dvěma třídami](two-class-boosted-decision-tree.md)  <br/> [Rozhodovací les se dvěma třídami](two-class-decision-forest.md) <br/>  [Logistická regrese se dvěma třídami](two-class-logistic-regression.md) <br/> [Neurální síť se dvěma třídami](two-class-neural-network.md) <br/> [Support Vector Machine (SVM) se dvěma třídami](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduly pro vytváření a vyhodnocování modelů

| Funkce | Description | Modul |
| --- |--- | --- |
| Školení modelů | Spusťte data pomocí algoritmu. |  [Trénování modelu clusteringu](train-clustering-model.md) <br/> [Trénování modelu](train-model.md) <br/> [Trénování modelu PyTorch](train-pytorch-model.md) <br/> [Ladění hyperparametrů modelů](tune-model-hyperparameters.md) |
| Bodování a vyhodnocení modelu | Změřte přesnost vyučeného modelu. | [Použití transformace](apply-transformation.md) <br/> [Přiřazení dat do clusterů](assign-data-to-clusters.md) <br/> [Křížové ověření modelu](cross-validate-model.md) <br/> [Vyhodnocení modelu](evaluate-model.md) <br/> [Model určení skóre obrázků](score-image-model.md) <br/> [Určení skóre modelu](score-model.md) |
| Jazyk Pythonu | Napište kód a vložte ho do modulu pro integraci Pythonu s vaším kanálem. | [Vytvoření modelu Pythonu](create-python-model.md) <br/> [Spouštění skriptů Pythonu](execute-python-script.md) |
| Jazyk R | Napište kód a vložte ho do modulu pro integraci jazyka R s vaším kanálem. | [Spouštění skriptů R](execute-r-script.md) |
| Analýza textu | Poskytněte specializované výpočetní nástroje pro práci s strukturovaným i nestrukturovaným textem. |  [Převod slova na vektor](convert-word-to-vector.md) <br/> [Extrakce funkcí n-gramů z textu](extract-n-gram-features-from-text.md) <br/> [Hashování funkcí](feature-hashing.md) <br/> [Předzpracování textu](preprocess-text.md) <br/> [Model LDA (Latent Dirichlet Allocation)](latent-dirichlet-allocation.md) <br/> [Hodnocení modelu Vowpal Wabbit](score-vowpal-wabbit-model.md) <br/> [Trénování modelu Vowpal Wabbit](train-vowpal-wabbit-model.md)|
| Počítačové zpracování obrazu | Předzpracování dat imagí a související moduly pro rozpoznávání imagí |  [Použití transformace obrázku](apply-image-transformation.md) <br/> [Převod do adresáře obrázků](convert-to-image-directory.md) <br/> [Počáteční transformace obrázku](init-image-transformation.md) <br/> [Rozdělení adresáře obrázků](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| Doporučení | Modely doporučení pro sestavení. | [Vyhodnocení doporučovacího systému](evaluate-recommender.md) <br/> [Určení skóre doporučovacího systému SVD](score-svd-recommender.md) <br/> [Určení skóre doporučovacího systému Wide and Deep](score-wide-and-deep-recommender.md)<br/> [Trénování doporučovacího systému SVD](train-SVD-recommender.md) <br/> [Trénování doporučovacího systému Wide and Deep](train-wide-and-deep-recommender.md)|
| Detekce anomálií | Sestavujte modely detekce anomálií. | [Detekce anomálií na základě PCA](pca-based-anomaly-detection.md) <br/> [Trénování modelu detekce anomálií](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Webová služba

Přečtěte si o [modulech webové služby](web-service-input-output.md) , které jsou nezbytné pro odvození v reálném čase v Návrháři Azure Machine Learning.

## <a name="error-messages"></a>Chybové zprávy

Seznamte se s [chybovými zprávami a kódy výjimek](designer-error-codes.md) , se kterými se můžete setkat pomocí modulů v Návrháři Azure Machine Learning.

## <a name="next-steps"></a>Další kroky

* [Kurz: sestavení modelu v Návrháři pro předpověď automatických cen](../tutorial-designer-automobile-price-train-score.md)

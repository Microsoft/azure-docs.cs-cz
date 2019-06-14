---
title: Referenční informace k modulům a algoritmům
titleSuffix: Azure Machine Learning service
description: Další informace o dostupných modulů ve vizuální rozhraní Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8825f1dc3b66a5c4981ba25a90813aec63975b1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145140"
---
# <a name="algorithm--module-reference-overview"></a>Přehled modulům a algoritmům

Tato referenční obsah poskytuje technické znalosti potřebné v každém strojového učení algoritmů a modulů k dispozici v rozhraní visual (preview) služby Azure Machine Learning.

Každý modul představuje sadu kód, který můžete spustit nezávisle a provést služby machine learning úkolu, zadané vstupy. Modul může obsahovat konkrétního algoritmu nebo provést úlohu, která je důležitý pro strojové učení, jako je například chybějící hodnota náhrada nebo statistická analýza.

> [!TIP]
> V experimentu vizuální rozhraní můžete získat informace o konkrétní modulu. Vyberte modul, a pak vyberte **další pomoc** odkaz v **rychlou nápovědu** podokně.

## <a name="modules"></a>Moduly

Moduly jsou uspořádané podle funkce:

| Funkce | Popis | Modul |
| --- |--- | ---- |
| Převody formátů dat | Převod dat mezi různými formáty souborů používaných pro strojové učení | [Převést na sdílený svazek clusteru](convert-to-csv.md) |
| Datový vstup a výstup | Přesun dat z cloudových zdrojů do svého experimentu. Zapsat výsledky nebo dočasných dat do služby Azure Storage, SQL database nebo Hivu, při spuštění experimentu, nebo použít cloudové úložiště pro výměnu dat mezi pokusy.  | [Import dat](import-data.md)<br/>[Export dat](export-data.md)<br/>[Ruční zadání dat](enter-data-manually.md) |
| Transformace dat | Operace s daty, které jsou jedinečné pro strojové učení, jako je například normalizace nebo binning dat, výběr funkce a snížení.| [Výběr sloupců v datové sadě](select-columns-in-dataset.md) <br/> [Upravit Metadata](edit-metadata.md) <br/> [Vyčištění chybějících dat](clean-missing-data.md) <br/> [Přidání sloupců](add-columns.md) <br/> [Přidání řádků](add-rows.md) <br/> [Odebrání duplicitních řádků](remove-duplicate-rows.md) <br/> [Rozdělení dat.](split-data.md) <br/> [Normalizovat Data](normalize-data.md) <br/> [Rozdělení a vzorky](partition-and-sample.md) |
| Modul Python | Psaní kódu a vložení v modulu integrace Python s experimentu. | [Spusťte skript Pythonu](execute-python-script.md)   <br/> [Create Python Model](create-python-model.md)
|  | **Strojového učení se supervizí**: | |
| Klasifikace | Předpověď třídu.  Vyberte si z binárního souboru (dvěma třídami) nebo víc tříd algoritmy.| [Rozhodovací les se multiclass](multiclass-decision-forest.md) <br/> [Víc tříd logistické regrese](multiclass-logistic-regression.md)  <br/> [Multiclass Neural Network](multiclass-neural-network.md)  <br/>  [Logistické regrese Two-Class](two-class-logistic-regression.md)  <br/>[Průměrné Perceptron Two-Class](two-class-averaged-perceptron.md) <br/> [Two-Class&nbsp;Boosted&nbsp;rozhodnutí&nbsp;stromu](two-class-boosted-decision-tree.md)  <br/> [Rozhodovací les se dvěma třídami](two-class-decision-forest.md)  <br/> [Two-Class Neuronové sítě](two-class-neural-network.md)  <br/> [Dvě&#8209;třídy&nbsp;podporu&nbsp;vektoru&nbsp;počítače](two-class-support-vector-machine.md) 
| Vytváření clusterů | Data seskupit dohromady.| [K-Means Clustering](k-means-clustering.md)
| Regrese | Předvídejte hodnotu. | [Lineární regrese](linear-regression.md)  <br/> [Regrese neuronové sítě](neural-network-regression.md)  <br/> [Rozhodovací les regrese](decision-forest-regression.md)  <br/> [Vylepšené&nbsp;rozhodnutí&nbsp;stromu&nbsp;regrese](boosted-decision-tree-regression.md)
|  | **Sestavení a vyhodnocení modelů**: | |
| Trénování   | Zpracování dat pomocí algoritmu. | [Trénování modelu](train-model.md)  <br/> [Trénování modelu Clustering](train-clustering-model.md)    |
| Vyhodnocení modelu | Měří přesnost trénovaného modelu. |  [Vyhodnocení modelu](evaluate-model.md)
| Skóre | Získání předpovědi z jsme právě natrénovaného modelu. | [Použije transformace](apply-transformation.md)<br/>[Přiřadit&nbsp;Data&nbsp;k&nbsp;clusterů](assign-data-to-clusters.md) <br/>[Určení skóre modelu](score-model.md)

## <a name="error-messages"></a>Chybové zprávy

Další informace o [chybové zprávy a kódy výjimek](machine-learning-module-error-codes.md) může dojít k používání modulů ve vizuální rozhraní služby Azure Machine Learning.

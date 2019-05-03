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
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029337"
---
# <a name="algorithm--module-reference-overview"></a>Přehled modulům a algoritmům

Tato referenční obsah poskytuje technické znalosti potřebné v každém strojového učení algoritmů a modulů k dispozici v rozhraní visual (preview) služby Azure Machine Learning. 

Každý modul představuje sadu kód, který můžete spustit nezávisle a provést služby machine learning úkolu, zadané vstupy. Modul může obsahovat konkrétního algoritmu nebo provést úlohu, která je důležitý pro strojové učení, jako je například chybějící hodnota náhrada nebo statistická analýza. 

> [!TIP]
> V experimentu vizuální rozhraní můžete získat informace o konkrétní modulu. Vyberte modul, a pak vyberte **další pomoc** odkaz v **rychlou nápovědu** podokně.

Moduly jsou uspořádané podle funkce:

**Převody formátů data**

  + [Převést na sdílený svazek clusteru ](convert-to-csv.md)

**Data vstupní a výstupní moduly** práci pro přesun dat z cloudových zdrojů do svého experimentu. Můžete napsat výsledků nebo dočasných dat do služby Azure Storage, SQL database nebo Hivu, při spuštění experimentu nebo používat cloudové úložiště pro výměnu dat mezi pokusy.  

  + [Import dat](import-data.md)

  + [Export dat](export-data.md)

  + [Ruční zadání dat](enter-data-manually.md)


**Moduly transformace dat** podporují operace pro data, která jsou jedinečné pro strojové učení, jako je například normalizace nebo binning dat, výběr funkce a snížení.

  + [Výběr sloupců v datové sadě](select-columns-in-dataset.md)

  + [Upravit Metadata](edit-metadata.md)

  + [Vyčištění chybějících dat](clean-missing-data.md)

  + [Přidání sloupců](add-columns.md)

  + [Přidání řádků](add-rows.md)

  + [Odebrání duplicitních řádků](remove-duplicate-rows.md)

  + [Rozdělení dat.](split-data.md)

  + [Normalizovat Data](normalize-data.md)

  + [Rozdělení a vzorky](partition-and-sample.md)


**Strojového učení se supervizí** například clustering, počítač vektorové podpory nebo neuronové sítě, jsou k dispozici v rámci jednotlivých modulů, které umožňují přizpůsobit úlohy strojového učení s příslušnými parametry.  
  + [Určení skóre modelu](score-model.md)

  + [Přiřaďte Data do clusterů ](assign-data-to-clusters.md)

  + [Trénování modelu](train-model.md)

  + [Trénování modelu Clustering](train-clustering-model.md)

  + [Vyhodnocení modelu](evaluate-model.md)

  + [Použije transformace](apply-transformation.md)

  + [Lineární regrese](linear-regression.md)

  + [Regrese neuronové sítě](neural-network-regression.md)

  + [Rozhodovací les regrese](decision-forest-regression.md)

  + [Posílená regrese rozhodovacího stromu](boosted-decision-tree-regression.md)

  + [Two-Class posíleného rozhodovacího stromu](two-class-boosted-decision-tree.md)

  + [Logistické regrese Two-Class](two-class-logistic-regression.md)

  + [Víc tříd logistické regrese](multiclass-logistic-regression.md)

  + [Multiclass Neural Network](multiclass-neural-network.md)

  + [Rozhodovací les se multiclass](multiclass-decision-forest.md)

  + [Průměrné Perceptron Two-Class](two-class-averaged-perceptron.md)

  + [Rozhodovací les se dvěma třídami](two-class-decision-forest.md)

  + [Two-Class Neuronové sítě](two-class-neural-network.md)

  + [Two-Class Support Vector Machine](two-class-support-vector-machine.md)
  
  + [K-Means Clustering](k-means-clustering.md)


**Modul Pythonu** umožňuje snadno spouštět vlastní funkci. Psát kód a vložit do modulu integrace Pythonu pomocí služby experimentu.
  + [Spusťte skript Pythonu](execute-python-script.md)

  + [Create Python Model](create-python-model.md)



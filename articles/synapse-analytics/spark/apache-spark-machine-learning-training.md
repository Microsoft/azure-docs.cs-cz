---
title: Výuka modelů strojového učení pomocí Apache Spark
description: Použití Apache Spark ve službě Azure synapse Analytics k výuce modelů strojového učení
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116802"
---
# <a name="train-machine-learning-models"></a>Výuka modelů strojového učení
Apache Spark ve službě Azure synapse Analytics umožňuje strojové učení s velkými objemy dat a poskytuje možnost získat hodnotný přehled z velkých objemů strukturovaných, nestrukturovaných a rychlých přesunů dat. K dispozici je několik možností pro školení modelů strojového učení pomocí Azure Spark ve službě Azure synapse Analytics: Apache Spark MLlib, Azure Machine Learning a různých dalších open source knihoven. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML a MLlib
Apache Spark ve službě Azure synapse Analytics je jedním z implementací Apache Spark v cloudu od Microsoftu. Poskytuje jednotnou Open Source architekturu paralelního zpracování dat, která podporuje zpracování v paměti, aby se zvýšila analýza velkých objemů dat. Modul pro zpracování Spark je založený na rychlosti, snadném použití a propracované analýze. Funkce distribuovaného výpočtu v paměti Sparku nabízí dobrou volbu pro iterativní algoritmy používané v strojovém učení a výpočtech grafů. 

Existují dvě škálovatelné knihovny strojového učení, které přinášejí funkce pro modelování algoritmu do tohoto distribuovaného prostředí: MLlib a SparkML. MLlib obsahuje původní rozhraní API postavené nad RDD. SparkML je novější balíček, který poskytuje rozhraní API vyšší úrovně postavené nad datovými snímky pro sestavování kanálů ML. SparkML ještě nepodporuje všechny funkce MLlib, ale nahrazuje MLlib jako standardní knihovnu strojového učení Spark.

> [!NOTE]
> 
> Další informace o vytváření modelu SparkML najdete v tomto [kurzu](../spark/apache-spark-azure-machine-learning-tutorial.md).

## <a name="popular-libraries"></a>Oblíbené knihovny
Každý Apache Spark fond ve službě Azure synapse Analytics se dodává se sadou předem načtených a oblíbených knihoven strojového učení. Tyto knihovny poskytují opakovaně použitelný kód, který můžete chtít zahrnout do svých programů nebo projektů. Mezi relevantní knihovny strojového učení, které jsou zahrnuté ve výchozím nastavení, patří:
- [Scikit-učit](https://scikit-learn.org/stable/index.html) se jedna z nejoblíbenějších knihoven strojového učení s jedním uzlem pro algoritmy klasických ml. Scikit – Přečtěte si, že se podporuje většina algoritmů učení pod dohledem a bez dohledu a dá se použít i pro dolování dat a analýzu dat.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) je oblíbená knihovna strojového učení, která obsahuje optimalizované algoritmy pro účely školení pro rozhodovací stromy a náhodné doménové struktury. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) jsou výkonné knihovny pro hloubkové učení v Pythonu. V rámci fondu Apache Spark ve službě Azure synapse Analytics můžete pomocí těchto knihoven vytvořit modely na jednom počítači nastavením počtu prováděcích modulů ve fondu na hodnotu nula. I když Apache Spark v rámci této konfigurace není funkční, je to jednoduchý a nákladově efektivní způsob, jak vytvořit modely na jednom počítači.

Další informace o dostupných knihovnách a souvisejících verzích najdete v publikovaném [modulu runtime Azure synapse Analytics](../spark/apache-spark-version-support.md).

## <a name="mmlspark"></a>MMLSpark
Knihovna Microsoft Machine Learning pro Apache Spark je [MMLSpark](https://github.com/Azure/mmlspark). Tato knihovna je navržená tak, aby byly vědečtí data v Sparku větší produktivita, zvýšila se rychlost experimentování a využila špičkové techniky strojového učení, včetně hloubkového učení, u velkých datových sad. 

MMLSpark poskytuje vrstvu nad rozhraními API nízké úrovně SparkML při sestavování škálovatelných modelů ML, jako jsou indexování řetězců, vynucený data do rozložení očekávaného algoritmy strojového učení a nastavování vektorů funkcí. MMLSpark Library zjednodušuje tyto a další běžné úlohy pro vytváření modelů v PySpark.

## <a name="automated-ml-in-azure-machine-learning"></a>Automatizované ML v Azure Machine Learning 
Azure Machine Learning je cloudové prostředí, které umožňuje výukové, nasazování, automatizaci, správu a sledování modelů strojového učení. Automatizovaná ML v Azure Machine Learning přijímá školicí data a nastavení konfigurace a automaticky iterovat kombinace různých metod normalizace/normalizace funkcí, modelů a nastavení parametrů pro dosažení nejlepšího modelu. 

Při použití automatizovaného ML v rámci služby Azure synapse Analytics můžete využít hloubkovou integraci mezi různými službami a zjednodušit tak & školení modelu ověřování. 

> [!NOTE]
> 
> V tomto [kurzu](./spark/../apache-spark-azure-machine-learning-tutorial.md)se dozvíte víc o vytváření Azure Machine Learning automatizované experimenty ml.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) poskytuje možnosti strojového učení, které řeší Obecné problémy, jako je například analýza textu pro emocionálních mínění nebo analýza obrázků pro rozpoznávání objektů a ploch. Abyste mohli s těmito službami pracovat, nepotřebujete žádné speciální vědomosti o strojovém učení nebo datových vědách. Služba rozpoznávání poskytuje součást nebo všechny komponenty v řešení strojového učení: data, algoritmus a trained model. Tyto služby mají vyžadovat obecné znalosti o vašich datech, aniž by bylo potřeba mít zkušenosti s Machine Learningem nebo datovou vědy. Tyto předem připravené Cognitive Services můžete v rámci služby Azure synapse Analytics využít automaticky.

## <a name="next-steps"></a>Další kroky
Tento článek poskytuje přehled různých možností pro učení modelů strojového učení v rámci fondů Apache Spark ve službě Azure synapse Analytics. Další informace o školení modelů najdete v následujícím kurzu:

- Spouštění automatizovaných experimentů ML pomocí Azure Machine Learning a Azure synapse Analytics: [kurz automatizovaného ml](../spark/apache-spark-azure-machine-learning-tutorial.md) 
- Spuštění SparkML experimentů: [kurz Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)
- Zobrazit výchozí knihovny: [Azure synapse Analytics runtime](../spark/apache-spark-version-support.md)
---
title: Strojové učení s využitím Apache Sparku
description: Tento článek obsahuje koncepční přehled možností strojového učení a datové vědy, které jsou dostupné prostřednictvím Apache Spark ve službě Azure synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116921"
---
# <a name="machine-learning-with-apache-spark"></a>Machine Learning s Apache Spark

Apache Spark ve službě Azure synapse Analytics umožňuje strojové učení s velkými objemy dat a poskytuje možnost získat hodnotný přehled z velkých objemů strukturovaných, nestrukturovaných a rychlých přesunů dat. 

Tato část obsahuje přehled a kurzy k pracovním postupům pro strojové učení, včetně analýz dat průzkumného zpracování, funkcí, školení modelů, bodování modelu a nasazení.  

## <a name="synapse-runtime"></a>Modul runtime synapse 
Modul runtime synapse je spravované prostředí optimalizované pro datové vědy a strojové učení. Modul runtime synapse nabízí ve výchozím nastavení řadu oblíbených open source knihoven a sestavení v sadě Azure Machine Learning SDK. Modul runtime synapse zahrnuje i mnoho externích knihoven, včetně PyTorch, Scikit-učení, XGBoost a dalších.

Přečtěte si další informace o dostupných knihovnách a souvisejících verzích zobrazením publikovaného [běhového prostředí Azure synapse Analytics](../spark/apache-spark-version-support.md).

## <a name="exploratory-data-analysis"></a>Analýza dat průzkumného testování
Při použití Apache Spark ve službě Azure synapse Analytics jsou k dispozici různé možnosti, které vám pomohou vizualizovat vaše data, včetně možností grafu poznámkového bloku synapse, přístup k oblíbeným Open Source knihovnám, jako jsou Seaborn a matplotlib, a také integraci s synapse SQL a Power BI.

Další informace o vizualizaci dat a možnostech analýzy dat najdete v článku o tom, jak [vizualizovat data pomocí notebooků Azure synapse](../spark/apache-spark-data-visualization.md).

## <a name="feature-engineering"></a>Příprava atributů
Ve výchozím nastavení obsahuje modul runtime synapse sadu knihoven, které se běžně používají pro strojírenství funkcí. U rozsáhlých datových sad můžete pro strojírenství funkcí použít Spark SQL, MLlib a koalas. U menších datových sad poskytují knihovny třetích stran, jako jsou numpy, PANDAS a Scikit, užitečné metody pro tyto scénáře.

## <a name="train-models"></a>Trénování modelů
K dispozici je několik možností pro školení modelů strojového učení pomocí Azure Spark ve službě Azure synapse Analytics: Apache Spark MLlib, Azure Machine Learning a různých dalších open source knihoven. 

Další informace o možnostech strojového učení najdete v článku o postupu při [výuce modelů ve službě Azure synapse Analytics](../spark/apache-spark-machine-learning-training.md).

### <a name="sparkml-and-mllib"></a>SparkML a MLlib
Funkce distribuovaného výpočtu v paměti Sparku nabízí dobrou volbu pro iterativní algoritmy používané v strojovém učení a výpočtech grafů. ```spark.ml``` poskytuje jednotnou sadu rozhraní API vysoké úrovně, které uživatelům umožňují vytvářet a ladit kanály strojového učení. Další informace o nástroji najdete ```spark.ml``` v [Průvodci programováním Apache Spark ml](https://spark.apache.org/docs/1.2.2/ml-guide.html).

### <a name="azure-machine-learning-automated-ml"></a>Azure Machine Learning automatizované ML
[Azure Machine Learning automatizovanou ml](../../machine-learning/concept-automated-ml.md) (automatizované Machine Learning) pomáhá automatizovat proces vývoje modelů strojového učení. Umožňuje datovým vědcům, analytikům a vývojářům vytvářet modely ML s vysokým škálováním, efektivitou a produktivitou, a přitom udržuje kvalitu modelu. Komponenty pro spuštění Azure Machine Learning automatizovaného ML sady SDK jsou přímo integrovány do modulu runtime synapse.

### <a name="open-source-libraries"></a>Open source knihovny
Každý Apache Spark fond ve službě Azure synapse Analytics se dodává se sadou předem načtených a oblíbených knihoven strojového učení.  Mezi relevantní knihovny strojového učení, které jsou zahrnuté ve výchozím nastavení, patří:

- [Scikit-učit](https://scikit-learn.org/stable/index.html) se jedna z nejoblíbenějších knihoven strojového učení s jedním uzlem pro algoritmy klasických ml. Scikit – Přečtěte si, že se podporuje většina algoritmů učení pod dohledem a bez dohledu a dá se použít i pro dolování dat a analýzu dat.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) je oblíbená knihovna strojového učení, která obsahuje optimalizované algoritmy pro účely školení pro rozhodovací stromy a náhodné doménové struktury. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) jsou výkonné knihovny pro hloubkové učení v Pythonu. V rámci fondu Apache Spark ve službě Azure synapse Analytics můžete pomocí těchto knihoven vytvořit modely na jednom počítači nastavením počtu prováděcích modulů ve fondu na hodnotu nula. I když Apache Spark v rámci této konfigurace není funkční, je to jednoduchý a nákladově efektivní způsob, jak vytvořit modely na jednom počítači.

## <a name="track-model-development"></a>Sledování vývoje modelů
[MLFlow](https://www.mlflow.org/) je open source knihovna pro správu životního cyklu experimentů ve strojovém učení. Sledování MLFlow je součást MLflow, která protokoluje a sleduje metriky běhu a artefakty modelu. Další informace o tom, jak můžete používat sledování MLFlow prostřednictvím služby Azure synapse Analytics a Azure Machine Learning, najdete v tomto kurzu, [Jak používat MLFlow](../../machine-learning/how-to-use-mlflow.md).

## <a name="model-scoring"></a>Bodování modelu
Model bodování nebo Inferencing je fáze, kde se model používá k vytvoření předpovědi. Pro vyhodnocování modelů pomocí SparkML nebo MLLib můžete využít nativní metody Spark k provádění Inferencing přímo v dataframe Spark. Pro jiné open source knihovny a typy modelů můžete také vytvořit Spark UDF pro horizontální navýšení kapacity pro velké datové sady. Pro menší datové sady můžete použít také metody odvození nativního modelu poskytované knihovnou.

## <a name="register-and-serve-models"></a>Registrace a obsluha modelů
Registrace modelu umožňuje ukládat, uchovávat verze a sledovat metadata o modelech v pracovním prostoru. Po dokončení školení modelu můžete model zaregistrovat do [registru Azure Machine Learning modelu](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere). Po registraci můžete ONNX modely použít také k [rozšíření dat](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) uložených ve vyhrazených fondech SQL.

## <a name="next-steps"></a>Další kroky
Pokud chcete začít se službou Machine Learning ve službě Azure synapse Analytics, přečtěte si následující kurzy:
- [Analýza dat pomocí notebooků Azure synapse](../spark/apache-spark-data-visualization-tutorial.md)

- [Výuka modelu strojového učení s využitím automatizovaného ML](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Školení modelu strojového učení pomocí Apache Spark MLlib](../spark/apache-spark-machine-learning-mllib-notebook.md)

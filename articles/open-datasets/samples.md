---
title: Příklady Jupyter poznámkových bloků pomocí dat NOAA
titleSuffix: Azure Open Datasets
description: Pomocí ukázkových poznámkových bloků Jupyter pro Azure Open DataSets se naučíte načíst otevřené datové sady a použít je k obohacení ukázkových dat. Mezi techniky patří použití Sparku a PANDAS ke zpracování dat.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: c24363caac1db8dd8ce21b690ef989b2beb97f2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88506052"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Příklady Jupyter poznámkových bloků ukazují, jak rozšířit data pomocí otevřených datových sad 
Příklady Jupyter poznámkových bloků pro Azure Open DataSet ukazují, jak načíst otevřené datové sady a používat je k obohacení ukázkových dat. Mezi techniky patří použití Apache Spark a PANDAS ke zpracování dat.

>[!IMPORTANT]
>Když pracujete v prostředí, které není Spark, umožňuje otevřené datové sady stahovat jenom jeden měsíc dat s určitými třídami, aby se předešlo MemoryErrorí s velkými datovými sadami.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Načtení dat NOAA Integrated Surface Database (ISD) 
|Poznámkový blok        | Popis                                    |
|----------------|------------------------------------------------|
|[Načtení jednoho nedávných měsíců dat počasí do PANDAS dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Naučte se, jak načíst historická data o počasí do oblíbeného PANDAS dataframe. |
|[Načtení jednoho posledního měsíce dat o počasí do datového rámce Sparku](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Naučte se, jak načíst historická data o počasí do oblíbeného datového rámce Sparku.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Spojit ukázková data s daty NOAA ISD 
|Poznámkový blok        | Popis                                    |
|----------------|------------------------------------------------|
|[Spojit ukázková data s daty o počasí – PANDAS](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Propojte si ukázkovou datovou sadu senzorů na 1 měsíc s čtenými počasí v PANDAS dataframe.  |
|[Spojování ukázkových dat s daty o počasí – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Připojte se k demonstrační datové sadě umístění senzorů s čtenými počasí v dataframe Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Spojování dat NYC taxislužby s daty NOAA ISD 
|Poznámkový blok        | Popis                                    |
|----------------|------------------------------------------------|
|[Taxislužby data o cestách obohacená daty o počasí – PANDAS](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Načtěte data NYC zelenou taxislužby (více než 1 měsíc) a rozšiřte je daty o počasí v dataframe PANDAS. Tento příklad přepisuje metodu `get_pandas_limit` a vyvažuje výkon načítání dat s množstvím dat.|
|[Taxislužby data o cestách obohacená daty o počasí – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Načtěte NYC zelená data taxislužby a rozšiřujte je pomocí dat o počasí v dataframe Spark.  |

## <a name="next-steps"></a>Další kroky

* [Kurz: regresní modelování pomocí automatizovaného strojového učení a otevřené datové sady](/azure/machine-learning/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK pro otevřené datové sady](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Katalog Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)
* [Vytvoření datové sady Azure Machine Learning z otevřené sady dat](how-to-create-azure-machine-learning-dataset-from-open-dataset.md)
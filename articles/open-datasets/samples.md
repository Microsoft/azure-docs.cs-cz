---
title: Příklad poznámkových bloků Jupyter s použitím dat NOAA
titleSuffix: Azure Open Datasets
description: Pomocí ukázkových poznámkových bloků Jupyter pro datové sady Azure Open se dozvíte, jak načíst otevřené datové sady a použít je k obohacení ukázkových dat. Techniky zahrnují použití Spark a Pandas ke zpracování dat.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 8b96a35db91a282be1fb5e4c6143e6bd0a0203f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73606147"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Příklad poznámkových bloků Jupyter ukazují, jak obohatit data o otevřené datové sady 
Příklad poznámkových bloků Jupyter pro datové sady Azure Open ukazují, jak načíst otevřené datové sady a použít je k obohacení ukázkových dat. Techniky zahrnují použití Apache Spark a Pandas ke zpracování dat.

>[!IMPORTANT]
>Při práci v prostředí, které není součástí sparku, umožňují otevřené datové sady stahovat pouze jeden měsíc dat současně s určitými třídami, aby se zabránilo MemoryError s velkými datovými sadami.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Načíst data integrované povrchové databáze (ISD) NOAA 
|Poznámkový blok        | Popis                                    |
|----------------|------------------------------------------------|
|[Načíst jeden poslední měsíc dat o počasí do datového rámce Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Přečtěte si, jak načíst historická data o počasí do svého oblíbeného datového rámce Pandas. |
|[Načtení dat o počasí za poslední měsíc do datového rámce Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Přečtěte si, jak načíst historická data o počasí do svého oblíbeného datového rámce Spark.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Připojení ukázkových dat pomocí dat NOAA ISD 
|Poznámkový blok        | Popis                                    |
|----------------|------------------------------------------------|
|[Připojte se k demo datům s údaji o počasí - Pandy](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Připojte se k 1měsíční demo datové sadě míst senzorů s údaji o počasí v datovém rámu Pandas.  |
|[Připojte se k ukázkovým datům s daty o počasí – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Připojte se k demo datové sadě míst senzorů s údaji o počasí v datovém rámci Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Připojte se k datům taxi služby NYC s daty NOAA ISD 
|Poznámkový blok        | Popis                                    |
|----------------|------------------------------------------------|
|[Údaje o jízdě taxíkem obohacené o údaje o počasí - Pandy](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Načtěte nyc zelená data taxi (více než 1 měsíc) a obohaťte je o data o počasí v datovém rámu Pandas. Tento příklad přepíše `get_pandas_limit` metodu a vyvažuje výkon načítání dat s množstvím dat.|
|[Údaje o jízdě taxíkem obohacené o údaje o počasí – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Načtěte nyc zelená data taxi a obohaťte je o data o počasí v datovém rámu Spark.  |

## <a name="next-steps"></a>Další kroky

* [Kurz: Regresní modelování s automatizovaným strojovým učením a otevřenou datovou sadou](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Sada Python SDK pro otevřené datové sady](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Katalog Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)

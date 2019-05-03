---
title: Poznámkové bloky Jupyter příklad použití NOAA otevřete datovou sadu
titleSuffix: Azure Open Datasets
description: Zjistěte, jak načíst otevřít datové sady a využít k obohacení ukázková data pomocí příkladu poznámkové bloky Jupyter pro Azure otevření datové sady. Mezi dostupné techniky patří použití Sparku a Pandas zpracovat data.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: c1b86199f13454f4785a6737b25e489d45dd53f8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026841"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Poznámkové bloky Jupyter příklad ukazují, jak rozšířit data s datovými sadami otevřít 
Poznámkové bloky Jupyter příklad pro datové sady otevře Azure ukazují, jak načíst otevřít datové sady a využít k obohacení ukázková data. Mezi dostupné techniky patří použití Apache Spark a Pandas zpracovat data.

>[!IMPORTANT]
>Při práci v prostředí bez Spark, umožňuje otevřít datové sady, stahování jenom jeden měsíc dat najednou kombinací určité třídy předejdete tak MemoryError s rozsáhlými datovými sadami.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Načíst data NOAA integrované Surface databáze (investičních službách) 
|Poznámkový blok        | Popis                                    |
|----------------|------------------------------------------------|
|[Načíst poslední měsíc data o počasí do struktury Pandas dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Zjistěte, jak načíst do vaší oblíbené balíčky Pandas dataframe historická data o počasí. |
|[Načíst poslední měsíc data o počasí do Spark dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Zjistěte, jak načíst do oblíbených Spark dataframe historická data o počasí.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Připojte se k ukázková data s daty investičních NOAA službách 
|Poznámkový blok        | Popis                                    |
|----------------|------------------------------------------------|
|[Připojte se k ukázková data s daty o počasí - Pandas ](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Připojte se k 1 měsíc ukázkové datové sady senzor umístění se údaje o počasí v Pandas dataframe.  |
|[Připojte se k ukázková data s daty o počasí – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Připojte se k ukázkové datové sady senzor umístění se údaje o počasí v Spark dataframe. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Připojte se k NYC data taxislužby města s daty investičních NOAA službách 
|Poznámkový blok        | Popis                                    |
|----------------|------------------------------------------------|
|[Data o jízdách taxislužby obohacené data o počasí - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Načtení dat taxislužby zelené NYC (více než 1 měsíc) a rozšiřovat s daty o počasí v Pandas dataframe. Tento příklad přepíše metodu `get_pandas_limit` a vyváží výkon množství dat a dat zatížení.|
|[Data o jízdách taxislužby obohacené data o počasí – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Načíst data taxislužby města zelené NYC a rozšiřovat s daty o počasí v Spark dataframe.  |

## <a name="next-steps"></a>Další postup

* [Kurz: Regrese modelování automatizované strojového učení a otevřete datovou sadu](tutorial-opendatasets-automl.md)
* [Python SDK pro Open datové sady](https://aka.ms/open-datasets-api)
* [Katalog Azure Open datové sady](https://azure.microsoft.com/services/open-datasets/catalog/)

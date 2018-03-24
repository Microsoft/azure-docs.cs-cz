---
title: Transformace dat pomocí poznámkového bloku Databricks - Azure | Microsoft Docs
description: Zjistěte, jak zpracovávat nebo transformace dat spuštěním Databricks Poznámkový blok.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: d4a57e45d5ddf55906fcf575df39135a227418ec
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformace dat spuštěním Databricks Poznámkový blok

Aktivity Azure Databricks poznámkového bloku v [kanál služby Data Factory](concepts-pipelines-activities.md) spouští Databricks Poznámkový blok v pracovním prostoru Azure Databricks. Tento článek vychází [aktivit transformace dat](transform-data.md) článek, který poskytne Obecné přehled o transformaci dat a aktivity podporované transformace. Azure Databricks je spravovaná platforma pro spuštění Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definici aktivity Databricks Poznámkový blok

Zde je ukázka definici JSON aktivity Databricks Poznámkový blok:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Vlastnosti aktivity Databricks Poznámkový blok

Následující tabulka popisuje vlastnostech JSON použitých v definici JSON:

|Vlastnost|Popis|Požaduje se|
|---|---|---|
|jméno|Název aktivity v kanálu.|Ano|
|description|Popisuje, jakým způsobem aktivita naloží text.|Ne|
|type|Typ aktivity aktivity Databricks Poznámkový blok, je DatabricksNotebook.|Ano|
|linkedServiceName|Název propojené služby Databricks, na kterém běží Databricks poznámkového bloku. Další informace o této propojené služby najdete v tématu [výpočetní propojené služby](compute-linked-services.md) článku.|Ano|
|notebookPath|Absolutní cesta ke spuštění v pracovním prostoru Databricks poznámkového bloku. Tato cesta musí začínat lomítkem.|Ano|
|baseParameters|Pole páry klíč-hodnota. Základní parametry, můžete použít pro každou aktivitu spustit. Pokud poznámkového bloku přebírá parametr, který není zadán, použije se výchozí hodnota z poznámkového bloku. Najít další informace o parametry v [poznámkových bloků Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Ne|

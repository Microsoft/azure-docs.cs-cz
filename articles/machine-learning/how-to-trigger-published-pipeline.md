---
title: Aktivovat kanál ML pro nová data
titleSuffix: Azure Machine Learning
description: Naučte se aktivovat spuštění kanálu ML pomocí Azure Logic Apps.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4
ms.openlocfilehash: 2e3544bee5158a855467f8cb142f176df2187ef5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318292"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Aktivovat spuštění kanálu Machine Learning z aplikace logiky

Aktivovat spuštění kanálu Azure Machine Learning, když se zobrazí nová data Můžete například chtít, aby kanál aktivoval nový model při zobrazení nových dat v účtu BLOB Storage. Nastavte Trigger pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

* Koncový bod REST pro publikovaný kanál Machine Learning. [Vytvořte a publikujte svůj kanál](how-to-create-your-first-pipeline.md). Pak vyhledejte koncový bod REST vašeho PublishedPipeline s použitím ID kanálu:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-overview.md) pro ukládání vašich dat.
* [Úložiště dat](how-to-access-data.md) ve vašem pracovním prostoru, které obsahuje podrobné informace o vašem účtu úložiště objektů BLOB.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Teď vytvořte instanci [Aplikace logiky Azure](../logic-apps/logic-apps-overview.md) . Pokud chcete, [použijte prostředí integračních služeb (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) a [nastavte klíč spravovaný zákazníkem](../logic-apps/customer-managed-keys-integration-service-environment.md) pro použití vaší aplikace logiky.

Po zřízení aplikace logiky můžete pomocí těchto kroků nakonfigurovat aktivační událost pro svůj kanál:

1. [Vytvořte spravovanou identitu přiřazenou systémem](../logic-apps/create-managed-service-identity.md) , která aplikaci poskytne přístup k vašemu pracovní prostor Azure Machine Learning.

1. Přejděte do zobrazení návrháře aplikací logiky a vyberte šablonu prázdná aplikace logiky. 
    > [!div class="mx-imgBorder"]
    > ![Prázdná šablona](media/how-to-trigger-published-pipeline/blank-template.png)

1. V Návrháři vyhledejte **objekt BLOB**. Vyberte aktivační událost **při přidání nebo úpravě objektu BLOB (pouze vlastnosti)** a přidejte tuto aktivační událost do aplikace logiky.
    > [!div class="mx-imgBorder"]
    > ![Přidání triggeru](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Zadejte informace o připojení pro účet úložiště objektů blob, který chcete monitorovat pro doplňky nebo úpravy objektu BLOB. Vyberte kontejner, který chcete monitorovat. 
 
    Vyberte **interval** a **frekvenci** dotazování na aktualizace, které pro vás budou fungovat.  

    > [!NOTE]
    > Tato aktivační událost bude monitorovat vybraný kontejner, ale nebude sledovat podsložky.

1. Přidejte akci HTTP, která se spustí při zjištění nového nebo upravovaného objektu BLOB. Vyberte **+ Nový krok**, vyhledejte a vyberte akci HTTP.

  > [!div class="mx-imgBorder"]
  > ![Vyhledat akci HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Ke konfiguraci akce použijte následující nastavení:

  | Nastavení | Hodnota | 
  |---|---|
  | Akce HTTP | POST |
  | Identifikátor URI |koncový bod publikovaného kanálu, který jste našli jako [požadavek](#prerequisites) |
  | Režim ověřování | Spravovaná identita |

1. Nastavte svůj plán tak, aby se nastavila hodnota libovolné [PipelineParametersy DataPath](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) , kterou jste mohli mít:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Použijte `DataStoreName` jako [součást](#prerequisites), kterou jste přidali do svého pracovního prostoru.
     
    > [!div class="mx-imgBorder"]
    > ![Nastavení HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Vyberte **Uložit** a váš plán je teď připravený.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématech:

> [!div class="nextstepaction"]
> [Použití Azure Machine Learningch kanálů pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md)

* Další informace o [kanálech](concept-ml-pipelines.md)
* Další informace o [prozkoumání Azure Machine Learning pomocí Jupyter](samples-notebooks.md)


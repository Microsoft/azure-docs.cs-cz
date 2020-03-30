---
title: Aktivace spuštění kanálu ML z aplikace logiky
titleSuffix: Azure Machine Learning
description: Zjistěte, jak spustit kanál ML pomocí Azure Logic Apps.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122854"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Aktivace spuštění kanálu Machine Learning z aplikace logiky

Aktivujte spuštění kanálu Azure Machine Learning Pipeline, když se objeví nová data. Můžete například použít kanál pro trénování nového modelu, když se v účtu úložiště objektů blob zobrazí nová data. Nastavte aktivační událost pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete [v tématu Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

* Koncový bod REST pro publikovaný kanál machine learningu. [Vytvořte a publikujte kanál](how-to-create-your-first-pipeline.md). Pak vyhledejte koncový bod REST publikovaného kanálu pomocí ID kanálu:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Úložiště objektů blob Azure](../storage/blobs/storage-blobs-overview.md) pro ukládání dat.
* [Úložiště dat](how-to-access-data.md) ve vašem pracovním prostoru, které obsahuje podrobnosti o účtu úložiště objektů blob.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky

Teď vytvořte instanci [aplikace Azure Logic App.](../logic-apps/logic-apps-overview.md) Pokud chcete, [použijte prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) a [nastavte klíč spravovaný zákazníkem](../logic-apps/customer-managed-keys-integration-service-environment.md) pro použití v aplikaci logiky.

Po zřízení aplikace logiky nakonfigurujte aktivační událost pro váš kanál takto:

1. [Vytvořte systémem přiřazenou spravovanou identitu,](../logic-apps/create-managed-service-identity.md) která aplikaci poskytne přístup k vašemu pracovnímu prostoru Azure Machine Learning Workspace.

1. Přejděte do zobrazení Návrháře aplikace logiky a vyberte šablonu Blank Logic App. 
    > [!div class="mx-imgBorder"]
    > ![Prázdná šablona](media/how-to-trigger-published-pipeline/blank-template.png)

1. V Návrháři vyhledejte **objekt blob**. Vyberte při přidání **nebo změně objektu blob (pouze vlastnosti)** aktivační událost a přidejte tuto aktivační událost do aplikace logiky.
    > [!div class="mx-imgBorder"]
    > ![Přidání triggeru](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Vyplňte informace o připojení pro účet úložiště objektů Blob, který chcete sledovat pro přidání nebo úpravy objektů blob. Vyberte kontejner, který chcete sledovat. 
 
    Zvolte **interval** a **frekvence,** chcete-li dotazovat na aktualizace, které vám budou fungovat.  

    > [!NOTE]
    > Tato aktivační událost bude sledovat vybraný kontejner, ale nebude monitorovat podsložky.

1. Přidejte akci HTTP, která se spustí při zjištění nového nebo upraveného objektu blob. Vyberte **+ Nový krok**, pak vyhledejte a vyberte akci HTTP.

  > [!div class="mx-imgBorder"]
  > ![Hledat akci HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Ke konfiguraci akce použijte následující nastavení:

  | Nastavení | Hodnota | 
  |---|---|
  | Akce HTTP | POST |
  | Identifikátor URI |koncový bod publikovaného kanálu, který jste našli jako [předpoklad](#prerequisites) |
  | Režim ověřování | Spravovaná identita |

1. Nastavte plán pro nastavení hodnoty všech [parametrů pipelineské datové cesty,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) které můžete mít:

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

    Použijte `DataStoreName` přidaný do pracovního prostoru jako [předpoklad](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Nastavení protokolu HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. Vyberte **Uložit** a váš plán je nyní připraven.
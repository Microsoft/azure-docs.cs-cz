---
title: Schéma události Azure Event Grid Machine Learning
description: Popisuje vlastnosti, které jsou k dispozici pro pracovní prostor Machine Learning události s Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 5f2d23b3fe33691d37dc00b2d4e79036293252d9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132873"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure Event Grid schéma událostí pro Azure Machine Learning

Tento článek poskytuje informace o vlastnostech a schématu pro události pracovního prostoru Machine Learning. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

Seznam ukázkových skriptů a kurzů najdete v tématu o [zdroji událostí AzureML](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Dostupné typy událostí

Azure Machine Learning emituje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Vyvolá se v případě, že byl úspěšně registrován nový model nebo verze modelu. |
| Microsoft. MachineLearningServices. ModelDeployed | Vyvolá se při úspěšném nasazení modelů do koncového bodu. |
| Microsoft. MachineLearningServices. RunCompleted | Vyvolá se při úspěšném dokončení běhu. |
| Microsoft. MachineLearningServices. DatasetDriftDetected | Vyvolá se v případě, že dojde ke sledování posunu datové sady. |

## <a name="the-contents-of-an-event-response"></a>Obsah odpovědi na událost

Když se aktivuje událost, Služba Event Grid odešle data o této události do předplatného koncového bodu.

V této části najdete příklad toho, jak by tato data vypadala jako u každé události.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Událost Microsoft. MachineLearningServices. ModelRegistered

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Událost Microsoft. MachineLearningServices. ModelDeployed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Událost Microsoft. MachineLearningServices. RunCompleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Událost Microsoft. MachineLearningServices. DatasetDriftDetected

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | řetězec | Cesta definovaná vydavatelem k předmětu události |
| eventType | řetězec | Jeden z registrovaných typů událostí pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data události služby Blob Storage. |
| dataVersion | řetězec | Verze schématu datového objektu. Vydavatel definuje verzi schématu. |
| metadataVersion | řetězec | Verze schématu metadat události. Event Grid definuje schéma vlastností nejvyšší úrovně. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má pro každý typ události následující vlastnosti:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| ModelName | řetězec | Název modelu, který byl zaregistrován. |
| ModelVersion | int | Verze modelu, který byl zaregistrován. |
| ModelTags | objekt | Značky modelu, který byl zaregistrován. |
| ModelProperties | objekt | Vlastnosti modelu, který byl zaregistrován. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. ModelDeployed

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| ServiceName | řetězec | Název nasazené služby. |
| ServiceComputeType | řetězec | Výpočetní typ (např. ACI, AKS) nasazené služby. |
  | ModelIds | řetězec | Čárkami oddělený seznam ID modelů. ID modelů nasazených ve službě. |
| ServiceTags | objekt | Značky nasazené služby. |
| ServiceProperties | objekt | Vlastnosti nasazené služby. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| ExperimentId | řetězec | ID experimentu, do kterého daný běh patří. |
| Experiment | řetězec | Název experimentu, do kterého daný běh patří. |
| RunId | řetězec | ID běhu, které bylo dokončeno. |
| RunType | řetězec | Typ spuštění dokončeného běhu. |
| RunTags | objekt | Značky dokončeného spuštění. |
| RunProperties | objekt | Vlastnosti dokončeného běhu |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. DatasetDriftDetected

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| DataDriftId | řetězec | ID monitoru pro posunování dat, který spustil událost |
| Datatenatových | řetězec | Název monitoru pro posun dat, který spustil událost. |
| RunId | řetězec | ID spuštění, které zjistilo posun dat |
| BaseDatasetId | řetězec | ID základní datové sady použité k detekci posunu |
| TargetDatasetId | řetězec | ID cílové sady dat použité ke zjištění posunu |
| DriftCoefficient | double | Koeficient – výsledek, který spustil událost |
| StartTime | datetime | Čas zahájení časové řady cílové sady dat, která vedla k detekci posunu.  |
| EndTime | datetime | Koncový čas časové řady cílové sady, která vedla k detekci posunu. |


## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md) .
* Úvod k použití Azure Event Grid s Azure Machine Learning najdete v tématu věnovaném zpracování [Azure Machine Learningch událostí](/azure/machine-learning/service/concept-event-grid-integration) .
* Příklad použití Azure Event Grid s Azure Machine Learning najdete v tématu [vytváření pracovních postupů pro Machine Learning řízených událostmi](/azure/machine-learning/service/how-to-use-event-grid) .

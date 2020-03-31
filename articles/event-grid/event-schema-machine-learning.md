---
title: Schéma událostí Azure Event Grid Machine Learning
description: Popisuje vlastnosti, které jsou k dispozici pro události pracovního prostoru machine learningu s Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 4051598a9abd787f6707e67a8c4dab12fc6d626a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79202140"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Schéma událostí Azure Event Grid pro Azure Machine Learning

Tento článek obsahuje vlastnosti a schéma pro události pracovního prostoru strojového učení. Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md).

Seznam ukázkových skriptů a kurzů najdete v tématu [Zdroj událostí AzureML](event-sources.md#azure-machine-learning).

## <a name="available-event-types"></a>Dostupné typy událostí

Azure Machine Learning vydává následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.MachineLearningServices.ModelRegistered | Je aktivována, když byl úspěšně zaregistrován nový model nebo verze modelu. |
| Microsoft.MachineLearningServices.ModelNasypaně | Aktivováno, když model (y) byly úspěšně nasazeny do koncového bodu. |
| Microsoft.MachineLearningServices.RunCompleted | Vyvolána po úspěšném dokončení spuštění. |
| Microsoft.MachineLearningServices.DatasetDriftDetected | Je aktivována, když sledování driftu datové sady detekuje drift. |
| Microsoft.MachineLearningServices.RunStatusZměněn | Je aktivována při změně stavu spuštění na "se nezdařilo". |

## <a name="the-contents-of-an-event-response"></a>Obsah odpovědi na událost

Když je událost spuštěna, služba Event Grid odešle data o této události do koncového bodu přihlášení.

Tato část obsahuje příklad toho, jak by tato data vypadala pro každou událost.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>Událost Microsoft.MachineLearningServices.ModelRegistrovaná událost

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

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>Událost Microsoft.MachineLearningServices.ModelDeployed

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

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>Událost Microsoft.MachineLearningServices.RunCompleted

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

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>Událost Microsoft.MachineLearningServices.DatasetDriftDetected

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

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Událost Microsoft.MachineLearningServices.RunStatusChanged

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
    },
   "RunStatus": "failed"
   },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```




## <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí úložiště objektů blob. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má pro každý typ události následující vlastnosti:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft.MachineLearningServices.ModelRegistered

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| ModelName | řetězec | Název modelu, který byl registrován. |
| ModelVersion | řetězec | Verze modelu, který byl registrován. |
| Značky modelů | objekt | Značky modelu, který byl registrován. |
| Vlastnosti modelu | objekt | Vlastnosti modelu, který byl registrován. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft.MachineLearningServices.ModelNasypaně

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| ServiceName | řetězec | Název nasazené služby. |
| ServiceComputeType | řetězec | Typ výpočetní ho (např. aci, AKS) nasazené služby. |
  | ModelIds | řetězec | Seznam ID modelu oddělený chod čárkami. ID modelů nasazených ve službě. |
| Značky služeb | objekt | Značky nasazené služby. |
| Vlastnosti služby | objekt | Vlastnosti nasazené služby. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft.MachineLearningServices.RunCompleted

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Id experimentu | řetězec | ID experimentu, který patří spustit. |
| Název experimentu | řetězec | Název experimentu, ke kterému patří spuštění. |
| RunId | řetězec | ID spuštění, který byl dokončen. |
| Typ běhu | řetězec | Typ spuštění dokončeného spuštění. |
| RunTags | objekt | Značky dokončeného spuštění. |
| RunProperties | objekt | Vlastnosti dokončené Run. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft.MachineLearningServices.DatasetDriftDetected

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| DataDriftId | řetězec | ID sledování posunu dat, který spustil událost. |
| Název DataDriftName | řetězec | Název sledování posunu dat, který událost spustil. |
| RunId | řetězec | ID spustit, který zjistil posun dat. |
| BaseDatasetId | řetězec | ID základní datové sady použité k detekci posunu. |
| TargetDatasetId | řetězec | ID cílové datové sady použité k detekci driftu. |
| Driftkoeficient | double | Výsledek koeficientu, který spustil událost. |
| StartTime | datetime | Čas zahájení cílové časové řady datové sady, která vedla k detekci driftu.  |
| EndTime | datetime | Čas ukončení časové řady cílové datové sady, která vedla k detekci driftu. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft.MachineLearningServices.RunStatusZměněn

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Id experimentu | řetězec | ID experimentu, který patří spustit. |
| Název experimentu | řetězec | Název experimentu, ke kterému patří spuštění. |
| RunId | řetězec | ID spuštění, který byl dokončen. |
| Typ běhu | řetězec | Typ spuštění dokončeného spuštění. |
| RunTags | objekt | Značky dokončeného spuštění. |
| RunProperties | objekt | Vlastnosti dokončené Run. |
| RunStatus | řetězec | Stav spustit. |

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v tématu [Schéma předplatného Služby událostí](subscription-creation-schema.md)
* Úvod k používání Azure Event Grid s Azure Machine Learning najdete v [tématu Využití událostí Azure Machine Learning](/azure/machine-learning/service/concept-event-grid-integration)
* Příklad použití Azure Event Grid s Azure Machine Learning najdete v tématu [Vytvoření pracovních postupů strojového učení založeného na událostech.](/azure/machine-learning/service/how-to-use-event-grid)

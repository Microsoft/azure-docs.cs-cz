---
title: Azure Machine Learning jako zdroj Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro pracovní prostor Machine Learning události s Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: f77a76d6acb67c739e0adf186d23e9b16ff7e2ee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82928869"
---
# <a name="azure-machine-learning-as-an-event-grid-source"></a>Azure Machine Learning jako zdroj Event Grid

Tento článek poskytuje informace o vlastnostech a schématu pro události pracovního prostoru Machine Learning. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

### <a name="available-event-types"></a>Dostupné typy událostí

Azure Machine Learning emituje následující typy událostí:

| Typ události | Description |
| ---------- | ----------- |
| Microsoft. MachineLearningServices. ModelRegistered | Vyvolá se v případě, že byl úspěšně registrován nový model nebo verze modelu. |
| Microsoft. MachineLearningServices. ModelDeployed | Vyvolá se při úspěšném nasazení modelů do koncového bodu. |
| Microsoft. MachineLearningServices. RunCompleted | Vyvolá se při úspěšném dokončení běhu. |
| Microsoft. MachineLearningServices. DatasetDriftDetected | Vyvolá se v případě, že dojde ke sledování posunu datové sady. |
| Microsoft. MachineLearningServices. RunStatusChanged | Vyvolá se při změně stavu spuštění na Failed. |

### <a name="the-contents-of-an-event-response"></a>Obsah odpovědi na událost

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

### <a name="microsoftmachinelearningservicesrunstatuschanged-event"></a>Událost Microsoft. MachineLearningServices. RunStatusChanged

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

### <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| závislosti | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Typ | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | odkazy objektů | Data události služby Blob Storage. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má pro každý typ události následující vlastnosti:

### <a name="microsoftmachinelearningservicesmodelregistered"></a>Microsoft. MachineLearningServices. ModelRegistered

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| ModelName | řetězec | Název modelu, který byl zaregistrován. |
| ModelVersion | řetězec | Verze modelu, který byl zaregistrován. |
| ModelTags | odkazy objektů | Značky modelu, který byl zaregistrován. |
| ModelProperties | odkazy objektů | Vlastnosti modelu, který byl zaregistrován. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>Microsoft. MachineLearningServices. ModelDeployed

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| ServiceName | řetězec | Název nasazené služby. |
| ServiceComputeType | řetězec | Výpočetní typ (např. ACI, AKS) nasazené služby. |
  | ModelIds | řetězec | Čárkami oddělený seznam ID modelů. ID modelů nasazených ve službě. |
| ServiceTags | odkazy objektů | Značky nasazené služby. |
| ServiceProperties | odkazy objektů | Vlastnosti nasazené služby. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>Microsoft. MachineLearningServices. RunCompleted

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| ExperimentId | řetězec | ID experimentu, do kterého daný běh patří. |
| Experiment | řetězec | Název experimentu, do kterého daný běh patří. |
| RunId | řetězec | ID běhu, které bylo dokončeno. |
| RunType | řetězec | Typ spuštění dokončeného běhu. |
| RunTags | odkazy objektů | Značky dokončeného spuštění. |
| RunProperties | odkazy objektů | Vlastnosti dokončeného běhu |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>Microsoft. MachineLearningServices. DatasetDriftDetected

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| DataDriftId | řetězec | ID monitoru pro posunování dat, který spustil událost |
| Datatenatových | řetězec | Název monitoru pro posun dat, který spustil událost. |
| RunId | řetězec | ID spuštění, které zjistilo posun dat |
| BaseDatasetId | řetězec | ID základní datové sady použité k detekci posunu |
| TargetDatasetId | řetězec | ID cílové sady dat použité ke zjištění posunu |
| DriftCoefficient | double | Koeficient – výsledek, který spustil událost |
| StartTime | datetime | Čas zahájení časové řady cílové sady dat, která vedla k detekci posunu.  |
| EndTime | datetime | Koncový čas časové řady cílové sady, která vedla k detekci posunu. |

### <a name="microsoftmachinelearningservicesrunstatuschanged"></a>Microsoft. MachineLearningServices. RunStatusChanged

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| ExperimentId | řetězec | ID experimentu, do kterého daný běh patří. |
| Experiment | řetězec | Název experimentu, do kterého daný běh patří. |
| RunId | řetězec | ID běhu, které bylo dokončeno. |
| RunType | řetězec | Typ spuštění dokončeného běhu. |
| RunTags | odkazy objektů | Značky dokončeného spuštění. |
| RunProperties | odkazy objektů | Vlastnosti dokončeného běhu |
| RunStatus | řetězec | Stav spuštění. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
| Nadpis | Popis |
| ----- | ----- |
| [Využívání Azure Machine Learningch událostí](../machine-learning/concept-event-grid-integration.md) | Přehled Integrace Azure Machine Learning s Event Grid |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md) .
* Úvod k použití Azure Event Grid s Azure Machine Learning najdete v tématu věnovaném zpracování [Azure Machine Learningch událostí](/azure/machine-learning/concept-event-grid-integration) .
* Příklad použití Azure Event Grid s Azure Machine Learning najdete v tématu [vytváření pracovních postupů pro Machine Learning řízených událostmi](/azure/machine-learning/how-to-use-event-grid) .

---
title: Publikování kanálů na ML
titleSuffix: Azure Machine Learning
description: Spouštění pracovních postupů strojového učení s kanály strojového učení a Azure Machine Learning SDK pro Python
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 08529d1bb50a1a5d5b3c7d0296aa36f021f45e98
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646081"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publikování a sledování kanálů strojového učení

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak sdílet kanál strojového učení se svými kolegy nebo zákazníky.

Kanály strojového učení jsou opakovaně použitelnými pracovními postupy pro úlohy strojového učení. Jedna výhoda kanálů zvyšuje spolupráci. Můžete také vytvářet verze kanálů a umožnit tak zákazníkům používat aktuální model při práci na nové verzi. 

## <a name="prerequisites"></a>Předpoklady

* Vytvoření [pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md) pro uložení všech prostředků kanálu

* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [výpočetní instanci Azure Machine Learning](concept-compute-instance.md) s již nainstalovanou sadou SDK.

* Vytvořte a spusťte kanál strojového učení, například pomocí následujícího [kurzu: sestavení kanálu Azure Machine Learning pro vyhodnocování dávky](tutorial-pipeline-batch-scoring-classification.md). Další možnosti najdete v tématu [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](how-to-create-your-first-pipeline.md) .

## <a name="publish-a-pipeline"></a>Publikování kanálu

Jakmile budete mít kanál v provozu, můžete publikovat kanál, aby běžel s různými vstupy. Pro koncový bod REST již publikovaného kanálu pro příjem parametrů je nutné nakonfigurovat kanál tak, aby používal `PipelineParameter` objekty pro argumenty, které se budou lišit.

1. Chcete-li vytvořit parametr kanálu, použijte objekt [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) s výchozí hodnotou.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Tento `PipelineParameter` objekt přidejte jako parametr do některého z kroků v kanálu následujícím způsobem:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publikování tohoto kanálu, který při vyvolání přijme parametr.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Spuštění publikovaného kanálu

Všechny publikované kanály mají koncový bod REST. Pomocí koncového bodu kanálu můžete aktivovat spuštění kanálu z libovolného externího systému, včetně klientů mimo Python. Tento koncový bod povoluje ve scénářích dávkového vyhodnocování a přeškolení možnost spravovaná opakovatelnost.

K vyvolání běhu předchozího kanálu potřebujete Azure Active Directory token hlavičky ověřování. Získání takového tokenu je popsané v referenčních informacích [třídy AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py&preserve-view=true) a v tématu [ověřování v](https://aka.ms/pl-restep-auth) poznámkovém bloku Azure Machine Learning.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Vytvoření koncového bodu kanálu se správou verzí

Koncový bod kanálu můžete vytvořit s několika publikovanými kanály. Získáte tak pevný koncový bod REST při iteraci a aktualizujete kanály ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Odeslání úlohy do koncového bodu kanálu

Úlohu můžete odeslat do výchozí verze koncového bodu kanálu:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Můžete také odeslat úlohu do konkrétní verze:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Totéž můžete dosáhnout pomocí REST API:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Použití publikovaných kanálů v studiu

Můžete také spustit publikovaný kanál z studia:

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. [Zobrazení pracovního prostoru](how-to-manage-workspace.md#view)

1. Na levé straně vyberte **koncové body**.

1. V horní části vyberte **koncové body kanálu**.
 ![Seznam publikovaných kanálů ve strojovém učení](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Vyberte konkrétní kanál, který se má spustit, spotřebovat nebo zkontrolovat výsledky předchozích spuštění koncového bodu kanálu.

## <a name="disable-a-published-pipeline"></a>Zakázání publikovaného kanálu

Pokud chcete kanál ze seznamu publikovaných kanálů skrýt, můžete ho zakázat v sadě Studio nebo v sadě SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Můžete ho znovu povolit pomocí `p.enable()` . Další informace naleznete v tématu [PublishedPipeline Class Reference třídy](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py&preserve-view=true) .

## <a name="next-steps"></a>Další kroky

- Pomocí [těchto poznámkových bloků Jupyter na GitHubu](https://aka.ms/aml-pipeline-readme) můžete dále prozkoumat kanály strojového učení.
- Podívejte se na referenční nápovědu sady SDK pro balíček [AzureML-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) a balíček [AzureML-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) .
- Tipy k ladění kanálů a řešení potíží najdete v tématu [postupy](how-to-debug-pipelines.md) .

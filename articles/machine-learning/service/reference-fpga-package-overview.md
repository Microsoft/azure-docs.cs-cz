---
title: Balíček FPGA pro hardwarovou akceleraci pro Azure Machine Learning
description: Další informace o balíčcích python, která je k dispozici pro uživatele Azure Machine Learning.
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: routlaw
author: rloutlaw
ms.date: 05/07/2018
ms.openlocfilehash: 905f6943470acfd3051a33db6f6f3269470406d7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Balíček Azure Machine Learning hardwarovou akceleraci

Balíček Azure Machine Learning hardwarovou akceleraci je Python pip instalovat rozšíření pro Azure Machine Learning, která umožňuje rychle datových vědců a vývojářů AI na:

+ Obrázky Featurize quantized verzi ResNet 50

+ Cvičení třídění na základě těchto funkcí

+ Nasazení modely, které [pole programovatelný brány pole (FPGA)](concept-accelerate-with-fpgas.md) v Azure pro inferencing s velmi nízkou latencí

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Musíte vytvořit účet Azure Machine Learning modelu správy. Další informace o vytvoření účtu najdete v tématu [Azure Machine Learning Quickstart a Workbench instalace](../service/quickstart-installation.md) dokumentu. 

1. Balíček musí být nainstalován. 

 
## <a name="how-to-install-the-package"></a>Postup instalace balíčku

1. Stáhněte a nainstalujte nejnovější verzi [Git](https://git-scm.com/downloads).

2. Nainstalujte [Anaconda (Python 3.6)](https://conda.io/miniconda.html)

3. Ke stažení předem nakonfigurovaná Anaconda prostředí, použijte následující příkaz z příkazového řádku Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
5. Chcete-li vytvořit prostředí, otevřete **Anaconda výzva** a použijte následující příkaz:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

6. Pokud chcete aktivovat prostředí, použijte následující příkaz:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Ukázka kódu

Tento ukázkový kód provede instalaci modelu FPGA pomocí sady SDK.

1. Import balíčku:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Předběžně zpracovat bitovou kopii:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Featurize bitové kopie:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Vytvoření klasifikátoru:
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. Vytvořte definici služby:
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. Příprava modelu ke spuštění na FPGA:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Nasazení modelu na FPGA spouštět:
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. Vytvoření klienta:
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. Volání rozhraní API:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Hlášení problémů

Použití [fórum](https://aka.ms/aml-forum) nahlaste všechny problémy setkáte s tímto balíčkem.

## <a name="next-steps"></a>Další postup

[Nasadit model jako webovou službu na FPGA](how-to-deploy-fpga-web-service.md)
---
title: Balíček FPGA pro hardwarovou akceleraci pro Azure Machine Learning
description: Další informace o balíčcích pythonu, který je k dispozici pro uživatele Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 83c5a788f85fcc47c221f5c8f9e6944c4448fbf9
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712122"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Azure Machine Learning hardwarovou akceleraci balíčku

>[!Note]
>**Tento článek je zastaralý.** Tento balíček FPGA byla zrušena. Přidala se podpora pro tuto funkci na sadu SDK Azure ML. Podpora pro tento balíček končí postupně. [Zobrazení časové osy podpory](overview-what-happened-to-workbench.md#timeline). Další informace o aktualizaci [podporu FPGA](concept-accelerate-with-fpgas.md).

Balíček Azure Machine Learning hardwarovou akceleraci je Python pip instalovat rozšíření pro službu Azure Machine Learning, která umožňuje odborníkům přes data a vývojářům AI rychle:

+ Zpracování volných imagí kvantizované verzi modelem ResNet 50

+ Třídění trénovat na základě modelu na základě těchto funkcí

+ Nasadit modely, které [pole programmable gate Array (FPGA)](concept-accelerate-with-fpgas.md) v Azure pro odvozování mimořádně nízkou latencí

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLfree) před tím, než začnete.

1. Účet správy modelů Azure Machine Learning. Další informace o vytvoření účtu najdete v tématu [Quickstart pro Azure Machine Learning a Workbench instalace](../desktop-workbench/quickstart-installation.md) dokumentu. 

1. Balíček se musí nainstalovat. 

 
## <a name="how-to-install-the-package"></a>Postup instalace balíčku

1. Stáhněte a nainstalujte nejnovější verzi [Git](https://git-scm.com/downloads).

2. Nainstalujte [Anaconda (Python 3.6)](https://conda.io/miniconda.html)

   Ke stažení předem nakonfigurované prostředí Anaconda, použijte následující příkaz z příkazového řádku Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
1. Chcete-li vytvořit prostředí, otevřete **Anaconda výzvy** a použijte následující příkaz:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

1. Pokud chcete aktivovat prostředí, použijte následující příkaz:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Ukázka kódu

Tento ukázkový kód provede nasazení modelu pro FPGA pomocí sady SDK.

1. Importujte balíčku:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Předběžné zpracování obrazu:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Zpracování volných imagí:
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

1. Vytvoření definice služby:
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
 
1. Příprava modelu, který má spustit na FPGA:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Nasazení modelu, který má spustit na FPGA:
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

## <a name="reporting-issues"></a>Hlášení problémů s

Použití [fórum](https://aka.ms/aml-forum-service) nahlaste všechny problémy setkáte s balíčkem.

## <a name="next-steps"></a>Další postup

[Nasazení modelu jako webové služby na FPGA](how-to-deploy-fpga-web-service.md)
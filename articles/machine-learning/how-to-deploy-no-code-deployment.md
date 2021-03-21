---
title: Žádné nasazení kódu (Preview)
titleSuffix: Azure Machine Learning
description: Žádné nasazení kódu vám neumožňuje nasadit model jako webovou službu, aniž by bylo nutné ručně vytvořit skript pro zadávání.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: a17126695aa5138d1df7fd17cfaa2f5f75ad1004
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324918"
---
# <a name="preview-no-code-model-deployment"></a>Tisk Nasazení modelu bez kódu

Nasazení modelu bez kódu je momentálně ve verzi Preview a podporuje následující architektury strojového učení:

## <a name="tensorflow-savedmodel-format"></a>Formát TensorFlow SavedModel
Modely TensorFlow je nutné zaregistrovat ve **formátu SavedModel** pro práci s nasazením modelu bez kódu.

V [tomto odkazu](https://www.tensorflow.org/guide/saved_model) najdete informace o tom, jak vytvořit SavedModel.

Podporujeme všechny verze TensorFlow, které jsou uvedeny v části "značky" na [TensorFlow obsluhující dockerhubu](https://registry.hub.docker.com/r/tensorflow/serving/tags).

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>Modely ONNX

Registrace a nasazení modelu ONNX se podporuje pro libovolný graf odvození ONNX. Kroky předběžného zpracování a postprocess se v tuto chvíli nepodporují.

Tady je příklad, jak zaregistrovat a nasadit model ONNX MNIST ručně zapsaných:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Chcete-li určit skóre modelu, přečtěte si téma věnované [využívání modelu Azure Machine Learning nasazeného jako webové služby](./how-to-consume-web-service.md). Mnoho projektů ONNX používá soubory protobuf k komprimaci dat o školeních a ověřováních, což může být obtížné zjistit, co formát dat očekávala služba. Jako vývojář modelů byste měli dokument pro vaše vývojáře:

* Vstupní formát (JSON nebo binární)
* Vstupní datový tvar a typ (například pole plovoucích tvarů [100100, 3])
* Informace o doméně (například pro obrázek, barevný prostor, pořadí součástí a zda jsou hodnoty normalizovány)

Pokud používáte Pytorch, jsou [exportovány modely z Pytorch na ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) v podrobnostech o převodu a omezeních. 

## <a name="scikit-learn-models"></a>Modely Scikit – informace

Pro všechny integrované typy modelů scikit-učí není podporováno žádné nasazení modelu kódu.

Tady je příklad, jak zaregistrovat a nasadit model skriptu sklearn bez dalšího kódu:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> Modely, které podporují predict_proba použijí tuto metodu ve výchozím nastavení. Pokud chcete tuto možnost přepsat, abyste mohli použít předpověď, můžete tělo příspěvku upravit následujícím způsobem:

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> Tyto závislosti jsou zahrnuté v předem sestaveném kontejneru pro odvození scikit:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>Další kroky

* [Řešení potíží s neúspěšným nasazením](how-to-troubleshoot-deployment.md)
* [Nasazení do Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Vytváření klientských aplikací pro využívání webových služeb](how-to-consume-web-service.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Vytváření výstrah a triggerů událostí pro nasazení modelů](how-to-use-event-grid.md)
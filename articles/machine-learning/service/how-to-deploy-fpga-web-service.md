---
title: Nasazovat modely na FPGA
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak nasadit webovou službu s modelem FPGA službou Azure Machine Learning pro odvozování mimořádně nízkou latenci a systémem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 1/29/2019
ms.custom: seodec18
ms.openlocfilehash: d7403d4f791151b167f03ee79c30623bbd644008
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100674"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Nasazení modelu jako webové služby na FPGA s využitím služby Azure Machine Learning

Nasazení modelu jako webové služby na [pole programmable gate Array (FPGA)](concept-accelerate-with-fpgas.md).  Použití FPGA poskytuje mimořádně nízkou latenci odvozování, dokonce i s velikostí jedné dávce.  Tyto modely jsou aktuálně k dispozici:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16   

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

- Pracovní prostor služby Azure Machine Learning service a Azure Machine Learning SDK for nainstalovaný Python. Další informace o získání těchto nezbytných podmínkách používání [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.
 
  - Musí být v pracovním prostoru *USA – východ 2* oblasti.

  - Instalace funkce contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```

  - Aktuálně pouze tensorflow verze < = 1.10 je podporováno, proto ji můžete nainstalovat po dokončení další instalace:

    ```shell
    pip install "tensorflow==1.10"
    ```

### <a name="get-the-notebook"></a>Získání poznámkového bloku

Pro usnadnění práce je tento kurz k dispozici jako poznámkový blok Jupyter. Postupujte podle zde nebo spustit kód [Poznámkový blok rychlý Start](https://github.com/Azure/aml-real-time-ai/blob/master/notebooks/project-brainwave-quickstart.ipynb).

## <a name="create-and-deploy-your-model"></a>Vytvoření a nasazení modelu
Vytvoření kanálu pro předzpracování vstupního obrázku vytrénovaných pomocí modelem ResNet 50 na FPGA a pak spusťte funkce prostřednictvím třídění školení na datové sadě ImageNet.

Postupujte podle pokynů:

* Definovat modelu kanálu
* Nasazení modelu
* Používání nasazeného modelu
* Odstranění nasazené služby

> [!IMPORTANT]
> Pokud chcete optimalizovat latenci a propustnost, musí být váš klient ve stejné oblasti Azure jako koncový bod.  Rozhraní API jsou aktuálně vytvořené v oblasti Azure USA – východ.



### <a name="preprocess-image"></a>Předběžné zpracování obrazu
První fáze kanálu je předběžné zpracování imagí.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Přidat Featurizer
Inicializovat model a stáhněte si kontrolní bod TensorFlow kvantizované verze ResNet50 má být použit jako featurizer.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Přidat třídění
Na datové sadě ImageNet byla vyškolila tento třídění.

```python
classifier_output = model.get_default_classifier(feature_tensor)
```

### <a name="create-service-definition"></a>Vytvoření definice služby
Teď, když jste definovali předběžného zpracování obrazu, featurizer a třídění, na kterém běží ve službě, můžete vytvořit definice služby. Definice služby je sada soubory vygenerované z modelu, který je nasazený do FPGA služby. Definice služby se skládá z kanálu. Kanál je několika fází, které jsou spouštěny v pořadí.  Fáze TensorFlow, Keras fáze a fáze BrainWave jsou podporovány.  Fáze jsou spuštěny v pořadí na službu s výstupem každou z fází stávají vstup do další fáze.

Chcete-li vytvořit fázi TensorFlow, zadejte relaci obsahující grafu (v tomto případě se používá výchozí graf) a vstupní a výstupní tensors do této fáze.  Tyto informace slouží k uložení grafu tak, aby ji můžete spustit ve službě.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'model_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, feature_tensor, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Nasazení modelu
Vytvoření služby z definice služby.  Váš pracovní prostor musí být v umístění východní USA 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(True)
```

### <a name="test-the-service"></a>Testování služby
Odeslat image do rozhraní API a otestovat odpovědi, přidejte mapování z výstupu ID třídy ImageNet název třídy.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Volají službu a název souboru "your image.jpg" níže nahraďte obrázek z vašeho počítače. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Vyčištění služby
Odstraňte službu.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Zabezpečení webových služeb, FPGA

Informace o zabezpečení FPGA webové služby, najdete v článku [zabezpečení webových služeb](how-to-secure-web-service.md) dokumentu.


## <a name="next-steps"></a>Další postup

Zjistěte, jak [využívání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md).

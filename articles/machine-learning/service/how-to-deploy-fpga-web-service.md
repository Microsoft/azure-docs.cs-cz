---
title: Nasazení modelu jako webové služby na FPGA službou Azure Machine Learning
description: Zjistěte, jak nasadit webovou službu pomocí modelu běží na FPGA službou Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 10/29/2018
ms.openlocfilehash: e46b2c0de1b65d2355e7258e9940ee1fee6be071
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872131"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Nasazení modelu jako webové služby na FPGA službou Azure Machine Learning

Nasazení modelu jako webové služby na [pole programmable gate Array (FPGA)](concept-accelerate-with-fpgas.md).  Použití FPGA poskytuje mimořádně nízkou latenci odvozování, dokonce i s velikostí jedné dávce.   

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://aka.ms/AMLfree).

- Pracovní prostor služby Azure Machine Learning service a Azure Machine Learning SDK for nainstalovaný Python. Další informace o získání těchto nezbytných podmínkách používání [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.
 
  - Musí být v pracovním prostoru *USA – východ 2* oblasti.

  - Instalace funkce contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Vytvoření a nasazení modelu
Vytvoření kanálu pro předzpracování vstupního obrázku vytrénovaných pomocí modelem ResNet 50 na FPGA a pak spusťte funkce prostřednictvím classifer školení na datové sadě ImageNet.

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
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
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
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Vytvoření definice služby
Teď, když máte definované předběžného zpracování obrazu, featurizer a třídění, na kterém běží ve službě, můžete vytvořit definice služby. Definice služby je sada soubory vygenerované z modelu, který je nasazený do FPGA služby. Definice služby se skládá z kanálu. Kanál je několika fází, které jsou spouštěny v pořadí.  Fáze TensorFlow, Keras fáze a fáze BrainWave jsou podporovány.  Fáze jsou spuštěny v pořadí na službu s výstupem každé fáze vstup do další fáze.

Chcete-li vytvořit fázi TensorFlow, zadejte relaci obsahující grafu (v tomto případě se používá výchozí graf) a vstupní a výstupní tensors do této fáze.  Tyto informace slouží k uložení grafu tak, aby ji můžete spustit ve službě.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
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
    service.wait_for_deployment(true)
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

Modely Azure Machine Learning, běží na FPGA poskytovat podpora protokolu SSL a ověřování na základě klíče. To umožňuje omezit přístup k vaší služby a zabezpečit data odeslaná klienty. [Zjistěte, jak zabezpečit webovou službu](how-to-secure-web-service.md).


## <a name="next-steps"></a>Další postup

Zjistěte, jak [využívání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md).

---
title: Nasazovat modely na FPGA
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak nasadit webovou službu s modelem FPGA službou Azure Machine Learning pro odvozování mimořádně nízkou latenci a systémem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 249a21bf9eeb3913826971fd1aae136197d264c4
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149614"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Nasazení modelu jako webové služby na FPGA s využitím služby Azure Machine Learning

Nasazení modelu jako webové služby na [pole programmable gate Array (FPGA)](concept-accelerate-with-fpgas.md) s Azure Machine Learning Hardware Accelerated modely. Použití FPGA poskytuje mimořádně nízkou latenci odvozování, dokonce i s velikostí jedné dávce.

Tyto modely jsou aktuálně k dispozici:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA jsou k dispozici v těchto oblastech Azure:
  - USA – východ
  - Jihovýchodní Asie
  - Západní Evropa
  - Západní USA 2

> [!IMPORTANT]
> Optimalizovat latenci a propustnost, by měl být klientu odesílat data do modelu FPGA v oblastech výše (ten, který jste nasadili modelu, který má).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.  Pokud nemáte, vytvořte si bezplatný účet, než začnete. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

- FPGA kvóty.  Pomocí Azure CLI ke kontrole, jestli máte kvóty.
    ```shell
    az vm list-usage --location "eastus" -o table
    ```

    Jiné umístění jsou ``southeastasia``, ``westeurope``, a ``westus2``.

    Ve sloupci "Name" Vyhledat "Řada Standard PBS Vcpu" a ujistěte se, že máte alespoň 6 virtuálních procesorů v části "CurrentValue."

    Pokud nemáte kvótu, odešlete formulář žádosti o [tady](https://aka.ms/accelerateAI).

- Pracovní prostor služby Azure Machine Learning service a Azure Machine Learning SDK for nainstalovaný Python. Další informace o získání těchto nezbytných podmínkách používání [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.
 
- Python SDK pro hardwarově urychlené modely:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Ukázkové poznámkové bloky

Pro usnadnění práce [ukázkové poznámkové bloky](https://aka.ms/aml-accel-models-notebooks) jsou k dispozici pro následující příklad a další příklady.

## <a name="create-and-containerize-your-model"></a>Vytvoření a umístit svůj model

Tento dokument se popisují, jak vytvořit graf TensorFlow předběžné zpracování vstupního obrázku, nastavte ji featurizer pomocí modelem ResNet 50 na FPGA a spusťte funkce prostřednictvím třídění školení na datové sadě ImageNet.

Postupujte podle pokynů:

* Definovat TensorFlow model
* Převést modelu
* Nasazení modelu
* Používání nasazeného modelu
* Odstranění nasazené služby

### <a name="load-azure-ml-workspace"></a>Načíst pracovní prostor služby Azure ML

Načtení pracovního prostoru Azure ML.

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>Předběžné zpracování obrazu

Vstup do webové služby je ve formátu JPEG.  Prvním krokem je dekódování obrázku JPEG a předzpracování ho.  Obrázků JPEG, je zacházeno podle řetězce a výsledek se tensors, které mají být vstup do modelu modelem ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Načíst featurizer

Inicializovat model a stáhněte si kontrolní bod TensorFlow kvantizované verze ResNet50 má být použit jako featurizer.  Můžete nahradit "QuantizedResnet50" ve fragmentu kódu níže s importováním jiných hluboké neuronové sítě:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Přidat třídění

Na datové sadě ImageNet byla vyškolila tento třídění.  Příklady pro přenos učení a trénování přizpůsobené váhy jsou k dispozici v sadě [ukázkové poznámkové bloky](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Uložit model

Teď, když byly načteny preprocesoru, 50 modelem ResNet featurizer a třídění, uložte graf a přidružená proměnné jako model.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Uložení vstupních a výstupních tensors
Vstupní a výstupní tensors, které byly vytvořeny během předběžného zpracování a třídění kroky bude potřeba pro převod modelu a odvozování.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Uložit vstup a výstup tensors, protože je budete potřebovat pro převod a odvozování požadavků na modely.

Dostupné modely a odpovídající třídění výchozí výstupní tensors jsou níže, což je, co můžete využít při odvozování Pokud jste použili výchozí třídění.

+ Resnet50 QuantizedResnet50 ``
output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
``
+ Resnet152, QuantizedResnet152 ``
output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
``
+ Densenet121 QuantizedDensenet121 ``
output_tensors = "classifier/densenet121/predictions/Softmax:0"
``
+ Vgg16, QuantizedVgg16 ``
output_tensors = "classifier/vgg_16/fc8/squeezed:0"
``
+ SsdVgg, QuantizedSsdVgg ``
output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
``

### <a name="register-model"></a>Registrace modelu

[Zaregistrujte](./concept-model-management-and-deployment.md) model, který jste vytvořili.  Přidání značek a další metadata týkající se modelu, vám pomůže sledovat vaše trénované modely.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws,
                                  model_path = model_save_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

Pokud jste už zaregistrovaný modelu a chcete ho načíst, může ho načíst.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>Převést modelu

Převod na formát Open Neural Network Exchange TensorFlow grafu ([ONNX](https://onnx.ai/)).  Budete muset zadat názvy vstupních a výstupních tensors a tyto názvy se použije klient při používání této webové služby.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output = False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version, 
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Vytvoření image Dockeru

Převedený model a všechny závislosti jsou přidány do image Dockeru.  Tato image Dockeru je potom nasadit a vytvořit instance.  Podporované nasazení cíle zahrnují AKS v cloudu nebo hraničním zařízením, jako [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Můžete také přidat značky a popisy pro registrované image Dockeru.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)
image.wait_for_creation(show_output = False)
```

Výpis všech imagí podle značky a získání podrobných protokolů pro všechny ladění.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Nasazení modelu

### <a name="deploy-to-the-cloud"></a>Nasadit do cloudu

Nasazení modelu jako produkční vysoce škálovatelnou webovou službu, použijte Azure Kubernetes Service (AKS). Můžete vytvořit novou zásadu pomocí sady SDK Azure Machine Learning, rozhraní příkazového řádku nebo na webu Azure portal.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)
```

Nasazení služby AKS může trvat přibližně 15 minut.  Zaškrtněte, pokud chcete zobrazit, pokud nasazení bylo úspěšné.

```python
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Nasazení kontejneru do clusteru AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled = False)

aks_service_name ='my-aks-service'

aks_service = Webservice.deploy_from_image(workspace = ws,
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
```

#### <a name="test-the-cloud-service"></a>Testování cloudové služby
Image Dockeru podporuje gRPC a obsluhují TensorFlow "předpovědět" rozhraní API.  Pomocí klienta ukázkové volání image Dockeru, chcete-li získat předpovědí z modelu.  Ukázkový kód klienta je k dispozici:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Pokud chcete použít obsluhující TensorFlow, můžete si [stáhnout ukázkový klienta](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel.client import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Protože byl tento třídění trénovaných na [ImageNet](http://www.image-net.org/) dat nastavit, mapování tříd na popisky čitelné.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensors, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Čištění služby
Odstraňte webovou službu, image a modelu (je třeba provést v tomto pořadí od neexistují závislosti).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Nasazení do místní hraniční server

Všechny [zařízení Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) obsahovat FPGA pro spouštění modelu.  Pouze jeden model může běžet na FPGA najednou.  Pokud chcete spustit jiný model, nasadíte nový kontejner. Pokyny a ukázkový kód lze nalézt v [této ukázce Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Zabezpečení webových služeb, FPGA

Informace o zabezpečení FPGA webové služby, najdete v článku [zabezpečení webových služeb](how-to-secure-web-service.md) dokumentu.

## <a name="pbs-family-vms"></a>Virtuální počítače řady PBS

PBS řady virtuálních počítačů Azure obsahuje Intel FPGA 10 Arria.  Zobrazí se jako "Virtuální procesory řada Standard PBS" Pokud kontrolovat přidělení kvótu Azure.  Virtuální počítač PB6 má šest virtuálních procesorů a jeden FPGA a se automaticky zřídí Azure ML jako součást nasazení modelu pro FPGA.  Používá se pouze s použitím Azure ML a nelze jej spustit libovolný bitstreams.  Například nebude možné flash FPGA s bitstreams provedete šifrování, kódování a tak podobně. 
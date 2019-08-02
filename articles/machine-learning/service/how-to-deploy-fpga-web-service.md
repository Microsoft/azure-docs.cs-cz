---
title: Nasazení modelů na FPGA
titleSuffix: Azure Machine Learning service
description: Naučte se, jak nasadit webovou službu s modelem spuštěným v FPGA s využitím služby Azure Machine Learning pro odvození nízké latence.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: cec1a74938690a4f781ea7850fdd6d649550b3eb
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494918"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Nasazení modelu jako webové služby v FPGA s využitím služby Azure Machine Learning

Model můžete nasadit jako webovou službu pro [pole programovatelné brány (FPGA)](concept-accelerate-with-fpgas.md) s využitím Azure Machine Learning modely s hardwarovou akcelerací. Použití FPGA poskytuje odvození nízké latence, a to i s jednou velikostí dávky. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data.

Tyto modely jsou aktuálně k dispozici:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA jsou k dispozici v těchto oblastech Azure:
  - East US
  - Jihovýchodní Asie
  - Západní Evropa
  - USA – západ 2

> [!IMPORTANT]
> Aby bylo možné optimalizovat latenci a propustnost, váš klient odesílající data do modelu FPGA by měl být v jedné z výše uvedených oblastí (ten, na který jste model nasadili).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure.  Pokud ho ještě nemáte, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

- FPGA kvóta. K ověření, zda máte kvótu, použijte rozhraní příkazového řádku Azure.

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Další možná umístění jsou ``southeastasia``, ``westeurope``a ``westus2``.

    Příkaz vrátí text podobný následujícímu:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Ujistěte se, že v části __CurrentValue__máte minimálně 6 vCPU.

    Pokud nemáte kvótu, odešlete žádost na [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI).

- Pracovní prostor služby Azure Machine Learning service a Azure Machine Learning SDK for nainstalovaný Python. Další informace najdete v tématu [Vytvoření pracovního prostoru](setup-create-workspace.md).
 
- Sada Python SDK pro hardwarové a akcelerované modely:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Ukázkové poznámkové bloky

Pro usnadnění práce jsou k dispozici [ukázkové poznámkové bloky](https://aka.ms/aml-accel-models-notebooks) pro následující příklad a další příklady.

## <a name="create-and-containerize-your-model"></a>Vytvoření a kontejnerizaceí modelu

Tento dokument popisuje, jak vytvořit graf TensorFlow k předzpracování vstupní image, vytvořit featurizer pomocí ResNet 50 na FPGA a pak tyto funkce spustit prostřednictvím klasifikátoru, který je vyškolený v sadě dat ImageNet.

Postupujte podle pokynů:

* Definování modelu TensorFlow
* Převést model
* Nasazení modelu
* Používání nasazeného modelu
* Odstranění nasazené služby

### <a name="load-azure-ml-workspace"></a>Načíst pracovní prostor Azure ML

Načtěte svůj pracovní prostor Azure ML.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Předběžné zpracování obrazu

Vstupem do webové služby je obrázek JPEG.  Prvním krokem je dekódování obrázku JPEG a jeho předzpracování.  Obrázky JPEG se považují za řetězce a výsledkem jsou intenzity, které budou vstupem do modelu ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Načíst featurizer

Inicializovat model a stáhněte si kontrolní bod TensorFlow kvantizované verze ResNet50 má být použit jako featurizer.  V následujícím fragmentu kódu můžete nahradit text "QuantizedResnet50" pomocí importu jiných neuronové sítí s hloubkou:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Přidat klasifikátor

Na datové sadě ImageNet byla vyškolila tento třídění.  V sadě ukázkových poznámkových [bloků](https://aka.ms/aml-notebooks)jsou k dispozici příklady pro učení a školení pro vaše vlastní váhy.

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Uložit model

Nyní, když byl načten preprocesor, ResNet 50 featurizer a klasifikátor, uložte graf a přidružené proměnné jako model.

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

### <a name="save-input-and-output-tensors"></a>Uložit vstupní a výstupní desítkové hodnoty
Vstupní a výstupní modely, které byly vytvořeny během procesu předběžného zpracování a třídění, budou potřeba pro převod modelů a odvození.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Uložte vstupní a výstupní koeficienty, protože je budete potřebovat pro převod modelu a požadavky na odvození.

Dostupné modely a odpovídající výchozí hodnoty pro výstup třídění jsou uvedeny níže, což je to, co byste použili pro odvození, pokud jste použili výchozí třídění.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Registrace modelu

[](./concept-model-management-and-deployment.md) Zaregistrujte model, který jste vytvořili.  Přidání značek a dalších metadat k modelu vám pomůže sledovat vaše vyškolené modely.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Pokud jste už model zaregistrovali a chcete ho načíst, můžete ho načíst.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Převést model

Převeďte graf TensorFlow do formátu Open neuronové Network Exchange ([ONNX](https://onnx.ai/)).  Budete muset zadat názvy vstupních a výstupních intenzit a tyto názvy bude klient používat při využívání webové služby.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Vytvořit obrázek Docker

Převedený model a všechny závislosti jsou přidány do bitové kopie Docker.  Tuto image Docker je pak možné nasadit a vytvořit její instanci.  Mezi podporované cíle nasazení patří AKS do cloudu nebo hraničního zařízení, například [Azure Data box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Můžete také přidat značky a popisy pro zaregistrovanou bitovou kopii Docker.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Seznamte se s obrázky podle značky a Získejte podrobné protokoly pro jakékoli ladění.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Nasazení modelu

### <a name="deploy-to-the-cloud"></a>Nasazení do cloudu

Nasazení modelu jako produkční vysoce škálovatelnou webovou službu, použijte Azure Kubernetes Service (AKS). Můžete vytvořit nový pomocí Azure Machine Learning SDK, CLI nebo Azure Portal.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_PB6s",
                                                    agent_count=1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

Nasazení AKS může trvat přibližně 15 minut.  Zkontrolujte, zda nasazení proběhlo úspěšně.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Nasaďte kontejner do clusteru AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Testování cloudové služby
Image Docker podporuje gRPC a TensorFlow, které obsluhuje "předpověď" rozhraní API.  K získání předpovědi z modelu použijte ukázkový klient pro volání do image Docker.  Vzorový klientský kód je k dispozici:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Pokud chcete používat TensorFlow obsluhující, můžete [si stáhnout ukázkového klienta](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

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

Vzhledem k tomu, že tento klasifikátor byl vyškolený v sadě dat [ImageNet](http://www.image-net.org/) , namapujte třídy na popisky, které jsou uživatelsky čitelné.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

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

### <a name="clean-up-the-service"></a>Vyčištění služby
Odstranění webové služby, image a modelu (je třeba provést v tomto pořadí, protože existují závislosti).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Nasazení na místní hraniční Server

Všechna [Azure Data box Edge zařízení](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) obsahují FPGA pro spuštění modelu.  V FPGA může být současně spuštěn pouze jeden model.  Pokud chcete spustit jiný model, stačí nasadit nový kontejner. Pokyny a ukázkový kód najdete v [této ukázce Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Zabezpečení webových služeb, FPGA

Informace o zabezpečení webových služeb FPGA naleznete v dokumentu [zabezpečené webové služby](how-to-secure-web-service.md) .

## <a name="pbs-family-vms"></a>Virtuální počítače rodiny služby PBS

Rodina služby PBS virtuálních počítačů Azure obsahuje Intel Arria 10 FPGA.  Po kontrole přidělení kvót Azure se zobrazí jako standardní rodina vCPU služby PBS.  Virtuální počítač PB6 má šest vCPU a jeden FPGA a automaticky ho zřídí Azure ML v rámci nasazení modelu na FPGA.  Používá se jenom s Azure ML a nemůže spustit libovolný bitstreams.  Například nebudete moci zablikat FPGA pomocí bitstreams pro šifrování, kódování atd. 

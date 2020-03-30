---
title: Co jsou FPGA - jak nasadit
titleSuffix: Azure Machine Learning
description: Zjistěte, jak nasadit webovou službu s modelem spuštěným na FPGA s Azure Machine Learning pro odvození od ultra nízké latence.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 8cb6cf49e302122849dc2402bcff008e72e15608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472354"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Co jsou pole programovatelná pole brány (FPGA) a jak nasadit
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento článek obsahuje úvod do pole programovatelná pole brány (FPGA) a ukazuje, jak nasadit modely pomocí Azure Machine Learning do Azure FPGA.

Pole FPGA obsahují pole programovatelných bloků logiky a hierarchii propojení, jejichž konfigurace se dá měnit. Propojení umožňují, aby tyto bloky byly po výrobě konfigurovány různými způsoby. Ve srovnání s jinými čipy poskytují FPGA kombinaci programovatelnosti a výkonu.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA vs. CPU, GPU a ASIC

Následující diagram a tabulka ukazují, jak fpga porovnání s jinými procesory.

![Diagram porovnání FPGA azure machine learningu](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Procesor||Popis|
|---|:-------:|------|
|Integrované obvody specifické pro aplikaci|Asics|Vlastní obvody, jako jsou procesorové jednotky TensorFlow (TPU) společnosti Google, poskytují nejvyšší účinnost. Nelze je překonfigurovat, jak se mění vaše potřeby.|
|Pole-programovatelná vratová pole|FPGA|FPGA, jako jsou ty, které jsou k dispozici v Azure, poskytují výkon v blízkosti ASIC. Jsou také flexibilní a rekonfigurovatelné v průběhu času, k implementaci nové logiky.|
|Grafické procesory|Grafické procesory|Oblíbená volba pro výpočty AI. Gpu nabízejí možnosti paralelního zpracování, takže je rychlejší při vykreslování obrázků než procesory.|
|Centrální zpracovatelské jednotky|CPUs|Univerzální procesory, jejichž výkon není ideální pro zpracování grafiky a videa.|

FPGA v Azure jsou založeny na zařízeních Intel FPGA, které datoví vědci a vývojáři používají k urychlení výpočtů ai v reálném čase. Tato architektura s podporou FPGA nabízí výkon, flexibilitu a škálování a je dostupná v Azure.

FPGA umožňují dosáhnout nízké latence pro požadavky na odvození v reálném čase (nebo vyhodnocování modelu). Asynchronní požadavky (dávkování) nejsou potřeba. Dávkování může způsobit latenci, protože je třeba zpracovat více dat. Implementace jednotek nervového zpracování nevyžadují dávkování; proto latence může být mnohokrát nižší, ve srovnání s procesory CPU a GPU.

### <a name="reconfigurable-power"></a>Rekonfigurovatelný výkon
FPGA můžete překonfigurovat pro různé typy modelů strojového učení. Tato flexibilita usnadňuje urychlení aplikací na základě nejoptimálnější numerické přesnosti a použitého paměťového modelu. Vzhledem k tomu, že FPGA jsou rekonfigurovatelné, můžete zůstat aktuální s požadavky rychle se měnících algoritmů AI.

## <a name="whats-supported-on-azure"></a>Co je v Azure podporované
Microsoft Azure je největší investice do cloudu na světě v oblasti FPGA. Pomocí této hardwarové architektury s podporou FPGA běží trénované neuronové sítě rychle a s nižší latencí. Azure můžete paralelizovat předem trénované hluboké neuronové sítě (DNN) napříč FPGA škálovat vaše služby. Dnns mohou být předem vycvičeny, jako hluboký featurizer pro přenos učení, nebo jemně vyladěné s aktualizovanými závažími.

FPGA v Azure podporuje:

+ Scénáře klasifikace a rozpoznávání obrázků
+ Nasazení Tentendflow
+ Hardware Intel FPGA

Tyto modely DNN jsou v současné době k dispozici:
  - Síť ResNet 50
  - Síť ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA jsou dostupné v těchto oblastech Azure:
  - USA – východ
  - Jihovýchodní Asie
  - Západní Evropa
  - USA – západ 2

> [!IMPORTANT]
> Chcete-li optimalizovat latenci a propustnost, váš klient odesílání dat do modelu FPGA by měl být v jedné z výše uvedených oblastí (ten, do kterých jste nasadili model).

PbS **rodina virtuálních počítačů Azure** obsahuje Intel Arria 10 FPGA. Při kontrole přidělení kvóty Azure se zobrazí jako virtuální procesory standard pbs řady. Virtuální počítač PB6 má šest virtuálních procesorů a jeden FPGA a bude automaticky zřízen Azure ML jako součást nasazení modelu do FPGA. Používá se pouze s Azure ML a nelze spustit libovolné bitové proudy. Například nebudete moci blikat FPGA s bitstreamy pro šifrování, kódování atd.

### <a name="scenarios-and-applications"></a>Scénáře a aplikace

Azure FPGA jsou integrované s Azure Machine Learning. Společnost Microsoft používá FPGA pro vyhodnocení DNN, bing vyhledávání pořadí a softwarově definované síťové (SDN) akcelerace ke snížení latence, zatímco uvolnění procesorů pro jiné úkoly.

Následující scénáře používají FPGA:
+ [Automatizovaný optický kontrolní systém](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapování pozemků](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>Příklad: Nasazení modelů na FPGA

Model můžete nasadit jako webovou službu na FPGA s modely s akcelerací hardwaru Azure Machine Learning. Použití FPGA poskytuje velmi nízkou latenci odvození, a to i s jednou velikostí dávky. Odvození nebo vyhodnocování modelu je fáze, kde se nasazený model používá pro předpověď, nejčastěji na produkčních datech.

### <a name="prerequisites"></a>Požadavky

- Předplatné Azure.  Pokud ho nemáte, než začnete, vytvořte si bezplatný účet. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

- Kvóta FPGA. Pomocí azure cli zkontrolujte, zda máte kvótu:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Další možná umístění ``southeastasia`` ``westeurope``jsou ``westus2``, a .

    Příkaz vrátí text podobný následujícímu:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Ujistěte se, že máte alespoň 6 virtuálních procesorů v části __CurrentValue__.

    Pokud kvótu nemáte, odešlete žádost [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)na adrese .

- Pracovní prostor Azure Machine Learning a nainstalovaná sada Azure Machine Learning SDK pro Python. Další informace naleznete [v tématu Vytvoření pracovního prostoru](how-to-manage-workspace.md).
 
- Sada Python SDK pro hardwarově akcelerované modely:

    ```bash
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. Vytváření a kontejnery modelů

Tento dokument popisuje, jak vytvořit graf TensorFlow pro předběžné zpracování vstupního obrazu, znějí featurizer pomocí ResNet 50 na FPGA a pak spusťte funkce prostřednictvím třídění trénovaného v datové sadě ImageNet.

Postupujte podle pokynů:

* Definování modelu TensorFlow
* Převést model
* Nasazení modelu
* Využití nasazeného modelu
* Odstranění nasazených služeb

Pomocí [sady Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) vytvořte definici služby. Definice služby je soubor popisující kanál grafů (vstup, featurizer a třídění) založený na TensorFlow. Příkaz nasazení automaticky komprimuje definici a grafy do souboru ZIP a nahraje zip do úložiště objektů blob Azure. DNN je již nasazena ke spuštění na FPGA.

### <a name="load-azure-machine-learning-workspace"></a>Načtení pracovního prostoru Azure Machine Learning

Načtěte pracovní prostor Azure Machine Learning.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Obrázek před zpracováním

Vstup emituje webovou službu jako obrázek JPEG.  Prvním krokem je dekódování obrazu JPEG a jeho předběžné zpracování.  Obrazy JPEG jsou považovány za řetězce a výsledkem jsou tenzory, které budou vstupem do modelu ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Zatížení featurizer

Inicializovat model a stáhnout TensorFlow kontrolní bod kvantizované verze ResNet50, které mají být použity jako featurizer.  Můžete nahradit "QuantizedResnet50" v kódu fragment níže s importem dalších hlubokých neuronových sítí:

- KvantizovanýResnet152
- KvantizovanýVgg16
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

### <a name="add-classifier"></a>Přidat třídění

Tento klasifikátor byl trénován na datové sadě ImageNet.  Příklady pro přenos učení a školení vlastní váhy jsou k dispozici v sadě [ukázkových poznámkových bloků](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Uložení modelu

Nyní, když preprocesor, ResNet 50 featurizer a třídění byly načteny, uložte graf a přidružené proměnné jako model.

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

### <a name="save-input-and-output-tensors"></a>Uložení vstupních a výstupních tenzorů
Vstupní a výstupní tenzory, které byly vytvořeny během kroků předběžného zpracování a třídění, budou potřebné pro převod modelu a odvození.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Uložte vstupní a výstupní tenzory, protože je budete potřebovat pro požadavky na převod modelu a odvození.

Dostupné modely a odpovídající výchozí třídění výstupní tenzory jsou níže, což je to, co byste použili pro odvození, pokud jste použili výchozí třídění.

+ Resnet50, KvantizovanýResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, KvantizovanýResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, KvantizovanýDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, KvantizovánVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, KvantizovánOSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Registrace modelu

[Zaregistrujte](concept-model-management-and-deployment.md) model pomocí sady SDK se souborem ZIP v úložišti objektů Blob Azure. Přidání značek a dalších metadat o modelu vám pomůže sledovat vaše trénované modely.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Pokud jste již model zaregistrovali a chcete jej načíst, můžete jej načíst.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Převést model

Převeďte graf TensorFlow do formátu Open Neural Network Exchange ([ONNX](https://onnx.ai/)).  Budete muset zadat názvy vstupnía výstupní tenzory a tyto názvy budou použity klientem při využívání webové služby.

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

### <a name="create-docker-image"></a>Vytvoření image Dockeru

Převedený model a všechny závislosti jsou přidány do image Dockeru.  Tuto image Dockeru pak můžete nasadit a vytvořit instanci.  Mezi podporované cíle nasazení patří AKS v cloudu nebo hraniční zařízení, jako je [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Můžete také přidat značky a popisy pro registrovanou bitové kopii Dockeru.

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

Seznam obrázků podle značky a získat podrobné protokoly pro všechny ladění.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Nasazení do cloudu nebo okraje

### <a name="deploy-to-the-cloud"></a>Nasazení do cloudu

Chcete-li nasadit model jako webovou službu ve velkém měřítku, použijte službu Azure Kubernetes Service (AKS). Novou můžete vytvořit pomocí sady Azure Machine Learning SDK, CLI nebo [Studia Azure Machine Learning](https://ml.azure.com).

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

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
Image Docker podporuje gRPC a TensorFlow obsluhující "předpovědět" API.  Pomocí ukázkového klienta můžete volat do image Dockeru, abyste získali předpovědi z modelu.  Ukázkový klientský kód je k dispozici:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C #](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Pokud chcete použít TensorFlow Serving, můžete [si stáhnout ukázkového klienta](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Vzhledem k tomu, že tento klasifikátor byl trénován na datové sadě [ImageNet,](http://www.image-net.org/) mapujte třídy na popisky čitelné pro člověka.

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
Odstraňte webovou službu, obrázek a model (musí být provedeno v tomto pořadí, protože existují závislosti).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Nasazení na místní hraniční server

Všechna [zařízení Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) obsahují FPGA pro spuštění modelu.  Pouze jeden model může být spuštěn na FPGA najednou.  Chcete-li spustit jiný model, stačí nasadit nový kontejner. Pokyny a ukázkový kód najdete v [této ukázce Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Zabezpečené webové služby FPGA

Chcete-li zabezpečit webové služby FPGA, přečtěte si dokument [Zabezpečené webové služby.](how-to-secure-web-service.md)

## <a name="next-steps"></a>Další kroky

Podívejte se na tyto poznámkové bloky, videa a blogy:

+ Několik [ukázkových poznámkových bloků](https://aka.ms/aml-accel-models-notebooks)

+ [Hardware hyperškálování: ML ve velkém měřítku nad Azure + FPGA: Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Uvnitř konfigurovatelného cloudu založeného na Microsoft FPGA (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Projekt Brainwave pro umělou intelidenční ai v reálném čase: domovská stránka projektu](https://www.microsoft.com/research/project/project-brainwave/)

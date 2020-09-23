---
title: Co je FPGA – jak nasadit
titleSuffix: Azure Machine Learning
description: Naučte se, jak nasadit webovou službu pomocí modelu běžícího na FPGA s využitím Azure Machine Learning pro odvození nízké latence.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 06/03/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 7637cc911ea2fbb950a18c2c8d91f5c3eaf02c23
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905078"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Co jsou pole – programovatelné pole brány (FPGA) a jak nasadit



Tento článek obsahuje úvod do polí s programovatelné branou (FPGA) a ukazuje, jak nasadit vaše modely pomocí [Azure Machine Learning](overview-what-is-azure-ml.md) do Azure FPGA.

## <a name="what-are-fpgas"></a>Co jsou FPGA

Pole FPGA obsahují pole programovatelných bloků logiky a hierarchii propojení, jejichž konfigurace se dá měnit. Propojení umožňují, aby tyto bloky byly po výrobě nakonfigurované různými způsoby. V porovnání s jinými čipy FPGA poskytuje kombinaci programovatelnosti a výkonu. 

![Diagram porovnání Azure Machine Learning FPGA](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Procesor| Zkratka |Description|
|---|:-------:|------|
|Integrované okruhy specifické pro aplikaci|ASICs|Vlastní okruhy, například jednotky procesoru Google TensorFlow (TPU), poskytují nejvyšší efektivitu. Nedají se znovu nakonfigurovat, jak se vaše potřeby mění.|
|Pole – programovatelné pole brány|FPGA|FPGA, jako jsou ty, které jsou k dispozici v Azure, poskytují výkon blízko ASICs. Jsou také flexibilní a znovu konfigurovatelné v čase, k implementaci nové logiky.|
|Jednotky pro zpracování grafiky|Grafické procesory|Oblíbená volba pro výpočty AI. GPU nabízí možnosti paralelního zpracování, což urychluje vykreslování obrázků než CPU.|
|Jednotky centrálního zpracování|CPUs|Procesory pro obecné účely, výkon, který není ideální pro zpracování grafiky a videa.|


FPGA umožňuje dosáhnout nízké latence pro požadavky na odvození v reálném čase (nebo bodování modelu). Asynchronní požadavky (dávkování) nejsou potřeba. Dávkování může způsobit latenci, protože je potřeba zpracovat víc dat. Implementace jednotek zpracování neuronové nevyžadují dávkování; Proto latence může být v porovnání s procesorem a procesorem GPU mnohokrát nižší.

FPGA můžete znovu nakonfigurovat pro různé typy modelů strojového učení. Tato flexibilita usnadňuje zrychlení aplikací na základě optimální číselné přesnosti a používaného modelu paměti. Vzhledem k tomu, že se FPGA znovu nakonfiguruje, můžete zůstat na aktuálním základě požadavky na rychlé změny algoritmů AI.

### <a name="fpga-support-in-azure"></a>Podpora FPGA v Azure

Microsoft Azure je celosvětová investice do cloudu v FPGA. Microsoft používá FPGA k vyhodnocení DNN, hodnocení vyhledávání Bingu a akceleraci softwarově definovaných sítí (SDN), aby se snížila latence, a přitom uvolňují procesory pro jiné úlohy.

FPGA v Azure vychází ze zařízení s FPGA Intel, kteří odborníci přes data a vývojáři používají k urychlení výpočtů AI v reálném čase. Tato architektura s podporou FPGA nabízí výkon, flexibilitu a škálování a je dostupná v Azure.

Azure FPGA jsou integrované s Azure Machine Learning. Azure může paralelizovat předem vyškolené neuronové sítě (DNN) napříč FPGA pro horizontální navýšení kapacity vaší služby. Hluboké může být předem vyškolená jako hluboká featurizer pro přenosové učení nebo doladitelné s aktualizovanými závažími.

FPGA v Azure podporuje:

+ Klasifikace obrázků a scénáře rozpoznávání
+ Nasazení TensorFlow (vyžaduje Tensorflow 1. x)
+ Hardware Intel FPGA

Tyto modely DNN jsou aktuálně k dispozici:

  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD – VGG

  
FPGA jsou k dispozici v těchto oblastech Azure:
  - East US
  - Southeast Asia
  - West Europe
  - Západní USA 2

Aby bylo možné optimalizovat latenci a propustnost, váš klient odesílající data do modelu FPGA by měl být v jedné z výše uvedených oblastí (ten, na který jste model nasadili).

**Rodina služby PBS virtuálních počítačů Azure** obsahuje Intel Arria 10 FPGA. Po kontrole přidělení kvót Azure se zobrazí jako standardní rodina vCPU služby PBS. Virtuální počítač PB6 má šest vCPU a jeden FPGA a automaticky ho zřídí Azure ML v rámci nasazení modelu na FPGA. Používá se jenom s Azure ML a nemůže spustit libovolný bitstreams. Například nebudete moci zablikat FPGA pomocí bitstreams pro šifrování, kódování atd.


## <a name="deploy-models-on-fpgas"></a>Nasazení modelů na FPGA

Model můžete nasadit jako webovou službu v FPGA s využitím [Azure Machine Learning modely s hardwarovou akcelerací](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py&preserve-view=true). Použití FPGA poskytuje odvození nízké latence, a to i s jednou velikostí dávky. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data.

Nasazení modelu do FPGA zahrnuje následující kroky:

1. Definování modelu TensorFlow
1. Převést model na ONNX
1. Nasazení modelu do cloudu nebo hraničního zařízení
1. Využití nasazeného modelu

V této ukázce vytvoříte graf TensorFlow, abyste mohli předzpracovat vstupní image, vytvořit ji featurizer pomocí ResNet 50 na FPGA a pak tyto funkce spustit prostřednictvím klasifikátoru, který je vyškolený na ImageNet sadě dat. Model se pak nasadí do clusteru AKS.

### <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, budete muset vytvořit účet s průběžnými [platbami](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) (bezplatné účty Azure nejsou vhodné pro FPGA kvótu).
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- FPGA kvóta. K ověření, zda máte kvótu, použijte rozhraní příkazového řádku Azure.

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```


    Příkaz vrátí text podobný následujícímu:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Ujistěte se, že v části __CurrentValue__máte minimálně 6 vCPU.

    Pokud nemáte kvótu, odešlete žádost na [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI) .

- Je nainstalovaný Azure Machine Learning pracovní prostor a sada Azure Machine Learning SDK pro Python. Další informace najdete v tématu [Vytvoření pracovního prostoru](how-to-manage-workspace.md).
 
- Sada Python SDK pro hardwarové a akcelerované modely:

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```
### <a name="1-define-the-tensorflow-model"></a>1. definování modelu TensorFlow

K vytvoření definice služby použijte [sadu SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) . Definice služby je soubor popisující kanál grafů (vstup, featurizer a klasifikátor) založený na TensorFlow. Příkaz pro nasazení automaticky komprimuje definice a grafy do souboru ZIP a nahraje soubor ZIP do úložiště objektů BLOB v Azure. DNN je už nasazené pro běh na FPGA.

1. Načíst Azure Machine Learning pracovní prostor

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Obrázek předběžného zpracování. Vstupem do webové služby je obrázek JPEG.  Prvním krokem je dekódování obrázku JPEG a jeho předzpracování.  Obrázky JPEG se považují za řetězce a výsledkem jsou intenzity, které budou vstupem do modelu ResNet 50.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Načíst featurizer. Inicializujte model a stáhněte kontrolní bod TensorFlow verze quantized pro ResNet50, který se použije jako featurizer.  V následujícím fragmentu kódu můžete nahradit text "QuantizedResnet50" pomocí importu jiných neuronové sítí s hloubkou:

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

1. Přidejte klasifikátor. Tento klasifikátor byl vyškolený pro ImageNet datovou sadu.  V sadě [ukázkových poznámkových bloků](https://github.com/Azure/MachineLearningNotebooks)jsou k dispozici příklady pro učení a školení pro vaše vlastní váhy.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Uložte model. Nyní, když byl načten preprocesor, ResNet 50 featurizer a klasifikátor, uložte graf a přidružené proměnné jako model.

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

1. Uložte vstupní a výstupní sedesítkování. Vstupní a výstupní modely, které byly vytvořeny během procesu předběžného zpracování a třídění, budou potřeba pro převod modelů a odvození.

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

### <a name="2-convert-the-model"></a>2. Převeďte model

Před nasazením modelu do FPGA je nutné ho převést na formát ONNX.

1. [Zaregistrujte](concept-model-management-and-deployment.md) model pomocí sady SDK se souborem zip v úložišti objektů BLOB v Azure. Přidání značek a dalších metadat k modelu vám pomůže sledovat vaše vyškolené modely.

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

1. Převeďte graf TensorFlow do formátu Open neuronové Network Exchange ([ONNX](https://onnx.ai/)).  Budete muset zadat názvy vstupních a výstupních intenzit a tyto názvy bude klient používat při využívání webové služby.

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

### <a name="3-containerize-and-deploy-the-model"></a>3. kontejnerizace a nasazení modelu

Vytvořte image Docker z převedeného modelu a všech závislostí.  Tuto image Docker je pak možné nasadit a vytvořit její instanci.  Mezi podporované cíle nasazení patří AKS do cloudu nebo hraničního zařízení, například [Azure Data box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Můžete také přidat značky a popisy pro zaregistrovanou bitovou kopii Docker.

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

#### <a name="deploy-to-aks-cluster"></a>Nasazení do clusteru AKS

1. K nasazení modelu jako vysoce škálovatelné produkční webové služby použijte službu Azure Kubernetes Service (AKS). Můžete vytvořit nový pomocí Azure Machine Learning SDK, CLI nebo [Azure Machine Learning studia](https://ml.azure.com).

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

1. Nasaďte kontejner do clusteru AKS.

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

#### <a name="deploy-to-a-local-edge-server"></a>Nasazení na místní hraniční Server

Všechna [Azure Data box Edge zařízení](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) obsahují FPGA pro spuštění modelu.  V FPGA může být současně spuštěn pouze jeden model.  Pokud chcete spustit jiný model, stačí nasadit nový kontejner. Pokyny a ukázkový kód najdete v [této ukázce Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

### <a name="4-consume-the-deployed-model"></a>4. využívání nasazeného modelu

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

# Initialize Azure ML Accelerated Models client
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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstranění webové služby, image a modelu (je třeba provést v tomto pořadí, protože existují závislosti).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Další kroky

Podívejte se na tyto poznámkové bloky, videa a blogy:

+ Několik [ukázkových poznámkových bloků](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/accelerated-models)
+ Chcete-li zabezpečit webové služby FPGA, přečtěte si dokument [zabezpečené webové služby](how-to-secure-web-service.md) .
+ [Hardware s škálovatelným škálováním: ML na škále nad Azure + FPGA: Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)
+ [V rámci konfigurovatelného cloudu založeného na Microsoft FPGA (video)](https://channel9.msdn.com/Events/Build/2017/B8063)
+ [Projekt Brainwave pro AI v reálném čase: Domovská stránka projektu](https://www.microsoft.com/research/project/project-brainwave/)
+ [Automatizovaný systém optické kontroly](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
+ [Mapování pozemkového pokrytí](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

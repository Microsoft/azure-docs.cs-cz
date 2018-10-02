---
title: ONNX a Azure Machine Learning | Vytvoření a nasazení modelů
description: Další informace o ONNX a jak vytvářet a nasazovat modely ONNX pomocí Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: 1350c543990963f8f860d9dd1da5670d3a1e990c
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585556"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX a Azure Machine Learning: vytvoření a nasazení interoperabilní modely AI

[Otevřete Neural Network Exchange](http://onnx.ai) formátu (ONNX) je otevřený standard představující modelů strojového učení. Je podporován ONNX [komunitě partnerů](http://onnx.ai/supported-tools), včetně společnosti Microsoft, který vytvořit kompatibilní architekturami a nástroji. Společnosti Microsoft záleží na otevřené a interoperabilní AI, takže odborníci přes data a vývojáři můžou:

+ Použití rozhraní framework podle vlastní volby k vytvoření a trénování modelů
+ Nasazení modelů napříč platformami s minimálními integrační práci

Microsoft podporuje ONNX své produkty, včetně Azure a Windows, které vám pomůžou dosáhnout těchto cílů.  

## <a name="why-choose-onnx"></a>Proč zvolit ONNX?
Vzájemná funkční spolupráce, získáte s ONNX umožňuje rychleji získat představu do produkčního prostředí. Odborníci přes data ONNX, můžete zvolit jejich preferované rozhraní pro úlohu. Podobně vývojáři můžete strávit méně času na získání vyšší modely připravené pro produkční a nasadit napříč cloudem a hraničními zařízeními.  

Využijte modely ONNX můžete vytvořit z mnoha architektur, včetně PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet, ML.Net, TensorFlow, Keras, SciKit-informace a další.

Je také ekosystém nástrojů pro vizualizaci a zrychluje modely ONNX. Počet předem vytrénovaných modely ONNX jsou také k dispozici pro běžné scénáře.

[Je možné nasadit modely ONNX](#deploy) do cloudu pomocí služby Azure Machine Learning a modul Runtime ONNX. Může být také nasazený na zařízení s Windows 10 pomocí [Windows ML](https://docs.microsoft.com/windows/ai/). Dokonce je možné nasadit do jiné platformy použití převaděče, které jsou k dispozici od komunity ONNX. 

[ ![ONNX vývojový diagram znázorňující nasazení, školení a převaděče](media/concept-onnx/onnx.png) ] (. / media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Získat modely ONNX

Můžete získat modely ONNX několika způsoby:
+ Získejte předem vytrénovaných modelu ONNX ze [Zoo modelu ONNX](https://github.com/onnx/models) (viz příklad v dolní části tohoto článku)
+ Generovat přizpůsobené modelu ONNX ze [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 
+ Převést existující model z jiného formátu ONNX (viz příklad v dolní části tohoto článku) 
+ Trénování modelu ONNX nové služby Azure Machine Learning (viz příklad v dolní části tohoto článku)

## <a name="saveconvert-your-models-to-onnx"></a>Uložit/převést vaše modely ONNX

Můžete převést stávající modely ONNX nebo je uložit jako ONNX na konci trénování.

|Architektura pro model|Například převod nebo nástroj|
|-----|-------|
|PyTorch|[Poznámkový blok Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit&nbsp;(CNTK)|[Poznámkový blok Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[Převaděč tensorflow onnx](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Poznámkový blok Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Poznámkový blok Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras další ScitKit CoreML<br/>XGBoost a libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

Můžete vyhledat nejnovější seznam podporovaných platforem a převaděče na [ONNX kurzy lokality](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Nasadit modely ONNX v Azure

Pomocí služby Azure Machine Learning můžete nasadit, spravovat a monitorovat využijte modely ONNX. Pomocí standardní [pracovní postup nasazení](concept-model-management-and-deployment.md) a modul Runtime ONNX můžete vytvořit koncový bod REST hostované v cloudu. Podívejte se úplný příklad Poznámkový blok Jupyter na konci tohoto článku můžete vyzkoušet sami. 

### <a name="install-and-configure-the-onnx-runtime"></a>Instalace a konfigurace modulu Runtime ONNX

Modul Runtime ONNX je modul pro vysoce výkonné odvození pro modely ONNX. Součástí rozhraní Python API a poskytuje hardwarovou akceleraci CPU a GPU. Aktuálně podporuje modely ONNX 1.2 a běží na systému Linux Ubuntu 16.04. Obě [procesoru](https://pypi.org/project/onnxruntime) a [GPU](https://pypi.org/project/onnxruntime-gpu) balíčky jsou k dispozici na [PyPi.org](https://pypi.org).

Pokud chcete nainstalovat modul Runtime ONNX, použijte:
```python
pip install onnxruntime
```

Pokud chcete volat ONNX Runtime ve svém skriptu Pythonu, použijte:
```python
import onnxruntime

session = onnxruntime.InferenceSession("path to model")
```

Dokumentaci modelu obvykle zjistíte, vstupy a výstupy pro použití modelu. Můžete také použít vizualizačního nástroje jako [Netron](https://github.com/lutzroeder/Netron) zobrazíte modelu. Modul Runtime ONNX také umožňuje dotazování metadat modelu, vstupy a výstupy:
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

K odvození váš model, použijte `run` a předejte mu seznam výstupy požadujete vrátil (ponechte prázdné Pokud chcete všechny z nich) a mapu vstupních hodnot. Výsledkem je seznam výstupy.
```python
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Úplné referenční rozhraní API najdete v článku [ONNX Runtime referenční dokumenty](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>Příklady kroků nasazení

Tady je příklad pro nasazení modelu ONNX:

1. Inicializace vaší aplikace Azure Machine Learning pracovního prostoru. Pokud jeden ještě nemáte, zjistěte, jak vytvořit pracovní prostor v [v tomto rychlém startu](quickstart-get-started.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Zaregistrujte model Azure Machine Learning.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Vytvoření image s modelem a všechny závislosti.

   ```python
   from azureml.core.image import ContainerImage
   
   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   Soubor `score.py` obsahuje hodnoticí logiku a musí být součástí image. Tento soubor se používá ke spuštění modelu v bitové kopii. Najdete v tomto [kurzu](tutorial-deploy-models-with-aml.md#create-scoring-script) pro pokyny, jak vytvořit bodování skriptu. Příklad souboru u modelu ONNX je zobrazena níže:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   Soubor `myenv.yml` popisuje závislosti potřebné pro bitovou kopii. Najdete v tomto [kurzu](tutorial-deploy-models-with-aml.md#create-environment-file) pokyny o tom, jak vytvořit soubor prostředí, jako je například tento ukázkový soubor:

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies()
   myenv.add_pip_package("numpy")
   myenv.add_pip_package("azureml-core")
   myenv.add_pip_package("onnxruntime")

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Nasazení modelu ONNX pomocí Azure Machine Learning provádět:
   + Azure Container Instances (ACI): [zjistěte, jak...](how-to-deploy-to-aci.md)

   + Azure Kubernetes Service (AKS): [zjistěte, jak...](how-to-deploy-to-aks.md)


## <a name="examples"></a>Příklady
 
Tyto poznámkové bloky ukazují, jak vytvořit modely ONNX a nasadit je pomocí služby Azure Machine Learning: 
+ `/onnx/onnx-modelzoo-aml-deploy-resnet50.ipynb` 
+ `/onnx/onnx-convert-aml-deploy-tinyyolo.ipynb`
+ `/onnx/onnx-train-pytorch-aml-deploy-mnist.ipynb`

Tyto poznámkové bloky ukazují, jak nasazovat existující modely ONNX službou Azure Machine Learning: 
+ `/onnx/onnx-inference-mnist.ipynb` 
+ `/onnx/onnx-inference-emotion-recognition.ipynb`
 
Získáte tento poznámkový blok:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Další informace

Další informace o ONNX nebo přispět k projektu:
+ [ONNX projekt webu](http://onnx.ai)

+ [ONNX kódu na Githubu](https://github.com/onnx/onnx)

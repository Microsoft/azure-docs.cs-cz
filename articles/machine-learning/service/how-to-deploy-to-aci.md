---
title: Nasazování webových služeb pro Azure Container Instances (ACI) – Azure Machine Learning
description: Zjistěte, jak trénovaný model nasadíme jako webovou službu v Azure Container Instances (ACI) se službou Azure Machine Learning. Tento článek ukazuje tři různé způsoby nasazení modelu ve službě ACI. Se liší v počtu řádků kódu a ovládací prvek, který máte v pojmenování součástí nasazení.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 5a62d4b0b324d8b2536e408132210f07f08e8bb8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958692"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Nasazení webové služby Azure Container Instances 

Trénovaného modelu můžete nasadit jako webovou službu na [Azure Container Instances](https://azure.microsoft.com/services/container-instances/) (ACI) [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) (AKS), IoT hraniční zařízení, nebo [pole programmable gate Array (FPGA)](concept-accelerate-with-fpgas.md) 

ACI je obvykle levnější než AKS a můžete nastavit 4 až 6 řádků kódu. ACI je ideální volbou pro testování nasazení. Později, až budete připraveni používat modely a webové služby pro zajištění vysoce škálovatelné, produkční účely, můžete [jejich nasazení do AKS](how-to-deploy-to-aks.md).

Tento článek ukazuje tři různé způsoby nasazení modelu ve službě ACI. Se liší v počtu řádků kódu a ovládací prvek, který máte v pojmenování součástí nasazení. Z metody s minimem kódu a ovládací prvky do metody s většinu kódu a ACI možnosti jsou:

* Nasazení ze souboru pomocí modelu `Webservice.deploy()` 
* Nasazení pomocí registrovaného modelu `Webservice.deploy_from_model()`
* Nasazení registrovanému modelu pomocí bitové kopie `Webservice.deploy_from_image()`

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning a Azure Machine Learning SDK for nainstalovaný Python. Další informace o získání těchto nezbytných podmínkách používání [Začínáme s Azure Machine Learning quickstart](quickstart-get-started.md).

- Objekt pracovního prostoru Azure Machine Learning

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- Model nasazení. V příkladech v tomto dokumentu pomocí modelu vytvoří, když budete postupovat podle "[trénování modelu](tutorial-train-models-with-aml.md)" kurzu. Pokud nepoužijete tento model, upravte kroky k odkazování na název vašeho modelu.  Potřebujete také napsat vlastní hodnoticí skript pro spuštění vašeho modelu.


## <a name="configure-an-image"></a>Konfigurace image

Nakonfigurujte image Dockeru, který se používá k uložení všech souborů modelu.
1. Vytváření hodnoticí skript (score.py) [podle těchto pokynů](tutorial-deploy-models-with-aml.md#create-scoring-script)

1. Vytvoření souboru prostředí (myenv.yml) [podle těchto pokynů](tutorial-deploy-models-with-aml.md#create-environment-file) 

1. Použijte tyto dva soubory konfigurace image Dockeru v Pythonu pomocí sady SDK následujícím způsobem:

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>Konfigurace kontejneru ACI

Konfigurace kontejneru ACI určit počet procesorů a GB paměti RAM potřebné kontejneru ACI. Výchozí jedno jádro a 1 GB paměti RAM je dostačující pro mnoho modelů. Pokud máte pocit, že budete potřebovat další rozšíření později, znovu vytvořte image a službu znovu nasaďte.  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>Zaregistrujte model

> Tuto požadovanou součást vynechat, pokud jste [nasazení ze souboru modelu](#deploy-from-model-file) (`Webservice.deploy()`).

Zaregistrujte model použití [ `Webservice.deploy_from_model` ](#deploy-from-registered-model) nebo [ `Webservice.deploy_from_image` ](#deploy-from-image). Nebo pokud už máte registrovanému modelu, načtěte ji.

### <a name="retrieve-a-registered-model"></a>Načíst registrovanému modelu
Pokud použijete k natrénování modelu Azure Machine Learning, modelu již může být zaregistrován ve vašem pracovním prostoru.  Například na poslední krok [trénování modelu](tutorial-train-models-with-aml.md) kurzu] zaregistrovaný modelu.  Potom získá registrované modelu pro nasazení.

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>Registrovat soubor modelu

Pokud váš model byl vytvořen jinde, můžete stále ho zaregistrovat do pracovního prostoru.  K registraci modelu, soubor modelu (`sklearn_mnist_model.pkl` v tomto příkladu) musí být v aktuálním pracovním adresáři. Zaregistrujte tento soubor jako model volá `sklearn_mnist` v pracovním prostoru s `Model.register()`.
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```


## <a name="option-1-deploy-from-model-file"></a>Možnost 1: Nasazení ze souboru modelu

Možnost nasazení ze souboru modelu vyžaduje minimální množství kódu zapisovat, ale také nabízí nejnižší možné kontrolu nad pojmenování komponenty. Tato možnost začíná soubor modelu a zaregistruje ho do pracovního prostoru.  Nelze však název modelu nebo přidružit značky a popis pro něj.  

Tato volba používá metody SDK Webservice.deploy().  

**Časový odhad**: nasazení trvá přibližně 6-7 minut.

1. Ujistěte se, že je soubor modelu do místního pracovního adresáře.

1. Otevřete soubor požadovaných součástí modelu, score.py a změn `init()` části:

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. Nasazení souboru modelu.

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Teď můžete [testovat webovou službu](#test-web-service).

## <a name="option-2-deploy-from-registered-model"></a>Možnost 2: Nasazení z registrovaného modelu

Možnost k nasazení souboru registrovanému modelu má několik více řádků kódu a umožňuje některé kontrolu nad názvy výstupů. Tato možnost je pohodlný způsob, jak nasadit registrovanému modelu, které už máte.  Nelze však název image Dockeru.  

Tato volba používá metody SDK Webservice.deploy_from_model().

**Časový odhad**: pomocí této možnosti nasazení trvá přibližně 8 minut.

1. Spuštění kódu a konfigurace kontejneru Dockeru a kontejnerů ACI zadejte registrovanému modelu.

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. Teď můžete [testovat webovou službu](#test-web-service).

## <a name="option-3-deploy-from-image"></a>Možnost 3: Nasazení z image

Nasazení registrovanému modelu (`model`) pomocí `Webservice.deploy_from_image()`. Tato metoda umožňuje vytvoření image Dockeru samostatně a pak nasadit z této image.

1. Sestavení a registrací image Dockeru pomocí pracovního prostoru `ContainerImage.create()`

    Tato metoda poskytuje větší kontrolu nad image tak, že vytvoříte v samostatný krok.  Registrovanému modelu (`model`) je zahrnutá v bitové kopii.
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**Časový odhad**: přibližně 3 minuty.

1. Nasadit image Dockeru jako služby pomocí `Webservice.deploy_from_image()`

    Nyní nasazení image do služby ACI.  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**Časový odhad**: přibližně 3 minuty.

Tato metoda poskytuje většinu kontrolu nad vytváření a pojmenování součástí nasazení.

Teď můžete otestovat webovou službu.

## <a name="test-the-web-service"></a>Test webové služby

Webová služba je stejný bez ohledu na to, která byla použita metoda.  Chcete-li získat predikcí, použijte `run` metody služby.  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto webovou službu používat, odstraňte ho tak nebudou účtovat žádné poplatky.

```python
service.delete()
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nasazení do služby Azure Kubernetes Service](how-to-deploy-to-aks.md) větší měřítko nasazení. 

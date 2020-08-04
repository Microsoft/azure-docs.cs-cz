---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 624824f5b6b8f7154ccd7b50da49f3f4bb179bb9
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542764"
---
## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).
- Model. Pokud nemáte školený model, můžete použít soubory modelů a závislostí, které jsou k dispozici v [tomto kurzu](https://aka.ms/azml-deploy-cloud).
- [Sada SDK (Azure Machine Learning Software Development Kit) pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


## <a name="connect-to-your-workspace"></a>Připojení k pracovnímu prostoru

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Další informace o použití sady SDK pro připojení k pracovnímu prostoru naleznete v dokumentaci [Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

## <a name="register-your-model"></a><a id="registermodel"></a>Registrace modelu

Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model uložený ve více souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru. Po registraci souborů si pak můžete stáhnout nebo nasadit registrovaný model a získat všechny soubory, které jste zaregistrovali.

> [!TIP]
> Při registraci modelu zadáte cestu buď umístění v cloudu (z školicího běhu), nebo místního adresáře. Tato cesta je určena pouze k vyhledání souborů pro nahrání v rámci procesu registrace. Nemusí odpovídat cestě použité ve skriptu pro zadávání. Další informace najdete v tématu [vyhledání souborů modelu ve vstupním skriptu](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Modely strojového učení jsou zaregistrované ve vašem pracovním prostoru Azure Machine Learning. Model může pocházet z Azure Machine Learning nebo z někde jinde. Při registraci modelu můžete volitelně zadat metadata o modelu. `tags` `properties` Slovníky a, které použijete pro registraci modelu, lze použít k filtrování modelů.

Následující příklady ukazují, jak registrovat model.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrace modelu z školicího běhu Azure ML

  Když použijete sadu SDK k vytvoření výukového modelu, můžete získat buď objekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) , nebo objekt [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) v závislosti na tom, jak jste model využívali. Každý objekt lze použít k registraci modelu vytvořeného spuštěním experimentu.

  + Registrace modelu z `azureml.core.Run` objektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Parametr odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na cestu ke složce, která obsahuje soubory. Další informace najdete v dokumentaci ke [spuštění. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Registrace modelu z `azureml.train.automl.run.AutoMLRun` objektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    V tomto příkladu `metric` `iteration` nejsou zadány parametry a, takže iterace s nejlepší primární metrikou bude zaregistrována. `model_id`Hodnota vrácená z běhu se používá místo názvu modelu.

    Další informace najdete v dokumentaci k [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .


### <a name="register-a-model-from-a-local-file"></a>Registrace modelu z místního souboru

Model můžete zaregistrovat zadáním místní cesty k modelu. Můžete zadat cestu buď složky, nebo jednoho souboru. Tuto metodu můžete použít k registraci modelů vyškolených pomocí Azure Machine Learning a potom ke stažení. Tuto metodu můžete použít také k registraci modelů, které jsou vyškolené mimo Azure Machine Learning.

[!INCLUDE [trusted models](machine-learning-service-trusted-model.md)]

+ **Používání sady SDK a ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Pokud chcete do registrace modelu zahrnout více souborů, nastavte `model_path` na cestu ke složce, která obsahuje soubory.

Další informace naleznete v dokumentaci k [třídě modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Další informace o práci s modely poučenými mimo Azure Machine Learning najdete v tématu [Jak nasadit existující model](../articles/machine-learning/how-to-deploy-existing-model.md).


## <a name="define-an-entry-script"></a>Definování skriptu pro zadávání

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Definovat odvozenou konfiguraci

Konfigurace odvození popisuje, jak nastavit webovou službu, která obsahuje váš model. Používá se později při nasazení modelu.

Odvozená konfigurace používá Azure Machine Learning prostředí k definování závislostí softwaru potřebných pro vaše nasazení. Prostředí umožňují vytvářet, spravovat a opakovaně používat závislosti softwaru vyžadované pro školení a nasazení. Můžete vytvořit prostředí z vlastních souborů závislosti nebo použít jedno z Azure Machine Learning prostředí. Následující YAML je příkladem souboru závislostí conda pro odvození. Všimněte si, že je třeba určit, že se ve službě AzureML-Defaults verze >= 1.0.45 jako závislost PIP, protože obsahuje funkce potřebné k hostování modelu jako webové služby. Pokud chcete použít automatické generování schématu, váš vstupní skript musí také naimportovat `inference-schema` balíčky.

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
    - pip:
        # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Pokud je vaše závislost k dispozici prostřednictvím conda i PIP (z PyPi), společnost Microsoft doporučuje používat verzi Conda, protože balíčky conda obvykle obsahují předem připravené binární soubory, které zajistí spolehlivější instalaci.
>
> Další informace najdete v tématu [Principy conda a PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Pokud chcete zjistit, jestli je vaše závislost k dispozici prostřednictvím Conda, použijte `conda search <package-name>` příkaz nebo použijte indexy balíčku v [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) a [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Pomocí souboru závislostí můžete vytvořit objekt prostředí a uložit ho do svého pracovního prostoru pro budoucí použití:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Důkladnou diskuzi o používání a přizpůsobení prostředí Pythonu s Azure Machine Learning najdete v tématu [vytvoření & použití softwarových prostředí v Azure Machine Learning](../articles/machine-learning/how-to-use-environments.md)

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](../articles/machine-learning/how-to-deploy-custom-docker-image.md).


Následující příklad ukazuje načtení prostředí z pracovního prostoru a jeho následné použití s konfigurací odvození:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Další informace o prostředích najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](../articles/machine-learning/how-to-use-environments.md).

Další informace o konfiguraci odvození naleznete v dokumentaci třídy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

## <a name="choose-a-compute-target"></a>Zvolit cíl výpočetní technologie


[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]



## <a name="define-a-deployment-configuration"></a>Definování konfigurace nasazení

Před nasazením modelu je nutné definovat konfiguraci nasazení. *Konfigurace nasazení je specifická pro výpočetní cíl, který bude hostitelem webové služby.* Pokud například nasadíte model místně, je nutné zadat port, kam služba přijímá požadavky. Konfigurace nasazení není součástí vašeho skriptu pro vložení. Slouží k definování charakteristik výpočetního cíle, který bude hostovat model a vstupní skript.

Je také možné, že budete muset vytvořit výpočetní prostředek, pokud například ještě nemáte k vašemu pracovnímu prostoru přidruženou instanci služby Azure Kubernetes (AKS).

Následující tabulka uvádí příklad vytvoření konfigurace nasazení pro každý cíl služby Compute:

| Cílový výpočetní objekt | Příklad konfigurace nasazení |
| ----- | ----- |
| Místní | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Třídy pro místní, Azure Container Instances a webové služby AKS lze importovat z `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```


## <a name="deploy-your-model"></a>Nasazení modelu

Teď jste připraveni nasadit svůj model. Následující příklad ukazuje místní nasazení. Syntaxe se bude lišit v závislosti na cílovém výpočetním cíli, který jste zvolili v předchozím kroku.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace najdete v dokumentaci pro [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)a [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).


## <a name="delete-resources"></a>Odstranění prostředků

Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()` .
Pokud chcete odstranit registrovaný model, použijte `model.delete()` .

Další informace najdete v dokumentaci pro [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) a [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).


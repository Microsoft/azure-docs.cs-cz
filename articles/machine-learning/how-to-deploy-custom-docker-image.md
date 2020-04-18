---
title: Nasazení modelů s vlastní image Dockeru
titleSuffix: Azure Machine Learning
description: Zjistěte, jak používat vlastní základní image Dockeru při nasazování modelů Azure Machine Learning. Zatímco Azure Machine Learning poskytuje výchozí základní image pro vás, můžete také použít vlastní základní image.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: a237beb72e35a236e353c58db520a8d611fdfdcd
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617998"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Nasazení modelu pomocí vlastní základní image Dockeru
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak používat vlastní základní bitovou kopii Dockeru při nasazování trénovacích modelů pomocí Azure Machine Learning.

Když nasadíte trénovaný model do webové služby nebo zařízení IoT Edge, vytvoří se balíček, který obsahuje webový server pro zpracování příchozích požadavků.

Azure Machine Learning poskytuje výchozí základní image Dockeru, takže se nemusíte starat o jeho vytvoření. Prostředí Azure Machine __Learning__ můžete také použít k výběru konkrétní základní bitové kopie nebo použít vlastní image, kterou zadáte.

Základní bitová kopie se používá jako výchozí bod při vytvoření bitové kopie pro nasazení. Poskytuje základní operační systém a součásti. Proces nasazení pak přidá další součásti, jako je například váš model, prostředí conda a další prostředky, do bitové kopie před jeho nasazením.

Obvykle vytvoříte vlastní základní bitovou kopii, když chcete pomocí Dockeru spravovat vaše závislosti, udržovat přísnější kontrolu nad verzemi komponent nebo ušetřit čas během nasazení. Můžete například chtít standardizovat na konkrétní verzi Pythonu, Conda nebo jiné součásti. Můžete také nainstalovat software vyžadované modelem, kde proces instalace trvá dlouhou dobu. Instalace softwaru při vytváření základní bitové kopie znamená, že jej nemusíte instalovat pro každé nasazení.

> [!IMPORTANT]
> Při nasazování modelu nelze přepsat základní součásti, jako je například webový server nebo součásti IoT Edge. Tyto součásti poskytují známé pracovní prostředí, které je testováno a podporováno společností Microsoft.

> [!WARNING]
> Společnost Microsoft nemusí být schopna pomoci při řešení problémů způsobených vlastní bitovou kopii. Pokud narazíte na problémy, můžete být požádáni o použití výchozí obrázek nebo jeden z obrázků Microsoft poskytuje, aby zjistili, zda je problém specifický pro váš obrázek.

Tento dokument je rozdělen do dvou částí:

* Vytvoření vlastní základní image: Poskytuje informace správcům a devops o vytvoření vlastní image a konfiguraci ověřování pro registr kontejnerů Azure pomocí rozhraní příkazového příkazu Konstru Azure a rozhraní příkazového příkazu k machine learningu.
* Nasazení modelu pomocí vlastní základní image: Poskytuje informace datovým vědcům a technikům DevOps / ML o použití vlastních bitových kopií při nasazování trénovaného modelu z sady ClI pythonu SDK nebo ML.

## <a name="prerequisites"></a>Požadavky

* Pracovní skupina Azure Machine Learning. Další informace naleznete v článku [Vytvoření pracovního prostoru.](how-to-manage-workspace.md)
* Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Rozšíření cli pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
* [Registr kontejnerů Azure](/azure/container-registry) nebo jiný registr Dockeru, který je přístupný na internetu.
* Kroky v tomto dokumentu předpokládají, že jste obeznámeni s vytvářením a používáním objektu __konfigurace odvození__ jako součást nasazení modelu. Další informace naleznete v části "připravit nasazení" [kde nasadit a jak](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-base-image"></a>Vytvoření vlastní základní bitové kopie

Informace v této části předpokládá, že používáte Azure Container Registry k ukládání ibi Dockeru. Při plánování vytváření vlastních bitových kopií pro Azure Machine Learning použijte následující kontrolní seznam:

* Použijete registr kontejnerů Azure vytvořený pro pracovní prostor Azure Machine Learning nebo samostatný registr kontejnerů Azure?

    Při použití bitových kopií uložených v __registru kontejneru pro pracovní prostor__není nutné ověřovat do registru. Ověřování zpracovává pracovní prostor.

    > [!WARNING]
    > Azure Container Registry pro váš pracovní prostor se __vytvoří při prvním trénování nebo nasazování modelu__ pomocí pracovního prostoru. Pokud jste vytvořili nový pracovní prostor, ale ne trénovaný nebo vytvořený model, žádný Registr kontejnerů Azure bude existovat pro pracovní prostor.

    Informace o načtení názvu registru kontejnerů Azure pro váš pracovní prostor najdete v části [Získat název registru kontejneru](#getname) v tomto článku.

    Při použití bitových kopií uložených v __samostatném registru kontejneru__budete muset nakonfigurovat instanční objekt, který má alespoň přístup pro čtení. Potom zadejte ID instančního objektu (uživatelské jméno) a heslo pro každého, kdo používá obrázky z registru. Výjimkou je, pokud provedete registr kontejneru veřejně přístupné.

    Informace o vytvoření privátního registru kontejnerů Azure najdete v [tématu Vytvoření registru privátního kontejneru](/azure/container-registry/container-registry-get-started-azure-cli).

    Informace o použití instancí s Azure Container Registry najdete v tématu [ověřování registru kontejnerů Azure s instančními objekty](/azure/container-registry/container-registry-auth-service-principal).

* Azure Container Registry a informace o image: Zadejte název bitové kopie komukoli, kdo ho potřebuje používat. Například bitová `myimage`kopie s názvem `myregistry`, uložená `myregistry.azurecr.io/myimage` v registru s názvem , je odkazována jako při použití bitové kopie pro nasazení modelu

* Požadavky na image: Azure Machine Learning podporuje jenom image Dockeru, které poskytují následující software:

    * Ubuntu 16.04 nebo vyšší.
    * Conda 4.5.# nebo vyšší.
    * Python 3.5.# nebo 3.6.#.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Získání informací o registru kontejneru

V této části se dozvíte, jak získat název registru kontejnerů Azure pro pracovní prostor Azure Machine Learning.

> [!WARNING]
> Azure Container Registry pro váš pracovní prostor se __vytvoří při prvním trénování nebo nasazování modelu__ pomocí pracovního prostoru. Pokud jste vytvořili nový pracovní prostor, ale ne trénovaný nebo vytvořený model, žádný Registr kontejnerů Azure bude existovat pro pracovní prostor.

Pokud jste už vycvičili nebo nasadili modely pomocí Azure Machine Learning, byl pro váš pracovní prostor vytvořen registr kontejnerů. Chcete-li najít název tohoto registru kontejneru, postupujte takto:

1. Otevřete nové prostředí nebo příkazový řádek a k ověření předplatného Azure použijte následující příkaz:

    ```azurecli-interactive
    az login
    ```

    Postupujte podle pokynů k ověření předplatného.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Pomocí následujícího příkazu zobrazíte seznam registru kontejnerů pro pracovní prostor. Nahraďte `<myworkspace>` název pracovního prostoru Azure Machine Learning. Nahraďte `<resourcegroup>` skupinou prostředků Azure, která obsahuje váš pracovní prostor:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Vrácené informace jsou podobné následujícímu textu:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Hodnota `<registry_name>` je název registru kontejnerů Azure pro váš pracovní prostor.

### <a name="build-a-custom-base-image"></a>Vytvoření vlastní základní bitové kopie

Kroky v této části návod k vytvoření vlastní image Dockeru v registru kontejnerů Azure.

1. Vytvořte nový textový `Dockerfile`soubor s názvem a jako obsah použijte následující text:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Z prostředí nebo příkazového řádku použijte následující ověření do registru kontejnerů Azure. `<registry_name>` Nahraďte název registru kontejneru, do kterého chcete bitovou kopii uložit:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Chcete-li soubor Dockerfile nahrát a sestavit, použijte následující příkaz. Nahraďte `<registry_name>` název registru kontejneru, do kterého chcete bitovou kopii uložit:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > V tomto příkladu `:v1` je na obraz použita značka. Pokud není k dispozici žádná `:latest` značka, použije se značka.

    Během procesu sestavení jsou informace přenášeny datovým proudem zpět do příkazového řádku. Pokud je sestavení úspěšné, zobrazí se zpráva podobná následujícímu textu:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Další informace o vytváření ibi pomocí registru kontejnerů Azure najdete v tématu [sestavení a spuštění image kontejneru pomocí úloh registru kontejnerů Azure](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Další informace o nahrávání existujících ibi do registru kontejnerů Azure najdete v tématu [Push your first image to a private Docker container registry](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-base-image"></a>Použití vlastní základní bitové kopie

Chcete-li použít vlastní obrázek, potřebujete následující informace:

* __Název obrázku__. Například `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` je cesta k základní image Dockeru poskytované společností Microsoft.

    > [!IMPORTANT]
    > U vlastních obrázků, které jste vytvořili, nezapomeňte zahrnout všechny značky, které byly s obrázkem použity. Pokud byl například obrázek vytvořen s určitou značkou, například `:v1`. Pokud jste při vytváření obrazu nepoužili určitou `:latest` značku, byla použita značka.

* Pokud je obrázek v __soukromém úložišti__, potřebujete následující informace:

    * __Adresa__registru . Například, `myregistry.azureecr.io`.
    * Uživatelské __jméno__ a __heslo__ hlavního uživatele služby, které má přístup pro čtení do registru.

    Pokud tyto informace nemáte, obraťte se na správce registru kontejnerů Azure, který obsahuje vaši bitovou kopii.

### <a name="publicly-available-base-images"></a>Veřejně dostupné základní obrázky

Společnost Microsoft poskytuje několik imales dockeru ve veřejně přístupném úložišti, které lze použít s kroky v této části:

| Image | Popis |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Základní image pro Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Obsahuje runtime ONNX pro odvození procesoru |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Obsahuje běhový čas ONNX a CUDA pro GPU. |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Obsahuje ONNX Runtime a TensorRT pro GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Obsahuje onnx runtime a OpenVINO pro Intel<sup> </sup> Vision Accelerator Design založený na Movidius<sup>TM</sup> MyriadX VPUP |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Obsahuje ONNX Runtime a OpenVINO pro Usb flash disky Intel<sup> </sup> Movidius<sup>TM</sup> |

Další informace o základních bitových kopiích onnx runtime naleznete v [části ONNX Runtime dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) v úložišti GitHub.

> [!TIP]
> Vzhledem k tomu, že tyto obrázky jsou veřejně dostupné, nemusíte při jejich používání zadávajít adresu, uživatelské jméno ani heslo.

Další informace najdete v tématu [kontejnery Azure Machine Learning](https://github.com/Azure/AzureML-Containers).

> [!TIP]
>__Pokud váš model trénovaný na Azure Machine Learning Compute__, pomocí verze __1.0.22 nebo vyšší__ azure machine learning sdk, image se vytvoří během školení. Chcete-li zjistit název tohoto `run.properties["AzureML.DerivedImageName"]`obrázku, použijte . Následující příklad ukazuje, jak používat tento obrázek:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Použití image se sadou Azure Machine Learning SDK

Chcete-li použít bitovou kopii uloženou v **registru kontejnerů Azure pro váš pracovní prostor**nebo registr **kontejnerů, který je veřejně přístupný**, nastavte následující atributy [prostředí:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)

+ `docker.enabled=True`
+ `docker.base_image`: Nastavte na registr a cestu k bitové kopii.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Chcete-li použít bitovou kopii z __registru soukromého kontejneru,__ která není ve vašem pracovním prostoru, musíte zadat `docker.base_image_registry` adresu úložiště a uživatelské jméno a heslo:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Je nutné přidat azureml výchozí hodnoty s verzí >= 1.0.45 jako pip závislost. Tento balíček obsahuje funkce potřebné k hostování modelu jako webové služby. Musíte také nastavit inferencing_stack_version vlastnost v prostředí na "nejnovější", to bude instalovat konkrétní apt balíčky potřebné pro webovou službu. 

Po definování prostředí jej použijte s objektem [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) k definování prostředí odvození, ve kterém bude model a webová služba spuštěna.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

V tomto okamžiku můžete pokračovat v nasazení. Například následující fragment kódu by nasadil webovou službu místně pomocí konfigurace odvození a vlastní image:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace o nasazení najdete v [tématu Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

Další informace o přizpůsobení prostředí Pythonu najdete v [tématu Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Použití obrázku s cli machine learningu

> [!IMPORTANT]
> V současné době machine learning CLI můžete použít image z registru kontejnerů Azure pro váš pracovní prostor nebo veřejně přístupné repozitáře. Nelze použít obrázky ze samostatných soukromých registrů.

Před nasazením modelu pomocí cli machine learningu vytvořte [prostředí,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) které používá vlastní image. Pak vytvořte konfigurační soubor odvození, který odkazuje na prostředí. Můžete také definovat prostředí přímo v konfiguračním souboru odvození. Následující dokument JSON ukazuje, jak odkazovat na bitovou kopii v registru veřejných kontejnerů. V tomto příkladu je prostředí definováno vřádkem:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Tento soubor se `az ml model deploy` používá s příkazem. Parametr `--ic` se používá k určení konfiguračního souboru odvození.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Další informace o nasazení modelu pomocí CLI ML najdete v části "registrace modelu, profilování a nasazení" [rozšíření příkazového příkazu pro Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) článku.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [tom, kam a jak nasadit](how-to-deploy-and-where.md).
* Naučte se [trénovat a nasazovat modely strojového učení pomocí Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).

---
title: Nasazení modelů s vlastní imagí Docker
titleSuffix: Azure Machine Learning
description: Naučte se používat vlastní základní image Docker k nasazení modelů Azure Machine Learning. I když Azure Machine Learning k dispozici výchozí základní image, můžete použít také vlastní základní image.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 4a9e374923f6317f7a325979dca1810fad91aeb6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209461"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Nasazení modelu pomocí vlastního obrázku Docker Base

Naučte se používat vlastní základní image Docker při nasazování vycvičených modelů pomocí Azure Machine Learning.

Azure Machine Learning použije výchozí základní image Docker, pokud není zadaný žádný. Můžete najít konkrétní bitovou kopii Docker použitou v nástroji `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . Pomocí Azure Machine Learning __prostředí__ můžete také vybrat konkrétní základní bitovou kopii nebo použít vlastní, kterou zadáte.

Základní bitová kopie se používá jako výchozí bod, když se pro nasazení vytvoří obrázek. Poskytuje základní operační systém a součásti. Proces nasazení pak do image přidá další součásti, jako je model, prostředí conda a další prostředky.

Obvykle vytvoříte vlastní základní bitovou kopii, pokud chcete použít Docker ke správě závislostí, udržovat užší kontrolu nad verzemi součástí nebo ušetřit čas během nasazování. Možná budete chtít nainstalovat i software vyžadovaný modelem, kdy proces instalace trvá dlouho. Instalace softwaru při vytváření základní image znamená, že ji nemusíte instalovat pro každé nasazení.

> [!IMPORTANT]
> Při nasazení modelu nelze přepsat základní komponenty, jako jsou například webové servery nebo součásti IoT Edge. Tyto komponenty poskytují známé pracovní prostředí, které Microsoft testuje a podporuje.

> [!WARNING]
> Společnost Microsoft možná nebude schopna pomoct řešit problémy způsobené vlastní imagí. Pokud narazíte na problémy, můžete být požádáni, abyste použili výchozí Image nebo jednu z imagí, které Microsoft poskytuje, abyste zjistili, jestli je problém specifický pro vaši image.

Tento dokument je rozdělen do dvou částí:

* Vytvoření vlastní základní Image: poskytuje informace správcům a DevOpsům při vytváření vlastní image a konfiguraci ověřování pro Azure Container Registry pomocí rozhraní příkazového řádku Azure CLI a Machine Learning CLI.
* Nasazení modelu pomocí vlastní základní Image: poskytuje informace pro odborníky na data a inženýry DevOps/ML na používání vlastních imagí při nasazení vyučeného modelu ze sady Python SDK nebo ML CLI.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](how-to-manage-workspace.md) .
* [Sada Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). 
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)
* [Rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
* [Azure Container Registry](../container-registry/index.yml) nebo jiný registr Docker, který je přístupný na internetu.
* Kroky v tomto dokumentu předpokládají, že máte zkušenosti s vytvářením a používáním objektu __Konfigurace odvození__ jako součást nasazení modelu. Další informace najdete v tématu [kam nasadit a jak](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Vytvoření vlastní základní image

Informace v této části předpokládají, že používáte Azure Container Registry k ukládání imagí Docker. Při plánování vytváření vlastních imagí pro Azure Machine Learning použijte následující kontrolní seznam:

* Použijete Azure Container Registry vytvořenou pro Azure Machine Learning pracovní prostor nebo samostatný Azure Container Registry?

    Při použití imagí uložených v __registru kontejnerů pro pracovní prostor__ není nutné provádět ověřování v registru. Ověřování je zpracováváno pracovním prostorem.

    > [!WARNING]
    > Azure Container Registry pro váš pracovní prostor se __vytvoří při prvním spuštění modelu nebo nasazení modelu__ pomocí pracovního prostoru. Pokud jste vytvořili nový pracovní prostor, ale nevyškolený nebo nevytvořil model, nebude pro tento pracovní prostor existovat žádná Azure Container Registry.

    Při použití imagí uložených v __samostatném registru kontejnerů__ budete muset nakonfigurovat instanční objekt, který má alespoň přístup pro čtení. Pak zadáte ID objektu služby (Username) a heslo všem, kdo používá image z registru. Výjimkou je případ, kdy je v registru kontejnerů zpřístupněný veřejně přístupný.

    Informace o vytváření privátních Azure Container Registry najdete v tématu [vytvoření soukromého registru kontejnerů](../container-registry/container-registry-get-started-azure-cli.md).

    Informace o používání instančních objektů s Azure Container Registry najdete v tématu [ověřování Azure Container Registry pomocí instančních objektů](../container-registry/container-registry-auth-service-principal.md).

* Informace o Azure Container Registry a obrázku: zadejte název image pro kohokoli, kdo ji musí použít. Například Image s názvem, která `myimage` je uložena v registru s názvem `myregistry` , je odkazována jako `myregistry.azurecr.io/myimage` při použití image pro nasazení modelu.

### <a name="image-requirements"></a>Požadavky image

Azure Machine Learning podporuje pouze image Docker, které poskytují následující software:
* Ubuntu 16,04 nebo vyšší.
* Conda 4.5. # nebo vyšší.
* Python 3.5 +.

Pokud chcete použít datové sady, nainstalujte prosím balíček libfuse-dev. Nezapomeňte také nainstalovat jakékoli balíčky uživatelských prostorů, které budete možná potřebovat.

Azure ML udržuje sadu základních imagí CPU a GPU publikovaných v Microsoft Container Registry, které můžete volitelně využít (nebo odkazovat) místo vytvoření vlastní image. Pokud chcete zobrazit fázemi pro tyto image, přečtěte si v úložišti GitHub [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) .

V případě imagí GPU teď Azure ML v současnosti nabízí základní image cuda9 i cuda10. Hlavní závislosti nainstalované v těchto základních bitových kopiích jsou:

| Závislosti | IntelMPI procesor | CPU OpenMP | Grafický procesor IntelMPI | PROCESOR OpenMP |
| --- | --- | --- | --- | --- |
| miniconda | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 |
| MPI | intelmpi = = 2018.3.222 |OpenMP a = = 3.1.2 |intelmpi = = 2018.3.222| OpenMP a = = 3.1.2 |
| CUDA | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2,4 | 2,4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

Image CPU jsou sestavené z Ubuntu 16.04. Obrázky GPU pro cuda9 jsou sestavené z NVIDIA/CUDA: 9.0-cudnn7-devel-Ubuntu 16.04. Image GPU pro cuda10 jsou sestavené z NVIDIA/CUDA: 10.0-cudnn7-devel-Ubuntu 16.04.
<a id="getname"></a>

> [!IMPORTANT]
> Pokud používáte vlastní image Docker, doporučujeme připnout verze balíčků, abyste lépe zajistili reprodukovatelnost.

### <a name="get-container-registry-information"></a>Získat informace o registru kontejneru

V této části se dozvíte, jak získat název Azure Container Registry pro pracovní prostor Azure Machine Learning.

> [!WARNING]
> Azure Container Registry pro váš pracovní prostor se __vytvoří při prvním spuštění modelu nebo nasazení modelu__ pomocí pracovního prostoru. Pokud jste vytvořili nový pracovní prostor, ale nevyškolený nebo nevytvořil model, nebude pro tento pracovní prostor existovat žádná Azure Container Registry.

Pokud jste už provedli nebo nasadili modely pomocí Azure Machine Learning, vytvořil se registr kontejneru pro váš pracovní prostor. Chcete-li najít název tohoto registru kontejneru, použijte následující postup:

1. Otevřete nové prostředí nebo příkazový řádek a pomocí následujícího příkazu proveďte ověření u svého předplatného Azure:

    ```azurecli-interactive
    az login
    ```

    Postupujte podle výzev k ověření předplatného.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. K vypsání registru kontejneru pro pracovní prostor použijte následující příkaz. Nahraďte `<myworkspace>` názvem svého pracovního prostoru Azure Machine Learning. Nahraďte `<resourcegroup>` skupinou prostředků Azure, která obsahuje váš pracovní prostor:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Vrácené informace se podobají následujícímu textu:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>`Hodnota je název Azure Container registry pro váš pracovní prostor.

### <a name="build-a-custom-base-image"></a>Vytvoření vlastní základní image

Postup v této části vás seznámí s vytvořením vlastní image Docker ve vašem Azure Container Registry. Ukázku fázemi najdete v úložišti GitHub [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) .

1. Vytvořte nový textový soubor s názvem `Dockerfile` a jako obsah použijte následující text:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. V prostředí nebo příkazovém řádku použijte následující příkaz k ověření Azure Container Registry. Nahraďte `<registry_name>` názvem registru kontejneru, do kterého chcete uložit Image:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Pokud chcete nahrát souboru Dockerfile a sestavit ho, použijte následující příkaz. Nahraďte `<registry_name>` názvem registru kontejneru, do kterého chcete uložit Image:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > V tomto příkladu `:v1` je použita značka pro obrázek. Pokud není zadána žádná značka, `:latest` je použita značka.

    Během procesu sestavení jsou informace streamování do příkazového řádku. Pokud je sestavení úspěšné, zobrazí se zpráva podobná následujícímu textu:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Další informace o vytváření imagí pomocí Azure Container Registry najdete v tématu [sestavení a spuštění image kontejneru pomocí Azure Container Registry úloh](../container-registry/container-registry-quickstart-task-cli.md) .

Další informace o nahrání existujících imagí do Azure Container Registry najdete v tématu odeslání [prvního obrázku do privátního registru kontejneru Docker](../container-registry/container-registry-get-started-docker-cli.md).

## <a name="use-a-custom-base-image"></a>Použití vlastní základní image

Pokud chcete použít vlastní image, potřebujete tyto informace:

* __Název Image__ Například `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` je cesta k jednoduché imagi Docker, kterou poskytuje Microsoft.

    > [!IMPORTANT]
    > U vlastních imagí, které jste vytvořili, nezapomeňte zahrnout všechny značky, které se s imagí používaly. Například pokud se vaše image vytvořila s konkrétní značkou, jako je například `:v1` . Pokud jste při vytváření image nepoužili konkrétní značku, `:latest` použila se značka.

* Pokud je obrázek v __privátním úložišti__, budete potřebovat následující informace:

    * __Adresa__ registru. Například, `myregistry.azureecr.io`.
    * __Uživatelské jméno__ a __heslo__ instančního objektu, které mají přístup pro čtení k registru.

    Pokud tyto informace nemáte, obraťte se na správce Azure Container Registry, který obsahuje vaši image.

### <a name="publicly-available-base-images"></a>Veřejně dostupné základní image

Společnost Microsoft poskytuje několik imagí Docker pro veřejně dostupné úložiště, které je možné použít s kroky v této části:

| Image | Popis |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Základní obrázek pro Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Obsahuje ONNX runtime pro PROCESORové Inferencing |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Obsahuje modul runtime ONNX a CUDA pro GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Obsahuje ONNX runtime a TensorRT pro GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Obsahuje ONNX runtime a OpenVINO pro <sup></sup> Návrh akcelerátoru Intel Vision na základě Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Obsahuje ONNX runtime a OpenVINO pro Intel <sup></sup> Movidius<sup>TM</sup> USB Stick |

Další informace o základních imagích modulu runtime ONNX naleznete v [části ONNX runtime souboru Dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) v úložišti GitHub.

> [!TIP]
> Vzhledem k tomu, že tyto image jsou veřejně dostupné, nemusíte při jejich používání zadávat adresu, uživatelské jméno ani heslo.

Další informace najdete v tématu [Azure Machine Learning úložiště kontejnerů](https://github.com/Azure/AzureML-Containers) na GitHubu.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Použití obrázku s Azure Machine Learning SDK

Pokud chcete použít image uloženou v **Azure Container registry pro váš pracovní prostor** nebo **kontejner kontejneru, který je veřejně přístupný**, nastavte následující atributy [prostředí](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) :

+ `docker.enabled=True`
+ `docker.base_image`: Nastavte na registr a cestu k imagi.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Pokud chcete použít image z __privátního registru kontejnerů__ , který není ve vašem pracovním prostoru, musíte použít `docker.base_image_registry` k zadání adresy úložiště a uživatelského jména a hesla:

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

Je nutné přidat AzureML-Defaults s Version >= 1.0.45 jako závislost v PIP. Tento balíček obsahuje funkce potřebné pro hostování modelu jako webové služby. Musíte také nastavit vlastnost inferencing_stack_version v prostředí na "nejnovější". tím se nainstalují konkrétní balíčky apt, které webová služba potřebuje. 

Po definování prostředí jej pomocí objektu [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) definujte odvozená prostředí, ve kterém se model a webová služba spustí.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

V tomto okamžiku můžete pokračovat v nasazení. Například následující fragment kódu by nasadil webovou službu místně pomocí odvození konfigurace a vlastní image:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Další informace o nasazení najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

Další informace o přizpůsobení prostředí Pythonu najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Použití obrázku s Machine Learning CLI

> [!IMPORTANT]
> V současné době může Machine Learning CLI používat image z Azure Container Registry pro váš pracovní prostor nebo veřejně přístupná úložiště. Nemůže použít obrázky ze samostatných privátních registrů.

Před nasazením modelu pomocí Machine Learning CLI vytvořte [prostředí](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) , které používá vlastní image. Pak vytvořte konfigurační soubor odvození, který odkazuje na prostředí. Prostředí můžete také definovat přímo v konfiguračním souboru odvození. Následující dokument JSON ukazuje, jak odkazovat na obrázek ve veřejném registru kontejneru. V tomto příkladu je prostředí definované jako vložené:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
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

Tento soubor se používá spolu s `az ml model deploy` příkazem. `--ic`Parametr se používá k určení konfiguračního souboru odvození.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Další informace o nasazení modelu pomocí rozhraní příkazového řádku (ML) najdete v části "registrace modelů, profilace a nasazení" v [rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) článek.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [tom, kde nasadit a jak](how-to-deploy-and-where.md).
* Naučte se [, jak pomocí Azure Pipelines naučit a nasazovat modely strojového učení](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops&preserve-view=true).
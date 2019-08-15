---
title: Nasazení modelů pomocí vlastní image Docker
titleSuffix: Azure Machine Learning service
description: Naučte se používat vlastní image Docker při nasazení modelů služby Azure Machine Learning. Při nasazování proučeného modelu se vytvoří image Docker pro hostování image, webového serveru a dalších součástí potřebných ke spuštění služby. I když služba Azure Machine Learning poskytuje pro vás výchozí image, můžete také použít vlastní image.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/11/2019
ms.openlocfilehash: f41ccef7803366e63247e6862c59ddb983527d26
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990530"
---
# <a name="deploy-a-model-by-using-a-custom-docker-image"></a>Nasazení modelu pomocí vlastní image Docker

Naučte se používat vlastní image Docker při nasazení vycvičených modelů pomocí služby Azure Machine Learning.

Když nasadíte školený model do webové služby nebo IoT Edge zařízení, vytvoří se image Docker. Tato image obsahuje model, prostředí conda a assety potřebné k používání modelu. Obsahuje taky webový server, který zpracovává příchozí požadavky při nasazení jako webovou službu a součásti potřebné pro práci s Azure IoT Hub.

Služba Azure Machine Learning poskytuje výchozí snímek Docker, takže se o jeho vytvoření nemusíte starat. Můžete také použít vlastní image, kterou vytvoříte jako _základní image_. Základní bitová kopie se používá jako výchozí bod, když se pro nasazení vytvoří obrázek. Poskytuje základní operační systém a součásti. Proces nasazení poté přidá další součásti, jako je model, prostředí conda a další prostředky, do image před jejich nasazením.

Obvykle vytvoříte vlastní bitovou kopii, když chcete řídit verze součástí nebo ušetřit čas během nasazování. Například můžete chtít standardizovat konkrétní verzi Pythonu, conda nebo jiné součásti. Možná budete chtít nainstalovat i software vyžadovaný modelem, kdy proces instalace trvá dlouho. Instalace softwaru při vytváření základní image znamená, že ji nemusíte instalovat pro každé nasazení.

> [!IMPORTANT]
> Při nasazení modelu nelze přepsat základní komponenty, jako jsou například webové servery nebo součásti IoT Edge. Tyto komponenty poskytují známé pracovní prostředí, které Microsoft testuje a podporuje.

> [!WARNING]
> Společnost Microsoft možná nebude schopna pomoct řešit problémy způsobené vlastní imagí. Pokud narazíte na problémy, můžete být požádáni, abyste použili výchozí Image nebo jednu z imagí, které Microsoft poskytuje, abyste zjistili, jestli je problém specifický pro vaši image.

Tento dokument je rozdělen do dvou částí:

* Vytvoření vlastní image: Poskytuje informace správcům a DevOpsům při vytváření vlastní image a konfiguraci ověřování pro Azure Container Registry pomocí rozhraní příkazového řádku Azure CLI a Machine Learning CLI.
* Použít vlastní image: Poskytuje informace pro odborníky přes data a DevOps/MLOps při používání vlastních imagí při nasazování proučeného modelu ze sady Python SDK nebo ML CLI.

## <a name="prerequisites"></a>Požadavky

* Pracovní skupina služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](how-to-manage-workspace.md) .
* [Sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
* [Azure Container Registry](/azure/container-registry) nebo jiný registr Docker, který je přístupný na internetu.
* Kroky v tomto dokumentu předpokládají, že máte zkušenosti s vytvářením a používáním objektu __Konfigurace odvození__ jako součást nasazení modelu. Další informace najdete v části Příprava na nasazení v tématu [nasazení a jak](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Vytvoření vlastní image

Informace v této části předpokládají, že používáte Azure Container Registry k ukládání imagí Docker. Při plánování vytváření vlastních imagí pro službu Azure Machine Learning použijte následující kontrolní seznam:

* Použijete Azure Container Registry vytvořenou pro pracovní prostor služby Azure Machine Learning nebo samostatný Azure Container Registry?

    Při použití imagí uložených v __registru kontejnerů pro pracovní prostor__není nutné provádět ověřování v registru. Ověřování je zpracováváno pracovním prostorem.

    > [!WARNING]
    > Azure Container Rzegistry pro váš pracovní prostor se __vytvoří při prvním spuštění modelu nebo nasazení modelu__ pomocí pracovního prostoru. Pokud jste vytvořili nový pracovní prostor, ale nevyškolený nebo nevytvořil model, nebude pro tento pracovní prostor existovat žádná Azure Container Registry.

    Informace o načtení názvu Azure Container Registry pro váš pracovní prostor najdete v části [získání názvu registru kontejneru](#getname) v tomto článku.

    Při použití imagí uložených v __samostatném registru kontejnerů__budete muset nakonfigurovat instanční objekt, který má alespoň přístup pro čtení. Pak zadáte ID objektu služby (Username) a heslo všem, kdo používá image z registru. Výjimkou je případ, kdy je v registru kontejnerů zpřístupněný veřejně přístupný.

    Informace o vytváření privátních Azure Container Registry najdete v tématu [vytvoření soukromého registru kontejnerů](/azure/container-registry/container-registry-get-started-azure-cli).

    Informace o používání instančních objektů s Azure Container Registry najdete v tématu [ověřování Azure Container Registry pomocí instančních objektů](/azure/container-registry/container-registry-auth-service-principal).

* Informace o Azure Container Registry a imagi: Zadejte název image pro kohokoli, kdo ho musí používat. Například Image s názvem `myimage`, která je uložena v registru s názvem `myregistry`, je odkazována jako `myregistry.azurecr.io/myimage` při použití image pro nasazení modelu.

* Požadavky na bitovou kopii: Služba Azure Machine Learning podporuje jenom image Docker, které poskytují následující software:

    * Ubuntu 16,04 nebo vyšší.
    * Conda 4.5. # nebo vyšší.
    * Python 3.5. # nebo 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Získat informace o registru kontejneru

V této části se dozvíte, jak získat název Azure Container Registry pro pracovní prostor služby Azure Machine Learning.

> [!WARNING]
> Azure Container Registry pro váš pracovní prostor se __vytvoří při prvním spuštění modelu nebo nasazení modelu__ pomocí pracovního prostoru. Pokud jste vytvořili nový pracovní prostor, ale nevyškolený nebo nevytvořil model, nebude pro tento pracovní prostor existovat žádná Azure Container Registry.

Pokud jste už provedli vyškolené nebo nasazené modely pomocí služby Azure Machine Learning, vytvořil se pro váš pracovní prostor registr kontejnerů. Chcete-li najít název tohoto registru kontejneru, použijte následující postup:

1. Otevřete nové prostředí nebo příkazový řádek a pomocí následujícího příkazu proveďte ověření u svého předplatného Azure:

    ```azurecli-interactive
    az login
    ```

    Postupujte podle výzev k ověření předplatného.

2. K vypsání registru kontejneru pro pracovní prostor použijte následující příkaz. Nahraďte `<myworkspace>` názvem pracovního prostoru služby Azure Machine Learning. Nahraďte `<resourcegroup>` skupinou prostředků Azure, která obsahuje váš pracovní prostor:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

    Vrácené informace se podobají následujícímu textu:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` Hodnota je název Azure Container registry pro váš pracovní prostor.

### <a name="build-a-custom-image"></a>Vytvoření vlastní image

Postup v této části vás seznámí s vytvořením vlastní image Docker ve vašem Azure Container Registry.

1. Vytvořte nový textový soubor s názvem `Dockerfile`a jako obsah použijte následující text:

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

2. V prostředí nebo příkazovém řádku použijte následující příkaz k ověření Azure Container Registry. `<registry_name>` Nahraďte názvem registru kontejneru, do kterého chcete uložit Image:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Pokud chcete nahrát souboru Dockerfile a sestavit ho, použijte následující příkaz. Nahraďte `<registry_name>` názvem registru kontejneru, do kterého chcete uložit Image:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Během procesu sestavení jsou informace streamování do příkazového řádku. Pokud je sestavení úspěšné, zobrazí se zpráva podobná následujícímu textu:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Další informace o vytváření imagí pomocí Azure Container Registry najdete v tématu [sestavení a spuštění image kontejneru pomocí Azure Container Registry úloh](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli) .

Další informace o nahrání existujících imagí do Azure Container Registry najdete v tématu odeslání [prvního obrázku do privátního registru kontejneru Docker](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Použití vlastní image

Pokud chcete použít vlastní image, potřebujete tyto informace:

* __Název Image__ Například `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` je cesta k základní imagi Docker, kterou poskytuje Microsoft.
* Pokud je obrázek v privátním __úložišti__, budete potřebovat následující informace:

    * __Adresa__registru. Například, `myregistry.azureecr.io`.
    * __Uživatelské jméno__ a __heslo__ instančního objektu, které mají přístup pro čtení k registru.

    Pokud tyto informace nemáte, obraťte se na správce Azure Container Registry, který obsahuje vaši image.

### <a name="publicly-available-images"></a>Veřejně dostupné obrázky

Společnost Microsoft poskytuje několik imagí Docker pro veřejně dostupné úložiště, které je možné použít s kroky v této části:

| Image | Popis |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Základní image pro službu Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Obsahuje modul runtime ONNX. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Obsahuje součásti modulu runtime ONNX a CUDA. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Obsahuje modul runtime ONNX a TensorRT. |

> [!TIP]
> Vzhledem k tomu, že tyto image jsou veřejně dostupné, nemusíte při jejich používání zadávat adresu, uživatelské jméno ani heslo.

> [!IMPORTANT]
> Image Microsoft, které používají CUDA nebo TensorRT, se musí používat jenom pro Microsoft Azure služby.

> [!TIP]
>__Pokud je váš model vyškolený na Azure Machine Learning výpočetní__výkon, používá __1.0.22 nebo větší__ verzi Azure Machine Learning SDK, během školení se vytvoří obrázek. Chcete-li zjistit název tohoto obrázku, použijte `run.properties["AzureML.DerivedImageName"]`. Následující příklad ukazuje, jak použít tuto bitovou kopii:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Použití obrázku s Azure Machine Learning SDK

Chcete-li použít vlastní image, nastavte `base_image` vlastnost [objektu odvození konfigurace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) na adresu Image:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Tento formát funguje pro bitové kopie uložené v Azure Container Registry pro váš pracovní prostor a Registry kontejnerů, které jsou veřejně přístupné. Například následující kód používá výchozí bitovou kopii od společnosti Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Pokud chcete použít image z __privátního registru kontejnerů__ , který není ve vašem pracovním prostoru, musíte zadat adresu úložiště a uživatelské jméno a heslo:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Použití obrázku s Machine Learning CLI

> [!IMPORTANT]
> V současné době může Machine Learning CLI používat image z Azure Container Registry pro váš pracovní prostor nebo veřejně přístupná úložiště. Nemůže použít obrázky ze samostatných privátních registrů.

Při nasazování modelu pomocí Machine Learning CLI zadáte odvozený konfigurační soubor, který odkazuje na vlastní image. Následující dokument JSON ukazuje, jak odkazovat na obrázek ve veřejném registru kontejneru:

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

Tento soubor se používá spolu s `az ml model deploy` příkazem. `--ic` Parametr se používá k určení konfiguračního souboru odvození.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Další informace o nasazení modelu pomocí rozhraní příkazového řádku najdete v části "registrace modelů, profilace a nasazení" v článku [rozšíření CLI pro službu Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) .

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [tom, kde nasadit a jak](how-to-deploy-and-where.md).
* Naučte se [, jak pomocí Azure Pipelines naučit a nasazovat modely strojového učení](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).

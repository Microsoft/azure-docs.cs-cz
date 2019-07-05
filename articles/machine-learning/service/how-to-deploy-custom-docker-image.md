---
title: Jak nasadit model s použitím vlastní image Dockeru
titleSuffix: Azure Machine Learning service
description: Další informace o použití vlastní image Dockeru při nasazování modelů služby Azure Machine Learning. Při nasazování trénovaného modelu, se vytvoří image Dockeru do hostitele bitovou kopii, webový server a další komponenty potřebné ke spuštění služby. Zatímco služba Azure Machine Learning poskytuje výchozí image pro vás, můžete použít také vlastní image.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/05/2019
ms.openlocfilehash: 29fdb200075a5b5843944a7a890cc2f8ad61f1ee
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543854"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Nasazení modelu používání vlastní image Dockeru

Další informace o použití vlastní image Dockeru při nasazování trénované modely ve službě Azure Machine Learning.

Když nasadíte trénovaného modelu na webovou službu nebo zařízení IoT Edge, se vytvoří image Dockeru. Tato image obsahuje model, prostředí conda a prostředky potřebné k použití modelu. Také obsahuje webový server ke zpracování příchozích požadavků při nasazení jako webové služby a komponenty potřebné pro práci s Azure IoT Hub.

Služba Azure Machine Learning poskytuje výchozí image Dockeru, takže není nutné se starat o vytvoření nového. Můžete také použít vlastní image, kterou vytvoříte jako _základní image_. Základní image se používá jako výchozí bod při vytvoření image pro nasazení. Poskytuje základní operační systém a komponent. Proces nasazení k bitové kopii ještě před nasazením přidá další komponenty, jako je váš model, prostředí conda a dalších zdrojů.

Obvykle vytvoříte vlastní image, pokud chcete řídit verzí komponent nebo ušetřit čas při nasazení. Můžete například chtít standardizovat používání na konkrétní verzi Pythonu, Conda nebo jiné součásti. Můžete také chtít nainstalovat software vyžadované k modelu, kde proces instalace trvá dlouhou dobu. Instalace softwaru při vytváření základní image znamená, že není nutné k instalaci pro každé nasazení.

> [!IMPORTANT]
> Při nasazování modelu, nelze přepsat základní součásti, jako je webový server nebo komponenty IoT Edge. Tyto komponenty nabízejí známé pracovní prostředí, které je testováno a podporované společností Microsoft.

> [!WARNING]
> Microsoft nemusí být schopný vám pomůže vyřešit potíže způsobené aktivitami vlastní image. Pokud narazíte na problémy, může vás požádat použít výchozí image nebo některou k imagí, které společnost Microsoft poskytuje a zjistěte, jestli problém je specifický pro bitové kopie.

Tento dokument je rozdělen do dvou částí:

* Vytvoření vlastní image: Obsahuje informace, které správci a DevOps na vytvoření vlastní image a konfigurace ověřování do služby Azure Container Registry pomocí Azure CLI a Machine Learning CLI.
* Použití vlastní image: Poskytuje informace pro odborníky přes Data a DevOps/MLOps o používání vlastních imagí při nasazování trénovaného modelu ze sady Python SDK nebo rozhraní příkazového řádku ML.

## <a name="prerequisites"></a>Požadavky

* Pracovní skupině služby Azure Machine Learning. Další informace najdete v tématu [vytvořit pracovní prostor](setup-create-workspace.md) článku.
* Azure Machine Learning SDK. Další informace najdete v tématu část Python SDK [vytvořit pracovní prostor](setup-create-workspace.md#sdk) článku.
* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Rozšíření rozhraní příkazového řádku pro službu Azure Machine Learning](reference-azure-machine-learning-cli.md).
* [Azure Container Registry](/azure/container-registry) nebo jiných registru Dockeru, který je přístupný na Internetu.
* Kroky v tomto dokumentu předpokládají, že máte zkušenosti s vytvářením a používáním __odvozování configuration__ objekt jako součást modelu nasazení. Další informace najdete v části "Příprava na nasazení" z [, jak nasadíte a jak](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Vytvoření vlastní image

Informace v této části se předpokládá, že používáte službu Azure Container Registry k ukládání imagí Dockeru. Následující kontrolní seznam použijte k vytvoření vlastní Image pro službu Azure Machine Learning při plánování:

* Použijete Azure Container Registry, vytvoří pro pracovní prostor služby Azure Machine Learning nebo samostatný Azure Container Registry?

    Při použití imagí uložených v __registru kontejneru pro pracovní prostor__, nepotřebujete k ověření do registru. Ověřování zařizuje Služba pracovního prostoru.

    > [!WARNING]
    > Rzegistry kontejneru Azure pro váš pracovní prostor je __vytvoří při prvním trénování nebo nasazení modelu__ používání pracovního prostoru. Pokud vytvořili nový pracovní prostor, ale nejsou školení nebo vytvořili model, pro pracovní prostor nebude existovat žádné Azure Container Registry.

    Informace o načtení název registru kontejneru Azure pro váš pracovní prostor, najdete v článku [název registru kontejneru Get](#getname) části tohoto článku.

    Při použití imagí uložených v __samostatné registr__, budete muset nakonfigurovat hlavní název služby, který má alespoň přístup pro čtení. Potom zadáte ID instančního objektu (uživatelské jméno) a heslo pro každý uživatel, který používá Image z registru. Výjimkou je, pokud provedete do registru kontejneru veřejně přístupná.

    Informace o vytvoření soukromého registru kontejnerů Azure najdete v tématu [vytvořit privátní registr](/azure/container-registry/container-registry-get-started-azure-cli).

    Informace o používání instančních objektů se službou Azure Container Registry najdete v tématu [ověřování Azure Container Registry pomocí instančních objektů](/azure/container-registry/container-registry-auth-service-principal).

* Azure Container Registry a image informace: Zadejte název image všem uživatelům, kterou je potřeba ji použít. Například image s názvem `myimage`, uložené v registru s názvem `myregistry`, je odkazováno jako `myregistry.azurecr.io/myimage` při použití image pro nasazení modelu

* Požadavky na Image: Služba Azure Machine Learning podporuje pouze imagí Dockeru, které poskytují následující software:

    * Ubuntu 16.04 nebo vyšší.
    * Conda 4.5. # nebo vyšší.
    * Python 3.5. # nebo 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Získání informací o registru kontejneru

V této části se dozvíte, jak získat název registru kontejneru Azure pro váš pracovní prostor služby Azure Machine Learning.

> [!WARNING]
> Azure Container Registry pro váš pracovní prostor je __vytvoří při prvním trénování nebo nasazení modelu__ používání pracovního prostoru. Pokud vytvořili nový pracovní prostor, ale nejsou školení nebo vytvořili model, pro pracovní prostor nebude existovat žádné Azure Container Registry.

Pokud jste už vytrénovaný nebo nasadit modely s využitím služby Azure Machine Learning, registr kontejnerů byl vytvořen pro váš pracovní prostor. Chcete-li najít název registru kontejneru, postupujte následovně:

1. Otevřete nové prostředí nebo v příkazovém řádku a použijte následující příkaz k ověření ke svému předplatnému Azure:

    ```azurecli-interactive
    az login
    ```

    Postupujte podle výzev k ověření předplatného.

2. Pomocí následujícího příkazu zobrazíte seznam registru kontejneru pro pracovní prostor. Nahraďte `<myworkspace>` pomocí názvu pracovního prostoru služby Azure Machine Learning. Nahraďte `<resourcegroup>` s vybranou skupinou prostředků Azure, která obsahuje váš pracovní prostor:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    Vrácené informace se podobá následujícímu textu:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` Hodnota je název registru kontejneru Azure pro váš pracovní prostor.

### <a name="build-a-custom-image"></a>Sestavit vlastní image

Kroky v tomto názorném postupu část vytvoření vlastní image Dockeru do služby Azure Container Registry.

1. Vytvořte nový textový soubor s názvem `Dockerfile`a použít jako obsah následující text:

    ```text
    FROM ubuntu:16.04

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y python=3.6 && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Z prostředí nebo v příkazovém řádku použijte následující postup k ověření do služby Azure Container Registry. Nahraďte `<registry_name>` s názvem registru kontejneru, které chcete uložit obrázek v:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Nahrát soubor Dockerfile a sestavte ho, použijte následující příkaz. Nahraďte `<registry_name>` s názvem registru kontejneru, které chcete uložit obrázek v:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Během procesu sestavování informace streamuje vraťte se do příkazového řádku. Pokud sestavení není úspěšné, zobrazí se zpráva podobná následující text:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Další informace o vytváření imagí pomocí služby Azure Container Registry, najdete v části [sestavení a spuštění image kontejneru pomocí Azure Container Registry úlohy](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Další informace o nahrávání existující Image do služby Azure Container Registry, najdete v části [nahrání první image do soukromého registru kontejnerů Dockeru](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Použití vlastní image

Pokud chcete použít vlastní image, budete potřebovat následující informace:

* __Název image__. Například `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` je cesta k základní Image Dockeru poskytnutých microsoftem.
* Pokud se image nachází v __soukromé úložiště__, budete potřebovat následující informace:

    * Registr __adresu__. Například, `myregistry.azureecr.io`.
    * Instanční objekt služby __uživatelské jméno__ a __heslo__ , který má oprávnění ke čtení registru.

    Pokud tyto informace nemáte, mluvte s správce pro registr kontejneru Azure, který obsahuje image.

### <a name="publicly-available-images"></a>Veřejně dostupných imagí

Společnost Microsoft poskytuje několik imagí dockeru na veřejně dostupné úložiště, které lze použít s kroky v této části:

| Image | Popis |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Základní image pro službu Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Obsahuje modul runtime ONNX. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Obsahuje modul ONNX runtime a CUDA komponenty. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Obsahuje modul runtime ONNX a TensorRT. |

> [!TIP]
> Protože tyto Image jsou veřejně dostupné, není potřeba zadejte adresu, uživatelské jméno nebo heslo při jejich používání.

> [!IMPORTANT]
> Microsoft obrazy, které používají CUDA nebo TensorRT musí použít pouze na služby Microsoft Azure.

> [!TIP]
>__Pokud váš model se trénuje na Azure Machine Learning Compute__s použitím __verze 1.0.22 nebo větší__ sady Azure Machine Learning SDK, image se vytvoří během cvičení. Chcete-li zjistit název tohoto obrázku, použijte `run.properties["AzureML.DerivedImageName"]`. Následující příklad ukazuje, jak použít tuto bitovou kopii:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Použít obraz se sadou SDK Azure Machine Learning

Chcete-li použít vlastní image, nastavte `base_image` vlastnost [objekt konfigurace odvození](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) adresu bitové kopie:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Tento formát funguje u obou imagí uložených v Azure Container Registry pro váš pracovní prostor a container registry, které jsou veřejně přístupné. Například následující kód používá výchozí image poskytované společností Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Chcete použít některou image z __privátní registr kontejnerů__ , který není ve vašem pracovním prostoru, musíte zadat adresu úložiště a uživatelské jméno a heslo:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Použít bitovou kopii pomocí rozhraní příkazového řádku Machine Learning

> [!IMPORTANT]
> Aktuálně můžete rozhraní příkazového řádku Machine Learning použít Image ze služby Azure Container Registry pro pracovní prostor nebo úložiště veřejně přístupná. Nelze ho pomocí imagí z samostatné privátních registrů.

Při nasazování modelu pomocí rozhraní příkazového řádku Machine Learning, zadejte konfigurační soubor odvození, který odkazuje na vlastní image. Následující dokument JSON ukazuje, jak odkazovat na image v registru veřejného kontejneru:

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

Tento soubor se používá s `az ml model deploy` příkazu. `--ic` Parametr se používá k určení odvození konfigurační soubor.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Další informace o nasazení model pomocí rozhraní příkazového řádku ML, najdete v části "registrace modelu, profilace a nasazení" [rozšíření rozhraní příkazového řádku pro službu Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) článku.

## <a name="next-steps"></a>Další postup

* Další informace o [, jak nasadíte a jak](how-to-deploy-and-where.md).
* Zjistěte, jak [trénování a nasazovat modely machine learningu pomocí kanálů Azure](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).

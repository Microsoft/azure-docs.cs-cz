---
title: Nasazení modelů ml do Azure Functionsch aplikací (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se používat Azure Machine Learning k nasazení modelu do Azure Functions aplikace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 2f5658d6df2b20e5bce0fab2ca1787ede5ab7883
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540227"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Nasazení modelu Machine Learning do Azure Functions (Preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se, jak nasadit model z Azure Machine Learning jako aplikace Function App v Azure Functions.

> [!IMPORTANT]
> I když jsou všeobecně k dispozici Azure Machine Learning i Azure Functions, možnost zabalit model ze služby Machine Learning for Functions je ve verzi Preview.

Pomocí Azure Machine Learning můžete vytvářet image Docker z školicích modelů strojového učení. Azure Machine Learning teď má funkce Preview k sestavování těchto modelů strojového učení do aplikací Function App, které se dají [nasadit do Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](how-to-manage-workspace.md) .
* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Vyškolený model strojového učení zaregistrovaný ve vašem pracovním prostoru. Pokud model nemáte, použijte [kurz k klasifikaci imagí: výukový model](tutorial-train-models-with-aml.md) pro výuku a registraci k jednomu.

    > [!IMPORTANT]
    > Fragmenty kódu v tomto článku předpokládají, že jste nastavili následující proměnné:
    >
    > * `ws` – váš pracovní prostor Azure Machine Learning.
    > * `model` – registrovaný model, který se nasadí.
    > * `inference_config` – odvození konfigurace modelu.
    >
    > Další informace o nastavení těchto proměnných najdete v tématu [nasazení modelů pomocí Azure Machine Learning](service/how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení

Před nasazením musíte definovat, co je potřeba ke spuštění modelu jako webové služby. Následující seznam popisuje základní položky, které jsou potřeba pro nasazení:

* __Vstupní skript__. Tento skript přijímá požadavky, vyhodnotí požadavek pomocí modelu a vrátí výsledky.

    > [!IMPORTANT]
    > Vstupní skript je specifický pro váš model; musí pochopit formát příchozích dat požadavků, formát dat očekávaných modelem a formát dat vrácených klientům.
    >
    > Pokud jsou data požadavku ve formátu, který model nepoužívá, skript ho může transformovat do přijatelného formátu. Může také transformovat odpověď předtím, než se vrátí do klienta.
    >
    > Ve výchozím nastavení je při balení pro funkce vstup považován za text. Pokud vás zajímá využívání nezpracovaných bajtů vstupu (například pro triggery objektů BLOB), měli byste použít [AMLRequest k přijetí nezpracovaných dat](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#binary-data).


* **Závislosti**, například pomocné skripty nebo balíčky python/conda potřebné ke spuštění skriptu vstupu nebo modelu

Tyto entity jsou zapouzdřeny do __Konfigurace odvození__. Konfigurace odvození odkazuje na skript vstupu a další závislosti.

> [!IMPORTANT]
> Při vytváření odvozených konfigurací pro použití s Azure Functions je nutné použít objekt [prostředí](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . Počítejte s tím, že pokud definujete vlastní prostředí, musíte přidat AzureML-Defaults s Version > = 1.0.45 jako závislost v PIP. Tento balíček obsahuje funkce potřebné pro hostování modelu jako webové služby. Následující příklad ukazuje vytvoření objektu prostředí a jeho použití s odvozenou konfigurací:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Další informace o prostředích najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md).

Další informace o konfiguraci odvození najdete v tématu [nasazení modelů pomocí Azure Machine Learning](service/how-to-deploy-and-where.md).

> [!IMPORTANT]
> Při nasazování do funkcí není nutné vytvářet __konfiguraci nasazení__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instalace balíčku sady SDK Preview pro podporu funkcí

Chcete-li vytvořit balíčky pro Azure Functions, je nutné nainstalovat balíček sady SDK verze Preview.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Vytvoření image

Chcete-li vytvořit bitovou kopii Docker, která je nasazena do Azure Functions, použijte funkci [AzureML. contrib. Functions. Package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) nebo konkrétního balíčku pro aktivační událost, které vás zajímá. Následující fragment kódu ukazuje, jak vytvořit nový balíček s triggerem objektu BLOB z modelu a odvozené konfigurace:

> [!NOTE]
> Fragment kódu předpokládá, že `model` obsahuje registrovaný model a že `inference_config` obsahuje konfiguraci pro odvození prostředí. Další informace najdete v tématu [nasazení modelů pomocí Azure Machine Learning](service/how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Při `show_output=True`se zobrazí výstup procesu Docker buildu. Po dokončení procesu se image vytvoří v Azure Container Registry pro váš pracovní prostor. Po sestavení obrázku se zobrazí umístění v Azure Container Registry. Umístění, které se vrátilo, je ve formátu `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> Balení pro funkce v současné době podporuje triggery protokolu HTTP, triggery objektů BLOB a triggery služby Service Bus. Další informace o aktivačních událostech najdete v tématu [Azure Functions Bindings](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns).

> [!IMPORTANT]
> Uložte informace o umístění, jak se používá při nasazování bitové kopie.

## <a name="deploy-image-as-a-web-app"></a>Nasazení image jako webové aplikace

1. K získání přihlašovacích údajů pro Azure Container Registry, které obsahují obrázek, použijte následující příkaz. Nahraďte `<acrinstance>` hodnotou vrácenou dříve z `package.location`: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Výstup tohoto příkazu je podobný následujícímu dokumentu JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Uložte hodnotu pro __uživatelské jméno__ a jedno z __hesel__.

1. Pokud ještě nemáte skupinu prostředků nebo plán služby App Service pro nasazení služby, následující příkazy ukazují, jak vytvořit obojí:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    V tomto příkladu se používá cenová úroveň pro _Linux Premium_ (`--sku EP1`).

    > [!IMPORTANT]
    > Image vytvořené Azure Machine Learning používají Linux, takže musíte použít parametr `--is-linux`.

1. K vytvoření aplikace Function App použijte následující příkaz. Nahraďte `<app-name>` názvem, který chcete použít. Nahraďte `<acrinstance>` a `<imagename>` hodnotami vrácenými `package.location` dříve:

    ```azurecli-interactive
    az storage account create --name 
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    > [!IMPORTANT]
    > V tuto chvíli se vytvořila aplikace Function App. Vzhledem k tomu, že jste nezadali připojovací řetězec pro Trigger objektu BLOB nebo přihlašovací údaje k Azure Container Registry, který obsahuje obrázek, aplikace Function app není aktivní. V dalších krocích zadejte připojovací řetězec a ověřovací informace pro registr kontejneru. 

1. Vytvořte účet úložiště, který se použije jako Trigger, a získejte připojovací řetězec.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name triggerStorage --query connectionString --output tsv
    ```
    Záznam tohoto připojovacího řetězce pro poskytnutí aplikace Function App. Později ji použijeme, až se požádáme o `<triggerConnectionString>`

1. Vytvořte kontejnery pro vstup a výstup v účtu úložiště. 

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Značku přidruženou k vytvořenému kontejneru budete muset načíst pomocí následujícího příkazu:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Nejnovější zobrazená značka bude `imagetag` níže.

1. K poskytnutí aplikace Function App s přihlašovacími údaji potřebnými pro přístup k registru kontejneru použijte následující příkaz. Nahraďte `<app-name>` názvem, který chcete použít. Nahraďte `<acrinstance>` a `<imagetag>` hodnotami z volání AZ CLI v předchozím kroku. Nahraďte `<username>` a `<password>` informace o přihlášení ACR, které byly načteny dříve:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Tento příkaz vrátí informace podobné následujícímu dokumentu JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

V tuto chvíli začne aplikace Function App načítat obrázek.

> [!IMPORTANT]
> Než se image načte, může to trvat několik minut. Průběh můžete sledovat pomocí webu Azure Portal.

## <a name="next-steps"></a>Další kroky

* Naučte se konfigurovat aplikaci Functions v dokumentaci k [funkcím](/azure/azure-functions/functions-create-function-linux-custom-image) .
* Další informace o službě BLOB Storage spouští [vazby úložiště objektů BLOB v Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Nasaďte model do Azure App Service](service/how-to-deploy-app-service.md).
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
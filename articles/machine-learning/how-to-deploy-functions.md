---
title: Nasazení modelů ml do aplikací Azure Functions Apps (preview)
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí Azure Machine Learning nasadit model do aplikace Azure Functions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927444"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Nasazení modelu strojového učení do funkcí Azure (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak nasadit model z Azure Machine Learning jako funkční aplikace v Azure Functions.

> [!IMPORTANT]
> Zatímco Azure Machine Learning a Azure Functions jsou obecně dostupné, možnost zabalit model ze služby Machine Learning pro funkce je ve verzi preview.

S Azure Machine Learning můžete vytvářet imitace Dockeru z trénovaných modelů strojového učení. Azure Machine Learning má teď funkci preview pro sestavení těchto modelů strojového učení do funkčních aplikací, které se dá [nasadit do funkcí Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace naleznete v článku [Vytvoření pracovního prostoru.](how-to-manage-workspace.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Trénovaný model strojového učení registrovaný ve vašem pracovním prostoru. Pokud nemáte model, použijte [kurz klasifikace obrázků: trénování modelu](tutorial-train-models-with-aml.md) trénovat a registrovat jeden.

    > [!IMPORTANT]
    > Fragmenty kódu v tomto článku předpokládají, že jste nastavili následující proměnné:
    >
    > * `ws`- Pracovní prostor Azure Machine Learning.
    > * `model`- Registrovaný model, který bude nasazen.
    > * `inference_config`- Odvození konfigurace pro model.
    >
    > Další informace o nastavení těchto proměnných najdete v [tématu Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení

Před nasazením je nutné definovat, co je potřeba ke spuštění modelu jako webové služby. Následující seznam popisuje základní položky potřebné pro nasazení:

* __Vstupní skript__. Tento skript přijímá požadavky, skóre požadavek pomocí modelu a vrátí výsledky.

    > [!IMPORTANT]
    > Vstupní skript je specifický pro váš model; musí pochopit formát dat příchozích požadavků, formát dat očekávaných modelem a formát dat vrácených klientům.
    >
    > Pokud jsou data požadavku ve formátu, který není použitelný modelem, skript jej může převést do přijatelného formátu. Může také transformovat odpověď před návratem do klienta.
    >
    > Ve výchozím nastavení při balení pro funkce je vstup považován za text. Pokud máte zájem o využití nezpracovaných bajtů vstupu (například pro aktivační události objektů Blob), měli byste použít [AMLRequest k přijetí nezpracovaných dat](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data).


* **Závislosti**, jako jsou pomocné skripty nebo balíčky Python/Conda potřebné ke spuštění vstupního skriptu nebo modelu

Tyto entity jsou zapouzdřeny do __odvození konfigurace__. Odvozená konfigurace odkazuje na vstupní skript a další závislosti.

> [!IMPORTANT]
> Při vytváření konfigurace odvození pro použití s funkcemi Azure, musíte použít objekt [prostředí.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Vezměte prosím na vědomí, že pokud definujete vlastní prostředí, musíte přidat azureml-defaults s verzí >= 1.0.45 jako závislost pip. Tento balíček obsahuje funkce potřebné k hostování modelu jako webové služby. Následující příklad ukazuje vytvoření objektu prostředí a jeho použití s konfigurací odvození:
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

Další informace o prostředích naleznete v [tématu Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md).

Další informace o konfiguraci odvození najdete v [tématu nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Při nasazování do funkcí není nutné vytvářet __konfiguraci nasazení__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instalace balíčku Náhled sady SDK pro podporu funkcí

Chcete-li vytvořit balíčky pro funkce Azure, musíte nainstalovat balíček Náhled sady SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Vytvoření image

Chcete-li vytvořit image Dockeru, která se nasadí do funkce Azure, použijte [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) nebo konkrétní funkci balíčku pro aktivační událost, kterou máte zájem používat. Následující fragment kódu ukazuje, jak vytvořit nový balíček s aktivační událostí objektu blob z konfigurace modelu a odvození:

> [!NOTE]
> Fragment kódu předpokládá, že `model` obsahuje registrovaný model a `inference_config` který obsahuje konfiguraci pro prostředí odvození. Další informace najdete [v tématu Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Když `show_output=True`se zobrazí výstup procesu sestavení Dockeru. Po dokončení procesu image byla vytvořena v registru kontejnerů Azure pro váš pracovní prostor. Po vyestavěné image se zobrazí umístění v registru kontejnerů Azure. Vrácené umístění je `<acrinstance>.azurecr.io/package@sha256:<hash>`ve formátu .

> [!NOTE]
> Balení pro funkce aktuálně podporuje aktivační události HTTP, aktivační události objektů Blob a aktivační události služby Service bus. Další informace o aktivačních událostech najdete v tématu [Vazby Funkce Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns).

> [!IMPORTANT]
> Uložte informace o poloze tak, jak se používají při nasazování bitové kopie.

## <a name="deploy-image-as-a-web-app"></a>Nasazení bitové kopie jako webové aplikace

1. Pomocí následujícího příkazu získat přihlašovací údaje pro Azure Container Registry, který obsahuje image. Nahradit `<myacr>` hodnotou vrácenou `package.location`dříve z : 

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

    Uložte hodnotu __uživatelského jména__ a jedno z __hesel__.

1. Pokud ještě nemáte skupinu prostředků nebo plán služby App Service k nasazení služby, následující příkazy ukazují, jak vytvořit obojí:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    V tomto příkladu se používá`--sku B1`základní cenová úroveň _Linuxu_ ( ).

    > [!IMPORTANT]
    > Image vytvořené Azure Machine Learning používat Linux, `--is-linux` takže je nutné použít parametr.

1. Vytvořte účet úložiště, který chcete použít pro úložiště webových úloh, a získejte jeho připojovací řetězec. Nahraďte `<webjobStorage>` název, který chcete použít.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Chcete-li vytvořit aplikaci funkce, použijte následující příkaz. Nahraďte `<app-name>` název, který chcete použít. Nahradit `<acrinstance>` `<imagename>` a s hodnotami z vrácené `package.location` dříve. Nahraďte `<webjobStorage>` se názvem účtu úložiště z předchozího kroku:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > V tomto okamžiku byla vytvořena aplikace funkce. Protože jste však neposkytli připojovací řetězec pro aktivační událost objektu blob nebo pověření k registru kontejnerů Azure, který obsahuje bitovou kopii, aplikace funkce není aktivní. V dalších krocích zadáte připojovací řetězec a ověřovací informace pro registr kontejneru. 

1. Vytvořte účet úložiště, který se použije pro úložiště aktivační události objektu blob a získejte jeho připojovací řetězec. Nahraďte `<triggerStorage>` název, který chcete použít.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Zaznamenejte tento připojovací řetězec, který chcete poskytnout aplikaci funkce. Použijeme ji později, až požádáme`<triggerConnectionString>`

1. Vytvořte kontejnery pro vstup a výstup v účtu úložiště. Nahradit `<triggerConnectionString>` připojovacím řetězcem vráceným dříve:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Chcete-li přidružit připojovací řetězec aktivační události k aplikaci funkce, použijte následující příkaz. Nahraďte `<app-name>` název aplikace funkce. Nahradit `<triggerConnectionString>` připojovacím řetězcem vráceným dříve:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Značku přidruženou k vytvořenému kontejneru budete muset načíst pomocí následujícího příkazu. Nahradit `<username>` uživatelským jménem vráceným dříve z registru kontejneru:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Uložit vrácenou hodnotu, bude `imagetag` použita jako v dalším kroku.

1. Chcete-li aplikaci funkce poskytnout pověření potřebná pro přístup k registru kontejneru, použijte následující příkaz. Nahraďte `<app-name>` název aplikace funkce. Nahraďte `<acrinstance>` a `<imagetag>` s hodnotami z volání AZ CLI v předchozím kroku. Nahraďte `<username>` a `<password>` s přihlašovací údaje ACR načtené dříve:

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

V tomto okamžiku aplikace funkce začne načítat obrázek.

> [!IMPORTANT]
> Může trvat několik minut, než se obraz načte. Průběh můžete sledovat pomocí portálu Azure Portal.

## <a name="test-the-deployment"></a>Otestování nasazení

Jakmile se obrázek načte a aplikace je k dispozici, použijte následující kroky k aktivaci aplikace:

1. Vytvořte textový soubor, který obsahuje data, která score.py soubor očekává. Následující příklad by pracovat s score.py, který očekává pole 10 čísel:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Formát dat závisí na tom, co vaše score.py a model očekává.

2. Pomocí následujícího příkazu nahrajte tento soubor do vstupního kontejneru v objektu blob úložiště aktivační události vytvořeném dříve. Nahraďte `<file>` název souboru obsahujícího data. Nahraďte `<triggerConnectionString>` připojovacím řetězcem vráceným dříve. V tomto `input` příkladu je název vstupního kontejneru vytvořeného dříve. Pokud jste použili jiný název, nahraďte tuto hodnotu:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Výstup tohoto příkazu je podobný následujícímu jsonu:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Chcete-li zobrazit výstup vytvořený funkcí, použijte následující příkaz k zobrazení seznamu vygenerovaných výstupních souborů. Nahraďte `<triggerConnectionString>` připojovacím řetězcem vráceným dříve. V tomto `output` příkladu je název výstupního kontejneru vytvořeného dříve. Pokud jste použili jiný název, nahraďte tuto hodnotu::

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    Výstup tohoto příkazu je `sample_input_out.json`podobný .

4. Chcete-li soubor stáhnout a zkontrolovat obsah, použijte následující příkaz. Nahraďte `<file>` název souboru vráceným předchozím příkazem. Nahradit `<triggerConnectionString>` připojovacím řetězcem vráceným dříve: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Po dokončení příkazu otevřete soubor. Obsahuje data vrácená modelem.

Další informace o použití aktivačních událostí objektů blob najdete v [tématu Vytvoření funkce aktivované](/azure/azure-functions/functions-create-storage-blob-triggered-function) azure blob úložiště článku.

## <a name="next-steps"></a>Další kroky

* Naučte se konfigurovat aplikaci Funkce v dokumentaci [funkce.](/azure/azure-functions/functions-create-function-linux-custom-image)
* Další informace o úložišti objektů Blob aktivační události [Azure blob vazby úložiště](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Nasazení modelu do služby Azure App Service](how-to-deploy-app-service.md).
* [Využití modelu ML nasazeného jako webová služba](how-to-consume-web-service.md)
* [Referenční informace k rozhraním API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)

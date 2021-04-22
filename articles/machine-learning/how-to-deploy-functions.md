---
title: Nasazení modelů ml do Azure Functionsch aplikací (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se používat Azure Machine Learning k zabalení a nasazení modelu jako webové služby v aplikaci Azure Functions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: vaidya-s
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.topic: how-to
ms.custom: racking-python, devx-track-azurecli
ms.openlocfilehash: bdeecf02e81ba3c8143f707896bbf3e5c36cf212
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885393"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Nasazení modelu Machine Learning do Azure Functions (Preview)


Naučte se, jak nasadit model z Azure Machine Learning jako aplikace Function App v Azure Functions.

> [!IMPORTANT]
> I když jsou všeobecně k dispozici Azure Machine Learning i Azure Functions, možnost zabalit model ze služby Machine Learning for Functions je ve verzi Preview.

Pomocí Azure Machine Learning můžete vytvářet image Docker z školicích modelů strojového učení. Azure Machine Learning teď má funkce Preview k sestavování těchto modelů strojového učení do aplikací Function App, které se dají [nasadit do Azure Functions](../azure-functions/functions-deployment-technologies.md#docker-container).

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v článku o [Vytvoření pracovního prostoru](how-to-manage-workspace.md) .
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)
* Vyškolený model strojového učení zaregistrovaný ve vašem pracovním prostoru. Pokud model nemáte, použijte [kurz k klasifikaci imagí: výukový model](tutorial-train-models-with-aml.md) pro výuku a registraci k jednomu.

    > [!IMPORTANT]
    > Fragmenty kódu v tomto článku předpokládají, že jste nastavili následující proměnné:
    >
    > * `ws` – Váš pracovní prostor Azure Machine Learning.
    > * `model` – Registrovaný model, který se nasadí.
    > * `inference_config` – Odvození konfigurace pro model.
    >
    > Další informace o nastavení těchto proměnných najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Příprava nasazení

Před nasazením musíte definovat, co je potřeba ke spuštění modelu jako webové služby. Následující seznam popisuje základní položky potřebné pro nasazení:

* __Vstupní skript__. Tento skript přijímá požadavky, vyhodnotí požadavek pomocí modelu a vrátí výsledky.

    > [!IMPORTANT]
    > Vstupní skript je specifický pro váš model; musí pochopit formát příchozích dat požadavků, formát dat očekávaných modelem a formát dat vrácených klientům.
    >
    > Pokud jsou data požadavku ve formátu, který model nepoužívá, skript ho může transformovat do přijatelného formátu. Může také transformovat odpověď předtím, než se vrátí do klienta.
    >
    > Ve výchozím nastavení je při balení pro funkce vstup považován za text. Pokud vás zajímá využívání nezpracovaných bajtů vstupu (například pro triggery objektů BLOB), měli byste použít [AMLRequest k přijetí nezpracovaných dat](./how-to-deploy-advanced-entry-script.md#binary-data).

Další informace o vstupním skriptu najdete v tématu [Definování kódu bodování](./how-to-deploy-and-where.md#define-an-entry-script) .

* **Závislosti**, například pomocné skripty nebo balíčky python/conda potřebné ke spuštění skriptu vstupu nebo modelu

Tyto entity jsou zapouzdřeny do __Konfigurace odvození__. Odvozená konfigurace odkazuje na vstupní skript a další závislosti.

> [!IMPORTANT]
> Při vytváření odvozených konfigurací pro použití s Azure Functions je nutné použít objekt [prostředí](/python/api/azureml-core/azureml.core.environment%28class%29) . Počítejte s tím, že pokud definujete vlastní prostředí, musíte přidat AzureML-Defaults s Version >= 1.0.45 jako závislost v PIP. Tento balíček obsahuje funkce potřebné pro hostování modelu jako webové služby. Následující příklad ukazuje vytvoření objektu prostředí a jeho použití s odvozenou konfigurací:
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

Další informace o konfiguraci odvození najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Při nasazování do funkcí není nutné vytvářet __konfiguraci nasazení__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instalace balíčku sady SDK Preview pro podporu funkcí

Chcete-li vytvořit balíčky pro Azure Functions, je nutné nainstalovat balíček sady SDK verze Preview.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Vytvoření image

Chcete-li vytvořit bitovou kopii Docker, která je nasazena do Azure Functions, použijte funkci [AzureML. contrib. Functions. Package](/python/api/azureml-contrib-functions/azureml.contrib.functions) nebo konkrétního balíčku pro aktivační událost, které vás zajímá. Následující fragment kódu ukazuje, jak vytvořit nový balíček s triggerem objektu BLOB z modelu a odvozené konfigurace:

> [!NOTE]
> Fragment kódu předpokládá, že `model` obsahuje registrovaný model a `inference_config` obsahuje konfiguraci pro odvození prostředí. Další informace najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

`show_output=True`V případě je zobrazen výstup procesu Docker Build. Po dokončení procesu se image vytvoří v Azure Container Registry pro váš pracovní prostor. Po sestavení obrázku se zobrazí umístění v Azure Container Registry. Navrácené umístění má formát `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Balení pro funkce v současné době podporuje triggery protokolu HTTP, triggery objektů BLOB a triggery služby Service Bus. Další informace o aktivačních událostech najdete v tématu [Azure Functions Bindings](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Uložte informace o umístění, jak se používá při nasazování bitové kopie.

## <a name="deploy-image-as-a-web-app"></a>Nasazení image jako webové aplikace

1. K získání přihlašovacích údajů pro Azure Container Registry, které obsahují obrázek, použijte následující příkaz. Nahraďte `<myacr>` hodnotou, kterou jste dříve vrátili `blob.location` : 

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
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    V tomto příkladu se používá cenová úroveň _Basic pro Linux_ ( `--sku B1` ).

    > [!IMPORTANT]
    > Image vytvořené Azure Machine Learning používají Linux, takže musíte použít `--is-linux` parametr.

1. Vytvořte účet úložiště, který se použije pro úložiště webové úlohy, a získejte připojovací řetězec. Nahraďte `<webjobStorage>` názvem, který chcete použít.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. K vytvoření aplikace Function App použijte následující příkaz. Nahraďte `<app-name>` názvem, který chcete použít. `<acrinstance>`Hodnoty a nahraďte `<imagename>` hodnotami vrácenými `package.location` dříve. Nahraďte `<webjobStorage>` názvem účtu úložiště z předchozího kroku:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > V tuto chvíli se vytvořila aplikace Function App. Vzhledem k tomu, že jste nezadali připojovací řetězec pro Trigger objektu BLOB nebo přihlašovací údaje k Azure Container Registry, který obsahuje obrázek, aplikace Function app není aktivní. V dalších krocích zadejte připojovací řetězec a ověřovací informace pro registr kontejneru. 

1. Vytvořte účet úložiště, který se použije pro úložiště triggeru objektu blob, a získejte připojovací řetězec. Nahraďte `<triggerStorage>` názvem, který chcete použít.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Záznam tohoto připojovacího řetězce pro poskytnutí aplikace Function App. Později ji použijeme, až se vám požádáme o `<triggerConnectionString>`

1. Vytvořte kontejnery pro vstup a výstup v účtu úložiště. Nahraďte `<triggerConnectionString>` připojovacím řetězcem, který se vrátil dříve:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. K přidružení připojovacího řetězce triggeru k aplikaci Function App použijte následující příkaz. Nahraďte `<app-name>` názvem aplikace Function App. Nahraďte `<triggerConnectionString>` připojovacím řetězcem, který se vrátil dříve:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Pomocí následujícího příkazu bude nutné načíst značku přidruženou k vytvořenému kontejneru. Nahraďte `<username>` uživatelským jménem vráceným dříve z registru kontejneru:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Uložte vrácenou hodnotu, která bude použita jako `imagetag` v dalším kroku.

1. K poskytnutí aplikace Function App s přihlašovacími údaji potřebnými pro přístup k registru kontejneru použijte následující příkaz. Nahraďte `<app-name>` názvem aplikace Function App. `<acrinstance>`Hodnoty a nahraďte `<imagetag>` pomocí volání AZ CLI v předchozím kroku. Nahraďte `<username>` a `<password>` informacemi o přihlášení ACR získanými dříve:

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

## <a name="test-the-deployment"></a>Otestování nasazení

Jakmile se image načte a aplikace je k dispozici, použijte k aktivaci aplikace následující postup:

1. Vytvořte textový soubor, který obsahuje data, která soubor score.py očekává. Následující příklad bude fungovat s score.py, která očekává pole 10 čísel:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Formát dat závisí na tom, co váš score.py a model očekává.

2. Pomocí následujícího příkazu nahrajte tento soubor do vstupního kontejneru v objektu BLOB úložiště triggeru, který jste vytvořili dříve. Nahraďte `<file>` názvem souboru, který obsahuje data. Nahraďte `<triggerConnectionString>` připojovacím řetězcem, který se vrátil dříve. V tomto příkladu `input` je název vstupního kontejneru, který jste vytvořili dříve. Pokud jste použili jiný název, nahraďte tuto hodnotu:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Výstup tohoto příkazu je podobný následujícímu formátu JSON:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Chcete-li zobrazit výstup vyprodukovaný funkcí, použijte následující příkaz pro výpis vygenerovaných výstupních souborů. Nahraďte `<triggerConnectionString>` připojovacím řetězcem, který se vrátil dříve. V tomto příkladu `output` je název výstupního kontejneru, který jste vytvořili dříve. Pokud jste použili jiný název, nahraďte tuto hodnotu:

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    Výstup tohoto příkazu je podobný `sample_input_out.json` .

4. Chcete-li stáhnout soubor a zkontrolovat obsah, použijte následující příkaz. Nahraďte `<file>` názvem souboru vráceným předchozím příkazem. Nahraďte `<triggerConnectionString>` připojovacím řetězcem, který se vrátil dříve: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Až se příkaz dokončí, otevřete soubor. Obsahuje data vrácená modelem.

Další informace o používání triggerů objektů BLOB najdete v článku [Vytvoření funkce aktivované úložištěm Azure Blob Storage](../azure-functions/functions-create-storage-blob-triggered-function.md) .

## <a name="next-steps"></a>Další kroky

* Naučte se konfigurovat aplikaci Functions v dokumentaci k [funkcím](../azure-functions/functions-create-function-linux-custom-image.md) .
* Další informace o službě BLOB Storage spouští [vazby úložiště objektů BLOB v Azure](../azure-functions/functions-bindings-storage-blob.md).
* [Nasaďte model do Azure App Service](how-to-deploy-app-service.md).
* [Využití modelu ML nasazeného jako webové služby](how-to-consume-web-service.md)
* [Referenční informace k rozhraním API](/python/api/azureml-contrib-functions/azureml.contrib.functions)

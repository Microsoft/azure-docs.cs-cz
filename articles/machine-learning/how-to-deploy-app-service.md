---
title: Nasazení modelů ml do Azure App Service (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se používat Azure Machine Learning k nasazení modelu trained ML do webové aplikace pomocí Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 3b1b416f3fec9e40261a82c88260c041918c1424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521998"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Nasazení modelu Machine Learning do Azure App Service (Preview)


Naučte se nasadit model z Azure Machine Learning jako webovou aplikaci v Azure App Service.

> [!IMPORTANT]
> I když jsou všeobecně k dispozici Azure Machine Learning i Azure App Service, možnost nasadit model ze služby Machine Learning do App Service je ve verzi Preview.

Pomocí Azure Machine Learning můžete vytvářet image Docker z školicích modelů strojového učení. Tato image obsahuje webovou službu, která přijímá data, odesílá je do modelu a pak vrátí odpověď. Azure App Service lze použít k nasazení bitové kopie a k dispozici jsou následující funkce:

* Rozšířené [ověřování](../app-service/configure-authentication-provider-aad.md) pro rozšířené zabezpečení. Metody ověřování zahrnují Azure Active Directory i Multi-Factor auth.
* [Automatické škálování](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json) bez nutnosti opětovného nasazení.
* [Podpora TLS](../app-service/configure-ssl-certificate-in-code.md) pro zabezpečenou komunikaci mezi klienty a službou.

Další informace o funkcích poskytovaných nástrojem Azure App Service najdete v [přehledu App Service](../app-service/overview.md).

> [!IMPORTANT]
> Pokud potřebujete mít možnost protokolovat data bodování používaná s nasazeným modelem nebo výsledky bodování, měli byste je místo toho nasadit do služby Azure Kubernetes. Další informace najdete v tématu [shromažďování dat v produkčních modelech](how-to-enable-data-collection.md).

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

Před nasazením musíte definovat, co je potřeba ke spuštění modelu jako webové služby. Následující seznam popisuje hlavní položky potřebné pro nasazení:

* __Vstupní skript__. Tento skript přijímá požadavky, vyhodnotí požadavek pomocí modelu a vrátí výsledky.

    > [!IMPORTANT]
    > Vstupní skript je specifický pro váš model; musí pochopit formát příchozích dat požadavků, formát dat očekávaných modelem a formát dat vrácených klientům.
    >
    > Pokud jsou data požadavku ve formátu, který model nepoužívá, skript ho může transformovat do přijatelného formátu. Může také transformovat odpověď předtím, než se vrátí do klienta.

    > [!IMPORTANT]
    > Sada Azure Machine Learning SDK neposkytuje způsob, jak webové službě přistupovat k úložišti dat nebo datovým sadám. Pokud potřebujete nasadit model pro přístup k datům uloženým mimo nasazení, jako je například v účtu Azure Storage, je nutné vyvinout vlastní řešení kódu pomocí příslušné sady SDK. Například [sada SDK Azure Storage pro Python](https://github.com/Azure/azure-storage-python).
    >
    > Další alternativou, která může fungovat pro váš scénář, je [Batch předpovědi](./tutorial-pipeline-batch-scoring-classification.md), která poskytuje přístup k úložišti dat při bodování.

    Další informace o vstupních skriptech najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

* **Závislosti**, například pomocné skripty nebo balíčky python/conda potřebné ke spuštění skriptu vstupu nebo modelu

Tyto entity jsou zapouzdřeny do __Konfigurace odvození__. Odvozená konfigurace odkazuje na vstupní skript a další závislosti.

> [!IMPORTANT]
> Při vytváření odvozených konfigurací pro použití s Azure App Service je nutné použít objekt [prostředí](/python/api/azureml-core/azureml.core.environment(class)) . Počítejte s tím, že pokud definujete vlastní prostředí, musíte přidat AzureML-Defaults s Version >= 1.0.45 jako závislost v PIP. Tento balíček obsahuje funkce potřebné pro hostování modelu jako webové služby. Následující příklad ukazuje vytvoření objektu prostředí a jeho použití s odvozenou konfigurací:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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
> Při nasazování do Azure App Service nemusíte vytvářet __konfiguraci nasazení__.

## <a name="create-the-image"></a>Vytvoření image

Chcete-li vytvořit bitovou kopii Docker, která je nasazena do Azure App Service, použijte [model. Package](/python/api/azureml-core/azureml.core.model.model). Následující fragment kódu ukazuje, jak vytvořit novou bitovou kopii z modelu a odvozené konfigurace:

> [!NOTE]
> Fragment kódu předpokládá, že `model` obsahuje registrovaný model a `inference_config` obsahuje konfiguraci pro odvození prostředí. Další informace najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

`show_output=True`V případě je zobrazen výstup procesu Docker Build. Po dokončení procesu se image vytvoří v Azure Container Registry pro váš pracovní prostor. Po sestavení obrázku se zobrazí umístění v Azure Container Registry. Navrácené umístění má formát `<acrinstance>.azurecr.io/package@sha256:<imagename>` . Například, `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`.

> [!IMPORTANT]
> Uložte informace o umístění, jak se používá při nasazování bitové kopie.

## <a name="deploy-image-as-a-web-app"></a>Nasazení image jako webové aplikace

1. K získání přihlašovacích údajů pro Azure Container Registry, které obsahují obrázek, použijte následující příkaz. Nahraďte `<acrinstance>` hodnotou, kterou jste dříve vrátili `package.location` :

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

    V tomto příkladu se používá cenová úroveň __Basic__ ( `--sku B1` ).

    > [!IMPORTANT]
    > Image vytvořené Azure Machine Learning používají Linux, takže musíte použít `--is-linux` parametr.

1. Pokud chcete vytvořit webovou aplikaci, použijte následující příkaz. Nahraďte `<app-name>` názvem, který chcete použít. Nahraďte `<acrinstance>` a `<imagename>` hodnotami vrácenými `package.location` dříve:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    Tento příkaz vrátí informace podobné následujícímu dokumentu JSON:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > V tuto chvíli se vytvořila webová aplikace. Vzhledem k tomu, že jste neposkytli přihlašovací údaje Azure Container Registry, který obsahuje obrázek, není tato webová aplikace aktivní. V dalším kroku zadáte informace o ověřování pro registr kontejnerů.

1. K poskytnutí přihlašovacích údajů, které jsou potřebné pro přístup k registru kontejnerů, použijte následující příkaz. Nahraďte `<app-name>` názvem, který chcete použít. `<acrinstance>`Hodnoty a nahraďte `<imagename>` hodnotami vrácenými `package.location` dříve. Nahraďte `<username>` a `<password>` informacemi o přihlášení ACR získanými dříve:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

V tuto chvíli webová aplikace začne načítat image.

> [!IMPORTANT]
> Než se image načte, může to trvat několik minut. Chcete-li sledovat průběh, použijte následující příkaz:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Po načtení bitové kopie a umístění lokality do protokolu se zobrazí zpráva s oznámením `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` .

Po nasazení bitové kopie můžete název hostitele najít pomocí následujícího příkazu:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Tento příkaz vrátí informace podobné následujícímu názvu hostitele – `<app-name>.azurewebsites.net` . Tuto hodnotu použijte jako součást __základní adresy URL__ služby.

## <a name="use-the-web-app"></a>Použití webové aplikace

Webová služba, která předává požadavky do modelu, je umístěna na adrese `{baseurl}/score` . Například, `https://<app-name>.azurewebsites.net/score`. Následující kód Pythonu ukazuje, jak odesílat data do adresy URL a zobrazovat odpověď:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Další kroky

* Naučte se konfigurovat webovou aplikaci v dokumentaci k [App Service v systému Linux](/azure/app-service/containers/) .
* Další informace o škálování najdete v [Seznámení s automatickým škálováním v Azure](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json).
* [V Azure App Service použijte certifikát TLS/SSL](../app-service/configure-ssl-certificate-in-code.md).
* [Nakonfigurujte aplikaci App Service, aby používala Azure Active Directory přihlášení](../app-service/configure-authentication-provider-aad.md).
* [Využití modelu ML nasazeného jako webové služby](how-to-consume-web-service.md)
---
title: Nasazení modelů ml do služby Azure App Service (preview)
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí Azure Machine Learning nasadit model do webové aplikace ve službě Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282813"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Nasazení modelu strojového učení do služby Azure App Service (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak nasadit model z Azure Machine Learning jako webové aplikace ve službě Azure App Service.

> [!IMPORTANT]
> Zatímco Azure Machine Learning a Azure App Service jsou obecně dostupné, možnost nasadit model ze služby Machine Learning do služby App Service je ve verzi preview.

S Azure Machine Learning můžete vytvářet imitace Dockeru z trénovaných modelů strojového učení. Tento obrázek obsahuje webovou službu, která přijímá data, odešle je do modelu a potom vrátí odpověď. Azure App Service slouží k nasazení image a poskytuje následující funkce:

* Rozšířené [ověřování](/azure/app-service/configure-authentication-provider-aad) pro lepší zabezpečení. Metody ověřování zahrnují azure active directory a vícefaktorové ověření.
* [Automatické škálování](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) bez nutnosti opětovného nasazení.
* [Podpora TLS](/azure/app-service/configure-ssl-certificate-in-code) pro zabezpečenou komunikaci mezi klienty a službou.

Další informace o funkcích poskytovaných službou Azure App Service najdete v přehledu [služby App Service](/azure/app-service/overview).

> [!IMPORTANT]
> Pokud potřebujete možnost protokolovat data vyhodnocování použitá s nasazeným modelem nebo výsledky vyhodnocování, měli byste místo toho nasadit do služby Azure Kubernetes. Další informace naleznete v [tématu Shromažďování dat o produkčních modelech](how-to-enable-data-collection.md).

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

    > [!IMPORTANT]
    > Sada Azure Machine Learning SDK neposkytuje způsob, jak pro webovou službu přístup k úložišti dat nebo datových sad. Pokud potřebujete nasazený model pro přístup k datům uloženým mimo nasazení, například v účtu Azure Storage, musíte vyvinout vlastní kódové řešení pomocí příslušné sady SDK. Například [sada Azure Storage SDK pro Python](https://github.com/Azure/azure-storage-python).
    >
    > Další alternativou, která může fungovat pro váš scénář je [predikce dávky](how-to-use-parallel-run-step.md), který poskytuje přístup k úložišti dat při vyhodnocování.

    Další informace o vstupních skriptech najdete v [tématu Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

* **Závislosti**, jako jsou pomocné skripty nebo balíčky Python/Conda potřebné ke spuštění vstupního skriptu nebo modelu

Tyto entity jsou zapouzdřeny do __odvození konfigurace__. Odvozená konfigurace odkazuje na vstupní skript a další závislosti.

> [!IMPORTANT]
> Při vytváření konfigurace odvození pro použití se službou Azure App Service, musíte použít objekt [prostředí.](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Vezměte prosím na vědomí, že pokud definujete vlastní prostředí, musíte přidat azureml-defaults s verzí >= 1.0.45 jako závislost pip. Tento balíček obsahuje funkce potřebné k hostování modelu jako webové služby. Následující příklad ukazuje vytvoření objektu prostředí a jeho použití s konfigurací odvození:
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

Další informace o prostředích naleznete v [tématu Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md).

Další informace o konfiguraci odvození najdete v [tématu nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Při nasazování do služby Azure App Service není nutné vytvářet __konfiguraci nasazení__.

## <a name="create-the-image"></a>Vytvoření image

Chcete-li vytvořit image Dockeru, která se nasadí do služby Azure App Service, použijte [Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-). Následující fragment kódu ukazuje, jak vytvořit novou bitovou kopii z konfigurace modelu a odvození:

> [!NOTE]
> Fragment kódu předpokládá, že `model` obsahuje registrovaný model a `inference_config` který obsahuje konfiguraci pro prostředí odvození. Další informace najdete [v tématu Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Když `show_output=True`se zobrazí výstup procesu sestavení Dockeru. Po dokončení procesu image byla vytvořena v registru kontejnerů Azure pro váš pracovní prostor. Po vyestavěné image se zobrazí umístění v registru kontejnerů Azure. Vrácené umístění je `<acrinstance>.azurecr.io/package:<imagename>`ve formátu . Například, `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Uložte informace o poloze tak, jak se používají při nasazování bitové kopie.

## <a name="deploy-image-as-a-web-app"></a>Nasazení bitové kopie jako webové aplikace

1. Pomocí následujícího příkazu získat přihlašovací údaje pro Azure Container Registry, který obsahuje image. Nahradit `<acrinstance>` hodnotou hodnotou `package.location`vrácenou dříve z :

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

    V tomto příkladu se`--sku B1`používá __základní__ cenová úroveň ( ).

    > [!IMPORTANT]
    > Image vytvořené Azure Machine Learning používat Linux, `--is-linux` takže je nutné použít parametr.

1. Chcete-li vytvořit webovou aplikaci, použijte následující příkaz. Nahraďte `<app-name>` název, který chcete použít. Nahradit `<acrinstance>` `<imagename>` a s hodnotami z vrácené `package.location` dříve:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
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
    > V tomto okamžiku byla vytvořena webová aplikace. Protože jste však nezadali přihlašovací údaje registru kontejnerů Azure, který obsahuje bitovou kopii, webová aplikace není aktivní. V dalším kroku zadáte informace o ověřování pro registr kontejneru.

1. Chcete-li webové aplikaci poskytnout přihlašovací údaje potřebné pro přístup k registru kontejneru, použijte následující příkaz. Nahraďte `<app-name>` název, který chcete použít. Nahradit `<acrinstance>` `<imagename>` a s hodnotami z vrácené `package.location` dříve. Nahraďte `<username>` a `<password>` s přihlašovací údaje ACR načtené dříve:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

V tomto okamžiku webová aplikace začne načítat obrázek.

> [!IMPORTANT]
> Může trvat několik minut, než se obraz načte. Chcete-li sledovat průběh, použijte následující příkaz:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Po načtení obrázku a aktivní web zobrazí protokol zprávu, která uvádí `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`.

Po nasazení bitové kopie můžete najít název hostitele pomocí následujícího příkazu:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Tento příkaz vrátí informace podobné následujícímu názvu hostitele - `<app-name>.azurewebsites.net`. Tuto hodnotu použijte jako součást __základní adresy URL__ služby.

## <a name="use-the-web-app"></a>Použití webové aplikace

Webová služba, která předává požadavky `{baseurl}/score`modelu, je umístěna na adrese . Například, `https://<app-name>.azurewebsites.net/score`. Následující kód Pythonu ukazuje, jak odeslat data na adresu URL a zobrazit odpověď:

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

* Naučte se nakonfigurovat webovou aplikaci v dokumentaci [app služby na Linuxu.](/azure/app-service/containers/)
* Další informace o škálování v [začínáme s automatickým škálováním v Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Ve službě Azure App Service použijte certifikát TLS/SSL](/azure/app-service/configure-ssl-certificate-in-code).
* [Nakonfigurujte aplikaci App Service tak, aby používala přihlášení k Azure Active Directory](/azure/app-service/configure-authentication-provider-aad).
* [Využití modelu ML nasazeného jako webová služba](how-to-consume-web-service.md)

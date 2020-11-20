---
title: Spuštění kontejneru Rozpoznávání jazyka ve službě Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Nasaďte kontejner rozpoznávání jazyka se spuštěnou ukázkou do služby Azure Kubernetes a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2a6e7ee125cac66b48e666a766c39813aa38fb1a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968498"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Nasazení kontejneru rozpoznávání jazyka Analýza textu do služby Azure Kubernetes

Přečtěte si, jak nasadit kontejner rozpoznávání jazyka. Tento postup vám ukáže, jak vytvořit místní kontejnery Docker, nasdílet kontejnery do vlastního privátního registru kontejnerů, spustit kontejner v clusteru Kubernetes a otestovat ho ve webovém prohlížeči.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které je třeba nainstalovat a spustit místně. Nepoužívejte Azure Cloud Shell.

* Použijte předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services).
* [Git](https://git-scm.com/downloads) pro váš operační systém, abyste mohli klonovat [ukázku](https://github.com/Azure-Samples/cognitive-services-containers-samples) použitou v tomto postupu.
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Modul Docker](https://www.docker.com/products/docker-engine) a ověří, že rozhraní příkazového řádku Docker funguje v okně konzoly.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Prostředek Azure se správnou cenovou úrovní. Ne všechny cenové úrovně v tomto kontejneru fungují:
  * **Analýza textu** prostředek s cenovou úrovní F0 nebo Standard.
  * **Cognitive Services** prostředku pomocí cenové úrovně S0

## <a name="running-the-sample"></a>Spuštění ukázky

Tato procedura načte a spustí ukázku kontejneru Cognitive Services pro detekci jazyka. Vzorek má dva kontejnery, jeden pro klientskou aplikaci a jeden pro Cognitive Services kontejner. Oba tyto obrázky pošleme do Azure Container Registry. Až budou ve vašem vlastním registru, vytvořte službu Azure Kubernetes pro přístup k těmto imagí a spusťte kontejnery. Po spuštění kontejnerů použijte rozhraní příkazového řádku **kubectl** ke sledování výkonu kontejnerů. Přístup k klientské aplikaci pomocí požadavku HTTP a zobrazení výsledků.

![Koncepční nápad spuštění ukázkových kontejnerů](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Ukázkové kontejnery

Ukázka má dvě image kontejneru, jednu pro front-end Web. Druhý obrázek je kontejner pro detekci jazyka, který vrací zjištěný jazyk (jazykovou verzi) textu. Po dokončení jsou oba kontejnery přístupné z externí IP adresy.

### <a name="the-language-frontend-container"></a>Kontejner s front-endového jazyka

Tento web je ekvivalentní vaší vlastní aplikaci na straně klienta, která dává požadavky na koncový bod detekce jazyka. Po dokončení postupu obdržíte zjištěný jazyk řetězce znaků přístupem k kontejneru webu v prohlížeči pomocí `http://<external-IP>/<text-to-analyze>` . Příkladem této adresy URL je `http://132.12.23.255/helloworld!` . Výsledek v prohlížeči je `English` .

### <a name="the-language-container"></a>Kontejner jazyka

Kontejner pro detekci jazyka, v tomto konkrétním postupu, je přístupný všem externím žádostem. Kontejner se žádným způsobem nezměnil, takže je k dispozici standardní Cognitive Services rozhraní API pro detekci jazyka specifického pro kontejner.

Pro tento kontejner je toto rozhraní API požadavkem POST pro detekci jazyka. Stejně jako u všech kontejnerů Cognitive Services můžete získat další informace o kontejneru z jeho hostované informace Swagger `http://<external-IP>:5000/swagger/index.html` .

Port 5000 je výchozím portem používaným pro kontejnery Cognitive Services.

## <a name="create-azure-container-registry-service"></a>Vytvoření služby Azure Container Registry

K nasazení kontejneru do služby Azure Kubernetes musí být k dispozici image kontejneru. Vytvořte vlastní službu Azure Container Registry pro hostování imagí.

1. Přihlášení k rozhraní příkazového řádku Azure

    ```azurecli-interactive
    az login
    ```

1. Vytvořte skupinu prostředků s názvem `cogserv-container-rg` pro uložení každého prostředku vytvořeného tímto postupem.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Vytvořte vlastní Azure Container Registry ve formátu vašeho jména `registry` , například `pattyregistry` . V názvu nepoužívejte pomlčky ani znaky podtržení.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Uložte výsledky a získejte vlastnost **loginServer** . Ta bude součástí adresy hostovaného kontejneru, která se používá později v `language.yml` souboru.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Přihlaste se ke svému registru kontejneru. Než budete moct do svého registru nahrávat image, musíte se přihlásit.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Získat obrázek Docker webu

1. Vzorový kód použitý v tomto postupu je v úložišti ukázek Cognitive Servicesch kontejnerů. Naklonujte úložiště tak, aby mělo místní kopii ukázky.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Jakmile je úložiště na místním počítači, vyhledejte web v adresáři [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) . Tento web slouží jako klientská aplikace, která volá rozhraní API pro detekci jazyka hostovaného v kontejneru rozpoznávání jazyka.  

1. Sestavte image Docker pro tento web. Ujistěte se, že je konzola v adresáři [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) , kde se nachází souboru Dockerfile při spuštění následujícího příkazu:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Chcete-li sledovat verzi v registru kontejneru, přidejte značku ve formátu verze, například `v1` .

1. Nahrajte image do registru kontejneru. Může to trvat několik minut.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Pokud se zobrazí `unauthorized: authentication required` Chyba, přihlaste se pomocí `az acr login --name <your-container-registry-name>` příkazu. 

    Po dokončení procesu by se měly výsledky podobat následujícímu:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Získat obrázek Docker pro detekci jazyka

1. Stáhněte si nejnovější verzi image Docker do místního počítače. Může to trvat několik minut. Pokud je k dispozici novější verze tohoto kontejneru, změňte hodnotu z `1.1.006770001-amd64-preview` na novější verzi.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Označení Image pomocí registru kontejnerů. Vyhledejte nejnovější verzi a nahraďte verzi, `1.1.006770001-amd64-preview` Pokud máte novější verzi. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Nahrajte image do registru kontejneru. Může to trvat několik minut.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Získat přihlašovací údaje pro Container Registry

Následující kroky jsou potřebné k získání požadovaných informací pro připojení registru kontejneru ke službě Azure Kubernetes, kterou vytvoříte později v tomto postupu.

1. Vytvoření instančního objektu

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    `appId`V kroku 3 uložte hodnotu výsledků pro parametr zmocnění `<appId>` . Uložte `password` parametr pro parametr Client-Secret pro další oddíl `<client-secret>` .

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Získejte ID registru kontejneru.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    V dalším kroku uložte výstup hodnoty parametru scope `<acrId>` . Vypadá takto:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Uložte celou hodnotu pro krok 3 v této části.

1. Chcete-li udělit správnému přístupu ke clusteru AKS použití imagí uložených ve vašem registru kontejnerů, vytvořte přiřazení role. Hodnoty a nahraďte hodnotami, které jste `<appId>` `<acrId>` shromáždili v předchozích dvou krocích.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Vytvoření služby Azure Kubernetes

1. Vytvořte cluster Kubernetes. Všechny hodnoty parametrů jsou z předchozích sekcí s výjimkou parametru name. Vyberte název, který indikuje, kdo ho vytvořil, a jeho účel, například `patty-kube` .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Tento krok může trvat několik minut. Výsledek je následující:

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    Služba je vytvořená, ale ještě nemá kontejner webu nebo kontejner rozpoznávání jazyka.  

1. Získejte přihlašovací údaje clusteru Kubernetes.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Načtení definice orchestrace do služby Kubernetes

Tato část používá rozhraní příkazového řádku **kubectl** ke komunikaci se službou Azure Kubernetes.

1. Před načtením definice orchestrace ověřte, že **kubectl** má přístup k uzlům.

    ```console
    kubectl get nodes
    ```

    Odpověď vypadá nějak takto:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Zkopírujte následující soubor a pojmenujte ho `language.yml` . Soubor obsahuje `service` oddíl a `deployment` oddíl pro dva typy kontejnerů, `language-frontend` kontejner webu a `language` kontejner detekce.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Změňte řádky nasazení na základě jazyka, které jsou `language.yml` založené na následující tabulce, a přidejte tak vlastní názvy imagí registru kontejneru, tajný klíč klienta a nastavení analýzy textu.

    Nastavení nasazení ve front-endu|Účel|
    |--|--|
    |Řádek 32<br> `image` majetek|Umístění obrázku pro Image front-end v Container Registry<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Řádek 44<br> `name` majetek|Container Registry tajný klíč pro obrázek, který se označuje jako `<client-secret>` v předchozí části.|

1. Změňte řádky nasazení jazyka `language.yml` podle následující tabulky a přidejte tak vlastní názvy imagí registru kontejneru, tajný klíč klienta a nastavení analýzy textu.

    |Nastavení nasazení jazyka|Účel|
    |--|--|
    |Řádek 78<br> `image` majetek|Umístění obrázku pro obrázek jazyka v Container Registry<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Řádek 95<br> `name` majetek|Container Registry tajný klíč pro obrázek, který se označuje jako `<client-secret>` v předchozí části.|
    |Řádek 91<br> `apiKey` majetek|Váš klíč prostředku pro analýzu textu|
    |Řádek 92<br> `billing` majetek|Koncový bod fakturace pro váš prostředek textu Analytics.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Vzhledem k tomu, že **koncový bod** **apiKey** a Fakturovaný je nastaven jako součást definice orchestrace Kubernetes, nemusí kontejner webu znát tyto informace nebo je předat v rámci žádosti. Kontejner webu odkazuje na kontejner rozpoznávání jazyka podle jeho názvu Orchestrator `language` .

1. Načte definiční soubor Orchestration pro tuto ukázku ze složky, ve které jste vytvořili a uložili `language.yml` .

    ```console
    kubectl apply -f language.yml
    ```

    Odpověď:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Získat externí IP adresy kontejnerů

V případě těchto dvou kontejnerů ověřte, že `language-frontend` `language` jsou spuštěné služby a a získejte externí IP adresu.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Pokud se `EXTERNAL-IP` pro službu zobrazí jako čeká na vyřízení, spusťte příkaz znovu, dokud se nezobrazí IP adresa před přechodem k dalšímu kroku.

## <a name="test-the-language-detection-container"></a>Testování kontejneru rozpoznávání jazyka

Otevřete prohlížeč a přejděte na externí IP adresu `language` kontejneru z předchozí části: `http://<external-ip>:5000/swagger/index.html` . `Try it`K otestování koncového bodu detekce jazyka můžete použít funkci rozhraní API.

![Zobrazit dokumentaci k Swagger kontejneru](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testování kontejneru klientské aplikace

Změňte adresu URL v prohlížeči na externí IP adresu `language-frontend` kontejneru pomocí následujícího formátu: `http://<external-ip>/helloworld` . Text anglické jazykové verze `helloworld` je předpovězen jako `English` .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s clusterem hotovi, odstraňte skupinu prostředků Azure.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Související informace

* [kubectl pro uživatele Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kontejnery služeb Cognitive Services](../cognitive-services-container-support.md)
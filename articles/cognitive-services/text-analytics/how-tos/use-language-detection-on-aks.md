---
title: Spustit službu Kubernetes
titleSuffix: Text Analytics - Azure Cognitive Services
description: Nasaďte kontejner rozpoznávání jazyka se spuštěnou ukázkou do služby Azure Kubernetes a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: ba6fde66b6173bdbff8e9acc08b16f47c5bf7ea4
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377095"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Nasazení kontejneru rozpoznávání jazyka do služby Azure Kubernetes

Přečtěte si, jak nasadit kontejner rozpoznávání jazyka. V tomto postupu se dozvíte, jak vytvořit místní kontejnery Docker, jak je nasdílet do vlastního privátního registru kontejnerů, spustit kontejner v clusteru Kubernetes a otestovat ho ve webovém prohlížeči. Pomocí kontejnerů můžete posouvat pozornost od správy infrastruktury a místo toho se zaměřit na vývoj aplikací.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které je třeba nainstalovat a spustit místně. Nepoužívejte Azure Cloud Shell. Potřebujete následující:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [Git](https://git-scm.com/downloads) pro váš operační systém, abyste mohli klonovat [ukázku](https://github.com/Azure-Samples/cognitive-services-containers-samples) použitou v tomto postupu.
* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Modul Docker](https://www.docker.com/products/docker-engine)a ověří, že rozhraní příkazového řádku Docker funguje v okně konzoly.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Prostředek Azure se správnou cenovou úrovní. Ne všechny cenové úrovně v tomto kontejneru fungují:
    * **Azure analýza textu** prostředky s cenovou úrovní F0 nebo Standard.
    * Prostředek **Azure Cognitive Services** s cenovou úrovní S0

## <a name="run-the-sample"></a>Spuštění ukázky

Tato procedura načte a spustí ukázku kontejneru Cognitive Services pro detekci jazyka. Vzorek má dva kontejnery, jeden pro klientskou aplikaci a jeden pro Cognitive Services kontejner. Tyto Image je potřeba nasdílet do vaší vlastní instance Azure Container Registry. Až budou ve vašem vlastním registru, vytvořte instanci služby Azure Kubernetes Service (AKS) pro přístup k těmto imagí a spusťte kontejnery. Po spuštění kontejnerů použijte rozhraní příkazového řádku **kubectl** ke sledování výkonu kontejnerů. Přístup k klientské aplikaci pomocí požadavku HTTP a zobrazení výsledků.

![Koncepční nápad spuštění ukázkových kontejnerů](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Ukázkové kontejnery

Ukázka má dvě image kontejneru. Jedna je určena pro front-end Web. Druhý obrázek je kontejner pro detekci jazyka, který vrací zjištěný jazyk (jazykovou verzi) textu. Po dokončení budete mít oba kontejnery přístup z externí IP adresy.

### <a name="the-language-front-end-container"></a>Jazyk front-end kontejneru

Tento web je ekvivalentní vaší vlastní aplikaci na straně klienta, která dává požadavky na koncový bod detekce jazyka. Po dokončení postupu obdržíte zjištěný jazyk řetězce znaků přístupem k kontejneru webu v prohlížeči pomocí `http://<external-IP>/<text-to-analyze>`. Příkladem této adresy URL je `http://132.12.23.255/helloworld!`. Výsledek v prohlížeči je `English`.

### <a name="the-language-container"></a>Kontejner jazyka

V tomto konkrétním postupu je kontejner rozpoznávání jazyka přístupný všem externím žádostem. Kontejner se žádným způsobem nemění, takže je k dispozici standardní Cognitive Services rozhraní API pro detekci jazyka specifického pro kontejner.

Pro tento kontejner je toto rozhraní API požadavkem POST pro detekci jazyka. Stejně jako u všech kontejnerů Cognitive Services můžete získat další informace o kontejneru z jeho hostované informace `http://<external-IP>:5000/swagger/index.html`Swagger.

Port 5000 je výchozím portem používaným pro kontejnery Cognitive Services.

## <a name="create-an-azure-container-registry-service"></a>Vytvoření služby Azure Container Registry

K nasazení kontejneru do služby Azure Kubernetes musí být k dispozici image kontejneru. Vytvořte vlastní službu Azure Container Registry pro hostování imagí.

1. Přihlaste se k Azure CLI.

    ```azurecli
    az login
    ```

1. Vytvořte skupinu prostředků s názvem `cogserv-container-rg` pro uložení každého prostředku vytvořeného tímto postupem.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Vytvořte vlastní instanci Azure Container Registry s formátem názvu následovaným `registry`. Příklad: `pattyregistry`. V názvu nepoužívejte pomlčky ani znaky podtržení.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Uložte výsledky a získejte vlastnost **loginServer** . Tato vlastnost je součástí adresy hostovaného kontejneru, která se používá později v `language.yml` souboru.

    ```console
    > az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
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

1. Přihlaste se ke svému registru kontejneru. Než budete moct do svého registru doručovat image, musíte se přihlásit.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-the-website-docker-image"></a>Získat obrázek Docker webu

1. Vzorový kód použitý v tomto postupu je v úložišti ukázek Cognitive Servicesch kontejnerů. Naklonujte úložiště tak, aby mělo místní kopii ukázky.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Po uložení úložiště na místním počítači Najděte web v adresáři [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) . Tento web slouží jako klientská aplikace, která volá rozhraní API pro detekci jazyka hostovaného v kontejneru rozpoznávání jazyka. 

1. Sestavte image Docker pro tento web. Ujistěte se, že je konzola v adresáři [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) , kde se nachází soubor Docker, když spustíte následující příkaz:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Chcete-li sledovat verzi v registru kontejneru, přidejte značku ve formátu verze, například `v1`. 

1. Nahrajte image do registru kontejneru. Tento krok může trvat několik minut. 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Pokud se zobrazí `unauthorized: authentication required` chyba, přihlaste `az acr login --name <your-container-registry-name>` se pomocí příkazu. 

    Po dokončení procesu jsou výsledky podobné následujícímu:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-the-language-detection-docker-image"></a>Získat obrázek Docker pro detekci jazyka 

1. Stáhněte si nejnovější verzi image Docker do místního počítače. Tento krok může trvat několik minut. Pokud existuje novější verze tohoto kontejneru, změňte hodnotu z `1.1.006770001-amd64-preview` na novější verzi. 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Označte Image pomocí registru kontejnerů. Vyhledejte nejnovější verzi a verzi `1.1.006770001-amd64-preview` nahraďte, pokud máte novější verzi. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Nahrajte image do registru kontejneru. Tento krok může trvat několik minut. 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Získat přihlašovací údaje pro Container Registry

K získání požadovaných informací pro připojení registru kontejneru s instancí služby Azure Kubernetes, kterou vytvoříte později v tomto postupu, je nutné provést následující kroky.

1. Vytvoření instančního objektu.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    V kroku `appId` `<appId>`3 uložte hodnotu výsledků pro parametr zmocnění. Uložte heslo pro parametr `<client-secret>`Client-tajného klíče další sekce.

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Získejte ID registru kontejneru.

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    V dalším kroku uložte výstup hodnoty `<acrId>`parametru Scope. Vypadá takto:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Uložte celou hodnotu pro krok 3 v této části. 

1. Chcete-li udělit správnému přístupu ke clusteru AKS použití imagí uložených ve vašem registru kontejnerů, vytvořte přiřazení role. Hodnoty `<appId>` a`<acrId>` nahraďte hodnotami, které jste shromáždili v předchozích dvou krocích.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-the-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

1. Vytvořte cluster Kubernetes. Všechny hodnoty parametrů jsou z předchozích sekcí s výjimkou parametru name. Vyberte název, který indikuje, kdo ho vytvořil, a jeho účel, `patty-kube`například. 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Tento krok může trvat několik minut. Výsledek je následující: 

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
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

    Služba je vytvořena, ale ještě nemá kontejner webu nebo kontejner rozpoznávání jazyka. 

1. Získejte přihlašovací údaje clusteru Kubernetes. 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Načtení definice orchestrace do služby Kubernetes

V této části se používá rozhraní příkazového řádku **kubectl** ke komunikaci s vaší instancí služby Azure Kubernetes. 

1. Před načtením definice orchestrace ověřte, že **kubectl** má přístup k uzlům.

    ```console
    kubectl get nodes
    ```

    Odpověď vypadá nějak takto:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Zkopírujte následující soubor a pojmenujte ho `language.yml`. Soubor obsahuje `service` oddíl `deployment` a oddíl pro `language-frontend` dva typy kontejnerů, kontejner webu a `language` kontejner detekce. 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Změnou jazyka front-end nasazení `language.yml` v závislosti na následující tabulce můžete přidat vlastní názvy imagí registru kontejneru, tajný klíč klienta a nastavení analýza textu.

    Nastavení jazyka front-end nasazení|Účel|
    |--|--|
    |Řádek 32<br> `image`majetek|Umístění obrázku pro front-end image ve vašem registru kontejneru<br>`<container-registry-name>.azurecr.io/language-frontend:v1`.|
    |Řádek 44<br> `name`majetek|Tajný kód registru kontejneru pro obrázek, který se označuje `<client-secret>` jako v předchozí části.|

1. Změňte řádky `language.yml` nasazení jazyka podle následující tabulky a přidejte tak vlastní názvy imagí registru kontejneru, tajný klíč klienta a nastavení analýza textu.

    |Nastavení nasazení jazyka|Účel|
    |--|--|
    |Řádek 78<br> `image`majetek|Umístění obrázku pro Image jazyka ve vašem registru kontejneru<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`.|
    |Řádek 95<br> `name`majetek|Tajný kód registru kontejneru pro obrázek, který se označuje `<client-secret>` jako v předchozí části.|
    |Řádek 91<br> `apiKey`majetek|Váš klíč prostředku Analýza textu.|
    |Řádek 92<br> `billing`majetek|Koncový bod fakturace pro váš Analýza textu prostředek.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`.|

    Vzhledem k tomu, že vlastnosti **koncového bodu** **apiKey** a fakturace jsou nastaveny jako součást definice orchestrace Kubernetes, nemusí kontejner webu znát tyto informace nebo je předat v rámci žádosti. Kontejner webu odkazuje na kontejner rozpoznávání jazyka podle jeho názvu `language`Orchestrator. 

1. Načte definiční soubor Orchestration pro tuto ukázku ze složky, ve které jste vytvořili a uložili `language.yml`. 

    ```console
    kubectl apply -f language.yml
    ```

    Odpověď:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Získat externí IP adresy kontejnerů

V případě těchto dvou kontejnerů ověřte, `language-frontend` zda `language` jsou spuštěny služby a a získejte externí IP adresu. 

```console
kubectl get all
```

```console
> kubectl get all
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

Otevřete prohlížeč a v předchozí části použijte externí IP adresu `language` kontejneru:. `http://<external-ip>:5000/swagger/index.html` `Try it` Použijte funkci rozhraní API k otestování koncového bodu detekce jazyka. 

![Zobrazit dokumentaci k Swagger kontejneru](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testování kontejneru klientské aplikace

Změňte adresu URL v prohlížeči na externí IP `language-frontend` adresu kontejneru pomocí následujícího formátu:. `http://<external-ip>/helloworld` Text `helloworld` anglické jazykové verze je předpovězen jako `English`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s clusterem hotovi, odstraňte skupinu prostředků Azure. 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Související informace

* [kubectl pro uživatele Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Další kroky 

* Použijte více [Cognitive Services kontejnerů](../../cognitive-services-container-support.md).
* Použijte [Analýza textu připojené služby](../vs-text-connected-service.md).


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->
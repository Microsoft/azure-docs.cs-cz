---
title: Spuštění služby Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Nasazení kontejneru detekce jazyka s ukázku spuštěné ve službě Azure Kubernetes a testování ve webovém prohlížeči.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 01/11/2019
ms.author: diberry
ms.openlocfilehash: 513067f09d8cac64ca747ff217c84667c5469d82
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248234"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Nasazení kontejneru detekce jazyka do služby Azure Kubernetes Service

Zjistěte, jak nasadit kontejner detekce jazyka. Tento postup ukazuje, jak vytvářet místní kontejnery Dockeru, nabízená kontejnery na váš vlastní privátní registr kontejnerů, spusťte kontejner v clusteru Kubernetes a testování ve webovém prohlížeči. 

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které musí být nainstalován a spuštěn místně. Nepoužívejte Azure Cloud shell. 

* Použijte předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [Git](https://git-scm.com/downloads) pro váš operační systém, takže můžete klonovat [ukázka](https://github.com/Azure-Samples/cognitive-services-containers-samples) použité v tomto postupu. 
* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
* [Modul docker](https://www.docker.com/products/docker-engine) a ověřte, že rozhraní příkazového řádku Dockeru funguje v okně konzoly.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe). 
* Prostředek Azure s správné cenovou úroveň. Ne všechny cenové úrovně pracovat s tímto kontejnerem:
    * **Rozhraní text Analytics** pouze úrovní prostředků, které F0 nebo standardní ceny.
    * **Služby cognitive Services** cenová úroveň prostředku s S0.

## <a name="running-the-sample"></a>Spuštění ukázky

Tento postup načte a spustí ukázku kontejneru Cognitive Services pro rozpoznávání jazyka. Ukázka má dva kontejnery, jeden pro klientskou aplikaci a jeden pro kontejner služeb Cognitive Services. Musíte nainstalovat obě tyto bitové kopie do vlastní službě Azure Container Registry. Poté, co jsou na vlastním registru, vytvoření služby Azure Kubernetes pro přístup k těmto obrázkům a spouštět kontejnery. Pokud jsou spuštěné kontejnery, použijte **kubectl** rozhraní příkazového řádku a sledujte výkon kontejnerů. Přístup k aplikaci klienta s žádostí HTTP a zobrazit výsledky. 



## <a name="the-sample-containers"></a>Ukázkových kontejnerů

Ukázka obsahuje dvě Image kontejneru, jednu pro front-end webu. Druhý obrázek je kontejner detekce jazyka, vrátí zjištěný jazyk (jazyková verze) text. Obě kontejnerů jsou přístupné z externí IP adresa, až budete hotovi. 

### <a name="the-language-frontend-container"></a>Kontejner jazyk front-endu

Tento web je stejná jako své aplikaci na straně klienta, který zadává požadavky na koncový bod zjišťování jazyka. Po dokončení postupu získáte zjištěný jazyk řetězec znaků díky přístupu do kontejneru webu v prohlížeči pomocí `http://<external-IP>/<text-to-analyze>`. Je například tuto adresu URL `http://132.12.23.255/helloworld!`. Výsledek v prohlížeči je `English`.

### <a name="the-language-container"></a>Jazyk kontejneru

Kontejneru detekce jazyka v tomto postupu konkrétní je přístupný pro všechny externí požadavek. Kontejner nebylo změněno žádným způsobem tak standardní detekce jazyka specifického pro kontejner služeb Cognitive Services rozhraní API je k dispozici. 

Toto rozhraní API pro tento kontejner, je požadavek POST pro rozpoznání jazyka. Jak s kontejnery všech služeb Cognitive Services, můžete další informace o kontejneru z jeho informace o hostovaných Swagger `http://<external-IP>:5000/swagger/index.html`. 

Port 5000 je výchozí port používá s kontejnery služby Cognitive Services. 

## <a name="create-azure-container-registry-service"></a>Vytvoření služby Azure Container Registry

Nasazení kontejneru do služby Azure Kubernetes Service, musí být dostupné Image kontejneru. Vytvoření vlastní služby Azure Container Registry k hostování imagí. 

1. Přihlaste se k Azure CLI 

    ```azurecli
    az login
    ```

1. Vytvořte skupinu prostředků s názvem `cogserv-container-rg` pro uložení všech prostředků vytvořené v tomto postupu.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Potom vytvořte vlastní Azure Container Registry ve formátu název vaší `registry`, jako například `pattyregistry`. Použijte pomlčky nebo znaky v názvu podtržení.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Uložit výsledky zobrazíte **loginServer** vlastnost. Bude jím částí adresy hostované kontejneru, potřebovat později v `language.yml` souboru.

    ```console
    >az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
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

1. Přihlaste se do vašeho registru kontejneru. Musíte se přihlásit, než budete moct odeslat Image do registru.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Získat image Dockeru webu 

1. Ukázkový kód použitý v tomto postupu se v úložišti ukázek kontejnery služby Cognitive Services. Naklonujte úložiště mít místní kopii ukázky.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Jakmile úložiště na místním počítači, najdete webu [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) adresáře. Tento web funguje jako klientská aplikace volání rozhraní API hostovaná v kontejneru detekce jazyka rozpoznávání jazyka.  

1. Sestavte image Dockeru pro tento web. Ujistěte se, že je konzola v [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) adresáře, kde je soubor Dockerfile umístěn při spuštění následujícího příkazu:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 . 
    ```

    Pokud chcete sledovat verzi nad svým registrem kontejnerů, přidejte značku ve formátu verze `v1`. 

1. Nahrání image do vašeho registru kontejneru. Může to trvat několik minut. 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Pokud dojde `unauthorized: authentication required` chybě, přihlaste se pomocí `az acr login --name <your-container-registry-name>` příkazu. 

    Po dokončení procesu by měl být podobný výsledky:

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

## <a name="get-language-detection-docker-image"></a>Získat image Dockeru detekce jazyka 

1. Stažení nejnovější verze image Dockeru do místního počítače. Může to trvat několik minut. Pokud je novější verze tohoto kontejneru, změňte hodnotu z `1.1.006770001-amd64-preview` na novější verzi. 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Obrázek značky k vašemu registru kontejneru. Nejnovější verze najít a nahradit verzi `1.1.006770001-amd64-preview` máte novější verzi. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Nahrání image do vašeho registru kontejneru. Může to trvat několik minut. 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Získání přihlašovacích údajů registru kontejneru

Chcete-li získat informace potřebné k připojení vašeho registru kontejneru ve službě Azure Kubernetes Service, kterou vytvoříte později v tomto postupu vyžaduje následující kroky.

1. Vytvoření instančního objektu.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Uložit výsledky `appId` hodnotu pro parametr pověřené osoby v kroku 3, `<appId>`. Uložit `password` parametru tajný kód klienta v další části `<client-secret>`.

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

1. Získejte ID vašeho kontejneru registru.

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Uložte si výstup pro hodnotu parametru oboru `<acrId>`, v dalším kroku. Vypadá jako:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Uložte maximum kroku 3 v této části. 

1. K udělení správný přístup pro cluster AKS na používaly Image uložené v registru kontejneru, vytvořte přiřazení role. Nahraďte <appId> a <acrId> hodnotami získanou v předchozí dva kroky.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Vytvoření služby Azure Kubernetes

1. Vytvořte cluster Kubernetes. Všechny hodnoty parametrů jsou z předchozí části s výjimkou parametr name. Zvolte název, který označuje, kdo vytvořil, jako je a jeho účel, `patty-kube`. 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Tento krok může trvat několik minut. Výsledkem je: 

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

    Vytvoření služby, ale je ještě nemá web kontejner nebo kontejner detekce jazyka.  

1. Získání přihlašovacích údajů clusteru Kubernetes. 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Načíst definici Orchestrace do služby Kubernetes

Tato část používá **kubectl** rozhraní příkazového řádku pro komunikaci ve službě Azure Kubernetes Service. 

1. Před načtením definici Orchestrace, zkontrolujte **kubectl** má přístup k uzlům.

    ```console
    kubectl get nodes
    ```

    Odpověď bude vypadat jako:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Zkopírujte tento soubor a pojmenujte ho `language.yml`. Soubor má `service` oddílu a `deployment` části jednotlivých typů dvou kontejnerů `language-frontend` webu kontejneru a `language` detekce kontejneru. 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Změnit jazyk front-endu nasazení řádků `language.yml` podle následující tabulky přidáte vlastní názvy registru imagí kontejnerů, tajný klíč klienta a nastavení analýzy textu.

    Nastavení nasazení jazyka front-endu|Účel|
    |--|--|
    |Řádek 32<br> `image` Vlastnost|Umístění Image pro front-endu image do vašeho registru kontejneru<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Řádek 44<br> `name` Vlastnost|Tajný klíč registru kontejneru pro bitovou kopii, označuje jako `<client-secret>` v předchozí části.|

1. Změnit jazyk nasazení řádků `language.yml` podle následující tabulky přidáte vlastní názvy registru imagí kontejnerů, tajný klíč klienta a nastavení analýzy textu.

    |Nasazení nastavení jazyka|Účel|
    |--|--|
    |Řádku 78<br> `image` Vlastnost|Umístění Image pro jazyk image do vašeho registru kontejneru<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |95 řádku<br> `name` Vlastnost|Tajný klíč registru kontejneru pro bitovou kopii, označuje jako `<client-secret>` v předchozí části.|
    |Řádku 91<br> `apiKey` Vlastnost|Klíč prostředku text analytics|
    |92 řádku<br> `billing` Vlastnost|Fakturační koncový bod pro prostředek text analytics.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

    Protože **apiKey** a **fakturace koncový bod** jsou nastaveny jako součást definice Orchestrace Kubernetes, kontejner webu nemusí vědět o těchto a předat je jako součást požadavku. Kontejner web odkazuje na kontejner detekce jazyka podle názvu produktu orchestrator `language`. 

1. Načíst soubor definice orchestration pro tuto ukázku ze složky, ve kterém jste vytvořili a uložili `language.yml`. 

    ```console
    kubectl apply -f language.yml
    ```

    Odpověď je:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Získání externí IP adresy kontejnerů

Dva kontejnery, ověřte `language-frontend` a `language` services běží a získejte externí IP adresu. 

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

Pokud `EXTERNAL-IP` pro služby se zobrazí jako nedokončené, spusťte znovu příkaz, dokud IP adresa se zobrazí před přechodem k dalšímu kroku. 

## <a name="test-the-language-detection-container"></a>Test kontejneru detekce jazyka

Otevřete prohlížeč a přejděte na externí IP adresu `language` kontejneru z předchozí části: `http://<external-ip>:5000/swagger/index.html`. Můžete použít `Try it` funkce rozhraní API za účelem testování koncový bod zjišťování jazyka. 

![Zobrazit dokumentaci swagger kontejneru.](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Test kontejneru klientské aplikace

Změňte adresu URL v prohlížeči na externí IP adresu `language-frontend` kontejneru v následujícím formátu: `http://<external-ip>/helloworld`. Angličtina culture text `helloworld` očekává se, že jako `English`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s clusterem, odstraňte skupinu prostředků Azure. 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Související informace

* [kubectl pro uživatele Dockeru](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Další postup 

> [!div class="nextstepaction"]
> [Použití rozhraní Text Analytics připojená služba](../vs-text-connected-service.md)


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->
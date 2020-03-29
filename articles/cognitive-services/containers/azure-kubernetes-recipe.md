---
title: Spuštění kontejneru detekce jazyka ve službě Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Nasaďte kontejner detekce jazyka s spuštěnou ukázkou do služby Azure Kubernetes a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 1968bc03bfddb9d6f6c8fe743a2a1a99722c074d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399180"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Nasazení kontejneru detekce jazyka Analýzy textových analýz do služby Azure Kubernetes

Přečtěte si, jak nasadit kontejner pro zjišťování jazyka. Tento postup ukazuje, jak vytvořit místní kontejnery Dockeru, push kontejnery do vlastního registru soukromých kontejnerů, spustit kontejner v clusteru Kubernetes a otestovat ve webovém prohlížeči.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které musí být nainstalovány a spuštěny místně. Nepoužívejte prostředí Azure Cloud.

* Použijte předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* [Git](https://git-scm.com/downloads) pro váš operační systém, takže můžete klonovat [ukázku](https://github.com/Azure-Samples/cognitive-services-containers-samples) použitou v tomto postupu.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker motor u stavařské/ [systému](https://www.docker.com/products/docker-engine) a ověřte, že docker CLI funguje v okně konzoly.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Prostředek Azure se správnou cenovou úrovní. S tímto kontejnerem nefungují všechny cenové úrovně:
  * **Prostředek textové analýzy** pouze s cenovými úrovněmi F0 nebo Standard.
  * **Prostředek služeb Cognitive Services** s cenovou úrovní S0.

## <a name="running-the-sample"></a>Spuštění ukázky

Tento postup načte a spustí ukázku kontejneru služeb Cognitive Services pro detekci jazyka. Ukázka má dva kontejnery, jeden pro klientskou aplikaci a jeden pro kontejner Cognitive Services. Obě tyto image předáme do registru kontejnerů Azure. Jakmile jsou ve vašem vlastním registru, vytvořte službu Azure Kubernetes pro přístup k těmto bitovým kopiím a spusťte kontejnery. Když jsou spuštěny kontejnery, použijte **kubectl** CLI sledovat výkon kontejnerů. Přístup k klientské aplikaci s požadavkem HTTP a zobrazit výsledky.

![Koncepční myšlenka spuštění ukázkových kontejnerů](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Vzorkovnice

Ukázka má dvě image kontejneru, jeden pro web frontendu. Druhý obrázek je kontejner detekce jazyka, který vrací detekovaný jazyk (jazykovou verzi) textu. Oba kontejnery jsou přístupné z externí IP adresy po dokončení.

### <a name="the-language-frontend-container"></a>Kontejner frontendu jazyka

Tato webová stránka je ekvivalentní vaší vlastní aplikaci na straně klienta, která provádí požadavky koncového bodu detekce jazyka. Po dokončení procedury získáte zjištěný jazyk řetězce znaků přístupem k kontejneru webu v `http://<external-IP>/<text-to-analyze>`prohlížeči s . Příkladem této adresy `http://132.12.23.255/helloworld!`URL je . Výsledkem v prohlížeči je `English`.

### <a name="the-language-container"></a>Kontejner jazyka

Kontejner detekce jazyka v tomto konkrétním postupu je přístupný pro všechny externí požadavky. Kontejner nebyl žádným způsobem změněn, takže je k dispozici standardní rozhraní API pro detekci jazyka specifické pro kontejner cognitive services.

Pro tento kontejner, že rozhraní API je požadavek POST pro detekci jazyka. Stejně jako u všech kontejnerů služeb Cognitive Services se můžete dozvědět více o kontejneru z jeho hostovaných informací Swagger. `http://<external-IP>:5000/swagger/index.html`

Port 5000 je výchozí port používaný s kontejnery cognitive services.

## <a name="create-azure-container-registry-service"></a>Vytvoření služby Azure Container Registry

Chcete-li nasadit kontejner do služby Azure Kubernetes, musí být přístupné image kontejneru. Vytvořte si vlastní službu Azure Container Registry pro hostování bitových kopií.

1. Přihlášení k azure cli

    ```azurecli-interactive
    az login
    ```

1. Vytvořte skupinu `cogserv-container-rg` prostředků s názvem pro všechny prostředky vytvořené v tomto postupu.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Vytvořte si vlastní Azure Container Registry `registry`s formátem vašeho jména pak , například `pattyregistry`. V názvu nepoužívejte pomlčky ani podtržení.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Uložte výsledky získat **loginServer** vlastnost. Bude součástí adresy hostovaného kontejneru, která bude `language.yml` použita později v souboru.

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

1. Přihlaste se do registru kontejnerů. Musíte se přihlásit, než budete moci tlačit obrázky do registru.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Získat obrázek Dockeru na webu

1. Ukázkový kód použitý v tomto postupu je v úložišti ukázky kontejnerů služby Cognitive Services. Klonovat úložiště mít místní kopii vzorku.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Jakmile je úložiště v místním počítači, vyhledejte web v adresáři [\dotnet\Language\FrontendService.](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) Tato webová stránka funguje jako klientská aplikace, která volá rozhraní API pro detekci jazyka hostované v kontejneru pro detekci jazyka.  

1. Vytvořte image Dockeru pro tento web. Ujistěte se, že konzola je v adresáři [\FrontendService,](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) kde je umístěn Dockerfile při spuštění následujícího příkazu:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Chcete-li sledovat verzi v registru kontejnerů, přidejte `v1`značku s formátem verze, například .

1. Zasuňte bitovou kopii do registru kontejnerů. Může to trvat několik minut.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Pokud se `unauthorized: authentication required` zobrazí chyba, `az acr login --name <your-container-registry-name>` přihlaste se pomocí příkazu. 

    Po dokončení procesu by výsledky měly být podobné:

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

## <a name="get-language-detection-docker-image"></a>Získání image Dockeru pro detekci jazyka

1. Vyžádej nejnovější verzi image Dockeru do místního počítače. Může to trvat několik minut. Pokud existuje novější verze tohoto kontejneru, změňte hodnotu z `1.1.006770001-amd64-preview` na novější verzi.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Označení bitové kopie pomocí registru kontejnerů Najděte nejnovější verzi a `1.1.006770001-amd64-preview` nahraďte ji, pokud máte novější verzi. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Zasuňte bitovou kopii do registru kontejnerů. Může to trvat několik minut.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Získání přihlašovacích údajů registru kontejneru

Následující kroky jsou potřebné k získání požadovaných informací pro připojení registru kontejnerů se službou Azure Kubernetes, kterou vytvoříte později v tomto postupu.

1. Vytvořte instanční objekt.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Uložte `appId` hodnotu výsledků parametru postupníka v kroku 3 . `<appId>` `password` Uložte pro další část klient-tajný `<client-secret>`parametr .

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

    V dalším kroku uložte `<acrId>`výstup pro hodnotu parametru oboru , v dalším kroku. Vypadá to takto:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    V této části uložte úplnou hodnotu pro krok 3.

1. Chcete-li clusteru AKS udělit správný přístup k použití bitových kopií uložených v registru kontejnerů, vytvořte přiřazení role. Nahraďte `<appId>` a `<acrId>` s hodnotami shromážděnými v předchozích dvou krocích.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Vytvoření služby Azure Kubernetes

1. Vytvořte cluster Kubernetes. Všechny hodnoty parametrů jsou z předchozích oddílů kromě parametru name. Zvolte název, který označuje, kdo ho `patty-kube`vytvořil a jeho účel, například .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Tento krok může trvat několik minut. Výsledkem je:

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

    Služba je vytvořena, ale ještě nemá kontejner webu nebo kontejner pro detekci jazyka.  

1. Získejte pověření clusteru Kubernetes.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Načtení definice orchestrace do služby Kubernetes

Tato část používá k rozhovoru **se** službou Azure Kubernetes.

1. Před načtením definice orchestrace zkontrolujte, zda **kubectl** má přístup k uzlům.

    ```console
    kubectl get nodes
    ```

    Odpověď vypadá takto:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Zkopírujte následující soubor `language.yml`a pojmenujte jej . Soubor má `service` oddíl a `deployment` oddíl pro dva typy `language-frontend` kontejnerů, `language` kontejner webu a kontejner zjišťování.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Změňte řádky nasazení frontendu jazyků `language.yml` na základě následující tabulky a přidejte vlastní názvy obrázků registru kontejneru, tajný klíč klienta a nastavení analýzy textu.

    Nastavení nasazení front-endu jazyka|Účel|
    |--|--|
    |Řádek 32<br> `image`Vlastnost|Umístění obrázku pro bitovou kopii front-endu v registru kontejnerů<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Řádek 44<br> `name`Vlastnost|Tajný klíč registru kontejnerů pro `<client-secret>` bitovou kopii, označovaný jako v předchozí části.|

1. Změňte řádky nasazení `language.yml` jazyka na základě následující tabulky a přidejte vlastní názvy bitových obrázků registru kontejneru, tajný klíč klienta a nastavení analýzy textu.

    |Nastavení nasazení jazyka|Účel|
    |--|--|
    |Řádek 78<br> `image`Vlastnost|Umístění obrázku pro bitovou kopii jazyka v registru kontejnerů<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Řádek 95<br> `name`Vlastnost|Tajný klíč registru kontejnerů pro `<client-secret>` bitovou kopii, označovaný jako v předchozí části.|
    |Řádek 91<br> `apiKey`Vlastnost|Klíč prostředku analýzy textu|
    |Řádek 92<br> `billing`Vlastnost|Koncový bod fakturace pro prostředek analýzy textu.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Vzhledem k tomu, že koncový bod **apiKey** a **fakturace** jsou nastaveny jako součást definice orchestrace Kubernetes, kontejner webu nemusí vědět o těchto nebo předat jako součást požadavku. Kontejner webu odkazuje na kontejner detekce jazyka podle `language`názvu orchestrátoru .

1. Načtěte soubor definice orchestrace pro tuto ukázku `language.yml`ze složky, do které jste vytvořili a uložili soubor .

    ```console
    kubectl apply -f language.yml
    ```

    Odpověď je:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Získání externích IP adresy kontejnerů

Pro dva kontejnery `language-frontend` ověřte a `language` služby jsou spuštěny a získat externí IP adresu.

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

`EXTERNAL-IP` Pokud pro službu se zobrazí jako čekající na vyřízení, znovu spusťte příkaz, dokud IP adresa se zobrazí před přechodem na další krok.

## <a name="test-the-language-detection-container"></a>Testování kontejneru pro detekci jazyka

Otevřete prohlížeč a přejděte na `language` externí IP adresu `http://<external-ip>:5000/swagger/index.html`kontejneru z předchozí části: . Funkci rozhraní `Try it` API můžete použít k testování koncového bodu detekce jazyka.

![Zobrazit dokumentaci kontejneru naparování](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testování kontejneru klientské aplikace

Změňte adresu URL v prohlížeči `language-frontend` na externí IP `http://<external-ip>/helloworld`adresu kontejneru v následujícím formátu: . Text anglické jazykové `helloworld` verze je `English`předpovězen jako .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení clusteru odstraňte skupinu prostředků Azure.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Související informace

* [kubectl pro uživatele Dockeru](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kontejnery služeb cognitive services](../cognitive-services-container-support.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->
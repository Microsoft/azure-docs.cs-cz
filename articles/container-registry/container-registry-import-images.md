---
title: Import imagí kontejnerů
description: Naimportujte image kontejneru do služby Azure Container Registry pomocí rozhraní API Azure, aniž byste museli spouštět příkazy Docker.
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 3950b9fb24b80db4d9654a615521c0eb82914499
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695601"
---
# <a name="import-container-images-to-a-container-registry"></a>Import imagí kontejneru do registru kontejneru

Můžete snadno importovat (kopírovat) image kontejneru do služby Azure Container Registry bez použití příkazů Docker. Například importujte obrázky z registru pro vývoj do produkčního registru nebo zkopírujte základní image z veřejného registru.

Azure Container Registry zpracovává řadu běžných scénářů pro kopírování imagí z existujícího registru:

* Import z veřejného registru

* Import z jiného služby Azure Container Registry ve stejném nebo jiném předplatném Azure nebo tenantovi

* Import z privátního registru kontejnerů mimo Azure

Import obrázku do služby Azure Container Registry má oproti použití příkazů Docker CLI následující výhody:

* Vzhledem k tomu, že vaše klientské prostředí nepotřebuje místní instalaci Docker, importujte veškerou image kontejneru bez ohledu na podporovaný typ operačního systému.

* Při importu imagí s více architekturami (například oficiálních imagí Docker) se zkopírují image pro všechny architektury a platformy, které jsou uvedené v seznamu manifestů.

* Přístup k cílovému registru nemusí používat veřejný koncový bod registru.

Aby bylo možné importovat image kontejnerů, Tento článek vyžaduje, abyste spustili Azure CLI v Azure Cloud Shell nebo lokálně (doporučuje se verze 2.0.55 nebo novější). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

> [!NOTE]
> Pokud potřebujete distribuovat identické image kontejneru napříč několika oblastmi Azure, Azure Container Registry podporuje taky [geografickou replikaci](container-registry-geo-replication.md). Když geograficky replikuje registr (vyžaduje se úroveň Premium Service), můžete zajišťovat více oblastí s identickými názvy obrázků a značek z jednoho registru.
>

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte službu Azure Container Registry, vytvořte registr. Postup najdete v tématu [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md).

K importu image do služby Azure Container Registry musí mít vaše identita oprávnění k zápisu do cílového registru (minimálně role přispěvatele nebo vlastní role, která umožňuje akci importImage). Viz [Azure Container Registry role a oprávnění](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Import z veřejného registru

### <a name="import-from-docker-hub"></a>Import z dokovacího centra

Pomocí příkazu [AZ ACR import][az-acr-import] můžete například importovat image s více architekturami `hello-world:latest` z Docker Hub do registru s názvem *myregistry*. Vzhledem k `hello-world` tomu, že se jedná o oficiální image z Docker Hub, je tento obrázek ve výchozím `library` úložišti. Zahrňte název úložiště a volitelně značku v hodnotě `--source` parametru image. (Volitelně můžete identifikovat obrázek pomocí výtahu manifestu namísto značky, který garantuje konkrétní verzi obrázku.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Spuštěním příkazu můžete ověřit, zda je k této imagi přidruženo více manifestů `az acr repository show-manifests` :

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Následující příklad importuje veřejnou image z `tensorflow` úložiště v Docker Hub:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importovat z Microsoft Container Registry

Například importujte `ltsc2019` základní bitovou kopii Windows serveru z `windows` úložiště v Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Import z Azure Container Registry ve stejném tenantovi AD

Pomocí integrovaných oprávnění Azure Active Directory můžete importovat image ze služby Azure Container Registry do stejného tenanta služby AD.

* Vaše identita musí mít Azure Active Directory oprávnění ke čtení ze zdrojového registru (role čtenář) a pro import do cílového registru (role přispěvatele nebo [vlastní role](container-registry-roles.md#custom-roles) , která umožňuje akci importImage).

* Registr se může nacházet ve stejném nebo jiném předplatném Azure ve stejném tenantovi Active Directory.

* [Veřejný přístup](container-registry-access-selected-networks.md#disable-public-network-access) ke zdrojovému registru může být zakázaný. Pokud je veřejný přístup zakázaný, místo názvu přihlašovacího serveru registru zadejte zdrojový registr podle ID prostředku.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Import z registru ve stejném předplatném

Naimportujte například `aci-helloworld:latest` image ze zdrojového registru *mysourceregistry* do *myregistry* ve stejném předplatném Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Následující příklad importuje `aci-helloworld:latest` image do *myregistry* ze zdrojového registru *mysourceregistry* , ve kterém je zakázaný přístup ke veřejnému koncovému bodu registru. Zadejte ID prostředku zdrojového registru s `--registry` parametrem. Všimněte si, že `--source` parametr určuje pouze zdrojové úložiště a značku, nikoli název přihlašovacího serveru registru.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

Následující příklad importuje obrázek pomocí algoritmu Digest (SHA-256 hash, který je reprezentován jako `sha256:...` ) místo podle značky:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Import z registru v jiném předplatném

V následujícím příkladu je *mysourceregistry* v jiném předplatném, než je *myregistry* ve stejném tenantovi Active Directory. Zadejte ID prostředku zdrojového registru s `--registry` parametrem. Všimněte si, že `--source` parametr určuje pouze zdrojové úložiště a značku, nikoli název přihlašovacího serveru registru.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Import z registru pomocí přihlašovacích údajů instančního objektu

Pokud chcete importovat z registru, ke kterému nemáte přístup pomocí integrovaných oprávnění služby Active Directory, můžete k zdrojovému registru použít přihlašovací údaje instančního objektu (pokud jsou k dispozici). Zadejte appID a heslo [instančního objektu služby](container-registry-auth-service-principal.md) Active Directory, který má ACRPull přístup ke zdrojovému registru. Použití instančního objektu je užitečné pro systémy sestavení a další bezobslužné systémy, které potřebují importovat image do registru.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Import z Azure Container Registry v jiném tenantovi AD

Pokud chcete importovat z služby Azure Container Registry v jiném Azure Active Directory tenantovi, zadejte zdrojový registr podle názvu přihlašovacího serveru a zadejte přihlašovací údaje pro uživatelské jméno a heslo, které umožní přístup pro přístup k registru. Můžete například použít token a heslo v [oboru úložiště](container-registry-repository-scoped-permissions.md) nebo AppID a heslo [instančního objektu služby](container-registry-auth-service-principal.md) Active Directory, který má ACRPull přístup ke zdrojovému registru. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Import z privátního registru kontejnerů mimo Azure

Naimportujte image z privátního registru jiného typu než Azure tím, že zadáte přihlašovací údaje, které umožní přístup k získání přístupu do registru. Načetli byste třeba obrázek z privátního registru Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o importování imagí kontejneru do služby Azure Container Registry z veřejného registru nebo jiného privátního registru. Další možnosti importu obrázků najdete v tématu [AZ ACR import][az-acr-import] Command reference. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli

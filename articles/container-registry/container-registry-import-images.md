---
title: Import imagí kontejnerů
description: Importujte ibi kontejnerů do registru kontejnerů Azure pomocí azure api, aniž byste museli spouštět příkazy Dockeru.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: caf7a47ac8f7ff0e72d2e049a7013542d274a225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051924"
---
# <a name="import-container-images-to-a-container-registry"></a>Import iobrazek kontejneru do registru kontejnerů

Můžete snadno importovat (kopírovat) ibi kontejnerů do registru kontejnerů Azure, bez použití příkazů Dockeru. Můžete například importovat bitové kopie z vývojového registru do produkčního registru nebo zkopírovat základní bitové kopie z veřejného registru.

Azure Container Registry zpracovává řadu běžných scénářů pro kopírování ibi z existujícího registru:

* Import z veřejného registru

* Import z jiného registru kontejnerů Azure ve stejném nebo jiném předplatném Azure

* Import z registru privátního kontejneru, který není azure

Import bitových obrázků do registru kontejnerů Azure má následující výhody než pomocí příkazů Docker CLI:

* Vzhledem k tomu, že vaše klientské prostředí nepotřebuje místní instalaci Dockeru, importujte libovolnou image kontejneru bez ohledu na podporovaný typ operačního systému.

* Při importu bitových kopií s více architekturami (například oficiálních irek dockeru) se zkopírují image pro všechny architektury a platformy zadané v seznamu manifestů.

Chcete-li importovat image kontejneru, tento článek vyžaduje spuštění příkazového příkazu k onomu Azure v Prostředí Azure Cloud Shell nebo místně (verze 2.0.55 nebo novější doporučeno). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

> [!NOTE]
> Pokud potřebujete distribuovat identické iimage kontejneru ve více oblastech Azure, Azure Container Registry také podporuje [geografickou replikaci](container-registry-geo-replication.md). Geografickou replikací registru (požadována úroveň služby Premium) můžete obsluhovat více oblastí s identickými názvy bitových obrázků a značek z jednoho registru.
>

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte registr kontejnerů Azure, vytvořte registr. Postup najdete [v tématu Úvodní příručka: Vytvoření registru privátního kontejneru pomocí azure cli](container-registry-get-started-azure-cli.md).

Chcete-li importovat bitovou kopii do registru kontejnerů Azure, musí mít vaše identita oprávnění k zápisu do cílového registru (alespoň role přispěvatele). Viz [Role a oprávnění registru kontejnerů Azure](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Import z veřejného registru

### <a name="import-from-docker-hub"></a>Import z centra Docker

Pomocí příkazu [az acr import][az-acr-import] můžete například `hello-world:latest` importovat image více architektur z centra Docker Hub do registru s názvem *myregistry*. Vzhledem k tomu, že `hello-world` je oficiální image `library` z Docker Hub, tato image je ve výchozím úložišti. Do hodnoty parametru `--source` obrázku zahrňte název úložiště a volitelně značku. (Volitelně můžete identifikovat obraz podle jeho manifestdigest místo podle značky, která zaručuje určitou verzi bitové kopie.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Spuštěním příkazu `az acr repository show-manifests` můžete ověřit, že k této bitové kopii je přidruženo více manifestů:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Následující příklad importuje veřejnou `tensorflow` bitovou kopii z úložiště v Docker Hubu:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Import z registru kontejnerů společnosti Microsoft

Můžete například importovat nejnovější bitovou `windows` kopii Jádra systému Windows Server z úložiště v registru kontejnerů společnosti Microsoft.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:latest \
--image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Import z jiného registru kontejnerů Azure

Bitovou kopii můžete importovat z jiného registru kontejnerů Azure pomocí integrovaných oprávnění služby Azure Active Directory.

* Vaše identita musí mít oprávnění služby Azure Active Directory ke čtení ze zdrojového registru (role Čtečka) a k zápisu do cílového registru (role přispěvatele).

* Registr může být ve stejném nebo jiném předplatném Azure ve stejném tenantovi služby Active Directory.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Import z registru ve stejném předplatném

Například importovat `aci-helloworld:latest` bitovou kopii ze zdrojového registru *mysourceregistry* do *myregistry* ve stejném předplatném Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Následující příklad importuje obraz podle manifestu digest (SHA-256 hash, reprezentovaný jako) `sha256:...`namísto podle značky:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Import z registru v jiném předplatném

V následujícím příkladu *je mysourceregistry* v jiném předplatném než *myregistry* ve stejném tenantovi služby Active Directory. Zadej `--registry` parametr ID prostředku zdrojového registru. Všimněte `--source` si, že parametr určuje pouze zdrojové úložiště a značku, nikoli název přihlašovacího serveru registru.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Import z registru pomocí pověření instančního objektu

Chcete-li importovat z registru, ke kterému nelze získat přístup pomocí oprávnění služby Active Directory, můžete použít pověření zaregistrovaný objekt služby (pokud jsou k dispozici). Zadejme id aplikace a heslo [objektu služby](container-registry-auth-service-principal.md) Active Directory, který má přístup ACRPull ke zdrojovému registru. Použití instančního objektu je užitečné pro systémy sestavení a další bezobslužné systémy, které je třeba importovat bitové kopie do registru.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Import z registru privátního kontejneru, který není azure

Importujte bitovou kopii ze soukromého registru zadáním pověření, která umožňují přístup vyžádat do registru. Například natahovací bitovou kopii z soukromého registru Dockeru: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o importu ibi kontejnerů do registru kontejnerů Azure z veřejného registru nebo jiného privátního registru. Další volby importu obrazu najdete v odkazu příkazu [az acr import.][az-acr-import] 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli

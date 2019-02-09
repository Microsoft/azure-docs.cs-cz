---
title: Importovat Image kontejneru do služby Azure Container Registry
description: Importujte Image kontejneru do služby Azure container registry pomocí Azure API, aniž byste museli spouštět příkazy Dockeru.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: 8e9f488f194c3326e79439a65214a060ff16e6c4
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958744"
---
# <a name="import-container-images-to-a-container-registry"></a>Importovat Image kontejneru do registru kontejneru

Image kontejnerů (kopie) můžete snadno importovat do služby Azure container registry, bez použití příkazy Dockeru. Například importovat Image z registru vývoje do produkčního prostředí registru nebo zkopírujte základní Image z veřejného registru.

Služba Azure Container Registry zpracovává řadu běžných scénářů kopírování imagí z existující registr:

* Importovat z veřejného registru

* Import z registru kontejneru Azure, v rámci stejného nebo jiného předplatného Azure

* Importovat z jiné než Azure privátní registr kontejneru

Importovat Image do služby Azure container registry pomocí příkazů rozhraní příkazového řádku Dockeru má následující výhody:

* Protože prostředí klient nepotřebuje místní instalaci Dockeru, importujte všechny image kontejneru, bez ohledu na podporované typ operačního systému.

* Když importujete více Architektura bitové kopie (například oficiální Image Dockeru), se zkopíruje Image pro všechny architektury a platformy podle seznamu manifestu.

Importovat Image kontejneru, tento článek vyžaduje, abyste spustili Azure CLI ve službě Azure Cloud Shell nebo místně (verze 2.0.55 nebo později doporučené). Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

> [!NOTE]
> Pokud potřebujete k distribuci identické kontejnerové Image ve víc oblastech Azure, Azure Container Registry podporuje také [geografickou replikaci](container-registry-geo-replication.md). Díky geografické replikaci v registru (vyžaduje SKU úrovně Premium) může sloužit několika oblastmi se stejným názvem image a značky z jednoho registru.
>

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte služby Azure container registry, vytvořte registr. Pokyny najdete v tématu [rychlý start: Vytvořit privátní registr pomocí rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md).

Importovat image do služby Azure container registry, vaši identitu musí mít oprávnění k zápisu do registru cíl (nejméně role Přispěvatel). Zobrazit [Azure Container Registry role a oprávnění](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importovat z veřejného registru

### <a name="import-from-docker-hub"></a>Import z Docker Hubu

Například použít [az acr import] [ az-acr-import] příkaz pro import více architektura `hello-world:latest` image z Docker Hubu registru s názvem *myregistry*. Protože `hello-world` je oficiální image z Docker Hubu, tato image je ve výchozím `library` úložiště. Zahrnout hodnotu názvu úložiště a volitelně značku `--source` parametr image. (Můžete volitelně poznat bitovou kopii podle jeho manifestu výtah podle klíčových slov, což zaručuje určité verze image.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

Můžete ověřit, že více manifestů jsou přidruženy k této bitové kopii spuštěním `az acr repository show-manifests` příkaz:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

Následující příklad importuje veřejnou image z `tensorflow` úložiště Docker Hub:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Import ze služby Microsoft Container Registry

Například importovat z nejnovější image systému Windows Server Core `windows` úložiště ve službě Microsoft Container Registry.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importovat z jiné registru kontejneru Azure

Image můžete importovat z jiné registru kontejnerů Azure pomocí integrovaného oprávnění služby Azure Active Directory.

* Azure Active Directory oprávnění ke čtení z registru zdroje (role čtenáře) a zápis do registru cíl (role Přispěvatel) musí mít svoji identitu.

* Registru může být ve stejné nebo jiné předplatné Azure ve stejném tenantovi Active Directory.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importovat z registru ve stejném předplatném

Například importovat `aci-helloworld:latest` image z registru zdrojové *mysourceregistry* k *myregistry* ve stejném předplatném Azure.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

Následující příklad importuje bitovou kopii podle manifestu digest (hodnota hash SHA-256, vyjádřené `sha256:...`) místo podle značky:

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importovat z registru v jiném předplatném.

V následujícím příkladu *mysourceregistry* je v jiném předplatném než *myregistry* ve stejném tenantovi Active Directory. Zadat ID prostředku zdrojového registru `--registry` parametru. Všimněte si, že `--source` parametr určuje pouze úložišť a imagí název zdroje, ne registru název přihlašovacího serveru.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importovat z registru pomocí přihlašovacích údajů instančního objektu služby

Pokud chcete importovat z registru, který nelze přistupovat pomocí oprávnění služby Active Directory, můžete použít pověření instančního objektu (Pokud je k dispozici). Zadejte ID aplikace a hesla služby Active Directory [instanční objekt služby](container-registry-auth-service-principal.md) , který má přístup ACRPull do zdrojového registru. Pomocí hlavního názvu služby je užitečné pro sestavení a jiných bezobslužné systémů, které je potřeba importovat Image do registru.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importovat z jiné než Azure privátní registr kontejneru

Importujte image z privátního registru tak, že zadáte přihlašovací údaje, které umožňují přístup o přijetí změn v registru. Například stáhněte si image z privátního registru Dockeru: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o import imagí kontejneru do služby Azure container registry z veřejného registru nebo jiného privátního registru. Možnosti importu další image, najdete v článku [az acr import] [ az-acr-import] referenčních příkazu. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
---
title: Vytvoření obrázku Azure Container Registry z aplikace
description: Pomocí příkazu AZ ACR Pack Build sestavíte image kontejneru z aplikace a nahrajete ji do Azure Container Registry bez použití souboru Dockerfile.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/24/2019
ms.author: danlep
ms.openlocfilehash: 34ef0fe4be00cfa7ce3e73c23eec636784071e56
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965899"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Sestavení a vložení obrázku z aplikace pomocí cloudového nativního Buildpacku

Příkaz Azure CLI `az acr pack build` používá k sestavení aplikace a vložení jeho image do služby Azure Container Registry nástroj příkazového řádku [`pack`](https://github.com/buildpack/pack) z [Buildpacks](https://buildpacks.io/). Tato funkce poskytuje možnost rychlého sestavení image kontejneru ze zdrojového kódu vaší aplikace v Node. js, Java a dalších jazycích bez nutnosti definovat souboru Dockerfile.

Příklady v tomto článku můžete spustit pomocí Azure Cloud Shell nebo místní instalace rozhraní příkazového řádku Azure CLI. Pokud ho chcete používat místně, je potřeba verze 2.0.70 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="use-the-build-command"></a>Použití příkazu Build

Pokud chcete vytvořit a nasdílet image kontejneru pomocí cloudového nativního Buildpacksu, spusťte příkaz [AZ ACR Pack Build][az-acr-pack-build] . Vzhledem k tomu, že příkaz [AZ ACR Build sestaví][az-acr-build] a nahraje obrázek ze zdrojového souboru Dockerfile a souvisejícího kódu, s `az acr pack build` určíte zdrojový strom aplikace přímo.

Při spuštění `az acr pack build` zadejte minimálně následující:

* Registr kontejnerů Azure, kde spustíte příkaz
* Název a značka obrázku pro výsledný obrázek
* Jedno z [podporovaných umístění kontextu](container-registry-tasks-overview.md#context-locations) pro ACR úlohy, jako je například místní adresář, úložiště GitHub nebo vzdálené tarballu
* Název obrázku tvůrce Buildpack, který je vhodný pro vaši aplikaci. Azure Container Registry ukládá do mezipaměti image tvůrce, jako je například `cloudfoundry/cnb:0.0.34-cflinuxfs3` pro rychlejší sestavení.  

`az acr pack build` podporuje další funkce příkazů úloh ACR, včetně [proměnných pro spuštění](container-registry-tasks-reference-yaml.md#run-variables) a [protokolů spuštění úloh](container-registry-tasks-overview.md#view-task-logs) , které jsou streamované a také uložené pro pozdější načtení.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Příklad: sestavení obrázku Node. js pomocí nástroje Cloud Foundry Builder

Následující příklad vytvoří image kontejneru z aplikace Node. js v úložišti [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) pomocí Tvůrce `cloudfoundry/cnb:0.0.34-cflinuxfs3`. Tento tvůrce ukládá do mezipaměti Azure Container Registry, takže parametr `--pull` není povinný:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Tento příklad vytvoří bitovou kopii `node-app` pomocí značky `1.0` a vloží ji do registru kontejneru *myregistry* . V tomto příkladu je název cílového registru explicitně připojen k názvu bitové kopie. Pokud tento parametr nezadáte, název přihlašovacího serveru registru se automaticky přidá do názvu image.

Výstup příkazu zobrazuje průběh sestavení a vložení image. 

Po úspěšném vytvoření image ji můžete spustit s Docker, pokud ji máte nainstalovanou. Nejdřív se přihlaste ke svému registru:

```azurecli
az acr login --name myregistry
```

Spusťte bitovou kopii:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Pokud si chcete prohlédnout ukázkovou webovou aplikaci, přejděte do složky `localhost:1337` v oblíbeném prohlížeči. Kontejner zastavíte stisknutím `[Ctrl]+[C]`.

## <a name="example-build-java-image-with-heroku-builder"></a>Příklad: sestavení image Java pomocí Tvůrce Heroku

Následující příklad vytvoří image kontejneru z aplikace Java v úložišti [buildpack/Sample-Java-App](https://github.com/buildpack/sample-java-app) pomocí nástroje `heroku/buildpacks:18` Builder. Parametr `--pull` určuje, že by příkaz měl načíst nejnovější image tvůrce. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Tento příklad vytvoří obrázek `java-app` označený s ID spuštění příkazu a vloží ho do registru kontejneru *myregistry* .

Výstup příkazu zobrazuje průběh sestavení a vložení image. 

Po úspěšném vytvoření image ji můžete spustit s Docker, pokud ji máte nainstalovanou. Nejdřív se přihlaste ke svému registru:

```azurecli
az acr login --name myregistry
```

Spusťte image a nahraďte značku image pro *RunId*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Pokud si chcete prohlédnout ukázkovou webovou aplikaci, přejděte do složky `localhost:8080` v oblíbeném prohlížeči. Kontejner zastavíte stisknutím `[Ctrl]+[C]`.


## <a name="next-steps"></a>Další kroky

Po sestavení a nahrání image kontejneru s `az acr pack build` ji můžete nasadit jako libovolný obrázek do cíle podle vašeho výběru. Možnosti nasazení do Azure zahrnují spouštění IT ve službě [App Service](../app-service/containers/tutorial-custom-docker-image.md) nebo [Azure Kubernetes](../aks/tutorial-kubernetes-deploy-cluster.md), a to mimo jiné.

Další informace o funkcích úloh ACR najdete v tématu [Automatizace sestavení a údržby imagí kontejneru pomocí úloh ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

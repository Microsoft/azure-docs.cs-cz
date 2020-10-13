---
title: Sestavení image s nativním cloudovým Buildpackm
description: Pomocí příkazu AZ ACR Pack Build sestavíte image kontejneru z aplikace a nahrajete ji do Azure Container Registry bez použití souboru Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: cad7d5c066dd8c8a7468833cb67e6085f067417a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262106"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Sestavení a vložení obrázku z aplikace pomocí cloudového nativního Buildpacku

Příkaz rozhraní příkazového řádku Azure `az acr pack build` využívá [`pack`](https://github.com/buildpack/pack) Nástroj CLI z [Buildpacks](https://buildpacks.io/)k sestavení aplikace a vložení jeho image do služby Azure Container Registry. Tato funkce poskytuje možnost rychle vytvořit image kontejneru ze zdrojového kódu vaší aplikace v Node.js, Java a dalších jazycích, aniž by bylo nutné definovat souboru Dockerfile.

Příklady v tomto článku můžete spustit pomocí Azure Cloud Shell nebo místní instalace rozhraní příkazového řádku Azure CLI. Pokud ho chcete používat místně, je potřeba verze 2.0.70 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="use-the-build-command"></a>Použití příkazu Build

Pokud chcete vytvořit a nasdílet image kontejneru pomocí cloudového nativního Buildpacksu, spusťte příkaz [AZ ACR Pack Build][az-acr-pack-build] . Vzhledem k tomu, že příkaz [AZ ACR Build sestaví][az-acr-build] a nahraje obrázek ze zdrojového souboru Dockerfile a souvisejícího kódu, `az acr pack build` určíte zdrojový strom aplikace přímo.

Pokud spustíte následující příkaz, zadejte alespoň tento příkaz `az acr pack build` :

* Registr kontejnerů Azure, kde spustíte příkaz
* Název a značka obrázku pro výsledný obrázek
* Jedno z [podporovaných umístění kontextu](container-registry-tasks-overview.md#context-locations) pro ACR úlohy, jako je například místní adresář, úložiště GitHub nebo vzdálené tarballu
* Název obrázku tvůrce Buildpack, který je vhodný pro vaši aplikaci. Azure Container Registry ukládá do mezipaměti image tvůrce, například `cloudfoundry/cnb:0.0.34-cflinuxfs3` pro rychlejší sestavení.  

`az acr pack build` podporuje další funkce příkazů ACR Tasks, včetně [proměnných pro spuštění](container-registry-tasks-reference-yaml.md#run-variables) a [protokolů spuštění úloh](container-registry-tasks-logs.md) , které jsou streamované a také uložené pro pozdější načtení.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Příklad: sestavení Node.js Image pomocí Tvůrce Cloud Foundry

Následující příklad vytvoří image kontejneru z Node.js aplikace v úložišti [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) pomocí `cloudfoundry/cnb:0.0.34-cflinuxfs3` Tvůrce. Tento tvůrce ukládá do mezipaměti Azure Container Registry, takže `--pull` parametr není povinný:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Tento příklad `node-app` sestaví image se `1.0` značkou a vloží ji do registru kontejneru *myregistry* . V tomto příkladu je název cílového registru explicitně připojen k názvu bitové kopie. Pokud tento parametr nezadáte, název přihlašovacího serveru registru se automaticky přidá do názvu image.

Výstup příkazu zobrazuje průběh sestavení a vložení image. 

Po úspěšném vytvoření image ji můžete spustit s Docker, pokud ji máte nainstalovanou. Nejdřív se přihlaste ke svému registru:

```azurecli
az acr login --name myregistry
```

Spusťte bitovou kopii:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Pokud `localhost:1337` si chcete prohlédnout ukázkovou webovou aplikaci, přejděte do oblíbeného prohlížeče. Stisknutím klávesy `[Ctrl]+[C]` zastavíte kontejner.

## <a name="example-build-java-image-with-heroku-builder"></a>Příklad: sestavení image Java pomocí Tvůrce Heroku

Následující příklad vytvoří image kontejneru z aplikace Java v úložišti [buildpack/Sample-Java-App](https://github.com/buildpack/sample-java-app) pomocí `heroku/buildpacks:18` Tvůrce. `--pull`Parametr určuje, že příkaz by měl načíst nejnovější image tvůrce. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Tento příklad vytvoří `java-app` obrázek označený s ID spuštění příkazu a vloží ho do registru kontejneru *myregistry* .

Výstup příkazu zobrazuje průběh sestavení a vložení image. 

Po úspěšném vytvoření image ji můžete spustit s Docker, pokud ji máte nainstalovanou. Nejdřív se přihlaste ke svému registru:

```azurecli
az acr login --name myregistry
```

Spusťte image a nahraďte značku image pro *RunId*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Pokud `localhost:8080` si chcete prohlédnout ukázkovou webovou aplikaci, přejděte do oblíbeného prohlížeče. Stisknutím klávesy `[Ctrl]+[C]` zastavíte kontejner.


## <a name="next-steps"></a>Další kroky

Po sestavení a vložení image kontejneru s nástrojem je `az acr pack build` můžete nasadit jako libovolný obrázek do cíle podle vašeho výběru. Možnosti nasazení do Azure zahrnují spouštění IT ve službě [App Service](../app-service/tutorial-custom-container.md) nebo [Azure Kubernetes](../aks/tutorial-kubernetes-deploy-cluster.md), a to mimo jiné.

Další informace o funkcích úloh ACR najdete v tématu [Automatizace sestavení a údržby imagí kontejneru pomocí úloh ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

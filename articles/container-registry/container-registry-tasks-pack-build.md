---
title: Vytváření image pomocí cloudového nativního buildpacku
description: Pomocí příkazu sestavení az acr pack vytvořte image kontejneru z aplikace a přemistíme se do registru kontejnerů Azure bez použití souboru Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087076"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Vytvoření a nabízení bitové kopie z aplikace pomocí cloudového nativního buildpacku

Příkaz Azure CLI `az acr pack build` [`pack`](https://github.com/buildpack/pack) používá nástroj příkazového příkazu příkazového příkazu z [Buildpacks](https://buildpacks.io/)k vytvoření aplikace a nabízení její image do registru kontejnerů Azure. Tato funkce poskytuje možnost rychle vytvořit image kontejneru ze zdrojového kódu aplikace v Node.js, Java a dalších jazycích bez nutnosti definovat dockerfile.

Můžete použít Azure Cloud Shell nebo místní instalace Azure CLI ke spuštění příklady v tomto článku. Pokud jej chcete používat místně, je vyžadována verze 2.0.70 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="use-the-build-command"></a>Použití příkazu sestavení

Chcete-li vytvořit a push image kontejneru pomocí Cloud Native Buildpacks, spusťte příkaz [az acr pack build.][az-acr-pack-build] Vzhledem k tomu, [az acr sestavení sestavení][az-acr-build] a odešle image `az acr pack build` ze zdroje Dockerfile a související kód, s zadat zdroj aplikace stromu přímo.

Při spuštění `az acr pack build`zadejte minimálně následující :

* Registr kontejnerů Azure, ve kterém spustíte příkaz
* Název obrázku a značka výsledného obrázku
* Jedno z [podporovaných umístění kontextu](container-registry-tasks-overview.md#context-locations) pro úlohy ACR, například místní adresář, úložiště GitHub nebo vzdálený tarball
* Název buildpack builder image vhodné pro vaši aplikaci. Azure Container Registry mezipaměti `cloudfoundry/cnb:0.0.34-cflinuxfs3` image tvůrce, jako je například pro rychlejší sestavení.  

`az acr pack build`podporuje další funkce příkazů ACR Tasks, včetně [spouštění proměnných](container-registry-tasks-reference-yaml.md#run-variables) a [protokolů spuštění úloh,](container-registry-tasks-logs.md) které jsou přenášeny datovým proudem a také uloženy pro pozdější načítání.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Příklad: Build Node.js image s Cloud Foundry builder

Následující příklad vytvoří image kontejneru z aplikace Node.js v [azure-samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) repo pomocí `cloudfoundry/cnb:0.0.34-cflinuxfs3` tvůrce. Tento tvůrce je uložen do mezipaměti `--pull` podle Azure Container Registry, takže parametr není vyžadován:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Tento příklad vytvoří `node-app` bitovou `1.0` kopii se značkou a odešle ji do registru *kontejneru myregistry.* V tomto příkladu je cílový název registru explicitně předřazený k názvu bitové kopie. Pokud není zadán, název přihlašovacího serveru registru je automaticky předřazený k názvu bitové kopie.

Výstup příkazu zobrazuje průběh vytváření a posouvání obrazu. 

Po úspěšném sestrojit image, můžete spustit s Docker, pokud máte nainstalován. První přihlášení do registru:

```azurecli
az acr login --name myregistry
```

Spusťte obrázek:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Přejděte `localhost:1337` do svého oblíbeného prohlížeče a prohlédněte si ukázkovou webovou aplikaci. Stisknutím `[Ctrl]+[C]` zastavíte nádobu.

## <a name="example-build-java-image-with-heroku-builder"></a>Příklad: Vytvoření obrazu Javy s tvůrcem Heroku

Následující příklad vytvoří image kontejneru z aplikace Java v repo [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) pomocí `heroku/buildpacks:18` tvůrce. Parametr `--pull` určuje, že příkaz by měl vytáhnout nejnovější image tvůrce. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Tento příklad vytvoří `java-app` bitovou kopii označenou ID spuštění příkazu a odešle ji do registru *kontejneru myregistry.*

Výstup příkazu zobrazuje průběh vytváření a posouvání obrazu. 

Po úspěšném sestrojit image, můžete spustit s Docker, pokud máte nainstalován. První přihlášení do registru:

```azurecli
az acr login --name myregistry
```

Spusťte obrázek a nakontou značku obrázku *runidem*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Přejděte `localhost:8080` do svého oblíbeného prohlížeče a prohlédněte si ukázkovou webovou aplikaci. Stisknutím `[Ctrl]+[C]` zastavíte nádobu.


## <a name="next-steps"></a>Další kroky

Po sestavení a nabízení `az acr pack build`image kontejneru s , můžete nasadit jako každá image na cíl podle vašeho výběru. Možnosti nasazení Azure zahrnují spuštění ve [službě App Service](../app-service/containers/tutorial-custom-docker-image.md) nebo Azure [Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md), mimo jiné.

Další informace o funkcích úloh ACR naleznete v [tématu Automatizace sestavení image kontejneru a údržby pomocí úloh ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

---
title: Úloha s více kroky pro sestavení, test & opravu obrázku
description: Seznámení s více kroky – funkce ACR úloh v Azure Container Registry, které poskytují pracovní postupy založené na úlohách pro vytváření, testování a opravy imagí kontejnerů v cloudu.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "78399696"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Spouštění úloh s více kroky sestavení, testování a oprav v úlohách ACR

Úlohy s více kroky zvyšují možnosti vytváření a nabízených imagí pro ACR úlohy s využitím více kroků a pracovních postupů využívajících více kontejnerů. Použijte úlohy s více kroky k vytvoření a vložení několika imagí, v řadě nebo paralelně. Pak tyto image spusťte jako příkazy v rámci jednoho spuštění úlohy. Každý krok definuje sestavení image kontejneru nebo operaci Push a může také definovat spuštění kontejneru. Každý krok v úloze s více kroky používá jako své spouštěcí prostředí kontejner.

> [!IMPORTANT]
> Pokud jste už dříve vytvořili úlohy ve verzi Preview pomocí příkazu `az acr build-task`, tyto úlohy bude potřeba vytvořit znovu pomocí příkazu [az acr task][az-acr-task].

Například můžete spustit úlohu s kroky, které automatizují následující logiku:

1. Sestavení image webové aplikace
1. Spuštění kontejneru webové aplikace
1. Sestavení image testu webové aplikace
1. Spusťte test kontejneru webové aplikace, který provádí testy proti běžícímu kontejneru aplikace.
1. Pokud testy projde, sestavte balíček pro archivaci grafu Helm.
1. Provedení `helm upgrade` použití nového balíčku pro archivaci grafu Helm

Všechny kroky se provádějí v rámci Azure, přesměrování práce do výpočetních prostředků Azure a jejich uvolnění ze správy infrastruktury. Kromě služby Azure Container Registry platíte jenom za prostředky, které využijete. Informace o cenách najdete v části **sestavení kontejneru** v tématu [Azure Container Registry ceny][pricing].


## <a name="common-task-scenarios"></a>Běžné scénáře úloh

Úlohy s více kroky umožňují scénáře podobně jako v následující logice:

* Sestavujte, označte a nahrajte jednu nebo více imagí kontejneru, v řadě nebo paralelně.
* Spusťte a zachyťte výsledky testování částí a pokrytí kódu.
* Spusťte a zachyťte funkční testy. ACR úlohy podporují spouštění více než jednoho kontejneru a provádění řady požadavků mezi nimi.
* Provede provádění na základě úkolů, včetně kroků před nebo po sestavení image kontejneru.
* Nasaďte jeden nebo více kontejnerů s oblíbeným modulem nasazení do cílového prostředí.

## <a name="multi-step-task-definition"></a>Definice úlohy s více kroky

Úloha s více kroky v úlohách ACR je definována jako série kroků v souboru YAML. Každý krok může určovat závislosti po úspěšném dokončení jednoho nebo více předchozích kroků. K dispozici jsou následující typy kroků úlohy:

* [`build`](container-registry-tasks-reference-yaml.md#build): Sestavte jednu nebo více imagí kontejneru pomocí známé `docker build` syntaxe, v řadě nebo paralelně.
* [`push`](container-registry-tasks-reference-yaml.md#push): Push images do registru kontejneru. Soukromé Registry, jako je Azure Container Registry, jsou podporovány jako veřejné Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Spusťte kontejner, aby mohl fungovat jako funkce v kontextu běžící úlohy. Můžete předat parametry kontejneru `[ENTRYPOINT]` a zadat vlastnosti, jako jsou ENV, detach a další známé `docker run` parametry. `cmd`Typ kroku povoluje jednotky a funkční testování se souběžným spouštěním kontejneru.

Následující fragmenty kódu ukazují, jak kombinovat tyto typy kroků úloh. Úlohy s více kroky můžou být jednoduché, protože sestavování jedné image z souboru Dockerfile a jejich vkládání do registru se souborem YAML podobným:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Nebo složitější, jako je například tato fiktivní definice s více kroky zahrnující kroky pro sestavování, testování, Helm a Helm nasazení (registr kontejnerů a konfigurace úložiště Helm se nezobrazuje):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

V tématu [Příklady úloh](container-registry-tasks-samples.md) pro více kroků YAML soubory úloh a fázemi pro několik scénářů.

## <a name="run-a-sample-task"></a>Spuštění ukázkového úkolu

Úlohy podporují ruční spuštění, nazývané "rychlý běh" a automatizované spouštění při potvrzení Git nebo aktualizaci základní image.

Pokud chcete spustit úlohu, nejdřív definujte kroky úkolu v souboru YAML a pak spusťte příkaz Azure CLI [AZ ACR Run][az-acr-run].

Tady je příklad příkazu rozhraní příkazového řádku Azure CLI, který spouští úlohu pomocí ukázkového souboru YAML úlohy. Postup sestaví a potom nahraje obrázek. `\<acrName\>`Před spuštěním příkazu aktualizujte s názvem vlastního služby Azure Container Registry.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Při spuštění úlohy by měl výstup zobrazit průběh každého kroku definovaného v souboru YAML. V následujícím výstupu se kroky zobrazí jako `acb_step_0` a `acb_step_1` .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Další informace o automatizovaných sestavách na potvrzení Git nebo aktualizaci základní image najdete v článcích kurzu [Automatizace sestavení imagí](container-registry-tutorial-build-task.md) a [základní buildy aktualizace imagí](container-registry-tutorial-base-image-update.md) .

## <a name="next-steps"></a>Další kroky

Odkazy na úlohy a příklady pro více kroků najdete tady:

* [Odkaz na úkol](container-registry-tasks-reference-yaml.md) – typy kroků úlohy, jejich vlastnosti a využití.
* [Příklady úloh](container-registry-tasks-samples.md) – příklad `task.yaml` a soubory Docker pro několik scénářů, které jsou jednoduché a složité.
* [Úložiště cmd](https://github.com/AzureCR/cmd) – kolekce kontejnerů jako příkazy pro úlohy ACR

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task
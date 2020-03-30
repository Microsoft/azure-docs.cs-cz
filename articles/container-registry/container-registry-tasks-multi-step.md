---
title: Vícekrokový úkol sestavit, otestovat & opravu bitové kopie
description: Úvod k vícekrokovým úlohám, což je funkce úloh ACR v registru kontejnerů Azure, která poskytuje pracovní postupy založené na úlohách pro vytváření, testování a opravy ibi kopií kontejnerů v cloudu.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399696"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Spuštění úloh vícekrokového sestavení, testování a opravy v úlohách ACR

Vícekrokové úlohy rozšiřují možnosti sestavení a nabízení jednotlivých bitových kopie úloh ACR pomocí vícekrokových pracovních postupů založených na více kontejnerech. Pomocí vícekrokových úloh můžete vytvářet a tlačit několik bitových kopií, a to sériově nebo paralelně. Pak spusťte tyto obrázky jako příkazy v rámci jednoho spuštění úlohy. Každý krok definuje sestavení image kontejneru nebo operace nabízení a můžete také definovat provádění kontejneru. Každý krok v úlohě více kroků používá kontejner jako jeho prostředí spuštění.

> [!IMPORTANT]
> Pokud jste už dříve vytvořili úlohy ve verzi Preview pomocí příkazu `az acr build-task`, tyto úlohy bude potřeba vytvořit znovu pomocí příkazu [az acr task][az-acr-task].

Můžete například spustit úlohu s kroky, které automatizují následující logiku:

1. Vytvoření bitové kopie webové aplikace
1. Spuštění kontejneru webové aplikace
1. Vytvoření testovací bitové kopie webové aplikace
1. Spuštění testovacího kontejneru webové aplikace, který provádí testy proti spuštěnému kontejneru aplikace
1. Pokud testy projdou, vytvořte archivní balíček grafu Helm
1. Provedení `helm upgrade` nového balíčku archivu grafu Helm

Všechny kroky se provádějí v rámci Azure, převedením práce na výpočetní prostředky Azure a uvolnění mne od správy infrastruktury. Kromě registru kontejnerů Azure platíte jenom za prostředky, které používáte. Informace o cenách najdete v části **Kontejner ové sestavení** v [cenách registru kontejnerů Azure][pricing].


## <a name="common-task-scenarios"></a>Běžné scénáře úkolů

Vícekrokové úlohy umožňují scénáře, jako je následující logika:

* Vytvářejte, tagujte a vtlačujte jednu nebo více iobrazek kontejnerů, a to sériově nebo paralelně.
* Spuštění a zachycení testování částí a výsledky pokrytí kódu.
* Spusťte a zachyťte funkční testy. Úlohy ACR podporuje spuštění více než jeden kontejner, provádění řady požadavků mezi nimi.
* Proveďte provádění založené na úlohách, včetně kroků před/zaúčtovánísestavení image kontejneru.
* Nasaďte jeden nebo více kontejnerů s oblíbeným nasazením motoru do cílového prostředí.

## <a name="multi-step-task-definition"></a>Definice vícekrokových úkolů

Vícekrokový úkol v úlohách ACR je definován jako řada kroků v souboru YAML. Každý krok můžete určit závislosti na úspěšné dokončení jednoho nebo více předchozích kroků. K dispozici jsou následující typy kroků úkolu:

* [`build`](container-registry-tasks-reference-yaml.md#build): Vytvořte jeden nebo `docker build` více iobrazů kontejneru pomocí známé syntaxe, v řadě nebo paralelně.
* [`push`](container-registry-tasks-reference-yaml.md#push): Zasuňte vytvořené bitové kopie do registru kontejnerů. Soukromé registry, jako je Azure Container Registry, jsou podporované, stejně jako veřejné Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Spusťte kontejner tak, aby jej můžete pracovat jako funkce v kontextu spuštěné úlohy. Můžete předat parametry kontejneru `[ENTRYPOINT]`a určit vlastnosti, jako je env, `docker run` odpojit a další známé parametry. Typ `cmd` kroku umožňuje testování jednotky a funkce s souběžným spuštěním kontejneru.

Následující úryvky ukazují, jak kombinovat tyto typy kroků úkolu. Vícekrokové úlohy mohou být stejně jednoduché jako vytvoření jedné bitové kopie z dockerového souboru a odeslání do registru pomocí souboru YAML podobného:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Nebo složitější, jako je například tato fiktivní vícekroková definice, která obsahuje kroky pro sestavení, testování, balíček helmu a nasazení kormidla (není zobrazena konfigurace registru kontejneru a úložiště helmu):

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

Podívejte se na [příklady úloh](container-registry-tasks-samples.md) pro vícekrokové úlohy souborů YAML a Dockerfiles pro několik scénářů.

## <a name="run-a-sample-task"></a>Spuštění ukázkové úlohy

Úlohy podporují jak ruční provádění, nazývané "rychlé spuštění", tak automatické spuštění při potvrzení Gitu nebo aktualizaci základní bitové kopie.

Chcete-li spustit úlohu, nejprve definujte kroky úlohy v souboru YAML a potom spusťte příkaz Azure CLI [az az run][az-acr-run].

Tady je příklad příkazu Azure CLI, který spouští úlohu pomocí ukázkové úlohy souboru YAML. Jeho kroky se staví a pak tlačí obrázek. Před `\<acrName\>` spuštěním příkazu aktualizujte s názvem vlastního registru kontejnerů Azure.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Při spuštění úlohy by měl výstup zobrazit průběh každého kroku definovaného v souboru YAML. V následujícím výstupu se kroky `acb_step_0` `acb_step_1`zobrazí jako a .

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

Další informace o automatizovaných sestaveních na potvrzení Gitu nebo aktualizaci základní bitové kopie najdete v tématu [Automatizujte sestavení bitové kopie](container-registry-tutorial-build-task.md) a články s [kurzovými informacemi sestavení základní aktualizace bitové](container-registry-tutorial-base-image-update.md) kopie.

## <a name="next-steps"></a>Další kroky

Vícekrokový odkaz na úkoly a příklady naleznete zde:

* [Odkaz na úkol](container-registry-tasks-reference-yaml.md) – typy kroků úkolu, jejich vlastnosti a použití.
* [Příklady úloh](container-registry-tasks-samples.md) `task.yaml` – příklad a soubory Dockeru pro několik scénářů, jednoduché až složité.
* [Cmd repo](https://github.com/AzureCR/cmd) - kolekce kontejnerů jako příkazy pro úlohy ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task
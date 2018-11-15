---
title: Automatizace sestavování image, testování a opravy s využitím Azure Container Registry vícekrokových úkolů
description: Úvod do vícekrokových úkolů funkce ACR úloh ve službě Azure Container Registry poskytující založené na úlohách pracovní postupy pro sestavování, testování a opravy chyb imagí kontejnerů v cloudu.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 10/29/2018
ms.author: danlep
ms.openlocfilehash: 4492e05339c72c371eb2c935d0397b469440c4f6
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632688"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Spuštění několika kroky sestavení, testování a opravu úkoly v úlohách služby ACR

Vícekrokové úlohy rozšířit možnosti sestavení a nabízená jedné image ACR úkolů s více kroky, založené na více container pracovních postupů. Pomocí vícekrokových úkolů sestavíte a odešlete několik imagí v řadě nebo paralelně a spuštění příkazů v rámci jednoho úkolu spuštění těchto imagí. Každý krok definuje image kontejneru sestavení nebo vynucená operace a můžete také definujte spuštění kontejneru. Každý krok v rámci vícekrokové úlohy používá jako své prostředí pro spouštění kontejneru.

> [!IMPORTANT]
> Pokud jste už dříve vytvořili úlohy ve verzi Preview pomocí příkazu `az acr build-task`, tyto úlohy bude potřeba vytvořit znovu pomocí příkazu [az acr task][az-acr-task].

Můžete například spustit úlohu s kroky, které automatizují následující:

1. Sestavíte image webové aplikace
1. Spuštění kontejneru webové aplikace
1. Sestavíte image test webové aplikace
1. Spuštění kontejneru test webové aplikace, který provádí testy pro spuštěné aplikace kontejneru
1. Pokud jsou testy úspěšné, vytvořit balíček archivu grafu helmu
1. Provést `helm upgrade` pomocí nového balíčku archivu grafu helmu

Všechny kroky se provádějí v rámci Azure, snižování zátěže práce, kterou výpočetní prostředky Azure a tím vás zbaví správu infrastruktury. Kromě vašeho registru kontejneru Azure platíte jenom za ty prostředky, které používáte. Informace o cenách najdete v tématu **sestavení kontejneru** tématu [ceny Azure Container Registry][pricing].

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="common-task-scenarios"></a>Běžné scénáře úloh

Vícekrokové úlohy umožňují scénáře, jako jsou následující:

* Příznak, sestavení a vložit jednu nebo víc imagí kontejnerů v řadě nebo paralelně.
* Spuštění a zaznamenejte výsledky pokrytí testu a kódu jednotek.
* Spuštění a zaznamenejte funkčních testů. Úlohy služby ACR podporuje spuštění více kontejnerů, provádění se série požadavků mezi nimi.
* Proveďte spuštění založené na úlohách, včetně předzálohovacího nebo pozálohovacího kroků sestavení image kontejneru.
* Nasadíte jeden nebo více kontejnerů s modulem vaše oblíbené nasazení na cílovém prostředí.

## <a name="multi-step-task-definition"></a>Definice vícekrokové úlohy

Vícekrokové úlohy v úlohách služby ACR je definován jako série kroků v rámci souboru YAML. Jednotlivé kroky můžete určit závislosti na úspěšném dokončení jedné nebo více předchozích kroků. K dispozici jsou následující typy úloh kroku:

* [`build`](container-registry-tasks-reference-yaml.md#build): Sestavení využívá známou jednu nebo víc imagí kontejneru `docker build` syntaxe v řadě nebo paralelně.
* [`push`](container-registry-tasks-reference-yaml.md#push): Nabízených oznámení vytvořená Image do registru kontejneru. Podporují se privátních registrů, jako je Azure Container Registry, jako je veřejného Docker Hubu.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Spuštění kontejneru, tak, že bude správně fungovat jako funkce v rámci kontextu spuštěná úloha. Můžete předat parametry do tohoto kontejneru `[ENTRYPOINT]`a zadejte vlastnosti, jako je env, odpojení a další známé `docker run` parametry. `cmd` Typ kroku umožňuje jednotek a funkční testování s spuštění souběžných kontejneru.

Následující fragmenty kódu ukazují, jak kombinací těchto typů úkolů krok. Vícekrokové úlohy může být stejně jednoduché jako vytváření jedné image ze souboru Dockerfile a nahráním do vašeho registru, pomocí souboru YAML podobně jako:

```yaml
version: 1.0-preview-1
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Nebo složitější, jako je například tuto fiktivní vícekrokového definice, která zahrnuje kroky pro sestavení, testování, balíček helm a helm nasazení (registr kontejnerů a Helm konfigurace úložiště není vidět):

```yaml
version: 1.0-preview-1
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

Zobrazit [úkolů příklady] [ task-examples] pro dokončení vícekrokových úkolů YAML soubory a soubory Dockerfile pro několik scénářů.

## <a name="run-a-sample-task"></a>Spuštění ukázkové úlohy

Úlohy podporují oba ruční spuštění nazývá "rychlého spuštění," a aktualizovat automatické spuštění na Git commit nebo základní image.

Spustit úlohu, nejprve definovat kroky úkolu v souboru YAML, pak spustíte příkaz rozhraní příkazového řádku Azure [az acr spustit][az-acr-run].

Tady je příklad rozhraní příkazového řádku Azure, který spustí úlohu pomocí souboru YAML ukázkový úkol. Z kroků sestavení a potom nasdílejte image. Aktualizace `\<acrName\>` s názvem ve vlastní službě Azure container registry před spuštěním příkazu.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Při spuštění úlohy, výstup by měl zobrazit průběh jednotlivých kroků definované v souboru YAML. V následujícím výstupu zobrazí kroky jako `acb_step_0` a `acb_step_1`.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
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

Další informace o automatizované buildy při aktualizaci Git commit nebo základní image, najdete v článku [. automatizace sestavování imagí](container-registry-tutorial-build-task.md) a [základní image aktualizace sestavení](container-registry-tutorial-base-image-update.md) kurzech.

## <a name="preview-feedback"></a>Zpětnou vazbu ve verzi Preview

I když jsou funkce vícekrokových úkolů ACR úloh ve verzi preview, Zveme vás k poskytnutí zpětné vazby. Několik kanálech zpětné vazby jsou k dispozici:

* [Problémy s](https://aka.ms/acr/issues) – zobrazení existujících chyb a problémů a protokolovat nové značky
* [UserVoice](https://aka.ms/acr/uservoice) -hlasovat pro existující funkce požadavky nebo vytvořte nové požadavky
* [Popisují](https://aka.ms/acr/feedback) -zapojit do diskusí Azure Container Registry s komunitou Stack Overflow

## <a name="next-steps"></a>Další postup

Referenční dokumentace úlohy několika kroky a příklady v tomto článku najdete:

* [Úloha odkazu](container-registry-tasks-reference-yaml.md) -úloh typy krok, jejich vlastnosti a využití.
* [Příklady úloh] [ task-examples] – příklad `task.yaml` soubory pro několik scénářů, jednoduché i složité.
* [Úložiště cmd](https://github.com/AzureCR/cmd) – kolekce kontejnerů jako příkazy pro úlohy služby ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-acr-task]: /cli/azure/acr#az-acr-task
---
title: Ukázky úloh ACR
description: Ukázka úloh registru kontejnerů Azure (úlohy ACR) pro vytváření, spouštění a opravování ibi kontejnerů
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456085"
---
# <a name="acr-tasks-samples"></a>Ukázky úkolů ACR

Tento článek odkazuje `task.yaml` na ukázkové soubory a přidružené dockerfiles pro několik úloh [registru kontejnerů Azure](container-registry-tasks-overview.md) (ACR úkoly) scénáře. 

Další příklady najdete v tématu Azure [ukázky][task-examples] repo.

## <a name="scenarios"></a>Scénáře

* **Vytvořit image** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Spustit kontejner** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Sestavení a nabízení bitové kopie** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Sestavení a spuštění bitové kopie** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Sestavení a nabízení více bitových kopií** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Vytváření a testování bitových kopií v paralelním** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Vytváření a nabízení bitových kopií do více registrů** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Další kroky

Další informace o úkolech ACR:

* [Vícekrokové úlohy](container-registry-tasks-multi-step.md) – pracovní postupy založené na úlohách ACR pro vytváření, testování a opravy iobrazek kontejnerů v cloudu.
* [Odkaz na úkol](container-registry-tasks-reference-yaml.md) – typy kroků úkolu, jejich vlastnosti a použití.
* [Cmd repo](https://github.com/AzureCR/cmd) - kolekce kontejnerů jako příkazy pro úlohy ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

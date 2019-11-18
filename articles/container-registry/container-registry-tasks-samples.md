---
title: Ukázky úloh Azure Container Registry
description: Ukázkové úlohy Azure Container Registry (úlohy ACR) k sestavování, spouštění a opravování imagí kontejneru
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/14/2019
ms.author: danlep
ms.openlocfilehash: 488e13ed0d1961fbafad545057accb61957a7005
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152850"
---
# <a name="acr-tasks-samples"></a>Ukázky úloh ACR

Tento článek obsahuje odkazy na ukázkové `task.yaml` soubory a související fázemi pro několik scénářů [Azure Container Registry úloh](container-registry-tasks-overview.md) (úlohy ACR). 

Další příklady najdete v úložišti [ukázek Azure][task-examples] .

## <a name="scenarios"></a>Scénáře

* **Sestavení image** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Spustit kontejner** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Sestavování a vkládání imagí** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Sestavování a spouštění imagí** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Sestavení a vložení více imagí** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Sestavení a testování imagí v paralelní** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Sestavování a vkládání imagí do více registrů** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Další kroky

Další informace o úlohách ACR:

* [Úlohy s více kroky](container-registry-tasks-multi-step.md) – ACR pracovní postupy založené na úlohách pro vytváření, testování a opravy imagí kontejnerů v cloudu.
* [Odkaz na úkol](container-registry-tasks-reference-yaml.md) – typy kroků úlohy, jejich vlastnosti a využití.
* [Úložiště cmd](https://github.com/AzureCR/cmd) – kolekce kontejnerů jako příkazy pro úlohy ACR


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

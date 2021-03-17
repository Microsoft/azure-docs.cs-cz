---
title: Ukázky úloh ACR
description: Ukázkové úlohy Azure Container Registry (úlohy ACR) k sestavování, spouštění a opravování imagí kontejneru
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74456085"
---
# <a name="acr-tasks-samples"></a>Ukázky úloh ACR

Tento článek obsahuje odkazy na ukázkové `task.yaml` soubory a související fázemi pro několik scénářů [úloh Azure Container Registry](container-registry-tasks-overview.md) (úlohy ACR). 

Další příklady najdete v úložišti [ukázek Azure][task-examples] .

## <a name="scenarios"></a>Scénáře

* **Obrázek sestavení**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Spustit kontejner**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Sestavit a vložit obrázek**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* Obrázek sestavení a **spuštění**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Sestavení a nabízení více imagí**  -   [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* Paralelní sestavení a **testování imagí**  -   [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Sestavování a vkládání imagí do více registrů**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [souboru Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Další kroky

Další informace o úlohách ACR:

* [Úlohy s více kroky](container-registry-tasks-multi-step.md) – ACR pracovní postupy založené na úlohách pro vytváření, testování a opravy imagí kontejnerů v cloudu.
* [Odkaz na úkol](container-registry-tasks-reference-yaml.md) – typy kroků úlohy, jejich vlastnosti a využití.
* [Úložiště cmd](https://github.com/AzureCR/cmd) – kolekce kontejnerů jako příkazy pro úlohy ACR


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

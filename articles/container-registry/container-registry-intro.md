---
title: Registry kontejnerů pro Private Docker v Azure – přehled
description: Úvod do služby Azure Container Registry poskytující cloudové, spravované, privátní registry Dockeru.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b8b4b5fc3ec15d921ff5580aff4d0202be1d38b9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797901"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Seznámení se soukromými registry kontejnerů Dockeru v Azure

Služba Azure Container Registry je spravované, privátní služby registru Dockeru podle open source nástroji Docker Registry 2.0. Vytvářet a spravovat registry kontejnerů Azure k ukládání a správě privátních imagí kontejnerů Dockeru.

Azure container registry pomocí vaší existující kontejner kanály vývoje a nasazení nebo sestavení imagí kontejnerů v Azure pomocí Azure Container Registry úlohy. Sestavení na vyžádání, nebo plně automatizovat sestavení s aktivační události, jako je zdrojový kód potvrdí a základní aktualizací bitové kopie.

Další informace o Dockeru a registru koncepty, najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/) a [o registrů, úložišť a imagí](container-registry-concepts.md).

## <a name="use-cases"></a>Případy použití

Stažení imagí z registru kontejnerů Azure na různé cíle nasazení:

* **Škálovatelné systémy orchestrace**, které spravují kontejnerizované aplikace napříč clustery hostitelů, včetně [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) a [Dockeru Swarm](https://docs.docker.com/swarm/).
* **Služby Azure**, které podporují vytváření a spouštění škálovaných aplikací, včetně [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) a dalších.

Vývojáři mohou nahrávat do registru kontejnerů také jako součást pracovního postupu vývoje kontejneru. Například určit registr kontejnerů z průběžné integrace a doručování nástroje, jako cíl [kanály Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) nebo [Jenkins](https://jenkins.io/).

Konfigurace úloh ACR automaticky znovu sestavit Image aplikací při jejich základní bitové kopie jsou aktualizovány nebo pokud váš tým potvrzení změn kódu do úložiště Git. automatizace sestavování imagí. Vytváření vícekrokových úkolů k automatizaci sestavování, testování a opravy chyb několik imagí kontejneru paralelní v cloudu.

Azure poskytuje nástroje, včetně rozhraní příkazového řádku Azure, webu Azure portal a podpora rozhraní API pro správu vašich registry kontejnerů Azure. Volitelně můžete nainstalovat [rozšíření Dockeru pro Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) a [účet Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) rozšíření pro práci s vaší registry kontejnerů Azure. O přijetí změn a nahrávání imagí do služby Azure container registry nebo spuštění úlohy služby ACR, vše v rámci Visual Studio Code.

## <a name="key-features"></a>Klíčové funkce

* **Skladové položky registru** – vytvořte jeden nebo více registrů kontejnerů ve vašem předplatném Azure. Registry jsou dostupné ve třech skladových položkách: [Basic, Standard a Premium](container-registry-skus.md), z nichž každý podporuje integraci webhooků, ověřování registru pomocí Azure Active Directory a funkci odstranit. Využijte místní úložiště imagí kontejnerů v síťové blízkosti vytvořením registru ve stejném umístění Azure, jako jsou vaše nasazení. Funkci [geografické replikace](container-registry-geo-replication.md) v registrech úrovně Premium můžete využít ve scénářích pokročilé replikace a distribuce imagí kontejnerů. 

  Můžete [řízení přístupu](container-registry-authentication.md) do registru kontejnerů pomocí Azure identity, Azure Active Directory zajišťuje [instanční objekt služby](../active-directory/develop/app-objects-and-service-principals.md), nebo poskytnutého účtu správce. Přihlášení k registru pomocí Azure CLI nebo standardní `docker login` příkazu.

* **Podporované obrázky a artefakty** -seskupené v úložišti, každé image je jen pro čtení snímek kontejneru kompatibilní s Dockerem. Registry kontejnerů Azure mohou zahrnovat image systémů Windows i Linux. Názvy imagí pro všechna nasazení kontejnerů určujete vy. Pomocí standardních [příkazů Dockeru](https://docs.docker.com/engine/reference/commandline/) můžete nahrávat image do úložiště nebo si z úložiště image stáhnout. Kromě imagí kontejnerů Dockeru, registr kontejnerů Azure uchovává [týkající se obsahu formáty](container-registry-image-formats.md) jako [grafy Helm](container-registry-helm-repos.md) a obrázky, které jsou vytvořeny tak, aby [iniciativy otevřete kontejner (OCI) Image Specifikace formátu](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Úkoly v Azure Container Registry** – použití [Azure Container Registry úlohy](container-registry-tasks-overview.md) (ACR úkoly) zjednodušuje vytváření, testování, doručením (push) a nasazování imagí v Azure. Například použití úkolů ACR rozšířit vaše vývoje vnitřní smyčky do cloudu přesměrováním `docker build` operace do Azure. Můžete nakonfigurovat úlohy sestavení tak, aby se kanál oprav operačního systému a architektury kontejneru automatizoval a aby se image po potvrzení kódu vaším týmem ve správě zdrojového kódu automaticky sestavovaly.

  [Vícekrokové úlohy](container-registry-tasks-overview.md#multi-step-tasks) zadejte definici úlohy založené na krok a provádění pro sestavování, testování a opravy chyb imagí kontejnerů v cloudu. Kroky úlohy definují jednotlivá sestavení image kontejneru a operací nabízených oznámení. Mohou také definovat spuštění jednoho nebo více kontejnerů, u každého kroku pomocí kontejneru jako prostředí pro spuštění.

## <a name="next-steps"></a>Další postup

* [Vytvoření registru kontejnerů pomocí webu Azure Portal](container-registry-get-started-portal.md)
* [Vytvoření registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatizujte sestavení kontejneru a údržba s úlohami služby ACR](container-registry-tasks-overview.md)

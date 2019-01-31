---
title: Registry kontejnerů pro Private Docker v Azure – přehled
description: Úvod do služby Azure Container Registry poskytující cloudové, spravované, privátní registry Dockeru.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 09/25/2018
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 0118c17045b86c88d8d92048787a20bd7d309c0b
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298343"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Seznámení se soukromými registry kontejnerů Dockeru v Azure

Azure Container Registry je spravovaná služba [registrů Dockeru](https://docs.docker.com/registry/) založená na opensourcovém nástroji Docker Registry 2.0. Vytvořte a udržujte registry kontejnerů Azure za účelem ukládání a správy privátních imagí [kontejneru Dockeru](https://www.docker.com/what-docker).

Použijte registry kontejnerů v Azure se svými stávajícími kanály pro vývoj a nasazení kontejnerů. K sestavení imagí kontejnerů v Azure použijte Azure Container Registry Build (ACR Build). Můžete využít sestavení na vyžádání nebo zcela automatická sestavení s potvrzením zdrojového kódu a aktivačními událostmi pro sestavení aktualizací základních imagí.

Související informace o Dockeru a kontejnerech najdete v [přehledu Dockeru](https://docs.docker.com/engine/docker-overview/).

## <a name="use-cases"></a>Případy použití

Stažení imagí z registru kontejnerů Azure na různé cíle nasazení:

* **Škálovatelné systémy orchestrace**, které spravují kontejnerizované aplikace napříč clustery hostitelů, včetně [Kubernetes](http://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) a [Dockeru Swarm](https://docs.docker.com/swarm/).
* **Služby Azure**, které podporují vytváření a spouštění škálovaných aplikací, včetně [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) a dalších.

Vývojáři mohou nahrávat do registru kontejnerů také jako součást pracovního postupu vývoje kontejneru. Mohou například určit registr kontejnerů jako cíl v nástroji pro nasazení a nástroji průběžné integrace, jako je například [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) nebo [Jenkins](https://jenkins.io/).

Nakonfigurujte [úlohy ACR](#azure-container-registry-build) tak, aby se image aplikací po aktualizaci jejich základních imagí automaticky znovu sestavily. Použijte úlohy ACR k automatizaci sestavení imagí po potvrzení kódu vaším týmem v úložišti Git.

## <a name="key-concepts"></a>Klíčové koncepty

* **Registr** – Vytvořte jeden nebo více registrů kontejnerů ve svém předplatném Azure. Registry jsou dostupné ve třech skladových položkách: [Basic, Standard a Premium](container-registry-skus.md), každý z nich podporuje integraci webhooků, ověřování registru pomocí Azure Active Directory a funkci odstraňování. Využijte místní úložiště imagí kontejnerů v síťové blízkosti vytvořením registru ve stejném umístění Azure, jako jsou vaše nasazení. Funkci [geografické replikace](container-registry-geo-replication.md) v registrech úrovně Premium můžete využít ve scénářích pokročilé replikace a distribuce imagí kontejnerů. Plně kvalifikovaný název registru má formát `myregistry.azurecr.io`.

  Můžete [řízení přístupu](container-registry-authentication.md) do registru kontejnerů pomocí Azure identity, Azure Active Directory zajišťuje [instanční objekt služby](../active-directory/develop/app-objects-and-service-principals.md), nebo poskytnutého účtu správce. Přihlášení k registru pomocí rozhraní příkazového řádku Azure nebo standardní `docker login` příkazu.

* **Úložiště** – registr obsahuje jedno nebo více úložišť, které ukládají skupiny imagí kontejnerů. Azure Container Registry podporuje víceúrovňové obory názvů úložiště. S víceúrovňovými obory názvů můžete seskupovat kolekce imagí souvisejících s konkrétní aplikací nebo kolekcí aplikací pro konkrétní vývojové nebo provozní týmy. Příklad:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` představuje image pro celý podnik.
  * `myregistry.azurecr.io/warrantydept/dotnet-build` představuje image sloužící k vytváření aplikací .NET, která je sdílená v rámci záručního oddělení.
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` představuje webovou image seskupenou v zákaznické aplikaci pro odesílání a vlastněnou záručním oddělením.

* **Obrázek** -uložen v úložišti, každé image je jen pro čtení snímek kontejneru kompatibilní s Dockerem. Registry kontejnerů Azure mohou zahrnovat image systémů Windows i Linux. Názvy imagí pro všechna nasazení kontejnerů určujete vy. Pomocí standardních [příkazů Dockeru](https://docs.docker.com/engine/reference/commandline/) můžete nahrávat image do úložiště nebo si z úložiště image stáhnout. Kromě imagí kontejnerů Azure Container Registry ukládá [týkající se obsahu formáty](container-registry-image-formats.md) například [grafy Helm](container-registry-helm-repos.md), která slouží k nasazení aplikací do Kubernetes.

* **Kontejner** – Kontejner definuje softwarovou aplikaci a její závislosti zabalené do kompletního systému souborů, včetně kódu, modulu runtime, systémových nástrojů a knihoven. Spouštějte kontejnery Dockeru na základě imagí systémů Windows nebo Linux, které si stáhnete z registru kontejnerů. Kontejnery běžící na jednom počítači sdílí jádro operačního systému. Kontejnery Dockeru jsou plně přenositelné na všechny hlavní distribuce Linuxu, macOS i Windows.

## <a name="azure-container-registry-tasks"></a>Úlohy Azure Container Registry

[Úlohy Azure Container Registry](container-registry-tasks-overview.md) (úlohy ACR) je sada funkcí ve službě Azure Container Registry, která poskytuje zjednodušená a efektivní sestavení imagí kontejnerů Dockeru v Azure. Úlohy ACR umožňuje pomocí přesměrování operací `docker build` do Azure rozšířit váš vývoj do cloudu. Můžete nakonfigurovat úlohy sestavení tak, aby se kanál oprav operačního systému a architektury kontejneru automatizoval a aby se image po potvrzení kódu vaším týmem ve správě zdrojového kódu automaticky sestavovaly.

[U vícekrokových úloh](container-registry-tasks-overview.md#multi-step-tasks-preview) funkce Preview úloh ACR poskytuje definice úloh na základě kroků a spuštění pro sestavování, testování a opravy chyb imagí kontejnerů v cloudu. Kroky úlohy definují jednotlivá sestavení image kontejneru a operací nabízených oznámení. Mohou také definovat spuštění jednoho nebo více kontejnerů, u každého kroku pomocí kontejneru jako prostředí pro spuštění.

## <a name="next-steps"></a>Další postup

* [Vytvoření registru kontejnerů pomocí webu Azure Portal](container-registry-get-started-portal.md)
* [Vytvoření registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatizace oprav operačního systému a architektury pomocí úloh ACR](container-registry-tasks-overview.md)
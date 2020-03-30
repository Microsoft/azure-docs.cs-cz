---
title: Spravované kontejnerové registry
description: Úvod do služby Azure Container Registry poskytující cloudové, spravované, privátní registry Dockeru.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 1992a2a63d16a955d136459f5dbaece7df815c71
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77132034"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Seznámení se soukromými registry kontejnerů Dockeru v Azure

Azure Container Registry je spravovaná soukromá služba registru Dockeru založená na open source registru Docker 2.0. Vytvořte a spravujte registry kontejnerů Azure pro ukládání a správu privátních ibi kontejnerů Dockeru a souvisejících artefaktů.

Použijte registry kontejnerů Azure s existujícími kanály pro vývoj a nasazení kontejnerů nebo použijte úlohy registru kontejnerů Azure k vytváření ibi kontejnerů v Azure. Nazákladě poptávky nebo plně automatizujte sestavení pomocí aktivačních událostí, jako jsou potvrzení zdrojového kódu a aktualizace základní bitové kopie.

Další informace o konceptech Dockeru a registru najdete v [tématech Přehled Dockeru](https://docs.docker.com/engine/docker-overview/) a [O registrech, repozitářích a irekcích](container-registry-concepts.md).

## <a name="use-cases"></a>Případy použití

Stažení imagí z registru kontejnerů Azure na různé cíle nasazení:

* **Škálovatelné systémy orchestrace**, které spravují kontejnerizované aplikace napříč clustery hostitelů, včetně [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) a [Dockeru Swarm](https://docs.docker.com/swarm/).
* **Služby Azure**, které podporují vytváření a spouštění škálovaných aplikací, včetně [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) a dalších.

Vývojáři mohou nahrávat do registru kontejnerů také jako součást pracovního postupu vývoje kontejneru. Například cílregistru kontejneru z průběžné integrace a doručování nástroj, jako je [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) nebo [Jenkins](https://jenkins.io/).

Nakonfigurujte úlohy ACR tak, aby automaticky znovu obnovovaly image aplikací při aktualizaci jejich základních bitových kopií, nebo automatizujte sestavení bitových kopií, když váš tým potvrdí kód do úložiště Git. Vytvářejte vícekrokové úlohy pro automatizaci vytváření, testování a oprav více iobrazek kontejnerů paralelně v cloudu.

Azure poskytuje nástroje včetně rozhraní Azure Command-Line, portálu Azure a podpory rozhraní API pro správu registrů kontejnerů Azure. Volitelně nainstalujte [rozšíření Docker u Visual Studia code](https://code.visualstudio.com/docs/azure/docker) a rozšíření účtu [Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pro práci s registry kontejnerů Azure. Vyžádat a push image do registru kontejneru Azure nebo spustit úlohy ACR, to vše v rámci kódu Visual Studio.

## <a name="key-features"></a>Klíčové funkce

* **SKU registru** – vytvořte jeden nebo více registrů kontejnerů ve vašem předplatném Azure. Registry jsou k dispozici ve třech sku, které: [Basic, Standard a Premium](container-registry-skus.md), z nichž každá podporuje integraci webhooku, ověřování registru pomocí služby Azure Active Directory a funkci odstranění. Využijte místní úložiště imagí kontejnerů v síťové blízkosti vytvořením registru ve stejném umístění Azure, jako jsou vaše nasazení. Funkci [geografické replikace](container-registry-geo-replication.md) v registrech úrovně Premium můžete využít ve scénářích pokročilé replikace a distribuce imagí kontejnerů. 

* **Zabezpečení a přístup** – přihlášení k registru pomocí azure `docker login` cli nebo standardní příkaz. Azure Container Registry přenáší image kontejnerů přes protokol HTTPS a podporuje protokol TLS k zabezpečení připojení klientů. 

  > [!IMPORTANT]
  > ledna 2020 bude Azure Container Registry vyžadovat všechna zabezpečená připojení ze serverů a aplikací k použití TLS 1.2. Povolte TLS 1.2 pomocí libovolného nedávného klienta dockeru (verze 18.03.0 nebo novější). Podpora tls 1.0 a 1.1 bude vyřazena. 

  [Přístup k](container-registry-authentication.md) registru kontejnerů můžete řídit pomocí identity Azure, [instančního objektu](../active-directory/develop/app-objects-and-service-principals.md)podporovaného službou Azure Active Directory nebo účtu poskytovaného správce. Pomocí řízení přístupu na základě rolí (RBAC) přiřaďte uživatelům nebo systémům jemně odstupňovaná oprávnění k registru.

  Funkce zabezpečení hlavní skladové položky premium zahrnují [důvěryhodnost obsahu](container-registry-content-trust.md) pro podepisování značek bitových označení a brány firewall a virtuální [sítě (náhled)](container-registry-vnet.md) pro omezení přístupu k registru. Azure Security Center volitelně integruje s Azure Container Registry [pro skenování ibi](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) vždy, když image je pushd do registru.

* **Podporované bitové kopie a artefakty** – seskupené v úložišti, každá bitová kopie je snímek kontejneru kompatibilního s Dockerem jen pro čtení. Registry kontejnerů Azure mohou zahrnovat image systémů Windows i Linux. Názvy imagí pro všechna nasazení kontejnerů určujete vy. Pomocí standardních [příkazů Dockeru](https://docs.docker.com/engine/reference/commandline/) můžete nahrávat image do úložiště nebo si z úložiště image stáhnout. Kromě ibi kontejnerů Dockeru ukládá Azure Container Registry [související formáty obsahu,](container-registry-image-formats.md) jako jsou [grafy helmu](container-registry-helm-repos.md) a image vytvořené podle [specifikace formátu image Open Container Initiative (OCI).](https://github.com/opencontainers/image-spec/blob/master/spec.md)

* **Sestavení automatických bitových kopií** – Pomocí [úloh registru kontejnerů Azure](container-registry-tasks-overview.md) (úlohy ACR) můžete zjednodušit vytváření, testování, odesílání a nasazování ibi v Azure. Můžete například použít úlohy ACR k rozšíření vašeho vývoje `docker build` vnitřní smyčky do cloudu převedením operací do Azure. Můžete nakonfigurovat úlohy sestavení tak, aby se kanál oprav operačního systému a architektury kontejneru automatizoval a aby se image po potvrzení kódu vaším týmem ve správě zdrojového kódu automaticky sestavovaly.

  [Vícekrokové úlohy](container-registry-tasks-overview.md#multi-step-tasks) poskytují definici a provádění úloh na základě kroků pro vytváření, testování a opravy iobrazek kontejnerů v cloudu. Kroky úlohy definují jednotlivá sestavení image kontejneru a operací nabízených oznámení. Mohou také definovat spuštění jednoho nebo více kontejnerů, u každého kroku pomocí kontejneru jako prostředí pro spuštění.

## <a name="next-steps"></a>Další kroky

* [Vytvoření registru kontejnerů pomocí webu Azure Portal](container-registry-get-started-portal.md)
* [Vytvoření registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatizace sestavení a údržby kontejnerů pomocí úloh ACR](container-registry-tasks-overview.md)
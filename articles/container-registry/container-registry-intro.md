---
title: Spravované Registry kontejnerů
description: Úvod do služby Azure Container Registry poskytující cloudové, spravované, privátní registry Dockeru.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b5c81d8b2e6d7eac2dcd9070bf1f448340ea1a18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341261"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Seznámení se soukromými registry kontejnerů Dockeru v Azure

Azure Container Registry je spravovaná privátní služba registru Docker založená na Open Source registru Docker 2,0. Vytvářejte a udržujte Registry kontejnerů Azure pro ukládání a správu vašich privátních imagí kontejnerů Docker a souvisejících artefaktů.

Využijte Azure Container Registry se stávajícími kanály pro vývoj a nasazení kontejnerů nebo použijte úlohy Azure Container Registry k vytváření imagí kontejnerů v Azure. Sestavujte na vyžádání nebo plně Automatizujte sestavení s triggery, jako jsou potvrzení zdrojového kódu a aktualizace základního obrázku.

Další informace o konceptech Docker a registru najdete v tématu [Přehled Docker](https://docs.docker.com/engine/docker-overview/) a [o registrech, úložištích a obrázcích](container-registry-concepts.md).

## <a name="use-cases"></a>Případy použití

Stažení imagí z registru kontejnerů Azure na různé cíle nasazení:

* **Škálovatelné systémy orchestrace**, které spravují kontejnerizované aplikace napříč clustery hostitelů, včetně [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) a [Dockeru Swarm](https://docs.docker.com/get-started/swarm-deploy/).
* **Služby Azure**, které podporují vytváření a spouštění škálovaných aplikací, včetně [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml) a dalších.

Vývojáři mohou nahrávat do registru kontejnerů také jako součást pracovního postupu vývoje kontejneru. Můžete například cílit do registru kontejneru z nástroje pro průběžnou integraci a doručování, jako je například [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) nebo [Jenkinse](https://jenkins.io/).

Nakonfigurujte úlohy ACR k automatickému opětovnému sestavení imagí aplikace, když jsou aktualizovány jejich základní obrázky, nebo Automatizujte sestavení imagí, když váš tým potvrdí kód do úložiště Git. Vytvářejte úlohy s více kroky pro automatizaci sestavování, testování a opravování více imagí kontejnerů paralelně v cloudu.

Azure poskytuje nástroje, včetně rozhraní Azure Command-Line, Azure Portal a podpory rozhraní API pro správu registrů kontejnerů Azure. Volitelně můžete nainstalovat [rozšíření Docker pro Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) a rozšíření [účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pro práci se službou Azure Container Registry. Vyhrajte a nahrajte image do služby Azure Container registry nebo spouštějte ACR úlohy, a to všechno v rámci Visual Studio Code.

## <a name="key-features"></a>Klíčové funkce

* **Úrovně služby registru** – ve svém předplatném Azure vytvořte jednu nebo víc registrů kontejnerů. Registry jsou dostupné ve třech úrovních: [Basic, Standard a Premium](container-registry-skus.md), z nichž každý podporuje integraci webhooků, ověřování registru pomocí Azure Active Directory a odstraňování funkcí. Využijte místní úložiště imagí kontejnerů v síťové blízkosti vytvořením registru ve stejném umístění Azure, jako jsou vaše nasazení. Funkci [geografické replikace](container-registry-geo-replication.md) v registrech úrovně Premium můžete využít ve scénářích pokročilé replikace a distribuce imagí kontejnerů. 

* **Zabezpečení a přístup** – Přihlaste se k registru pomocí rozhraní příkazového řádku Azure CLI nebo standardního `docker login` příkazu. Azure Container Registry přenáší image kontejneru přes protokol HTTPS a podporuje TLS k zabezpečení připojení klientů. 

  > [!IMPORTANT]
  > Od 13. ledna 2020 bude Azure Container Registry vyžadovat, aby všechna zabezpečená připojení ze serverů a aplikací používala protokol TLS 1,2. Povolte TLS 1,2 pomocí libovolného nedávného klienta Docker (verze 18.03.0 nebo novější). Bude vyřazena podpora TLS 1,0 a 1,1. 

  Přístup k registru kontejnerů [ovládáte](container-registry-authentication.md) pomocí identity Azure, [instančního objektu](../active-directory/develop/app-objects-and-service-principals.md), který je Azure Active Directory zálohovaný, nebo zadaného účtu správce. Pomocí řízení přístupu na základě role Azure (Azure RBAC) přiřaďte k registru uživatele nebo systémy jemně odstupňovaná oprávnění.

  K funkcím zabezpečení úrovně Premium Service patří [důvěryhodnost obsahu](container-registry-content-trust.md) pro podepisování značek image a [brány firewall a virtuální sítě (Preview)](container-registry-vnet.md) , aby se omezil přístup k registru. Azure Security Center se volitelně integruje s Azure Container Registry pro [skenování imagí](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) vždy, když se do registru vloží obrázek.

* **Podporované image a artefakty** – seskupené do úložiště, každý obrázek je snímkem, který je jen pro čtení kontejneru kompatibilního s Docker. Registry kontejnerů Azure mohou zahrnovat image systémů Windows i Linux. Názvy imagí pro všechna nasazení kontejnerů určujete vy. Pomocí standardních [příkazů Dockeru](https://docs.docker.com/engine/reference/commandline/) můžete nahrávat image do úložiště nebo si z úložiště image stáhnout. Kromě imagí kontejnerů Docker Azure Container Registry ukládá [související formáty obsahu](container-registry-image-formats.md) , jako jsou grafy a obrázky [Helm](container-registry-helm-repos.md) sestavené do [specifikace formátu rozhraní OCI (Open container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Automatizované sestavení imagí** – pomocí [úloh Azure Container Registry](container-registry-tasks-overview.md) (úlohy ACR) můžete zjednodušit sestavování, testování, doručování a nasazování imagí v Azure. ACR úkoly můžete například využít k rozšiřování vnitřních smyček vývoje do cloudu díky přesměrování `docker build` operací na Azure. Můžete nakonfigurovat úlohy sestavení tak, aby se kanál oprav operačního systému a architektury kontejneru automatizoval a aby se image po potvrzení kódu vaším týmem ve správě zdrojového kódu automaticky sestavovaly.

  [Úlohy s více kroky](container-registry-tasks-overview.md#multi-step-tasks) poskytují definice úloh založené na kroku a provádění pro vytváření, testování a opravy imagí kontejnerů v cloudu. Kroky úlohy definují jednotlivá sestavení image kontejneru a operací nabízených oznámení. Mohou také definovat spuštění jednoho nebo více kontejnerů, u každého kroku pomocí kontejneru jako prostředí pro spuštění.

## <a name="next-steps"></a>Další kroky

* [Vytvoření registru kontejnerů pomocí webu Azure Portal](container-registry-get-started-portal.md)
* [Vytvoření registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md)
* [Automatizace sestavení a údržby kontejnerů pomocí úloh ACR](container-registry-tasks-overview.md)
---
title: Nastavení prostředí pro vytváření dev systému Windows pro síť Service Fabric
description: Nastavte své vývojového prostředí ve Windows, abyste mohli vytvořit aplikaci Service Fabric Mesh a nasadit ji do Azure Service Fabric Mesh.
author: dkkapur
ms.author: dekapur
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: a674047722d4deca02d8f4d38a0826e479065037
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259198"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Nastavení vývojového prostředí ve Windows pro sestavování aplikací Service Fabric Mesh

Chcete-li vytvářet a spouštět aplikace Azure Service Fabric Mesh ve vývojovém počítači s Windows, budete potřebovat:

* Docker
* Visual Studio 2017 nebo novější
* Za běhu sítě Service Fabric
* Service Fabric Mesh SDK a nástroje.

A jedna z následujících verzí systému Windows:

* Windows 10 (Enterprise, Professional, nebo Education) verze 1709 (fall Creators update) nebo 1803 (aktualizace Windows 10 duben 2018)
* Windows Server verze 1709
* Windows Server verze 1803

Následující pokyny vám pomohou nainstalovat vše na základě verze systému Windows, kterou používáte.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 nebo novější je nutné nasadit aplikace Service Fabric Mesh. [Nainstalujte verzi 15.6.0][download-visual-studio] nebo vyšší a povolte následující sady funkcí:

* Vývoj pro ASP.NET a web
* Vývoj pro Azure

## <a name="install-docker"></a>Instalace Dockeru

Pokud už máte nainstalovaný Docker, ujistěte se, že máte nejnovější verzi. Docker vás může vyzvat, když je nová verze venku, ale zkontrolujte ručně, abyste se ujistili, že máte nejnovější verzi.

#### <a name="install-docker-on-windows-10"></a>Instalace Dockeru ve Windows 10

Stáhněte a nainstalujte nejnovější verzi [Docker Community Edition pro Windows][download-docker] sloužící k podpoře kontejnerizovaných aplikací Service Fabric používaných službou Service Fabric Mesh.

Když se během instalace zobrazí výzva, vyberte **Use Windows containers instead of Linux containers** (Použít kontejnery Windows místo kontejnerů Linuxu).

Pokud technologie Hyper-V není ve vašem počítači povolená, instalační program Dockeru nabídne jeho povolení. Po zobrazení příslušné výzvy klikněte na **OK**.

#### <a name="install-docker-on-windows-server-2016"></a>Instalace Dockeru na Windows Server 2016

Pokud nemáte povolenou roli Hyper-V, otevřete PowerShell jako správce, spuštěním následujícího příkazu povolte Hyper-V a pak restartuje počítač. Další informace najdete v tématu [Docker Enterprise Edition pro Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Restartujte počítač.

Otevřete PowerShell jako správce a spusťte následující příkaz pro instalaci Dockeru:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>Sada SDK a nástroje

Nainstalujte modul runtime Service Fabric Mesh, sadu SDK a nástroje v následujícím pořadí.

1. Nainstalujte sadu [Service Fabric Mesh SDK][download-sdkmesh] pomocí Instalace webové platformy. Tím se také provede instalace sady Microsoft Azure Service Fabric SDK a modulu runtime.
2. Nainstalujte [rozšíření Nástroje Microsoft Service Fabric Mesh pro Visual Studio (Preview)][download-tools] z webu Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Sestavení clusteru

> [!IMPORTANT]
> Před vytvořením clusteru **musí** být spuštěný Docker.
> Zkontrolujte, že je Dockeru spuštěný, otevřením okna terminálu a zadáním příkazu `docker ps`, abyste viděli, jestli došlo k chybě. Pokud odpověď neindikuje chybu, Docker je spuštěný a vy jste připravení vytvořit cluster.

> [!Note]
> Pokud vyvíjíte v počítači Windows Fall Creators update (verze 1709), můžete používat pouze windows verze 1709 docker image.
> Pokud vyvíjíte v počítači s aktualizací Windows 10 z dubna 2018 (verze 1803), můžete použít buď windows verze 1709 nebo 1803 irekátoru.

Pokud používáte Visual Studio, můžete tuto část přeskočit, protože Visual Studio vytvoří místní cluster pro vás, pokud ho nemáte.

Chcete-li dosáhnout nejlepšího výkonu ladění při vytváření a spouštění jedné aplikace Service Fabric najednou, vytvořte cluster místního vývoje s jedním uzlem. Pokud používáte více aplikací najednou, vytvořte cluster místního vývoje s pěti uzlovými daty. Cluster musí být spuštěn při každém nasazení nebo ladění projektu sítě Service Fabric.

Po instalaci runtime, Sady SDK, nástroje Sady Visual Studio, Docker a mají Docker běží, vytvořte vývojový cluster.

1. Zavřete okno PowerShellu.
2. Jako správce otevřete nové okno PowerShellu se zvýšenými oprávněními. Tento krok je nutný pro načtení modulů Service Fabric, které se nedávno nainstalovaly.
3. Vývojový cluster vytvoříte spuštěním následujícího příkazu PowerShellu:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Chcete-li spustit nástroj správce místního clusteru, spusťte následující příkaz Powershellu:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Po spuštění nástroje Správce clusteru služby (zobrazí se na hlavním panelu), klepněte na něj pravým tlačítkem myši a klepněte na příkaz **Spustit místní cluster**.

![Obrázek 1 – Spuštění místního clusteru](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Teď můžete začít vytvářet aplikace Service Fabric Mesh.

## <a name="next-steps"></a>Další kroky

Přečtěte si kurz [vytvoření aplikace Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

Najděte odpovědi na [běžné otázky a známé problémy](service-fabric-mesh-faq.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
---
title: Nastavení vývojového prostředí pro Windows pro Service Fabric sítě
description: Nastavte své vývojového prostředí ve Windows, abyste mohli vytvořit aplikaci Service Fabric Mesh a nasadit ji do Azure Service Fabric Mesh.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: 479e975abd40be80e17fc187f1d13500d11cb983
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842746"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Nastavení vývojového prostředí ve Windows pro sestavování aplikací Service Fabric Mesh

Pokud chcete vytvářet a spouštět aplikace Service Fabric sítě Azure na vývojovém počítači s Windows, budete potřebovat:

* Docker
* Visual Studio 2017 nebo novější
* Modul runtime sítě Service Fabric
* Sada SDK a nástroje pro Service Fabricovou mřížku.

A jednu z následujících verzí systému Windows:

* Windows 10 (Enterprise, Professional nebo školství) verze 1709 (aktualizace Creators Update) nebo 1803 (aktualizace Windows 10 duben 2018)
* Windows Server verze 1709
* Windows Server verze 1803

Následující pokyny vám pomůžou zajistit, aby se všechno nainstalovalo na základě používané verze Windows.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

K nasazení Service Fabricch aplikací pro mřížku se vyžaduje Visual Studio 2017 nebo novější. [Nainstalujte verzi 15.6.0][download-visual-studio] nebo vyšší a povolte následující sady funkcí:

* Vývoj pro ASP.NET a web
* Vývoj pro Azure

## <a name="install-docker"></a>Instalace Dockeru

Pokud už máte Docker nainstalovaný, ujistěte se, že máte nejnovější verzi. Docker vás může vyzvat, když je nová verze vydaná, ale zkontrolujte, jestli máte nejnovější verzi.

#### <a name="install-docker-on-windows-10"></a>Instalace Docker ve Windows 10

Stáhněte a nainstalujte nejnovější verzi [Docker Community Edition pro Windows][download-docker] sloužící k podpoře kontejnerizovaných aplikací Service Fabric používaných službou Service Fabric Mesh.

Když se během instalace zobrazí výzva, vyberte **Use Windows containers instead of Linux containers** (Použít kontejnery Windows místo kontejnerů Linuxu).

Pokud na vašem počítači není technologie Hyper-V povolená, instalační program Docker nabídne povolení. Po zobrazení příslušné výzvy klikněte na **OK**.

#### <a name="install-docker-on-windows-server-2016"></a>Instalace Docker na Windows serveru 2016

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
> Pokud vyvíjíte na počítači Windows Update Creators Update (verze 1709), můžete použít jenom image Windows verze 1709 Docker.
> Pokud vyvíjíte na počítači s Windows 10. dubna 2018 Update (verze 1803), můžete použít image Windows verze 1709 nebo 1803 Docker.

Pokud používáte aplikaci Visual Studio, můžete tuto část přeskočit, protože Visual Studio vytvoří místní cluster za vás, pokud ho ještě nemáte.

Pro dosažení nejlepšího výkonu ladění při vytváření a spouštění jediné aplikace Service Fabric v jednom okamžiku vytvořte místní vývojový cluster s jedním uzlem. Pokud spouštíte více aplikací najednou, vytvořte místní vývojový cluster s pěti uzly. Cluster musí být spuštěn při každém nasazení nebo ladění projektu Service Fabricové sítě.

Až nainstalujete modul runtime, sady SDK, nástroje sady Visual Studio, Docker a máte spuštěný Docker, vytvořte vývojový cluster.

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
5. Jakmile je nástroj Service Cluster Manager spuštěný (zobrazí se na hlavním panelu), klikněte na něj pravým tlačítkem myši a pak klikněte na **Spustit místní cluster**.

![Obrázek 1 – spuštění místního clusteru](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Teď můžete začít vytvářet aplikace Service Fabric Mesh.

## <a name="next-steps"></a>Další kroky

Přečtěte si kurz [vytvoření aplikace Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

Vyhledejte odpovědi na [Časté otázky a známé problémy](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/

---
title: Nastavení vývojového prostředí ve Windows pro sestavování aplikací Service Fabric Mesh | Microsoft Docs
description: Nastavte své vývojového prostředí ve Windows, abyste mohli vytvořit aplikaci Service Fabric Mesh a nasadit ji do Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 08/08/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 094cab324c7a7c66f5fcb57d488661109fcdabea
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362666"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Nastavení vývojového prostředí ve Windows pro sestavování aplikací Service Fabric Mesh

Pokud chcete sestavovat a spouštět aplikace Azure Service Fabric Mesh na vývojovém počítači s Windows, nainstalujte modul runtime Service Fabric Mesh, sadu SDK a nástroje.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Podporované verze operačních systémů

Pro vývoj jsou podporovány tyto verze operačních systémů:

* Windows 10 (Enterprise, Professional nebo Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

K nasazování aplikací Service Fabric Mesh se vyžaduje sada Visual Studio 2017. [Nainstalujte verzi 15.6.0][download-visual-studio] nebo vyšší a povolte následující sady funkcí:

* Vývoj pro ASP.NET a web
* Vývoj pro Azure

## <a name="install-docker"></a>Instalace Dockeru

#### <a name="windows-10"></a>Windows 10

Stáhněte a nainstalujte nejnovější verzi [Docker Community Edition pro Windows][download-docker] sloužící k podpoře kontejnerizovaných aplikací Service Fabric používaných službou Service Fabric Mesh.

Když se během instalace zobrazí výzva, vyberte **Use Windows containers instead of Linux containers** (Použít kontejnery Windows místo kontejnerů Linuxu).

Pokud na vašem počítači není povolená technologie Hyper-V, v instalačním programu Dockeru se zobrazí možnost ji povolit. Po zobrazení příslušné výzvy klikněte na **OK**.

#### <a name="windows-server-2016"></a>Windows Server 2016

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

Pokud používáte Visual Studio, můžete tuto část přeskočit, protože Visual Studio vytvoří místní cluster, pokud žádný nemáte.

Kvůli nejlepšímu výkonu ladění při vytváření a spouštění aplikací Service Fabric doporučujeme vytvořit místní vývojový cluster s jedním uzlem. Tento cluster musí být spuštěný vždy, když nasazujete nebo ladíte projekt služby Service Fabric Mesh.

Před vytvořením clusteru **musí** být spuštěný Docker. Zkontrolujte, že je Dockeru spuštěný, otevřením okna terminálu a zadáním příkazu `docker ps`, abyste viděli, jestli došlo k chybě. Pokud odpověď neindikuje chybu, Docker je spuštěný a vy jste připravení vytvořit cluster.

Po instalaci modulu runtime, sad SDK a nástrojů sady Visual Studio vytvořte vývojový cluster.

1. Zavřete okno PowerShellu.
2. Jako správce otevřete nové okno PowerShellu se zvýšenými oprávněními. Tento krok je nutný pro načtení modulů Service Fabric, které se nedávno nainstalovaly.
3. Vývojový cluster vytvoříte spuštěním následujícího příkazu PowerShellu:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. Chcete-li spustit nástroj správce místního clusteru, spusťte následující příkaz Powershellu:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

Teď můžete začít vytvářet aplikace Service Fabric Mesh.

## <a name="next-steps"></a>Další kroky

Přečtěte si kurz [vytvoření aplikace Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/
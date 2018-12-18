---
title: Nastavení vývojového prostředí ve Windows pro sestavování aplikací Service Fabric Mesh | Microsoft Docs
description: Nastavte své vývojového prostředí ve Windows, abyste mohli vytvořit aplikaci Service Fabric Mesh a nasadit ji do Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 12/12/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: a2c00d5373bd4e19a6e6057c6eddc758857860da
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556760"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Nastavení vývojového prostředí ve Windows pro sestavování aplikací Service Fabric Mesh

K vytváření a spouštění aplikací Azure Service Fabric mřížky na vývojovém počítači s Windows, budete potřebovat:

* Docker
* Visual Studio 2017
* Modul runtime Service Fabric mřížky
* Sada Service Fabric mřížky SDK a nástroje.

A jeden z následujících verzí Windows:

* Windows 10 (Enterprise, Professional nebo Education) verze 1709 (Fall Creators update) nebo 1803 (aktualizace systému Windows 10. dubna 2018)
* Windows Server verze 1709
* Windows Server verze 1803

Následující pokyny se vše, co je nainstalována nápověda založen na verzi Windows, kterou používáte.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

K nasazování aplikací Service Fabric Mesh se vyžaduje sada Visual Studio 2017. [Nainstalujte verzi 15.6.0][download-visual-studio] nebo vyšší a povolte následující sady funkcí:

* Vývoj pro ASP.NET a web
* Vývoj pro Azure

## <a name="install-docker"></a>Instalace Dockeru

Pokud už máte nainstalovaný Docker, ujistěte se, že máte nejnovější verzi. Docker může výzvu, pokud je nová verze ale Zkontrolujte ručně, ujistěte se, že máte nejnovější verzi.

#### <a name="install-docker-on-windows-10"></a>Nainstalujte Docker ve Windows 10

Stáhněte a nainstalujte nejnovější verzi [Docker Community Edition pro Windows][download-docker] sloužící k podpoře kontejnerizovaných aplikací Service Fabric používaných službou Service Fabric Mesh.

Když se během instalace zobrazí výzva, vyberte **Use Windows containers instead of Linux containers** (Použít kontejnery Windows místo kontejnerů Linuxu).

Pokud na vašem počítači není povolená technologie Hyper-V, v instalačním programu Dockeru se zobrazí možnost ji povolit. Po zobrazení příslušné výzvy klikněte na **OK**.

#### <a name="install-docker-on-windows-server-2016"></a>Nainstalovat Docker na Windows serveru 2016

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
> Pokud vyvíjíte na Windows Fall Creators update (verze 1709) počítače, můžete použít pouze imagí dockeru Windows verze 1709.
> Pokud vyvíjíte ve Windows 10. dubna 2018 update (verze 1803) počítače, můžete použít buď imagí dockeru verze 1709 nebo 1803 Windows.

Pokud používáte Visual Studio, můžete tuto část přeskočit, protože Visual Studio vytvoří místní cluster, pokud žádný nemáte.

Pro nejlepší ladění výkonu při vytvoření a spuštění jednoduché aplikace Service Fabric najednou vytvořte jedním uzlem místního vývojového clusteru. Pokud používáte několik aplikací najednou, vytvořte pěti uzly místního vývojového clusteru. Cluster musí být spuštěna vždy, když nasazujete nebo ladit projekt služby prostředků infrastruktury sítě.

Po instalaci modulu runtime, sady SDK, nástroje sady Visual Studio, Docker a mít spuštěnou Dockeru, vytvořte vývojový cluster.

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
5. Jakmile nástroj Správce clusteru služby je spuštěná (se zobrazí v hlavním panelu systému), pravým tlačítkem myši a klikněte na tlačítko **spustit místní Cluster**.

![Obrázek 1 – spuštění místního clusteru](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Teď můžete začít vytvářet aplikace Service Fabric Mesh.

## <a name="next-steps"></a>Další postup

Přečtěte si kurz [vytvoření aplikace Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

Najděte odpovědi na [běžné dotazy a známé problémy](service-fabric-mesh-faq.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
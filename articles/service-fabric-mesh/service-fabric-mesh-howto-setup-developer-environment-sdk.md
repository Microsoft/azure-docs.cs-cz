---
title: Nastavení vývojového prostředí ve Windows pro aplikace Service Fabric Mesh | Microsoft Docs
description: Nastavte své vývojového prostředí ve Windows, abyste mohli vytvořit aplikaci Service Fabric Mesh a nasadit ji do Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/20/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 589bef1894a3bee1e6974a0ea2516200fae2891f
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185539"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Nastavení vývojového prostředí ve Windows pro sestavování aplikací Service Fabric

Pokud chcete sestavovat a spouštět aplikace Azure Service Fabric na vývojovém počítači s Windows, nainstalujte modul runtime Service Fabric, sadu SDK a nástroje.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Podporované verze operačních systémů

Pro vývoj jsou podporovány tyto verze operačních systémů:

* Windows 10 (Enterprise, Professional nebo Education)
* Windows Server 2016

## <a name="visual-studio"></a>Visual Studio

K nasazení aplikace Service Fabric je nutné Visual Studio 2017. [Nainstalujte verzi 15.6.0][download-visual-studio] nebo vyšší a povolte následující sady funkcí:

- Vývoj pro ASP.NET a web
- Vývoj pro Azure
 
## <a name="windows-10---install-docker"></a>Windows 10 – instalace Dockeru

Stáhněte a nainstalujte nejnovější verzi [Docker Community Edition pro Windows][download-docker] sloužící k podpoře kontejnerizovaných aplikací Service Fabric používaných službou Service Fabric Mesh.

Když se během instalace zobrazí výzva, vyberte **Use Windows containers instead of Linux containers** (Použít kontejnery Windows místo kontejnerů Linuxu). Pokud na vašem počítači není povolená technologie Hyper-V, při instalaci Dockeru se zobrazí možnost ji povolit. Po zobrazení příslušné výzvy klikněte na **OK**.

## <a name="windows-server-2016---install-hyper-v-and-docker"></a>Windows Server 2016 – instalace Hyper-V a Dockeru

**Instalace Hyper-V**

Nejdřív otevřete PowerShell jako správce a spusťte následující příkaz, který nainstaluje Hyper-V a restartuje počítač. Další informace najdete v tématu [Docker Enterprise Edition pro Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

**Instalace Dockeru**

Otevřete PowerShell jako správce a spusťte následující příkaz pro instalaci Dockeru:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Restartujte počítač.

## <a name="sdk-and-tools"></a>Sada SDK a nástroje

Nainstalujte modul runtime Service Fabric Mesh, sadu SDK a nástroje v následujícím pořadí.

1. Nainstalujte sadu [Service Fabric Mesh SDK][download-sdkmesh] pomocí Instalace webové platformy. Tím se také provede instalace sady Microsoft Azure Service Fabric SDK a modulu runtime.
2. Nainstalujte [rozšíření Nástroje Microsoft Azure Service Fabric pro Visual Studio (Preview)][download-tools] z webu Visual Studio Marketplace.

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
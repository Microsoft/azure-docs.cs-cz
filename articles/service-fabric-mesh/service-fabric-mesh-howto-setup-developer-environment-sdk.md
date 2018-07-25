---
title: Nastavení vývojového prostředí ve Windows pro aplikace Service Fabric Mesh | Microsoft Docs
description: Nastavte své vývojového prostředí ve Windows, abyste mohli vytvořit aplikaci Service Fabric Mesh a nasadit ji do Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 96549696013a2dd94741090a0a017b57a3b1e19e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125157"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>Nastavení vývojového prostředí ve Windows pro sestavování aplikací Service Fabric

Pokud chcete sestavovat a spouštět aplikace Azure Service Fabric na vývojovém počítači s Windows, nainstalujte modul runtime Service Fabric, sadu SDK a nástroje.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>Podporované verze operačních systémů

Pro vývoj jsou podporovány tyto verze operačních systémů:

* Windows 10 (Enterprise, Professional nebo Education)
* Windows Server 2016

## <a name="enable-hyper-v"></a>Povolení Hyper-V

Aby bylo možné vytvářet aplikace Service Fabric, musí být povolená technologie Hyper-V. 

### <a name="windows-10"></a>Windows 10

Otevřete PowerShell jako správce a spusťte následující příkaz:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

Restartujte počítač. Další informace o povolení Hyper-V najdete v popisu [instalace technologie Hyper-V ve Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

### <a name="windows-server-2016"></a>Windows Server 2016

Otevřete PowerShell jako správce a spusťte následující příkaz:

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Restartujte počítač. Další informace o povolení Hyper-V najdete v popisu [instalace role Hyper-V ve Windows Serveru 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="visual-studio"></a>Visual Studio

K nasazení aplikace Service Fabric je nutné Visual Studio 2017. [Nainstalujte verzi 15.6.0][download-visual-studio] nebo vyšší a povolte následující sady funkcí:

- Vývoj pro ASP.NET a web
- Vývoj pro Azure

## <a name="docker"></a>Docker

Nainstalujte Docker pro podporu kontejnerizovaných aplikací Service Fabric používaných službou Service Fabric Mesh.

### <a name="windows-10"></a>Windows 10

Stáhněte a nainstalujte nejnovější verzi [Docker Community Edition pro Windows][download-docker]. 

Když se během instalace zobrazí výzva, vyberte **Use Windows containers instead of Linux containers** (Použít kontejnery Windows místo kontejnerů Linuxu). Budete se muset odhlásit a pak zase přihlásit. Po přihlášení zpět, pokud jste dříve nepovolili Hyper-V, se vám může zobrazit výzva k povolení Hyper-V. Musíte povolit Hyper-V a pak restartovat počítač.

Po restartování počítače vás Docker vyzve k povolení funkce **Kontejnery**. Povolte ji a restartujte počítač.

### <a name="windows-server-2016"></a>Windows Server 2016

K instalaci Dockeru použijte následující příkazy PowerShellu. Další informace najdete v tématu [Docker Enterprise Edition pro Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Restartujte počítač.

## <a name="sdk-and-tools"></a>Sada SDK a nástroje

Nainstalujte modul runtime Service Fabric Mesh, sadu SDK a nástroje v závislém pořadí.

1. Nainstalujte sadu [Service Fabric Mesh SDK][download-sdkmesh] pomocí Instalace webové platformy. Tím se také provede instalace sady Microsoft Azure Service Fabric SDK a modulu runtime.
2. Nainstalujte [rozšíření Nástroje Microsoft Azure Service Fabric pro Visual Studio (Preview)][download-tools] z webu Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Sestavení clusteru

Kvůli nejlepšímu výkonu ladění při vytváření a spouštění aplikací Service Fabric doporučujeme vytvořit místní vývojový cluster s jedním uzlem. Tento cluster musí být spuštěný vždy, když nasazujete nebo ladíte projekt služby Service Fabric Mesh.

Před vytvořením clusteru **musí** být spuštěný Docker. Zkontrolujte, že je Dockeru spuštěný, otevřením okna terminálu a zadáním příkazu `docker ps`, abyste viděli, jestli došlo k chybě. Pokud odpověď neindikuje chybu, Docker je spuštěný a vy jste připravení vytvořit cluster.

Po instalaci modulu runtime, sad SDK a nástrojů sady Visual Studio vytvořte vývojový cluster.

1. Zavřete okno PowerShellu.
2. Jako správce otevřete nové okno PowerShellu se zvýšenými oprávněními. Tento krok je nezbytný pro načtení modulů Service Fabric, které jste nainstalovali.
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

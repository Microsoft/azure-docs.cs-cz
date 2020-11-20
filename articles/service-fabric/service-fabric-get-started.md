---
title: Nastavení vývojového prostředí pro Windows
description: Nainstalujte modul runtime, sadu SDK a nástroje a vytvořte místní vývojový cluster. Po dokončení této instalace a nastavení budete moci sestavovat aplikace ve Windows.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: ff8af09e88c9c0d2af63070396824471af3ceeca
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980812"
---
# <a name="prepare-your-development-environment-on-windows"></a>Příprava vývojového prostředí ve Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)
>
>

Pokud chcete sestavovat a spouštět [aplikace Azure Service Fabric][1] na vývojovém počítači s Windows, nainstalujte modul runtime Service Fabric, sadu SDK a nástroje. Musíte také [povolit spouštění skriptů Windows PowerShellu](#enable-powershell-script-execution), které jsou součástí sady SDK.

## <a name="prerequisites"></a>Požadavky

### <a name="supported-operating-system-versions"></a>Podporované verze operačních systémů

Pro vývoj jsou podporovány tyto verze operačních systémů:

* Windows 7
* Windows 8 / Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Podpora Windows 7:
> - Windows 7 ve výchozím nastavení obsahuje jenom prostředí Windows PowerShell 2.0 Rutiny prostředí PowerShell pro Service Fabric vyžadují PowerShell 3.0 nebo novější. [Windows PowerShell 5,1][powershell5-download] si můžete stáhnout z webu Stažení softwaru.
> - Reverzní proxy Service Fabric není ve Windows 7 k dispozici.

## <a name="install-the-sdk-and-tools"></a>Instalace sady SDK a nástrojů

Instalační program webové platformy (WebPI) je doporučeným způsobem, jak nainstalovat sadu SDK a nástroje. Pokud obdržíte chyby za běhu pomocí WebPI, můžete také najít přímé odkazy na instalační programy v poznámkách k verzi pro konkrétní verzi Service Fabric. Poznámky k verzi najdete v různých oznámeních k vydaným verzím na [blogu týmu Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

> [!NOTE]
> Upgrade místních Service Fabric vývoje clusteru se nepodporuje.

### <a name="to-use-visual-studio-2017-or-2019"></a>Použití sady Visual Studio 2017 nebo 2019

Nástroje Service Fabric jsou součástí úlohy vývoje Azure v prostředí Visual Studio 2017 a 2019. Povolte tuto úlohu jako součást instalace sady Visual Studio.
Kromě toho budete muset pomocí Instalace webové platformy nainstalovat sadu Microsoft Azure Service Fabric SDK a modul runtime.

* [Instalace sady Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="sdk-installation-only"></a>Jenom instalace sady SDK

Pokud potřebujete jenom sadu SDK, můžete nainstalovat tento balíček:

* [Instalace sady Microsoft Azure Service Fabric SDK][core-sdk]

Aktuální verze jsou:

* 4.2.434 SDK a nástroje pro Service Fabric
* Service Fabric modulu runtime 7.2.434

Seznam podporovaných verzí najdete v tématu [Service Fabric verze](service-fabric-versions.md) .

> [!NOTE]
> Clustery s jedním počítačem (OneBox) nejsou pro upgrady aplikací nebo clusterů podporovány; Odstraňte cluster OneBox a znovu ho vytvořte, pokud potřebujete provést upgrade clusteru nebo máte problémy s upgradem aplikace. 

## <a name="enable-powershell-script-execution"></a>Povolení spouštění skriptů prostředí PowerShell

Platforma Service Fabric používá skripty prostředí Windows PowerShell k vytvoření místního vývojového clusteru a k nasazení aplikací ze sady Visual Studio. Systém Windows ve výchozím nastavení spouštění těchto skriptů blokuje. Pokud je chcete povolit, musíte upravit zásady spouštění prostředí PowerShell. Otevřete prostředí PowerShell jako správce a zadejte tento příkaz:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Nainstalovat Docker (volitelné)

[Service Fabric je kontejner Orchestrator](service-fabric-containers-overview.md) pro nasazení mikroslužeb napříč clusterem počítačů. Chcete-li spustit aplikace služby Windows Container v místním vývojovém clusteru, je nutné nejprve nainstalovat Docker for Windows. Získat [Docker CE for Windows (stabilní)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) Po nainstalování a spuštění Dockeru klikněte pravým tlačítkem myši na ikonu na hlavním panelu a vyberte **Switch to Windows containers** (Přepnout na kontejnery Windows). Tento krok se vyžaduje pro spuštění imagí Dockeru založených na Windows.

## <a name="next-steps"></a>Další kroky

Teď, když jste dokončili nastavení vývojového prostředí, můžete začít sestavovat a spouštět aplikace.

* [Naučte se vytvářet, nasazovat a spravovat aplikace.](service-fabric-tutorial-create-dotnet-app.md)
* [Seznamte se s programovacími modely: Reliable Services a Reliable Actors](service-fabric-choose-framework.md)
* [Prohlédněte si ukázky kódu Service Fabric na GitHubu](/samples/browse/?products=azure)
* [Vizualizujte cluster pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md)
* [Příprava linuxového vývojového prostředí ve Windows](service-fabric-local-linux-cluster-windows.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Stránka kampaně Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Odkaz na VS 2015 WebPI"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Odkaz na Dev15 WebPI"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Odkaz na Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616

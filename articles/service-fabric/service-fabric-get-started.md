---
title: Nastavení vývojového prostředí systému Windows
description: Nainstalujte modul runtime, sadu SDK a nástroje a vytvořte místní vývojový cluster. Po dokončení této instalace a nastavení budete moci sestavovat aplikace ve Windows.
author: peterpogorski
ms.topic: conceptual
ms.date: 03/02/2020
ms.custom: sfrev
ms.openlocfilehash: f08c6b0675475b4e15ce6db3a9dbe0e2863b9ddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252768"
---
# <a name="prepare-your-development-environment-on-windows"></a>Příprava vývojového prostředí ve Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
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
> - Windows 7 ve výchozím nastavení obsahuje jenom prostředí Windows PowerShell 2.0 Rutiny prostředí PowerShell pro Service Fabric vyžadují PowerShell 3.0 nebo novější. Systém [Windows PowerShell 5.1 5.1 si][powershell5-download] můžete stáhnout ze služby Stažení softwaru.
> - Reverzní proxy Service Fabric není ve Windows 7 k dispozici.

## <a name="install-the-sdk-and-tools"></a>Instalace sady SDK a nástrojů

Instalační služba webové platformy (WebPI) je doporučený způsob instalace sady SDK a nástrojů. Pokud se zobrazí chyby za běhu pomocí webpi, můžete také najít přímé odkazy na instalační programy v poznámkách k verzi pro konkrétní verzi Service Fabric. Poznámky k verzi lze nalézt v různých oznámeních o verzi na [blogu týmu Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Upgrady vývojového clusteru infrastruktury místních služeb nejsou podporovány.

### <a name="to-use-visual-studio-2017-or-2019"></a>Použití Visual Studia 2017 nebo 2019

Nástroje Service Fabric jsou součástí úlohy vývoje Azure ve Visual Studiu 2017 a 2019. Povolte tuto úlohu jako součást instalace sady Visual Studio.
Kromě toho budete muset pomocí Instalace webové platformy nainstalovat sadu Microsoft Azure Service Fabric SDK a modul runtime.

* [Instalace sady Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Použití sady Visual Studio 2015 (vyžaduje Visual Studio 2015 Update 2 nebo novější)

Pro sadu Visual Studio 2015 se nástroje Service Fabric nainstalují společně se sadou SDK a modulem runtime pomocí Instalace webové platformy:

* [Instalace sady Microsoft Azure Service Fabric SDK a nástrojů][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Jenom instalace sady SDK

Pokud potřebujete jenom sadu SDK, můžete nainstalovat tento balíček:

* [Instalace sady Microsoft Azure Service Fabric SDK][core-sdk]

Aktuální verze jsou:

* Service Fabric SDK a nástroje 4.0.470
* Provozní doba service fabric 7.0.470

Seznam podporovaných verzí naleznete v tématu [Service Fabric verze](service-fabric-versions.md)

> [!NOTE]
> Clustery jednoho počítače (OneBox) nejsou podporovány pro inovace aplikací nebo clusterů; Odstraňte cluster OneBox a znovu jej vytvořte, pokud potřebujete provést upgrade clusteru nebo máte nějaké problémy s upgradem aplikace. 

## <a name="enable-powershell-script-execution"></a>Povolení spouštění skriptů prostředí PowerShell

Platforma Service Fabric používá skripty prostředí Windows PowerShell k vytvoření místního vývojového clusteru a k nasazení aplikací ze sady Visual Studio. Systém Windows ve výchozím nastavení spouštění těchto skriptů blokuje. Pokud je chcete povolit, musíte upravit zásady spouštění prostředí PowerShell. Otevřete prostředí PowerShell jako správce a zadejte tento příkaz:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Instalace Dockeru (volitelné)

[Service Fabric je kontejner orchestrator](service-fabric-containers-overview.md) pro nasazení mikroslužeb v clusteru počítačů. Chcete-li spustit aplikace kontejnerů systému Windows v místním vývojovém clusteru, musíte nejprve nainstalovat Docker pro Windows. Získejte [Docker CE pro Windows (stabilní)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Po nainstalování a spuštění Dockeru klikněte pravým tlačítkem myši na ikonu na hlavním panelu a vyberte **Switch to Windows containers** (Přepnout na kontejnery Windows). Tento krok se vyžaduje pro spuštění imagí Dockeru založených na Windows.

## <a name="next-steps"></a>Další kroky

Teď, když jste dokončili nastavení vývojového prostředí, můžete začít sestavovat a spouštět aplikace.

* [Naučte se vytvářet, nasazovat a spravovat aplikace](service-fabric-tutorial-create-dotnet-app.md)
* [Seznamte se s programovacími modely: Reliable Services a Reliable Actors](service-fabric-choose-framework.md)
* [Prohlédněte si ukázky kódu Service Fabric na GitHubu](https://aka.ms/servicefabricsamples)
* [Vizualizujte cluster pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Stránka kampaně Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Odkaz na VS 2015 WebPI"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Odkaz na Dev15 WebPI"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Odkaz na Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616

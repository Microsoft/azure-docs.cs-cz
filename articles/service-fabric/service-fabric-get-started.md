---
title: Nastavení vývojového prostředí ve Windows pro mikroslužby Azure | Dokumentace Microsoftu
description: Nainstalujte modul runtime, sadu SDK a nástroje a vytvořte místní vývojový cluster. Po dokončení této instalace a nastavení budete moci sestavovat aplikace ve Windows.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: ryanwi, mikhegn
ms.openlocfilehash: 1012cdc73e721c65502d749ce27eb6e9cbda6bf4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
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

* Windows 7
* Windows 8 / Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Podpora Windows 7:
> - Windows 7 ve výchozím nastavení obsahuje jenom prostředí Windows PowerShell 2.0 Rutiny prostředí PowerShell pro Service Fabric vyžadují PowerShell 3.0 nebo novější. Můžete si [stáhnout prostředí Windows PowerShell 5.0][powershell5-download] z webu Microsoft Download Center.
> - Reverzní proxy Service Fabric není ve Windows 7 k dispozici.
>

## <a name="install-the-sdk-and-tools"></a>Instalace sady SDK a nástrojů
### <a name="to-use-visual-studio-2017"></a>Použití sady Visual Studio 2017
Nástroje Service Fabric Tools jsou součástí sady funkcí Vývoj pro Azure v sadě Visual Studio 2017. Povolte tuto úlohu jako součást instalace sady Visual Studio.
Kromě toho budete muset pomocí Instalace webové platformy nainstalovat sadu Microsoft Azure Service Fabric SDK a modul runtime.

* [Instalace sady Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Použití sady Visual Studio 2015 (vyžaduje Visual Studio 2015 Update 2 nebo novější)
Pro sadu Visual Studio 2015 se nástroje Service Fabric nainstalují společně se sadou SDK a modulem runtime pomocí Instalace webové platformy:

* [Instalace sady Microsoft Azure Service Fabric SDK a nástrojů][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Jenom instalace sady SDK
Pokud potřebujete jenom sadu SDK, můžete nainstalovat tento balíček:
* [Instalace sady Microsoft Azure Service Fabric SDK][core-sdk]

Aktuální verze jsou:
* Sada Service Fabric SDK a Service Fabric Tools 3.0.480
* Modul runtime Service Fabric 6.1.480
* Service Fabric Tools for Visual Studio 2015 2.0.10124.2
* Visual Studio 2017 15.5.6 obsahuje Service Fabric Tools for Visual Studio 2.0.20180124.2  

Seznam podporovaných verzí najdete v tématu [Podpora pro Service Fabric](service-fabric-support.md)

## <a name="enable-powershell-script-execution"></a>Povolení spouštění skriptů prostředí PowerShell
Platforma Service Fabric používá skripty prostředí Windows PowerShell k vytvoření místního vývojového clusteru a k nasazení aplikací ze sady Visual Studio. Systém Windows ve výchozím nastavení spouštění těchto skriptů blokuje. Pokud je chcete povolit, musíte upravit zásady spouštění prostředí PowerShell. Otevřete prostředí PowerShell jako správce a zadejte tento příkaz:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Další kroky
Teď, když jste dokončili nastavení vývojového prostředí, můžete začít sestavovat a spouštět aplikace.

* [Vytvořte první aplikaci Service Fabric v sadě Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Naučte se nasadit a spravovat aplikace v místním clusteru](service-fabric-get-started-with-a-local-cluster.md)
* [Příprava linuxového vývojového prostředí ve Windows](service-fabric-local-linux-cluster-windows.md)
* [Seznamte se s programovacími modely: Reliable Services a Reliable Actors](service-fabric-choose-framework.md)
* [Pravidelné zálohování a obnovení v Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
* [Prohlédněte si ukázky kódu Service Fabric na GitHubu](https://aka.ms/servicefabricsamples)
* [Vizualizujte cluster pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md)
* [Postupujte podle studijního plánu Service Fabric a získejte obecný úvod k platformě](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)
* [Automatizace oprav operačního systému v clusteru](service-fabric-patch-orchestration-application.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Stránka kampaně Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Odkaz na VS 2015 WebPI"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Odkaz na Dev15 WebPI"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Odkaz na Core SDK WebPI"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

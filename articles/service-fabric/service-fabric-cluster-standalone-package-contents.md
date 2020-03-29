---
title: Samostatný balíček Azure Service Fabric pro Windows Server
description: Popis a obsah samostatného balíčku Azure Service Fabric pro Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451851"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Obsah samostatného balíčku Service Fabric pro Windows Server
Ve [staženém](https://go.microsoft.com/fwlink/?LinkId=730690) balíčku Service Fabric Standalone najdete následující soubory:

| **Název souboru** | **Stručný popis** |
| --- | --- |
| Vytvořit službu CreateServiceFabricCluster.ps1 |Skript prostředí PowerShell, který vytváří cluster pomocí nastavení v souboru ClusterConfig.json. |
| Odebrat servicefabriccluster.ps1 |Skript prostředí PowerShell, který odebere cluster pomocí nastavení v souboru ClusterConfig.json. |
| AddNode.ps1 |Skript prostředí PowerShell pro přidání uzlu do existujícího nasazeného clusteru v aktuálním počítači. |
| RemoveNode.ps1 |Skript prostředí PowerShell pro odebrání uzlu z existujícího nasazeného clusteru z aktuálního počítače. |
| CleanFabric.ps1 |Skript prostředí PowerShell pro čištění samostatné instalace Service Fabric z aktuálního počítače. Předchozí instalace MSI by měly být odebrány pomocí vlastních přidružených odinstalačních programů. |
| TestConfiguration.ps1 |Skript prostředí PowerShell pro analýzu infrastruktury, jak je uvedeno v clusteru.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Skript prostředí PowerShell používaný ke stažení nejnovějšího balíčku runtime mimo pásmo pro scénáře, kde nasazovací počítač není připojen k Internetu. |
| DeploymentComponentsAutoextractor.exe |Samorozbalovací archiv obsahující součásti nasazení používané skripty samostatného balíčku. |
| EULA_ENU.txt |Licenční podmínky pro použití samostatného balíčku Microsoft Azure Service Fabric windows serveru. Nyní si můžete [stáhnout kopii eula.](https://go.microsoft.com/fwlink/?LinkID=733084) |
| Readme.txt |Odkaz na poznámky k verzi a základní pokyny k instalaci. Jedná se o podmnožinu pokynů v tomto dokumentu. |
| Třetí stranaOznámení.rtf |Oznámení o softwaru jiného výrobce, který je v balíčku. |
| Nástroje\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |Samostatný logCollector.exe, který je spuštěn na vyžádání shromažďovat a nahrávat protokoly trasování společnosti Microsoft pro účely podpory. |
| Nástroje\ServiceFabricUpdateService.zip |Nástroj používaný k povolení automatického upgradu kódu pro clustery, které nemají přístup k internetu. Více informací naleznete [zde](service-fabric-cluster-upgrade-windows-server.md)|

**Šablony** 

| **Název souboru** | **Stručný popis** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Ukázkový soubor konfigurace clusteru, který obsahuje nastavení pro nezabezpečený vývojový cluster se třemi uzly, jedním počítačem (nebo virtuálním počítačem), včetně informací pro každý uzel v clusteru. |
| ClusterConfig.Unsecure.MultiMachine.json |Ukázkový soubor konfigurace clusteru, který obsahuje nastavení pro nezabezpečený cluster s více počítači (nebo virtuálním počítačem), včetně informací pro každý počítač v clusteru. |
| ClusterConfig.Windows.DevCluster.json |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení pro vývojový cluster zabezpečeného tříuzlů, jednoho počítače (nebo virtuálního počítače), včetně informací pro každý uzel, který je v clusteru. Cluster je zabezpečen pomocí [identit systému Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení zabezpečeného clusteru s více počítači (nebo virtuálního počítače) pomocí zabezpečení systému Windows, včetně informací pro každý počítač v zabezpečeném clusteru. Cluster je zabezpečen pomocí [identit systému Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení pro vývojový cluster zabezpečeného tříuzlů, jednoho počítače (nebo virtuálního počítače), včetně informací pro každý uzel v clusteru. Cluster je zabezpečen pomocí certifikátů x509. |
| ClusterConfig.x509.MultiMachine.json |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení zabezpečeného clusteru s více počítači (nebo virtuálnípočítače), včetně informací pro každý uzel zabezpečeného clusteru. Cluster je zabezpečen pomocí certifikátů x509. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení zabezpečeného clusteru s více počítači (nebo virtuálnípočítače), včetně informací pro každý uzel zabezpečeného clusteru. Cluster je zabezpečen pomocí [účtů spravovaných služeb skupiny](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Ukázky konfigurace clusteru
Nejnovější verze konfiguračních šablon clusteru najdete na stránce GitHub: [Ukázky konfigurace samostatného clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Nezávislý runtime balíček
Nejnovější runtime balíček se stahuje automaticky během nasazení clusteru z [odkazu ke stažení – Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Související
* [Vytvoření samostatného clusteru Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Scénáře zabezpečení clusteru Service Fabric](service-fabric-windows-cluster-windows-security.md)

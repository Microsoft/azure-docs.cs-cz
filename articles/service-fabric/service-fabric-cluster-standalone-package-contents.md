---
title: Samostatný balíček Azure Service Fabric pro Windows Server
description: Popis a obsah samostatného balíčku Azure Service Fabric pro Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: fb9a52510788a275bdeea67cd5c1fdd2e894738d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261021"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Obsah samostatného balíčku Service Fabric pro Windows Server
Ve [staženém](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric samostatném balíčku najdete následující soubory:

| **Název souboru** | **Krátký popis** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Skript PowerShellu, který vytvoří cluster s použitím nastavení v ClusterConfig.js. |
| RemoveServiceFabricCluster.ps1 |Skript PowerShellu, který odebere cluster s použitím nastavení v ClusterConfig.js. |
| AddNode.ps1 |PowerShellový skript pro přidání uzlu do existujícího nasazeného clusteru v aktuálním počítači. |
| RemoveNode.ps1 |PowerShellový skript pro odebrání uzlu z existujícího nasazeného clusteru z aktuálního počítače. |
| CleanFabric.ps1 |PowerShellový skript pro vyčištění samostatné instalace Service Fabric mimo aktuální počítač. Předchozí instalace MSI by se měly odebrat pomocí vlastních přidružených odinstalačních programů. |
| TestConfiguration.ps1 |PowerShellový skript pro analýzu infrastruktury, jak je uvedeno v Cluster.js. |
| DownloadServiceFabricRuntimePackage.ps1 |PowerShellový skript, který se používá pro stažení nejnovějšího běhového balíčku, pro scénáře, ve kterých není nasazený počítač připojený k Internetu. |
| DeploymentComponentsAutoextractor.exe |Samorozbalovací soubor archivu obsahující součásti pro nasazení používané samostatnými skripty balíčku. |
| EULA_ENU.txt |Licenční smlouva pro použití samostatného balíčku Windows Server Microsoft Azure Service Fabric [Kopii smlouvy EULA](https://go.microsoft.com/fwlink/?LinkID=733084) si můžete stáhnout hned teď. |
| Readme.txt |Odkaz na poznámky k verzi a základní pokyny k instalaci. Je podmnožinou pokynů v tomto dokumentu. |
| ThirdPartyNotice. RTF |Oznámení o softwaru třetích stran, který je v balíčku. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe, která se spouští na vyžádání pro shromáždění a nahrání protokolů trasování do Microsoftu pro účely podpory. |
| Tools\ServiceFabricUpdateService.zip |Nástroj, který slouží k povolení automatického upgradu kódu pro clustery, které nemají přístup k Internetu. Další podrobnosti najdete [tady](service-fabric-cluster-upgrade-windows-server.md).|

**Šablony** 

| **Název souboru** | **Krátký popis** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.jsna |Ukázkový soubor konfigurace clusteru, který obsahuje nastavení pro nezabezpečený vývojový cluster se třemi uzly, jeden počítač (nebo virtuální počítač), včetně informací pro každý uzel v clusteru. |
| ClusterConfig.Unsecure.MultiMachine.jsna |Ukázkový soubor konfigurace clusteru, který obsahuje nastavení pro nezabezpečený cluster s více počítači (nebo virtuální počítač), včetně informací pro každý počítač v clusteru. |
| ClusterConfig.Windows.DevCluster.jsna |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení zabezpečeného vývojového clusteru se třemi uzly, jednoho počítače (nebo virtuálního počítače), včetně informací pro každý uzel v clusteru. Cluster je zabezpečený pomocí [identit Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)). |
| ClusterConfig.Windows.MultiMachine.jsna |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení zabezpečeného clusteru s více počítači (nebo virtuálního počítače) používajícího zabezpečení systému Windows, včetně informací pro každý počítač, který je v zabezpečeném clusteru. Cluster je zabezpečený pomocí [identit Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)). |
| ClusterConfig.x509.DevCluster.jsna |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení zabezpečeného vývojového clusteru se třemi uzly, jednoho počítače (nebo virtuálního počítače), včetně informací pro každý uzel v clusteru. Cluster je zabezpečený pomocí certifikátů x509. |
| ClusterConfig.x509.MultiMachine.jsna |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení pro zabezpečený cluster s více počítači (nebo virtuální počítač), včetně informací pro každý uzel v zabezpečeném clusteru. Cluster je zabezpečený pomocí certifikátů x509. |
| ClusterConfig.gMSA.Windows.MultiMachine.jsna |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení pro zabezpečený cluster s více počítači (nebo virtuální počítač), včetně informací pro každý uzel v zabezpečeném clusteru. Cluster je zabezpečený pomocí [skupinových účtů spravované služby](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)). |

## <a name="cluster-configuration-samples"></a>Ukázky konfigurace clusteru
Nejnovější verze šablon konfigurace clusteru najdete na stránce GitHub: [samostatné ukázky konfigurace clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Nezávislý běhový balíček
Nejnovější běhový balíček se stáhne automaticky během nasazování clusteru z [odkazu na stažení Service Fabric modul runtime – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Související
* [Vytvoření samostatného clusteru Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Service Fabric scénáře zabezpečení clusteru](service-fabric-windows-cluster-windows-security.md)

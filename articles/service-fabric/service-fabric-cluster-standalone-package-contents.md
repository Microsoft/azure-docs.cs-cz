---
title: Samostatný balíček Azure Service Fabric pro Windows Server
description: Popis a obsah samostatného balíčku Azure Service Fabric pro Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451851"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Obsah samostatného balíčku Service Fabric pro Windows Server
Ve [staženém](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric samostatném balíčku najdete následující soubory:

| **Název souboru** | **Krátký popis** |
| --- | --- |
| CreateServiceFabricCluster. ps1 |Skript PowerShellu, který vytvoří cluster pomocí nastavení v ClusterConfig. JSON. |
| RemoveServiceFabricCluster. ps1 |Skript PowerShellu, který odebere cluster pomocí nastavení v ClusterConfig. JSON. |
| Této. ps1 |PowerShellový skript pro přidání uzlu do existujícího nasazeného clusteru v aktuálním počítači. |
| RemoveNode. ps1 |PowerShellový skript pro odebrání uzlu z existujícího nasazeného clusteru z aktuálního počítače. |
| CleanFabric. ps1 |PowerShellový skript pro vyčištění samostatné instalace Service Fabric mimo aktuální počítač. Předchozí instalace MSI by se měly odebrat pomocí vlastních přidružených odinstalačních programů. |
| TestConfiguration. ps1 |PowerShellový skript pro analýzu infrastruktury, jak je uvedeno v clusteru. JSON. |
| DownloadServiceFabricRuntimePackage. ps1 |PowerShellový skript, který se používá pro stažení nejnovějšího běhového balíčku, pro scénáře, ve kterých není nasazený počítač připojený k Internetu. |
| DeploymentComponentsAutoextractor. exe |Samorozbalovací soubor archivu obsahující součásti pro nasazení používané samostatnými skripty balíčku. |
| EULA_ENU. txt |Licenční smlouva pro použití samostatného balíčku Windows Server Microsoft Azure Service Fabric [Kopii smlouvy EULA](https://go.microsoft.com/fwlink/?LinkID=733084) si můžete stáhnout hned teď. |
| Soubor Readme. txt |Odkaz na poznámky k verzi a základní pokyny k instalaci. Je podmnožinou pokynů v tomto dokumentu. |
| ThirdPartyNotice. RTF |Oznámení o softwaru třetích stran, který je v balíčku. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector. exe, který je spuštěn na vyžádání pro shromáždění a nahrání protokolů trasování do Microsoftu pro účely podpory. |
| Tools\ServiceFabricUpdateService.zip |Nástroj, který slouží k povolení automatického upgradu kódu pro clustery, které nemají přístup k Internetu. Další podrobnosti najdete [tady](service-fabric-cluster-upgrade-windows-server.md) .|

**Šablony** 

| **Název souboru** | **Krátký popis** |
| --- | --- |
| ClusterConfig. Unsecure. DevCluster. JSON |Ukázkový soubor konfigurace clusteru, který obsahuje nastavení pro nezabezpečený vývojový cluster se třemi uzly, jeden počítač (nebo virtuální počítač), včetně informací pro každý uzel v clusteru. |
| ClusterConfig. Unsecure. getmachine. JSON |Ukázkový soubor konfigurace clusteru, který obsahuje nastavení pro nezabezpečený cluster s více počítači (nebo virtuální počítač), včetně informací pro každý počítač v clusteru. |
| ClusterConfig. Windows. DevCluster. JSON |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení zabezpečeného vývojového clusteru se třemi uzly, jednoho počítače (nebo virtuálního počítače), včetně informací pro každý uzel v clusteru. Cluster je zabezpečený pomocí [identit Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig. Windows. Machine. JSON |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení zabezpečeného clusteru s více počítači (nebo virtuálního počítače) používajícího zabezpečení systému Windows, včetně informací pro každý počítač, který je v zabezpečeném clusteru. Cluster je zabezpečený pomocí [identit Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig. X509. DevCluster. JSON |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení zabezpečeného vývojového clusteru se třemi uzly, jednoho počítače (nebo virtuálního počítače), včetně informací pro každý uzel v clusteru. Cluster je zabezpečený pomocí certifikátů x509. |
| ClusterConfig. X509. Machine. JSON |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení pro zabezpečený cluster s více počítači (nebo virtuální počítač), včetně informací pro každý uzel v zabezpečeném clusteru. Cluster je zabezpečený pomocí certifikátů x509. |
| ClusterConfig. gMSA. Windows. Machine. JSON |Ukázkový soubor konfigurace clusteru, který obsahuje všechna nastavení pro zabezpečený cluster s více počítači (nebo virtuální počítač), včetně informací pro každý uzel v zabezpečeném clusteru. Cluster je zabezpečený pomocí [skupinových účtů spravované služby](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Ukázky konfigurace clusteru
Nejnovější verze šablon konfigurace clusteru najdete na stránce GitHub: [samostatné ukázky konfigurace clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Nezávislý běhový balíček
Nejnovější běhový balíček se stáhne automaticky během nasazování clusteru z [odkazu na stažení Service Fabric modul runtime – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Související
* [Vytvoření samostatného clusteru Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Service Fabric scénáře zabezpečení clusteru](service-fabric-windows-cluster-windows-security.md)

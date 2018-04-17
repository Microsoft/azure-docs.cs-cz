---
title: Samostatný balíček prostředků infrastruktury Azure služby pro systém Windows Server | Microsoft Docs
description: Popis a obsah balíčku Azure Service Fabric Standalone pro systém Windows Server.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: maburlik;aljo
ms.openlocfilehash: 37425f8a076be2579726e5a83bec3da8c6eb5fff
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Obsah balíčku Fabric samostatné služby pro systém Windows Server
V [Stáhnout](http://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric samostatný balíček, zjistíte, následující soubory:

| **Název souboru** | **Krátký popis** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Skript prostředí PowerShell, který vytváří cluster pomocí nastavení v souboru ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Skript prostředí PowerShell, které odstraní cluster pomocí nastavení v souboru ClusterConfig.json. |
| AddNode.ps1 |Skript prostředí PowerShell pro přidání uzlu do existujícího nasazení clusteru do aktuálního počítače. |
| RemoveNode.ps1 |Skript prostředí PowerShell pro odebrání uzlu ze stávajícího nasazení clusteru z aktuálního počítače. |
| CleanFabric.ps1 |Skript prostředí PowerShell pro čištění samostatnou instalaci Service Fabric vypnout do aktuálního počítače. Pomocí vlastních přidružené uninstallers, byste měli odebrat předchozí instalace Instalační služby MSI. |
| TestConfiguration.ps1 |Skript prostředí PowerShell pro analýzu infrastruktury zadané v Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Skript prostředí PowerShell použít pro stažení nejnovější balíčku runtime vzdálené správy pro scénáře, kde není nasazení počítač připojený k Internetu. |
| DeploymentComponentsAutoextractor.exe |Samorozbalovací archivu obsahující nasazení komponenty používané stránkami skripty samostatný balíček. |
| EULA_ENU.txt |Licenční podmínky pro použití balíček Windows Server samostatné verze Microsoft Azure Service Fabric. Můžete [stáhnout kopii smlouvy EULA](http://go.microsoft.com/fwlink/?LinkID=733084) nyní. |
| Readme.txt |Odkaz na poznámky k verzi a pokyny k základní instalaci. Je podmnožinou podle pokynů v tomto dokumentu. |
| ThirdPartyNotice.rtf |Oznámení o software třetích stran, který je v balíčku. |
| Tools\Microsoft.Azure.ServiceFabric.windowsserver.SupportPackage.zip |StandaloneLogCollector.exe který běží na vyžádání shromažďovat a odesílat protokoly trasování pro účely podpory společnosti Microsoft. |
| Tools\ServiceFabricUpdateService.zip |Nástroj používaný pro povolení automatického upgradu kódu pro clustery, které nemají přístup k Internetu. Další podrobnosti najdete [sem](service-fabric-cluster-upgrade-windows-server.md)|

**Šablony** 
| **Název souboru** | **Krátký popis** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Soubor ukázkové konfiguraci clusteru, který obsahuje nastavení pro zabezpečená, tři uzly, jeden počítač (nebo virtuální počítač) vývoj clusteru služby, včetně informací pro každý uzel v clusteru. |
| ClusterConfig.Unsecure.MultiMachine.json |Soubor ukázkové konfiguraci clusteru, který obsahuje nastavení pro cluster služby zabezpečená, více počítače (nebo virtuálního počítače), včetně informací pro každý počítač v clusteru. |
| ClusterConfig.Windows.DevCluster.json |Soubor ukázkové konfiguraci clusteru, který obsahuje všechna nastavení pro zabezpečené, tři uzly, jeden počítač (nebo virtuální počítač) vývoj cluster, včetně informací pro každý uzel, který je v clusteru. Cluster je zabezpečená pomocí [Windows identity](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Soubor ukázkové konfiguraci clusteru, který obsahuje všechna nastavení zabezpečení, více počítače (nebo virtuálního počítače) clusteru pomocí zabezpečení systému Windows, včetně informací pro každý počítač, který je v zabezpečené clusteru. Cluster je zabezpečená pomocí [Windows identity](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Soubor ukázkové konfiguraci clusteru, který obsahuje všechna nastavení pro zabezpečené, tři uzly, jeden počítač (nebo virtuální počítač) vývoj cluster, včetně informací pro každý uzel v clusteru. Cluster zabezpečené pomocí x509 certifikáty. |
| ClusterConfig.x509.MultiMachine.json |Soubor ukázkové konfiguraci clusteru, který obsahuje všechna nastavení pro cluster zabezpečené, více počítače (nebo virtuálního počítače), včetně informací pro každý uzel v clusteru zabezpečené. Cluster zabezpečené pomocí x509 certifikáty. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Soubor ukázkové konfiguraci clusteru, který obsahuje všechna nastavení pro cluster zabezpečené, více počítače (nebo virtuálního počítače), včetně informací pro každý uzel v clusteru zabezpečené. Cluster zabezpečené pomocí [skupinové účty spravované služby](https://technet.microsoft.com/en-us/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Ukázky konfigurace clusteru
Nejnovější verze šablon konfigurace clusteru najdete na GitHub stránce: [ukázky konfigurace clusteru samostatné](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Nezávislé balíček modulu Runtime
Nejnovější modul runtime balíček se nestahuje automaticky při nasazení clusteru z [stáhnout odkaz - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Související
* [Vytvoření clusteru s podporou samostatné Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Scénáře zabezpečení clusteru Service Fabric](service-fabric-windows-cluster-windows-security.md)

---
title: Azure Service Fabric samostatného balíčku pro Windows Server | Dokumentace Microsoftu
description: Popis a obsah Azure Service Fabric samostatného balíčku pro systém Windows Server.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1a34ad340463faf0cdccdb7fa8b87283de60b4cc
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141607"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Obsah balíčku Service Fabric Standalone pro systém Windows Server
V [stáhli](https://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric samostatného balíčku, najdete následující soubory:

| **Název souboru** | **Krátký popis** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Skript Powershellu, který vytváří cluster pomocí nastavení v ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Skript Powershellu, které odstraní cluster pomocí nastavení v ClusterConfig.json. |
| AddNode.ps1 |Skript prostředí PowerShell pro přidání uzlu do existujícího nasazení clusteru na aktuálním počítači. |
| RemoveNode.ps1 |Skript Powershellu pro odebrání uzlu ze stávajícího nasazení clusteru z aktuálního počítače. |
| CleanFabric.ps1 |Skript prostředí PowerShell pro čištění samostatná instalace Service Fabric vypnout aktuálního počítače. Používání svoje vlastní přidružené uninstallers, byste měli odebrat předchozí instalace MSI. |
| TestConfiguration.ps1 |Skript prostředí PowerShell pro analýzu infrastruktury, jak je uvedeno v Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Skript prostředí PowerShell použít si stáhli nejnovější balíček modulu runtime mimo pásmo, pro scénáře, kdy nasazujete počítač není připojený k Internetu. |
| DeploymentComponentsAutoextractor.exe |Extrahování archiv obsahující komponenty pro nasazení používá skripty samostatný balíček. |
| EULA_ENU.txt |Licenční podmínky pro použití Microsoft Azure Service Fabric samostatného systému Windows Server balíčku. Je možné [stáhnout kopii této smlouvy EULA](https://go.microsoft.com/fwlink/?LinkID=733084) nyní. |
| Readme.txt |Odkaz na zprávu k vydání verze a pokyny k základní instalaci. Je podmnožinou podle pokynů v tomto dokumentu. |
| ThirdPartyNotice.rtf |Oznámení o softwaru třetích stran, který je v balíčku. |
| Tools\Microsoft.Azure.ServiceFabric.windowsserver.SupportPackage.zip |StandaloneLogCollector.exe který běží na vyžádání ke shromáždění a nahrajeme do Microsoftu protokoly trasování za účelem podpory. |
| Tools\ServiceFabricUpdateService.zip |Nástroj, který se používá k povolení automatického upgradu kódu pro clustery, které nemají přístup k Internetu. Další podrobnosti najdete [zde](service-fabric-cluster-upgrade-windows-server.md)|

**Šablony** 

| **Název souboru** | **Krátký popis** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Soubor ukázky konfigurace clusteru, který obsahuje nastavení nezabezpečenou se třemi uzly, jednoho počítače (nebo virtuálním počítači) vývojový cluster, včetně informací pro každý uzel v clusteru. |
| ClusterConfig.Unsecure.MultiMachine.json |Soubor ukázkové konfigurace clusteru, který obsahuje nastavení pro nezabezpečené, více počítačů (nebo virtuálního počítače) clusteru, včetně informací pro každý počítač v clusteru. |
| ClusterConfig.Windows.DevCluster.json |Soubor ukázky konfigurace clusteru, který obsahuje všechna nastavení zabezpečení se třemi uzly, jeden počítač (nebo virtuálním počítači) vývojový cluster, včetně informací pro každý uzel, který je v clusteru. Je tento cluster zabezpečený pomocí [identity Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Soubor ukázky konfigurace clusteru, který obsahuje všechna nastavení zabezpečení, více počítačů (nebo virtuálního počítače) clusteru pomocí zabezpečení Windows, včetně informací pro každý počítač, který je zabezpečený cluster. Je tento cluster zabezpečený pomocí [identity Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Soubor ukázky konfigurace clusteru, který obsahuje všechna nastavení zabezpečení se třemi uzly, jeden počítač (nebo virtuálním počítači) vývojový cluster, včetně informací pro každý uzel v clusteru. Je tento cluster zabezpečený pomocí x509 certifikáty. |
| ClusterConfig.x509.MultiMachine.json |Soubor ukázky konfigurace clusteru, který obsahuje všechna nastavení pro zabezpečené, více počítačů (nebo virtuálního počítače) clusteru, včetně informací pro každý uzel v zabezpečenému clusteru. Je tento cluster zabezpečený pomocí x509 certifikáty. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Soubor ukázky konfigurace clusteru, který obsahuje všechna nastavení pro zabezpečené, více počítačů (nebo virtuálního počítače) clusteru, včetně informací pro každý uzel v zabezpečenému clusteru. Je tento cluster zabezpečený pomocí [skupinových účtů spravované služby](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Ukázky konfigurace clusteru
Nejnovější verze šablony konfigurace clusteru můžete najít na stránku Githubu: [ukázky konfigurace samostatného clusteru](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Nezávislé balíček modulu Runtime
Nejnovější balíček modulu runtime se nestahuje automaticky při nasazování clusteru z [stáhnout odkaz – modul Runtime Service Fabric – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Související
* [Vytvoření samostatného clusteru Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Scénáře zabezpečení clusteru Service Fabric](service-fabric-windows-cluster-windows-security.md)

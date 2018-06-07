---
title: Nastavení samostatného clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Můžete vytvořit samostatný vývojový cluster se třemi uzly spuštěnými ve stejném počítači. Po dokončení tohoto nastavení budete umět sestavit cluster s více počítači.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 93c921e54c69693ed02c122a2491b0942f550126
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643460"
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>Vytvoření vašeho prvního samostatného clusteru Service Fabric
Samostatný cluster Service Fabric můžete vytvořit v každém virtuálním nebo fyzickém počítači se systémem Windows Server 2012 R2 nebo Windows Server 2016, a to místně nebo v cloudu. Tento rychlý postup vám pomůže vytvořit samostatný vývojový cluster za několik minut.  Po provedení postupu budete mít cluster se třemi uzly, který běží v jednom počítači a do kterého můžete nasazovat aplikace.

## <a name="before-you-begin"></a>Než začnete
Service Fabric nabízí instalační balíček pro vytváření samostatných clusterů Service Fabric.  [Stáhněte instalační balíček](http://go.microsoft.com/fwlink/?LinkId=730690).  Rozbalte instalační balíček do složky na počítači nebo virtuálním počítači, kde nastavujete vývojový cluster.  Obsah instalačního balíčku je podrobně popsán [zde](service-fabric-cluster-standalone-package-contents.md).

Správce clusteru, který cluster nasazuje a konfiguruje, musí mít v příslušném počítači oprávnění správce. Service Fabric nelze nainstalovat na řadič domény.

## <a name="validate-the-environment"></a>Ověření prostředí
Skript *TestConfiguration.ps1* v samostatném balíčku se používá jako analyzátor s osvědčenými postupy pro ověření, jestli je možné cluster nasadit do daného prostředí. V článku [Příprava nasazení](service-fabric-cluster-standalone-deployment-preparation.md) jsou vypsány předpoklady a požadavky prostředí. Spusťte skript a ověřte, jestli můžete vývojový cluster vytvořit:

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>Vytvoření clusteru
Několik ukázkových konfiguračních souborů clusteru se nainstaluje spolu s instalačním balíčkem. Soubor *ClusterConfig.Unsecure.DevCluster.json* představuje nejjednodušší konfiguraci clusteru: nezabezpečený cluster se třemi uzly spuštěnými v jednom počítači.  Další konfigurační soubory popisují clustery s jedním nebo více počítači zabezpečené pomocí certifikátů X.509 nebo s použitím zabezpečení systému Windows.  Nemusíte upravovat žádná výchozí nastavení konfigurace pro tento kurz, ale projděte si konfigurační soubor a seznamte se s nastavením.  Část **nodes** popisuje tři uzly v clusteru: název, IP adresa, [typ uzlu, doména selhání a upgradovací doména](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  Část **properties** definuje [zabezpečení, úroveň spolehlivosti, shromažďování diagnostických dat a typy uzlů](service-fabric-cluster-manifest.md#cluster-properties) pro cluster.

Tento cluster není zabezpečený.  Každý se může anonymně připojit a provádět operace správy, proto by produkční clustery vždy měly být zabezpečené pomocí certifikátů X.509 nebo zabezpečení systému Windows.  Zabezpečení se konfiguruje jenom při vytváření clusteru a není možné povolit zabezpečení po vytvoření clusteru.  V článku [Zabezpečení clusteru](service-fabric-cluster-security.md) najdete další informace o zabezpečení clusteru Service Fabric.  

Chcete-li vytvořit vývojový cluster se třemi uzly, spusťte skript *CreateServiceFabricCluster.ps1* z relace prostředí PowerShell správce:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Balíček běhového prostředí Service Fabric se automaticky stáhne a nainstaluje v době vytváření clusteru.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru
Vývojový cluster se třemi uzly je nyní spuštěn. Modul PowerShell ServiceFabric se instaluje spolu s modulem runtime.  Ověřit, že je cluster spuštěn, můžete ze stejného počítače nebo ze vzdáleného počítače s modulem runtime Service Fabric.  Rutina [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) vytvoří připojení ke clusteru.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
Další příklady připojení ke clusteru najdete v článku [Připojení k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md). Po připojení ke clusteru zobrazte pomocí rutiny [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) seznam uzlů v clusteru a stavové informace pro každý uzel. Položka **HealthState** by měla mít pro každý uzel hodnotu *OK*.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Vizualizace clusteru pomocí Service Fabric Exploreru
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) je nástroj vhodný pro vizualizaci clusteru a správu aplikací.  Service Fabric Explorer je služba, která běží v clusteru, kterým můžete přistupovat pomocí prohlížeče přechodem na [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer). 

Řídicí panel clusteru poskytuje přehled o clusteru včetně souhrnu stavu aplikací a uzlů. Zobrazení uzlu obsahuje fyzické rozložení clusteru. Pro daný uzel můžete zjistit, které aplikace mají v uzlu nasazený kód.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>Odebrání clusteru
Chcete-li cluster odebrat, spusťte skript *RemoveServiceFabricCluster.ps1* prostředí PowerShell ze složky balíčku a předejte mu cestu ke konfiguračnímu souboru JSON. Volitelně můžete určit umístění pro protokol odstranění.

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

Pokud chcete z počítače odebrat modul runtime Service Fabric, spusťte následující skript prostředí PowerShell ze složky balíčku.

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>Další postup
Teď, když jste nastavili samostatný vývojový cluster, zkuste následující články:
* [Nastavení samostatného clusteru s více počítači](service-fabric-cluster-creation-for-windows-server.md) a povolení zabezpečení
* [Nasazení aplikací s použitím prostředí PowerShell](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png

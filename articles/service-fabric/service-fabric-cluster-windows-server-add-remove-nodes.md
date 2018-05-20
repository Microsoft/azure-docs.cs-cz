---
title: Přidávat nebo odebírat uzly do clusteru Service Fabric samostatné | Microsoft Docs
description: Naučte se přidávat nebo odebírat uzly do clusteru Azure Service Fabric na fyzický nebo virtuální počítač se systémem Windows Server, který může být místní nebo v jakékoli cloudu.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: b1ffe3510cf4f5e73b05572a482f49d529fca60d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Přidávat nebo odebírat uzly do clusteru Service Fabric samostatné systémem Windows Server
Až budete mít [vytvořen samostatný cluster Service Fabric na počítačích systému Windows Server](service-fabric-cluster-creation-for-windows-server.md), může změnit potřeb (business) a budete muset přidávat nebo odebírat uzly do clusteru. Tento článek obsahuje podrobné pokyny k dosažení tohoto cíle. Upozorňujeme, že přidat nebo odebrat uzel funkce není podporována v clusterech místní vývoj.

## <a name="add-nodes-to-your-cluster"></a>Přidat uzly do clusteru

1. Příprava virtuálního počítače nebo počítače, který chcete přidat do clusteru pomocí následujících kroků uvedených v [plánování a příprava nasazení clusteru Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
2. Identifikovat, které domény selhání a chcete přidat tento virtuální počítač nebo počítač k doméně pro upgrade
3. Vzdálené plochy (RDP) do virtuálního počítače nebo počítač, který chcete přidat do clusteru
4. Kopírování nebo [stáhnout samostatný balíček pro prostředky infrastruktury služby pro systém Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) do virtuálního počítače nebo počítače a rozbalte balíček
5. Spusťte prostředí Powershell se zvýšenými oprávněními a přejděte do umístění rozbalené balíčku
6. Spustit *AddNode.ps1* skriptu s parametry popisující přidat nový uzel. Následující příklad přidá nový uzel s názvem VM5, s typem NodeType0 a IP adres 182.17.34.52, do UD1 a fd: / dc1/r0. *ExistingClusterConnectionEndPoint* je koncový bod připojení pro uzel již v existujícím clusteru, který může být IP adresa *žádné* uzlu v clusteru.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Po dokončení skriptu můžete zkontrolovat, zda byl přidán nový uzel spuštěním [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) rutiny.

7. K zachování konzistence napříč různými uzly v clusteru, je nutné inicializovat konfigurace upgradu. Spustit [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) získat nejnovější konfigurační soubor a přidejte uzlu nově přidané do části "Uzlů". Doporučujeme také mít vždy k dispozici v případě, že je potřeba provést redploy clusteru se stejnou konfigurací nejnovější konfigurace clusteru.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Spustit [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) spustíte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Můžete sledovat průběh upgradu v Service Fabric Exploreru. Alternativně můžete spustit [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Přidat uzly do clusterů, které jsou nakonfigurované pomocí gMSA zabezpečení systému Windows
Pro clustery nakonfigurované skupiny spravované služby Account(gMSA) (https://technet.microsoft.com/library/hh831782.aspx), lze přidat nový uzel pomocí konfigurace upgradu:
1. Spustit [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) na žádném z existujících uzlů získat nejnovější konfigurační soubor a přidat podrobnosti o nový uzel, který chcete přidat v části "Uzlů". Zajistěte, aby byl nový uzel součástí stejného účtu skupiny spravované. Tento účet by měl být správce na všech počítačích.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Spustit [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) spustíte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Můžete sledovat průběh upgradu v Service Fabric Exploreru. Alternativně můžete spustit [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Přidat typy uzlů do clusteru
Chcete-li přidat nového typu uzlu, změňte konfiguraci zahrnout nového typu uzlu v "NodeTypes" tématu v části "Vlastnosti" a začněte konfiguraci upgradovat pomocí [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Po dokončení upgradu, můžete přidat nové uzly do clusteru s tímto typem uzlu.

## <a name="remove-nodes-from-your-cluster"></a>Odebrání uzlů z clusteru
Uzel může být odebrán z clusteru pomocí konfigurace upgrade, následujícím způsobem:

1. Spustit [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) získat nejnovější konfigurační soubor a *odebrat* uzlu z části "Uzlů".
Parametr "NodesToBeRemoved" přidáte do části "Nastavení" v části "FabricSettings". "value" by měl být čárkami oddělený seznam názvů uzlu uzlů, které je třeba je odebrat.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Spustit [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) spustíte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Můžete sledovat průběh upgradu v Service Fabric Exploreru. Alternativně můžete spustit [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Odebrání uzlů může zahájit více upgradů. Některé uzly jsou označené jako `IsSeedNode=”true”` značky a lze identifikovat podle dotazování clusteru manifestu pomocí `Get-ServiceFabricClusterManifest`. Odebrání těchto uzlů může trvat déle než jiné, protože uzly počáteční hodnoty musí přesunout v těchto scénářích. Cluster musí zachovat minimálně 3 uzlů typu primárního uzlu.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Odebrat typy uzlů z clusteru
Před odebráním typ uzlu, prosím Překontrolujte, pokud jsou všechny uzly odkazující na typ uzlu. Odeberte tyto uzly před odebráním odpovídající typ uzlu. Po odebrání všech odpovídajících uzlů můžete odebrat typ uzlu z clusteru konfigurace a zahájit konfiguraci upgradovat pomocí [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Nahraďte primární uzlů ve vašem clusteru
Nahrazení primární uzly by měl být provádí jeden uzel za druhým, místo odebráním a potom přidat v dávkách.


## <a name="next-steps"></a>Další postup
* [Nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md)
* [Zabezpečení clusteru s podporou samostatné do systému Windows pomocí X509 certifikáty](service-fabric-windows-cluster-x509-security.md)
* [Vytvořit cluster Service Fabric samostatné s virtuálními počítači Azure s Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)


---
title: Přidávání a odebírání uzlů do samostatného clusteru Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak přidávat a odebírat uzly do clusteru Azure Service Fabric na fyzický nebo virtuální počítač s Windows serverem, který může být místní nebo v libovolném cloudu.
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
ms.openlocfilehash: 69680331bdad0faa36cb3df6117baf8b358da132
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251015"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Přidávání a odebírání uzlů do samostatného clusteru Service Fabric běžící na Windows serveru
Až budete mít [vytvoření samostatného clusteru Service Fabric na počítačích s Windows serverem](service-fabric-cluster-creation-for-windows-server.md), může změnit potřeb (firmy) a je potřeba přidat nebo odebrat uzly do clusteru. Tento článek obsahuje podrobné pokyny k dosažení tohoto cíle. Všimněte si, že přidání nebo odebrání v uzlu funkce není podporována v místním vývojovém clusteru.

## <a name="add-nodes-to-your-cluster"></a>Přidání uzlů do clusteru

1. Příprava virtuálního počítače nebo počítače, který chcete přidat do clusteru pomocí následujících kroků uvedených v [plánování a příprava vašeho nasazení clusteru Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
2. Identifikovat, které doména selhání a upgradovací domény, které se chystáte přidat tento virtuální počítač a počítač
3. Vzdálené plochy (RDP) do virtuálního počítače nebo počítače, který chcete přidat do clusteru
4. Kopírování nebo [stáhnout samostatného balíčku pro Service Fabric pro Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) do virtuálního počítače nebo počítače a rozbalte balíček
5. Spusťte prostředí Powershell se zvýšenými oprávněními a přejděte do umístění rozzipovaný balíčku
6. Spustit *AddNode.ps1* skript s parametry popisující, chcete-li přidat nový uzel. Následující příklad přidá nový uzel s názvem VM5, s typem NodeType0 a IP adres 182.17.34.52, do UD1 a fd: / dc1/r0. *ExistingClusterConnectionEndPoint* je koncový bod připojení pro uzel již v existujícím clusteru, což může být IP adresa *jakékoli* uzlu v clusteru.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Po dokončení spuštění skriptu můžete zkontrolovat, zda byl přidán nový uzel spuštěním [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) rutiny.

7. K zachování konzistence napříč různými uzly v clusteru, je nutné inicializovat konfigurace upgradu. Spustit [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) získat nejnovější konfigurační soubor a přidat do části "Uzly" nově přidané uzlu. Doporučuje se také vždy mít nejnovější konfiguraci clusteru, která je k dispozici v případě, že budete muset opět nasadit cluster se stejnou konfigurací.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Spustit [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) spustíte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Můžete sledovat průběh upgradu v Service Fabric Explorer. Alternativně můžete spustit [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Přidání uzlů do clusterech nakonfigurovaných s využitím gMSA zabezpečení Windows
Pro clustery nakonfigurovanou Account(gMSA) spravované služby skupiny (https://technet.microsoft.com/library/hh831782.aspx), můžete přidat nový uzel pomocí konfigurace upgradu:
1. Spustit [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) na některý z existujících uzlů získat nejnovější konfigurační soubor a přidejte podrobnosti o nový uzel, který chcete přidat v části "Uzly". Ujistěte se, že se že nový uzel je součástí stejného účtu skupiny spravované. Tento účet musí být správce na všech počítačích.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Spustit [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) spustíte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Můžete sledovat průběh upgradu v Service Fabric Explorer. Alternativně můžete spustit [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Přidat typy uzlů do clusteru
Pokud chcete přidat nový typ uzlu, upravte konfiguraci tak, aby zahrnují nového typu uzlu pod nadpisem "Properties" v "NodeTypes" a začněte konfiguraci upgrade s využitím [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Po dokončení upgradu můžete přidat nové uzly do clusteru s tímto typem uzlu.

## <a name="remove-nodes-from-your-cluster"></a>Odebrání uzlů z clusteru
Lze odebrat uzel z clusteru pomocí konfigurace upgradu, následujícím způsobem:

1. Spustit [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) zobrazíte nejnovější konfigurační soubor a *odebrat* uzlu z část "Uzly".
Přidáte parametr "NodesToBeRemoved" části "Nastavení" v části "Nastavení FabricSettings". "value" by měl být čárkami oddělený seznam názvů uzlů uzlů, které musí být odstraněny.

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
2. Spustit [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) spustíte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Můžete sledovat průběh upgradu v Service Fabric Explorer. Alternativně můžete spustit [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Odebrání uzlů může zahájit více upgradů. Některé uzly jsou označené `IsSeedNode=”true”` značku a lze je identifikovat pomocí dotazu na clusteru manifestu pomocí `Get-ServiceFabricClusterManifest`. Odebrání těchto uzlů může trvat déle než jiné, jelikož budou muset počáteční uzly přesouvat v takových scénářích. Cluster, musíte mít aspoň 3 uzly typ primárního uzlu.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Odebrat typy uzlů z clusteru
Před odebráním uzlu typu double Zkontrolujte prosím, jestli jsou všechny uzly, které odkazuje typ uzlu. Před odebráním odpovídající typ uzlu, odeberte tyto uzly. Jakmile se odeberou všechny odpovídající uzly, můžete odebrat uzlu (NodeType) z konfigurace clusteru a začněte konfiguraci upgrade s využitím [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Nahraďte hlavní uzly clusteru
Nahrazení hlavní uzly, které musí být jeden uzel provádí po druhé, namísto odeberte a pak přidejte v dávkách.


## <a name="next-steps"></a>Další postup
* [Nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md)
* [Zabezpečení samostatného clusteru ve Windows pomocí X509 certifikáty](service-fabric-windows-cluster-x509-security.md)
* [Vytvoření samostatného clusteru Service Fabric pomocí virtuálních počítačů Azure s Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)


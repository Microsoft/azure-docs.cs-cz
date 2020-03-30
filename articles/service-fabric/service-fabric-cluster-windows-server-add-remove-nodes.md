---
title: Přidání nebo odebrání uzlů do samostatného clusteru Service Fabric
description: Zjistěte, jak přidat nebo odebrat uzly do clusteru Azure Service Fabric na fyzickém nebo virtuálním počítači se systémem Windows Server, který může být místní nebo v libovolném cloudu.
author: dkkapur
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: f9bee35ee8e82070b4cf601139b471562ba5e10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934209"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Přidání nebo odebrání uzlů u samostatného clusteru Service Fabric na Windows Serveru
Po [vytvoření samostatného clusteru Service Fabric v počítačích se systémem Windows Server](service-fabric-cluster-creation-for-windows-server.md)se vaše (obchodní) potřeby mohou změnit a budete muset přidat nebo odebrat uzly do clusteru. Tento článek obsahuje podrobné kroky k dosažení tohoto cíle. Upozorňujeme, že funkce přidání nebo odebrání uzlu není v clusterech místního vývoje podporována.

## <a name="add-nodes-to-your-cluster"></a>Přidání uzlů do clusteru

1. Připravte virtuální počítač/počítač, který chcete přidat do clusteru podle kroků popsaných v [plánu a připravit nasazení clusteru Service Fabric](service-fabric-cluster-standalone-deployment-preparation.md).

2. Určete, do které domény selhání a upgradu chcete přidat tento virtuální počítač nebo počítač.

   Pokud k zabezpečení clusteru používáte certifikáty, očekává se, že certifikáty budou nainstalovány v místních úložištích certifikátů v rámci přípravy na připojení uzlu ke clusteru. Analog je použitelný při použití jiných forem zabezpečení.

3. Vzdálená plocha (RDP) do virtuálního počítače/počítače, který chcete přidat do clusteru.

4. Zkopírujte nebo [stáhněte samostatný balíček pro Service Fabric pro Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) do virtuálního počítače a rozbalte balíček.

5. Spusťte prostředí PowerShell se zvýšenými oprávněními a přejděte do umístění rozbaleného balíčku.

6. Spusťte skript *AddNode.ps1* s parametry popisujícími nový uzel, který chcete přidat. Následující příklad přidá nový uzel s názvem VM5 s typem NodeType0 a IP adres182.17.34.52 do UD1 a fd:/dc1/r0. `ExistingClusterConnectionEndPoint`je koncový bod připojení pro uzel, který je již v existujícím clusteru, což může být IP adresa *libovolného* uzlu v clusteru. 

   Nezabezpečené (prototypování):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Zabezpečené (na základě certifikátu):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Po dokončení skriptu můžete zkontrolovat, zda byl nový uzel přidán spuštěním rutiny [Get-ServiceFabricNode.](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps)

7. Chcete-li zajistit konzistenci mezi různými uzly v clusteru, je nutné zahájit upgrade konfigurace. Spusťte [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) získat nejnovější konfigurační soubor a přidat nově přidaný uzel do části "Uzly". Doporučujeme také mít vždy k dispozici nejnovější konfiguraci clusteru v případě, že potřebujete znovu nasadit cluster, který má stejnou konfiguraci.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Spusťte [spuštění nástroje Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) a zahajte upgrade.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Průběh upgradu můžete sledovat v aplikaci Service Fabric Explorer. Alternativně můžete spustit [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Přidání uzlů do clusterů nakonfigurovaných pomocí zabezpečení systému Windows pomocí gMSA
Pro clustery nakonfigurované pomocí účtu spravovanéhttps://technet.microsoft.com/library/hh831782.aspx)služby skupiny (gMSA)( lze přidat nový uzel pomocí upgradu konfigurace:
1. Spusťte [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) na libovolném existujícím uzlu, abyste získali nejnovější konfigurační soubor a přidali podrobnosti o novém uzlu, který chcete přidat v části "Uzly". Ujistěte se, že nový uzel je součástí stejného účtu spravovaného skupiny. Tento účet by měl být správcem na všech počítačích.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Spusťte [spuštění nástroje Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) a zahajte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Průběh upgradu můžete sledovat v aplikaci Service Fabric Explorer. Alternativně můžete spustit [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Přidání typů uzlů do clusteru
Chcete-li přidat nový typ uzlu, upravte konfiguraci tak, aby zahrnovala nový typ uzlu v části "NodeTypes" v části Vlastnosti, a zahajte upgrade konfigurace pomocí [programu Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Po dokončení upgradu můžete do clusteru přidat nové uzly s tímto typem uzlu.

## <a name="remove-nodes-from-your-cluster"></a>Odebrání uzlů z clusteru
Uzel lze odebrat z clusteru pomocí upgradu konfigurace následujícím způsobem:

1. Spusťte [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) získat nejnovější konfigurační soubor a *odebrat* uzel z části "Uzly".
Přidejte parametr "NodesToBeRemoved" do oddílu "Setup" v části "FabricSettings". "Hodnota" by měla být čárka oddělený seznam názvů uzlů, které je třeba odebrat.

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
2. Spusťte [spuštění nástroje Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) a zahajte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Průběh upgradu můžete sledovat v aplikaci Service Fabric Explorer. Alternativně můžete spustit [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

> [!NOTE]
> Odebrání uzlů může zahájit více inovací. Některé uzly `IsSeedNode=”true”` jsou označeny značkou a lze je `Get-ServiceFabricClusterManifest`identifikovat dotazováním na manifest clusteru pomocí . Odebrání těchto uzlů může trvat déle než ostatní, protože uzly osiva budou muset být v takových scénářích přesunuty. Cluster musí udržovat minimálně 3 uzly typu primárního uzlu.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Odebrání typů uzlů z clusteru
Před odebráním typu uzlu zkontrolujte, zda existují uzly odkazující na typ uzlu. Před odebráním odpovídajícího typu uzlu odeberte tyto uzly. Po odebrání všech odpovídajících uzlů můžete odebrat typ NodeType z konfigurace clusteru a zahájit upgrade konfigurace pomocí [programu Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Nahrazení primárních uzlů clusteru
Nahrazení primárních uzlů by mělo být provedeno jeden uzel za druhým, namísto odebrání a následného přidání v dávkách.


## <a name="next-steps"></a>Další kroky
* [Nastavení konfigurace samostatného clusteru Windows](service-fabric-cluster-manifest.md)
* [Zabezpečení samostatného clusteru v systému Windows pomocí certifikátů X509](service-fabric-windows-cluster-x509-security.md)
* [Vytvoření samostatného clusteru Service Fabric s virtuálními počítači Azure se systémem Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)


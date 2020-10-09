---
title: Přidání nebo odebrání uzlů do samostatného Service Fabric clusteru
description: Naučte se přidávat nebo odebírat uzly do clusteru Azure Service Fabric na fyzickém nebo virtuálním počítači s Windows serverem, který může být místní nebo v jakémkoli cloudu.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 3e5f32274d2263bc5bf1bbec8f1626d519f8ca3f
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842916"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Přidání nebo odebrání uzlů u samostatného clusteru Service Fabric na Windows Serveru
Po [vytvoření samostatného clusteru Service Fabric na počítačích s Windows serverem](service-fabric-cluster-creation-for-windows-server.md)se můžou vaše (firmy) změnit a budete muset přidat nebo odebrat uzly do clusteru, jak je popsáno v tomto článku.

> [!NOTE]
> Funkce přidávání a odebírání uzlů se v místních vývojových clusterech nepodporují.

## <a name="add-nodes-to-your-cluster"></a>Přidání uzlů do clusteru

1. Připravte virtuální počítač/počítač, který chcete přidat do clusteru, podle kroků uvedených v části [plánování a příprava nasazení clusteru Service Fabric](service-fabric-cluster-standalone-deployment-preparation.md).

2. Identifikujte doménu selhání a upgradovací doménu, do které budete tento virtuální počítač nebo počítač přidávat.

   Pokud používáte certifikáty k zabezpečení clusteru, očekává se, že certifikáty budou nainstalovány v místních úložištích certifikátů, které se připravují pro uzel, který se připojí ke clusteru. Analogie je k dispozici při použití jiných forem zabezpečení.

3. Vzdálená plocha (RDP) do virtuálního počítače nebo počítače, který chcete přidat do clusteru.

4. Zkopírujte nebo [Stáhněte samostatný balíček pro Service Fabric pro Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) do virtuálního počítače nebo počítače a rozbalíte balíček.

5. Spusťte PowerShell se zvýšenými oprávněními a přejdete do umístění balíčku Get.

6. Spusťte skript *AddNode.ps1* s parametry, které popisují nový uzel, který chcete přidat. Následující příklad přidá nový uzel s názvem VM5 s typem NodeType0 a IP adresa 182.17.34.52 do UD1 a FD:/DC1/R0. `ExistingClusterConnectionEndPoint` je koncový bod připojení pro uzel, který už je v existujícím clusteru, což může být IP adresa *kteréhokoli* uzlu v clusteru. 

   Nezabezpečené (vytváření prototypů):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Zabezpečení (založené na certifikátech):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Až se skript dokončí, můžete zjistit, jestli byl nový uzel přidaný spuštěním rutiny [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) .

7. Aby byla zajištěna konzistence napříč různými uzly v clusteru, je nutné zahájit upgrade konfigurace. Spuštěním příkazu [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) Získejte nejnovější konfigurační soubor a přidejte nově přidaný uzel do oddílu "Nodes". Pro případ, že je potřeba znovu nasadit cluster, který má stejnou konfiguraci, je také doporučeno mít k dispozici nejnovější konfiguraci clusteru.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Spuštěním rutiny [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) zahajte upgrade.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Průběh upgradu můžete monitorovat na Service Fabric Explorer. Alternativně můžete spustit rutinu [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Přidání uzlů do clusterů nakonfigurovaných pomocí zabezpečení systému Windows pomocí gMSA
Pro clustery nakonfigurované pomocí skupinového účtu spravované služby (gMSA) (je https://technet.microsoft.com/library/hh831782.aspx) možné přidat nový uzel pomocí upgradu konfigurace:
1. Spuštěním rutiny [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) na kterémkoli z existujících uzlů získáte nejnovější konfigurační soubor a přidejte podrobnosti o novém uzlu, který chcete přidat do oddílu uzly. Ujistěte se, že je nový uzel součástí stejného spravovaného účtu skupiny. Tento účet by měl být správce na všech počítačích.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Spuštěním rutiny [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) zahajte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Průběh upgradu můžete monitorovat na Service Fabric Explorer. Alternativně můžete spustit rutinu [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Přidání typů uzlů do clusteru
Chcete-li přidat nový typ uzlu, upravte konfiguraci tak, aby zahrnovala nový typ uzlu v části "NodeTypes" v části "Properties" a zahajte upgrade konfigurace pomocí příkazu [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Po dokončení upgradu můžete do clusteru přidat nové uzly s tímto typem uzlu.

## <a name="remove-nodes-from-your-cluster"></a>Odebrání uzlů z clusteru
Uzel se dá z clusteru odebrat pomocí upgradu konfigurace následujícím způsobem:

1. Spusťte příkaz [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) , abyste získali nejnovější konfigurační soubor a *odebrali* uzel z části Nodes.
Přidejte do oddílu "nastavení" v části "FabricSettings" parametr "NodesToBeRemoved". Hodnota by měla být čárkami oddělený seznam názvů uzlů uzlů, které je třeba odebrat.

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
2. Spuštěním rutiny [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) zahajte upgrade.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Průběh upgradu můžete monitorovat na Service Fabric Explorer. Alternativně můžete spustit rutinu [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

> [!NOTE]
> Odebrání uzlů může iniciovat více upgradů. Některé uzly jsou označeny `IsSeedNode=”true”` značkou a lze je identifikovat pomocí dotazování manifestu clusteru pomocí příkazu `Get-ServiceFabricClusterManifest` . Odebrání takových uzlů může trvat déle než jiné, protože počáteční uzly se v takových scénářích musí pohybovat. Cluster musí udržovat minimálně 3 uzly typu primární uzel.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Odebrání typů uzlů z clusteru
Před odebráním typu uzlu ověřte, zda existují uzly odkazující na typ uzlu. Odeberte tyto uzly před odebráním odpovídajícího typu uzlu. Po odebrání všech odpovídajících uzlů můžete z konfigurace clusteru odebrat uzel NodeType a zahájit upgrade konfigurace pomocí funkce [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Nahrazení primárních uzlů clusteru
Nahrazení primárních uzlů by mělo být provedeno v jednom uzlu za jiným uzlem namísto odebrání a následně přidání v dávkách.


## <a name="next-steps"></a>Další kroky
* [Nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md)
* [Zabezpečení samostatného clusteru ve Windows pomocí certifikátů x509](service-fabric-windows-cluster-x509-security.md)
* [Vytvoření samostatného Service Fabricho clusteru s virtuálními počítači Azure s Windows](./service-fabric-cluster-creation-via-arm.md)

---
title: Upgrade clusteru služby Azure Service Fabric SKU virtuální počítače nebo operačního systému | Microsoft Docs
description: Naučte se upgradovat virtuální počítače v clusteru Service Fabric primárního uzlu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: ryanwi
ms.openlocfilehash: bf707bf4b1c001b5467dd9954e9c6feb55e65654
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654998"
---
# <a name="upgrade-the-primary-node-type-vms-of-a-service-fabric-cluster"></a>Upgrade typ primárního uzlu virtuální počítače clusteru Service Fabric
Tento článek popisuje postup upgradu primárního uzlu typu virtuální počítače z clusteru Service Fabric běžící v Azure.  Cluster Service Fabric je sada virtuální nebo fyzické počítače, do kterých jsou nasazené a spravovat vaše mikroslužeb připojené k síti. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Sady škálování virtuálního počítače se Azure výpočtový prostředek, který použijete k nasazení a správě kolekci jako sada virtuálních počítačů. Každý typ uzlu, který je definován v clusteru služby Azure je [nastavený jako samostatné škálovací sadu](service-fabric-cluster-nodetypes.md). Každý typ uzlu je pak možné spravovat samostatně. Po vytvoření clusteru Service Fabric, je možné škálovat typu uzlu clusteru svisle (změnit prostředky uzlů) nebo upgradujte operační systém typu node virtuálních počítačů.  Je možné škálovat clusteru kdykoli, i když jsou úlohy běžící v clusteru.  Jako cluster škáluje, vaše aplikace se automaticky také škálování.

> [!WARNING]
> Doporučujeme, pokud se spouští ve není změnit SKU virtuálních počítačů typu sady nebo uzel škálování [stříbrnou odolnost nebo větší](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Změna velikosti virtuálních počítačů SKU je operace destruktivní dat místní infrastrukturu. Bez některé možnost zpoždění nebo sledovat tuto změnu je možné, že operaci může způsobit ztrátu dat pro stavové služby nebo způsobit další nepředpokládaného provozní problémy, i pro Bezstavová zatížení. 
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Upgrade velikost a operační systém typu primárního uzlu virtuální počítače
Tady je proces pro aktualizaci velikost virtuálního počítače a operačního systému primárního uzlu typu virtuálních počítačů.  Po upgradu primárního uzlu jsou virtuální počítače k velikost standardní D4_V2 a spuštěný Windows Server 2016 Datacenter s kontejnery.

> [!WARNING]
> Před provedením tohoto postupu na clusteru výroby, doporučujeme, abyste prostudovali ukázkových šablon a ověřit proces proti zkušební cluster. Cluster je také k dispozici po dobu.

1. Počáteční cluster se dvěma typy uzlů a dvě škálovatelné sady (jeden škálování nastavená na typ uzlu) nasadit pomocí tyto ukázkové [šablony](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) a [parametry](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) soubory.  Obě sady škálování jsou velikost standardní D2_V2 a spuštěný Windows Server 2012 R2 Datacenter.  Počkejte, než pro cluster a dokončit upgrade směrného plánu.   
2. Volitelné - nasadit ukázku stavová do clusteru.
3. Jakmile se rozhodnete k upgradu primárního uzlu typu virtuálních počítačů, přidejte nové škálování nastavené na primárním uzlu, který typ pomocí tyto ukázkové [šablony](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) a [parametry](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) soubory tak, že dvě škálovatelné sady teď má tento typ primárního uzlu.  K migraci mezi virtuální počítače ve dvou různých škálovací sady dokážou systémové služby a aplikace uživatele.  Nové sady škálování virtuálních počítačů jsou velikost standardní D4_V2 a spusťte Windows Server 2016 Datacenter s kontejnery.  Pomocí nové sady škálování také přidat nový nástroj pro vyrovnávání zatížení a veřejnou IP adresu.  
    Najít nové měřítka nastavenou v šabloně, vyhledejte "Microsoft.Compute/virtualMachineScaleSets" prostředek s názvem podle *vmNodeType2Name* parametr.  Nové sady škálování se přidá na primárním uzlu, který typ pomocí vlastnosti -> virtualMachineProfile - > extensionProfile -> Rozšíření -> Vlastnosti -> Nastavení -> Nastavení nodeTypeRef.
4. Zkontrolujte stav clusteru a ověřte, zda že jsou všechny uzly v pořádku.
5. Zakážete uzly v původní sadě škálování typu primárního uzlu se záměrem odebrat uzel. Najednou můžete zakázat a činnosti jsou zařazeny do fronty. Počkejte, dokud všechny uzly jsou zakázány, což může chvíli trvat.  Jako starší uzly v uzlu typu jsou zakázané, systému služby a počáteční hodnoty uzly migrovat k virtuálním počítačům nové měřítka, nastavte v typu primárního uzlu.
6. Odebrání sad z primárního uzlu typu starší škálování.
7. Odeberte nástroje pro vyrovnávání zatížení přidružené k původní sadě škálování. Clusteru není k dispozici, zatímco nové veřejné IP adresy a zatížení vyrovnávání jsou nakonfigurované pro nové sady škálování.  
8. Nastavení úložiště DNS veřejné IP adresy přidružené k původní primární uzel zadejte škálovací sadu do proměnné a odebrat tuto veřejnou IP adresu.
9. Nahraďte nastavení DNS veřejné IP adresy přidružené k sad s nastavení DNS odstraněné veřejnou IP adresu nového typu škálování primárního uzlu.  Cluster bude nyní znovu dosažitelné.
10. Odebrání uzlu stav uzlů z clusteru.  Pokud úroveň odolnosti původní sady škálování bylo silver nebo zlatý, tento krok je potřeba systém automaticky.
11. Pokud jste nasadili aplikace stavová v předchozím kroku, ověřte, že aplikace je funkční.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Další postup
* Další informace o [škálovatelnost aplikace](service-fabric-concepts-scalability.md).
* [Škálování clusteru služby Azure příchozí nebo odchozí](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru služby Azure prostřednictvím kódu programu](service-fabric-cluster-programmatic-scaling.md) pomocí fluent Azure výpočetní SDK.
* [Škálování clusteru standaone příchozí nebo odchozí](service-fabric-cluster-windows-server-add-remove-nodes.md).


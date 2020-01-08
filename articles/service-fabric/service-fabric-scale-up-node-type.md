---
title: Horizontální navýšení kapacity typu uzlu Azure Service Fabric
description: Naučte se škálovat Cluster Service Fabric přidáním sady škálování virtuálního počítače.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 33d535cb093eeb95e0ce95bdd5722bfd21150a40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464225"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Horizontální navýšení kapacity Service Fabric typ primárního uzlu clusteru
Tento článek popisuje, jak škálovat typ primárního uzlu clusteru Service Fabric pomocí zvýšení prostředků virtuálního počítače. Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Sady škálování virtuálních počítačů jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definovaný v clusteru Azure, je [nastavený jako samostatná sada škálování](service-fabric-cluster-nodetypes.md). Každý typ uzlu se pak dá spravovat samostatně. Po vytvoření clusteru Service Fabric můžete škálovat typ uzlu clusteru vertikálně (změnit prostředky uzlů) nebo upgradovat operační systém typu virtuálních počítačů typu uzel.  Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru.  I když se cluster škáluje, vaše aplikace se automaticky škálují.

> [!WARNING]
> Nespouštějte změnu primární jednotky SKU virtuálního počítače NodeType, pokud stav clusteru není v pořádku. Pokud stav clusteru není v pořádku, budete cluster dále rozstabilizovat jenom v případě, že se pokusíte změnit SKU virtuálního počítače.
>
> Nedoporučujeme měnit SKU virtuálního počítače pro typ nebo uzel škály, pokud není spuštěný při použití [odolnosti proti stříbru nebo většímu](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Změna velikosti SKU virtuálního počítače je místní operace infrastruktury, která je destruktivní dat. Bez možnosti zpozdit nebo sledovat tuto změnu je možné, že operace může způsobit ztrátu dat pro stavové služby nebo způsobovat jiné nepředvídatelné provozní problémy, a to i u bezstavových úloh. To znamená, že primární typ uzlu, na kterém běží stavová služba Service Fabric, nebo jakýkoli typ uzlu, na kterém je spuštěná vaše stavová zátěžová aplikace.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Upgradovat velikost a operační systém pro virtuální počítače typu primární uzel
Tady je postup aktualizace velikosti virtuálního počítače a operačního systému virtuálních počítačů typu primární uzel.  Po upgradu jsou virtuální počítače typu primární uzel standardní D4_V2 a běží na Windows serveru 2016 Datacenter s kontejnery.

> [!WARNING]
> Před pokusem o provedení této procedury v produkčním clusteru doporučujeme prostudovat ukázkové šablony a ověřit proces proti testovacímu clusteru. Cluster je také nedostupný po dobu. Nemůžete provádět změny ve více VMSS deklarovaných jako stejný typ NodeType paralelně; Chcete-li aplikovat změny na jednotlivé VMSS NodeType, budete muset provést operace odděleného nasazení.

1. Pomocí těchto ukázkových souborů [šablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) a [parametrů](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) nasaďte počáteční cluster se dvěma typy uzlů a dvěma sadami škálování (jedna sada škálování na typ uzlu).  Sady škálování mají standardní D2_V2 velikosti a běží na Windows Serveru 2012 R2 Datacenter.  Počkejte, než cluster dokončí upgrade standardních hodnot.   
2. Volitelné – nasaďte stavový vzorek do clusteru.
3. Po rozhodnutí o upgradu virtuálních počítačů typu primární uzel přidejte novou sadu škálování na typ primárního uzlu pomocí těchto ukázkových [šablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) a souborů [parametrů](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) , takže typ primárního uzlu má teď dvě sady škálování.  Systémové služby a uživatelské aplikace jsou schopné migrovat mezi virtuálními počítači ve dvou různých sadách škálování.  Nové virtuální počítače sady škálování mají velikost Standard D4_V2 a používají Windows Server 2016 Datacenter s kontejnery.  K nové sadě škálování se přidávají taky nové nástroje pro vyrovnávání zatížení a veřejná IP adresa.  
    Pokud chcete v šabloně najít novou sadu škálování, vyhledejte prostředek Microsoft. COMPUTE/virtualMachineScaleSets s názvem a parametrem *vmNodeType2Name* .  Nová sada škálování se přidá k primárnímu typu uzlu pomocí vlastnosti-> virtualMachineProfile-> extensionProfile-> rozšíření – > Properties-> Nastavení-> nodeTypeRef.
4. Zkontrolujte stav clusteru a ověřte, zda jsou všechny uzly v dobrém stavu.
5. Zakažte uzly v staré sadě škálování typu primárního uzlu s záměrem odebrat uzel. Můžete zakázat všechny najednou a operace se zařadí do fronty. Počkejte, dokud nebudou všechny uzly zakázané, což může nějakou dobu trvat.  Vzhledem k zakázání starších uzlů v typu uzlu se systémové služby a uzly počáteční verze migrují na virtuální počítače nové sady škálování v primárním uzlu.
6. Odeberte starší sadu škálování z primárního typu uzlu.
7. Odeberte Nástroj pro vyrovnávání zatížení přidružený k staré sadě škálování. Cluster není k dispozici, ale nová veřejná IP adresa a nástroj pro vyrovnávání zatížení jsou nakonfigurovány pro novou sadu škálování.  
8. Uložte nastavení DNS veřejné IP adresy přidružené k starému škálování typu primárního uzlu nastavenému v proměnné a odeberte tuto veřejnou IP adresu.
9. Nahraďte nastavení DNS veřejné IP adresy přidružené k novému primárnímu typu škálování sady s nastavením DNS odstraněné veřejné IP adresy.  Cluster je nyní dosažitelný.
10. Odeberte stav uzlu uzlů z clusteru.  Pokud byla úroveň odolnosti staré sady škálování stříbrná nebo zlatá, tento krok se provádí automaticky systémem.
11. Pokud jste v předchozím kroku nasadili stavovou aplikaci, ověřte, že je aplikace funkční.

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
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
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
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
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>Další kroky
* Naučte se, jak [Přidat typ uzlu do clusteru](virtual-machine-scale-set-scale-node-type-scale-out.md) .
* Přečtěte si o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* Horizontální navýšení nebo navýšení [kapacity clusteru Azure](service-fabric-tutorial-scale-cluster.md)
* [Škálujte cluster Azure pomocí programu](service-fabric-cluster-programmatic-scaling.md) Fluent Azure COMPUTE SDK.
* Horizontální navýšení [nebo zmenšení kapacity samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md)


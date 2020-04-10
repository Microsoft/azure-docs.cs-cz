---
title: Škálování typu uzlu Azure Service Fabric
description: Zjistěte, jak škálovat cluster Service Fabric přidáním škálovací sady virtuálních strojů.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 4dbb9e4fbfeb27c5b8b13f70207888cf37bbb0e0
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998930"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Vertikální navýšení kapacity primárního typu uzlu clusteru Service Fabric
Tento článek popisuje, jak vertikálně navýšit kapacitu typu primárního uzlu clusteru Service Fabric zvýšením prostředků virtuálního počítače. Cluster Service Fabric je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Škálovací sady virtuálních počítačů jsou výpočetní prostředek Azure, který používáte k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure, je [nastaven jako samostatná škálovací sada](service-fabric-cluster-nodetypes.md). Každý typ uzlu lze pak spravovat samostatně. Po vytvoření clusteru Service Fabric můžete škálovat typ uzlu clusteru svisle (změnit prostředky uzlů) nebo upgradovat operační systém virtuálních zařízení typu uzlu.  Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy.  Při škálování clusteru se automaticky škálují také vaše aplikace.

> [!WARNING]
> Nezačínejte měnit primární uzel SKU virtuálního virtuálního měn, pokud stav clusteru není v pořádku. Pokud stav clusteru není v pořádku, bude pouze destabilizovat clusteru dále, pokud se pokusíte změnit skladovou položku virtuálního řezu.
>
> Doporučujeme neměnit skladovou položku virtuálního měn typu škálovací sady nebo uzlu, pokud není spuštěna s [odolností stříbra nebo větší](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Změna velikosti skladové položky virtuálního počítače je operace infrastruktury na místě destruktivní pro data. Bez určité schopnosti zpoždění nebo sledování této změny je možné, že operace může způsobit ztrátu dat pro stavové služby nebo způsobit jiné nepředvídané provozní problémy, a to i pro bezstavové úlohy. To znamená, že váš primární typ uzlu, který je spuštěn stavové služby infrastruktury infrastruktury systémové služby nebo jakýkoli typ uzlu, který je spuštěn stavové aplikace pracovní zatížení.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Upgrade velikosti a operačního systému virtuálních počítače typu primárního uzlu
Tady je proces aktualizace velikosti virtuálního počítače a operačního systému primárních virtuálních uzlů typu virtuálních počítače.  Po upgradu jsou primárními virtuálními počítačemi typu uzlu velikost Standardní D4_V2 a s datovým centrem Windows Server 2016 s kontejnery.

> [!WARNING]
> Před pokusem o tento postup v produkčním clusteru doporučujeme prostudovat ukázkové šablony a ověřit proces proti testovacímu clusteru. Cluster je také k dispozici po dobu. Nelze provádět změny ve více vmss deklarované jako stejný NodeType paralelně; Budete muset provést oddělené operace nasazení, abyste mohli provádět změny u jednotlivých vsss.

1. Nasazení počátečního clusteru se dvěma typy uzlů a dvěma škálovacími sadami (jedna škálovací sada na typ uzlu) pomocí těchto ukázkových [šablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) a [souborů parametrů.](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)  Obě škálovací sady mají velikost Standardní D2_V2 a s windows serverem 2012 R2 Datacenter.  Počkejte, až cluster dokončí směrný upgrade.   
2. Volitelné - nasazení stavové ukázky do clusteru.
3. Po rozhodnutí o upgradu primárního typu uzlu virtuálních počítače, přidejte novou škálovací sadu na primární typ uzlu pomocí těchto ukázkové [šablony](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) a [parametry](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) soubory tak, aby typ primárního uzlu má nyní dvě škálovací sady.  Systémové služby a uživatelské aplikace jsou možné migrovat mezi virtuálními aplikacemi ve dvou různých škálovacích sadách.  Nové virtuální počítače škálovací sady jsou velikost standardní D4_V2 a spouštějí Windows Server 2016 Datacenter s kontejnery.  Nový systém vyrovnávání zatížení a veřejná IP adresa jsou také přidány s novou škálovací sadou.  
    Chcete-li najít novou škálovací sadu v šabloně, vyhledejte prostředek "Microsoft.Compute/virtualMachineScaleSets" pojmenovaný parametrem *vmNodeType2Name.*  Nová škálovací sada je přidána do primárního typu uzlu pomocí vlastností >virtualMachineProfile->extensions->extensions->properties->settings->nodeTypeRef.
4. Zkontrolujte stav clusteru a ověřte, zda jsou všechny uzly v pořádku.
5. Zakažte uzly ve staré škálovací sadě typu primárního uzlu s úmyslem odebrat uzel. Můžete zakázat všechny najednou a operace jsou zařazeny do fronty. Počkejte, až budou všechny uzly zakázány, což může nějakou dobu trvat.  Jako starší uzly v typu uzlu jsou zakázány, systémové služby a osiva uzly migrují do virtuálních počítačů nové škálovací sady v typu primární uzel.
6. Odeberte starší škálovací sadu z primárního typu uzlu. (Po uzly jsou zakázány jako v kroku 5, v okně škálovací sady virtuálních strojů na webu Azure Portal, navrátit uzly ze starého typu uzlu jeden po druhém.)
7. Odeberte pro vyrovnávání zatížení přidruženého ke staré škálovací sadě. Cluster není k dispozici, zatímco nová veřejná IP adresa a vyrovnávání zatížení jsou nakonfigurovány pro novou škálovací sadu.  
8. Uložte nastavení DNS veřejné IP adresy přidružené ke staré sadě typů primárních uzlů do proměnné a odeberte tuto veřejnou IP adresu.
9. Nahraďte nastavení DNS veřejné ip adresy přidružené k nové sadě měřítka typu primárního uzlu nastavením DNS odstraněné veřejné IP adresy.  Cluster je nyní opět dostupný.
10. Odeberte stav uzlu uzlů z clusteru.  Pokud byla úroveň odolnosti staré škálovací sady stříbrná nebo zlatá, systém tento krok provádí automaticky.
11. Pokud jste nasadili stavovou aplikaci v předchozím kroku, ověřte, zda je aplikace funkční.

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
* Přečtěte si, jak [do clusteru přidat typ uzlu](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Informace o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* [Škálování clusteru Azure dovnitř nebo ven](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru Azure programově](service-fabric-cluster-programmatic-scaling.md) pomocí plynulé Azure compute SDK.
* [Škálování samostatného clusteru dovnitř nebo ven](service-fabric-cluster-windows-server-add-remove-nodes.md).


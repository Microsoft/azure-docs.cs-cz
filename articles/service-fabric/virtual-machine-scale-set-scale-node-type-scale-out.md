---
title: Horizontální navýšení kapacity clusteru služby Azure Service Fabric pomocí přidání Virtual Machine Scale Sets | Dokumentace Microsoftu
description: Zjistěte, jak škálovat cluster Service Fabric, že přidáte Škálovací sady virtuálních počítačů.
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
ms.date: 08/21/2018
ms.author: ryanwi
ms.openlocfilehash: 8e1c194ea2ebc0e06918c8389c9ee6f72afb3e86
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887784"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Horizontální navýšení kapacity clusteru Service Fabric tak, že přidáte Škálovací sady virtuálních počítačů
Tento článek popisuje, jak škálování clusteru Azure Service Fabric přidáním nového virtuálního počítače škálovací sady do existujícího clusteru. Cluster Service Fabric je síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, který použijete k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure je [nastavit jako samostatné škálovací sada](service-fabric-cluster-nodetypes.md). Každý typ uzlu je pak spravovat samostatně. Po vytvoření clusteru Service Fabric, je možné škálovat typ uzlu clusteru svisle (změnit prostředky uzly), upgradujte operační systém typu uzlu virtuální počítače, nebo přidejte novou škálovací sadu virtuálních počítačů do existujícího clusteru.  Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru.  Škálování clusteru, vaše aplikace automaticky škálovat směrem také.

> [!WARNING]
> Nespouštějte změnit primární nodetype skladovou Položku virtuálního počítače, pokud je stav clusteru není v pořádku. Pokud je stav clusteru není v pořádku, bude pouze destabilizovat clusteru dál, pokud se pokusíte změnit skladovou Položku virtuálního počítače.
>
> Doporučujeme vám, pokud se spouští ve není změnit skladovou Položku virtuálního počítače škálovací sady/uzel typu [Silver odolnosti nebo větší](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Změna velikosti skladové položky virtuálních počítačů je operace destruktivní dat místní infrastrukturu. Bez některé možnosti zpoždění nebo sledovat tuto změnu je možné, že operace může způsobit ztrátu dat pro stavové služby nebo způsobit jiné nepředvídatelné provozní problémy, dokonce i u bezstavových zatížení. To znamená, že svůj typ primárního uzlu, který je spuštěn stavové služby infrastruktury služby system, nebo libovolný typ uzlu, na kterém běží práce stavové aplikace načte.
>

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Upgrade velikosti a operačního systému primárního uzlu typu virtuálních počítačů
Tady je proces pro aktualizaci velikosti virtuálního počítače a operačního systému primárního uzlu typu virtuálních počítačů.  Po upgradu primární typ uzlu virtuální počítače jsou velikosti Standard D4_V2 a spuštěný Windows Server 2016 Datacenter s kontejnery.

> [!WARNING]
> Před provedením tohoto postupu na clusteru pro produkční prostředí, doporučujeme, abyste studovat ukázkových šablon a kontrola procesu proti clusteru testu. Cluster je také k dispozici po dobu.

1. Nasadit počáteční clusteru se dvěma typy uzlů a dvě škálovací sady (jednu škálovací sadu podle typu uzlu) pomocí tyto ukázky [šablony](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) a [parametry](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) soubory.  Obě škálovací sady jsou velikost standardní D2_V2 a spuštěný Windows Server 2012 R2 Datacenter.  Počkejte, clusteru a dokončit upgrade směrného plánu.   
2. Nepovinné. nasazení ukázku stavové do clusteru.
3. Jakmile se rozhodnete upgradovat primární typ uzlu virtuální počítače, přidejte novou škálovací sadu pro primární typ uzlu pomocí tyto ukázky [šablony](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) a [parametry](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) soubory tak primární typ uzlu teď nabízí dvě škálovací sady.  Systémové služby a aplikace uživatele budou moct migrovat mezi virtuálními počítači ve dvou různých škálovací sady.  Nové škálovací sady virtuálních počítačů jsou velikosti Standard D4_V2 a spuštění systému Windows Server 2016 Datacenter s kontejnery.  Nový nástroj pro vyrovnávání zatížení a veřejnou IP adresu se také přidají nové škálovací sady.  
    Pokud chcete najít nové škálovací sady v šabloně, vyhledejte "Microsoft.Compute/virtualMachineScaleSets" prostředek s názvem podle *vmNodeType2Name* parametru.  Novou škálovací sadu se přidá do primárního uzlu typu pomocí vlastnosti -> virtualMachineProfile - > extensionProfile -> Rozšíření -> Vlastnosti -> Nastavení -> Nastavení nodeTypeRef.
4. Zkontrolujte stav clusteru a ověřte, zda že jsou všechny uzly v dobrém stavu.
5. Zakážete uzly ve škálovací sadě starého typu primárního uzlu se záměrem odebrat uzel. Můžete zakázat všechny najednou a operace jsou zařazené do fronty. Počkejte, dokud všechny uzly jsou zakázány, což může nějakou dobu trvat.  Jako starší uzly v typu uzlu jsou zakázané, systému služby a počáteční uzly migrace na virtuální počítače novou škálovací sadu v primární typ uzlu.
6. Odeberte starší škálovací sady z primární typ uzlu.
7. Odebrání nástroje pro vyrovnávání zatížení, který je přidružený k původní škálovací sady. Cluster není k dispozici, když jsou nakonfigurované novou veřejnou IP adresu a zatížení nástroje pro vyrovnávání pro novou škálovací sadu.  
8. Nastavení DNS Store veřejné IP adresy přidružené k původní primární uzel zadejte škálovací sady do proměnné a odebrat tuto veřejnou IP adresu.
9. Nahraďte nastavení DNS veřejné IP adresy přidružené k nové primární uzel typu škálovací sady pomocí nastavení DNS odstraněné veřejné IP adresy.  Cluster je teď dostupný znovu.
10. Odebrání uzlu stav uzlů z clusteru.  Pokud se úroveň odolnosti škálovací sady staré stříbrné nebo zlaté úrovně, tento krok je automaticky dokončen v systému.
11. Pokud jste nasadili stavové aplikace v předchozím kroku, ověřte, že aplikace je funkční.

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

## <a name="adding-an-additional-scale-set-to-an-existing-cluster"></a>Přidání další škálování nastavit do existujícího clusteru
Přidání nové NodeType virtuálního počítače Škálovací sady do existujícího clusteru, je podobný výše uvedené upgradu primární typ uzlu, s výjimkou nebudete používat stejné NodeTypeRef; samozřejmě nebudou zakazuje jakékoli aktivně používanými Virtual Machine Scale sets a nebudete ztratit dostupnost clusteru, pokud nelze aktualizovat primární typ uzlu. 

Vlastnost NodeTypeRef je deklarována v rámci vlastnosti rozšíření prostředků infrastruktury služby virtuálního počítače Škálovací sady služby:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Kromě toho budete muset přidat tento nový typ uzlu prostředku clusteru Service Fabric:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Další postup
* Další informace o [aplikace škálovatelnost](service-fabric-concepts-scalability.md).
* [Škálování clusteru Azure snížení nebo navýšení kapacity](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru Azure prostřednictvím kódu programu](service-fabric-cluster-programmatic-scaling.md) pomocí fluent Azure compute SDK.
* [Horizontální snížení nebo navýšení kapacity clusteru samostatná](service-fabric-cluster-windows-server-add-remove-nodes.md).


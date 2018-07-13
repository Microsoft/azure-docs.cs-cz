---
title: Škálování clusteru Service Fabric v Azure | Microsoft Docs
description: V tomto kurzu se naučíte rychle škálovat cluster Service Fabric v Azure.
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/06/2018
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 83f7a03744e7e8819d71eae81ed8e497797bef62
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109405"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Kurz: Škálování clusteru Service Fabric v Azure

Tento kurz je druhou částí série a ukazuje, jak můžete škálovat existující cluster a horizontálně navýšit nebo snížit jeho kapacitu. Po dokončení budete vědět, jak škálovat cluster a jak vyčistit všechny zbylé prostředky.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Číst počet uzlů clusteru
> * Přidat uzly clusteru (horizontálně navýšit kapacitu)
> * Odebrat uzly clusteru (horizontálně snížit kapacitu)

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Vytvoření zabezpečeného [clusteru s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [clusteru s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md) v Azure pomocí šablony
> * Horizontální snížení nebo navýšení kapacity clusteru
> * [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)
> * [Nasazení API Managementu se Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [modul Azure PowerShellu verze 4.1 nebo vyšší](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) nebo [Azure CLI 2.0](/cli/azure/install-azure-cli).
* Vytvořte zabezpečený [cluster s Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) nebo [cluster s Linuxem](service-fabric-tutorial-create-vnet-and-linux-cluster.md) v Azure.
* Pokud nasadíte cluster s Windows, nastavte vývojové prostředí ve Windows. Nainstalujte sadu [Visual Studio 2017](http://www.visualstudio.com) a sady funkcí **Vývoj pro Azure**, **Vývoj pro ASP.NET a web** a **Vývoj multiplatformních aplikací pomocí rozhraní .NET Core**.  Potom nastavte [vývojové prostředí .NET](service-fabric-get-started.md).
* Pokud nasadíte cluster s Linuxem, nastavte vývojové prostředí Java v [Linuxu](service-fabric-get-started-linux.md) nebo [MacOS](service-fabric-get-started-mac.md).  Nainstalujte [Service Fabric CLI](service-fabric-cli.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Před spouštěním příkazů Azure se přihlaste ke svému účtu Azure a vyberte své předplatné.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Pokud chcete úspěšně udělat tuto část kurzu, budete se muset připojit ke clusteru Service Fabric i ke škálovací sadě virtuálních počítačů (která je hostitelem clusteru). Škálovací sada virtuálních počítačů je prostředek Azure, který je hostitelem služby Service Fabric v Azure.

Jakmile se připojíte ke clusteru, můžete se ho dotazovat na informace. Můžete se pomocí clusteru dozvědět, jakých uzlů si je cluster vědom. Připojení ke clusteru v následujícím kódu používá stejný certifikát, který byl vytvořen v první části této série. Nezapomeňte nastavit proměnné `$endpoint` a `$thumbprint` na svoje hodnoty.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Teď, když jste připojení, můžete pomocí příkazu získat stav každého uzlu v clusteru. Pro **PowerShell** použijte příkaz `Get-ServiceFabricClusterHealth` a pro **sfctl** použijte příkaz `sfctl cluster select`.

## <a name="scale-out"></a>Horizontální navýšení kapacity

Při horizontálním navyšování kapacity přidáte do škálovací sady více instancí virtuálních počítačů. Tyto instance se stanou uzly, které bude Service Fabric používat. Service Fabric to pozná, když se do škálovací sady přidají další instance (díky horizontálnímu navýšení kapacity), a automaticky zareaguje. Následující kód načte škálovací sadu podle názvu a zvýší její **kapacitu** o 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Tento kód nastaví kapacitu na 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>Horizontální snížení kapacity

Horizontální snížení kapacity je stejné jako horizontální zvýšení kapacity. Jediný rozdíl je, že použijete nižší hodnotu **kapacity**. Když horizontálně snížíte kapacitu škálovací sady, odeberete instance virtuálních počítačů ze škálovací sady. Za normálních okolností Service Fabric nepozná, co se stalo, a domnívá se, že se uzel ztratil. Service Fabric pak hlásí, že stav clusteru není v pořádku. Pokud chcete tomuto nežádoucímu stavu zabránit, musíte Service Fabric informovat, že očekáváte, že uzel zmizí.

### <a name="remove-the-service-fabric-node"></a>Odebrání uzlu Service Fabric

> [!NOTE]
> Tato část se vztahuje jenom na *bronzovou* úroveň odolnosti. Další informace o odolnosti najdete v článku [Plánování kapacity clusteru Service Fabric][durability].

Když horizontálně snížíte kapacitu škálovací sady virtuálních počítačů, sada (ve většině případů) odebere instanci virtuálního počítače, který byl vytvořen naposledy. Proto musíte najít odpovídající, naposledy vytvořený uzel Service Fabric. Tento poslední uzel můžete najít kontrolou největší hodnoty vlastnosti `NodeInstanceId` na uzlech Service Fabric. Příklady kódu níže jsou seřazené podle instance uzlu a vrátí podrobnosti o instanci s největší hodnotou ID.

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Cluster Service Fabric potřebuje vědět, že tento uzel se odebere. Musíte provést tři kroky:

1. Zakázat uzel, aby už nefungoval jako replika pro data.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Zastavit uzel tak, aby se řádně ukončil provoz Service Fabricu a vaše aplikace obdržela žádost o ukončení.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Odebrat uzel z clusteru.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Jakmile na uzel aplikujete tyto tři kroky, může se odebrat ze škálovací sady. Pokud používáte nějakou jinou úroveň odolnosti než [bronzovou][durability], tyto kroky se provedou za vás při odebrání instance škálovací sady.

Následující blok kódu získá poslední vytvořený uzel a zakáže ho, zastaví a odebere z clusteru.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

V kódu **sfctl** níže slouží následující příkaz k získání hodnoty **node-name** posledního vytvořeného uzlu: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Pomocí následujících dotazů **sfctl** můžete zkontrolovat stav každého kroku.
>
> **Kontrola stavu deaktivace**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Kontrola stavu zastavení**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Horizontální snížení kapacity škálovací sady

Teď, když je uzel Service Fabric odebraný z clusteru, je možné horizontálně snížit kapacitu škálovací sady virtuálních počítačů. V následujícím příkladu je kapacita škálovací sady snížená o 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Tento kód nastaví kapacitu na 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Číst počet uzlů clusteru
> * Přidat uzly clusteru (horizontálně navýšit kapacitu)
> * Odebrat uzly clusteru (horizontálně snížit kapacitu)

Dále se v následujícím kurzu dozvíte, jak upgradovat modul runtime clusteru.
> [!div class="nextstepaction"]
> [Upgrade modulu runtime clusteru](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster

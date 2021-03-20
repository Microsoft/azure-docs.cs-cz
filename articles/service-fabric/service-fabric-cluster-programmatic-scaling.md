---
title: Programové škálování služby Azure Service Fabric
description: Horizontální navýšení nebo navýšení kapacity clusteru Azure Service Fabric v závislosti na vlastních triggerech
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f39bd874c1f5a1be42ca1c88e6ea2fe8df22f87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94648204"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Škálování Service Fabric clusteru prostřednictvím kódu programu 

Clustery Service Fabric spuštěné v Azure jsou postavené na službě Virtual Machine Scale Sets.  [Škálování clusteru](./service-fabric-cluster-scale-in-out.md) popisuje, jak lze Service Fabric clustery škálovat ručně nebo pomocí pravidel automatického škálování. Tento článek popisuje, jak spravovat přihlašovací údaje a škálovat cluster s využitím sady Azure COMPUTE SDK Fluent, což je pokročilejší scénář. Přehled najdete v tématu [programové metody pro koordinaci operací škálování Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Správa přihlašovacích údajů
Jednou z možností, jak napsat službu pro zpracování škálování, je, že služba musí mít přístup k prostředkům sady škálování virtuálních počítačů bez interaktivního přihlášení. Přístup ke clusteru Service Fabric je snadno, pokud služba škálování mění svoji vlastní Service Fabric aplikaci, ale pro přístup k sadě škálování je potřeba přihlašovací údaje. Pokud se chcete přihlásit, můžete použít [instanční objekt](/cli/azure/create-an-azure-service-principal-azure-cli) vytvořený pomocí [Azure CLI](https://github.com/azure/azure-cli).

Instanční objekt se dá vytvořit pomocí následujících kroků:

1. Přihlaste se k Azure CLI ( `az login` ) jako uživatel s přístupem k sadě škálování virtuálního počítače.
2. Vytvoření instančního objektu pomocí služby `az ad sp create-for-rbac`
    1. Poznamenejte si appId (označované jako "ID klienta" jinde), jméno, heslo a tenant pro pozdější použití.
    2. Budete také potřebovat ID vašeho předplatného, které se dá zobrazit s `az account list`

COMPUTE COMPUTE Library se může přihlásit pomocí těchto přihlašovacích údajů takto (Všimněte si, že základní typy technologie Azure, jako `IAzure` jsou v balíčku [Microsoft. Azure. Management. Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) ):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Po přihlášení se počet instancí sady škálování dá dotazovat prostřednictvím `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity` .

## <a name="scaling-out"></a>Horizontální navýšení kapacity
Pomocí sady Azure COMPUTE SDK Fluent můžete instance přidat do sady škálování virtuálního počítače jenom s několika voláními –

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternativně je možné spravovat i velikost sady škálování virtuálních počítačů pomocí rutin PowerShellu. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) může načíst objekt sady škálování virtuálního počítače. Aktuální kapacita je k dispozici prostřednictvím `.sku.capacity` Vlastnosti. Po změně kapacity na požadovanou hodnotu můžete pomocí příkazu aktualizovat sadu škálování virtuálního počítače v Azure [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) .

Stejně jako při ručním přidávání uzlu by se měla přidat instance sady škálování, která je potřeba ke spuštění nového uzlu Service Fabric, protože šablona sady škálování obsahuje rozšíření, která automaticky připojovat nové instance do clusteru Service Fabric. 

## <a name="scaling-in"></a>Škálování v

Škálování v nástroji je podobné jako horizontální navýšení kapacity. Skutečné změny sady škálování virtuálního počítače jsou prakticky stejné. Ale jak bylo popsáno dříve, Service Fabric pouze automaticky vyčistí odebrané uzly se odolností Gold nebo stříbrného. V případě nenáročného škálování v případě je proto nutné pracovat s clusterem Service Fabric, aby bylo možné vypnout uzel, který chcete odebrat, a poté odebrat jeho stav.

Příprava uzlu pro vypnutí zahrnuje vyhledání uzlu, který se má odebrat (naposledy přidané instance sady škálování virtuálního počítače), a její deaktivace. Instance sady škálování virtuálních počítačů jsou očíslovány v pořadí, v jakém jsou přidány, takže novější uzly lze nalézt porovnáním přípony čísla v názvech uzlů (které odpovídají základním názvům instancí sady škálování virtuálních počítačů). 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Jakmile bude uzel, který se má odebrat, nalezen, může být deaktivován a odebrán pomocí stejné `FabricClient` instance a `IAzure` instance z dřívější.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Stejně jako u horizontálního navýšení kapacity se dá rutiny PowerShellu pro úpravu kapacity sady škálování virtuálních počítačů použít taky v případě, že je vhodnější přístup k skriptování. Po odebrání instance virtuálního počítače je možné odebrat Service Fabric stav uzlu.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s implementací vlastní logiky automatického škálování, Seznamte se s následujícími koncepty a užitečnými rozhraními API:

- [Ruční škálování nebo pravidla automatického škálování](./service-fabric-cluster-scale-in-out.md)
- [Knihovny pro správu Azure pro .NET](https://github.com/Azure/azure-libraries-for-net) (užitečné pro interakci s podkladovou sadou virtuálních počítačů Service Fabricového clusteru)
- [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient) (užitečné pro interakci s clusterem Service Fabric a jeho uzly)

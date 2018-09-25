---
title: Azure Service Fabric prostřednictvím kódu programu škálování | Dokumentace Microsoftu
description: Škálování clusteru Azure Service Fabric nebo prostřednictvím kódu programu, podle vlastních aktivačních procedur
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ff02f79321823e42c25897e9de30dfbb6fac46b0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949606"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Škálování clusteru Service Fabric prostřednictvím kódu programu 

Clustery Service Fabric běžící v Azure jsou postavené na škálovací sady virtuálních počítačů.  [Škálování clusterů](./service-fabric-cluster-scale-up-down.md) popisuje, jak clustery Service Fabric je možné škálovat ručně nebo pomocí pravidel automatického škálování. Tento článek popisuje, jak spravovat přihlašovací údaje a škálovat cluster v nebo navýšení kapacity pomocí fluent Azure compute SDK, která je pokročilejších scénářích. Přehled najdete v článku [programové metody koordinovat operace škálování Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 

## <a name="manage-credentials"></a>Správa přihlašovacích údajů
Problémy může vyvolávat zápisu služby pro zpracování škálování je, že služba musí mít přístup k prostředků škálovací sady virtuálních počítačů bez interaktivní přihlášení. Přístup ke clusteru Service Fabric je jednoduché, pokud škálování služby upravuje své vlastní aplikace Service Fabric, ale přihlašovací údaje jsou potřeba pro přístup ke škálovací sadě. K přihlášení, můžete použít [instanční objekt služby](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) vytvořené pomocí [rozhraní příkazového řádku Azure](https://github.com/azure/azure-cli).

Instanční objekt služby se dají vytvářet pomocí následujících kroků:

1. Přihlaste se k rozhraní příkazového řádku Azure (`az login`) nastavit jako uživatel s přístupem ke škálování virtuálního počítače
2. Vytvoření instančního objektu pomocí služby `az ad sp create-for-rbac`
    1. Poznamenejte si ID aplikace (označované jako "ID klienta" jinde), jméno, heslo a tenanta pro pozdější použití.
    2. Budete také potřebovat své ID předplatného, které se dají zobrazit `az account list`

Knihovny fluent výpočetní přihlásit pomocí těchto přihlašovacích údajů takto (Všimněte si, že základní typy fluent Azure jako `IAzure` v [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) balíčku):

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

Po přihlášení, počet instancí škálovací sada může být dotázán pomocí `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Horizontální navýšení kapacity
Pomocí fluent Azure compute sady SDK, instance mohou být přidány do škálovací sady s několika volání - virtuálních počítačů

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Velikost virtuálního počítače škálovací sady je možné případně také spravovat pomocí rutin prostředí PowerShell. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) můžete načíst objekt virtuálního počítače škálovací sady. Aktuální kapacita bude k dispozici prostřednictvím `.sku.capacity` vlastnost. Po změně kapacitu na požadovanou hodnotu, virtuálního počítače škálovací sady v Azure se dají aktualizovat [ `Update-AzureRmVmss` ](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) příkazu.

Jako při přidávání uzlu ručně, přidání škálovací sady instance by měl být vše, co potřebujete ke spuštění nový uzel Service Fabric, protože škálovací sada šablony zahrnuje rozšíření automaticky připojí k nové instance do clusteru Service Fabric. 

## <a name="scaling-in"></a>Škálování v

Změna měřítka ve je podobný horizontální navýšení kapacity. Skutečné virtuálního počítače škálovací sady změn jsou prakticky stejné. Ale jak bylo popsáno dříve, Service Fabric automaticky vyčistí odebrání uzlů se stálostí Gold a Silver. Proto v bronzová odolnosti škálování – v případě, je nutné interakci s clusterem Service Fabric pro vypnutí uzel, který má být odebrána a pak odeberte svůj stav.

Příprava uzlu pro vypnutí zahrnuje hledání uzel, který má být odebrána (naposledy přidanou virtuálního počítače instance ve škálovací sadě) a její deaktivace ho. Instance škálovací sady virtuálních počítačů jsou číslovány v pořadí, ve kterém jsou přidány, takže novější uzlů najdete porovnáním číselnou příponu v názvech uzlů (které shodu základní škálovací sady virtuálních počítačů názvy instancí). 

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

Po nalezení uzel, který má být odebrána, lze deaktivovat a odebrat pomocí stejných `FabricClient` instance a `IAzure` instanci z dříve.

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

Jako s horizontálním navýšením kapacity rutin Powershellu pro úpravu virtuálního počítače škálovací sady kapacitou lze také zde Pokud skriptovací přístup je vhodnější. Po odebrání instance virtuálního počítače je možné odebrat stav uzlu Service Fabric.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Další postup

Abyste mohli začít, implementace vlastní logiky automatické škálování, seznamte se s následující pojmy a užitečné rozhraní API:

- [Škálování ručně nebo pomocí pravidel automatického škálování](./service-fabric-cluster-scale-up-down.md)
- [Fluent knihovny správy Azure pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (režim vhodný pro interakci s cluster Service Fabric základní škálovací sady virtuálních počítačů)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (režim vhodný pro interakci s clusterem Service Fabric a jeho uzly)

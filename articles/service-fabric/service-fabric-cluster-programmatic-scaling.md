---
title: Azure Service Fabric programový škálování | Microsoft Docs
description: Škálování clusteru služby Azure Service Fabric příchozí nebo odchozí prostřednictvím kódu programu, podle vlastní aktivační události
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
ms.openlocfilehash: dcf4721012fb8ec39bcd1de02c294747357b3539
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Škálování clusteru Service Fabric prostřednictvím kódu programu 

Service Fabric clustery spuštěná v Azure jsou postavená na sady škálování virtuálního počítače.  [Škálování clusterů](./service-fabric-cluster-scale-up-down.md) popisuje, jak je možné rozšířit clusterů Service Fabric buď ručně, nebo pomocí pravidel automatického škálování. Tento článek popisuje, jak spravovat přihlašovací údaje a škálování clusteru v nebo pomocí fluent Azure výpočetní SDK, která je pokročilejší scénáře. Přehled najdete v tématu [programové metody spolupráci Azure škálování operations](service-fabric-cluster-scaling.md#programmatic-scaling). 

## <a name="manage-credentials"></a>Správa přihlašovacích údajů
Jeden výzvy zápisu služby pro zpracování škálování je, že je služba mít přístup k prostředkům sady škálování virtuálního počítače bez interaktivního přihlášení. Přístup ke clusteru Service Fabric je snadné, pokud škálování služby upravuje vlastní aplikace Service Fabric, ale jsou přihlašovací údaje potřebné pro přístup k sadě škálování. K přihlášení, můžete použít [instanční objekt](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) vytvořené pomocí [Azure CLI 2.0](https://github.com/azure/azure-cli).

Hlavní název služby může být vytvořen pomocí následujících kroků:

1. Přihlaste se k Azure CLI (`az login`) nastavit jako uživatel s přístupem k škálování virtuálních počítačů
2. Vytvoření objektu zabezpečení pomocí služby `az ad sp create-for-rbac`
    1. Poznamenejte si appId (nazývané "ID klienta, jinde), jméno, heslo a klienta pro pozdější použití.
    2. Budete také potřebovat vaše ID odběru, který lze zobrazit pomocí `az account list`

Knihovna fluent výpočetní umožní přihlásit se pomocí těchto přihlašovacích údajů takto (Všimněte si, že základní fluent Azure typy jako `IAzure` v [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) balíčku):

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

Po přihlášení, počet instancí sady škálování může být dotazován prostřednictvím `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Horizontální navýšení kapacity
Použití fluent Azure výpočetní SDK, instancí mohou být přidány do sad s několika volání - škálování virtuálního počítače

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternativně velikost sady škálování virtuálního počítače můžete také spravovat pomocí rutin prostředí PowerShell. [`Get-AzureRmVmss`](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) můžete načíst objekt sady škálování virtuálního počítače. Aktuální kapacita je k dispozici prostřednictvím `.sku.capacity` vlastnost. Po změně kapacitu na požadovanou hodnotu, můžete aktualizovat v Azure sad škálování virtuálního počítače [ `Update-AzureRmVmss` ](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) příkaz.

Jako při přidávání uzlu ručně, musí být přidání nastavit instance škále všechno, co je potřeba spustit nový uzel Service Fabric od měřítka nastavení šablony zahrnuje rozšíření automaticky propojit nové instance služby cluster Service Fabric. 

## <a name="scaling-in"></a>Nastavení velikosti v

Změna velikosti v je podobná škálování. Skutečné škálovací sady virtuálních počítačů změny jsou téměř stejné. Ale, jak bylo popsáno dříve, Service Fabric pouze automaticky vyčistí odebrané uzly se stálostí zlatý nebo Silver. Ano, v bronzová odolnost škálování v případě, je nutné k interakci se cluster Service Fabric vypnutí uzlu, který má být odebrána a pak odeberte jeho stav.

Příprava uzlu pro vypnutí zahrnuje hledání uzlu, který má být odebrána (nedávno přidané instanci virtuálního počítače škálování sada) a její deaktivace ho. Instance sady škálování virtuálních počítačů jsou číslované v pořadí, ve kterém jsou přidány, takže novější uzlů najdete tak, že porovnáte číslem přípony v názvech uzlů (které shodu základní škálovací sady virtuálních počítačů názvy instancí). 

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

Jakmile je nalezen uzel, který má být odebrána, můžete deaktivovat a odebrat pomocí stejné `FabricClient` instance a `IAzure` instance z dříve.

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

Jako s škálování, rutiny prostředí PowerShell pro úpravy škálování virtuálních počítačů sady kapacity lze také zde Pokud skriptování přístup je vhodnější. Odebraný instanci virtuálního počítače stav uzlu Service Fabric se může odebrat.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Další postup

Abyste mohli začít implementace vlastní logiky automatické škálování, seznamte se s následující koncepty a užitečné rozhraní API:

- [Škálování ručně nebo pomocí pravidel automatického škálování](./service-fabric-cluster-scale-up-down.md)
- [Fluent správy knihovny Azure pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (je to užitečné pro interakci s cluster Service Fabric základní sady škálování virtuálního počítače)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (je to užitečné pro interakci s cluster Service Fabric a jeho uzly)

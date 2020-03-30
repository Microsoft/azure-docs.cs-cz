---
title: Programové škálování Azure Service Fabric
description: Škálování clusteru Azure Service Fabric v nebo zprogramovat programově podle vlastních aktivačních událostí
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ffe07960c6d32bea8ec31b1fe8248b6abc2b63af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458286"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Programové škálování clusteru Service Fabric 

Clustery Service Fabric spuštěné v Azure jsou postavené na škálovacích sadách virtuálních strojů.  [Škálování clusteru](./service-fabric-cluster-scale-up-down.md) popisuje, jak lze clustery Service Fabric škálovat ručně nebo pomocí pravidel automatického škálování. Tento článek popisuje, jak spravovat přihlašovací údaje a škálovat clusteru dovnitř nebo ven pomocí plynulé azure výpočetní sdk, což je pokročilejší scénář. Přehled najdete [v programových metodách koordinace operací škálování Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Správa přihlašovacích údajů
Jednou z výzev psaní služby pro zpracování škálování je, že služba musí mít přístup k prostředkům škálovací sady virtuálních strojů bez interaktivního přihlášení. Přístup ke clusteru Service Fabric je snadný, pokud služba škálování upravuje vlastní aplikaci Service Fabric, ale pro přístup k škálovací sadě jsou potřebná pověření. Chcete-li se přihlásit, můžete použít [instanční objekt](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) vytvořený pomocí [azure CLI](https://github.com/azure/azure-cli).

Instanční objekt lze vytvořit pomocí následujících kroků:

1. Přihlaste se k`az login`rozhraní příkazového příkazu Koni Azure ( ) jako uživatel s přístupem k škálovací sadě virtuálních strojů
2. Vytvoření instančního objektu pomocí`az ad sp create-for-rbac`
    1. Poznamenejte si appId (tzv. id klienta jinde), jméno, heslo a tenanta pro pozdější použití.
    2. Budete také potřebovat ID předplatného, které si můžete prohlédnout pomocí`az account list`

Plynulá výpočetní knihovna se může přihlásit pomocí těchto přihlašovacích `IAzure` údajů následujícím způsobem (všimněte si, že základní plynulé typy Azure, jako jsou v balíčku [Microsoft.Azure.Management.Fluent):](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/)

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

Po přihlášení lze počet instancí škálovací `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`sady dotazovat prostřednictvím .

## <a name="scaling-out"></a>Horizontální navýšení kapacity
Pomocí plynulé azure compute SDK, instance lze přidat do škálovací sady virtuálních strojů s několika voláními -

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Alternativně velikost škálovací sady virtuálních strojů lze také spravovat pomocí rutin prostředí PowerShell. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss)můžete načíst objekt škálovací sady virtuálního počítače. Aktuální kapacita je k `.sku.capacity` dispozici prostřednictvím ubytování. Po změně kapacity na požadovanou hodnotu lze pomocí příkazu [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) aktualizovat škálovací sadu virtuálních strojů v Azure.

Stejně jako při ručním přidávání uzlu by přidání instance škálovací sady mělo být vše, co je potřeba ke spuštění nového uzlu Service Fabric, protože šablona škálovací sady obsahuje rozšíření pro automatické připojení nových instancí do clusteru Service Fabric. 

## <a name="scaling-in"></a>Změna velikosti

Změna velikosti je podobná horizontálnínavýšení kapacity. Skutečné změny škálovací sady virtuálních strojů jsou prakticky stejné. Ale, jak bylo popsáno dříve, Service Fabric pouze automaticky vyčistí odstraněné uzly s trvanlivostí zlata nebo stříbra. Takže v případě bronzové trvanlivosti v případě, že je nutné komunikovat s clusteru Service Fabric vypnout uzel, který má být odebrán a potom odebrat jeho stav.

Příprava uzlu pro vypnutí zahrnuje nalezení uzlu, který má být odebrán (naposledy přidaná instance škálovací sady virtuálního počítače) a jeho deaktivaci. Instance škálovací sady virtuálních počítačů jsou číslovány v pořadí, v jakém jsou přidány, takže novější uzly lze najít porovnáním přípony čísel v názvech uzlů (které odpovídají názvům základních sad virtuálních počítačů). 

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

Jakmile je uzel, který má být odebrán, nalezen, může `FabricClient` být `IAzure` deaktivován a odebrán pomocí stejné instance a instance z dřívějších.

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

Stejně jako při horizontálním navýšení kapacity, rutiny prostředí PowerShell pro úpravu kapacity škálovací sady virtuálních strojů lze také použít zde, pokud je vhodnější skriptovací přístup. Po odebrání instance virtuálního počítače lze odebrat stav uzlu Service Fabric.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Další kroky

Chcete-li začít implementovat vlastní logiku automatického škálování, seznamte se s následujícími koncepty a užitečnými řešeními API:

- [Změna velikosti ručně nebo pomocí pravidel automatického škálování](./service-fabric-cluster-scale-up-down.md)
- [Knihovny fluent Azure Management librarie pro rozhraní .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (užitečné pro interakci se základními škálovacími sadami virtuálních strojů clusteru Service Fabric)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (užitečné pro interakci s clusteru Service Fabric a jeho uzlů)

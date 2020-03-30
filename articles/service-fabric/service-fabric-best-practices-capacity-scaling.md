---
title: Plánování kapacity a škálování pro Azure Service Fabric
description: Doporučené postupy pro plánování a škálování clusterů a aplikací Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: bf228e17ca24df9833f96f0c6fd3ef232cdf7ae6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258990"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Plánování kapacity a škálování pro Azure Service Fabric

Před vytvořením clusteru Azure Service Fabric nebo škálování výpočetních prostředků, které hostují váš cluster, je důležité plánovat kapacitu. Další informace o plánování kapacity naleznete [v tématu Plánování kapacity clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Další pokyny pro osvědčené postupy pro škálovatelnost clusteru naleznete v [tématu Aspekty škálovatelnosti service fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Kromě zvažování typu uzlu a charakteristikclusteru byste měli očekávat, že operace škálování budou trvat déle než hodinu pro produkční prostředí. Tato úvaha platí bez ohledu na počet virtuálních stránek, které přidáváte.

## <a name="autoscaling"></a>Automatické škálování
Operace škálování byste měli provádět pomocí šablon Azure Resource Manager, protože je osvědčeným postupem považovat [konfigurace prostředků za kód]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

Pomocí automatického škálování pomocí škálovacích sad virtuálních strojů bude vaše šablona Správce prostředků s verzí nepřesně definovat počty instancí pro škálovací sady virtuálních strojů. Nepřesná definice zvyšuje riziko, že budoucí nasazení způsobí nezamýšlené operace škálování. Obecně byste měli použít automatické škálování, pokud:

* Nasazení šablon Správce prostředků s příslušnou deklarovanou kapacitou nepodporuje váš případ použití.
     
   Kromě ručního škálování můžete nakonfigurovat [kanál průběžné integrace a doručování ve službách Azure DevOps pomocí projektů nasazení skupiny prostředků Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Tento kanál se obvykle aktivuje aplikace logiky, která používá metriky výkonu virtuálního počítače dotazován z [rozhraní API Azure Monitor REST](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). Kanál efektivně automaticky škáluje na základě jakékoli metriky, které chcete, při optimalizaci pro šablony Resource Manager.
* Musíte horizontálně škálovat pouze jeden uzel škálovací sady virtuálních strojů najednou.
   
   Chcete-li horizontální navýšení kapacity o tři nebo více uzlů najednou, měli byste [horizontální navýšení kapacity clusteru Service Fabric přidáním škálovací sady virtuálního počítače](virtual-machine-scale-set-scale-node-type-scale-out.md). Je nejbezpečnější škálovat a škálovat škálovací sady virtuálních strojů vodorovně, jeden uzel najednou.
* Máte stříbrnou spolehlivost nebo vyšší pro cluster Service Fabric a odolnost silver nebo vyšší v libovolném měřítku, kde nakonfigurujete pravidla automatického škálování.
  
   Minimální kapacita pro pravidla automatického škálování musí být rovna nebo větší než pět instancí virtuálních strojů. Musí být také rovno nebo větší než minimální úroveň spolehlivosti pro váš primární typ uzlu.

> [!NOTE]
> Service Fabric stavové služby fabric:/System/InfastructureService/<NODE_TYPE_NAME> běží na každém typu uzlu, který má silver nebo vyšší trvanlivost. Je to jediná systémová služba, která je podporovaná ke spuštění v Azure na libovolném typu uzlu clusterů.

## <a name="vertical-scaling-considerations"></a>Důležité informace o vertikálním měřítku

[Vertikální škálování](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) typu uzlu v Azure Service Fabric vyžaduje řadu kroků a aspekty. Například:

* Cluster musí být v pořádku před škálování. V opačném případě budete destabilizovat clusteru dále.
* Silver úroveň odolnosti nebo vyšší je vyžadována pro všechny typy uzlů clusteru Service Fabric, které hostují stavové služby.

> [!NOTE]
> Primární typ uzlu, který hostuje stavové služby Service Fabric systémových služeb musí být úroveň odolnosti silver nebo vyšší. Po povolení odolnosti silver, operace clusteru, jako je například upgrady, přidání nebo odebrání uzlů a tak dále bude pomalejší, protože systém optimalizuje pro bezpečnost dat přes rychlost operací.

Vertikální škálování škálovací sady virtuálních strojů je destruktivní operace. Místo toho horizontálně škálovat clusteru přidáním nové škálovací sady s požadovanou skladovou položkou. Potom migrujte služby na požadovanou skladovou položku a dokončete bezpečnou operaci vertikálního škálování. Změna prostředků soupoložky škálovací sady virtuálních strojů je destruktivní operace, protože reimages hostitele, který odebere všechny místně trvalé stavu.

Cluster používá [vlastnosti uzlu](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) Service Fabric a omezení umístění k rozhodnutí, kde chcete hostovat služby aplikace. Když vertikálně změníte velikost primárního typu uzlu, deklarujte identní hodnoty vlastností pro aplikaci `"nodeTypeRef"`. Tyto hodnoty najdete v rozšíření Service Fabric pro škálovací sady virtuálních strojů. 

Následující úryvek šablony Správce prostředků zobrazuje vlastnosti, které deklarujete. Má stejnou hodnotu pro nově zřízené škálovací sady, které máte škálování na a je podporována pouze jako dočasné stavové služby pro váš cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Nenechávejte cluster spuštěný s více škálovacími sadami, které používají stejnou `nodeTypeRef` hodnotu vlastnosti déle, než je nutné k dokončení úspěšné operace vertikálního škálování.
>
> Před pokusem o změny v provozním prostředí vždy ověřte operace v testovacích prostředích. Ve výchozím nastavení mají systémové služby clusteru Service Fabric omezení umístění pouze pro cílový typ primárního uzlu.

S vlastnostmi uzlu a omezení umístění deklarované, provést následující kroky jednu instanci virtuálního počítače najednou. To umožňuje systémové služby (a stavové služby) řádně vypnout na instanci virtuálního počítače, kterou odebíráte jako nové repliky jsou vytvořeny jinde.

1. Z Prostředí PowerShell `Disable-ServiceFabricNode` `RemoveNode` spusťte s úmyslem zakázat uzel, který chcete odebrat. Odeberte typ uzlu, který má nejvyšší číslo. Například pokud máte cluster se šesti uzl, odeberte instanci virtuálního počítače "MyNodeType_5".
2. Spusťte `Get-ServiceFabricNode` a ujistěte se, že uzel byl převeden na zakázáno. Pokud ne, počkejte, dokud není uzel zakázán. To může trvat několik hodin pro každý uzel. Nepokračujte, dokud uzel nebude převeden na deaktivován.
3. Snižte počet virtuálních discích o jednu v tomto typu uzlu. Nejvyšší instance virtuálního virtuálního byl nyní odebrán.
4. Opakujte kroky 1 až 3 podle potřeby, ale nikdy vertikálně snížit počet instancí v primárních typů uzlů menší, než co vyžaduje úroveň spolehlivosti. Seznam doporučených instancí najdete [v tématu Plánování kapacity clusteru Service Fabric.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)
5. Po ukončení všech virtuálních zařízení (reprezentované jako "Dolů") se stav fabric:/System/InfrastructureService/[název uzlu] zobrazí chybový stav. Potom můžete aktualizovat prostředek clusteru odebrat typ uzlu. Můžete použít nasazení šablony ARM nebo upravit prostředek clusteru prostřednictvím [Správce prostředků Azure](https://resources.azure.com). Tím se spustí upgrade clusteru, který odebere službu fabric:/System/InfrastructureService/[typ uzlu], která je v chybovém stavu.
 6. Poté můžete volitelně odstranit VMScaleSet, budete stále vidět uzly jako "Dolů" z zobrazení Service Fabric Explorer ačkoli. Posledním krokem by bylo vyčistit `Remove-ServiceFabricNodeState` je pomocí velení.

## <a name="horizontal-scaling"></a>Horizontální škálování

Horizontální škálování můžete provést [ručně](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) nebo [programově](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Pokud změníte měřítko typu uzlu, který má odolnost silver nebo gold, škálování bude pomalé.

### <a name="scaling-out"></a>Horizontální navýšení kapacity

Horizontální navýšení kapacity clusteru Service Fabric zvýšením počtu instancí pro konkrétní škálovací sadu virtuálních strojů. Můžete horizontální navýšení kapacity `AzureClient` programově pomocí a ID pro požadovanou škálovací sadu pro zvýšení kapacity.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Chcete-li horizontální navýšení kapacity ručně, aktualizujte kapacitu ve vlastnosti Skladové položky požadovaného prostředku [škálovací sady virtuálních strojů.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Změna velikosti

Změna velikosti v vyžaduje větší pozornost než horizontální navýšení kapacity. Například:

* Systémové služby Service Fabric jsou spuštěny v primárním typu uzlu v clusteru. Nikdy vypnout nebo vertikálně snížit počet instancí pro tento typ uzlu tak, aby měli méně instancí, než co vyžaduje úroveň spolehlivosti. 
* Pro stavovou službu potřebujete určitý počet uzlů, které jsou vždy až k zachování dostupnosti a zachování stavu služby. Minimálně potřebujete počet uzlů, které se rovnají počtu cílových replik sady oddílu nebo služby.

Chcete-li škálovat ručně, postupujte takto:

1. Z Prostředí PowerShell `Disable-ServiceFabricNode` `RemoveNode` spusťte s úmyslem zakázat uzel, který chcete odebrat. Odeberte typ uzlu, který má nejvyšší číslo. Například pokud máte cluster se šesti uzl, odeberte instanci virtuálního počítače "MyNodeType_5".
2. Spusťte `Get-ServiceFabricNode` a ujistěte se, že uzel byl převeden na zakázáno. Pokud ne, počkejte, dokud není uzel zakázán. To může trvat několik hodin pro každý uzel. Nepokračujte, dokud uzel nebude převeden na deaktivován.
3. Snižte počet virtuálních discích o jednu v tomto typu uzlu. Nejvyšší instance virtuálního virtuálního byl nyní odebrán.
4. Podle potřeby opakujte kroky 1 až 3, dokud nezřídíte požadovanou kapacitu. Nezvětšujte počet instancí v typech primárních uzlů na méně, než vyžaduje úroveň spolehlivosti. Seznam doporučených instancí najdete [v tématu Plánování kapacity clusteru Service Fabric.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

Chcete-li škálovat ručně, aktualizujte kapacitu ve vlastnosti Skladové položky požadovaného prostředku [škálovací sady virtuálních strojů.](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Je nutné připravit uzel pro vypnutí škálovat programově. Najděte uzel, který má být odebrán (uzel s nejvyšší instancí). Například:

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

Deaktivace a odebrání uzlu pomocí `FabricClient` stejné`client` instance (v tomto případě)`instanceIdString` a instance uzlu ( v tomto případě), kterou jste použili v předchozím kódu:

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shut down
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement virtual machine scale set capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> Když zmenšit kapacitu clusteru, zobrazí se instance odebraného uzlu nebo virtuálního aplikace zobrazená v nefunkčním stavu v aplikaci Service Fabric Explorer. Vysvětlení tohoto chování naleznete [v tématu Chování, které můžete pozorovat v aplikaci Service Fabric Explorer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Můžete:
> * Volání [Remove-ServiceFabricNodeState příkaz](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) s příslušným názvem uzlu.
> * Nasazení [pomocné aplikace automatického škálování Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) v clusteru. Tato aplikace zajišťuje, že zmenšené uzly jsou vymazány z Aplikace Service Fabric Explorer.

## <a name="reliability-levels"></a>Úrovně spolehlivosti

[Úroveň spolehlivosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) je vlastností prostředku clusteru Service Fabric. Nelze ji nakonfigurovat odlišně pro jednotlivé typy uzlů. Řídí faktor replikace systémových služeb pro cluster a je nastavením na úrovni prostředků clusteru. 

Úroveň spolehlivosti určí minimální počet uzlů, které musí mít váš primární typ uzlu. Úroveň spolehlivosti může mít následující hodnoty:

* Platinum: Spustí systémové služby s cílovým počtem sad replik sedmi a devíti semenných uzlů.
* Zlaťáky: Spustí systémové služby s počtem cílových replik sedmi a sedmi semenných uzlů.
* Silver: Spustí systémové služby s cílovou sadu replik počet pět a pět semen uzly.
* Bronz: Spustí systémové služby s cílovým počtem sad replik tří a tří semenných uzlů.

Minimální doporučená úroveň spolehlivosti je Stříbrná.

Úroveň spolehlivosti je nastavena v části vlastnosti [prostředku Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), takto:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Úrovně trvanlivosti

> [!WARNING]
> Typy uzlů se bronzovou odolností _nezískávají žádná oprávnění_. Úlohy infrastruktury, které ovlivňují vaše bezstavové úlohy, nebudou zastaveny nebo zpožděny, což může ovlivnit vaše úlohy. 
>
> Odolnost bronze používejte pouze pro typy uzlů, které spouštějí bezstavové úlohy. Pro produkční úlohy spusťte silver nebo vyšší, abyste zajistili konzistenci stavu. Vyberte správnou spolehlivost na základě pokynů v [dokumentaci k plánování kapacity](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Úroveň trvanlivosti musí být nastavena ve dvou prostředcích. Jedním z nich je profil rozšíření [prostředku škálovací sady virtuálních strojů](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

Druhý prostředek je `nodeTypes` v prostředku [Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Další kroky

* Vytvořte cluster na virtuálních počítačích nebo v počítačích se systémem Windows Server: [Service Fabric, který vytváří cluster pro systém Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Vytvořte cluster na virtuálních počítačích nebo počítačích se systémem Linux: [Vytvořte cluster Linux .](service-fabric-cluster-creation-via-portal.md)
* Další informace o [možnostech podpory service fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

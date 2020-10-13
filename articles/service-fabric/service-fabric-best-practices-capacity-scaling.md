---
title: Plánování kapacity a škálování pro Azure Service Fabric
description: Osvědčené postupy pro plánování a škálování Service Fabric clusterů a aplikací.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: 96cd460ddfea863eb27a1087ff59f3b87acf65d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531300"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Plánování kapacity a škálování pro Azure Service Fabric

Než začnete vytvářet libovolný cluster Azure Service Fabric nebo škálovat výpočetní prostředky, které hostují cluster, je důležité naplánovat kapacitu. Další informace o plánování kapacity najdete v tématu [plánování kapacity clusteru Service Fabric](./service-fabric-cluster-capacity.md). Další informace o osvědčených postupech pro škálovatelnost clusteru najdete v tématu věnovaném [Service Fabric škálovatelnosti](/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Kromě zvážení typu uzlu a vlastností clusteru byste měli očekávat, že operace škálování budou trvat déle než hodinu, než se dokončí pro produkční prostředí. Tato úvaha je pravdivá bez ohledu na počet virtuálních počítačů, které přidáváte.

## <a name="autoscaling"></a>Automatické škálování
Můžete provádět operace škálování prostřednictvím šablon Azure Resource Manager, protože je osvědčeným postupem, jak zacházet s [konfiguracemi prostředků jako s kódem](./service-fabric-best-practices-infrastructure-as-code.md). 

Díky automatickému škálování přes služby Virtual Machine Scale Sets bude vaše verze Správce prostředků šablona nepřesná verze definovat počty instancí pro Virtual Machine Scale Sets. Nepřesná definice zvyšuje riziko, že budoucí nasazení způsobí nezamýšlené operace škálování. Automatické škálování byste měli použít, pokud:

* Nasazení šablon Správce prostředků s příslušnou deklarovanou kapacitou nepodporuje váš případ použití.
     
   Kromě ručního škálování můžete [v Azure DevOps Services nakonfigurovat kanál průběžné integrace a doručování pomocí projektů nasazení skupiny prostředků Azure](../azure-resource-manager/templates/add-template-to-azure-pipelines.md). Tento kanál je běžně aktivovaný aplikací logiky, která využívá metriky výkonu virtuálních počítačů, které se dotazují z [Azure Monitor REST API](../azure-monitor/platform/rest-api-walkthrough.md). Kanál efektivně spolupracuje na základě jakékoli metriky, kterou chcete, při optimalizaci pro Správce prostředků šablony.
* V jednu chvíli budete muset horizontálně škálovat jenom jeden uzel sady škálování virtuálních počítačů.
   
   Pro horizontální navýšení kapacity na tři nebo více uzlů byste měli [škálovat cluster Service Fabric tím, že přidáte sadu škálování virtuálního počítače](virtual-machine-scale-set-scale-node-type-scale-out.md). Je bezpečnější škálovat a škálovat sady virtuálních počítačů škálované na horizontální dobu jednoho uzlu.
* Pro svůj Cluster Service Fabric máte navyšovat spolehlivost nebo vyšší a odolnost proti chybám, a to v jakémkoli měřítku, kde konfigurujete pravidla automatického škálování.
  
   Minimální kapacita pro pravidla automatického škálování musí být rovna nebo větší než pět instancí virtuálních počítačů. Musí být také rovno nebo větší než minimální úroveň spolehlivosti pro váš typ primárního uzlu.

> [!NOTE]
> Service Fabric stavový Service Fabric:/System/InfastructureService/<NODE_TYPE_NAME> běží na každém typu uzlu, který má odolnost stříbrné nebo vyšší. Jedná se o jedinou systémovou službu, která se v Azure podporuje na všech typech uzlů clusteru.

> [!IMPORTANT]
> Automatické škálování Service Fabric podporuje `Default` `NewestVM` [Konfigurace škálování](../virtual-machine-scale-sets/virtual-machine-scale-sets-scale-in-policy.md)sady škálování virtuálních počítačů.

## <a name="vertical-scaling-considerations"></a>Požadavky vertikálního škálování

[Vertikální škálování](./virtual-machine-scale-set-scale-node-type-scale-out.md) typ uzlu v Azure Service Fabric vyžaduje několik kroků a důležitých informací. Například:

* Před škálováním musí být cluster v pořádku. V opačném případě budete cluster dále rozstabilizovat.
* Pro všechny Service Fabric typy uzlů clusteru, které hostují stavové služby, se vyžaduje úroveň trvanlivosti stříbra nebo vyšší.

> [!NOTE]
> Váš typ primárního uzlu, který hostuje stavové Service Fabric systémové služby, musí být na úrovni trvanlivosti stříbra nebo vyšší. Po povolení odolnosti proti stříbru budou operace clusteru, jako například upgrady, přidávání nebo odebírání uzlů, pomalejší, protože systém optimalizuje zabezpečení dat nad zrychlou operací.

Vertikální škálování: sada škálování virtuálního počítače je destruktivní operace. Místo toho horizontálně škálovat cluster přidáním nové sady škálování s požadovanou SKU. Pak migrujte své služby do požadované SKU a dokončete tak bezpečnou operaci vertikálního škálování. Změna SKU prostředku prostředků sady škálování virtuálního počítače je destruktivní operace, protože přeimagí hostitelů, které odstraňují všechny místně trvalé stavy.

Cluster používá Service Fabric [Vlastnosti uzlu a omezení umístění](./service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) k rozhodnutí, kde hostovat služby vaší aplikace. Když vertikálně měníte velikost primárního typu uzlu, deklarujte identické hodnoty vlastností pro `"nodeTypeRef"` . Tyto hodnoty najdete v rozšíření Service Fabric pro Virtual Machine Scale Sets. 

Následující fragment kódu šablony Správce prostředků zobrazuje vlastnosti, které deklarujete. Má stejnou hodnotu pro nově zřízené sady škálování, na které se škáluje, a podporuje se jenom jako dočasná stavová služba pro váš cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Nenechte cluster spuštěný s více sadami škálování, které používají stejnou `nodeTypeRef` hodnotu vlastnosti, která je delší, než je potřeba k dokončení úspěšné operace vertikálního škálování.
>
> Před pokusem o změnu v produkčním prostředí vždy ověřte operace v testovacích prostředích. Ve výchozím nastavení mají služby Service Fabric systému clusterů omezení umístění jenom na cílový typ primárního uzlu.

V případě deklarovaných vlastností uzlu a omezení umístění proveďte v jednom okamžiku následující kroky po jednotlivých instancích virtuálních počítačů. To umožňuje, aby se systémové služby (a stavové služby) na instanci virtuálního počítače, kterou odebíráte, korektně vypnuly, protože nové repliky se vytvářejí jinde.

1. Z PowerShellu spusťte `Disable-ServiceFabricNode` s záměrem `RemoveNode` Zakázat uzel, který se chystáte odebrat. Odeberte typ uzlu, který má nejvyšší číslo. Pokud máte například cluster se šesti uzly, odeberte instanci virtuálního počítače "MyNodeType_5".
2. Spusťte `Get-ServiceFabricNode` , abyste se ujistili, že uzel byl přepnut do stavu zakázáno. V takovém případě počkejte, dokud nebude uzel zakázán. U každého uzlu to může trvat několik hodin. Nepokračujte, dokud uzel nepřejde do stavu zakázáno.
3. Snižte počet virtuálních počítačů podle jednoho z těchto typů uzlů. Nejvyšší instance virtuálního počítače se teď odebere.
4. Opakujte kroky 1 až 3 podle potřeby, ale nikdy nepoužívejte škálování v počtu instancí v primárních uzlech, které jsou menší než to, co zaručuje úroveň spolehlivosti. Seznam doporučených instancí najdete v tématu [plánování kapacity clusteru Service Fabric](./service-fabric-cluster-capacity.md) .
5. Jakmile se všechny virtuální počítače odešlou (zobrazují se jako "nižší"), zobrazí se chybový stav topologie Fabric:/System/InfrastructureService/[název uzlu]. Pak můžete prostředek clusteru aktualizovat a odebrat typ uzlu. Můžete buď použít nasazení šablony ARM, nebo upravit prostředek clusteru prostřednictvím [Azure Resource Manageru](https://resources.azure.com). Tím se spustí upgrade clusteru, který odebere službu Fabric:/System/InfrastructureService/[typ uzlu], která je v chybovém stavu.
 6. I když můžete volitelně odstranit VMScaleSet, pořád se uzly zobrazí jako "nižší" v Service Fabric Explorer zobrazení. Posledním krokem je vyčištění pomocí `Remove-ServiceFabricNodeState` příkazu.

## <a name="horizontal-scaling"></a>Horizontální škálování

Horizontální škálování můžete provádět buď [ručně](./service-fabric-cluster-scale-in-out.md) , nebo [programově](./service-fabric-cluster-programmatic-scaling.md).

> [!NOTE]
> Pokud měníte velikost typu uzlu, který má odolnost stříbra nebo Gold, škálování bude pomalé.

### <a name="scaling-out"></a>Horizontální navýšení kapacity

Horizontální navýšení kapacity Service Fabric clusteru zvýšením počtu instancí konkrétní sady škálování virtuálního počítače. Horizontální `AzureClient` navýšení kapacity můžete navýšit pomocí a ID požadované sady škálování a zvýšit tak kapacitu.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Ruční horizontální navýšení kapacity aktualizujte tak, že aktualizujete kapacitu ve vlastnosti SKU požadovaného prostředku [sady škálování virtuálního počítače](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) .

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Škálování v

Škálování v nástroji vyžaduje větší pozornost než horizontální navýšení kapacity. Například:

* Service Fabric systémové služby se spouštějí ve vašem clusteru jako primární typ uzlu. Nikdy nestavte ani nezmenšujte počet instancí tohoto typu uzlu tak, aby bylo méně instancí, než kolik zaručuje úroveň spolehlivosti. 
* Pro stavovou službu potřebujete určitý počet uzlů, které jsou vždycky v provozu a udržují stav služby. Minimálně potřebujete určitý počet uzlů, který se rovná počtu cílových sad replik oddílu nebo služby.

K ručnímu škálování použijte následující postup:

1. Z PowerShellu spusťte `Disable-ServiceFabricNode` s záměrem `RemoveNode` Zakázat uzel, který se chystáte odebrat. Odeberte typ uzlu, který má nejvyšší číslo. Pokud máte například cluster se šesti uzly, odeberte instanci virtuálního počítače "MyNodeType_5".
2. Spusťte `Get-ServiceFabricNode` , abyste se ujistili, že uzel byl přepnut do stavu zakázáno. V takovém případě počkejte, dokud nebude uzel zakázán. U každého uzlu to může trvat několik hodin. Nepokračujte, dokud uzel nepřejde do stavu zakázáno.
3. Snižte počet virtuálních počítačů podle jednoho z těchto typů uzlů. Nejvyšší instance virtuálního počítače se teď odebere.
4. Opakujte kroky 1 až 3 podle potřeby, dokud nezřídíte kapacitu, kterou požadujete. Neprovádějte škálování v počtu instancí v primárních uzlech tak, aby byla menší než to, co zaručuje úroveň spolehlivosti. Seznam doporučených instancí najdete v tématu [plánování kapacity clusteru Service Fabric](./service-fabric-cluster-capacity.md) .

Pokud chcete ručně škálovat, aktualizujte kapacitu ve vlastnosti SKU požadovaného prostředku [sady škálování virtuálního počítače](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) .

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Je nutné připravit uzel na vypnutí, aby bylo možné programově škálovat. Vyhledejte uzel, který má být odebrán (nejvyšší uzel instance). Například:

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

Deaktivujte a odeberte uzel pomocí stejné `FabricClient` instance ( `client` v tomto případě) a instance uzlu ( `instanceIdString` v tomto případě), kterou jste použili v předchozím kódu:

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
> Při škálování v clusteru uvidíte, že odebraný uzel/instance virtuálního počítače se v Service Fabric Explorer zobrazí ve stavu není v pořádku. Vysvětlení tohoto chování najdete v tématu chování, [které můžete sledovat v Service Fabric Explorer](./service-fabric-cluster-scale-in-out.md#behaviors-you-may-observe-in-service-fabric-explorer). Můžete:
> * Zavolejte [příkaz Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps&preserve-view=true) s odpovídajícím názvem uzlu.
> * Nasaďte do clusteru [aplikaci pomocníka automatického škálování Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper/) . Tato aplikace zajišťuje, aby se nezaškrtnuté uzly s horizontálním škálováním z Service Fabric Explorer.

## <a name="reliability-levels"></a>Úrovně spolehlivosti

[Úroveň spolehlivosti](./service-fabric-cluster-capacity.md) je vlastnost prostředku clusteru Service Fabric. Nedá se nakonfigurovat jinak pro jednotlivé typy uzlů. Řídí faktor replikace systémových služeb pro cluster a jedná se o nastavení na úrovni prostředků clusteru. 

Úroveň spolehlivosti určí minimální počet uzlů, které musí mít váš primární typ uzlu. Úroveň spolehlivosti může mít následující hodnoty:

* Platinum: spustí systémové služby s cílovým počtem sad replik na sedmi a devíti počátečních uzlech.
* Gold: spustí systémové služby s cílovou sadou repliky sedm a sedm počátečních uzlů.
* Stříbrné: spustí systémové služby s cílovým počtem sad replik na pět a pět počátečních uzlů.
* Bronz: spustí systémové služby s cílovým počtem sad replik na třech a třech počátečních uzlech.

Minimální doporučená úroveň spolehlivosti je stříbrná.

Úroveň spolehlivosti je nastavena v části vlastnosti [prostředku Microsoft. ServiceFabric/clustery](/azure/templates/microsoft.servicefabric/2018-02-01/clusters), například:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Úrovně trvanlivosti

> [!WARNING]
> Typy uzlů běžící s bronzovou trvanlivostí nezískají _žádná oprávnění_. Úlohy infrastruktury, které ovlivňují vaše bezstavové úlohy, se neukončí ani nebudou zpožděny, což může mít vliv na vaše úlohy. 
>
> Používejte bronzovou odolnost pouze pro typy uzlů, které spouštějí bezstavové úlohy. Pro produkční úlohy spusťte stříbro nebo vyšší a zajistěte tak konzistenci stavu. Vyberte správnou spolehlivost na základě pokynů v [dokumentaci pro plánování kapacity](./service-fabric-cluster-capacity.md).

Úroveň trvanlivosti musí být nastavena ve dvou zdrojích. Jedním z nich je profil rozšíření [prostředku sady škálování virtuálních počítačů](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

Druhý prostředek je pod `nodeTypes` [prostředkem Microsoft. ServiceFabric/clustery](/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Další kroky

* Vytvoření clusteru na virtuálních počítačích nebo počítačích se systémem Windows Server: [Service Fabric vytvoření clusteru pro Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Vytvořte cluster na virtuálních počítačích nebo počítačích se systémem Linux: [vytvořte cluster se systémem Linux](service-fabric-cluster-creation-via-portal.md).
* Přečtěte si o [možnostech podpory Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
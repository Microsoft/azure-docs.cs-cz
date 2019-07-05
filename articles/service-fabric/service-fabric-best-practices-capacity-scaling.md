---
title: Plánování kapacity a škálování pro Azure Service Fabric | Dokumentace Microsoftu
description: Osvědčené postupy pro plánování a škálování clusterů Service Fabric a aplikací.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 8ba4763e8d4835911d33d21c0f5bb431851a649b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444719"
---
# <a name="capacity-planning-and-scaling-for-azure-service-fabric"></a>Plánování kapacity a škálování pro Azure Service Fabric

Než vytvoříte jakéhokoli jiného clusteru Azure Service Fabric nebo škálovat výpočetní prostředky, které hostují váš cluster, je důležité plánovat kapacitu. Další informace o plánování kapacity najdete v tématu [plánování kapacity clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Další doporučené postupy pokyny pro: škálovatelnost clusteru najdete v tématu [aspekty zabezpečení Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#scalability-considerations).

Navíc vzhledem k tomu uzel typu a cluster charakteristiky, měli byste očekávat operací škálování bude trvat delší dobu než hodina dokončení pro produkční prostředí. Toto posouzení platí bez ohledu na počet virtuálních počítačů, které přidáváte.

## <a name="autoscaling"></a>Automatické škálování
Operace škálování prostřednictvím šablon Azure Resource Manageru, by měl provést, protože je osvědčeným postupem považovat [konfigurace prostředků jako kód]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). 

Pomocí automatické škálování prostřednictvím škálovacích sad virtuálních počítačů bude nesprávně vykázán definovat počet instancí pro škálovací sady virtuálních počítačů označené verzí šablony Resource Manageru. Nesprávné definice zvyšuje riziko, že budoucí nasazení způsobí, že nežádoucí operace škálování. Obecně platí měli byste použít automatické škálování, pokud:

* Nasazení své šablony Resource Manageru pomocí příslušné kapacitě deklarované nepodporuje vašemu případu použití.
     
   Kromě ruční škálování, můžete nakonfigurovat [průběžné integrace a doručování kanálu služby Azure DevOps s využitím projekty nasazení skupiny prostředků Azure](https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts). Tento kanál se běžně aktivuje aplikace logiky, která používá posílat dotaz z metrik výkonu virtuálních počítačů [REST API služby Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough). Kanál efektivně bude automaticky škálovat podle libovolné metriky chcete, a optimalizuje šablon Resource Manageru.
* Je potřeba horizontálně škálovat jenom jeden uzel škálovací sady virtuálních počítačů najednou.
   
   Horizontální navýšení kapacity o tři nebo více uzlů najednou, měli byste [horizontální navýšení kapacity clusteru Service Fabric tak, že přidáte škálovací sadu virtuálních počítačů](virtual-machine-scale-set-scale-node-type-scale-out.md). Je nejbezpečnější škálování v a horizontální navýšení kapacity virtuálního počítače škálovací sady vodorovně, jednoho uzlu současně.
* Máte stříbrné spolehlivost nebo vyšší pro cluster Service Fabric a stříbrné odolnosti nebo vyšší v libovolném měřítku, kde můžete nakonfigurovat pravidla automatického škálování.
  
   Minimální kapacita pro pravidla automatického škálování vám musí být roven nebo větší než pět instancí virtuálních počítačů. Také musí být roven nebo větší než vaše minimální úroveň spolehlivosti pro svůj typ primárního uzlu.

> [!NOTE]
> Stavová služba fabric Service Fabric: / System/InfastructureService/< NODE_TYPE_NAME > spustí na každý typ uzlu, který má stříbrné nebo vyšší odolnosti. Je jenom systémová služba, která je podporována pro spuštění v Azure na všech typů uzlu clusterů.

## <a name="vertical-scaling-considerations"></a>Vertikální škálování důležité informace

[Vertikální škálování](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out) typ uzlu v Azure Service Fabric vyžaduje několik kroků a důležité informace. Příklad:

* Cluster musí být v pořádku před Škálováním. V opačném případě budete destabilizovat cluster dál.
* Odolnost stříbrné úrovně nebo vyšší se vyžaduje pro všechny typy uzlů clusteru Service Fabric, které jsou hostiteli stavové služby.

> [!NOTE]
> Svůj typ primárního uzlu, který je hostitelem stavové systémové služby Service Fabric musí být odolnost stříbrné úrovně nebo vyšší. Po povolení stříbrné odolnost clusteru operace, jako je inovace, přidání nebo odebrání uzlů a tak dále sníží se vzhledem k tomu, že systém optimalizuje pro zabezpečení dat před rychlostí operací.

Vertikální škálování škálovací sady virtuálních počítačů je destruktivní operace. Horizontálně škálujte místo toho váš cluster tak, že přidáte novou škálovací sadu s požadovanou SKU. Poté migrujte služby do požadovaného skladové jednotky k dokončení operace bezpečné vertikální škálování. Změna zdroje sady škálování virtuálního počítače SKU je destruktivní operace, protože to umožňuje obnovit z Image hostitelů, která odebere všechny místně trvalý stav.

Cluster využívá Service Fabric [vlastnosti uzlu a omezení umístění](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) rozhodnout, kde se mají hostovat vaše aplikační služby. Při svisle škálujeme kapacitu vaší primární typ uzlu, deklarujte hodnoty vlastností identické `"nodeTypeRef"`. Tyto hodnoty můžete najít v rozšíření Service Fabric pro škálovací sady virtuálních počítačů. 

Následující fragment kódu šablony Resource Manageru jsou uvedeny vlastnosti, které budete deklarovat. Má stejnou hodnotu pro nově zřízené škálovací sady, které se škáluje na a je podporovaný jenom jako dočasné stavové služby pro váš cluster.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Nenechávejte svůj cluster během několika škálovacími sadami, které používají stejné `nodeTypeRef` delší než hodnota vlastnosti předpokladem pro dokončení úspěšného provozu vertikální škálování.
>
> Než se pokusíte změny v produkčním prostředí vždy ověřovaly operace v testovacích prostředích. Systémové služby Service Fabric cluster mají ve výchozím omezení umístění a jenom na cílové primární typ uzlu.

Vlastnosti uzlu a omezení umístění, které jsou deklarovány proveďte následující kroky jeden virtuální počítač instance najednou. Díky tomu systémové služby (a stavové služby) se řádně ukončit na instanci virtuálního počítače, který odstraňujete, jako jsou vytvořeny nové repliky jinde.

1. Z prostředí PowerShell, spusťte `Disable-ServiceFabricNode` s cílem `RemoveNode` zakázat uzel, který se chystáte odebrat. Odeberte typ uzlu, který má nejvyšší číslo. Například pokud máte cluster šesti uzly odeberte instanci virtuálního počítače "MyNodeType_5".
2. Spustit `Get-ServiceFabricNode` abyste měli jistotu, že uzel přešla na zakázáno. Pokud ne, počkejte, dokud nebude uzel je zakázaná. Může to trvat několik hodin pro každý uzel. Nechcete pokračovat, dokud se uzel přešla na zakázáno.
3. Snížit počet virtuálních počítačů jednou v tomto typu uzlu. Nejvyšší instance virtuálního počítače se nyní odeberou.
4. Opakujte kroky 1 až 3 podle potřeby, ale nikdy vertikálně snížit kapacitu počtu instancí ve primárního uzlu typy menší, než co zaručuje úroveň spolehlivosti. Zobrazit [plánování kapacity clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) seznam doporučených instancí.
5. Jakmile se všechny virtuální počítače jsou pryč (reprezentovány jako "Dolů") topologie fabric: / System/InfrastructureService / [název uzlu] Zobrazí chybový stav. Potom můžete aktualizovat prostředek clusteru k odebrání typu uzlu. Můžete buď použít nasazení šablony ARM nebo upravit prostředek clusteru prostřednictvím [Azure resource Manageru](https://resources.azure.com). Tím spustíte upgrade clusteru, který odebere topologie fabric: / System/InfrastructureService / [typ uzlu] služby, který je v chybovém stavu.
 6. Po, můžete volitelně můžete odstranit škálovací sady virtuálních počítačů, uzly se stále zobrazí jako "Dolů" v Service Fabric Exploreru zobrazte ale. Posledním krokem by ho PROČISTIT s `Remove-ServiceFabricNodeState` příkazu.

### <a name="example-scenario"></a>Příklad scénáře
Je podporovaný scénář, kdy se mají provést operaci vertikální škálování: Chcete provést migraci vašeho clusteru Service Fabric a aplikace z nespravovaných disků na managed disks bez výpadků aplikace. 

Můžete zřídit novou škálovací sadu virtuálních počítačů se spravovanými disky a proveďte upgrade aplikace pomocí omezení umístění, které se zaměřují zřízená kapacita. Cluster Service Fabric pak můžete plánovat úlohy zřízený cluster uzel kapacity nasazení podle upgradovací domény bez výpadků aplikace. 

Koncové body pro službu back endový fond [základní SKU nástroje pro vyrovnávání zatížení Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) může být virtuální počítače v jedné skupiny dostupnosti nebo škálovací sady virtuálních počítačů. To znamená, že pokud přesun aplikace Service Fabric systémy mezi škálovací sady, aniž by to způsobilo dočasné inaccessibility koncového bodu správy clusteru Service Fabric nelze použít nástroj pro vyrovnávání zatížení základní SKU. To platí i v případě, že cluster a jeho aplikace pořád běží.

Uživatelé obvykle zřízení nástroje pro vyrovnávání zatížení standardní SKU při provádění prohození virtuální IP adresy (VIP) mezi základní SKU nástroje pro vyrovnávání zatížení a prostředků nástroje pro vyrovnávání zatížení standardní SKU. Tato technika omezuje všechny budoucí inaccessibility přibližně 30 sekund, vyžaduje se pro prohození virtuálních IP adres.

## <a name="horizontal-scaling"></a>Horizontální škálování

Můžete provést buď horizontální škálování [ručně](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) nebo [programově](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Pokud typ uzlu, který má stříbrné nebo zlaté úrovně odolnosti škálujeme kapacitu, bude škálování pomalé.

### <a name="scaling-out"></a>Horizontální navýšení kapacity

Horizontální navýšení kapacity clusteru Service Fabric zvýšením počtu instancí pro škálovací sadu s konkrétní virtuální počítač. Můžete horizontálně navýšit prostřednictvím kódu programu pomocí `AzureClient` a ID požadované škálovací sady ke zvýšení kapacity.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Pro horizontální navýšení kapacity ručně, aktualizujte kapacitu ve požadovanou vlastnost SKU [škálovací sadu virtuálních počítačů](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) prostředků.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>Škálování v

Změna měřítka ve vyžaduje další zkoumání než horizontální navýšení kapacity. Příklad:

* Systémové služby Service Fabric spusťte v primární typ uzlu v clusteru. Ani na chvilku vypnutý vertikálně zvýšit nebo snížit počet instancí pro daný typ uzlu, abyste měli méně instancí, než co zaručuje úroveň spolehlivosti. 
* Stavové služby je nutné je určitý počet uzlů, které jsou vždycky nahoru k zachování dostupnosti a zachovávají stav vaší služby. Minimálně musíte počet uzlů roven počtu cílových replik sady oddílů nebo služby.

Škálování v ručně, postupujte takto:

1. Z prostředí PowerShell, spusťte `Disable-ServiceFabricNode` s cílem `RemoveNode` zakázat uzel, který se chystáte odebrat. Odeberte typ uzlu, který má nejvyšší číslo. Například pokud máte cluster šesti uzly odeberte instanci virtuálního počítače "MyNodeType_5".
2. Spustit `Get-ServiceFabricNode` abyste měli jistotu, že uzel přešla na zakázáno. Pokud ne, počkejte, dokud nebude uzel je zakázaná. Může to trvat několik hodin pro každý uzel. Nechcete pokračovat, dokud se uzel přešla na zakázáno.
3. Snížit počet virtuálních počítačů jednou v tomto typu uzlu. Nejvyšší instance virtuálního počítače se nyní odeberou.
4. Opakujte kroky 1 až 3 podle potřeby, dokud nezřídíte kapacitu, kterou chcete. Není vertikálně snížit kapacitu počtu instancí ve primárního uzlu typy na hodnotu menší než co zaručuje úroveň spolehlivosti. Zobrazit [plánování kapacity clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) seznam doporučených instancí.

Škálování v ručně, aktualizujte kapacitu ve požadovanou vlastnost SKU [škálovací sadu virtuálních počítačů](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) prostředků.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

Je třeba připravit uzel pro vypnutí škálování ve prostřednictvím kódu programu. Najdete uzel, který má být odebrána (uzel nejvyšší instance). Příklad:

```c#
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

Deaktivace a odebrání uzlu pomocí stejného `FabricClient` instance (`client` v tomto případě) a instance uzlu (`instanceIdString` v tomto případě), který jste použili v předchozím kódu:

```c#
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
> Když vertikálně snížit kapacitu clusteru, zobrazí se vám odebral uzel nebo instanci virtuálního počítače. zobrazí ve stavu není v pořádku v Service Fabric Exploreru. Vysvětlení tohoto chování najdete v tématu [chování můžete sledovat v Service Fabric Exploreru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer). Můžete:
> * Volání [příkaz Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) s názvem příslušný uzel.
> * Nasazení [podpůrnou aplikací Service Fabric automatického škálování](https://github.com/Azure/service-fabric-autoscale-helper/) ve vašem clusteru. Tuto aplikaci se zajistí, že uzly škálovat dolů jsou vymazány ze Service Fabric Explorer.

## <a name="reliability-levels"></a>Úroveň spolehlivosti

[Úroveň spolehlivosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) je vlastnost prostředku clusteru Service Fabric. Nedá se nakonfigurovat jinak pro typy jednotlivých uzlů. Ovládací prvky faktor replikace systému služeb pro cluster a je nastavení na úrovni prostředku clusteru. 

Úroveň spolehlivosti určí minimální počet uzlů, které musí mít svůj typ primárního uzlu. Úroveň spolehlivosti můžete provést následující hodnoty:

* Platinum: Spouští systémové služby a cílové repliky sady počet sedm a devět počáteční uzly.
* Zlatá: Spouští systémové služby cílový počet sady replik sedm na sedm uzlů počáteční hodnoty.
* Stříbrná: Spouští systémové služby cílový počet sady replik pěti až pět uzlů počáteční hodnoty.
* Bronzová: Spouští systémové služby cílový počet sady replik tři a tři uzly počáteční hodnoty.

Minimální doporučenou spolehlivostí úroveň je Silver.

V oddílu properties je nastavena úroveň spolehlivosti [Microsoft.ServiceFabric/clusters prostředků](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), tímto způsobem:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Úrovně odolnosti

> [!WARNING]
> Získat typy uzlů s bronzovou odolnosti _žádná oprávnění_. Infrastruktury úlohy, které ovlivňují Bezstavová zatížení nebude zastaven nebo zpoždění, které by mohly ovlivnit vaše úlohy. 
>
> Bronzová odolnosti používejte pouze pro typy uzlů, na kterých běží Bezstavová zatížení. Pro produkční úlohy spusťte stříbrné nebo vyšší zajistit konzistenci stavu. Zvolte správný spolehlivost podle pokynů v [dokumentace k plánování kapacity](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Úroveň odolnosti musí být nastavena v dva prostředky. Jeden je profil rozšíření [škálovací sady virtuálních počítačů resource](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

Probíhá jiný prostředek `nodeTypes` v [Microsoft.ServiceFabric/clusters prostředků](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters): 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Další postup

* Vytvoření clusteru na virtuální počítače nebo počítače se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Vytvoření clusteru na počítače s Linuxem nebo virtuální počítače: [Vytvoření clusteru s Linuxem](service-fabric-cluster-creation-via-portal.md).
* Další informace o [možnosti podpory pro Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

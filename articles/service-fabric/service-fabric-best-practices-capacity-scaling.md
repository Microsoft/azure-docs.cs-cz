---
title: Azure Service Fabric pro plánování kapacity a osvědčené postupy pro škálování | Dokumentace Microsoftu
description: Osvědčené postupy pro plánování a škálování clusterů Service Fabric a aplikací.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d6f2ca53829642009adbc50061966c5a7e924f7e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240399"
---
# <a name="capacity-planning-and-scaling"></a>Plánování kapacity a škálování

Před vytvořením jakéhokoli jiného clusteru Azure Service Fabric nebo škálování výpočetních prostředků, který je hostitelem vašeho clusteru, je důležité plánovat kapacitu. Další informace o plánování kapacity najdete v tématu [plánování kapacity clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity). Navíc vzhledem k tomu charakteristiky Nodetype a clusteru, plánování škálování operace trvá déle než hodinu k dokončení pro produkční prostředí bez ohledu na počet virtuálních počítačů, které chcete přidat.

## <a name="auto-scaling"></a>Auto Scaling
Operace škálování by měl možné provádět prostřednictvím šablony nasazení prostředků Azure, protože je osvědčeným postupem je považovat [konfigurace prostředků jako kód]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)a pomocí Škálovací sady virtuálních počítačů automatického škálování bude výsledkem vaší verze šablony Resource Manageru nesprávně vykázán definování škálovací virtuálního počítače nastavte počet instancí; zvýšeného rizika budoucí nasazení způsobuje nežádoucí operace škálování a obecně byste měli použít, automatické škálování, pokud:

* Nasazení své šablony Resource Manageru pomocí příslušné kapacitě deklarované nepodporuje vašemu případu použití.
  * Kromě ruční škálování, můžete nakonfigurovat [průběžnou integraci a doručování kanál služby Azure DevOps pomocí projekty nasazení skupiny prostředků Azure]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts), které obvykle aktivuje aplikace logiky, které využívá připojení metriky výkonu virtuálního počítače, posílat dotaz z [REST API služby Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough); efektivně automatické škálování podle libovolné metriky chcete, při optimalizaci Azure Resource Manageru přidejte hodnotu.
* Potřebujete horizontálně škálovat 1 uzel škálovací sady virtuálních počítačů najednou.
  * Horizontální navýšení kapacity 3 nebo více uzlů najednou, měli byste [škálovat cluster Service Fabric out tak, že přidáte Škálovací sady virtuálních počítačů](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), a je nejbezpečnější, které horizontálně sníží a navýšení kapacity Škálovací virtuálních počítačů nastaví vodorovně 1 uzel najednou.
* Máte na spolehlivost Silver nebo vyšší pro váš Service Fabric Cluster a Silver odolnosti nebo vyšší v libovolném měřítku Set nakonfigurovat pravidla automatického škálování.
  * Automatické škálování pravidla kapacity [minimální] musí být roven nebo větší než 5 instancí virtuálních počítačů a musí být roven nebo větší než vaše minimální úroveň spolehlivosti pro svůj typ primárního uzlu.

> [!NOTE]
> Prostředky infrastruktury pro stavové služby Azure Service Fabric: / System/InfastructureService/< NODE_TYPE_NAME > spustí na každý typ uzlu, který má Silver nebo vyšší odolnost, což je jediná služba systému, která je podporována pro spuštění v Azure na všech vašich clusterů typy uzlů . 

## <a name="vertical-scaling-considerations"></a>Vertikální škálování důležité informace

[Vertikální škálování](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out#upgrade-the-size-and-operating-system-of-the-primary-node-type-vms) typ uzlu v Azure Service Fabric vyžaduje několik kroků a důležité informace. Příklad:
* Cluster musí být v pořádku před Škálováním. V opačném případě bude pouze destabilizovat další clusteru.
* **Odolnost větší nebo na úrovni Silver** se vyžaduje pro všechny služby NodeTypes clusteru prostředků infrastruktury, které jsou hostiteli stavové služby.

> [!NOTE]
> Váš primární NodeType, který je hostitelem systémové služby Service Fabric stavová musí být odolnost stříbrné úrovně nebo vyšší. Jakmile povolíte stříbrné odolnost clusteru operace, jako je inovace, přidání nebo odebrání uzlů a tak dále sníží se vzhledem k tomu, že systém optimalizuje pro zabezpečení dat před rychlostí operací.

Vertikální škálování Škálovací sady virtuálních počítačů je destruktivní operace. Místo toho vodorovně škálování clusteru tak, že přidáte novou Škálovací sadu s požadovanou SKU a migraci vašich služeb na požadovanou skladové jednotky k dokončení operace bezpečné vertikální škálování. Změna prostředku Škálovací sady virtuálních počítačů SKU je destruktivní operace, protože to umožňuje obnovit z Image vaši hostitelé, které odebere všechny místně trvalého stavu.

Service Fabric [vlastnosti uzlu a omezení umístění](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints) se používají ve vašem clusteru se rozhodnout, kde se mají hostovat vaše aplikace služby. Vertikální škálování svůj typ primárního uzlu při deklaraci hodnoty vlastností identické `"nodeTypeRef"`, který se nachází v rozšíření prostředků infrastruktury služby virtuálního počítače Škálovací sady služby. Následující fragment kódu šablony Resource Manageru jsou uvedeny vlastnosti, že řetězec budete deklarovat, se stejnou hodnotou pro nové zřízené škálovací sady, které jsou pro škálování a je podporovaný jenom jako dočasný stavový pro váš cluster:

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> Nechte svůj cluster během několika škálovacími sadami, které používají stejné `nodeTypeRef` delší než hodnota vlastnosti předpokladem pro dokončení úspěšného provozu vertikální škálování.
> Před opakováním změny produkční prostředí vždy ověřte operace v testovacích prostředích. Systémové služby Service Fabric Cluster mají ve výchozím omezení umístění a cílit na pouze primární nodetype.

Vlastnosti uzlu a omezení umístění, které jsou deklarovány proveďte následující kroky jeden virtuální počítač instance najednou. Díky tomu systémové služby (a stavové služby) se řádně ukončit na instancích virtuálních počítačů, které odebíráte vytvářené nové repliky jinde.
1. Z prostředí PowerShell, spusťte `Disable-ServiceFabricNode` s cílem je "RemoveNode" Zakázat uzel, Chystáte se odebrat. Odeberte typ uzlu, který má nejvyšší číslo. Například pokud máte cluster šesti uzly odeberte instanci virtuálního počítače "MyNodeType_5".
2. Spustit `Get-ServiceFabricNode` abyste měli jistotu, že uzel přešla na zakázáno. Pokud ne, počkejte, dokud nebude uzel je zakázaná. To může trvat několik hodin pro každý uzel. Nechcete pokračovat, dokud se uzel přešla na zakázáno.
3. Snížit počet virtuálních počítačů jednou v tomto typu uzlu. Nejvyšší instance virtuálního počítače se nyní odeberou.
4. Opakujte kroky 1 až 3 podle potřeby, ale nikdy vertikálně snížit kapacitu počtu instancí ve primárního uzlu typy menší, než co zaručuje úroveň spolehlivosti. Zobrazit [plánování kapacity clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) seznam doporučených instancí.

## <a name="horizontal-scaling"></a>Horizontální škálování

Vodorovné škálování v Service Fabric můžete udělat buď [ručně](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) nebo [programově](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling).

> [!NOTE]
> Pokud jsou škálování typ uzlu, který má stříbrné nebo zlaté odolnost, škálování bude pomalé.

### <a name="scaling-out"></a>Horizontální navýšení kapacity

Horizontální navýšení kapacity clusteru Service Fabric zvýšením počtu instancí pro konkrétní sadu škálování virtuálního počítače. Lze upravit rozsah prostřednictvím kódu programu pomocí AzureClient a ID pro požadovanou škálovací sady ke zvýšení kapacity.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Pro horizontální navýšení kapacity ručně, aktualizujte kapacitu ve požadovanou vlastnost SKU [Škálovací sady virtuálních počítačů](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) prostředků.
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
* Stavové služby je nutné je určitý počet uzlů, které jsou vždycky nahoru k zachování dostupnosti a zachovávají stav vaší služby. Minimálně musíte počet uzlů roven počtu cílových replik sady oddílu/služby.

Škálování v ručně, postupujte takto:

1. Z prostředí PowerShell, spusťte `Disable-ServiceFabricNode` s cílem je "RemoveNode" Zakázat uzel, Chystáte se odebrat. Odeberte typ uzlu, který má nejvyšší číslo. Například pokud máte cluster šesti uzly odeberte instanci virtuálního počítače "MyNodeType_5".
2. Spustit `Get-ServiceFabricNode` abyste měli jistotu, že uzel přešla na zakázáno. Pokud ne, počkejte, dokud nebude uzel je zakázaná. To může trvat několik hodin pro každý uzel. Nechcete pokračovat, dokud se uzel přešla na zakázáno.
3. Snížit počet virtuálních počítačů jednou v tomto typu uzlu. Nejvyšší instance virtuálního počítače se nyní odeberou.
4. Opakujte kroky 1 až 3 podle potřeby, ale nikdy vertikálně snížit kapacitu počtu instancí ve primárního uzlu typy menší, než co zaručuje úroveň spolehlivosti. Zobrazit [plánování kapacity clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) seznam doporučených instancí.

Škálování v ručně, aktualizujte kapacitu ve požadovanou vlastnost SKU [Škálovací sady virtuálních počítačů](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) prostředků.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. Opakujte kroky 1 až 3, dokud nezřídíte kapacitu, kterou chcete. Není vertikálně snížit kapacitu počtu instancí ve primárního uzlu typy na hodnotu menší než co zaručuje úroveň spolehlivosti. Podrobnosti o úrovních spolehlivost a počtu instancí se vyžaduje, najdete [plánování kapacity clusteru Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Musíte Příprava uzlu pro vypnutí škálování ve prostřednictvím kódu programu. To zahrnuje hledání, že se uzel, který má být odebrána, což je nejvyšší uzel instance a její deaktivace ho. Příklad:

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

Jakmile identifikujete uzel, který má odebrat, deaktivovat a odebrat pomocí stejných `FabricClient` instance (`client` v tomto případě) a název instance uzlu (`instanceIdString` v tomto případě) jste použili ve výše uvedeném kódu:

```c#
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

## <a name="reliability-levels"></a>Úroveň spolehlivosti

[Úroveň spolehlivosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) je vlastnost prostředku clusteru Service Fabric a nedá se konfigurovat odlišně pro jednotlivé nodeTypes. Ovládací prvky faktor replikace systému služeb pro cluster a je nastavení na úrovni prostředku clusteru. Úroveň spolehlivosti určí minimální počet uzlů, které musí mít svůj typ primárního uzlu. Úroveň spolehlivosti můžete provést následující hodnoty:
* Platinum - spouští cílový počet sady replik ze sedmi a devět počáteční uzly systémových služeb.
* Zlatá - spouští cílový počet sady replik sedm na sedm uzlů počáteční hodnoty systémových služeb.
* Silver – spouští systémové služby a cílové repliky sady počet pěti až pěti uzly počáteční hodnoty.
* Bronzový – spuštění systémových služeb s cílový počet sady replik tři a tři uzly počáteční hodnoty.

Minimální doporučenou spolehlivostí úroveň je Silver.

V oddílu properties je nastavena úroveň spolehlivosti [Microsoft.ServiceFabric/clusters prostředků](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters), tímto způsobem:

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>Úrovně odolnosti

> [!WARNING]
> Získat typy uzlů s bronzovou odolnosti _žádná oprávnění_. To znamená, že úlohy infrastruktury, které ovlivnit Bezstavová zatížení nebude mohly zastavit nebo zpoždění, které může mít vliv na vaše úlohy. Bronzová odolnosti používejte pouze pro typy uzlů, na kterých běží Bezstavová zatížení. Pro produkční úlohy, spusťte Silver nebo výše, aby se zajistila konzistence stavu. Zvolte správný spolehlivost podle pokynů v [dokumentace k plánování kapacity](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity).

Úroveň odolnosti musí být nastavena v dva prostředky. Profil rozšíření [prostředku Škálovací sady virtuálních počítačů](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile):

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

V části `nodeTypes` v [Microsoft.ServiceFabric/clusters prostředků](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters) 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>Další postup

* Vytvoření clusteru na virtuální počítače nebo počítače se systémem Windows Server: [Vytvoření clusteru Service Fabric pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na počítače s Linuxem nebo virtuální počítače: [Vytvoření clusteru s Linuxem](service-fabric-cluster-creation-via-portal.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png

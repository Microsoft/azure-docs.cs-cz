---
title: Konfigurace samostatného clusteru Azure Service Fabric
description: Přečtěte si, jak nakonfigurovat samostatný nebo místní cluster Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: fd93263b38340ce080cca1aecb98f3a599ff1861
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843154"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Nastavení konfigurace samostatného clusteru Windows
Tento článek popisuje nastavení konfigurace samostatného clusteru Azure Service Fabric, který je možné nastavit v *ClusterConfig.jsv* souboru. Tento soubor použijete k zadání informací o uzlech clusteru, konfiguracích zabezpečení a topologii sítě z hlediska selhání a upgradovacích domén.  Po změně nebo přidání nastavení konfigurace můžete buď [vytvořit samostatný cluster](service-fabric-cluster-creation-for-windows-server.md) , nebo [upgradovat konfiguraci samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).

Když [si stáhnete samostatný balíček Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), jsou zahrnuté i ClusterConfig.jsna ukázkách. Ukázky, které mají v názvech "DevCluster", vytvoří cluster se všemi třemi uzly na stejném počítači pomocí logických uzlů. Z těchto uzlů musí být alespoň jeden označený jako primární uzel. Tento typ clusteru je užitečný pro vývojová nebo testovací prostředí. Nepodporuje se jako produkční cluster. Ukázky s "více počítači" v jejich názvech vám pomůžou vytvářet clustery produkčních stupňů s každým uzlem v samostatném počítači. Počet primárních uzlů pro tyto clustery je založený na [úrovni spolehlivosti](#reliability)clusteru. Ve verzi 5,7 rozhraní API verze 05-2017 jsme odebrali vlastnost na úrovni spolehlivosti. Místo toho náš kód vypočítá optimalizovanou úroveň spolehlivosti pro váš cluster. Nepokoušejte se nastavit hodnotu pro tuto vlastnost ve verzích 5,7 a vyšší.

* ClusterConfig.Unsecure.DevCluster.jsv a ClusterConfig.Unsecure.MultiMachine.jsna ukazuje, jak vytvořit nezabezpečený testovací nebo produkční cluster.

* ClusterConfig.Windows.DevCluster.jsna a ClusterConfig.Windows.MultiMachine.jsna ukazuje, jak vytvořit testovací nebo produkční clustery, které jsou zabezpečené pomocí [zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.jsna a ClusterConfig.X509.MultiMachine.jsna ukazuje, jak vytvořit testovací nebo produkční clustery, které jsou zabezpečené pomocí [zabezpečení založeného na certifikátech x509](service-fabric-windows-cluster-x509-security.md).

Teď se podívejme na různé části ClusterConfig.jsv souboru.

## <a name="general-cluster-configurations"></a>Obecné konfigurace clusteru
Obecné konfigurace clusteru se týkají rozsáhlých konfigurací specifických pro cluster, jak je znázorněno v následujícím fragmentu kódu JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

K vašemu Service Fabric clusteru můžete zadat libovolný popisný název tak, že ho přiřadíte proměnné název. ClusterConfigurationVersion je číslo verze vašeho clusteru. Zvyšte ho při každém upgradu clusteru Service Fabric. Ponechte apiVersion sadu na výchozí hodnotu.

## <a name="nodes-on-the-cluster"></a>Uzly v clusteru
Uzly v clusteru Service Fabric můžete nakonfigurovat pomocí oddílu uzly, jak ukazuje následující fragment kódu:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Cluster Service Fabric musí obsahovat aspoň tři uzly. Do této části můžete přidat další uzly podle nastavení. Následující tabulka vysvětluje nastavení konfigurace pro každý uzel:

| **Konfigurace uzlů** | **Popis** |
| --- | --- |
| nodeName |Uzlu můžete zadat nějaký popisný název. |
| Adresa |Zjistěte IP adresu vašeho uzlu otevřením okna příkazového řádku a zadáním příkazu `ipconfig` . Poznamenejte si adresu IPV4 a přiřaďte ji k proměnné iPAddress. |
| nodeTypeRef |Každému uzlu je možné přiřadit jiný typ uzlu. [Typy uzlů](#node-types) jsou definovány v následující části. |
| faultDomain |Doména selhání umožňuje správcům clusterů definovat fyzické uzly, které se mohou v současné době selhat v důsledku sdílených fyzických závislostí. |
| upgradeDomain |Upgradovací domény popisují sady uzlů, které jsou vypnuté pro Service Fabric upgrady ve stejnou dobu. Můžete zvolit, které uzly se mají přiřadit k doménám upgradu, protože nejsou omezeny žádnými fyzickými požadavky. |

## <a name="cluster-properties"></a>Vlastnosti clusteru
Oddíl Properties v ClusterConfig.jsv se používá ke konfiguraci clusteru, jak je znázorněno níže:

### <a name="reliability"></a>Spolehlivost
Pojem reliabilityLevel definuje počet replik nebo instancí systémových služeb Service Fabric, které mohou běžet v primárních uzlech clusteru. Určuje spolehlivost těchto služeb a tudíž i cluster. Hodnota se vypočítává systémem při vytváření clusteru a při upgradu.

### <a name="diagnostics"></a>Diagnostika
V části diagnosticsStore můžete nakonfigurovat parametry pro povolení diagnostiky a řešení potíží s uzlem nebo selháním clusteru, jak je znázorněno v následujícím fragmentu kódu: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Metadata jsou popisem diagnostiky clusteru a lze je nastavit podle nastavení. Tyto proměnné pomáhají při shromažďování protokolů trasování ETW a výpisů stavu systému a také čítačů výkonu. Další informace o protokolech trasování ETW najdete v tématu [nástroji tracelog](/windows-hardware/drivers/devtest/tracelog) a [trasování událostí pro Windows](/dotnet/framework/wcf/samples/etw-tracing). Všechny protokoly, včetně [výpisů stavu systému](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) a [čítačů výkonu](/windows/win32/perfctrs/performance-counters-portal), je možné směrovat do složky ConnectionString na vašem počítači. Pomocí AzureStorage můžete také ukládat diagnostiku. Podívejte se na následující vzorový fragment:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Zabezpečení
Část zabezpečení je nezbytná pro zabezpečený samostatný Service Fabric cluster. Následující fragment kódu ukazuje část této části:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Metadata jsou popisem zabezpečeného clusteru a dají se nastavit podle vašich nastavení. ClusterCredentialType a ServerCredentialType určují typ zabezpečení, který cluster a uzly implementují. Můžou být nastavené na hodnotu *x509* pro zabezpečení založené na certifikátech nebo *Windows* pro zabezpečení založené na službě Active Directory. Zbytek části zabezpečení je založen na typu zabezpečení. Informace o tom, jak vyplnit zbytek části zabezpečení, najdete v tématu [zabezpečení založené na certifikátech v samostatném clusteru](service-fabric-windows-cluster-x509-security.md) nebo [zabezpečení systému Windows v samostatném clusteru](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Typy uzlů
Část nodeTypes popisuje typ uzlů, které má váš cluster. Pro cluster je nutné zadat alespoň jeden typ uzlu, jak je znázorněno v následujícím fragmentu kódu: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

Název je popisný název tohoto konkrétního typu uzlu. Chcete-li vytvořit uzel tohoto typu uzlu, přiřaďte jeho popisný název proměnné nodeTypeRef pro tento uzel, jak je [uvedeno výše](#nodes-on-the-cluster). Pro každý typ uzlu definujte koncové body připojení, které se používají. Můžete zvolit libovolné číslo portu pro tyto koncové body připojení, pokud nebudou v konfliktu s jinými koncovými body v tomto clusteru. V clusteru s více uzly je jeden nebo více primárních uzlů (tj. primární je nastaven na *hodnotu true*) v závislosti na [reliabilityLevel](#reliability). Další informace o primárních a neprimárních typech uzlů najdete v tématu [Service Fabric požadavky na plánování kapacity clusteru](service-fabric-cluster-capacity.md) pro informace o uzlech NodeType a reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Koncové body, které slouží ke konfiguraci typů uzlů
* clientConnectionEndpointPort je port používaný klientem pro připojení ke clusteru při použití klientských rozhraní API. 
* clusterConnectionEndpointPort je port, na kterém uzly vzájemně komunikují.
* leaseDriverEndpointPort je port používaný ovladačem zapůjčení clusteru k zjištění, zda jsou uzly stále aktivní. 
* serviceConnectionEndpointPort je port používaný aplikacemi a službami nasazenými na uzlu ke komunikaci s klientem Service Fabric v tomto konkrétním uzlu.
* httpGatewayEndpointPort je port používaný Service Fabric Explorer pro připojení ke clusteru.
* ephemeralPorts přepíše [dynamické porty používané operačním systémem](https://support.microsoft.com/kb/929851). Service Fabric používá součást těchto portů jako aplikační porty a zbývající jsou k dispozici pro operační systém. Tento rozsah je také namapován na stávající rozsah, který je přítomný v operačním systému, takže pro všechny účely můžete použít rozsahy uvedené v ukázkových souborech JSON. Zajistěte, aby byl rozdíl mezi počátečním a koncovým portem minimálně 255. V případě, že je tento rozdíl příliš nízký, můžete spustit konflikty, protože tento rozsah je sdílen s operačním systémem. Chcete-li zobrazit nakonfigurovaný rozsah dynamických portů, spusťte příkaz `netsh int ipv4 show dynamicport tcp` .
* applicationPorts jsou porty používané aplikacemi Service Fabric. Rozsah portů aplikace by měl být dostatečně velký, aby pokryl požadavky vašich aplikací na koncový bod. Tento rozsah by měl být exkluzivní z rozsahu dynamických portů v počítači, to znamená ephemeralPorts rozsah nastavený v konfiguraci. Service Fabric tyto porty používá vždy, když jsou vyžadovány nové porty a postará se o otevření brány firewall pro tyto porty. 
* reverseProxyEndpointPort je volitelný koncový bod reverzního proxy serveru. Další informace najdete v tématu [Service Fabric reverzní proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Nastavení protokolu
V části fabricSettings můžete nastavit kořenové adresáře pro data a protokoly Service Fabric. Tyto adresáře můžete přizpůsobit pouze při počátečním vytváření clusteru. Podívejte se na následující ukázkový fragment kódu této části:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Doporučujeme používat jako FabricDataRoot a FabricLogRoot jednotku, která není typu OS. Nabízí větší spolehlivost při předcházení situacím, kdy operační systém přestane reagovat. Pokud přizpůsobíte pouze kořen dat, bude kořen protokolu umístěn na jednu úroveň pod kořenem dat.

### <a name="stateful-reliable-services-settings"></a>Nastavení stavového Reliable Services
V části KtlLogger můžete nastavit globální nastavení konfigurace pro Reliable Services. Další informace o těchto nastaveních najdete v tématu [Konfigurace stavového Reliable Services](service-fabric-reliable-services-configuration.md). Následující příklad ukazuje, jak změnit protokol sdílené transakce, který se vytvoří pro zálohování všech spolehlivých kolekcí pro stavové služby:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Funkce doplňku
Chcete-li nakonfigurovat funkce doplňku, nakonfigurujte apiVersion jako 04-2017 nebo vyšší a nakonfigurujte addonFeatures, jak je znázorněno zde:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Všechny dostupné funkce doplňku se dají zobrazit v [odkazu Service Fabric REST API](/rest/api/servicefabric/sfrp-model-addonfeatures).

### <a name="container-support"></a>Podpora kontejnerů
Pokud chcete povolit podporu kontejnerů pro kontejnery Windows serveru i pro kontejnery Hyper-V pro samostatné clustery, musí být povolená funkce doplňku služba DNSservice.

## <a name="next-steps"></a>Další kroky
PoClusterConfig.jsdokončení nastavení souboru * na* základě samostatné instalace clusteru můžete nasadit svůj cluster. Postupujte podle kroků v části [vytvoření samostatného Service Fabricho clusteru](service-fabric-cluster-creation-for-windows-server.md). 

Pokud máte nasazen samostatný cluster, můžete také [upgradovat konfiguraci samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md). 

Naučte [se vizualizovat cluster pomocí Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

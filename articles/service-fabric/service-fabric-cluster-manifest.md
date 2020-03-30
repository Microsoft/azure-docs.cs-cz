---
title: Konfigurace samostatného clusteru Azure Service Fabric
description: Zjistěte, jak nakonfigurovat samostatný nebo místní cluster Azure Service Fabric.
author: dkkapur
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: 0f9b625dfbe9c39bea7771dcc5fd58805ce19811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458374"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Nastavení konfigurace samostatného clusteru Windows
Tento článek popisuje nastavení konfigurace samostatného clusteru Azure Service Fabric, který lze nastavit v souboru *ClusterConfig.json.* Tento soubor použijete k určení informací o uzlech clusteru, konfiguracích zabezpečení a také o topologii sítě z hlediska chyb a upgradu domén.  Po změně nebo přidání nastavení konfigurace můžete vytvořit [samostatný cluster](service-fabric-cluster-creation-for-windows-server.md) nebo [inovovat konfiguraci samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).

Při [stahování samostatné služby fabric balíček](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)ClusterConfig.json vzorky jsou také zahrnuty. Ukázky, které mají "DevCluster" v jejich názvech vytvořit cluster se všemi třemi uzly na stejném počítači, pomocí logických uzlů. Z těchto uzlů musí být alespoň jeden označen jako primární uzel. Tento typ clusteru je užitečný pro vývojová nebo testovací prostředí. Není podporován jako produkční cluster. Ukázky, které mají "MultiMachine" v jejich názvech pomáhají vytvářet klastry výrobní třídy, s každým uzlem na samostatném počítači. Počet primárních uzlů pro tyto clustery je založen na [úrovni spolehlivosti](#reliability)clusteru . Ve verzi 5.7, API verze 05-2017, jsme odebrali vlastnost úrovně spolehlivosti. Místo toho náš kód vypočítá nejoptimalizovanější úroveň spolehlivosti pro váš cluster. Nepokoušejte se nastavit hodnotu pro tuto vlastnost ve verzích 5.7 a dále.

* ClusterConfig.Unsecure.DevCluster.json a ClusterConfig.Unsecure.MultiMachine.json ukazují, jak vytvořit nezabezpečený testovací nebo produkční cluster.

* ClusterConfig.Windows.DevCluster.json a ClusterConfig.Windows.MultiMachine.json ukazují, jak vytvořit testovací nebo produkční clustery zabezpečené pomocí [zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json a ClusterConfig.X509.MultiMachine.json ukazují, jak vytvořit testovací nebo produkční clustery, které jsou zabezpečeny pomocí [zabezpečení založeného na certifikátech X509](service-fabric-windows-cluster-x509-security.md).

Nyní pojďme prozkoumat různé části souboru ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Obecné konfigurace clusteru
Obecné konfigurace clusteru zahrnují rozsáhlé konfigurace specifické pro cluster, jak je znázorněno na následujícím fragmentu JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Clusteru Service Fabric můžete přiřadit libovolný popisný název tak, že jej přiřadíte proměnné název. ClusterConfigurationVersion je číslo verze clusteru. Zvyšte jej při každém upgradu clusteru Service Fabric. Ponechat apiVersion nastavena na výchozí hodnotu.

## <a name="nodes-on-the-cluster"></a>Uzly v clusteru
Uzly v clusteru Service Fabric můžete nakonfigurovat pomocí části uzly, jak ukazuje následující úryvek:
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

Cluster Service Fabric musí obsahovat alespoň tři uzly. Do této sekce můžete přidat další uzly podle nastavení. Následující tabulka vysvětluje nastavení konfigurace pro každý uzel:

| **Konfigurace uzlů** | **Popis** |
| --- | --- |
| nodeName |Můžete dát libovolný popisný název uzlu. |
| adresa iP |Ip adresu uzlu zjistíte otevřením příkazového okna `ipconfig`a zadáním . Poznamenejte si adresu IPV4 a přiřaďte ji proměnné iPAddress. |
| nodeTypeRef |Každému uzlu lze přiřadit jiný typ uzlu. [Typy uzlů](#node-types) jsou definovány v následující části. |
| chybaDoména |Domény selhání umožňují správcům clusteru definovat fyzické uzly, které mohou současně selhat kvůli sdíleným fyzickým závislostem. |
| upgradeDoména |Upgradovací domény popisují sady uzlů, které jsou ukončeny pro upgrady Service Fabric přibližně ve stejnou dobu. Můžete zvolit, které uzly chcete přiřadit kterým doménám upgradu, protože nejsou omezeny žádnými fyzickými požadavky. |

## <a name="cluster-properties"></a>Vlastnosti clusteru
Oddíl vlastností v souboru ClusterConfig.json slouží ke konfiguraci clusteru tak, jak je znázorněno:

### <a name="reliability"></a>Spolehlivost
Koncept reliabilityLevel definuje počet replik nebo instancí systémových služeb Service Fabric, které lze spustit na primárních uzlech clusteru. Určuje spolehlivost těchto služeb a tím i clusteru. Hodnota je vypočtena systémem při vytváření clusteru a době upgradu.

### <a name="diagnostics"></a>Diagnostika
V části diagnosticsStore můžete nakonfigurovat parametry pro povolení diagnostiky a odstraňování potíží s chybami uzlu nebo clusteru, jak je znázorněno v následujícím fragmentu: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Metadata jsou popisem diagnostiky clusteru a lze je nastavit podle nastavení. Tyto proměnné pomáhají při shromažďování protokolů trasování ETW a výpisy stavu systému, jakož i čítače výkonu. Další informace o protokolech trasování ETW naleznete v tématu [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) a [Trasování ETW](https://msdn.microsoft.com/library/ms751538.aspx). Všechny protokoly, včetně [výpisů stavu systému](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) a [čítačů výkonu](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), mohou být směrovány do složky connectionString v počítači. AzureStorage můžete také použít k ukládání diagnostiky. Viz následující fragment vzorku:

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
Část zabezpečení je nezbytná pro zabezpečený samostatný cluster Service Fabric. Následující úryvek zobrazuje část této části:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Metadata jsou popisem zabezpečeného clusteru a lze je nastavit podle nastavení. Typ clusteru Typ a Typ serverových pověření určují typ zabezpečení, které cluster a uzly implementují. Lze je nastavit buď na *X509* pro zabezpečení založené na certifikátech, nebo na *systém Windows* pro zabezpečení založené na službě Active Directory. Zbytek části zabezpečení je založen na typu zabezpečení. Informace o tom, jak vyplnit zbytek části zabezpečení, naleznete [v tématu Zabezpečení založené na certifikátech v samostatném clusteru](service-fabric-windows-cluster-x509-security.md) nebo [zabezpečení systému Windows v samostatném clusteru](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Typy uzlů
Část nodeTypes popisuje typ uzlů, které má váš cluster. Pro cluster musí být zadán alespoň jeden typ uzlu, jak je znázorněno v následujícím fragmentu: 

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

Název je popisný název pro tento konkrétní typ uzlu. Chcete-li vytvořit uzel tohoto typu uzlu, přiřaďte jeho popisný název proměnné nodeTypeRef pro tento uzel, jak [již bylo zmíněno](#nodes-on-the-cluster). Pro každý typ uzlu definujte koncové body připojení, které se používají. Můžete zvolit libovolné číslo portu pro tyto koncové body připojení, pokud nejsou v konfliktu s jinými koncovými body v tomto clusteru. V clusteru s více uzly existují jeden nebo více primárních uzlů (to znamená isPrimary je nastavena na *hodnotu true*), v závislosti na [spolehlivostLevel](#reliability). Další informace o primárních a neprimárních typech uzlů naleznete v [tématu Aspekty plánování kapacity clusteru Service Fabric,](service-fabric-cluster-capacity.md) kde najdete informace o typech nodeTypes a reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Koncové body používané ke konfiguraci typů uzlů
* clientConnectionEndpointPort je port používaný klientem pro připojení ke clusteru při použití klientských hraničních propojení. 
* clusterConnectionEndpointPort je port, ve kterém mezi sebou uzly komunikují.
* leaseDriverEndpointPort je port používaný ovladačem zapůjčení clusteru k prohledání, zda jsou uzly stále aktivní. 
* serviceConnectionEndpointPort je port používaný aplikacemi a službami nasazenými v uzlu ke komunikaci s klientem Service Fabric v tomto konkrétním uzlu.
* httpGatewayEndpointPort je port používaný aplikací Service Fabric Explorer pro připojení ke clusteru.
* edemeralPorts přepsat [dynamické porty používané operačního operačního prostoru](https://support.microsoft.com/kb/929851). Service Fabric používá část těchto portů jako porty aplikace a zbývající jsou k dispozici pro operační operační ho. Také mapuje tento rozsah na existující rozsah v os, takže pro všechny účely můžete použít rozsahy uvedené v ukázkových souborech JSON. Ujistěte se, že rozdíl mezi počátečním a koncovým portem je alespoň 255. Pokud je tento rozdíl příliš nízký, může dojít ke konfliktům, protože tento rozsah je sdílen s os. Chcete-li zobrazit nakonfigurovaný rozsah dynamických portů, spusťte . `netsh int ipv4 show dynamicport tcp`
* ports aplikace jsou porty, které jsou používány aplikace Service Fabric. Rozsah portů aplikace by měl být dostatečně velký, aby pokryl požadavek koncového bodu vašich aplikací. Tento rozsah by měl být výhradní z rozsahu dynamických portů v počítači, to znamená rozsah dočasných portů, jak je nastaven v konfiguraci. Service Fabric používá tyto porty vždy, když jsou vyžadovány nové porty a postará se o otevření brány firewall pro tyto porty. 
* reverseProxyEndpointPort je volitelný koncový bod s reverzním proxy serverem. Další informace naleznete v tématu [Service Fabric reverzní proxy](service-fabric-reverseproxy.md)server . 

### <a name="log-settings"></a>Nastavení protokolu
V části fabricSettings můžete nastavit kořenové adresáře pro data a protokoly Service Fabric. Tyto adresáře můžete přizpůsobit pouze během počátečního vytvoření clusteru. Viz následující ukázkový úryvek v této části:

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

Doporučujeme použít jednotku bez operačního ého operačního ého operačního ého jako FabricDataRoot a FabricLogRoot. Poskytuje větší spolehlivost při předcházení situacím, kdy operační systém přestane reagovat. Pokud upravíte pouze kořen dat, kořen protokolu se umístí o jednu úroveň pod kořen dat.

### <a name="stateful-reliable-services-settings"></a>Nastavení spolehlivých spolehlivých služeb
V části KtlLogger můžete nastavit globální nastavení konfigurace pro spolehlivé služby. Další informace o těchto nastaveních naleznete v [tématu Configure Stateful Reliable Services](service-fabric-reliable-services-configuration.md). Následující příklad ukazuje, jak změnit sdílený transakční protokol, který získá vytvořen zpět všechny spolehlivé kolekce pro stavové služby:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Doplňkové funkce
Chcete-li konfigurovat funkce doplňků, nakonfigurujte apiVersion jako 04-2017 nebo vyšší a nakonfigurujte funkce doplňků, jak je znázorněno zde:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Všechny dostupné funkce doplňků lze vidět v [service fabric REST API reference](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-addonfeatures).

### <a name="container-support"></a>Podpora kontejnerů
Chcete-li povolit podporu kontejnerů pro kontejnery Windows Server i Hyper-V pro samostatné clustery, musí být povolena funkce doplňku DnsService.

## <a name="next-steps"></a>Další kroky
Po nakonfigurování úplného souboru *ClusterConfig.json* podle samostatného nastavení clusteru můžete cluster nasadit. Postupujte podle pokynů v [části Vytvoření samostatného clusteru Service Fabric](service-fabric-cluster-creation-for-windows-server.md). 

Pokud máte nasazený samostatný cluster, můžete také [upgradovat konfiguraci samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md). 

Přečtěte si, jak [vizualizovat cluster pomocí aplikace Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).


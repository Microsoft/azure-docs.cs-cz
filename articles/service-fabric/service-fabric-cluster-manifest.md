---
title: Konfigurace samostatného clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat samostatná jednotka nebo v místním clusteru Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: c71473e975333d33406d78130ad28f417b9b967e
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853332"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Nastavení konfigurace pro samostatný cluster Windows
Tento článek popisuje konfiguraci nastavení samostatného clusteru Azure Service Fabric, který je možné nastavit v *ClusterConfig.json* souboru. Tento soubor použije k zadání informací o uzlech clusteru, konfigurace zabezpečení, jakož i síťové topologie z hlediska chybových nebo upgradovacích doménách.  Po změně nebo přidání nastavení konfigurace, můžete je buď [vytvoření samostatného clusteru](service-fabric-cluster-creation-for-windows-server.md) nebo [upgradovat konfiguraci samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md).

Pokud jste [stáhnout samostatného balíčku Service Fabric](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), ClusterConfig.json ukázky jsou zahrnuté také. Ukázky, které mají v názvu "DevCluster" vytvořit cluster s všechny tři uzly ve stejném počítači, pomocí logické uzly. Z těchto uzlů musí být alespoň jeden označen jako primárního uzlu. Tento typ clusteru je užitečné pro vývojové nebo testovací prostředí. Není podporován jako cluster pro produkční prostředí. Ukázky, které mají v názvu "MultiMachine" Vytvoření produkčních clusterů na podnikové úrovni, s každý uzel v samostatném počítači. Počet hlavní uzly, které tyto clustery je založen na clusteru [úroveň spolehlivosti](#reliability). Ve verzi 5.7, verze rozhraní API 05-2017, jsme odebrali vlastnost úroveň spolehlivosti. Místo toho náš kód vypočítá největší optimalizované úroveň spolehlivosti pro váš cluster. Nepokoušejte se nastavit hodnotu pro tuto vlastnost ve verzích 5.7 a vyšší.

* ClusterConfig.Unsecure.DevCluster.json a ClusterConfig.Unsecure.MultiMachine.json ukazují, jak vytvořit nezabezpečený testovací nebo produkční cluster.

* ClusterConfig.Windows.DevCluster.json a ClusterConfig.Windows.MultiMachine.json ukazují, jak vytvořit testovací nebo produkční clustery, které jsou zabezpečené pomocí [zabezpečení Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json a ClusterConfig.X509.MultiMachine.json ukazují, jak vytvořit testovací nebo produkční clustery, které jsou zabezpečené pomocí [X509 zabezpečení na základě certifikátů](service-fabric-windows-cluster-x509-security.md).

Teď se podíváme na různé části ClusterConfig.json souboru.

## <a name="general-cluster-configurations"></a>Konfigurace obecných clusteru
Obecné Clusterové konfigurace zahrnují široké konfigurace specifických pro cluster, jak je znázorněno v následujícím fragmentu kódu JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Jakýkoli popisný název do vašeho clusteru Service Fabric nabídnou přiřazením k názvu proměnné. ClusterConfigurationVersion je číslo verze vašeho clusteru. Zvětšete pokaždé, když se upgrade clusteru Service Fabric. Verze rozhraní API sady ponechte výchozí hodnotu.

## <a name="nodes-on-the-cluster"></a>Uzly v clusteru
Uzly ve vašem clusteru Service Fabric můžete nakonfigurovat pomocí uzlů oddílu, jak ukazuje následující fragment kódu:
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

Cluster Service Fabric musí obsahovat alespoň tři uzly. Do této části můžete přidat více uzlů podle vašeho nastavení. Následující tabulka popisuje nastavení konfigurace pro každý uzel:

| **Konfigurace uzlu** | **Popis** |
| --- | --- |
| nodeName |Můžete přiřadit libovolný popisný název k uzlu. |
| IP adresa |Zjistěte IP adresu vašeho uzlu tak, že otevřete okno příkazového řádku a zadáte `ipconfig`. Poznamenejte si IPV4 adresu a přiřadíte ho k proměnné iPAddress. |
| nodeTypeRef |Každý uzel je možné přiřadit typ jiný uzel. [Typy uzlů](#node-types) jsou definovány v následující části. |
| faultDomain |Domény selhání umožňují správcům clusteru k definování fyzických uzlů, které může selhat z důvodu sdílené fyzické závislosti současně. |
| upgradeDomain |Upgradovací domény popisují sady uzlů, které se vypnutí pro upgrade na Service Fabric na přibližně ve stejnou dobu. Uzly, na kterých chcete přiřadit které upgradovacích domén, můžete zvolit, protože nejsou omezeny libovolné fyzické požadavky. |

## <a name="cluster-properties"></a>Vlastnosti clusteru
Oddílu Vlastnosti. ClusterConfig.json slouží ke konfiguraci clusteru, jak je znázorněno:

### <a name="reliability"></a>Spolehlivost
Koncept reliabilityLevel definuje počet replik nebo instancí systémové služby Service Fabric, které lze spustit na hlavní uzly clusteru. Určuje spolehlivost tyto služby a clusteru. Hodnota se počítá v systému v době vytváření a upgradu clusteru.

### <a name="diagnostics"></a>Diagnostika
V části diagnosticsStore můžete nakonfigurovat parametry se mají povolit diagnostiku a řešení potíží se selháním uzlu nebo v clusteru, jak je znázorněno v následujícím fragmentu kódu: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

Metadata je popis diagnostickou clusteru a lze nastavit podle vašeho nastavení. Tyto proměnné pomoci při shromažďování trasování událostí pro Windows trasování protokolů a výpisů stavu a také čítačů výkonu. Další informace o trasování protokolů trasování událostí pro Windows najdete v tématu [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) a [trasování událostí pro Windows](https://msdn.microsoft.com/library/ms751538.aspx). Všechny protokoly, včetně [výpisy stavu systému](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) a [čítače výkonu](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), mohou být přesměrováni na připojovací řetězec složky na svém počítači. AzureStorage můžete použít také pro ukládání diagnostiky. Viz následující ukázka fragmentu kódu:

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
V části zabezpečení je nezbytné pro zabezpečení samostatného clusteru Service Fabric. Následující fragment kódu ukazuje část této části:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

Metadata je popis svému zabezpečenému clusteru a lze nastavit podle vašeho nastavení. ClusterCredentialType a ServerCredentialType určit typ zabezpečení, které cluster a uzly implementovat. To můžete udělat buď *X509* pro zabezpečení na základě certifikátů nebo *Windows* pro zabezpečení na základě Azure Active Directory. Zbytek části zabezpečení je založena na typu zabezpečení. Informace o tom, jak vyplnit zbývající části zabezpečení najdete v tématu [zabezpečení na základě certifikátů v samostatného clusteru](service-fabric-windows-cluster-x509-security.md) nebo [Windows zabezpečení samostatného clusteru](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Typy uzlů
Oddíle nodeTypes popisuje typ uzly, které má váš cluster. Je nutné zadat typ nejméně jeden uzel clusteru, jak je znázorněno v následujícím fragmentu kódu: 

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

Název je popisný název pro tento typ konkrétní uzel. Můžete vytvořit uzel tohoto typu uzlu, přiřadit svůj popisný název pro proměnnou nodeTypeRef pro tento uzel jako [už jsme zmínili](#nodes-on-the-cluster). Pro každý typ uzlu definujte koncové body připojení, které se používají. Můžete použít libovolné číslo portu pro tyto koncové body připojení a za předpokladu, že nejsou v konfliktu se žádné koncové body v tomto clusteru. V s víc uzly clusteru, jsou nejmíň jeden hlavní uzly (tedy isPrimary nastavená na *true*), v závislosti na [reliabilityLevel](#reliability). Další informace o typech primární a neprimárních uzlů najdete v tématu [informace o plánování kapacity clusteru Service Fabric](service-fabric-cluster-capacity.md) informace o nodeTypes a reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Koncové body, které slouží ke konfiguraci typy uzlů
* clientConnectionEndpointPort je port používaný klientem pro připojení ke clusteru, pokud jsou použity klientské rozhraní API. 
* clusterConnectionEndpointPort je port, ve kterém uzly komunikovat mezi sebou.
* leaseDriverEndpointPort je port používaný programem ovladač zapůjčení clusteru a zjistěte, pokud jsou stále aktivní uzly. 
* serviceConnectionEndpointPort je port používaný ke komunikaci s klientem nástroje Service Fabric v tomto konkrétním uzlu aplikací a služeb nasazených na uzlu.
* httpGatewayEndpointPort je port používat pro připojení ke clusteru Service Fabric Explorer.
* ephemeralPorts přepsat [dynamické porty, které používají operační systém](https://support.microsoft.com/kb/929851). Service Fabric používá součástí těchto portů jako porty aplikací a zbývající jsou k dispozici pro operační systém. Také mapuje tento rozsah na existující oblast k dispozici v operačním systému, takže pro všechny účely můžete použít rozsahy adres, uvedený v ukázkové soubory JSON. Ujistěte se, že je rozdíl mezi počáteční a koncové porty alespoň 255. Můžete narazit na konflikty, pokud tento rozdíl je příliš nízké, protože tento rozsah je sdílen s operačním systémem. Pokud chcete zobrazit rozsah dynamických portů nakonfigurované, spusťte `netsh int ipv4 show dynamicport tcp`.
* applicationPorts jsou porty, které jsou používány aplikací Service Fabric. Rozsahu portů aplikace by měla být dostatečně velký pro pokrytí požadavku koncového bodu vašich aplikací. Tento rozsah by měl být výhradně z rozsah dynamických portů na počítači, to znamená, že rozsah ephemeralPorts nastavený v konfiguraci. Service Fabric používá následující porty pokaždé, když nové porty jsou povinné a se postará o otevření brány firewall pro tyto porty. 
* reverseProxyEndpointPort je koncový bod volitelné reverzního proxy serveru. Další informace najdete v tématu [Service Fabric reverzní proxy server](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Nastavení protokolu
V části Nastavení fabricSettings můžete nastavit kořenové adresáře pro Service Fabric dat a protokolů. Tyto adresáře můžete přizpůsobit pouze při vytváření počátečního clusteru. Prohlédněte si následující fragment kódu ukázka tohoto oddílu:

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

Doporučujeme použít jednotky bez operačního systému jako FabricDataRoot a FabricLogRoot. Nabízí spolehlivější předcházet situace, když operační systém přestane reagovat. Pokud upravíte pouze kořen dat, kořenový adresář protokolu je umístěn o jednu úroveň pod kořen dat.

### <a name="stateful-reliable-services-settings"></a>Nastavení stavového modelu Reliable Services
V části KtlLogger můžete nastavit globální nastavení konfigurace pro služby Reliable Services. Další informace o těchto nastaveních najdete v tématu [nakonfigurovat stavové služby Reliable Services](service-fabric-reliable-services-configuration.md). Následující příklad ukazuje, jak změnit sdílené transakčního protokolu, která se vytvoří zálohovat všechny spolehlivé kolekce pro stavové služby:

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
Ke konfiguraci funkcí doplňku, nakonfigurujte verze rozhraní API jako 04-2017 nebo novější a nakonfigurovat addonFeatures, jak je znázorněno zde:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```

### <a name="container-support"></a>Podpora kontejnerů
Povolení podpory kontejneru pro kontejnery Windows serveru a kontejnery Hyper-V pro samostatné clustery, musí být povolena funkce doplňku služba DnsService.

## <a name="next-steps"></a>Další postup
Až budete mít kompletní *ClusterConfig.json* soubor nakonfigurovaný podle nastavení samostatného clusteru můžete nasadit do clusteru. Postupujte podle kroků v [vytvoření samostatného clusteru Service Fabric](service-fabric-cluster-creation-for-windows-server.md). 

Pokud máte samostatný cluster nasadit, můžete si také [upgradovat konfiguraci samostatného clusteru](service-fabric-cluster-config-upgrade-windows-server.md). 

Zjistěte, jak [Vizualizujte cluster pomocí Service Fabric Exploreru](service-fabric-visualizing-your-cluster.md).


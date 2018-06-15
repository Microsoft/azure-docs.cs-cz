---
title: Konfigurace clusteru samostatné Azure Service Fabric | Microsoft Docs
description: Zjistěte, jak nakonfigurovat samostatnou nebo na místní cluster Azure Service Fabric.
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
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e0fed608ac9dd02a6fe5563eefc30edb63d224b1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205361"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Nastavení konfigurace pro samostatné clusteru se systémem Windows
Tento článek popisuje postup konfigurace clusteru s podporou samostatné Azure Service Fabric pomocí souboru souboru ClusterConfig.json. Tento soubor použijete k zadání informací o uzly clusteru, konfigurace zabezpečení, jakož i topologie sítě z hlediska selhání a upgradu domény.

Pokud jste [Stáhnout balíček Service Fabric samostatné](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), souboru ClusterConfig.json ukázky jsou také zahrnuté. Ukázky, které jsou v jejich názvy "DevCluster" Vytvoření clusteru s všechny tři uzly na stejném počítači, pomocí logických uzlů. Mimo tyto uzly alespoň jeden musí být označen jako primárního uzlu. Tento typ clusteru je užitečné pro vývoj nebo testovací prostředí. Není podporováno jako provozní cluster. Ukázky, které jsou v jejich názvy "MultiMachine" pomoci vytvořit produkční úrovni clustery s každý uzel na samostatný počítač. Počet uzlů primární pro tyto clustery je založen na clusteru [úroveň spolehlivosti](#reliability). Ve verzi 5.7, verze rozhraní API 05-2017, jsme odebrali vlastnost úroveň spolehlivosti. Místo toho našem kódu, vypočítá úroveň spolehlivosti nejvíce optimalizované pro váš cluster. Nepokoušejte se nastavit hodnotu pro tuto vlastnost ve verzích 5.7 a vyšší.


* ClusterConfig.Unsecure.DevCluster.json a ClusterConfig.Unsecure.MultiMachine.json ukazují, jak vytvořit testovací zabezpečená nebo provozní cluster.

* ClusterConfig.Windows.DevCluster.json a ClusterConfig.Windows.MultiMachine.json ukazují, jak vytvořit testovací nebo produkční clustery, které jsou zabezpečené pomocí [zabezpečení systému Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig.X509.DevCluster.json a ClusterConfig.X509.MultiMachine.json ukazují, jak vytvořit testovací nebo produkční clustery, které jsou zabezpečené pomocí [X509 zabezpečení na základě certifikátu](service-fabric-windows-cluster-x509-security.md).

Teď se podíváme na různé části souboru souboru ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Konfigurace obecných clusteru
Konfigurace obecných clusteru zahrnují široké konfigurace specifických pro cluster, jak je znázorněno v následujícím fragmentu kódu JSON:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

Přiřazením název proměnné, můžete zajistit libovolný popisný název pro váš cluster Service Fabric. ClusterConfigurationVersion je číslo verze vašeho clusteru. Pokaždé, když upgradujete cluster Service Fabric, zvýšit jeho. Ponechte apiVersion sadu na výchozí hodnotu.

## <a name="nodes-on-the-cluster"></a>Uzly v clusteru

    <a id="clusternodes"></a>

Můžete nakonfigurovat uzly v clusteru Service Fabric pomocí části uzly jako následující fragment kódu ukazuje:

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

Cluster Service Fabric musí obsahovat alespoň tři uzly. Do této části můžete přidat další uzly, podle vašeho nastavení. Následující tabulka popisuje nastavení konfigurace pro každý uzel:

| **Konfigurace uzlu** | **Popis** |
| --- | --- |
| nodeName |Do uzlu můžete dát libovolný popisný název. |
| IP adresa |Zjistěte, tím otevřete okno příkazového řádku a zadáte IP adresu vašeho uzlu `ipconfig`. Poznamenejte si adresu IPV4 a přiřadit ji k proměnné iPAddress. |
| nodeTypeRef |Každý uzel může být přiřazen jiný uzel typu. [Typy uzlů](#node-types) jsou definovány v následující části. |
| faultDomain |Domén selhání umožňují správcům clusteru definování fyzických uzlů, které může selhat z důvodu sdílené fyzické závislosti současně. |
| upgradeDomain |Domén upgradu popisují sadu uzlů, které jsou vypnuté upgradů Service Fabric v přibližně ve stejnou dobu. Uzlů, které přiřadit které upgradu domény, můžete vybrat, protože nejsou omezeny všechny fyzické požadavky. |

## <a name="cluster-properties"></a>Vlastnosti clusteru
V části vlastnosti v souboru ClusterConfig.json slouží ke konfiguraci clusteru, jak je znázorněno:

### <a name="reliability"></a>Spolehlivost
Koncept reliabilityLevel definuje počet replik nebo instancí systémových služeb Service Fabric, které můžou běžet na primární uzlů v clusteru. Určuje spolehlivost tyto služby a proto clusteru. Hodnota je vypočítána systému v době vytváření a upgrade clusteru.

    <a id="reliability"></a>

### <a name="diagnostics"></a>Diagnostika
V části diagnosticsStore můžete nakonfigurovat parametry, které umožňují diagnostiku a řešení potíží s selhání uzlu nebo clusteru, jak je znázorněno v následujícím fragmentu kódu: 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Metadata popis vašeho clusteru diagnostiky a můžete nastavit podle vašeho nastavení. Tyto proměnné pomáhají při shromažďování protokolů trasování ETW a výpisů stavu systému a také čítače výkonu. Další informace o trasování událostí pro Windows protokolů trasování najdete v tématu [protokolu trasování](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) a [trasování událostí pro Windows](https://msdn.microsoft.com/library/ms751538.aspx). Všechny protokoly, včetně [havárií výpisy](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) a [čítače výkonu](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx), může přesměrovat do složky connectionString na váš počítač. Azurestorage také můžete použít k uložení diagnostiky. Viz následující ukázka fragment kódu:

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Zabezpečení
Je nezbytné pro cluster Service Fabric zabezpečené samostatné části zabezpečení. Následující fragment kódu ukazuje součástí této části:

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Metadata popis zabezpečení clusteru a můžete nastavit podle vašeho nastavení. ClusterCredentialType a ServerCredentialType Určuje typ zabezpečení, které cluster a uzly implementovat. Se může být nastaven na hodnotu *X509* pro zabezpečení na základě certifikátů nebo *Windows* pro zabezpečení na základě Azure Active Directory. Zbývající část zabezpečení je založený na typu zabezpečení. Informace o tom, jak vyplnění zbývající části zabezpečení najdete v tématu [zabezpečení na základě certifikátů v clusteru s podporou samostatné](service-fabric-windows-cluster-x509-security.md) nebo [zabezpečení systému Windows v clusteru s podporou samostatné](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Typy uzlů

    <a id="nodetypes"></a>

NodeTypes část popisuje typ uzlů, které má váš cluster. Je třeba zadat alespoň jeden uzel typ pro cluster, jak je znázorněno v následujícím fragmentu kódu: 

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

Název je popisný název pro tento typ konkrétním uzlu. Můžete vytvořit uzel typu uzlu, přiřadit jako jeho popisný název pro proměnnou nodeTypeRef pro tento uzel [výše](#nodes-on-the-cluster). Pro každý typ uzlu definujte koncové body připojení, které se používají. Jakékoli číslo portu pro tyto koncové body připojení můžete vybrat tak dlouho, dokud nejsou v konfliktu s žádné koncové body v tomto clusteru. Cluster s více uzly jsou jeden nebo více uzlů primární (tedy isPrimary nastavena na *true*), v závislosti na [reliabilityLevel](#reliability). Další informace o typech primární a nonprimary uzlu najdete v tématu [aspekty plánování kapacity služby cluster Service Fabric](service-fabric-cluster-capacity.md) informace o nodeTypes a reliabilityLevel. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Koncové body, které slouží ke konfiguraci typy uzlů
* clientConnectionEndpointPort je port používaný klientem pro připojení ke clusteru, pokud se používá rozhraní API klienta. 
* clusterConnectionEndpointPort je port, kde se uzly vzájemně komunikovat.
* leaseDriverEndpointPort je port je používán ovladač zapůjčení clusteru a zjistěte, zda jsou stále aktivní uzly. 
* serviceConnectionEndpointPort je port používaný ke komunikaci s klientem nástroje Service Fabric na příslušném uzlu aplikace a služby, které jsou nasazeny na uzlu.
* httpGatewayEndpointPort je port používaný pro připojení ke clusteru Service Fabric Exploreru.
* přepsání ephemeralPorts [dynamické porty používané operačního systému](https://support.microsoft.com/kb/929851). Service Fabric používá část tyto porty jako porty aplikace a zbývající jsou k dispozici pro operační systém. Také mapuje tento rozsah na existující rozsah, který je součástí operačního systému, takže pro všechny účely, můžete používat rozsahy uvedenému v ukázkové soubory JSON. Ujistěte se, že je rozdíl mezi počáteční a koncové porty alespoň 255. Je v konfliktu se mohou vyskytnout, pokud tento rozdíl je příliš nízké, protože tento rozsah je sdílen s operačním systémem. Rozsah nakonfigurované dynamických portů najdete spustit `netsh int ipv4 show dynamicport tcp`.
* applicationPorts jsou porty, které jsou používány aplikací Service Fabric. Rozsah portů aplikace by měl být dostatečně velký pro požadavek koncový bod, vaše aplikace. Tento rozsah musí být výhradní z rozsahu dynamický port na počítači, který je rozsah ephemeralPorts jako sada v konfiguraci. Service Fabric používá tyto porty vždy, když nové porty jsou povinné a má na starosti otevření brány firewall pro tyto porty. 
* reverseProxyEndpointPort je koncový bod volitelné reverzní proxy server. Další informace najdete v tématu [Service Fabric reverse proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Nastavení protokolu
V části fabricSettings můžete nastavit kořenové adresáře pro data Service Fabric a protokoly. Tyto adresáře je možné upravit pouze při vytváření počáteční clusteru. Viz následující ukázka fragment v této části:

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Doporučujeme použít jednotku operačního systému jako proměnná FabricDataRoot a adresáři FabricLogRoot. Poskytuje další spolehlivost v zabraňující situace, když je operačním systémem přestane reagovat. Pokud upravíte pouze kořenové datové, kořenový adresář protokolu je umístěn jednu úroveň pod kořenovým adresářem data.

### <a name="stateful-reliable-services-settings"></a>Stavová nastavení spolehlivé služby
V části KtlLogger můžete nastavit globální konfiguraci nastavení pro spolehlivé služby. Další informace o těchto nastaveních najdete v tématu [nakonfigurovat stavové služby Reliable Services](service-fabric-reliable-services-configuration.md). Následující příklad ukazuje, jak změnit sdílené transakčního protokolu, která je vytvořena zálohovat všechny spolehlivé kolekce pro stavové služby:

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>Funkce rozšíření
Ke konfiguraci rozšíření funkcí, nakonfigurujte apiVersion jako 04 2017 nebo vyšší a nakonfigurovat addonFeatures, jak je vidět tady:

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>Podpora kontejnerů
Povolení podpory kontejner pro Windows Server kontejnery a kontejnery technologie Hyper-V pro samostatné clustery, musí být povolena funkce rozšíření DnsService.


## <a name="next-steps"></a>Další postup
Po dokončení soubor souboru ClusterConfig.json nakonfigurovaný podle vaší samostatné nastavení clusteru, můžete nasadit cluster. Postupujte podle kroků v [vytvořit cluster Service Fabric samostatné](service-fabric-cluster-creation-for-windows-server.md). Pak pokračujte s [vizualizace clusteru pomocí Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) a postupujte podle pokynů.


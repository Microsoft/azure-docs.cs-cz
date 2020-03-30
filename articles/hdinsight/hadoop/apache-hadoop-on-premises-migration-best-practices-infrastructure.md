---
title: 'Infrastruktura: Místní Apache Hadoop do Azure HDInsight'
description: Seznamte se s osvědčenými postupy infrastruktury pro migraci místních clusterů Hadoop do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: d7ee8ae121e3cbb9760a87c95d12109a9b05e0c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951509"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrace místních clusterů Apache Hadoop do Azure HDInsight – osvědčené postupy pro infrastrukturu

Tento článek obsahuje doporučení pro správu infrastruktury clusterů Azure HDInsight. Je součástí řady, která poskytuje osvědčené postupy, které vám pomohou s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Plánování kapacity clusteru HDInsight

Klíčové volby pro plánování kapacity clusteru HDInsight jsou následující:

**Oblasti**  
Oblast Azure určuje, kde je cluster fyzicky zřízena. Chcete-li minimalizovat latenci čtení a zápisů, cluster by měl být ve stejné oblasti jako data.

**Umístění a velikost úložiště**  
Výchozí úložiště musí být ve stejné oblasti jako cluster.Pro cluster s 48 uzlovými daty se doporučuje mít 4 až 8 účtů úložiště. I když již může být dostatečné celkové úložiště, každý účet úložiště poskytuje další šířku pásma sítě pro výpočetní uzly. Pokud existuje více účtů úložiště, použijte náhodný název pro každý účet úložiště bez předpony. Účelem náhodného pojmenování je snížení pravděpodobnosti kritických míst úložiště (omezení) nebo selhání běžného režimu ve všech účtech. Pro lepší výkon použijte pouze jeden kontejner na účet úložiště.

**Velikost a typ virtuálního počítače (nyní podporuje řadu G)**  
Každý typ clusteru má sadu typů uzlů a každý typ uzlu má specifické možnosti pro jejich velikost a typ virtuálního počítače. Velikost a typ virtuálního počítače je určen výkonem procesoru, velikostí paměti RAM a latencí sítě. Simulované zatížení lze použít k určení optimální velikosti virtuálního počítače a typu pro každý typ uzlu.

**Počet pracovních uzlů**  
Počáteční počet pracovních uzlů lze určit pomocí simulovaných úloh. Cluster uvažuje později přidáním dalších pracovních uzlů, aby splňovaly požadavky na zatížení ve špičce. Cluster umocněn později, když nejsou vyžadovány další pracovní uzly.

Další informace naleznete v článku [Plánování kapacity pro clustery HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Použití doporučeného typu virtuálního počítače pro cluster

Viz [Výchozí konfigurace uzlu a velikosti virtuálních počítačů pro clustery](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) pro doporučené typy virtuálních počítačů pro každý typ clusteru HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Kontrola dostupnosti součástí Hadoop u HDInsight

Každá verze HDInsight je cloudová distribuce sady komponent ekosystému Hadoop. Podrobnosti o všech komponentách HDInsight a jejich aktuálních verzích najdete v tématu [Správa verzí komponent HDInsight.](../hdinsight-component-versioning.md)

Můžete také použít Apache Ambari UI nebo Ambari REST API ke kontrole hadoopkomponenty a verze v HDInsight.

Aplikace nebo součásti, které byly dostupné v místních clusterech, ale nejsou součástí clusterů HDInsight, se dají přidat na hraniční uzel nebo na virtuální počítač ve stejné virtuální síti jako cluster HDInsight. Aplikace Hadoop jiného výrobce, která není k dispozici v Azure HDInsight lze nainstalovat pomocí možnosti "Aplikace" v clusteru HDInsight. Vlastní hadoopové aplikace lze nainstalovat do clusteru HDInsight pomocí "akcí skriptů". V následující tabulce jsou uvedeny některé běžné aplikace a jejich možnosti integrace HDInsight:

|**Aplikace**|**Integrace**
|---|---|
|Proudění vzduchu|Hraniční uzel IaaS nebo HDInsight
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Žádné (pouze HDP)
|Datameer|Hraniční uzel HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB alternativa v Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Zapálit|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB alternativa v Azure)
|NiFi|IaaS 
|Presto|Hraniční uzel IaaS nebo HDInsight
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|Sas|IaaS 
|Vertica|IaaS (SQLDW alternativa v Azure)
|Tableau|IaaS 
|Vodoryskou|Hraniční uzel HDInsight
|Sady datových proudů|Hrana HDInsight 
|Palantir (Pallač)|IaaS 
|Místo plachty|Iaas 

Další informace naleznete v článku [Součásti Apache Hadoop dostupné v různých verzích HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Přizpůsobení clusterů HDInsight pomocí akcí skriptů

HDInsight poskytuje metodu konfigurace clusteru nazvanou **akce skriptu**. Akce skriptu je bash skript, který běží na uzlech v clusteru HDInsight a lze jej použít k instalaci dalších součástí a změnit nastavení konfigurace.

Akce skriptu musí být uloženy na identifikátoru URI, který je přístupný z clusteru HDInsight. Lze je použít během nebo po vytvoření clusteru a lze je také omezit na spuštění pouze na určitých typech uzlů.

Skript může být trvalá nebo spustit jednou. Trvalé skripty se používají k přizpůsobení nových pracovních uzlů přidaných do clusteru prostřednictvím operací škálování. Trvalý skript může také použít změny pro jiný typ uzlu, jako je například hlavní uzel, když dojde k operacím škálování.

HDInsight poskytuje předem napsané skripty pro instalaci následujících součástí do clusterů HDInsight:

- Přidání účtu Azure Storage
- Instalace odstínu
- Instalace aplikace Presto
- Instalace Solr
- Instalace Giraph
- Předběžné načtení knihoven Hive
- Instalace nebo aktualizace Mono

> [!Note]  
> HDInsight neposkytuje přímou podporu pro vlastní hadoop komponenty nebo součásti nainstalované pomocí akce skriptu.

Akce skriptu se dají taky publikovat na Azure Marketplace jako hdinsightovou aplikaci.

Další informace najdete v těchto článcích:

- [Instalace aplikací Apache Hadoop třetích stran na HDInsight](../hdinsight-apps-install-applications.md)
- [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publikování aplikace HDInsight na Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Přizpůsobení konfigurací HDInsight pomocí Bootstrapu

Změny konfiguračních souborů v `core-site.xml`konfiguračních souborech, jako je například , `hive-site.xml` a `oozie-env.xml` lze je provést pomocí bootstrapu. Následující skript je příkladem pomocí rutiny modulu Powershell [AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) [New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster):

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Další informace naleznete v článku [Přizpůsobení clusterů HDInsight pomocí bootstrapu](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Viz také [Správa clusterů HDInsight pomocí rozhraní Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Přístup ke klientským nástrojům z hraničních uzlů clusteru HDInsight Hadoop

Prázdný hraniční uzel je virtuální počítač Linuxu se stejnými klientskými nástroji nainstalovanými a nakonfigurovanými jako na hlavních uzlech, ale bez spuštěných služeb Hadoop. Hraniční uzel lze použít pro následující účely:

- přístup ke clusteru
- testování klientských aplikací
- hostování klientských aplikací

Hraniční uzly lze vytvořit a odstranit prostřednictvím portálu Azure a lze je použít během nebo po vytvoření clusteru. Po vytvoření hraničního uzlu se můžete připojit k hraničnímu uzlu pomocí SSH a spustit klientské nástroje pro přístup ke clusteru Hadoop v HDInsight. Koncový bod hraničního uzlu `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`ssh je .


Další informace naleznete v článku [Použití prázdných hraničních uzlů v clusterech Apache Hadoop v HDInsightu](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Použití funkce škálování nahoru a škálování clusterů

HDInsight poskytuje pružnost tím, že vám možnost vertikálně navýšit kapacitu a vertikálně snížit počet pracovních uzlů ve vašich clusterech. Tato funkce umožňuje zmenšit cluster po hodinách nebo o víkendech a rozšířit jej během špičkových obchodních požadavků. Další informace naleznete v tématu:

* [Škálujte clustery HDInsight](../hdinsight-scaling-best-practices.md).
* [Měřítko clusterů](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Použití HDInsightu s virtuální sítí Azure

Virtuální sítě Azure umožňují prostředkům Azure, jako jsou virtuální počítače Azure, bezpečně komunikovat mezi sebou, přes internet a místní sítě filtrováním a směrováním síťových přenosů.

Použití virtuální sítě Azure s HDInsight umožňuje následující scénáře:

- Připojení k HDInsight přímo z místní sítě.
- Připojení HDInsightu k datovým úložištím ve virtuální síti Azure.
- Přímý přístup ke službám Hadoop, které nejsou veřejně dostupné přes internet. Například rozhraní API Kafka nebo rozhraní HBase Java API.

HDInsight lze přidat do nové nebo existující virtuální sítě Azure. Pokud se HDInsight přidává do existující virtuální sítě, je třeba aktualizovat stávající skupiny zabezpečení sítě a uživatelem definované trasy, aby byl v datovém centru Azure umožněn neomezený přístup k [několika IP adresám.](../hdinsight-management-ip-addresses.md) Také se ujistěte, že blokovat provoz na [porty](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports), které jsou používány službami HDInsight.

> [!Note]  
> HDInsight v současné době nepodporuje vynucené tunelové propojení. Vynucené tunelové propojení je nastavení podsítě, které vynutí odchozí internetový provoz do zařízení pro kontrolu a protokolování. Před instalací hdinsightu do podsítě odeberte vynucené tunelování nebo vytvořte novou podsíť pro HDInsight. HDInsight také nepodporuje omezení odchozí připojení k síti.

Další informace najdete v těchto článcích:

- [Přehled virtuálních sítí Azure](../../virtual-network/virtual-networks-overview.md)
- [Rozšíření služby Azure HDInsight pomocí služby Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Bezpečné připojení ke službám Azure pomocí koncových bodů služby Azure Virtual Network

HDInsight podporuje [koncové body virtuální síťové služby](../../virtual-network/virtual-network-service-endpoints-overview.md), které umožňují bezpečné připojení k Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB a SQL databázím. Povolením koncového bodu služby pro Azure HDInsight, provoz toky přes zabezpečené trasy z datového centra Azure. S touto zvýšenou úrovní zabezpečení na síťové vrstvě můžete uzamknout účty úložiště velkých objemů dat do jejich určených virtuálních sítí (VNET) a stále používat clustery HDInsight bez problémů pro přístup k těmto datům a jejich zpracování.

Další informace najdete v těchto článcích:

- [Koncové body služby pro virtuální síť](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Vylepšete zabezpečení HDInsight pomocí koncových bodů služby](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Připojení HDInsightu k místní síti

HDInsight lze připojit k místní síti pomocí virtuálních sítí Azure a brány VPN. K navázání připojení lze použít následující kroky:

- HdInsight můžete používat ve virtuální síti Azure, která má připojení k místní síti.
- Konfigurace překladu názvů DNS mezi virtuální sítí a místní sítí.
- Nakonfigurujte skupiny zabezpečení sítě nebo uživatelem definované trasy (UDR) pro řízení síťového provozu.

Další informace najdete v článku [Připojení HDInsightu k místní síti.](../connect-on-premises-network.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii: [Doporučené postupy úložiště pro místní migraci Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-storage.md).

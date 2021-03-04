---
title: 'Infrastruktura: místní Apache Hadoop do Azure HDInsight'
description: Naučte se osvědčené postupy pro migraci místních clusterů Hadoop do Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 309053c2d7a0f9482016f1bd83e0c61dcd31bec5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740660"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrace místních Apache Hadoopových clusterů do Azure HDInsight – osvědčené postupy infrastruktury

Tento článek obsahuje doporučení pro správu infrastruktury clusterů Azure HDInsight. Je součástí série, která poskytuje osvědčené postupy, které vám pomůžou s migrací místních Apache Hadoop systémů do Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Plánování kapacity clusteru HDInsight

K dispozici jsou následující klíčové volby pro plánování kapacity clusteru HDInsight:

**Oblast**  
Oblast Azure určuje, kde se cluster fyzicky zřídí. Aby se minimalizovala latence čtení a zápisu, měl by cluster být ve stejné oblasti jako data.

**Umístění a velikost úložiště**  
Výchozí úložiště musí být ve stejné oblasti jako cluster. Pro cluster 48 se doporučuje mít 4 až 8 účtů úložiště. I když již může existovat dostatečná celková velikost úložiště, každý účet úložiště poskytuje pro výpočetní uzly další šířku pásma sítě. Pokud je k dispozici více účtů úložiště, použijte pro každý účet úložiště náhodný název bez předpony. Účelem náhodného pojmenování je snížit pravděpodobnost kritických bodů úložiště (omezování) nebo selhání v běžném režimu napříč všemi účty. Pro lepší výkon používejte jenom jeden kontejner na účet úložiště.

**Velikost a typ virtuálního počítače (teď podporuje G-series)**  
Každý typ clusteru má sadu typů uzlů a každý typ uzlu má konkrétní možnosti pro velikost a typ virtuálního počítače. Velikost a typ virtuálního počítače závisí na výkonu procesoru, velikosti paměti RAM a latenci sítě. Simulované úlohy lze použít k určení optimální velikosti a typu virtuálního počítače pro jednotlivé typy uzlů.

**Počet uzlů pracovního procesu**  
Počáteční počet uzlů pracovních procesů lze určit pomocí simulovaných úloh. Cluster se dá škálovat později přidáním dalších pracovních uzlů pro splnění požadavků na špičku zatížení. Cluster se dá později škálovat zpátky, když se další pracovní uzly nevyžadují.

Další informace najdete v článku [plánování kapacity pro clustery HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Použít doporučený typ virtuálního počítače pro cluster

Doporučené typy virtuálních počítačů pro každý typ clusteru HDInsight najdete v tématu [Konfigurace výchozích uzlů a velikosti virtuálních počítačů pro clustery](../hdinsight-supported-node-configuration.md) .

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Zkontroluje dostupnost součástí Hadoop ve službě HDInsight.

Každá verze HDInsight je cloudová distribuce sady komponent pro ekosystém systému Hadoop. Podrobnosti o všech komponentách HDInsight a jejich aktuálních verzích najdete v tématu [Správa verzí komponent HDInsight](../hdinsight-component-versioning.md) .

K ověření komponent a verzí Hadoop ve službě HDInsight můžete také použít uživatelské rozhraní Apache Ambari nebo Ambari REST API.

Aplikace nebo komponenty, které byly dostupné v místních clusterech, ale nejsou součástí clusterů HDInsight, se dají přidávat na hraničním uzlu nebo na virtuálním počítači ve stejné virtuální síti jako cluster HDInsight. Aplikace Hadoop třetí strany, která není dostupná v Azure HDInsight, se dá nainstalovat pomocí možnosti aplikace v clusteru HDInsight. Vlastní aplikace Hadoop můžete nainstalovat na cluster HDInsight pomocí akcí skriptů. V následující tabulce jsou uvedeny některé běžné aplikace a jejich možnosti integrace HDInsight:

|**Aplikace**|**Integrace**
|---|---|
|Tok dat|Hraniční uzel IaaS nebo HDInsight
|Alluxio|IaaS  
|Arcadia|IaaS 
|Tamazight|Žádné (pouze HDP)
|Datameer|Hraniční uzel HDInsight
|DataStax (Cassandra)|IaaS (CosmosDB alternativa v Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB alternativa v Azure)
|NiFi|IaaS 
|Presto|Hraniční uzel IaaS nebo HDInsight
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW alternativa v Azure)
|Tableau|IaaS 
|Hlavní|Hraniční uzel HDInsight
|StreamSets|Edge HDInsight 
|Palantir|IaaS 
|Sailpoint|IaaS 

Další informace najdete v článku věnovaném [Apache Hadoop komponentám, které jsou k dispozici v různých verzích HDInsight](../hdinsight-component-versioning.md) .

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Přizpůsobení clusterů HDInsight pomocí akcí skriptů

HDInsight poskytuje metodu konfigurace clusteru nazvanou **akce skriptu**. Akce skriptu je skript bash, který běží na uzlech v clusteru HDInsight a dá se použít k instalaci dalších komponent a změně nastavení konfigurace.

Akce skriptu musí být uložené na identifikátoru URI, který je přístupný z clusteru HDInsight. Dají se použít během vytváření clusteru nebo po ní a můžou se taky omezit na spouštění jenom na určitých typech uzlů.

Skript je možné zachovat nebo spustit jednou. Trvalé skripty se používají k přizpůsobení nových pracovních uzlů přidaných do clusteru prostřednictvím operací škálování. Trvalý skript může také při operacích škálování použít změny jiného typu uzlu, jako je například hlavní uzel.

HDInsight poskytuje předem napsané skripty pro instalaci následujících komponent do clusterů HDInsight:

- Přidat účet Azure Storage
- Nainstalovat odstín
- Nainstalovat Presto
- Nainstalovat Solr
- Nainstalovat Giraph
- Předběžné načtení knihoven podregistru
- Instalace nebo aktualizace Mono

> [!Note]  
> HDInsight neposkytuje přímou podporu pro vlastní součásti Hadoop nebo komponenty nainstalované pomocí akcí skriptů.

Akce skriptu je také možné publikovat do Azure Marketplace jako aplikace HDInsight.

Další informace najdete v následujících článcích:

- [Instalace aplikací Apache Hadoop třetích stran v HDInsight](../hdinsight-apps-install-applications.md)
- [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publikování aplikace HDInsight v Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Přizpůsobení konfigurací HDInsight pomocí Bootstrap

Změny konfiguračních souborů v konfiguračních souborech `core-site.xml` , například `hive-site.xml` a, `oozie-env.xml` lze provádět pomocí Bootstrap. Následující skript je příkladem použití PowerShellu [AZ Module](/powershell/azure/new-azureps-module-az) rutina [New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightcluster):

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

Další informace najdete v článku [Přizpůsobení clusterů HDInsight pomocí nástroje Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Viz také [Správa clusterů HDInsight pomocí REST API Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Přístup k nástrojům klienta z hraničních uzlů clusteru HDInsight Hadoop

Prázdný hraniční uzel je virtuální počítač pro Linux se stejnými klientskými nástroji, který je nainstalovaný a nakonfigurovaný jako hlavní, ale nemá spuštěné žádné služby Hadoop. Hraniční uzel lze použít pro následující účely:

- přístup ke clusteru
- testování klientských aplikací
- hostování klientských aplikací

Hraniční uzly lze vytvořit a odstranit pomocí Azure Portal a lze je použít během vytváření clusteru nebo po ní. Po vytvoření hraničního uzlu se můžete připojit k hraničnímu uzlu pomocí protokolu SSH a spustit klientské nástroje pro přístup ke clusteru Hadoop ve službě HDInsight. Koncový bod SSH hraničního uzlu je `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22` .


Další informace najdete v článku [použití prázdných hraničních uzlů na Apache Hadoop clusterech v HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Použití funkcí horizontálního navýšení kapacity a škálování clusterů

Služba HDInsight poskytuje pružnost díky možnosti horizontálního navýšení a snížení kapacity počtu pracovních uzlů ve vašich clusterech. Tato funkce umožňuje zmenšit cluster po hodinách nebo na víkendech a rozšířit ho během špičkových obchodních požadavků. Další informace naleznete v tématu:

* [Škálování clusterů HDInsight](../hdinsight-scaling-best-practices.md).
* [Škálování clusterů](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Použití HDInsight s Azure Virtual Network

Virtuální sítě Azure umožňují zabezpečeně komunikovat s prostředky Azure, jako je třeba Azure Virtual Machines, a to díky filtrování a směrování síťového provozu.

Použití Azure Virtual Network se službou HDInsight umožňuje následující scénáře:

- Připojení ke službě HDInsight přímo z místní sítě.
- Připojení HDInsight k úložištím dat ve službě Azure Virtual Network.
- Přímý přístup ke službám Hadoop, které nejsou veřejně dostupné po internetu. Například rozhraní API Kafka nebo rozhraní Java API pro adaptéry.

HDInsight se dá přidat do nové nebo existující služby Azure Virtual Network. Pokud se HDInsight přidává do existující Virtual Network, musí se aktualizovat existující skupiny zabezpečení sítě a uživatelsky definované trasy, aby bylo možné neomezený přístup k [několika IP adresám](../hdinsight-management-ip-addresses.md) v datacentru Azure. Také se ujistěte, že nechcete blokovat provoz do [portů](../control-network-traffic.md#required-ports), které jsou používány službami HDInsight.

> [!Note]  
> HDInsight v současné době nepodporuje vynucené tunelování. Vynucené tunelování je nastavení podsítě, které vynucuje odchozí internetový provoz do zařízení pro kontrolu a protokolování. Buď odeberte vynucené tunelové propojení před instalací HDInsight do podsítě, nebo vytvořte novou podsíť pro HDInsight. HDInsight také nepodporuje omezení odchozího připojení k síti.

Další informace najdete v následujících článcích:

- [Virtuální sítě Azure – přehled](../../virtual-network/virtual-networks-overview.md)
- [Rozšíření služby Azure HDInsight pomocí služby Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Zabezpečené připojení ke službám Azure pomocí koncových bodů služby Azure Virtual Network

HDInsight podporuje [koncové body služby virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md), které vám umožní zabezpečené připojení k databázím Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB a SQL. Povolením koncového bodu služby pro Azure HDInsight přenos toků přes zabezpečenou trasu z datového centra Azure. Díky této rozšířené úrovni zabezpečení v síťové vrstvě můžete uzamknout účty úložiště s velkými objemy dat na zadané virtuální sítě (virtuální sítě) a i nadále používat clustery HDInsight k bezproblémovému přístupu a zpracování těchto dat.

Další informace najdete v následujících článcích:

- [Koncové body služby pro virtuální síť](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Vylepšení zabezpečení HDInsight pomocí koncových bodů služby](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Připojení HDInsight k místní síti

HDInsight se dá připojit k místní síti pomocí virtuálních sítí Azure a brány VPN. K navázání připojení se dají použít tyto kroky:

- Použijte HDInsight v Virtual Network Azure, která má připojení k místní síti.
- Nakonfigurujte překlad názvů DNS mezi virtuální sítí a místní sítí.
- Nakonfigurujte skupiny zabezpečení sítě nebo trasy definované uživatelem (UDR) k řízení síťového provozu.

Další informace najdete v článku [připojení HDInsight k místní síti](../connect-on-premises-network.md) .

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii: [osvědčené postupy úložiště pro Azure HDInsight Hadoop migraci](apache-hadoop-on-premises-migration-best-practices-storage.md).
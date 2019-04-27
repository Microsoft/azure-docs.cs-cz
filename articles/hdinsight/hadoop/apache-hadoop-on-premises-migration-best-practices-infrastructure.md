---
title: Migrace místních Apache Hadoop clusterů Azure HDInsight – osvědčené postupy infrastruktury
description: Přečtěte si osvědčené postupy infrastruktury pro migrace místních Hadoop clusterů pro Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: hrasheed
ms.openlocfilehash: 1bd06507bd8a20cf504c1ff4cd9fe7e3b9196a3c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129310"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrace místních Apache Hadoop clusterů Azure HDInsight – osvědčené postupy infrastruktury

Tento článek obsahuje doporučení pro správu infrastruktury clustery Azure HDInsight. To je součástí série, která poskytuje osvědčené postupy pro pomoc s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Plánování kapacity clusteru HDInsight

Klíčové možnosti pro plánování kapacity clusteru HDInsight jsou následující:

- **Vyberte oblast,** -určuje oblast Azure, kde fyzicky zřízení clusteru. Kvůli minimalizaci latence čtení a zápisy, clusteru by měl být ve stejné oblasti jako data.
- **Vyberte umístění úložiště a jeho velikost** – výchozí úložiště musí být ve stejné oblasti jako cluster. Pro cluster s uzly 48 se doporučuje mít 4 až 8 účty úložiště. I když už může být dostatečné celkový úložiště, každý účet úložiště poskytuje větší šířku pásma sítě pro výpočetní uzly. Pokud existuje více účtů úložiště, použijte náhodný název pro každý účet úložiště, bez předpony. Účelem náhodných názvů snižuje pravděpodobnost vzniku kritických bodů úložiště (omezení využití sítě) nebo režimu běžné chyby ve všech účtech. Pro lepší výkon pomocí pouze jednoho kontejneru účtu úložiště.
- **Zvolte velikost virtuálního počítače a typ (teď podporuje řady G-series)** – každý typ clusteru má sadu typů uzlů a u každého typu uzlu má specifické nastavení pro jejich velikost virtuálního počítače a typu. Velikost virtuálního počítače a typ se určuje podle procesoru, výkon, velikost paměti RAM a latence sítě. Simulované zatížení lze použít k určení optimální velikost virtuálního počítače a zadejte pro každý typ uzlu.
- **Vyberte počet uzlů pracovního procesu** – počáteční počet uzlů pracovního procesu můžete určit pomocí simulovaných úloh. Clusteru je možné později škálovat tak, že přidáte další pracovní uzly určené k požadavkům zatížení ve špičce. Clusteru je možné škálovat později při pracovní uzly nejsou povinné.

Další informace najdete v článku [plánování kapacity pro clustery HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Typ virtuálního počítače pro cluster se doporučuje použít

Zobrazit [výchozí velikosti virtuálního počítače a konfigurace uzlů pro clustery](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) doporučeno typy virtuálních počítačů pro každý typ clusteru HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Zkontrolovat dostupnost součásti systému Hadoop v HDInsight

Každá verze HDInsight je Cloudová distribuce verze Hortonworks Data Platform (HDP) a se skládá sada součástí ekosystému Hadoop. Zobrazit [Správa verzí komponenty HDInsight](../hdinsight-component-versioning.md) podrobnosti o všech součástí HDInsight a jejich aktuální verze.

Ke kontrole komponenty a verze v HDInsight můžete také použít Apache Ambari, Uživatelskému nebo Ambari REST API.

Aplikace nebo komponenty, které byly k dispozici v místních clusterech, ale nejsou součástí clusterů HDInsight se dají přidat na hraničním uzlu nebo na virtuálním počítači ve stejné virtuální síti jako HDInsight cluster. Aplikace Hadoop jiných výrobců, která není k dispozici v Azure HDInsight můžete nainstalovat pomocí možnosti "Aplikace" v clusteru HDInsight. Vlastních aplikací Hadoop můžete nainstalovat na clusteru HDInsight pomocí "akce". V následující tabulce jsou uvedeny některé z běžných aplikací a jejich možnosti integrace HDInsight:

|**Aplikace**|**Integrace**
|---|---|
|Vzduchu|IaaS nebo HDInsight hraniční uzel
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Žádný (pouze HDP)
|Datameer|Hraniční uzel HDInsight
|Datastax (Cassandra)|IaaS (alternativu v Azure cosmos DB)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (alternativu v Azure cosmos DB)
|NiFi|IaaS 
|Presto|IaaS nebo HDInsight hraniční uzel
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW alternativu v Azure)
|Tableau|IaaS 
|Vodoryskou|Hraniční uzel HDInsight
|StreamSets|HDInsight edge 
|Palantir|IaaS 
|Sailpoint|Iaas 

Další informace najdete v článku [Apache Hadoop součásti, které jsou k dispozici v různých verzích HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Přizpůsobení clusterů HDInsight pomocí skriptových akcí

Poskytuje metodu volá konfigurace clusteru HDInsight **skript akce**. Akce skriptu je skriptu Bash, která běží na uzlech v clusteru služby HDInsight a slouží k instalaci dalších komponent a změnit nastavení konfigurace.

Akce se skripty musí být uložené na identifikátor URI, který je přístupný z clusteru HDInsight. Jejich lze použít během nebo po vytvoření clusteru a je také možné omezit pro spouštěn jen na určité typy uzlů.

Skript může být zachována nebo provést jednou. Trvalých skriptů se používají k přizpůsobení nové pracovních uzlů do clusteru prostřednictvím operace škálování. Trvalá akce se skripty může také použít změny na jiný typ uzlu, jako je například hlavního uzlu, když dojde k operace škálování.

HDInsight poskytuje předpřipravené skripty v clusterech HDInsight nainstalovat následující komponenty:

- Přidat účet Azure Storage
- Instalace rozhraní Hue
- Nainstalovat Presto
- Nainstalovat Solr
- Nainstalovat Giraph
- Přednačíst knihovny Hive
- Instalace nebo aktualizace Mono

> [!Note]  
> HDInsight neposkytuje přímou podporu pro vlastní hadoop součásti nebo součásti nainstalovat pomocí akcí skriptů.

Akce se skripty můžete také publikovat na webu Azure Marketplace jako aplikace HDInsight.

Další informace najdete v následujících článcích:

- [Instalace aplikací třetích stran Apache Hadoop v HDInsight](../hdinsight-apps-install-applications.md)
- [Přizpůsobení clusterů HDInsight pomocí skriptových akcí](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publikování aplikace HDInsight na Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Přizpůsobení konfigurace HDInsight pomocí Bootstrapu

Změny konfigurace v konfiguračních souborech, jako `core-site.xml`, `hive-site.xml` a `oozie-env.xml` je možné provádět pomocí Bootstrap. Následující skript představuje příklad, pomocí Powershellu [AZ modulu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) rutiny [New-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster):

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

Další informace najdete v článku [HDInsight přizpůsobení clusterů pomocí Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Viz také [HDInsight Správa clusterů pomocí rozhraní REST API Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Hraniční uzly clusteru přístup k nástrojům klienta z Hadoopu HDInsight

Prázdných hraničních uzlů je virtuální počítač s Linuxem pomocí stejných nástrojů klient nainstalovaný a nakonfigurovaný jako na hlavní uzly, ale žádné služby Hadoop spuštěné. Na hraničním uzlu lze použít k těmto účelům:

- přístup ke clusteru
- testování klientské aplikace
- hostování klientské aplikace

Hraniční uzly je možné vytvořit a odstranit prostřednictvím webu Azure portal a mohou být použity během nebo po vytvoření clusteru. Po vytvoření na hraničním uzlu, můžete se připojit k hraničnímu uzlu pomocí SSH a spuštění klientské nástroje pro přístup ke clusteru Hadoop v HDInsight. Na hraničním uzlu ssh je koncový bod `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.


Další informace najdete v článku [použití prázdných hraničních uzlů v clusterech Apache Hadoop v HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Pomocí funkce horizontálního navýšení nebo snížení clusterů

HDInsight poskytuje pružnost tím, že možnost vertikálně navýšit a snížit počet pracovních uzlů v clusterech služby. Tato funkce umožňuje zmenšit cluster po hodinách, nebo o víkendech a rozbalte ho během špičky obchodními požadavky. Další informace naleznete v tématu:

* [Škálování clusterů HDInsight](../hdinsight-scaling-best-practices.md).
* [Škálování clusterů](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Používat HDInsight s Azure Virtual Network

Virtuální sítě Azure umožňují prostředkům Azure, jako jsou Azure Virtual Machines, zabezpečeně komunikovat mezi sebou, internet, a místními sítěmi, filtrování a směrování síťového provozu.

Pomocí Azure Virtual Network s HDInsight umožňuje následující scénáře:

- Připojení k HDInsight přímo z místní sítě.
- Připojování k datům HDInsight ukládají ve službě Azure Virtual network.
- Přímý přístup k služby Hadoop, které nejsou k dispozici veřejně přes internet. Například rozhraní API systému Kafka nebo HBase Java API.

HDInsight je buď přidat do nové nebo existující virtuální sítě Azure. Pokud HDInsight se přidává do existující virtuální síť, existující skupiny zabezpečení sítě a trasy definované uživatelem musí aktualizovat, chcete-li povolit neomezený přístup k [několik IP adres](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) v datovém centru Azure. Také zajistěte, aby blokovat provoz [porty](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ports), které jsou používány služby HDInsight.

> [!Note]  
> HDInsight aktuálně nepodporují vynucené tunelování. Vynucené tunelování znamená nastavení podsítě, která vynutí odchozí internetový provoz do zařízení pro kontrolu a protokolování. Buď odeberte vynucené tunelování před instalací HDInsight do podsítě, nebo vytvořte novou podsíť pro HDInsight. HDInsight také nepodporuje omezení odchozího síťového připojení.

Další informace najdete v následujících článcích:

- [Azure virtual-networks-overview](../../virtual-network/virtual-networks-overview.md)
- [Rozšíření Azure HDInsight pomocí virtuální síť Azure](../hdinsight-extend-hadoop-virtual-network.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Připojte se bezpečně ke službám Azure s koncovými body služby virtuální sítě Azure

HDInsight podporuje [koncové body služeb virtuální sítě](../../virtual-network/virtual-network-service-endpoints-overview.md), které umožňují zabezpečené připojení k Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB a databáze SQL. Tím, že koncový bod služby pro Azure HDInsight, provoz prochází přes zabezpečené trasu z v rámci datového centra Azure. Tato rozšířená úroveň zabezpečení na síťové vrstvě můžete uzamčení účtů úložiště velké objemy dat do jejich zadané virtuální sítě (Vnet) a dál používat clustery HDInsight bez problémů pro přístup k a tato data zpracovávat.

Další informace najdete v následujících článcích:

- [Koncové body služby pro virtuální síť](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Umožňuje zvýšit zabezpečení HDInsight s koncovými body služby](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>HDInsight se připojit k místní síti

HDInsight můžete připojené k místní síti pomocí virtuální sítě Azure a bránu VPN. Následující postup slouží k navázání připojení:

- Pomocí HDInsight ve virtuální síti Azure, který má připojení k místní síti.
- Nakonfigurujte překlad názvů DNS mezi virtuální sítí a místní sítí.
- Konfigurace skupin zabezpečení sítě nebo uživatelem definované trasy (UDR) k řízení síťového provozu.

Další informace najdete v článku [HDInsight připojit k místní síti](../connect-on-premises-network.md)

## <a name="next-steps"></a>Další postup

Přečtěte si další článek v této sérii:

- [Úložiště osvědčené postupy pro místní migrace Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-storage.md)

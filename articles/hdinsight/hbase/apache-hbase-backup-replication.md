---
title: Nastavení zálohování HBase a Phoenix a replikace - Azure HDInsight | Microsoft Docs
description: Nastavení replikace pro HBase a Phoenix a zálohování.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: f43edaf16784e5ba5cc3d2b39df285582954210b
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="set-up-backup-and-replication-for-hbase-and-phoenix-on-hdinsight"></a>Nastavení zálohování a replikace HBase a Phoenix v HDInsight

HBase podporuje několik přístupů pro zabezpečení proti ztrátě dat:

* Kopírování `hbase` složky
* Exportovat pak importovat
* Kopírovat tabulky
* Snímky
* Replikace

> [!NOTE]
> Apache Phoenix ukládá metadata jeho v tabulkách HBase, tak, aby metadata zálohován při zálohování katalogu systémové tabulky HBase.

Následující oddíly popisují scénáře využití pro každou z těchto přístupů.

## <a name="copy-the-hbase-folder"></a>Zkopírujte složku hbase

S tímto přístupem zkopírujte všechna data HBase, aniž by bylo možné vybrat podmnožinu tabulky nebo rodin sloupců. Následující přístupy poskytují větší kontrolu.

HBase v HDInsight používá úložiště výchozí vybrán při vytvoření clusteru, objektů BLOB služby Azure Storage nebo Azure Data Lake Store. V obou případech se ukládají HBase jeho data a metadata soubory v následující cestě:

    /hbase

* V účtu Azure Storage `hbase` složka nachází v kořenovém kontejneru objektů blob:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* V Azure Data Lake Store `hbase` složka nachází v kořenové cestě, které jste zadali při zřizování clusteru. Tato kořenová cesta má obvykle `clusters` složku s podsložku s názvem po clusteru HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

V obou případech `hbase` složka obsahuje všechna data, která má HBase vyprazdňuje na disk, ale nemusí obsahovat data v paměti. Předtím, než můžete spoléhají na tuto složku jako přesné reprezentace dat HBase, je nutné vypnout clusteru.

Po odstranění clusteru, můžete buď ponechat data v místě, nebo zkopírovat data do nového umístění:

* Vytvoření nové instance HDInsight odkazující na aktuální umístění úložiště. S existujícími daty je vytvořena nová instance.

* Kopírování `hbase` složku do jiného úložiště Azure blob kontejneru nebo umístění Data Lake Store a pak spusťte nový cluster s daty. Pro úložiště Azure, použijte [AzCopy](../../storage/common/storage-use-azcopy.md)a pro Data Lake Store pomocí [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportovat pak importovat

Ve zdrojovém clusteru HDInsight použijte nástroj exportu (zahrnutá v HBase) k exportu dat ze zdrojové tabulky na výchozí připojené úložiště. Můžete zkopírovat složku exportovaný do cílového umístění úložiště a spusťte nástroj Import v cílovém clusteru HDInsight.

Export tabulky, první SSH do hlavního uzlu clusteru HDInsight zdroje a pak spusťte následující příkaz `hbase` příkaz:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

K importu tabulky, SSH do hlavního uzlu clusteru HDInsight cílové a pak spusťte následující příkaz `hbase` příkaz:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Zadejte export úplnou cestu k výchozí úložiště nebo na jednu z možností připojené úložiště. Například ve službě Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

V Azure Data Lake Store syntaxe je:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Tento přístup poskytuje členitosti úrovni tabulky. Můžete také zadat rozsah dat pro řádky, které patří, což umožňuje provádět proces postupně. Jednotlivá data je od Unix epoch v milisekundách.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Všimněte si, že budete muset zadat číslo verze každý řádek pro export. Chcete-li zahrnout všechny verze rozsah dat, nastavte `<numberOfVersions>` k hodnotě větší, než vaše verze maximální možné řádek, jako je například 100000.

## <a name="copy-tables"></a>Kopírovat tabulky

Nástroj CopyTable kopíruje data ze zdrojové tabulky řádek po řádku, do existující cílové tabulky se stejným schématem jako zdroj. Cílové tabulky může být ve stejném clusteru nebo na jiný cluster HBase.

Používáte CopyTable v rámci clusteru, SSH k hlavnímu uzlu clusteru HDInsight zdroje a pak spusťte toto `hbase` příkaz:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

Pomocí CopyTable zkopírovat do tabulky na jiný cluster, přidejte `peer` přepínače s adresou cílového clusteru:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Cílovou adresu se skládá z následujících tří částí:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` je čárkami oddělený seznam uzly ZooKeeper, například:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` na výchozí 2181, HDInsight a `<ZnodeParent>` je `/hbase-unsecure`, takže kompletní `<destinationAddress>` by:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

V tématu [ručně shromažďování seznamu kvora ZooKeeper](#manually-collect-the-zookeeper-quorum-list) v tomto článku podrobnosti týkající se načtení těchto hodnot pro váš cluster HDInsight.

Nástroj CopyTable podporuje také parametry, které zadat časový rozsah řádků zkopírovat a zadejte do něj podmnožinu rodin sloupců v tabulce pro kopírování. Pokud chcete zobrazit úplný seznam podporovaných CopyTable parametry, spusťte CopyTable bez parametrů:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable kontroluje celý zdrojový obsah tabulky, který bude přes zkopírován do cílové tabulky. Při CopyTable provede to může snížit výkon HBase cluster.

> [!NOTE]
> K automatizaci kopírování dat mezi tabulkami, najdete v článku `hdi_copy_table.sh` skript v [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) úložišti na Githubu.

### <a name="manually-collect-the-zookeeper-quorum-list"></a>Ručně shromáždit kvora ZooKeeper seznamu

Pokud oba clustery HDInsight jsou ve stejné virtuální síti, jak je popsáno výše, je automatické interní překlad názvů. Pokud chcete používat CopyTable pro clustery služby HDInsight v dvě samostatné virtuální sítě s připojením VPN Gateway, musíte zadejte hostitele IP adresy uzly Zookeeper kvora.

Chcete-li získat názvy hostitelů kvora, spusťte následující příkaz curl:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Příkaz curl načte dokument JSON s informace o konfiguraci HBase, a příkaz grep vrátí pouze položku "hbase.zookeeper.quorum", například:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Hodnota názvy hostitele kvora je celý řetězec napravo od dvojtečkou.

Pokud chcete načíst IP adres pro tyto hostitele, použijte následující příkaz curl pro každého hostitele v předchozím seznamu:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

V tomto příkazu curl `<zookeeperHostFullName>` je úplný název DNS ZooKeeper hostitele, jako je například v příkladu `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. Výstup příkazu obsahuje IP adresu pro zadaného hostitele, například:

    100    "ip" : "10.0.0.9",

Jakmile shromáždíte IP adresy pro všechny uzly ZooKeeper ve vašem kvora, znovu sestavte cílovou adresu:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

V našem příkladu:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Snímky

Snímky umožňují využít zálohování dat v daném okamžiku v vašeho úložiště HBase. Snímky mají minimální režii a dokončit během několika sekund, protože operace snímku efektivně operace metadat zaznamenávání názvy všech souborů v úložišti v tento okamžik. V době snímku se zkopíruje žádná skutečná data. Snímky využívají neměnné povaha dat uložených v HDFS, kde aktualizace, odstranění a vložení jsou všechny vyjádřené nová data. Můžete obnovit (*klon*) snímku ve stejném clusteru nebo exportovat snímku do jiného clusteru.

Pro vytvoření snímku, SSH v k hlavnímu uzlu HDInsight HBase clusteru a spusťte `hbase` prostředí:

    hbase shell

V rámci prostředí hbase použijte příkaz snímku s názvy tabulky a tento snímek:

    snapshot '<tableName>', '<snapshotName>'

K obnovení snímku podle názvu v rámci `hbase` prostředí shell, nejdřív zakázat tabulky, pak obnovení snímku a znovu povolit v tabulce:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Chcete-li obnovit snímek do nové tabulky, použijte clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Chcete-li exportovat snímek HDFS pro použití jiného cluster, nejprve vytvořit snímek, jak je popsáno výše a pak použijte nástroj ExportSnapshot. Tento nástroj spusťte z v rámci relace SSH k hlavnímu uzlu není uvnitř `hbase` prostředí:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

`<hdfsHBaseLocation>` Může být libovolná z umístění úložiště pro zdrojový cluster dostupné a by měla odkazovat na složce hbase používané cílový cluster. Například pokud máte sekundární účet úložiště Azure připojit ke clusteru a zdroje, a tento účet poskytuje přístup ke kontejneru používá výchozí úložiště cílový cluster, můžete použít tento příkaz:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Po exportu snímku příkaz SSH do hlavního uzlu clusteru cílový a obnovení snímek pomocí restore_snapshot, jak se popisuje výš.

Snímky poskytnout úplné zálohy tabulku v době `snapshot` příkaz. Snímky nenabízí možnost provádět přírůstkové snímky ve windows času ani zadejte podmnožiny rodin sloupců pro zahrnutí do snímku.

## <a name="replication"></a>Replikace

Replikace HBase automaticky doručí transakce z clusteru s podporou do cílového clusteru, pomocí asynchronní mechanismus s minimální režií na zdrojovém clusteru. V prostředí HDInsight, můžete nastavit replikace mezi clustery kde:

* Zdrojové a cílové clustery jsou ve stejné virtuální síti.
* Clustery zdroje a cíle jsou v různých virtuálních sítích, které jsou připojené pomocí brány VPN, ale oba clustery existují ve stejné zeměpisné umístění.
* Cluster zdroje a cíle clustery jsou v různých virtuálních sítích, které jsou připojené pomocí brány VPN a každý cluster existuje v jiném zeměpisné umístění.

Obecné kroky pro nastavení replikace jsou:

1. Ve zdrojovém clusteru vytváření tabulky a naplnit data.
2. V cílovém clusteru vytvořte prázdný cílové tabulky se schématem zdrojové tabulky.
3. Zaregistrujte cílového clusteru jako sdílené na zdrojovém clusteru.
4. Povolte replikaci na požadovanou zdrojové tabulky.
5. Zkopírujte existující data ze zdrojové tabulky do cílové tabulky.
6. Replikace automaticky zkopíruje nové změny dat na zdrojové tabulky do cílové tabulky.

Chcete-li povolit replikaci na HDInsight, použijte akci skriptu ke clusteru HDInsight běžící zdrojový. Postup povolení replikace v clusteru nebo experimentovat s replikací v ukázkové clustery, které jsou vytvořené ve virtuálních sítích pomocí šablon pro správu prostředků Azure, najdete v části [replikace nakonfigurovat HBase](apache-hbase-replication.md). Tento článek obsahuje také pokyny pro povolení replikace Phoenix metadat.

## <a name="next-steps"></a>Další postup

* [Konfigurace replikace HBase](apache-hbase-replication.md)

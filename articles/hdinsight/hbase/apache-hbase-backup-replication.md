---
title: Nastavení zálohování HBase a Phoenix a replikace – Azure HDInsight
description: Nastavení zálohování a replikace pro HBase a Phoenix.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: b9557dcd34ad59ae50240b76ae75df4ef3f39a5b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592948"
---
# <a name="set-up-backup-and-replication-for-hbase-and-phoenix-on-hdinsight"></a>Nastavení zálohování a replikace pro HBase a Phoenix v HDInsight

HBase podporuje několik přístupů pro zabezpečení proti ztrátě dat:

* Kopírovat `hbase` složky
* Export pak Import
* Kopírování tabulek
* Snímky
* Replikace

> [!NOTE]
> Apache Phoenix ukládá jeho metadata v tabulkách HBase, tak, aby se metadata zálohovány při zálohování tabulky HBase systém katalogu.

Následující části popisují scénáře použití pro každou z těchto přístupů.

## <a name="copy-the-hbase-folder"></a>Zkopírujte složku hbase

S tímto přístupem zkopírování všech dat HBase, aniž by bylo možné vybrat podmnožinu tabulek nebo rodin sloupců. Následující přístupy poskytují větší kontrolu.

HBase v HDInsight používá výchozí úložiště, které vybrali při vytváření clusteru, objekty BLOB služby Azure Storage nebo Azure Data Lake Store. V obou případech se ukládá HBase jeho data a metadata soubory v následující cestě:

    /hbase

* V účtu služby Azure Storage `hbase` je umístěn v kořenové složce kontejneru objektů blob:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* V Azure Data Lake Store `hbase` je umístěn v kořenové cestě, které jste zadali při vytváření clusteru. Tato kořenová cesta má obvykle `clusters` složky podsložku s názvem po vašeho clusteru HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

V obou případech `hbase` složka obsahuje všechna data, která má HBase se vyprázdní na disku, ale nemusí obsahovat data v paměti. Předtím, než můžete spolehnout na tuto složku jako přesnou reprezentací dat HBase, musíte vypnout clusteru.

Po odstranění clusteru můžete ponechat data na místě, nebo zkopírovat data do nového umístění:

* Vytvořte novou instanci HDInsight odkazující na aktuální umístění úložiště. Nová instance je vytvořena s existujícími daty.

* Kopírovat `hbase` složky do jiné služby Azure Storage blob nebo kontejneru umístění Data Lake Store a pak spusťte nový cluster s daty. Pro službu Azure Storage, použijte [AzCopy](../../storage/common/storage-use-azcopy.md)a pro použití Data Lake Store [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Export pak Import

Ve zdrojovém clusteru HDInsight použijte nástroj exportu (zahrnutá v HBase) k exportu dat ze zdrojové tabulky do výchozí připojené úložiště. Pak můžete zkopírovat exportované složku do cílového umístění úložiště a spusťte nástroj importu v cílovém clusteru HDInsight.

Export tabulky, první SSH k hlavnímu uzlu clusteru HDInsight zdroje a pak spusťte následující příkaz `hbase` příkaz:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Chcete-li importovat tabulku, SSH k hlavnímu uzlu clusteru HDInsight cílové a potom spusťte následující příkaz `hbase` příkaz:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Zadejte export úplnou cestu k výchozí úložiště nebo některou z možností připojené úložiště. Například ve službě Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

V Azure Data Lake Store syntaxe je:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Tento přístup nabízí tabulky úrovni členitosti. Můžete také zadat rozsah dat pro řádků, které chcete zahrnout, což umožňuje procesu provádět přírůstkové. Každé datum je v milisekundách od epochy Unix.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Všimněte si, že budete muset zadat několik verzí od jednotlivých řádků pro export. Chcete-li zahrnout všechny verze rozsah dat, nastavte `<numberOfVersions>` k hodnotě větší, než vaše verze maximální možné řádku, jako je například 100000.

## <a name="copy-tables"></a>Kopírování tabulek

Nástroj CopyTable kopíruje data ze zdrojové tabulky řádek po řádku, do existující cílová tabulka se stejným schématem jako zdroj. Cílové tabulky mohou být na stejném clusteru nebo na jiný cluster HBase.

Použít CopyTable v rámci clusteru, SSH k hlavnímu uzlu clusteru HDInsight zdroje a pak spusťte toto `hbase` příkaz:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

Chcete-li použít CopyTable ke zkopírování do tabulky na jiném clusteru, přidejte `peer` přepnout adresou cílového clusteru:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Cílová adresa se skládá z následujících tří částí:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` je čárkou oddělený seznam uzly ZooKeeper, například:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` na výchozí 2181, HDInsight a `<ZnodeParent>` je `/hbase-unsecure`, takže kompletní `<destinationAddress>` by být:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Zobrazit [ruční shromažďování seznamu kvorum ZooKeeper](#manually-collect-the-zookeeper-quorum-list) v tomto článku najdete podrobnosti o tom, jak načíst tyto hodnoty pro váš cluster HDInsight.

Nástroj CopyTable také podporuje parametry se mají zadat časový rozsah řádků kopírovat a zadejte do něj podmnožinu rodin sloupců v tabulce ke kopírování. Pokud chcete zobrazit úplný seznam podporovaných CopyTable parametry, spusťte CopyTable bez parametrů:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable prohledává celý zdrojový obsah tabulky, která se překopírovaly do cílové tabulky. Zatímco CopyTable provede to může snížit výkon HBase cluster.

> [!NOTE]
> K automatizaci kopírování dat mezi tabulkami, najdete v článku `hdi_copy_table.sh` skript v [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) úložišti na Githubu.

### <a name="manually-collect-the-zookeeper-quorum-list"></a>Ručně shromáždit kvorum ZooKeeper seznamu

Když oba clustery HDInsight jsou ve stejné virtuální síti, jak je popsáno výše, je automatický interní překlad názvů. Pro účely CopyTable clusterů HDInsight v dvě samostatné virtuální sítě připojený prostřednictvím brány sítě VPN, musíte poskytnout hostitele IP adresy uzly Zookeeper v kvora.

Chcete-li získat názvy hostitelů kvora, spusťte následující příkaz curl:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Příkaz curl načte dokument JSON se informace o konfiguraci HBase a grep příkaz vrátí jenom položky "hbase.zookeeper.quorum", například:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Hodnota názvy hostitele kvora se celý řetězec napravo od dvojtečka.

Pokud chcete načíst IP adresy pro tyto hostitele, použijte následující příkaz curl pro každého hostitele v předchozím seznamu:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

V tomto příkazu curl `<zookeeperHostFullName>` je úplný název DNS o hostiteli ZooKeeper, například v příkladu `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. Výstup tohoto příkazu obsahuje IP adresu pro zadané hostitele, například:

    100    "ip" : "10.0.0.9",

Jakmile shromáždíte IP adres pro všechny uzly ZooKeeper vaše kvora, znovu sestavte cílovou adresu:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

V našem příkladu:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Snímky

Snímky umožňují vytvořit zálohu dat v daném okamžiku v vaše úložiště dat HBase. Snímky mají minimální režií a dokončit během několika sekund, protože operace vytvoření snímku je v podstatě operace metadat zachytávání názvy všech souborů v úložišti v tomto okamžiku. V době snímku žádná skutečná data zkopírována. Snímky využívají neměnné povaze dat uložených v HDFS, kde aktualizace, odstraňování a vkládání jsou všechny reprezentovány jako nová data. Můžete obnovit (*klonování*) snímku na stejném clusteru nebo export snímku do jiného clusteru.

K vytvoření snímku, SSH k hlavnímu uzlu vaše HDInsight HBase v clusteru a spuštění `hbase` prostředí:

    hbase shell

V rámci prostředí hbase použijte příkaz snímku s názvy tabulky a z tohoto snímku:

    snapshot '<tableName>', '<snapshotName>'

Chcete-li obnovit snímek podle názvu v rámci `hbase` prostředí, nejdřív zakázat v tabulce, potom obnovení snímku a znovu povolit v tabulce:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Pokud chcete obnovit snímek do nové tabulky, použijte clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Exportovat snímek do HDFS pro použití podle jiného clusteru, nejprve vytvořte snímek, jak je popsáno dříve a pak použít nástroj ExportSnapshot. Tento nástroj spusťte z v rámci relace SSH k hlavnímu uzlu není v rámci `hbase` prostředí:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

`<hdfsHBaseLocation>` Může být kterýkoli z umístění úložiště přístupná pro zdrojový cluster a by odkazoval na složku hbase používané cílový cluster. Například pokud máte sekundární účet služby Azure Storage připojené k vašemu clusteru zdroje a tento účet poskytuje přístup ke kontejneru používá výchozí úložiště bude cílový cluster, můžete použít tento příkaz:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Po exportu snímku příkazu SSH k hlavnímu uzlu clusteru cílový a obnovení snímku pomocí restore_snapshot jak bylo popsáno dříve.

Snímky poskytují úplnou zálohu tabulky v době `snapshot` příkazu. Snímky se neposkytuje možnost k provedení přírůstkových snímků ve windows čas ani k určení podmnožiny rodin sloupců zahrnout snímek.

## <a name="replication"></a>Replikace

Replikace HBase automaticky odesílá transakce z clusteru zdroje do cílového clusteru pomocí mechanismu asynchronní s minimální režií na zdrojovém clusteru. V HDInsight, můžete nastavit replikaci mezi clustery kde:

* Clustery zdrojových a cílových jsou ve stejné virtuální síti.
* Clustery zdroje a cíle jsou v různých virtuálních sítích, které jsou připojené prostřednictvím brány VPN gateway, ale existují obou clusterech ve stejné zeměpisné umístění.
* Cluster zdroje a cíle clustery jsou v různých virtuálních sítích, které jsou připojené prostřednictvím brány sítě VPN a každý cluster existuje v jiném geografickém umístění.

Obecné kroky pro nastavení replikace jsou:

1. Ve zdrojovém clusteru se vytvořily tabulky a naplnit data.
2. V cílovém clusteru vytvořte prázdný cílových tabulek s schématu zdrojové tabulky.
3. Cílový cluster zaregistrujte jako partnera pro zdrojový cluster.
4. Povolte replikaci na požadovanou zdrojové tabulky.
5. Kopírovat existující data ze zdrojových tabulek do cílových tabulek.
6. Replikace automaticky zkopíruje nové změny dat do zdrojových tabulek do cílových tabulek.

Pokud chcete povolit replikaci na HDInsight, použijte akci skriptu ke clusteru HDInsight spuštěný zdroje. Postup povolení replikace ve vašem clusteru, nebo můžete experimentovat s replikací v clusterech ukázka vytvoří v virtuálních sítí pomocí šablon Azure Resource Manageru, najdete v části [replikace nakonfigurovat HBase](apache-hbase-replication.md). Tento článek také obsahuje pokyny pro povolení replikace Phoenix metadat.

## <a name="next-steps"></a>Další postup

* [Konfigurace replikace HBase](apache-hbase-replication.md)

---
title: Zálohování & replikace pro Apache HBase, Phoenix - Azure HDInsight
description: Nastavení zálohování a replikace pro Apache HBase a Apache Phoenix v Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: c6d33158b581bf4394a0d1bac2b277830328e110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495946"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Nastavení zálohování a replikace pro Apache HBase a Apache Phoenix na HDInsight

Apache HBase podporuje několik přístupů pro ochranu před ztrátou dat:

* Zkopírovat `hbase` složku
* Exportovat a pak importovat
* Kopírování tabulek
* Snímky
* Replikace

> [!NOTE]  
> Apache Phoenix ukládá svá metadata v tabulkách HBase, takže metadata jsou zálohována při zálohování tabulek katalogu systému HBase.

Následující části popisují scénář použití pro každý z těchto přístupů.

## <a name="copy-the-hbase-folder"></a>Kopírování složky hbase

Pomocí tohoto přístupu zkopírujete všechna data HBase, aniž byste mohli vybrat podmnožinu tabulek nebo rodin sloupců. Následné přístupy poskytují větší kontrolu.

HBase v HDInsight používá výchozí úložiště vybrané při vytváření clusteru, buď objekty BLOB Azure Storage nebo Azure Data Lake Storage. V obou případech HBase ukládá své soubory dat a metadat pod následující cestou:

    /hbase

* V účtu Azure `hbase` Storage se složka nachází v kořenovém adresáři kontejneru objektů blob:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* V Azure Data Lake `hbase` Storage se složka nachází pod kořenovou cestou, kterou jste zadali při zřizování clusteru. Tato kořenová cesta `clusters` má obvykle složku s podsložkou pojmenovanou po clusteru HDInsight:

    ```
    /clusters/<clusterName>/hbase
    ```

V obou případech `hbase` složka obsahuje všechna data, která HBase vyprázdněna na disk, ale nemusí obsahovat data v paměti. Než se budete moci spolehnout na tuto složku jako na přesnou reprezentaci dat HBase, je nutné cluster vypnout.

Po odstranění clusteru můžete data ponechat na místě nebo zkopírovat data do nového umístění:

* Vytvořte novou instanci HDInsight, která ukazuje na aktuální umístění úložiště. Nová instance je vytvořena se všemi existujícími daty.

* Zkopírujte `hbase` složku do jiného kontejneru objektů blob úložiště Azure nebo umístění úložiště datového jezera a spusťte nový cluster s těmito daty. Pro Azure Storage použijte [AzCopy](../../storage/common/storage-use-azcopy.md)a pro úložiště datových jezer použijte [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportovat a pak importovat

Ve zdrojovém clusteru HDInsight použijte [nástroj Export](https://hbase.apache.org/book.html#export) (součástí HBase) k exportu dat ze zdrojové tabulky do výchozího připojeného úložiště. Exportovnou složku pak můžete zkopírovat do cílového umístění úložiště a spustit [nástroj Import v](https://hbase.apache.org/book.html#import) cílovém clusteru HDInsight.

Chcete-li exportovat data tabulky, nejprve ssh do hlavního uzlu `hbase` zdrojového clusteru HDInsight a pak spusťte následující příkaz:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Adresář exportu již nesmí existovat. Název tabulky rozlišuje malá a velká písmena.

Chcete-li importovat data tabulky, SSH do hlavního uzlu cílového clusteru HDInsight a pak spusťte následující `hbase` příkaz:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Tabulka již musí existovat.

Zadejte úplnou cestu exportu do výchozího úložiště nebo do libovolné připojené možnosti úložiště. Například ve službě Azure:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

V Azure Data Lake Storage Gen2 syntaxe je:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

V Azure Data Lake Storage Gen1 syntaxe je:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Tento přístup nabízí rozlišovací schopnost na úrovni tabulky. Můžete také zadat rozsah dat pro řádky, které mají být zahrnuty, což umožňuje provádět proces postupně. Každé datum je v milisekundách od unixové epochy.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Všimněte si, že je nutné zadat počet verzí každého řádku, který chcete exportovat. Chcete-li zahrnout všechny verze do `<numberOfVersions>` období, nastavte hodnotu větší, než je maximální možné verze řádků, například 100000.

## <a name="copy-tables"></a>Kopírování tabulek

[Nástroj CopyTable](https://hbase.apache.org/book.html#copy.table) kopíruje data ze zdrojové tabulky řádek po řádku do existující cílové tabulky se stejným schématem jako zdroj. Cílová tabulka může být ve stejném clusteru nebo v jiném clusteru HBase. Názvy tabulek rozlišují malá a velká písmena.

Chcete-li použít CopyTable v rámci clusteru, SSH do hlavního `hbase` uzlu zdrojového clusteru HDInsight a spusťte tento příkaz:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>


Chcete-li pomocí copytable kopírovat do tabulky v `peer` jiném clusteru, přidejte přepínač s adresou cílového clusteru:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Cílová adresa se skládá z následujících tří částí:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>`je seznam uzlů Apache ZooKeeper oddělený chodů oddělený čárkami, například:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>`na HDInsight výchozí 2181, `<ZnodeParent>` `/hbase-unsecure`a je `<destinationAddress>` , takže kompletní by bylo:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay25 000xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-nesecure

Podrobnosti o tom, jak načíst tyto hodnoty pro cluster HDInsight, najdete v článku [Ruční shromažďování seznamu kvora Apache ZooKeeper](#manually-collect-the-apache-zookeeper-quorum-list) v tomto článku.

Nástroj CopyTable také podporuje parametry, které určují časový rozsah řádků ke kopírování a určují podmnožinu rodin sloupců v tabulce ke kopírování. Chcete-li zobrazit úplný seznam parametrů podporovaných CopyTable, spusťte CopyTable bez jakýchkoli parametrů:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable prohledá celý obsah zdrojové tabulky, který bude zkopírován do cílové tabulky. To může snížit výkon clusteru HBase při spuštění CopyTable.

> [!NOTE]  
> Pokud chcete automatizovat kopírování dat mezi `hdi_copy_table.sh` tabulkami, přečtěte si scénář v úložišti [Utils Azure HBase](https://github.com/Azure/hbase-utils/tree/master/replication) na GitHubu.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Ruční shromažďování seznamu kvora Apache ZooKeeper

Pokud jsou oba clustery HDInsight ve stejné virtuální síti, jak je popsáno výše, interní překlad názvů hostitele je automatický. Chcete-li použít CopyTable pro clustery HDInsight ve dvou samostatných virtuálních sítích propojených bránou VPN, budete muset v kvoru zadat hostitelské IP adresy uzlů Zookeeper.

Chcete-li získat názvy hostitelů kvora, spusťte následující příkaz curl:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Příkaz curl načte dokument JSON s informacemi o konfiguraci HBase a příkaz grep vrátí pouze položku "hbase.zookeeper.quorum", například:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Hodnota názvů hostitelů kvora je celý řetězec napravo od dvojtečky.

Chcete-li načíst adresy IP pro tyto hostitele, použijte pro každého hostitele v předchozím seznamu následující příkaz curl:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

V tomto příkazu curl `<zookeeperHostFullName>` je úplný název DNS hostitele ZooKeeper, například příklad `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. Výstup příkazu obsahuje IP adresu pro zadaného hostitele, například:

    100    "ip" : "10.0.0.9",

Po shromáždění IP adres pro všechny uzly ZooKeeper v kvoru znovu vytvořte cílovou adresu:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

V našem příkladu:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Snímky

[Snímky](https://hbase.apache.org/book.html#ops.snapshots) umožňují provést zálohování dat v okamžiku v časovém úložišti dat. Snímky mají minimální režii a dokončení během několika sekund, protože operace snímek je efektivně operace metadat zachycující názvy všech souborů v úložišti v tomto okamžiku. V době snímku se nekopírují žádná skutečná data. Snímky spoléhají na neměnnou povahu dat uložených v HDFS, kde jsou aktualizace, odstranění a vložení reprezentovány jako nová data. Snímek ve stejném clusteru můžete obnovit *(klonovat)* nebo exportovat snímek do jiného clusteru.

Chcete-li vytvořit snímek, SSH do hlavního uzlu clusteru `hbase` HDInsight HBase a spustit prostředí:

    hbase shell

V rámci prostředí hbase použijte příkaz snímek s názvy tabulky a tohoto snímku:

    snapshot '<tableName>', '<snapshotName>'

Chcete-li obnovit snímek `hbase` podle názvu v prostředí, nejprve zakažte tabulku, potom obnovte snímek a znovu povolte tabulku:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Chcete-li obnovit snímek do nové tabulky, použijte clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Chcete-li exportovat snímek do HDFS pro použití v jiném clusteru, nejprve vytvořte snímek, jak je popsáno výše, a potom použijte nástroj ExportSnapshot. Spusťte tento nástroj z relace SSH do hlavního uzlu, nikoli v `hbase` rámci prostředí:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

Může `<hdfsHBaseLocation>` se nacházet libovolné umístění úložiště přístupné pro zdrojový cluster a mělo by to směřovat ke složce hbase používané cílovým clusterem. Například pokud máte sekundární účet Azure Storage připojené ke zdrojovému clusteru a tento účet poskytuje přístup ke kontejneru používanému ve výchozím úložišti cílového clusteru, můžete použít tento příkaz:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Po exportu snímku SSH do hlavního uzlu cílového clusteru a obnovit snímek pomocí příkazu restore_snapshot, jak bylo popsáno dříve.

Snímky poskytují úplnou zálohu tabulky v době `snapshot` příkazu. Snímky neposkytují možnost provádět přírůstkové snímky podle oken času ani určit podmnožiny rodin sloupců, které mají být zahrnuty do snímku.

## <a name="replication"></a>Replikace

[Replikace HBase](https://hbase.apache.org/book.html#_cluster_replication) automaticky odesílá transakce ze zdrojového clusteru do cílového clusteru pomocí asynchronního mechanismu s minimální režií ve zdrojovém clusteru. V HDInsightu můžete nastavit replikaci mezi clustery, kde:

* Zdrojové a cílové clustery jsou ve stejné virtuální síti.
* Clustery zdrojových a cílech jsou v různých virtuálních sítích propojených bránou VPN, ale oba clustery existují ve stejném geografickém umístění.
* Clustery zdrojového clusteru a cílů jsou v různých virtuálních sítích propojených bránou VPN a každý cluster existuje v jiném geografickém umístění.

Obecné kroky k nastavení replikace jsou:

1. Ve zdrojovém clusteru vytvořte tabulky a naplňte data.
2. V cílovém clusteru vytvořte prázdné cílové tabulky se schématem zdrojové tabulky.
3. Zaregistrujte cílový cluster jako rovnocenný tým zdrojového clusteru.
4. Povolte replikaci v požadovaných zdrojových tabulkách.
5. Zkopírujte existující data ze zdrojových tabulek do cílových tabulek.
6. Replikace automaticky zkopíruje nové změny dat do zdrojových tabulek do cílových tabulek.

Chcete-li povolit replikaci na HDInsight, použijte akci skriptu na spuštěný zdrojový cluster HDInsight. Návod povolení replikace v clusteru nebo experimentování s replikací v ukázkových clusterech vytvořených ve virtuálních sítích pomocí šablon Azure Resource Management najdete v [tématu Konfigurace replikace Apache HBase](apache-hbase-replication.md). Tento článek také obsahuje pokyny pro povolení replikace metadat Phoenix.

## <a name="next-steps"></a>Další kroky

* [Konfigurace replikace Apache HBase](apache-hbase-replication.md)
* [Práce s nástrojem pro import a export HBase](https://blogs.msdn.microsoft.com/data_otaku/2016/12/21/working-with-the-hbase-import-and-export-utility/)

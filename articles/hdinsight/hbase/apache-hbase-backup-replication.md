---
title: Replikace zálohování & pro Apache HBA, Phoenix – Azure HDInsight
description: Nastavení zálohování a replikace pro Apache HBA a Apache Phoenix ve službě Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 5c0694f9ef16de9c69d424b5005ca0d5a277a77f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89505025"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Nastavení zálohování a replikace pro Apache HBA a Apache Phoenix v HDInsight

Apache HBA podporují několik přístupů, které chrání před ztrátou dat:

* Kopírování `hbase` složky
* Exportovat a pak importovat
* Kopírovat tabulky
* Snímky
* Replikace

> [!NOTE]  
> Apache Phoenix ukládá metadata do tabulek HBA, aby se metadata zálohovali při zálohování tabulek systémového katalogu HBA.

Následující části popisují scénář použití pro každý z těchto přístupů.

## <a name="copy-the-hbase-folder"></a>Kopírovat složku HBA

Pomocí tohoto přístupu zkopírujete všechna data HBA, aniž by bylo možné vybrat podmnožinu tabulek nebo rodin sloupců. Další přístupy poskytují lepší kontrolu.

HBA v HDInsight používá výchozí úložiště vybrané při vytváření clusteru, buď Azure Storage objektů blob, nebo Azure Data Lake Storage. V obou případech adaptéry HBA ukládají svoje data a soubory metadat v následující cestě:

`/hbase`

* V Azure Storage účtu se `hbase` složka nachází v kořenovém adresáři kontejneru objektů BLOB:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* V Azure Data Lake Storage se `hbase` složka nachází pod kořenovou cestou, kterou jste zadali při zřizování clusteru. Tato kořenová cesta má obvykle `clusters` složku s podsložkou s názvem po vašem clusteru HDInsight:

  `/clusters/<clusterName>/hbase`

V obou případech `hbase` Složka obsahuje všechna data, která se v adaptérech HBA vyprázdní na disk, ale nemusí obsahovat data v paměti. Než budete moct spoléhat na tuto složku jako přesnou reprezentaci dat HBA, musíte cluster vypnout.

Po odstranění clusteru můžete ponechat data na místě nebo zkopírovat data do nového umístění:

* Vytvořte novou instanci HDInsight ukazující na aktuální umístění úložiště. Vytvoří se nová instance se všemi existujícími daty.

* Zkopírujte `hbase` složku do jiného Azure Storage kontejneru objektů BLOB nebo umístění Data Lake Storage a pak spusťte nový cluster s těmito daty. Pro Azure Storage použijte [AzCopy](../../storage/common/storage-use-azcopy.md)a pro data Lake Storage použijte [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportovat a pak importovat

V případě zdrojového clusteru HDInsight použijte nástroj pro [Export](https://hbase.apache.org/book.html#export) (který je součástí adaptérů HBA) a exportujte data ze zdrojové tabulky do výchozího připojeného úložiště. Potom můžete zkopírovat exportovanou složku do cílového umístění úložiště a spustit [Nástroj pro import](https://hbase.apache.org/book.html#import) v cílovém clusteru HDInsight.

Chcete-li exportovat data tabulky, nejprve je třeba SSH do hlavního uzlu zdrojového clusteru HDInsight a pak spusťte následující `hbase` příkaz:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

Adresář pro export již nesmí existovat. V názvu tabulky se rozlišují velká a malá písmena.

Pokud chcete importovat data tabulky, požádejte SSH do hlavního uzlu vašeho cílového clusteru HDInsight a pak spusťte následující `hbase` příkaz:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

Tabulka musí již existovat.

Zadejte úplnou cestu pro export do výchozího úložiště nebo libovolné možnosti připojeného úložiště. Například v Azure Storage:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

V Azure Data Lake Storage Gen2 syntaxe je:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

V Azure Data Lake Storage Gen1 syntaxe je:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Tento přístup nabízí členitost na úrovni tabulky. Můžete také zadat rozsah kalendářních dat, který má být zahrnut do řádků, což vám umožní provést proces přírůstkově. Každé datum je v milisekundách od epocha systému UNIX.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Všimněte si, že musíte zadat počet verzí každého řádku, který se má exportovat. Pokud chcete zahrnout všechny verze v rozsahu kalendářních dat, nastavte `<numberOfVersions>` na hodnotu větší než maximální možné verze řádků, například 100000.

## <a name="copy-tables"></a>Kopírovat tabulky

[Nástroj pro kopírování](https://hbase.apache.org/book.html#copy.table) kopíruje data ze zdrojové tabulky, řádku podle řádku do existující cílové tabulky se stejným schématem jako se zdrojem. Cílová tabulka může být ve stejném clusteru nebo v různých clusterech HBA. V názvech tabulek se rozlišují velká a malá písmena.

Pokud chcete použít kopírovací sadu v rámci clusteru, požádejte SSH do hlavního uzlu zdrojového clusteru HDInsight a pak spusťte tento `hbase` příkaz:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Pokud chcete použít kopírovací tabulku ke kopírování do tabulky v jiném clusteru, přidejte `peer` přepínač s adresou cílového clusteru:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

Cílová adresa se skládá z následujících tří částí:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` je čárkami oddělený seznam uzlů Apache ZooKeeper, například:

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET, zk4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET, zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` ve výchozím nastavení má HDInsight hodnotu 2181 a `<ZnodeParent>` je `/hbase-unsecure` to proto, že dokončení bude `<destinationAddress>` :

    zk0-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET, zk4-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET, zk3-hdizc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. NET: 2181:/HBase-Unsecure

Podrobnosti o tom, jak načíst tyto hodnoty pro cluster HDInsight, najdete v tématu [ruční shromáždění seznamu kvora Apache Zookeeper](#manually-collect-the-apache-zookeeper-quorum-list) v tomto článku.

Nástroj pro kopírování také podporuje parametry pro určení časového rozsahu kopírování řádků a k určení podmnožiny rodin sloupců v tabulce ke zkopírování. Chcete-li zobrazit úplný seznam parametrů podporovaných kopírovacími rutinami, spusťte příkaz Copy bez parametrů:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

Kopírovací tabulka kontroluje celý obsah zdrojové tabulky, který se zkopíruje do cílové tabulky. To může snížit výkon clusteru HBA, zatímco se provádí kopírovací služba.

> [!NOTE]  
> Pokud chcete automatizovat kopírování dat mezi tabulkami, přečtěte si `hdi_copy_table.sh` skript v úložišti [Azure HBA](https://github.com/Azure/hbase-utils/tree/master/replication) na GitHubu.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Ruční shromáždění seznamu Apache ZooKeeper kvora

Pokud jsou oba clustery HDInsight ve stejné virtuální síti, jak je popsáno dříve, je překlad interního názvu hostitele automatický. Pokud chcete použít kopírovací sadu pro clustery HDInsight ve dvou samostatných virtuálních sítích, které jsou připojené pomocí VPN Gateway, musíte zadat IP adresy hostitele uzlů Zookeeper v kvoru.

Chcete-li získat názvy hostitelů kvora, spusťte následující příkaz složeného příkazu:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

Příkaz kudrlinkou načte dokument JSON s informacemi o konfiguraci HBA a příkaz grep vrátí pouze položku "adaptéry HBA. Zookeeper. kvora", například:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

Hodnota názvy hostitelů kvora je celý řetězec napravo od dvojtečky.

Chcete-li načíst IP adresy pro tyto hostitele, použijte následující příkaz kudrlinkou pro každého hostitele v předchozím seznamu:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

V tomto příkazu složeného příkazu `<zookeeperHostFullName>` je úplný název DNS hostitele Zookeeper, jako je například příklad `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net` . Výstup příkazu obsahuje IP adresu pro zadaného hostitele, například:

`100    "ip" : "10.0.0.9",`

Po shromáždění IP adres pro všechny uzly ZooKeeper v kvoru znovu sestavte cílovou adresu:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

V našem příkladu:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Snímky

Pomocí [snímků](https://hbase.apache.org/book.html#ops.snapshots) můžete v úložišti dat HBA (HBA) vytvořit zálohu dat v určitém bodě v čase. Snímky mají minimální režii a dokončení během několika sekund, protože operace snímku je efektivně zachytávání názvů všech souborů v úložišti. V okamžiku snímku se nekopírují žádná skutečná data. Snímky spoléhají na neproměnlivou povahu dat uložených v HDFS, kde jsou všechny aktualizace, odstranění a vložení znázorněny jako nová data. Můžete obnovit (*klonovat*) snímek ve stejném clusteru nebo exportovat snímek do jiného clusteru.

Pokud chcete vytvořit snímek, přihlaste se přes SSH do hlavního uzlu clusteru HDInsight HBA a spusťte `hbase` prostředí:

```console
hbase shell
```

V prostředí HBA použijte příkaz Snapshot s názvy tabulky a tohoto snímku:

```console
snapshot '<tableName>', '<snapshotName>'
```

Chcete-li obnovit snímek podle názvu v rámci `hbase` prostředí, nejprve tabulku zakažte a pak obnovte snímek a znovu povolte tabulku:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

Chcete-li obnovit snímek do nové tabulky, použijte clone_snapshot:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Pokud chcete exportovat snímek do HDFS pro použití jiným clusterem, vytvořte nejprve snímek, jak je popsáno výše, a pak použijte nástroj ExportSnapshot. Spusťte tento nástroj z relace SSH do hlavního uzlu, nikoli v rámci `hbase` prostředí:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

`<hdfsHBaseLocation>`Může to být libovolné umístění úložiště dostupné pro váš zdrojový cluster a mělo by odkazovat na složku HBA, kterou používá cílový cluster. Například pokud máte ke zdrojovému clusteru připojený sekundární Azure Storage účet a tento účet poskytuje přístup ke kontejneru, který používá výchozí úložiště cílového clusteru, můžete použít tento příkaz:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Pokud ke zdrojovému clusteru nemáte připojený sekundární Azure Storage účet, nebo pokud je zdrojový cluster místní cluster (nebo jiný cluster než HDI), může při pokusu o přístup k účtu úložiště clusteru HDI dojít k problémům s autorizací. Pokud to chcete vyřešit, zadejte klíč účtu úložiště jako parametr příkazového řádku, jak je znázorněno v následujícím příkladu. Klíč k účtu úložiště můžete získat v Azure Portal.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Po exportu snímku, SSH do hlavního uzlu cílového clusteru a obnovte snímek pomocí `restore_snapshot` příkazu, jak je popsáno výše.

Snímky poskytují úplnou zálohu tabulky v okamžiku `snapshot` příkazu. Snímky neposkytují možnost provádět přírůstkové snímky ve Windows čase, ani Neurčovat podmnožiny sloupců, které se mají zahrnout do snímku.

## <a name="replication"></a>Replikace

[HBA replikace](https://hbase.apache.org/book.html#_cluster_replication) automaticky přenáší transakce ze zdrojového clusteru do cílového clusteru pomocí asynchronního mechanismu s minimálními nároky na zdrojový cluster. Ve službě HDInsight můžete nastavit replikaci mezi clustery, kde:

* Zdrojové a cílové clustery jsou ve stejné virtuální síti.
* Zdrojové a cílové clustery jsou v různých virtuálních sítích, které jsou připojené pomocí brány VPN, ale oba clustery existují ve stejném geografickém umístění.
* Zdrojový cluster a cílové clustery jsou v různých virtuálních sítích, které jsou připojené přes bránu VPN, přičemž každý cluster existuje v jiném geografickém umístění.

Pro nastavení replikace jsou k dishlavní kroky:

1. Ve zdrojovém clusteru vytvořte tabulky a naplňte data.
2. V cílovém clusteru Vytvořte prázdné cílové tabulky se schématem zdrojové tabulky.
3. Zaregistrujte cílový cluster jako partnerský uzel na zdrojový cluster.
4. Povolte replikaci u požadovaných zdrojových tabulek.
5. Zkopírujte existující data ze zdrojových tabulek do cílových tabulek.
6. Replikace automaticky kopíruje nové změny dat do zdrojových tabulek do cílových tabulek.

Pokud chcete povolit replikaci ve službě HDInsight, použijte pro spuštěný zdrojový cluster HDInsight akci skriptu. Návod k povolení replikace v clusteru nebo experimentování s replikací na ukázkových clusterech vytvořených ve virtuálních sítích pomocí šablon správy prostředků Azure najdete v tématu [Konfigurace replikace Apache HBA](apache-hbase-replication.md). Tento článek také obsahuje pokyny pro povolení replikace metadat služby Phoenix.

## <a name="next-steps"></a>Další kroky

* [Konfigurace replikace Apache HBA](apache-hbase-replication.md)
* [Práce s nástrojem pro import a export adaptérů HBA](https://blogs.msdn.microsoft.com/data_otaku/2016/12/21/working-with-the-hbase-import-and-export-utility/)

---
title: Řešení potíží HDFS ve službě Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se práce se službou HDFS a Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: c2af536b2b6c6a4b220a877337ade9a74ad75bb8
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534987"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Řešení potíží s Apache Hadoop HDFS s využitím Azure HDInsightu

Naučte se hlavní problémy a řešení při práci se službou Hadoop systém souborů DFS (Distributed File System) (HDFS). Úplný seznam příkazů najdete v [příručce k příkazům HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) a v příručce k [prostředí systému souborů](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html).

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Návody se k místnímu HDFS přistupují v rámci clusteru?

### <a name="issue"></a>Problém

Přístup k místnímu HDFS z příkazového řádku a kódu aplikace místo pomocí Azure Blob Storage nebo Azure Data Lake Storage zevnitř v clusteru HDInsight.

### <a name="resolution-steps"></a>Postup řešení

1. Na příkazovém řádku použijte `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` doslova, jako v následujícím příkazu:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. Ze zdrojového kódu, použijte identifikátor URI `hdfs://mycluster/` doslova, jako v následující ukázkové aplikaci:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Spusťte zkompilovaný soubor. jar (například soubor s názvem `java-unit-tests-1.0.jar` ) v clusteru HDInsight s následujícím příkazem:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Výjimka úložiště pro zápis objektů BLOB

### <a name="issue"></a>Problém

Při použití `hadoop` příkazů nebo `hdfs dfs` k zápisu souborů, které jsou na clusteru HBA na 12 GB nebo větší, může docházet k následující chybě:

```error
ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
copyFromLocal: java.io.IOException
        at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
        at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
        at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
        at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
        ... 7 more
```

### <a name="cause"></a>Příčina

HBA v clusterech HDInsight ve výchozím nastavení na velikost bloku 256 KB při zápisu do služby Azure Storage. I když funguje pro adaptéry HBA rozhraní API nebo rozhraní REST API, výsledkem je chyba při použití `hadoop` `hdfs dfs` nástrojů příkazového řádku nebo.

### <a name="resolution"></a>Řešení

Použijte `fs.azure.write.request.size` k určení větší velikosti bloku. Tuto úpravu můžete provést na základě jednotlivých použití `-D` parametru pomocí parametru. Následující příkaz je příkladem použití tohoto parametru s `hadoop` příkazem:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Můžete také hodnotu `fs.azure.write.request.size` globálně zvýšit pomocí Apache Ambari. Pomocí následujících kroků můžete změnit hodnotu ve webovém uživatelském rozhraní Ambari:

1. V prohlížeči přejdete do webového uživatelského rozhraní Ambari pro váš cluster. Adresa URL je `https://CLUSTERNAME.azurehdinsight.net` , kde `CLUSTERNAME` je název vašeho clusteru. Po zobrazení výzvy zadejte jméno správce a heslo pro cluster.
2. Na levé straně obrazovky vyberte **HDFS** a pak vyberte kartu **Konfigurace** .
3. Do pole **Filter...** zadejte `fs.azure.write.request.size` .
4. Změňte hodnotu z 262144 (256 KB) na novou hodnotu. Například 4194304 (4 MB).

    ![Obrázek změny hodnoty prostřednictvím webového uživatelského rozhraní Ambari](./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png)

Další informace o použití Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>du

[`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du)Příkaz zobrazí velikosti souborů a adresářů obsažených v daném adresáři nebo délku souboru v případě, že se jedná pouze o soubor.

`-s`Možnost vytvoří agregovaný souhrn délek souborů, které se zobrazují.  
`-h`Možnost formátuje velikosti souborů.

Příklad:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>cílem

Příkaz [-RM](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) odstraní soubory zadané jako argumenty.

Příklad:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
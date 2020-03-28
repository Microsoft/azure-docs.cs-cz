---
title: 'Kurz: HDInsight Apache Storm to Storage – Azure/Data Lake'
description: Výuka – Naučte se používat Apache Storm k zápisu do úložiště kompatibilního s HDFS pro Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 579163180f6c7ba19927ca66d20bd92d1b2de52e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73241204"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Kurz: Napište apache hadoop hdfs z Apache Storm na Azure HDInsight

Tento kurz ukazuje, jak používat Apache Storm k zápisu dat do úložiště kompatibilního s HDFS, které používá Apache Storm na HDInsight. HDInsight můžete používat Azure Storage i Azure Data Lake Storage jako úložiště kompatibilní s HDFS. Storm poskytuje komponentu [HdfsBolt,](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) která zapisuje data do HDFS. Tento dokument poskytuje informace o zápisu do obou typů úložiště z hdfsbolt.

Příklad topologie použité v tomto dokumentu závisí na součástech, které jsou součástí Storm na HDInsight. Může vyžadovat úpravy pro práci s Azure Data Lake Storage při použití s jinými clustery Apache Storm.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace clusteru pomocí akce skriptu
> * Sestavení a balíček topologie
> * Nasazení a spuštění topologie
> * Zobrazit výstupní data
> * Zastavení topologie

## <a name="prerequisites"></a>Požadavky

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalován](https://maven.apache.org/install.html) podle Apache.  Maven je systém vytváření projektů pro java projekty.

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* [Schéma URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To by `wasb://` bylo pro `abfs://` Azure Storage, pro `adl://` Azure Data Lake Storage Gen2 nebo pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, identifikátor URI bude `wasbs://`.  Viz také [bezpečný přenos](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Příklad konfigurace

Následující YAML je výňatek `resources/writetohdfs.yaml` ze souboru obsaženého v příkladu. Tento soubor definuje topologii Storm pomocí frameworku [Flux](https://storm.apache.org/releases/current/flux.html) pro Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Tento YAML definuje následující položky:

* `syncPolicy`: Definuje, kdy jsou soubory synchronizovány nebo vyprázdněny do systému souborů. V tomto příkladu každých 1000 řazené kolekce členů.
* `fileNameFormat`: Definuje cestu a vzor názvu souboru, který se má použít při psaní souborů. V tomto příkladu je cesta k dispozici za běhu pomocí `.txt`filtru a přípona souboru je .
* `recordFormat`: Definuje vnitřní formát zapsaných souborů. V tomto příkladu jsou pole `|` oddělena znakem.
* `rotationPolicy`: Definuje, kdy se mají soubory otáčet. V tomto příkladu se neprovádí žádné otočení.
* `hdfs-bolt`: Používá předchozí součásti jako konfigurační parametry pro třídu. `HdfsBolt`

Další informace o rámci Flux [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)naleznete v tématu .

## <a name="configure-the-cluster"></a>Konfigurace clusteru

Ve výchozím nastavení Storm na HDInsight nezahrnuje součásti, které `HdfsBolt` se používá ke komunikaci s Azure Storage nebo Data Lake Storage v storm je cesta pro třídy. Pomocí následující akce skriptu přidejte `extlib` tyto součásti do adresáře Storm ve vašem clusteru:

| Vlastnost | Hodnota |
|---|---|
|Typ skriptu |- Vlastní|
|Bash skript URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Typ uzlu |Nimbus, vedoucí|
|Parametry |Žádný|

Informace o používání tohoto skriptu v clusteru najdete v tématu [Přizpůsobení clusterů HDInsight pomocí dokumentu akcí skriptu.](./../hdinsight-hadoop-customize-cluster-linux.md)

## <a name="build-and-package-the-topology"></a>Sestavení a balíček topologie

1. Stáhněte si [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) ukázkový projekt z vývojového prostředí.

2. Z příkazového řádku, terminálu nebo relace prostředí změňte adresáře do kořenového adresáře staženého projektu. Chcete-li vytvořit a zabalit topologii, použijte následující příkaz:

    ```cmd
    mvn compile package
    ```

    Po dokončení sestavení a balení je nový `target`adresář s názvem `StormToHdfs-1.0-SNAPSHOT.jar`, který obsahuje soubor s názvem . Tento soubor obsahuje kompilovanou topologii.

## <a name="deploy-and-run-the-topology"></a>Nasazení a spuštění topologie

1. Pomocí následujícího příkazu zkopírujte topologii do clusteru HDInsight. Nahraďte `CLUSTERNAME` se názvem clusteru.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Po dokončení nahrávání se pomocí následujícího připojení k clusteru HDInsight pomocí SSH. Nahraďte `CLUSTERNAME` se názvem clusteru.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po připojení vytvořte soubor s názvem `dev.properties`:

    ```bash
    nano dev.properties
    ```

1. Jako obsah `dev.properties` souboru použijte následující text. Podle potřeby je revidujte na základě [schématu identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Chcete-li soubor uložit, použijte __kombinaci kláves Ctrl + X__, potom __hodnotu Y__a nakonec __klávesu Enter__. Hodnoty v tomto souboru nastavují adresu URL úložiště a název adresáře, do kterého jsou data zapsána.

1. K spuštění topologie použijte následující příkaz:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Tento příkaz spustí topologii pomocí frameworku Flux jeho odesláním do uzlu Nimbus clusteru. Topologie je definována `writetohdfs.yaml` souborem obsaženým v nádobě. Soubor `dev.properties` je předán jako filtr a hodnoty obsažené v souboru jsou čteny topologií.

## <a name="view-output-data"></a>Zobrazit výstupní data

Chcete-li zobrazit data, použijte následující příkaz:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Zobrazí se seznam souborů vytvořených touto topologií. Následující seznam je příkladem dat vrácených předchozími příkazy:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Zastavení topologie

Topologií bouře spustit, dokud zastavena, nebo cluster uodstranění. Chcete-li topologii zastavit, použijte následující příkaz:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat Apache Storm k zápisu dat do úložiště kompatibilního s HDFS, které používá Apache Storm na HDInsight.

> [!div class="nextstepaction"]
> Objevte další [příklady Apache Storm pro HDInsight](apache-storm-example-topology.md)
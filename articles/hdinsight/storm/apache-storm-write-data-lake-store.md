---
title: 'Kurz: Apache Storm HDInsight do úložiště – Azure/Data Lake'
description: Kurz – Naučte se používat Apache Storm k zápisu do úložiště kompatibilního s HDFS pro Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 4e648c57be699620e669ce7db0845dad2b876095
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932561"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Kurz: zápis do Apache Hadoop HDFS z Apache Storm ve službě Azure HDInsight

V tomto kurzu se dozvíte, jak použít Apache Storm k zápisu dat do úložiště kompatibilního se službou HDFS, které používá Apache Storm ve službě HDInsight. HDInsight může jako úložiště kompatibilní s HDFS používat jak Azure Storage, tak Azure Data Lake Storage. V této části najdete komponentu [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) , která zapisuje data do HDFS. Tento dokument poskytuje informace o zápisu do libovolného typu úložiště z HdfsBolt.

Ukázková topologie použitá v tomto dokumentu spoléhá na komponenty, které jsou zahrnuty v systému HDInsight. V případě použití s jinými clustery Apache Storm může být nutné provést úpravy, aby se Azure Data Lake Storage.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace clusteru pomocí akce skriptu
> * Sestavení a zabalení topologie
> * Nasazení a spuštění topologie
> * Zobrazit výstupní data
> * Zastavení topologie

## <a name="prerequisites"></a>Požadavky

* [Java Developer Kit (JDK) verze 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) v souladu s Apache.  Maven je systém sestavení projektu pro projekty v jazyce Java.

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* [Schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) pro primární úložiště clusterů. To Azure Storage pro `wasb://` `abfs://` Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud je pro Azure Storage povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` .  Viz také [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Příklad konfigurace

Následující YAML je výňatek ze souboru, který je `resources/writetohdfs.yaml` zahrnutý v příkladu. Tento soubor definuje topologii zaplavování pomocí rozhraní pro [tok](https://storm.apache.org/releases/current/flux.html) Apache Storm.

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

* `syncPolicy`: Definuje, kdy jsou soubory synchronizovány/vyprázdněny do systému souborů. V tomto příkladu každé 1000 řazených kolekcí členů.
* `fileNameFormat`: Definuje cestu a vzor názvu souboru, který se má použít při zápisu souborů. V tomto příkladu je cesta k dispozici za běhu pomocí filtru a Přípona souboru je `.txt` .
* `recordFormat`: Definuje interní formát napsaných souborů. V tomto příkladu jsou pole oddělená `|` znakem.
* `rotationPolicy`: Definuje, kdy se mají soubory otáčet. V tomto příkladu není provedeno otočení.
* `hdfs-bolt`: Používá předchozí součásti jako konfigurační parametry pro `HdfsBolt` třídu.

Další informace o rozhraních toků naleznete v tématu [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) .

## <a name="configure-the-cluster"></a>Konfigurace clusteru

Ve výchozím nastavení zaplave v HDInsight neobsahuje součásti, které nástroj `HdfsBolt` používá ke komunikaci s Azure Storage nebo data Lake Storage v cestě třídy classpath. Pomocí následující akce skriptu přidejte tyto komponenty do adresáře pro zaplavení `extlib` v clusteru:

| Vlastnost | Hodnota |
|---|---|
|Typ skriptu |– Vlastní|
|Identifikátor URI skriptu bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Typ (typy) uzlů |Nimbus, nadřízený|
|Parametry |Žádné|

Informace o použití tohoto skriptu s clusterem naleznete v dokumentu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](./../hdinsight-hadoop-customize-cluster-linux.md) .

## <a name="build-and-package-the-topology"></a>Sestavení a zabalení topologie

1. Stáhněte si ukázkový projekt z nástroje [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) do vývojového prostředí.

2. Z příkazového řádku, terminálu nebo relace prostředí změňte adresáře na kořen staženého projektu. K sestavení a zabalení topologie použijte následující příkaz:

    ```cmd
    mvn compile package
    ```

    Po dokončení sestavení a balíčku je k dispozici nový adresář s názvem `target` , který obsahuje soubor s názvem `StormToHdfs-1.0-SNAPSHOT.jar` . Tento soubor obsahuje kompilovaná topologie.

## <a name="deploy-and-run-the-topology"></a>Nasazení a spuštění topologie

1. K zkopírování topologie do clusteru HDInsight použijte následující příkaz. Nahraďte `CLUSTERNAME` názvem clusteru.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Po dokončení nahrávání použijte následující příkaz pro připojení ke clusteru HDInsight pomocí protokolu SSH. Nahraďte `CLUSTERNAME` názvem clusteru.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po připojení pomocí následujícího příkazu vytvořte soubor s názvem `dev.properties` :

    ```bash
    nano dev.properties
    ```

1. Jako obsah souboru použijte následující text `dev.properties` . Podle vašeho [schématu identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme)upravte podle potřeby.

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Pokud chcete soubor uložit, použijte __CTRL + X__, pak __Y__ a nakonec __ENTER__. Hodnoty v tomto souboru nastavily adresu URL úložiště a název adresáře, do kterého se data zapisují.

1. K zahájení topologie použijte následující příkaz:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Tento příkaz spustí topologii pomocí rozhraní toků, protože ho odešle do uzlu Nimbus clusteru. Topologie je definována souborem, který je `writetohdfs.yaml` součástí jar. `dev.properties`Soubor se předává jako filtr a hodnoty obsažené v souboru jsou čteny topologiemi.

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

Topologie nečinnosti se spouštějí do zastavení, nebo se cluster odstraní. K zastavení topologie použijte následující příkaz:

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

V tomto kurzu jste zjistili, jak používat Apache Storm k zápisu dat do úložiště kompatibilního se systémem HDFS používaného Apache Storm v HDInsight.

> [!div class="nextstepaction"]
> Vyhledat další [příklady Apache Storm pro HDInsight](apache-storm-example-topology.md)
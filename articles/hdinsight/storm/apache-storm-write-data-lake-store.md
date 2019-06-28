---
title: Kurz – použití Apache Storm k zápisu do úložiště a Data Lake Storage – Azure HDInsight
description: Kurz – další informace o použití Apache Storm k zápisu do úložiště kompatibilního se systémem HDFS pro Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 5c1376c7d1afe9c9702cfb43a146ac1cd17d6e58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428352"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Kurz: Zápis do Apache Hadoop HDFS z Apache Storm v Azure HDInsight

Tento kurz ukazuje použití Apache Storm k zápisu dat do HDFS kompatibilního úložiště využitá službou Apache Storm v HDInsight. HDInsight, můžete použít jako HDFS kompatibilního úložiště Azure Storage a Azure Data Lake Storage. Storm poskytuje [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) komponenta, která zapisuje data do rozhraní HDFS. Tento dokument obsahuje informace o zápisu do obou typů úložiště z HdfsBolt.

Topologii příkladu v tomto dokumentu se spoléhá na součásti, které jsou součástí Storm v HDInsight. Může vyžadovat změny pro práci s Azure Data Lake Storage při použití s další clustery Apache Storm.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace clusteru pomocí skriptových akcí
> * Sestavení a zabalení topologie
> * Nasadit a spustit topologii
> * Zobrazení výstupní data
> * Zastavení topologie

## <a name="prerequisites"></a>Požadavky

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks)

* [Nástroje Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) podle Apache.  Maven je projekt sestavovacího systému pro projekty Java.

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* [Schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) jako primární úložiště vašich clusterů. To může být `wasb://` pro službu Azure Storage, `abfs://` pro Azure Data Lake Storage Gen2 nebo `adl://` pro Azure Data Lake Storage Gen1. Pokud pro Azure Storage nebo Azure Data Lake Storage Gen2 je povoleno zabezpečený přenos, identifikátor URI by `wasbs://` nebo `abfss://`, respektive najdete [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Příklad konfigurace

Následující kód YAML je výňatkem z `resources/writetohdfs.yaml` souboru zahrnutém v příkladu. Tento soubor definuje pomocí topologie Storm [tok](https://storm.apache.org/releases/1.1.2/flux.html) rámec pro Apache Storm.

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

Tato YAML definuje následující položky:

* `syncPolicy`: Definuje, kdy jsou soubory synchronizovat/vyprázdní do systému souborů. V tomto příkladu každých 1000 řazené kolekce členů.
* `fileNameFormat`: Definuje vzor název a cesta k souboru pro použití při zapisování souborů. V tomto příkladu je cesta k dispozici za běhu pomocí filtru a přípona souboru je `.txt`.
* `recordFormat`: Definuje interní formát soubory zapsané. V tomto příkladu jsou odděleny pole `|` znak.
* `rotationPolicy`: Definuje, kdy se otočí soubory. V tomto příkladu se provádí bez otočení.
* `hdfs-bolt`: Používá jako parametry konfigurace pro předchozí komponenty `HdfsBolt` třídy.

Další informace o rozhraní tok, najdete v části [ https://storm.apache.org/releases/current/flux.html ](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Konfigurace clusteru

Storm v HDInsight ve výchozím nastavení, neobsahuje součásti, které `HdfsBolt` používá ke komunikaci s Azure Storage nebo Azure Data Lake Storage v cestě pro Storm. Použijte následující akci skriptu pro přidání těchto komponent k `extlib` adresáře pro Storm v clusteru:

| Vlastnost | Hodnota |
|---|---|
|Typ skriptu |– Vlastní|
|URI skriptu bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Typy uzlů |Nimbus, správce|
|Parametry |Žádný|

Informace o použití tohoto skriptu s vaším clusterem, najdete v článku [HDInsight přizpůsobit clustery pomocí akcí skriptů](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentu.

## <a name="build-and-package-the-topology"></a>Sestavení a zabalení topologie

1. Stáhnout příklad projektu ze [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) do svého vývojového prostředí.

2. Z příkazového řádku, terminálu nebo skořápce relace změnit adresáře do kořenového adresáře ze staženého projektu. Pokud chcete sestavit a zabalit topologii, použijte následující příkaz:

    ```cmd
    mvn compile package
    ```

    Po dokončení sestavení a zabalení je nový adresář s názvem `target`, který bude obsahovat soubor s názvem `StormToHdfs-1.0-SNAPSHOT.jar`. Tento soubor obsahuje zkompilovaný topologie.

## <a name="deploy-and-run-the-topology"></a>Nasadit a spustit topologii

1. Použijte následující příkaz pro kopírování topologie do clusteru HDInsight. Nahraďte `CLUSTERNAME` s názvem clusteru.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Po dokončení nahrávání, použijte následující postup pro připojení ke clusteru HDInsight pomocí SSH. Nahraďte `CLUSTERNAME` s názvem clusteru.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Jakmile budete připojeni, použijte následující příkaz k vytvoření souboru s názvem `dev.properties`:

    ```bash
    nano dev.properties
    ```

1. Použijte následující text jako obsah `dev.properties` souboru. Na základě opravit, podle potřeby vaše [schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, pak __Y__a nakonec __Enter__. Hodnoty v tomto souboru nastavit adresu URL úložiště a název adresáře, který data se zapisují do.

1. Spustit topologii použijte následující příkaz:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Tento příkaz spustí topologii s použitím rozhraní tok odesláním do uzlu Nimbus clusteru. Topologie je definován `writetohdfs.yaml` soubor součástí soubor jar. `dev.properties` Souboru je předán jako filtr a hodnoty obsažené v souboru jsou přečteny topologie.

## <a name="view-output-data"></a>Zobrazení výstupní data

Chcete-li zobrazit data, použijte následující příkaz:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Zobrazí se seznam soubory vytvořené v této topologii. V následujícím seznamu je příkladem dat vrácených z předchozích příkazů:

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

Topologie Storm běží až do ukončení nebo odstranění clusteru. Pokud chcete ukončit topologii, použijte následující příkaz:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak pomocí Apache Storm k zápisu dat do HDFS kompatibilního úložiště využitá službou Apache Storm v HDInsight.

> [!div class="nextstepaction"]
> Objevte další [příklady Apache Storm pro HDInsight](apache-storm-example-topology.md)
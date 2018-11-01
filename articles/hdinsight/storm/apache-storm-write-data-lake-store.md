---
title: Apache Storm zapsat do úložiště a Data Lake Store – Azure HDInsight
description: Další informace o použití Apache Storm pro HDInsight zapsat do úložiště kompatibilního se systémem HDFS.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.openlocfilehash: 4e99ed88eb6a58308ecd972931ca877493504fea
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419825"
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Zápis do HDFS z Apache Storm v HDInsight

Zjistěte, jak použít k zápisu dat do HDFS kompatibilního úložiště využitá službou Apache Storm v HDInsight Storm. HDInsight můžete použít obě služby Azure Storage a Azure Data Lake úložiště jako HDFS kompatibilního úložiště. Storm poskytuje [HdfsBolt](http://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) komponenta, která zapisuje data do rozhraní HDFS. Tento dokument obsahuje informace o zápisu do obou typů úložiště z HdfsBolt. 

> [!IMPORTANT]
> Topologii příkladu v tomto dokumentu se spoléhá na součásti, které jsou součástí Storm v HDInsight. Může vyžadovat změny pro práci s Azure Data Lake Store při použití s další clustery Apache Storm.

## <a name="get-the-code"></a>Získání kódu

Projekt, který obsahuje tato topologie je k dispozici ke stažení z [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Pro kompilaci tohoto projektu, potřebujete následující konfigurace pro vývojové prostředí:

* [Java JDK 1.8](https://aka.ms/azure-jdks) nebo vyšší. HDInsight 3.5 nebo vyšší vyžadují Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Když na svoji vývojářskou pracovní stanici nainstalujete Javu a JDK, mohou být nastaveny následující proměnné prostředí. Nicméně byste měli zkontrolovat, že existují a že obsahují hodnoty správné pro váš systém.

* `JAVA_HOME` – instalační adresář sady JDK.
* `PATH` – měla by obsahovat následující cesty:
  
    * `JAVA_HOME` (nebo odpovídající cestu).
    * `JAVA_HOME\bin` (nebo odpovídající cestu).
    * Adresář, kde je nainstalovaný Maven.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Jak používat HdfsBolt s HDInsight

> [!IMPORTANT]
> Před použitím HdfsBolt se Stormem v HDInsight, musíte nejprve použít akci skriptu ke kopírování souborů vyžaduje soubor jar do `extpath` pro Storm. Další informace najdete v tématu [konfigurace clusteru](#configure) oddílu.

HdfsBolt používá schéma souboru, které poskytují návod k zápisu do rozhraní HDFS. S HDInsight použijte jednu z následujících schémat:

* `wasb://`: Používá se s účtem Azure Storage.
* `adl://`: Používá se s Azure Data Lake Store.

Následující tabulka obsahuje příklady použití souboru schématu pro různé scénáře:

| Schéma | Poznámky |
| ----- | ----- |
| `wasb:///` | Výchozí účet úložiště je kontejner objektů blob v účtu služby Azure Storage |
| `adl:///` | Výchozí účet úložiště je adresář v Azure Data Lake Store. Při vytváření clusteru zadejte adresář, v Data Lake Store, kořenový clusteru HDFS. Například `/clusters/myclustername/` adresáře. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Účet úložiště Azure (Další) jiné než výchozí, přidružené ke clusteru. |
| `adl://STORENAME/` | Kořenový adresář Data Lake Store používané clusterem. Toto schéma umožňuje přístup k datům, která se nachází mimo adresář, který obsahuje systém souborů clusteru. |

Další informace najdete v tématu [HdfsBolt](http://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) odkaz na webu Apache.org.

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
* `fileNameFormat`: Určuje cestu a název souboru vzor názvu pro použití při zapisování souborů. V tomto příkladu je cesta k dispozici za běhu pomocí filtru a přípona souboru je `.txt`.
* `recordFormat`: Definuje interní formát soubory zapsané. V tomto příkladu jsou odděleny pole `|` znak.
* `rotationPolicy`: Určuje, kdy obměna soubory. V tomto příkladu se provádí bez otočení.
* `hdfs-bolt`: Používá předchozí komponenty jako parametry konfigurace pro `HdfsBolt` třídy.

Další informace o rozhraní tok, najdete v části [ https://storm.apache.org/releases/1.1.2/flux.html ](https://storm.apache.org/releases/1.1.2/flux.html).

## <a name="configure-the-cluster"></a>Konfigurace clusteru

Storm v HDInsight ve výchozím nastavení, neobsahuje součásti, které HdfsBolt používá ke komunikaci s Azure Storage nebo Azure Data Lake Store v cestě pro Storm. Použijte následující akci skriptu pro přidání těchto komponent k `extlib` adresáře pro Storm v clusteru:

* Identifikátor URI skriptu: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Uzly platí pro: Nimbus, správce
* Parametry: žádné

Informace o použití tohoto skriptu s vaším clusterem, najdete v článku [HDInsight přizpůsobit clustery pomocí akcí skriptů](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentu.

## <a name="build-and-package-the-topology"></a>Sestavení a zabalení topologie

1. Stáhnout příklad projektu ze [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) do svého vývojového prostředí.

2. Z příkazového řádku, terminálu nebo skořápce relace změnit adresáře do kořenového adresáře ze staženého projektu. Pokud chcete sestavit a zabalit topologii, použijte následující příkaz:
   
        mvn compile package
   
    Po dokončení sestavení a zabalení je nový adresář s názvem `target`, který bude obsahovat soubor s názvem `StormToHdfs-1.0-SNAPSHOT.jar`. Tento soubor obsahuje zkompilovaný topologie.

## <a name="deploy-and-run-the-topology"></a>Nasadit a spustit topologii

1. Použijte následující příkaz pro kopírování topologie do clusteru HDInsight. Nahraďte **uživatele** s uživatelským jménem SSH jste použili při vytváření clusteru. Místo **CLUSTERNAME** zadejte název vašeho clusteru.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    Po zobrazení výzvy zadejte heslo použité při vytváření uživatele SSH pro cluster. Pokud jste použili veřejný klíč, místo hesla, budete možná muset použít `-i` parametr zadejte cestu k odpovídající privátní klíč.
   
   > [!NOTE]
   > Další informace o použití `scp` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po dokončení nahrávání, použijte následující postup pro připojení ke clusteru HDInsight pomocí SSH. Nahraďte **uživatele** s uživatelským jménem SSH jste použili při vytváření clusteru. Místo **CLUSTERNAME** zadejte název vašeho clusteru.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Po zobrazení výzvy zadejte heslo použité při vytváření uživatele SSH pro cluster. Pokud jste použili veřejný klíč, místo hesla, budete možná muset použít `-i` parametr zadejte cestu k odpovídající privátní klíč.
   
   Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Jakmile budete připojeni, použijte následující příkaz k vytvoření souboru s názvem `dev.properties`:

        nano dev.properties

4. Použijte následující text jako obsah `dev.properties` souboru:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > Tento příklad předpokládá, že váš cluster používá účet Azure Storage jako výchozí úložiště. Pokud váš cluster používá Azure Data Lake Store, použijte `hdfs.url: adl:///` místo.
    
    Chcete-li uložit soubor, použijte __Ctrl + X__, pak __Y__a nakonec __Enter__. Hodnoty v tomto souboru nastavit adresu URL Data Lake store a název adresáře, který data se zapisují do.

3. Spustit topologii použijte následující příkaz:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Tento příkaz spustí topologii s použitím rozhraní tok odesláním do uzlu Nimbus clusteru. Topologie je definován `writetohdfs.yaml` soubor součástí soubor jar. `dev.properties` Souboru je předán jako filtr a hodnoty obsažené v souboru jsou přečteny topologie.

## <a name="view-output-data"></a>Zobrazení výstupní data

Chcete-li zobrazit data, použijte následující příkaz:

    hdfs dfs -ls /stormdata/

Zobrazí se seznam soubory vytvořené v této topologii.

V následujícím seznamu je příklad dat retuned předchozí příkazy:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Zastavení topologie

Topologie Storm běží až do ukončení nebo odstranění clusteru. Pokud chcete ukončit topologii, použijte následující příkaz:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Odstranit cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak používat Storm k zápisu do služby Azure Storage a Azure Data Lake Store, zjišťovat další [příklady Storm pro HDInsight](apache-storm-example-topology.md).


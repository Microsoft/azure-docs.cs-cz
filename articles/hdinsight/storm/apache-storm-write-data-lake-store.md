---
title: Apache Storm při zápisu do úložiště nebo Data Lake Store - Azure HDInsight | Microsoft Docs
description: Další informace o použití Apache Storm k zápisu do HDFS kompatibilní úložiště pro HDInsight. Azure Storage nebo Azure Data Lake Store zadejte HDFS comptabile úložiště pro HDInsight. Tento dokument a související příklad ukazují, jak součást HdfsBolt slouží k zápisu do výchozího úložiště Storm v clusteru HDInsight.
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 149f91f3091f08da2e54458d708a17da928c1972
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131731"
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Zápis do HDFS z Apache Storm v HDInsight

Naučte se používat Storm k zápisu dat do HDFS kompatibilní úložiště používané Apache Storm v HDInsight. HDInsight můžete je používat jako úložiště HDFS kompatibilní úložiště Azure Storage a Azure Data Lake. Storm poskytuje [HdfsBolt](http://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) komponenty, která zapisuje data do HDFS. Tento dokument obsahuje informace o zápis z HdfsBolt na některý typ úložiště. 

> [!IMPORTANT]
> Příklad topologii použitou v tomto dokumentu využívá součásti, které jsou součástí Storm v HDInsight. Změny pro práci s Azure Data Lake Store při použití s další clustery Apache Storm může požadovat.

## <a name="get-the-code"></a>Získání kódu

Projekt obsahující tato topologie je k dispozici ke stažení z [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Kompilace projektu, potřebujete následující konfigurace pro vývojové prostředí:

* [Java JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) nebo vyšší. HDInsight 3.5 nebo vyšší vyžadují Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Když na svoji vývojářskou pracovní stanici nainstalujete Javu a JDK, mohou být nastaveny následující proměnné prostředí. Nicméně byste měli zkontrolovat, že existují a že obsahují hodnoty správné pro váš systém.

* `JAVA_HOME` – instalační adresář sady JDK.
* `PATH` – měla by obsahovat následující cesty:
  
    * `JAVA_HOME` (nebo odpovídající cestu).
    * `JAVA_HOME\bin` (nebo odpovídající cestu).
    * Adresář, kde je nainstalovaný Maven.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Postup použití HdfsBolt s HDInsight

> [!IMPORTANT]
> Před použitím HdfsBolt se Storm v HDInsight, musíte nejprve použít akci skriptu pro kopírování souborů vyžaduje jar do `extpath` pro Storm. Další informace najdete v tématu [konfigurovat cluster](#configure) části.

HdfsBolt používá schéma souboru, které poskytujete pochopit, jak k zápisu do HDFS. S HDInsight použijte jednu z následujících schémat:

* `wasb://`: Používá se účtu úložiště Azure.
* `adl://`: Použít s Azure Data Lake Store.

Následující tabulka obsahuje příklady použití souboru schématu pro různé scénáře:

| Schéma | Poznámky |
| ----- | ----- |
| `wasb:///` | Výchozí účet úložiště je kontejner objektů blob v účtu Azure Storage |
| `adl:///` | Výchozí účet úložiště je adresář v Azure Data Lake Store. Při vytváření clusteru zadejte adresář v Data Lake Store, který je kořenem HDFS clusteru. Například `/clusters/myclustername/` adresáře. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Účet úložiště Azure (Další) jiné než výchozí přidružen ke clusteru. |
| `adl://STORENAME/` | Kořenovém adresáři Data Lake Store používaný v clusteru. Toto schéma umožňuje přístup k datům, která se nachází mimo adresář, který obsahuje clusteru systému souborů. |

Další informace najdete v tématu [HdfsBolt](http://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) odkaz na Apache.org.

### <a name="example-configuration"></a>Příklad konfigurace

Následující YAML je výňatek ze `resources/writetohdfs.yaml` zahrnutý v příkladu. Tento soubor definuje pomocí topologie Storm [tok](https://storm.apache.org/releases/1.1.0/flux.html) framework pro Apache Storm.

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

* `syncPolicy`: Definuje, když jsou soubory synchronizována/vyprazdňuje na systém souborů. V tomto příkladu každých 1000 řazené kolekce členů.
* `fileNameFormat`: Definuje použijte při zápisu souborů vzoru pro název a cesta k souboru. V tomto příkladu jsou poskytovány cesta za běhu pomocí filtru, a přípona souboru `.txt`.
* `recordFormat`: Definuje interní formát zapisovat soubory. V tomto příkladu jsou odděleny pole `|` znak.
* `rotationPolicy`: Určuje, kdy otočení soubory. V tomto příkladu se provádí bez otočení.
* `hdfs-bolt`: Používá předchozí komponenty jako parametry konfigurace pro `HdfsBolt` třídy.

Další informace o rozhraní tok najdete v tématu [ https://storm.apache.org/releases/1.1.0/flux.html ](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>Konfigurace clusteru

Ve výchozím nastavení Storm v HDInsight neobsahuje součásti, které HdfsBolt používá ke komunikaci s Azure Storage nebo Data Lake Store v Storm je cesta pro třídy. Pomocí následující akce skriptu přidejte tyto součásti `extlib` adresář pro Storm v clusteru:

* Identifikátor URI skriptu: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Uzly, které chcete použít: Nimbus, Supervisor
* Parametry: žádné

Informace o použití tohoto skriptu k vašemu clusteru najdete v tématu [HDInsight přizpůsobit clustery pomocí akcí skriptů](./../hdinsight-hadoop-customize-cluster-linux.md) dokumentu.

## <a name="build-and-package-the-topology"></a>Sestavení a balíček topologie

1. Stáhněte si příklad projektu ze [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) na svoje vývojové prostředí.

2. Z příkazového řádku, terminálu nebo skořápce relace, změnit adresáře do kořenového adresáře staženého projektu. Pro sestavení a balíček topologii, použijte následující příkaz:
   
        mvn compile package
   
    Po dokončení sestavení a balení, je nový adresář s názvem `target`, který obsahuje soubor s názvem `StormToHdfs-1.0-SNAPSHOT.jar`. Tento soubor obsahuje kompilované topologie.

## <a name="deploy-and-run-the-topology"></a>Nasazení a spuštění topologie

1. Použijte následující příkaz pro kopírování topologie do clusteru HDInsight. Nahraďte **uživatele** uživatelským jménem SSH, které jste použili při vytvoření clusteru. Místo **CLUSTERNAME** zadejte název vašeho clusteru.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    Po zobrazení výzvy zadejte heslo použité při vytváření uživatele SSH pro cluster. Pokud jste použili veřejný klíč místo hesla, budete možná muset použít `-i` parametru určete cestu k odpovídající soukromý klíč.
   
   > [!NOTE]
   > Další informace o použití `scp` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Po dokončení nahrávání, použijte následující se připojit ke clusteru HDInsight pomocí protokolu SSH. Nahraďte **uživatele** uživatelským jménem SSH, které jste použili při vytvoření clusteru. Místo **CLUSTERNAME** zadejte název vašeho clusteru.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Po zobrazení výzvy zadejte heslo použité při vytváření uživatele SSH pro cluster. Pokud jste použili veřejný klíč místo hesla, budete možná muset použít `-i` parametru určete cestu k odpovídající soukromý klíč.
   
   Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Po připojení, použijte následující příkaz k vytvoření souboru s názvem `dev.properties`:

        nano dev.properties

4. Použít následující text jako obsah `dev.properties` souboru:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > Tento příklad předpokládá, že váš cluster používá jako úložiště pro výchozí účet úložiště Azure. Pokud váš cluster používá Azure Data Lake Store, použijte `hdfs.url: adl:///` místo.
    
    Chcete-li uložit soubor, použijte __kombinaci kláves Ctrl + X__, pak __Y__a v neposlední řadě __Enter__. Hodnoty v tomto souboru nastavit adresu URL obchodu s Data Lake a název adresáře, který data se zapisují do.

3. Použijte následující příkaz spusťte topologie:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Tento příkaz spustí topologie pomocí rozhraní tok odesláním uzel Nimbus clusteru. Topologie je definována `writetohdfs.yaml` souborů, které jsou součástí jar. `dev.properties` Souboru se předá jako filtr a hodnoty obsažené v souboru jsou číst topologii.

## <a name="view-output-data"></a>Zobrazení výstupní data

Chcete-li zobrazit data, použijte následující příkaz:

    hdfs dfs -ls /stormdata/

Zobrazí se seznam souborů vytvořených pomocí této topologii.

V následujícím seznamu je příklad dat retuned podle předchozích příkazů:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Zastavení topologie

Topologie Storm spustit, dokud nebude zastaven nebo odstranění clusteru. K zastavení topologie, použijte následující příkaz:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili jak používat Storm k zápisu do úložiště Azure a Azure Data Lake Store, zjišťování dalších [Storm příklady pro HDInsight](apache-storm-example-topology.md).


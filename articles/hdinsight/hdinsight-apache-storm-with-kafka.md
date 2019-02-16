---
title: 'Kurz: Použití Apache Storm pro čtení a zápis dat s využitím Apache Kafka – Azure HDInsight'
description: Přečtěte si, jak vytvořit streamovací kanál pomocí systémů Apache Storm a Apache Kafka ve službě HDInsight. V tomto kurzu použijete komponenty KafkaBolt a KafkaSpout ke streamování dat ze systému Kafka.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/06/2018
ms.openlocfilehash: a90546d97fbb42a0ee4ef2d8f26c1d4f189ef8d3
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310334"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>Kurz: Použití Apache Stormu s Apache Kafka v HDInsight

Tento kurz ukazuje, jak pomocí [Apache Storm](https://storm.apache.org/) topologie ke čtení a zápisu dat pomocí [Apache Kafka](https://kafka.apache.org/) v HDInsight. Tento kurz také ukazuje, jak uchování dat [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) kompatibilní úložiště na Storm cluster.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Storm a Kafka
> * Vysvětlení kódu
> * Vytvoření clusterů Kafka a Storm
> * Vytvoření topologie
> * Konfigurace topologie
> * Vytvoření tématu Kafka
> * Spuštění topologie
> * Zastavení topologií
> * Vyčištění prostředků

## <a name="prerequisites"></a>Požadavky

* Znalost vytváření témat Kafka. Další informace najdete v dokumentu [Rychlý start k systému Kafka ve službě HDInsight](./kafka/apache-kafka-get-started.md).

* Znalost vytváření a nasazení řešení Storm (topologií). Konkrétně topologií, které používají [Apache Storm tok](https://storm.apache.org/releases/current/flux.html) rozhraní framework. Další informace najdete v tématu [vytvoření topologie Apache Storm v jazyce Java](./storm/apache-storm-develop-java-topology.md) dokumentu.

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) nebo vyšší. HDInsight 3.5 nebo vyšší vyžadují Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Klient SSH (budete potřebovat příkazy `ssh` a `scp`) – informace viz [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Když na svoji vývojářskou pracovní stanici nainstalujete Javu a JDK, mohou být nastaveny následující proměnné prostředí. Nicméně byste měli zkontrolovat, že existují a že obsahují hodnoty správné pro váš systém.

* `JAVA_HOME` – instalační adresář sady JDK.
* `PATH` – měla by obsahovat následující cesty:
  
    * `JAVA_HOME` (nebo odpovídající cestu).
    * `JAVA_HOME\bin` (nebo odpovídající cestu).
    * Adresář, kde je nainstalovaný Maven.

> [!IMPORTANT]  
> Kroky v tomto dokumentu vyžadují skupinu prostředků Azure obsahující cluster Storm ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve službě Azure Virtual Network, což umožňuje přímou komunikaci clusteru Storm s clusterem Kafka.
> 
> Pro usnadnění práce tento dokument odkazuje na šablonu, která může vytvořit všechny požadované prostředky Azure. 
>
> Další informace o použití služby HDInsight ve virtuální síti najdete v dokumentu [Rozšíření služby HDInsight pomocí virtuální sítě](hdinsight-extend-hadoop-virtual-network.md).

## <a name="storm-and-kafka"></a>Storm a Kafka

Apache Storm poskytuje několik komponent pro práci s využitím Apache Kafka. V tomto kurzu jsou použité následující komponenty:

* `org.apache.storm.kafka.KafkaSpout`: Tato součást čte data z platformy Kafka. Závisí na následujících komponentách:

    * `org.apache.storm.kafka.SpoutConfig`: Konfigurace pro součásti spout poskytuje.

    * `org.apache.storm.spout.SchemeAsMultiScheme` a `org.apache.storm.kafka.StringScheme`: Jak se data z platformy Kafka transformuje na Storm řazené kolekce členů.

* `org.apache.storm.kafka.bolt.KafkaBolt`: Tato součást zapíše data do systému Kafka. Závisí na následujících komponentách:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: Popisuje, která jsou zapsána do tématu.

    * `org.apache.kafka.common.serialization.StringSerializer`: Nakonfiguruje bolt k serializaci dat jako hodnotu řetězce.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: Mapování z řazené kolekce členů struktury dat použít v rámci topologie Storm k polím v systému Kafka.

Tyto komponenty jsou dostupné v balíčku `org.apache.storm : storm-kafka`. Použijte verzi balíčku, která odpovídá verzi Stormu. Pro HDInsight 3.6 je verze Stormu 1.1.0.
Budete také potřebovat balíček `org.apache.kafka : kafka_2.10` s dalšími komponentami systému Kafka. Použijte verzi balíčku, která odpovídá verzi systému Kafka. Pro HDInsight 3.6 je verze Kafka 0.10.0.0.

Následující kód XML je deklarace závislostí v `pom.xml` pro [Apache Maven](https://maven.apache.org/) projektu:

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>0.10.0.0</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>Vysvětlení kódu

Kód použitý v tomto dokumentu je k dispozici na adrese [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

V tomto kurzu se využívají dvě topologie:

* Kafka a zápis: Generuje náhodné věty a uloží je do systému Kafka.

* Kafka-reader: Čte data z platformy Kafka a uloží jej do úložiště HDFS kompatibilní soubor pro Storm cluster.

    > [!WARNING]  
    > Pokud chcete umožnit Stormu práci s úložištěm kompatibilním s HDFS, které používá HDInsight, je nutná akce skriptu. Skript nainstaluje pro Storm několik souborů jar do cesty `extlib`. Šablona v tomto kurzu při vytváření clusteru tento skript použije automaticky.
    >
    > Pokud nepoužijete k vytvoření clusteru Storm šablonu v tomto dokumentu, pak musíte akci skriptu použít na cluster ručně.
    >
    > Akce skriptu je ve složce `https://hdiconfigactions2.blob.core.windows.net/stormextlib/stormextlib.sh` a použije se na nadřízené a nimbus uzly clusteru Storm. Další informace o použití akcí skriptu najdete v dokumentu [Přizpůsobení HDInsightu pomocí akcí skriptu](hdinsight-hadoop-customize-cluster-linux.md).

Topologie se definují pomocí komponenty [Flux](https://storm.apache.org/releases/1.1.2/flux.html). Flux byl uveden ve Stormu 0.10.x a umožňuje oddělení konfigurace topologie od kódu. Pro topologie používající architekturu Flux se topologie definuje v souboru YAML. Soubor YAML je možné zahrnout jako součást topologie. Může to ale být také samostatný soubor, který použijete při odesílání topologie. Flux také podporuje různé substituce za běhu, což se využívá i v tomto příkladu.

Pro tyto topologie jsou za běhu nastaveny následující parametry:

* `${kafka.topic}`: Název, který topologie čtení a zápis do tématu Kafka.

* `${kafka.broker.hosts}`: Hostitelé, kteří zprostředkovatelé Kafka spouštět. Informace o zprostředkovatelích využívá KafkaBolt při zápisu do systému Kafka.

* `${kafka.zookeeper.hosts}`: Hostitelé, Zookeeper, na kterých běží v clusteru Kafka.

* `${hdfs.url}`: Adresu URL souboru systému HDFSBolt komponenty. Určuje, jestli data se zapisují do účtu Azure Storage nebo Azure Data Lake Storage.

* `${hdfs.write.dir}`: Adresář, který data se zapisují do.

Další informace o topologiích Flux najdete zde: [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html)

### <a name="kafka-writer"></a>Kafka-writer

V topologii Kafka-writer přebírá komponenta Kafka bolt dva řetězcové parametry. Tyto parametry určují, která pole řazené kolekce členů se odesílají do systému Kafka jako __klíč__ a __zpráva__. Klíč se používá k třídění dat v systému Kafka. Zpráva jsou vlastní ukládaná data.

V tomto příkladu komponenta `com.microsoft.example.SentenceSpout` vydává řazenou kolekci členů obsahující dvě pole, `key` a `message`. Kafka bolt extrahuje tato pole a odešle data z nich do systému Kafka.

Samotná pole nemusí mít názvy `key` a `message`. Tyto názvy se v tomto projektu používají jen pro lepší srozumitelnost mapování.

Následující YAML obsahuje definici komponenty Kafka-writer:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-reader

V topologii Kafka-reader čte komponenta spout data ze systému Kafka jako řetězcové hodnoty. Data se pak zapíšou do protokolu Storm pomocí protokolovací komponenty a do systému souborů kompatibilního s HDFS pro cluster Storm komponentou HDFS bolt.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
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
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Náhrady vlastností

Projekt obsahuje soubor s názvem `dev.properties`, který se používá pro předávání parametrů využívaných topologiemi. Definuje následující vlastnosti:

| Soubor dev.properties | Popis |
| --- | --- |
| `kafka.zookeeper.hosts` | [Apache ZooKeeper](https://zookeeper.apache.org/) hostitelů pro Kafka cluster. |
| `kafka.broker.hosts` | Hostitelé zprostředkovatelů Kafka (pracovní uzly). |
| `kafka.topic` | Téma Kafka, které topologie používají. |
| `hdfs.write.dir` | Adresář, do kterého topologie Kafka-reader zapisuje. |
| `hdfs.url` | Systém souborů používaný clusterem Storm. Pro účty úložiště Azure Storage použijte hodnotu `wasb:///`. Azure Data Lake Storage Gen1, použijte hodnotu `adl:///`. |

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka ve službě HDInsight neposkytuje přístup ke zprostředkovatelům systému Kafka přes veřejný internet. Cokoli, co využívá systém Kafka, musí být ve stejné virtuální síti Azure. V tomto kurzu se clustery Kafka i Storm nacházejí ve stejné virtuální síti Azure. 

Následující diagram znázorňuje tok komunikace mezi Stormem a systémem Kafka:

![Diagram clusterů Storm a Kafka ve virtuální síti Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]  
> Další služby v clusteru, jako jsou SSH a [Apache Ambari](https://ambari.apache.org/) přístupné přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

K vytvoření virtuální sítě Azure a následnému vytvoření clusterů Kafka a Storm v rámci této sítě použijte následující postup:

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Šablona Azure Resource Manageru se nachází na adrese **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**. Vytvoří následující prostředky:
    
    * Skupina prostředků Azure
    * Azure Virtual Network
    * Účet služby Azure Storage
    * Kafka v HDInsight verze 3.6 (tři pracovní uzly)
    * Storm v HDInsight verze 3.6 (tři pracovní uzly)

  > [!WARNING]  
  > Pokud chcete zajistit dostupnost Kafka v HDInsightu, musí cluster obsahovat aspoň tři pracovní uzly. Tato šablona vytvoří cluster Kafka se třemi pracovními uzly.

2. Podle následujícího návodu vyplňte položky v části **Vlastní nasazení**:

    2. Pomocí následujících informací vyplňte položky v části **Přizpůsobená šablona**:

    | Nastavení | Hodnota |
    | --- | --- |
    | Předplatné | Vaše předplatné Azure |
    | Skupina prostředků | Skupina prostředků obsahující prostředky. |
    | Umístění | Oblast Azure, ve které se prostředky vytvoří. |
    | Název clusteru Kafka | Název clusteru Kafka. |
    | Název clusteru Storm | Název clusteru Storm. |
    | Uživatelské jméno přihlášení clusteru | Uživatelské jméno správce clusterů. |
    | Heslo přihlášení clusteru | Heslo správce clusterů. |
    | Uživatelské jméno SSH | Uživatel SSH, který se má pro clustery vytvořit. |
    | Heslo SSH | Heslo uživatele SSH. |
   
    ![Obrázek parametrů šablony](./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png)

3. Přečtěte si **Podmínky a ujednání** a pak vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**.

4. Nakonec zaškrtněte políčko **Připnout na řídicí panel** a vyberte **Koupit**.

> [!NOTE]  
> Vytvoření clusterů může trvat až 20 minut.

## <a name="build-the-topology"></a>Vytvoření topologie

1. Ve svém vývojovém prostředí si stáhněte projekt z adresy [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), otevřete příkazový řádek a přejděte do adresáře, kam jste projekt stáhli.

2. V adresáři **hdinsight-storm-java-kafka** následujícím příkazem zkompilujte projekt a vytvořte balíček pro nasazení:

  ```bash
  mvn clean package
  ```

    Proces vytvoří v adresáři `target` soubor balíčku s názvem `KafkaTopology-1.0-SNAPSHOT.jar`.

3. Pomocí následujících příkazů zkopírujte balíček do vašeho Stormu v clusteru HDInsight. `sshuser` nahraďte uživatelským jménem SSH pro cluster. `stormclustername` nahraďte názvem clusteru __Storm__.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Po zobrazení výzvy zadejte heslo, které jste při vytváření clusteru nastavili.

## <a name="configure-the-topology"></a>Konfigurace topologie

1. Ke zjišťování zprostředkovatelských hostitelů Kafka pro systém **Kafka** na clusteru HDInsight použijte jednu z následujících metod:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > Následující příklad Bash předpokládá, že `$CLUSTERNAME` obsahuje název clusteru __Kafka__. Dále předpokládá, že nainstalovaná verze [jq](https://stedolan.github.io/jq/) je 1.5 nebo vyšší. Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    Vrácená hodnota je obdobná následujícímu textu:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]  
    > V clusteru sice můžete mít víc než dva zprostředkovatelské hostitele, není však nutné poskytovat klientům jejich úplný seznam. Jeden nebo dva jsou postačující.

2. Pomocí jedné z následujících metod zjistěte hostitele Zookeeper pro systém __Kafka__ v clusteru HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > Následující příklad Bash předpokládá, že `$CLUSTERNAME` obsahuje název clusteru __Kafka__. Dále předpokládá instalaci [jq](https://stedolan.github.io/jq/). Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    Vrácená hodnota je obdobná následujícímu textu:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]  
    > Ačkoli máte více než dva uzly Zookeeper, není nutné poskytovat klientům úplný seznam jejich hostitelů. Jeden nebo dva jsou postačující.

    Uložte tuto hodnotu, bude se hodit později.

3. Upravte soubor `dev.properties` umístěný v kořenovém adresáři projektu. Přidejte informace o hostitelích zprostředkovatelů a Zookeeper pro cluster __Kafka__ do odpovídajících řádků v tomto souboru. Následující příklad je konfigurovaný s použitím hodnot z předchozích kroků:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

    > [!IMPORTANT]  
    > Položka `hdfs.url` je konfigurována pro cluster, který používá účet úložiště Azure Storage. Chcete-li tuto topologii použijte, pokud se cluster Storm, který používá Data Lake Storage, změňte tuto hodnotu z `wasb` k `adl`.

4. Uložte soubor `dev.properties` a následujícím příkazem ho nahrajte do clusteru **Storm**:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    **USERNAME** nahraďte uživatelským jménem SSH pro cluster. **BASENAME** nahraďte základním názvem použitým při vytváření clusteru.

## <a name="create-the-kafka-topic"></a>Vytvoření tématu Kafka

Kafka ukládá data do _tématu_. Téma je třeba vytvořit před spuštěním topologií Storm. Téma vytvořte následujícím postupem:

1. Připojte se ke clusteru __Kafka__ prostřednictvím SSH pomocí následujícího příkazu. Nahraďte `sshuser` uživatelským jménem SSH použitým při vytváření clusteru. Nahraďte `kafkaclustername` názvem clusteru Kafka:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    Po zobrazení výzvy zadejte heslo, které jste při vytváření clusteru nastavili.
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. K vytvoření tématu Kafka použijte následující příkaz. Nahraďte `$KAFKAZKHOSTS` informací o hostiteli Zookeeper, kterou jste použili při konfiguraci topologie:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz se připojí k hostiteli Zookeeper pro cluster Kafka a vytvoří nové téma s názvem `stormtopic`. Toto téma budou využívat topologie Storm.

## <a name="start-the-writer"></a>Spuštění topologie writer

1. Následujícím příkazem se připojte ke clusteru **Storm** přes SSH. `sshuser` nahraďte uživatelským jménem SSH použitým při vytváření clusteru. `stormclustername` nahraďte názvem clusteru Storm:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    Po zobrazení výzvy zadejte heslo, které jste při vytváření clusteru nastavili.
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. V okně připojení SSK ke clusteru zadáním následujícího příkazu spusťte topologii writer:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Parametry použité v tomto příkazu jsou následující:

    * `org.apache.storm.flux.Flux`: Tok slouží ke konfiguraci a spuštění této topologie.

    * `--remote`: Odešlete topologii Nimbus. Topologie bude distribuována do pracovních uzlů v clusteru.

    * `-R /writer.yaml`: Použití `writer.yaml` souboru ke konfiguraci topologie. `-R` označuje, že tento prostředek je zahrnutý v souboru jar. Je v kořenové složce souboru jar, takže `/writer.yaml` je cesta k němu.

    * `--filter`: Naplnění položek v `writer.yaml` topologii s použitím hodnoty `dev.properties` souboru. Například hodnota položky `kafka.topic` v souboru se použije k nahrazení položky `${kafka.topic}` v definici topologie.

## <a name="start-the-reader"></a>Spuštění topologie reader

1. V okně připojení ke clusteru Storm přes SSH zadáním následujícího příkazu spusťte topologii reader:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Počkejte chvíli a pak následujícím příkazem zobrazte soubory vytvořené topologií reader:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    Výstup se bude podobat následujícímu:

        Found 173 items
        -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
        -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
        -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
        ...

3. K zobrazení obsahu souboru použijte následující příkaz. Nahraďte `filename.txt` názvem souboru:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    Následující text uvádí příklad obsahu souboru:

        four score and seven years ago
        snow white and the seven dwarfs
        i am at two with nature
        snow white and the seven dwarfs
        i am at two with nature
        four score and seven years ago
        an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Zastavení topologií

V okně připojení ke clusteru Storm přes SSH zadáním následujícího příkazu zastavte topologie Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

> [!WARNING]  
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá.
> 
> Odstraněním clusteru Kafka ve službě HDInsight odstraníte také všechna data uložená v systému Kafka.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak pomocí [Apache Storm](https://storm.apache.org/) topologie, která bude zapisovat a číst z [Apache Kafka](https://kafka.apache.org/) v HDInsight. Můžete také zjistili, jak ukládat data do [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) kompatibilní úložiště využitá službou HDInsight.

Další informace o použití systému Kafka v HDInsight, najdete v článku [použití Apache Kafka Producer and Consumer API](kafka/apache-kafka-producer-consumer-api.md) dokumentu.

Informace o nasazení a monitorování topologií ve službě HDInsight na Linuxu najdete v dokumentu [Nasazení a správa topologií Apache Storm v HDInsight na Linuxu](storm/apache-storm-deploy-monitor-topology-linux.md).

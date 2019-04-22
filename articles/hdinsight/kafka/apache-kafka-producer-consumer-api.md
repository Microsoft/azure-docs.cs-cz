---
title: 'Kurz: Použití Apache Kafka Producer and Consumer API – Azure HDInsight '
description: Zjistěte, jak používat rozhraní Apache Kafka Producer and Consumer API se systémem Kafka ve službě HDInsight. V tomto kurzu zjistíte, jak používat tato rozhraní API se systémem Kafka ve službě HDInsight z aplikace Java.
services: hdinsight
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/02/2019
ms.openlocfilehash: 6b77cd9939e244fd031788164cdfe391c3e2b9d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916389"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Kurz: Použití Apache Kafka Producer and Consumer API

Zjistěte, jak používat rozhraní Apache Kafka Producer and Consumer API se systémem Kafka ve službě HDInsight.

Rozhraní Kafka Producer API umožňuje aplikacím odesílat datové proudy do clusteru Kafka. Rozhraní Kafka Consumer API umožňuje aplikacím číst datové proudy z clusteru.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Požadavky
> * Vysvětlení kódu
> * Sestavení a nasazení aplikace
> * Spuštění aplikace v clusteru

Další informace o rozhraních API najdete v dokumentaci k rozhraní [Producer API](https://kafka.apache.org/documentation/#producerapi) a [Consumer API](https://kafka.apache.org/documentation/#consumerapi) na webu Apache.

## <a name="prerequisites"></a>Požadavky

* Apache Kafka v HDInsight 3.6. Zjistěte, jak vytvořit systém Kafka na clusteru HDInsight, najdete v článku [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md).

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks) nebo ekvivalentní, například OpenJDK.

* [Nástroje Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) podle Apache.  Maven je projekt sestavovacího systému pro projekty Java.

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Vysvětlení kódu

Ukázková aplikace se nachází na adrese [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) v podadresáři `Producer-Consumer`. Aplikace se skládá primárně ze čtyř souborů:

* `pom.xml`: Tento soubor definuje závislosti projektu, verze Javy a balení metody.
* `Producer.java`: Tento soubor odešle náhodné věty Kafka pomocí konzumenta rozhraní API.
* `Consumer.java`: Tento soubor používá příjemce rozhraní API pro čtení dat z Kafka a posílat do STDOUT.
* `Run.java`: Rozhraní příkazového řádku používají ke spouštění kódu producenta a konzumenta.

### <a name="pomxml"></a>Pom.xml

V souboru `pom.xml` je důležité porozumět následujícímu:

* Závislosti: Tento projekt využívá Kafka producenta a konzumenta rozhraní API, které jsou k dispozici ve `kafka-clients` balíčku. Tuto závislost definuje následující kód XML:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]  
    > Položka `${kafka.version}` se deklaruje v části `<properties>..</properties>` souboru `pom.xml` a je nakonfigurovaná na verzi systému Kafka v clusteru HDInsight.

* Moduly plug-in: Moduly plug-in maven poskytují různé možnosti. V tomto projektu se používají následující moduly plug-in:

    * `maven-compiler-plugin`: Slouží k nastavení Java verze používané v projektu na 8. To je verze Javy, kterou používá HDInsight 3.6.
    * `maven-shade-plugin`: Použít ke generování uber jar, který obsahuje tato aplikace, stejně jako všechny závislosti. Používá se také k nastavení vstupního bodu aplikace, abyste mohli přímo spustit soubor JAR bez nutnosti zadávat hlavní třídu.

### <a name="producerjava"></a>Producer.java

Producent komunikuje s hostiteli zprostředkovatelů Kafka (pracovní uzly) a odesílá data do tématu Kafka. Následující fragment kódu je z [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) soubor [úložiště GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) a ukazuje, jak nastavit vlastnosti výrobce:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Konzument komunikuje s hostiteli zprostředkovatelů Kafka (pracovní uzly) a ve smyčce čte záznamy. Následující fragment kódu ze souboru [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) nastaví vlastnosti příjemce:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

V tomto kódu je konzument nakonfigurovaný tak, aby četl od začátku tématu (hodnota `auto.offset.reset` je nastavená na `earliest`).

### <a name="runjava"></a>Run.java

[Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) soubor poskytuje rozhraní příkazového řádku, která spustí producenta nebo příjemce kód. Jako parametr je potřeba zadat informace o hostiteli zprostředkovatele Kafka. Hodnota ID skupiny, který je používán procesem příjemce může volitelně zahrnovat. Pokud vytvoříte více instancí příjemce pomocí stejné ID skupiny, se bude zatížení můžete vyrovnávat čtení z tématu.

## <a name="build-and-deploy-the-example"></a>Sestavení a nasazení příkladu

1. Stažení a extrakci příklady z [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Nastavit aktuální adresář na umístění `hdinsight-kafka-java-get-started\Producer-Consumer` adresáře a použijte následující příkaz:

    ```cmd
    mvn clean package
    ```

    Tento příkaz vytvoří adresář s názvem `target`, který bude obsahovat soubor s názvem `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Zadejte následující příkaz pro kopírování `kafka-producer-consumer-1.0-SNAPSHOT.jar` soubor do vašeho clusteru HDInsight. Po zobrazení výzvy zadejte heslo uživatele SSH.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a>Spuštění příkladu

1. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Otevřete připojení SSH ke clusteru, tak, že zadáte následující příkaz. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Nainstalujte [jq](https://stedolan.github.io/jq/), příkazového řádku procesoru JSON. V otevřené připojení SSH, zadejte následující příkaz k instalaci `jq`:

    ```bash
    sudo apt -y install jq
    ```

3. Nastavte proměnné prostředí. Nahraďte `PASSWORD` a `CLUSTERNAME` přihlašovací heslo clusteru a clusteru název v uvedeném pořadí, a pak zadejte příkaz:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Rozbalte název clusteru správně notaci. Skutečné malých a velkých písmen na název clusteru může být jiný než byste očekávali, v závislosti na způsobu vytvoření clusteru. Tento příkaz se získat skutečný malých a velkých písmen, uložte ho do proměnné a pak zobrazí správně cased název a název, který jste zadali dříve. Zadejte následující příkaz:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. Chcete-li získat zprostředkovatele Kafka, hostitele a hostitele Apache Zookeeper, použijte následující příkaz:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

6. Vytvoří téma Kafka `myTest`, tak, že zadáte následující příkaz:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

7. Pokud chcete spustit producenta a zapsat data do tématu, použijte následující příkaz:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

8. Jakmile bude producent hotový, pomocí následujícího příkazu zahajte čtení z tématu:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    Zobrazí se počet načtených záznamů spolu s celkovým počtem.

9. Konzumenta ukončíte stisknutím __Ctrl+C__.

### <a name="multiple-consumers"></a>Víc současných konzumentů

Konzumenti Kafka při čtení záznamů používají skupiny konzumentů. Výsledkem použití skupiny s více konzumenty je vyvážení zatížení při čtení záznamů z tématu. Každý konzument ze skupiny obdrží určitou část záznamů.

Aplikace konzumenta přijímá parametr, který se použije jako ID skupiny. Například následující příkaz spustí konzumenta s použitím ID skupiny `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Konzumenta ukončíte stisknutím __Ctrl+C__.

Pokud chcete vidět tento proces v akci, použijte následující příkaz:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Tento příkaz pomocí `tmux` rozdělí terminál do dvou sloupců. V obou sloupcích je spuštěný konzument se stejnou hodnotou ID skupiny. Jakmile konzumenti dokončí čtení, všimněte si, že oba přečetli pouze část záznamů. Použití __Ctrl + C__ dvakrát ukončíte `tmux`.

Konzumace klienty ze stejné skupiny se realizuje rozdělením tématu na oddíly. V tomto vzorovém kódu má dříve vytvořené téma `test` osm oddílů. Pokud spustíte osm konzumentů, každý z nich bude číst záznamy z jednoho oddílu tématu.

> [!IMPORTANT]  
> Ve skupině příjemců nemůže být víc instancí konzumentů než má téma oddílů. V tomto příkladu může skupina konzumentů obsahovat až osm konzumentů, protože to je počet oddílů tématu. Nebo můžete mít více skupin konzumentů, každou s maximálně osmi konzumenty.

Záznamy se v systému Kafka ukládají v pořadí, ve kterém je oddíl přijme. Pro dosažení doručování záznamů ve správném pořadí *v rámci oddílu* vytvořte skupinu příjemců, ve které bude počet instancí konzumentů odpovídat počtu oddílů. Pro dosažení doručování záznamů ve správném pořadí *v rámci tématu* vytvořte skupinu obsahující pouze jednu instanci konzumenta.

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak používat Apache Kafka Producer and Consumer API s využitím Kafka v HDInsight. Další informace o práci s platformou Kafka najdete v těchto zdrojích:

* [Analýza protokolů platformy Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Apache Kafka](apache-kafka-mirroring.md)
* [Apache Kafka Streams API ve službě HDInsight](apache-kafka-streams-api.md)

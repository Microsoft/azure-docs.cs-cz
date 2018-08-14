---
title: 'Kurz: Použití rozhraní Apache Kafka Producer and Consumer API – Azure HDInsight '
description: Zjistěte, jak používat rozhraní Apache Kafka Producer and Consumer API se systémem Kafka ve službě HDInsight. V tomto kurzu zjistíte, jak používat tato rozhraní API se systémem Kafka ve službě HDInsight z aplikace Java.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/16/2018
ms.openlocfilehash: 8b20b2aa75c3872df1082ef1059000d80a2dd472
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621092"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Kurz: Použití rozhraní Apache Kafka Producer and Consumer API

Zjistěte, jak používat rozhraní Kafka Producer and Consumer API se systémem Kafka ve službě HDInsight.

Rozhraní Kafka Producer API umožňuje aplikacím odesílat datové proudy do clusteru Kafka. Rozhraní Kafka Consumer API umožňuje aplikacím číst datové proudy z clusteru.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Nastavení vývojového prostředí
> * Vysvětlení kódu
> * Sestavení a nasazení aplikace
> * Spuštění aplikace v clusteru

Další informace o rozhraních API najdete v dokumentaci k rozhraní [Producer API](https://kafka.apache.org/documentation/#producerapi) a [Consumer API](https://kafka.apache.org/documentation/#consumerapi) na webu Apache.

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Ve vývojovém prostředí potřebujete mít nainstalované následující komponenty:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) nebo ekvivalentní, například OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Klient SSH a příkaz `scp`. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Textový editor nebo Java IDE.

Když na svoji vývojářskou pracovní stanici nainstalujete Javu a JDK, mohou být nastaveny následující proměnné prostředí. Nicméně byste měli zkontrolovat, že existují a že obsahují hodnoty správné pro váš systém.

* `JAVA_HOME` – instalační adresář sady JDK.
* `PATH` – měla by obsahovat následující cesty:
  
    * `JAVA_HOME` (nebo odpovídající cestu).
    * `JAVA_HOME\bin` (nebo odpovídající cestu).
    * Adresář, kde je nainstalovaný Maven.

## <a name="set-up-your-deployment-environment"></a>Nastavení vývojového prostředí

Tento kurz vyžaduje systém Kafka ve službě HDInsight 3.6. Informace o postupu vytvoření systému Kafka v clusteru HDInsight najdete v dokumentu [Začínáme se systémem Kafka ve službě HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Vysvětlení kódu

Ukázková aplikace se nachází na adrese [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) v podadresáři `Producer-Consumer`. Aplikace se skládá primárně ze čtyř souborů:

* `pom.xml`: Tento soubor definuje závislosti projektu, verzi Javy a metody balení.
* `Producer.java`: Tento soubor do systému Kafka odešle 1 milion (1 000 000) náhodných vět pomocí rozhraní Producer API.
* `Consumer.java`: Tento soubor pomocí rozhraní Consumer API čte data ze systému Kafka a posílá je do výstupu STDOUT.
* `Run.java`: Rozhraní příkazového řádku, které slouží ke spuštění kódu producenta a konzumenta.

### <a name="pomxml"></a>Pom.xml

V souboru `pom.xml` je důležité porozumět následujícímu:

* Závislosti: Tento projekt spoléhá na rozhraní Kafka Producer and Consumer API, která jsou součástí balíčku `kafka-clients`. Tuto závislost definuje následující kód XML:

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

* Moduly plug-in: Moduly plug-in Mavenu poskytují různé funkce. V tomto projektu se používají následující moduly plug-in:

    * `maven-compiler-plugin`: Slouží k nastavení verze Javy, kterou projekt používá, na 8. To je verze Javy, kterou používá HDInsight 3.6.
    * `maven-shade-plugin`: Slouží k vygenerování souboru JAR, který obsahuje tuto aplikaci i všechny závislosti. Používá se také k nastavení vstupního bodu aplikace, abyste mohli přímo spustit soubor JAR bez nutnosti zadávat hlavní třídu.

### <a name="producerjava"></a>Producer.java

Producent komunikuje s hostiteli zprostředkovatelů Kafka (pracovní uzly) a ukládá data do tématu Kafka. Následující fragment kódu pochází ze souboru `Producer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
import java.util.Random;
import java.io.IOException;

public class Producer
{
    public static void produce(String brokers) throws IOException
    {

        // Set properties used to configure the producer
        Properties properties = new Properties();
        // Set the brokers (bootstrap servers)
        properties.setProperty("bootstrap.servers", brokers);
        // Set how to serialize key/value pairs
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // So we can generate random sentences
        Random random = new Random();
        String[] sentences = new String[] {
                "the cow jumped over the moon",
                "an apple a day keeps the doctor away",
                "four score and seven years ago",
                "snow white and the seven dwarfs",
                "i am at two with nature"
        };

        String progressAnimation = "|/-\\";
        // Produce a bunch of records
        for(int i = 0; i < 1000000; i++) {
            // Pick a sentence at random
            String sentence = sentences[random.nextInt(sentences.length)];
            // Send the sentence to the test topic
            producer.send(new ProducerRecord<String, String>("test", sentence));
            String progressBar = "\r" + progressAnimation.charAt(i % progressAnimation.length()) + " " + i;
            System.out.write(progressBar.getBytes());
        }
    }
}
```

Tento kód se připojí k hostitelům zprostředkovatelů Kafka (pracovní uzly) a pak do systému Kafka odešle 1 000 000 vět pomocí rozhraní Producer API.

### <a name="consumerjava"></a>Consumer.java

Konzument komunikuje s hostiteli zprostředkovatelů Kafka (pracovní uzly) a ve smyčce čte záznamy. Následující fragment kódu pochází ze souboru `Consumer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import java.util.Properties;
import java.util.Arrays;

public class Consumer {
    public static void consume(String brokers, String groupId) {
        // Create a consumer
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

        // Subscribe to the 'test' topic
        consumer.subscribe(Arrays.asList("test"));

        // Loop until ctrl + c
        int count = 0;
        while(true) {
            // Poll for records
            ConsumerRecords<String, String> records = consumer.poll(200);
            // Did we get any?
            if (records.count() == 0) {
                // timeout/nothing to read
            } else {
                // Yes, loop over records
                for(ConsumerRecord<String, String> record: records) {
                    // Display record and count
                    count += 1;
                    System.out.println( count + ": " + record.value());
                }
            }
        }
    }
}
```

V tomto kódu je konzument nakonfigurovaný tak, aby četl od začátku tématu (hodnota `auto.offset.reset` je nastavená na `earliest`).

### <a name="runjava"></a>Run.java

Soubor `Run.java` poskytuje rozhraní příkazového řádku, ve kterém se spustí buď kód producenta, nebo konzumenta. Jako parametr je potřeba zadat informace o hostiteli zprostředkovatele Kafka. Volitelně můžete zadat i hodnotu ID skupiny, kterou použije proces konzumenta. Pokud vytvoříte více instancí konzumentů s použitím stejného ID skupiny, budou vyrovnávat zatížení čtení z tématu.

```java
package com.microsoft.example;

import java.io.IOException;
import java.util.UUID;

// Handle starting producer or consumer
public class Run {
    public static void main(String[] args) throws IOException {
        if(args.length < 2) {
            usage();
        }

        // Get the brokers
        String brokers = args[1];
        switch(args[0].toLowerCase()) {
            case "producer":
                Producer.produce(brokers);
                break;
            case "consumer":
                // Either a groupId was passed in, or we need a random one
                String groupId;
                if(args.length == 3) {
                    groupId = args[2];
                } else {
                    groupId = UUID.randomUUID().toString();
                }
                Consumer.consume(brokers, groupId);
                break;
            default:
                usage();
        }
        System.exit(0);
    }
    // Display usage
    public static void usage() {
        System.out.println("Usage:");
        System.out.println("kafka-example.jar <producer|consumer> brokerhosts [groupid]");
        System.exit(1);
    }
}
```

## <a name="build-and-deploy-the-example"></a>Sestavení a nasazení příkladu

1. Stáhněte si příklady z adresy [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Přejděte do umístění adresáře `Producer-Consumer` a použijte následující příkaz:

    ```
    mvn clean package
    ```

    Tento příkaz vytvoří adresář s názvem `target`, který bude obsahovat soubor s názvem `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Pomocí následujících příkazů zkopírujte soubor `kafka-producer-consumer-1.0-SNAPSHOT.jar` do vašeho clusteru HDInsight:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Místo **SSHUSER** použijte jméno uživatele SSH pro váš cluster a místo **CLUSTERNAME** zadejte název clusteru. Po zobrazení výzvy zadejte heslo uživatele SSH.

## <a id="run"></a>Spuštění příkladu

1. K navázání připojení SSH ke clusteru použijte následující příkaz:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Místo **SSHUSER** použijte jméno uživatele SSH pro váš cluster a místo **CLUSTERNAME** zadejte název clusteru. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH. Další informace o použití `scp` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pokud chcete vytvořit témata Kafka, která se používají v tomto příkladu, postupujte podle následujících kroků:

    1. Pomocí následujících příkazů uložte název clusteru do proměnné a nainstalujte nástroj pro parsování JSON (`jq`). Po zobrazení výzvy zadejte název clusteru Kafka:
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. K získání hostitelů zprostředkovatelů Kafka a hostitelů Zookeeper použijte následující příkazy. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).
    
        ```bash
        export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. K vytvoření tématu `test` použijte následující příkaz:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
        ```
    4. K vytvoření tématu můžete použít také soubor JAR. Například k vytvoření tématu `test2` použijte následující příkaz:

        ```bash
        java -jar kafka-producer-consumer.jar create test2 $KAFKABROKERS
        ```

3. Pokud chcete spustit producenta a zapsat data do tématu, použijte následující příkaz:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

4. Jakmile bude producent hotový, pomocí následujícího příkazu zahajte čtení z tématu:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS
    ```
   
    Zobrazí se počet načtených záznamů spolu s celkovým počtem.

5. Konzumenta ukončíte stisknutím __Ctrl+C__.

### <a name="multiple-consumers"></a>Víc současných konzumentů

Konzumenti Kafka při čtení záznamů používají skupiny konzumentů. Výsledkem použití skupiny s více konzumenty je vyvážení zatížení při čtení záznamů z tématu. Každý konzument ze skupiny obdrží určitou část záznamů.

Aplikace konzumenta přijímá parametr, který se použije jako ID skupiny. Například následující příkaz spustí konzumenta s použitím ID skupiny `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup
```

Pokud chcete vidět tento proces v akci, použijte následující příkaz:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; attach
```

Tento příkaz pomocí `tmux` rozdělí terminál do dvou sloupců. V obou sloupcích je spuštěný konzument se stejnou hodnotou ID skupiny. Jakmile konzumenti dokončí čtení, všimněte si, že oba přečetli pouze část záznamů. Dvakrát stiskněte Ctrl + C a ukončete `tmux`.

Konzumace klienty ze stejné skupiny se realizuje rozdělením tématu na oddíly. Téma `test` vytvořené dříve má osm oddílů. Pokud spustíte osm konzumentů, každý z nich bude číst záznamy z jednoho oddílu tématu.

> [!IMPORTANT]
> Ve skupině příjemců nemůže být víc instancí konzumentů než má téma oddílů. V tomto příkladu může skupina konzumentů obsahovat až osm konzumentů, protože to je počet oddílů tématu. Nebo můžete mít více skupin konzumentů, každou s maximálně osmi konzumenty.

Záznamy se v systému Kafka ukládají v pořadí, ve kterém je oddíl přijme. Pro dosažení doručování záznamů ve správném pořadí *v rámci oddílu* vytvořte skupinu příjemců, ve které bude počet instancí konzumentů odpovídat počtu oddílů. Pro dosažení doručování záznamů ve správném pořadí *v rámci tématu* vytvořte skupinu obsahující pouze jednu instanci konzumenta.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat rozhraní Kafka Producer and Consumer API se systémem Kafka ve službě HDInsight. Další informace o práci s platformou Kafka najdete v těchto zdrojích:

* [Analýza protokolů Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Kafka](apache-kafka-mirroring.md)
* [Rozhraní API pro datové proudy Kafka pomocí HDInsight](apache-kafka-streams-api.md)

---
title: 'Kurz: Použití rozhraní Kafka Streams API – Azure HDInsight '
description: Zjistěte, jak používat rozhraní Apache Kafka Streams API se systémem Kafka ve službě HDInsight. Toto rozhraní API umožňuje provádět zpracování datových proudů mezi tématy v systému Kafka.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/17/2018
ms.openlocfilehash: d285575802dd830247533420154f6f5e868272a2
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621041"
---
# <a name="tutorial-apache-kafka-streams-api"></a>Kurz: Rozhraní Apache Kafka Streams API

Zjistěte, jak vytvořit aplikaci využívající rozhraní Kafka Streams API a spustit ji pomocí systému Kafka ve službě HDInsight. 

Aplikace použitá v tomto kurzu počítá slova v datovém proudu. Přečte textová data z tématu Kafka, extrahuje jednotlivá slova a pak uloží slova a jejich počet do jiného tématu Kafka.

> [!NOTE]
> Zpracování datových proudů Kafka se často provádí pomocí Apache Sparku nebo Stormu. V systému Kafka verze 0.10.0 (ve službě HDInsight 3.5 a 3.6) se zavedlo rozhraní Kafka Streams API. Toto rozhraní API umožňuje transformovat datové proudy mezi vstupními a výstupními tématy. V některých případech to může být alternativa k vytváření řešení streamování Sparku nebo Stormu. 
>
> Další informace o datových proudech Kafka najdete v [úvodní dokumentaci k datovým proudům](https://kafka.apache.org/10/documentation/streams/) na webu Apache.org.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Vysvětlení kódu
> * Sestavení a nasazení aplikace
> * Konfigurace témat Kafka
> * Spuštění kódu

## <a name="prerequisites"></a>Požadavky

* Cluster Kafka ve službě HDInsight 3.6. Informace o vytvoření clusteru Kafka ve službě HDInsight najdete v dokumentu [Začínáme se systémem Kafka ve službě HDInsight](apache-kafka-get-started.md).

* Dokončete kroky v dokumentu [Rozhraní Kafka Consumer and Producer API](apache-kafka-producer-consumer-api.md). Kroky v tomto dokumentu používají ukázkovou aplikaci a témata vytvořená v tomto kurzu.

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Ve vývojovém prostředí potřebujete mít nainstalované následující komponenty:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) nebo ekvivalentní, například OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Klient SSH a příkaz `scp`. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Vysvětlení kódu

Ukázková aplikace se nachází na adrese [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) v podadresáři `Streaming`. Aplikace se skládá ze dvou souborů:

* `pom.xml`: Tento soubor definuje závislosti projektu, verzi Javy a metody balení.
* `Stream.java`: Tento soubor implementuje logiku streamování.

### <a name="pomxml"></a>Pom.xml

V souboru `pom.xml` je důležité porozumět následujícímu:

* Závislosti: Tento projekt spoléhá na rozhraní Kafka Streams API, které je součástí balíčku `kafka-clients`. Tuto závislost definuje následující kód XML:

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

    * `maven-compiler-plugin`: Slouží k nastavení verze Javy, kterou projekt používá, na 8. Javu 8 vyžaduje HDInsight 3.6.
    * `maven-shade-plugin`: Slouží k vygenerování souboru JAR, který obsahuje tuto aplikaci i všechny závislosti. Používá se také k nastavení vstupního bodu aplikace, abyste mohli přímo spustit soubor JAR bez nutnosti zadávat hlavní třídu.

### <a name="streamjava"></a>Stream.java

Soubor `Stream.java` pomocí rozhraní Streams API implementuje aplikaci počítání slov. Čte data z tématu Kafka `test` a zapisuje počty slov do tématu `wordcounts`.

Následující kód definuje aplikaci počítání slov:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```


## <a name="build-and-deploy-the-example"></a>Sestavení a nasazení příkladu

Pokud chcete sestavit a nasadit projekt do clusteru Kafka ve službě HDInsight, postupujte následovně:

1. Stáhněte si příklady z adresy [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Přejděte do adresáře `Streaming` a pak pomocí následujícího příkazu vytvořte balíček jar:

    ```bash
    mvn clean package
    ```

    Tento příkaz vytvoří balíček v umístění `target/kafka-streaming-1.0-SNAPSHOT.jar`.

3. Pomocí následujícího příkazu zkopírujte soubor `kafka-streaming-1.0-SNAPSHOT.jar` do vašeho clusteru HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `clustername` zadejte název clusteru. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH. Další informace o použití `scp` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-kafka-topics"></a>Vytvoření témat Kafka

1. K navázání připojení SSH ke clusteru použijte následující příkaz:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `clustername` zadejte název clusteru. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH. Další informace o použití `scp` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pomocí následujících příkazů uložte název clusteru do proměnné a nainstalujte nástroj pro parsování JSON (`jq`). Po zobrazení výzvy zadejte název clusteru Kafka:

    ```bash
    sudo apt -y install jq
    read -p 'Enter your Kafka cluster name:' CLUSTERNAME
    ```

3. K získání hostitelů zprostředkovatelů Kafka a hostitelů Zookeeper použijte následující příkazy. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin). Zobrazí se dvě výzvy k zadání hesla.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
    ```

4. K vytvoření témat, která používá operace streamování, použijte následující příkazy:

    > [!NOTE]
    > Možná se zobrazí chyba, protože téma `test` již existuje. To je v pořádku, protože se mohlo vytvořit v kurzu k rozhraní Producer and Consumer API.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Témata slouží k následujícím účelům:

    * `test`: V tomto tématu se přijímají záznamy. Odtud čte data aplikace streamování.
    * `wordcounts`: Do tohoto tématu aplikace streamování ukládá výstup.
    * `RekeyedIntermediateTopic`: Toto téma slouží k opětovnému rozdělení dat při aktualizaci počtu pomocí operátoru `countByKey`.
    * `wordcount-example-Counts-changelog`: Toto téma používá operace `countByKey` jako úložiště stavu.

    > [!IMPORTANT]
    > Systém Kafka ve službě HDInsight je také možné nakonfigurovat tak, aby vytvářel témata automaticky. Další informace najdete v dokumentu [Konfigurace automatického vytváření témat](apache-kafka-auto-create-topics.md).

## <a name="run-the-code"></a>Spuštění kódu

1. Pokud chcete aplikaci streamování spustit jako proces na pozadí, použijte následující příkaz:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]
    > Možná se zobrazí upozornění ohledně log4j. To můžete ignorovat.

2. K odesílání záznamů do tématu `test` použijte následující příkaz, který spustí aplikaci producenta:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Jakmile bude producent hotový, pomocí následujícího příkazu zobrazte informace uložené v tématu `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    > [!NOTE]
    > Parametry `--property` říkají konzole konzumenta, aby vytiskla klíč (slovo) společně s počtem (hodnota). Tento parametr také konfiguruje deserializátor, který se použije při čtení těchto hodnot ze systému Kafka.

    Výstup se bude podobat následujícímu:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > Parametr `--from-beginning` konfiguruje konzumenta tak, aby začal číst záznamy uložené v tématu od začátku. Počet se zvýší při každém zjištění slova, takže téma obsahuje pro každé slovo několik záznamů s rostoucím počtem.

7. Producenta ukončíte stisknutím __Ctrl+C__. Pokračujte a pomocí __Ctrl + C__ ukončete aplikaci i konzumenta.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat rozhraní Kafka Streams API se systémem Kafka ve službě HDInsight. Další informace o práci s platformou Kafka najdete v těchto zdrojích:

* [Analýza protokolů Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Kafka](apache-kafka-mirroring.md)

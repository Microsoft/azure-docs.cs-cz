---
title: 'Kurz: Použít API datové proudy Apache Kafka – Azure HDInsight '
description: Zjistěte, jak používat rozhraní Apache Kafka Streams API se systémem Kafka ve službě HDInsight. Toto rozhraní API umožňuje provádět zpracování datových proudů mezi tématy v systému Kafka.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/02/2019
ms.openlocfilehash: 9425af0f39d14287b49fe06a81172281feb24e83
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715973"
---
# <a name="tutorial-apache-kafka-streams-api"></a>Kurz: Apache Kafka streams API

Zjistěte, jak vytvořit aplikaci, která používá rozhraní API Apache Kafka datových proudů a spustit ji s využitím Kafka v HDInsight. 

Aplikace použitá v tomto kurzu počítá slova v datovém proudu. Přečte textová data z tématu Kafka, extrahuje jednotlivá slova a pak uloží slova a jejich počet do jiného tématu Kafka.

> [!NOTE]  
> Zpracování datových proudů Kafka se často provádí pomocí Apache Spark nebo Apache Storm. Kafka verze 1.1.0 (v HDInsight 3.5 a 3.6) zavedla rozhraní API datové proudy Kafka. Toto rozhraní API umožňuje transformovat datové proudy mezi vstupními a výstupními tématy. V některých případech to může být alternativa k vytváření řešení streamování Sparku nebo Stormu. 
>
> Další informace o datových proudech Kafka najdete v [úvodní dokumentaci k datovým proudům](https://kafka.apache.org/10/documentation/streams/) na webu Apache.org.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vysvětlení kódu
> * Sestavení a nasazení aplikace
> * Konfigurace témat Kafka
> * Spuštění kódu

## <a name="prerequisites"></a>Požadavky

* Cluster Kafka ve službě HDInsight 3.6. Zjistěte, jak vytvořit systém Kafka na clusteru HDInsight, najdete v článku [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md) dokumentu.

* Proveďte kroky v [Apache Kafka příjemce a rozhraní API pro producenta](apache-kafka-producer-consumer-api.md) dokumentu. Kroky v tomto dokumentu používají ukázkovou aplikaci a témata vytvořená v tomto kurzu.

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks) nebo ekvivalentní, například OpenJDK.

* [Nástroje Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) podle Apache.  Maven je projekt sestavovacího systému pro projekty Java.

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Vysvětlení kódu

Ukázková aplikace se nachází na adrese [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) v podadresáři `Streaming`. Aplikace se skládá ze dvou souborů:

* `pom.xml`: Tento soubor definuje závislosti projektu, verze Javy a balení metody.
* `Stream.java`: Tento soubor implementuje logiku datových proudů.

### <a name="pomxml"></a>Pom.xml

V souboru `pom.xml` je důležité porozumět následujícímu:

* Závislosti: Tento projekt využívá rozhraní API datové proudy Kafka poskytuje `kafka-clients` balíčku. Tuto závislost definuje následující kód XML:

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

    * `maven-compiler-plugin`: Slouží k nastavení Java verze používané v projektu na 8. Javu 8 vyžaduje HDInsight 3.6.
    * `maven-shade-plugin`: Použít ke generování uber jar, který obsahuje tato aplikace, stejně jako všechny závislosti. Používá se také k nastavení vstupního bodu aplikace, abyste mohli přímo spustit soubor JAR bez nutnosti zadávat hlavní třídu.

### <a name="streamjava"></a>Stream.java

Soubor [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) pomocí rozhraní Streams API implementuje aplikaci počítání slov. Čte data z tématu Kafka `test` a zapisuje počty slov do tématu `wordcounts`.

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

1. Nastavit aktuální adresář na umístění `hdinsight-kafka-java-get-started-master\Streaming` adresáře a pak zadáním následujícího příkazu vytvořte balíček jar:

    ```cmd
    mvn clean package
    ```

    Tento příkaz vytvoří balíček v umístění `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `clustername` zadejte název clusteru. Použijte následující příkaz pro kopírování `kafka-streaming-1.0-SNAPSHOT.jar` soubor do vašeho clusteru HDInsight. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Vytvářejte témata Apache Kafka

1. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Otevřete připojení SSH ke clusteru, tak, že zadáte následující příkaz. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH.

    ```bash
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

5. Chcete-li získat zprostředkovatele Kafka, hostitele a hostitele Apache Zookeeper, použijte následující příkazy. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin). Zobrazí se dvě výzvy k zadání hesla.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`;
    export KAFKABROKERS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`;
    ```

6. K vytvoření témat, která používá operace streamování, použijte následující příkazy:

    > [!NOTE]  
    > Možná se zobrazí chyba, protože téma `test` již existuje. To je v pořádku, protože se mohlo vytvořit v kurzu k rozhraní Producer and Consumer API.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Témata slouží k následujícím účelům:

   * `test`: Toto téma je, kde jsou přijímány záznamy. Odtud čte data aplikace streamování.
   * `wordcounts`: Toto téma je, kde streamování aplikace ukládá svůj výstup.
   * `RekeyedIntermediateTopic`: V tomto tématu se používá k znovu rozdělovat data, jako je počet aktualizovat `countByKey` operátor.
   * `wordcount-example-Counts-changelog`: Toto téma je stav úložiště používané konvencemi `countByKey` operace

     > [!IMPORTANT]  
     > Systém Kafka ve službě HDInsight je také možné nakonfigurovat tak, aby vytvářel témata automaticky. Další informace najdete v dokumentu [Konfigurace automatického vytváření témat](apache-kafka-auto-create-topics.md).

## <a name="run-the-code"></a>Spuštění kódu

1. Pokud chcete aplikaci streamování spustit jako proces na pozadí, použijte následující příkaz:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    > [!NOTE]  
    > Může zobrazit upozornění týkající se Apache log4j. To můžete ignorovat.

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

4. Producenta ukončíte stisknutím __Ctrl+C__. Pokračujte a pomocí __Ctrl + C__ ukončete aplikaci i konzumenta.

5. Pokud chcete odstranit témata používat streamování operace, použijte následující příkazy:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak pomocí rozhraní API Apache Kafka datové proudy Kafka v HDInsight. Další informace o práci s platformou Kafka najdete v těchto zdrojích:

* [Analýza protokolů platformy Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Apache Kafka](apache-kafka-mirroring.md)

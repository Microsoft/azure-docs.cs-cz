---
title: 'Kurz: Použití rozhraní Kafka Streams API – Azure HDInsight '
description: Kurz – Naučte se používat rozhraní API pro Apache Kafka streamování s Kafka ve službě HDInsight. Toto rozhraní API umožňuje provádět zpracování datových proudů mezi tématy v systému Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/20/2020
ms.openlocfilehash: 0174c40a0fada0f78cc8d52f5c45b991c3851da0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85850558"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Kurz: použití rozhraní API streamování Apache Kafka ve službě Azure HDInsight

Naučte se, jak vytvořit aplikaci, která používá rozhraní API služby Apache Kafka Streams, a spusťte ji s Kafka ve službě HDInsight.

Aplikace použitá v tomto kurzu počítá slova v datovém proudu. Přečte textová data z tématu Kafka, extrahuje jednotlivá slova a pak uloží slova a jejich počet do jiného tématu Kafka.

Zpracování streamu Kafka se často provádí pomocí Apache Spark nebo Apache Storm. Kafka verze 1.1.0 (ve službě HDInsight 3,5 a 3,6) zavedla rozhraní API streamování Kafka. Toto rozhraní API umožňuje transformovat datové proudy mezi vstupními a výstupními tématy. V některých případech to může být alternativa k vytváření řešení streamování Sparku nebo Stormu.

Další informace o datových proudech Kafka najdete v [úvodní dokumentaci k datovým proudům](https://kafka.apache.org/10/documentation/streams/) na webu Apache.org.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vysvětlení kódu
> * Sestavení a nasazení aplikace
> * Konfigurace témat Kafka
> * Spuštění kódu

## <a name="prerequisites"></a>Požadavky

* Cluster Kafka ve službě HDInsight 3.6. Informace o tom, jak vytvořit Kafka v clusteru HDInsight, najdete v dokumentu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md) .

* Proveďte kroky v dokumentu [Apache Kafka příjemce a rozhraní API pro výrobce](apache-kafka-producer-consumer-api.md) . Kroky v tomto dokumentu používají ukázkovou aplikaci a témata vytvořená v tomto kurzu.

* [Java Developer Kit (JDK) verze 8](https://aka.ms/azure-jdks) nebo ekvivalent, jako je například OpenJDK.

* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) v souladu s Apache.  Maven je systém sestavení projektu pro projekty v jazyce Java.

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Vysvětlení kódu

Ukázková aplikace je umístěna [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) v `Streaming` podadresáři v adresáři. Aplikace se skládá ze dvou souborů:

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

    Položka `${kafka.version}` se deklaruje v části `<properties>..</properties>` souboru `pom.xml` a je nakonfigurovaná na verzi systému Kafka v clusteru HDInsight.

* Moduly plug-in: Moduly plug-in Mavenu poskytují různé funkce. V tomto projektu se používají následující moduly plug-in:

    * `maven-compiler-plugin`: Slouží k nastavení verze Javy, kterou projekt používá, na 8. Javu 8 vyžaduje HDInsight 3.6.
    * `maven-shade-plugin`: Používá se ke generování jar Uber, který obsahuje tuto aplikaci, a všechny závislosti. Slouží také k nastavení vstupního bodu aplikace, aby bylo možné přímo spustit soubor JAR bez nutnosti zadat hlavní třídu.

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

1. Nastavte aktuální adresář na umístění `hdinsight-kafka-java-get-started-master\Streaming` adresáře a potom pomocí následujícího příkazu vytvořte balíček JAR:

    ```cmd
    mvn clean package
    ```

    Tento příkaz vytvoří balíček v umístění `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `clustername` zadejte název clusteru. K zkopírování `kafka-streaming-1.0-SNAPSHOT.jar` souboru do clusteru HDInsight použijte následující příkaz. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Vytvoření Apache Kafka témata

1. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Zadáním následujícího příkazu otevřete připojení SSH ke clusteru. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Nainstalujte [JQ](https://stedolan.github.io/jq/)procesor JSON pro příkazový řádek. V otevřeném připojení SSH zadejte následující příkaz, který chcete nainstalovat `jq` :

    ```bash
    sudo apt -y install jq
    ```

3. Nastavte proměnnou hesla. Nahraďte `PASSWORD` heslem přihlášení clusteru a pak zadejte příkaz:

    ```bash
    export password='PASSWORD'
    ```

4. Extrahuje správně použita název clusteru. V závislosti na tom, jak byl cluster vytvořen, může být skutečná velikost názvu clusteru odlišná, než očekáváte. Tento příkaz získá skutečnou velikost písmen a uloží ji do proměnné. Zadejte následující příkaz:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Pokud provádíte tento proces mimo cluster, existuje jiný postup pro uložení názvu clusteru. Získá název clusteru z Azure Portal malými písmeny. Pak v následujícím příkazu nahraďte název clusteru `<clustername>` a spusťte ho: `export clusterName='<clustername>'` .  

5. Pokud chcete získat hostitele zprostředkovatele Kafka a hostitele Apache Zookeeper, použijte následující příkazy. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin).

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Tyto příkazy vyžadují přístup k Ambari. Pokud je váš cluster za NSG, spusťte tyto příkazy z počítače, který má přístup k Ambari.

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

   * `test`: V tomto tématu se přijímají záznamy. Odtud čte data aplikace streamování.
   * `wordcounts`: Do tohoto tématu aplikace streamování ukládá výstup.
   * `RekeyedIntermediateTopic`: Toto téma slouží k opětovnému rozdělení dat při aktualizaci počtu pomocí operátoru `countByKey`.
   * `wordcount-example-Counts-changelog`: Toto téma používá operace `countByKey` jako úložiště stavu.

    Systém Kafka ve službě HDInsight je také možné nakonfigurovat tak, aby vytvářel témata automaticky. Další informace najdete v dokumentu [Konfigurace automatického vytváření témat](apache-kafka-auto-create-topics.md).

## <a name="run-the-code"></a>Spuštění kódu

1. Pokud chcete aplikaci streamování spustit jako proces na pozadí, použijte následující příkaz:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    Může se zobrazit upozornění týkající se Apache log4j. To můžete ignorovat.

2. K odesílání záznamů do tématu `test` použijte následující příkaz, který spustí aplikaci producenta:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. Jakmile bude producent hotový, pomocí následujícího příkazu zobrazte informace uložené v tématu `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    Parametry `--property` říkají konzole konzumenta, aby vytiskla klíč (slovo) společně s počtem (hodnota). Tento parametr také konfiguruje deserializátor, který se použije při čtení těchto hodnot ze systému Kafka.

    Výstup se bude podobat následujícímu:

    ```output
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
    ```

    Parametr `--from-beginning` konfiguruje konzumenta tak, aby začal číst záznamy uložené v tématu od začátku. Počet se zvýší při každém zjištění slova, takže téma obsahuje pro každé slovo několik záznamů s rostoucím počtem.

4. Producenta ukončíte stisknutím __Ctrl+C__. Pokračujte a pomocí __Ctrl + C__ ukončete aplikaci i konzumenta.

5. Chcete-li odstranit témata používaná operací streamování, použijte následující příkazy:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat rozhraní API pro Apache Kafka streamování s Kafka ve službě HDInsight. Další informace o práci s Kafka získáte pomocí následujících kroků.

> [!div class="nextstepaction"]
> [Analýza protokolů Apache Kafka](apache-kafka-log-analytics-operations-management.md)

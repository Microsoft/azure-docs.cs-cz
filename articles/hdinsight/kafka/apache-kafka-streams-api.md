---
title: Použití rozhraní Apache Kafka datové proudy API - Azure HDInsight | Microsoft Docs
description: Naučte se používat rozhraní API Apache Kafka datových proudů s Kafka v HDInsight. Toto rozhraní API umožňuje provádět mezi témata v Kafka zpracování datového proudu.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 36d67cdb99871f3948db1f6497b1a4638df4f3f1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-streams-api"></a>Datové proudy Apache Kafka rozhraní API

Zjistěte, jak vytvořit aplikaci, která používá rozhraní API Kafka datové proudy a spustit ji s Kafka v HDInsight.

Při práci s Apache Kafka, se často provádí zpracování datového proudu pomocí Apache Spark nebo Storm. Kafka verze 0.10.0 (v HDInsight 3.5 a 3.6) zavedla rozhraní API Kafka datových proudů. Toto rozhraní API umožňuje transformovat datové proudy mezi vstupními a výstupními témata, v aplikaci, která běží na Kafka. V některých případech to může být alternativu k vytvoření Spark nebo Storm řešení datového proudu. Další informace o datových proudů Kafka, najdete v článku [Úvod do datových proudů](https://kafka.apache.org/10/documentation/streams/) dokumentace na Apache.org.

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Ve vývojovém prostředí potřebujete mít nainstalované následující komponenty:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) nebo ekvivalentní, například OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Klient SSH a příkaz `scp`. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Nastavení prostředí pro nasazení

Tento příklad vyžaduje Kafka na HDInsight 3.6. Naučte se vytvářet Kafka na clusteru HDInsight, najdete v článku [začínat Kafka v HDInsight](apache-kafka-get-started.md) dokumentu.

## <a name="build-and-deploy-the-example"></a>Sestavení a nasazení v příkladu

Použijte následující postup pro sestavení a nasazení projektu do vaší Kafka na clusteru HDInsight.

1. Stáhněte si příklady z [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Přejděte do adresáře `Streaming` adresáře a potom použijte následující příkaz, který má-li vytvořit balíček jar:

    ```bash
    mvn clean package
    ```

    Tento příkaz vytvoří adresář s názvem `target`, který bude obsahovat soubor s názvem `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Použijte následující příkaz pro kopírování `kafka-streaming-1.0-SNAPSHOT.jar` souboru ke svému clusteru HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Místo **SSHUSER** použijte jméno uživatele SSH pro váš cluster a místo **CLUSTERNAME** zadejte název clusteru. Pokud se zobrazí výzva, zadejte heslo pro uživatelský účet SSH. Další informace o používání `scp` s HDInsight, najdete v části [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-the-example"></a>Spuštění ukázkového

1. Chcete-li otevřít připojení SSH do clusteru, použijte následující příkaz:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Místo **SSHUSER** použijte jméno uživatele SSH pro váš cluster a místo **CLUSTERNAME** zadejte název clusteru. Pokud se zobrazí výzva, zadejte heslo pro uživatelský účet SSH. Další informace o používání `scp` s HDInsight, najdete v části [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. K vytvoření témata, které jsou používány v tomto příkladu Kafka, použijte následující příkazy:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    Nahraďte __název clusteru__ s názvem clusteru HDInsight. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru HDInsight.

    > [!NOTE]
    > Pokud vaše přihlášení clusteru je jiný než výchozí hodnotu `admin`, nahraďte `admin` hodnotu v předchozích příkazech nahraďte názvem přihlášení clusteru.

5. Chcete-li spustit tento příklad, musíte udělat tři věci:

    * Spuštění řešení datové proudy, které jsou obsažené v `kafka-streaming.jar`.
    * Spustit producent, který zapíše do `test` tématu.
    * Spustit příjemce, aby mohli zobrazit výstup zapsán do `wordcounts` tématu

    > [!NOTE]
    > Musíte ověřit, jestli `auto.create.topics.enable` je nastavena na `true` v konfiguračním souboru Kafka zprostředkovatele. Tato vlastnost může být prohlížet a upravovat v pokročilé Kafka zprostředkovatele konfiguračního souboru pomocí webového uživatelského rozhraní Ambari. Jinak je potřeba vytvořit zprostředkující tématu `RekeyedIntermediateTopic` ručně před spuštěním v tomto příkladu pomocí následujícího příkazu:
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic  --zookeeper $KAFKAZKHOSTS
    ```
    
    Tyto operace může provést otevřením tři relace SSH. Ale pak budete muset nastavit `$KAFKABROKERS` a `$KAFKAZKHOSTS` pro každou spuštěním kroku 4 z tohoto oddílu v každé relaci SSH. Jednodušší řešením je použití `tmux` nástroje, které můžete rozdělit aktuální zobrazení SSH do více oddílů. Ke spuštění datového proudu, výrobce a příjemce pomocí `tmux`, použijte následující příkaz:

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    Tento příkaz rozdělí zobrazení SSH do tří částí:

    * Spustí konzolu příjemce, který čte zprávy z levé části `wordcounts` tématu: `/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > `--property` Parametry říct konzole příjemce tisknout klíč (word) společně se počet (hodnota). Tento parametr nakonfiguruje taky deserializátor pro použití při čtení z Kafka tyto hodnoty.

    * V horní pravé části spustí řešení datové proudy rozhraní API: `java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * Pravé dolní části spouští producent konzoly a čeká na zadání zprávy k odeslání do `test` tématu: `/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. Po `tmux` příkaz rozdělí zobrazení, ukazatelem je v pravé dolní části. Spusťte zadáním věty. Po každé větě je aktualizována v levém podokně na počet jedinečných slov. Výstup se bude podobat následujícímu:
   
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
    > Počet se zvýší pokaždé, když se načte nové slovo.

7. Použití __kombinaci kláves Ctrl + C__ ukončíte Autor. Pokračovat v používání __kombinaci kláves Ctrl + C__ ukončete aplikaci a příjemce.

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak používat rozhraní API datové proudy Kafka s Kafka v HDInsight. Další informace o práci s platformou Kafka najdete v těchto zdrojích:

* [Analýza protokolů Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Kafka](apache-kafka-mirroring.md)
* [Rozhraní API pro producenta a konzumenta Kafka pomocí HDInsight](apache-kafka-producer-consumer-api.md)
* [Použití streamování Apache Sparku (DStream) se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití strukturovaného streamování Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Požití strukturovaného streamu Apache Spark k přesunu dat z Kafka na HDInsight do Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Připojení k systému Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)

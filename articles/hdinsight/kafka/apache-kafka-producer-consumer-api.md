---
title: Použít Apache Kafka producent a příjemce rozhraní API – Azure HDInsight | Microsoft Docs
description: Naučte se používat rozhraní API příjemce a Apache Kafka producent s Kafka v HDInsight. Tato rozhraní API umožňují vyvíjet aplikace, které pro zápis a čtení z Apache Kafka.
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
ms.openlocfilehash: 01592401c4c88adeed49b11df4e7963e27b1bcee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Apache Kafka producent a příjemce rozhraní API

Naučte se vytvářet aplikace, která používá rozhraní API příjemce a Kafka producent s Kafka v HDInsight.

Dokumentaci k rozhraní API, najdete v části [producent API](https://kafka.apache.org/documentation/#producerapi) a [příjemce API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Ve vývojovém prostředí potřebujete mít nainstalované následující komponenty:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) nebo ekvivalentní, například OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Klient SSH a příkaz `scp`. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Nastavení prostředí pro nasazení

Tento příklad vyžaduje Kafka na HDInsight 3.6. Naučte se vytvářet Kafka na clusteru HDInsight, najdete v článku [začínat Kafka v HDInsight](apache-kafka-get-started.md) dokumentu.

## <a name="build-and-deploy-the-example"></a>Sestavení a nasazení v příkladu

1. Stáhněte si příklady z [ https://github.com/Azure-Samples/hdinsight-kafka-java-get-started ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Změňte adresáře na umístění `Producer-Consumer` adresáře a použijte následující příkaz:

    ```
    mvn clean package
    ```

    Tento příkaz vytvoří adresář s názvem `target`, který bude obsahovat soubor s názvem `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Pomocí následujících příkazů zkopírujte soubor `kafka-producer-consumer-1.0-SNAPSHOT.jar` do vašeho clusteru HDInsight:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Místo **SSHUSER** použijte jméno uživatele SSH pro váš cluster a místo **CLUSTERNAME** zadejte název clusteru. Po zobrazení výzvy zadejte heslo uživatele SSH.

## <a id="run"></a> Spuštění ukázkového

1. Chcete-li otevřít připojení SSH do clusteru, použijte následující příkaz:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Místo **SSHUSER** použijte jméno uživatele SSH pro váš cluster a místo **CLUSTERNAME** zadejte název clusteru. Pokud se zobrazí výzva, zadejte heslo pro uživatelský účet SSH. Další informace o používání `scp` s HDInsight, najdete v části [použití SSH s HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. K vytvoření témata, které jsou používány v tomto příkladu Kafka, použijte následující příkazy:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Nahraďte __název clusteru__ s názvem clusteru HDInsight. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru HDInsight.

    > [!NOTE]
    > Pokud vaše přihlášení clusteru je jiný než výchozí hodnotu `admin`, nahraďte `admin` hodnotu v předchozích příkazech nahraďte názvem přihlášení clusteru.

3. Chcete-li spustit Autor a zapsat data do tématu, použijte následující příkaz:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. Po dokončení Autor, použijte následující příkaz přečíst v tématu:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    Zobrazí se počet načtených záznamů spolu s celkovým počtem.

5. Konzumenta ukončíte stisknutím __Ctrl+C__.

### <a name="multiple-consumers"></a>Víc současných konzumentů

Konzumenti Kafka při čtení záznamů používají skupiny konzumentů. Výsledkem použití skupiny s více konzumenty je vyvážení zatížení při čtení záznamů z tématu. Každý konzument ze skupiny obdrží určitou část záznamů.

Příjemce aplikace přijímá parametr, který se používá jako ID skupiny. Například následující příkaz spustí příjemce pomocí ID skupiny z `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Pokud chcete zobrazit tento proces v akci, použijte následující postup

1. Opakujte kroky 1 a 2 v [spuštění příkladu](#run) části otevřete novou relaci SSH.

2. V obou relace SSH zadejte následující příkaz:

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > Zadejte oba příkazy současně, tak, aby obě běží ve stejnou dobu.

    Všimněte si, že počet zpráv ke čtení, není stejný jako předchozí části, kde jste měli jenom jednoho příjemce. Místo toho čtení zprávy je rozdělená mezi instancemi.

Konzumace klienty ze stejné skupiny se realizuje rozdělením tématu na oddíly. Téma `test` vytvořené dříve má osm oddílů. Pokud otevřete osm relací SSH a v každé relaci spustíte konzumenta, bude každý z nich číst záznamy z jednoho oddílu tématu.

> [!IMPORTANT]
> Ve skupině příjemců nemůže být víc instancí konzumentů než má téma oddílů. V tomto příkladu může skupina konzumentů obsahovat až osm konzumentů, protože to je počet oddílů tématu. Nebo můžete mít více skupin konzumentů, každou s maximálně osmi konzumenty.

Záznamy se v systému Kafka ukládají v pořadí, ve kterém je oddíl přijme. Pro dosažení doručování záznamů ve správném pořadí *v rámci oddílu* vytvořte skupinu příjemců, ve které bude počet instancí konzumentů odpovídat počtu oddílů. Pro dosažení doručování záznamů ve správném pořadí *v rámci tématu* vytvořte skupinu obsahující pouze jednu instanci konzumenta.

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak používat Kafka producent a příjemce rozhraní API s Kafka v HDInsight. Další informace o práci s platformou Kafka najdete v těchto zdrojích:

* [Analýza protokolů Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replikace dat mezi clustery Kafka](apache-kafka-mirroring.md)
* [Rozhraní API pro datové proudy Kafka pomocí HDInsight](apache-kafka-streams-api.md)
* [Použití streamování Apache Sparku (DStream) se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití strukturovaného streamování Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Požití strukturovaného streamu Apache Spark k přesunu dat z Kafka na HDInsight do Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Připojení k systému Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)
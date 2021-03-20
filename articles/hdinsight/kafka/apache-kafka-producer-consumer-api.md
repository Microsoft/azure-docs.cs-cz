---
title: 'Kurz: Apache Kafka & zákaznických rozhraní API pro zákazníky – Azure HDInsight'
description: Zjistěte, jak používat rozhraní Apache Kafka Producer and Consumer API se systémem Kafka ve službě HDInsight. V tomto kurzu zjistíte, jak používat tato rozhraní API se systémem Kafka ve službě HDInsight z aplikace Java.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: e5a635a8837aadaf423c6f3a0925dbac4080e60f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945176"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Kurz: Použití rozhraní Apache Kafka Producer and Consumer API

Zjistěte, jak používat rozhraní Apache Kafka Producer and Consumer API se systémem Kafka ve službě HDInsight.

Rozhraní Kafka Producer API umožňuje aplikacím odesílat datové proudy do clusteru Kafka. Rozhraní Kafka Consumer API umožňuje aplikacím číst datové proudy z clusteru.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Požadavky
> * Vysvětlení kódu
> * Sestavení a nasazení aplikace
> * Spuštění aplikace v clusteru

Další informace o rozhraních API najdete v dokumentaci k rozhraní [Producer API](https://kafka.apache.org/documentation/#producerapi) a [Consumer API](https://kafka.apache.org/documentation/#consumerapi) na webu Apache.

## <a name="prerequisites"></a>Předpoklady

* Apache Kafka v clusteru HDInsight. Informace o tom, jak vytvořit cluster, najdete v tématu [Začínáme s Apache Kafka v HDInsight](apache-kafka-get-started.md).
* [Java Developer Kit (JDK) verze 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) nebo ekvivalent, jako je například OpenJDK.
* [Apache Maven](https://maven.apache.org/download.cgi) správně [nainstalované](https://maven.apache.org/install.html) v souladu s Apache.  Maven je systém sestavení projektu pro projekty v jazyce Java.
* Klient SSH, jako je například výstup. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Vysvětlení kódu

Ukázková aplikace je umístěna [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) v `Producer-Consumer` podadresáři v adresáři. Pokud používáte cluster Kafka s povoleným **balíček zabezpečení podniku (ESP)** , měli byste použít verzi aplikace, která se nachází v `DomainJoined-Producer-Consumer` podadresáři.

Aplikace se skládá primárně ze čtyř souborů:
* `pom.xml`: Tento soubor definuje závislosti projektu, verzi Javy a metody balení.
* `Producer.java`: Tento soubor pomocí rozhraní Producer API odesílá do systému Kafka náhodné věty.
* `Consumer.java`: Tento soubor pomocí rozhraní Consumer API čte data ze systému Kafka a posílá je do výstupu STDOUT.
* `AdminClientWrapper.java`: Tento soubor používá rozhraní API pro správu k vytváření, popisu a odstraňování Kafkach témat.
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

    Položka `${kafka.version}` se deklaruje v části `<properties>..</properties>` souboru `pom.xml` a je nakonfigurovaná na verzi systému Kafka v clusteru HDInsight.

* Moduly plug-in: Moduly plug-in Mavenu poskytují různé funkce. V tomto projektu se používají následující moduly plug-in:

    * `maven-compiler-plugin`: Slouží k nastavení verze Javy, kterou projekt používá, na 8. To je verze Javy, kterou používá HDInsight 3.6.
    * `maven-shade-plugin`: Slouží k vygenerování souboru JAR, který obsahuje tuto aplikaci i všechny závislosti. Používá se také k nastavení vstupního bodu aplikace, abyste mohli přímo spustit soubor JAR bez nutnosti zadávat hlavní třídu.

### <a name="producerjava"></a>Producer.java

Producent komunikuje s hostiteli zprostředkovatelů Kafka (pracovní uzly) a odesílá data do tématu Kafka. Následující fragment kódu pochází ze souboru [producent. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) z [úložiště GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) a ukazuje, jak nastavit vlastnosti producenta. U clusterů s podporou podnikového zabezpečení je nutné přidat další vlastnost "Properties. setProperty (CommonClientConfigs.SECURITY_PROTOCOL_CONFIG," SASL_PLAINTEXT ");"

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

Konzument komunikuje s hostiteli zprostředkovatelů Kafka (pracovní uzly) a ve smyčce čte záznamy. Následující fragment kódu ze souboru [Consumer. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) nastaví vlastnosti příjemce. U clusterů s podporou podnikového zabezpečení je nutné přidat další vlastnost "Properties. setProperty (CommonClientConfigs.SECURITY_PROTOCOL_CONFIG," SASL_PLAINTEXT ");"

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

Soubor [Run. Java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) poskytuje rozhraní příkazového řádku, které spouští buď producenta, nebo kód příjemce. Jako parametr je potřeba zadat informace o hostiteli zprostředkovatele Kafka. Volitelně můžete zahrnout hodnotu ID skupiny, kterou používá proces příjemce. Pokud vytvoříte více instancí příjemce pomocí stejného ID skupiny, vyčtou se z tématu čtení z vyrovnávání zatížení.

## <a name="build-and-deploy-the-example"></a>Sestavení a nasazení příkladu

### <a name="use-pre-built-jar-files"></a>Použití předem připravených souborů JAR

Stáhněte si jar z [ukázky Kafka Začínáme Azure](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars). Pokud je váš cluster **balíček zabezpečení podniku (ESP)** povolený, použijte Kafka-Producer-Consumer-ESP. jar. K zkopírování jar do clusteru použijte následující příkaz.

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>Sestavení souborů JAR z kódu

Chcete-li tento krok přeskočit, lze předem sestavené jar stáhnout z `Prebuilt-Jars` podadresáře. Stáhněte si Kafka-Producer-Consumer. jar. Pokud je váš cluster **balíček zabezpečení podniku (ESP)** povolený, použijte Kafka-Producer-Consumer-ESP. jar. Spusťte krok 3 ke zkopírování jar do clusteru HDInsight.

1. Stáhněte a extrahujte příklady z [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) .

2. Nastavte aktuální adresář na umístění `hdinsight-kafka-java-get-started\Producer-Consumer` adresáře. Pokud používáte cluster Kafka s povoleným **balíček zabezpečení podniku (ESP)** , měli byste nastavit umístění na `DomainJoined-Producer-Consumer` podadresář. Pomocí následujícího příkazu sestavte aplikaci:

    ```cmd
    mvn clean package
    ```

    Tento příkaz vytvoří adresář s názvem `target`, který bude obsahovat soubor s názvem `kafka-producer-consumer-1.0-SNAPSHOT.jar`. Pro clustery ESP bude soubor `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar`

3. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Zadejte následující příkaz, který zkopíruje `kafka-producer-consumer-1.0-SNAPSHOT.jar` soubor do clusteru HDInsight. Po zobrazení výzvy zadejte heslo uživatele SSH.

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a> Spustit příklad

1. Místo `sshuser` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Zadáním následujícího příkazu otevřete připojení SSH ke clusteru. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pokud chcete získat hostitele zprostředkovatele Kafka, nahraďte hodnoty pro `<clustername>` a `<password>` v následujícím příkazu a spusťte ho. Použijte stejnou velikost písmen pro `<clustername>` , jak je znázorněno v Azure Portal. Nahraďte `<password>` heslem přihlášení clusteru a pak spusťte:

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Tento příkaz vyžaduje přístup Ambari. Pokud je váš cluster za NSG, spusťte tento příkaz z počítače, který má přístup k Ambari.

1. Vytvořte Kafka téma, `myTest` a to tak, že zadáte následující příkaz:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. Pokud chcete spustit producenta a zapsat data do tématu, použijte následující příkaz:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. Jakmile bude producent hotový, pomocí následujícího příkazu zahajte čtení z tématu:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    Zobrazí se počet načtených záznamů spolu s celkovým počtem.

1. Konzumenta ukončíte stisknutím __Ctrl+C__.

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

Tento příkaz pomocí `tmux` rozdělí terminál do dvou sloupců. V obou sloupcích je spuštěný konzument se stejnou hodnotou ID skupiny. Jakmile konzumenti dokončí čtení, všimněte si, že oba přečetli pouze část záznamů. Pro ukončení použijte dvakrát __kombinaci kláves CTRL + C__ `tmux` .

Konzumace klienty ze stejné skupiny se realizuje rozdělením tématu na oddíly. V tomto vzorovém kódu má dříve vytvořené téma `test` osm oddílů. Pokud spustíte osm konzumentů, každý z nich bude číst záznamy z jednoho oddílu tématu.

> [!IMPORTANT]  
> Ve skupině příjemců nemůže být víc instancí konzumentů než má téma oddílů. V tomto příkladu může skupina konzumentů obsahovat až osm konzumentů, protože to je počet oddílů tématu. Nebo můžete mít více skupin konzumentů, každou s maximálně osmi konzumenty.

Záznamy uložené v Kafka jsou uloženy v pořadí, v jakém jsou přijímány v rámci oddílu. Pro dosažení doručování záznamů ve správném pořadí *v rámci oddílu* vytvořte skupinu příjemců, ve které bude počet instancí konzumentů odpovídat počtu oddílů. Pro dosažení doručování záznamů ve správném pořadí *v rámci tématu* vytvořte skupinu obsahující pouze jednu instanci konzumenta.

## <a name="common-issues-faced"></a>Běžné problémy

1. Nepovedlo se **vytvořit téma** Pokud je váš cluster povolený v sadě Enterprise Security Pack, používejte [předem připravené soubory JAR pro producenta a příjemce](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar). Sklenice ESP může být sestavena z kódu v [ `DomainJoined-Producer-Consumer` podadresáři](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer). Vlastnosti producent a příjemce mají dodatečnou vlastnost `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG` pro clustery s podporou protokolu ESP.

2. **Selhání v clusterech s povoleným** protokolem ESP: Pokud dojde k selhání operací a používání clusteru s povoleným protokolem ESP, ověřte, jestli `kafka` se uživatel nachází ve všech zásadách Ranger. Pokud není k dispozici, přidejte ji do všech zásad Ranger.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat Apache Kafkaho výrobce a zákaznického rozhraní API s Kafka ve službě HDInsight. Další informace o práci s platformou Kafka najdete v těchto zdrojích:

* [Použití proxy serveru REST Kafka](rest-proxy.md)
* [Analýza protokolů Apache Kafka](apache-kafka-log-analytics-operations-management.md)
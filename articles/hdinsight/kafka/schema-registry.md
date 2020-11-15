---
title: Apache Kafka s registry schématu s ochranou v Azure HDInsight
description: Nasaďte cluster Kafka spravovaného HDInsight s hraničním uzlem v rámci Virtual Network a pak na hraničním uzlu nainstalujte registr s výstrahou schématu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 7e17cdca508db81551d988c795bd1235fa729e82
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636856"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka s registry schématu s ochranou v Azure HDInsight

Registr schématu Kafka poskytuje serializátory, které se připojují k Apache Kafka klientům, kteří zpracovávají úložiště a načítání schématu zpráv pro zprávy Kafka, které se odesílají ve formátu Avro. Používá se k tomu, že se jedná o projekt OSS, který je teď v rámci licence s možností [influent Community](https://www.confluent.io/blog/license-changes-confluent-platform/). Registr schématu dále slouží pro následující účely:

* Ukládejte a obnovujte schémata pro výrobce a příjemce.
* Vydejte zpětnou nebo dopřednou kompatibilitu/Full na tématech.
* Zmenšete velikost datové části odeslané na Kafka.

V clusteru Kafka spravovaném ve službě HDInsight je registr schématu obvykle nasazený na hraničním uzlu, aby bylo možné oddělení výpočetní služby od hlavních uzlů.

Níže je reprezentativní architektura způsobu nasazení registru schématu v clusteru HDInsight. Registr schémat nativně zveřejňuje REST API pro operace.  Výrobci a příjemci můžou pracovat s registrem schématu v rámci virtuální sítě nebo pomocí [proxy Kafka REST](rest-proxy.md).

![Registr schématu HDInsight Kafka](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Nasazení Kafka spravovaného službou HDInsight s registrací schématu s výstrahou

V této části nasadíme cluster spravovaného Kafka HDInsight s hraničním uzlem ve virtuální síti a pak na hraničním uzlu nainstalujete registr s výstrahou schématu.  

1. Kliknutím na tlačítko **nasadit do Azure** níže se přihlaste k Azure a otevřete šablonu správce prostředků.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json" target="_blank">:::image type="icon" source="media/schema-registry/hdi-deploy-to-azure1.png":::</a>

1. V šabloně vlastního nasazení vyplňte pole, jak je popsáno níže:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**.|
    |Oblast|V rozevíracím seznamu vyberte oblast, ve které se cluster vytvoří.|
    |Název clusteru|Zadejte globálně jedinečný název. Pokud chcete použít výchozí název, ponechte to jako.|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí nastavení je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo.|
    |Uživatelské jméno SSH|Zadejte uživatelské jméno, výchozí hodnota je **sshuser**.|
    |Heslo SSH|Zadejte heslo.|

    Ostatní pole ponechte beze změny. Pokračujte výběrem **Zobrazit + vytvořit** .

1. Zkontrolujte podrobnosti nasazení a pak vyberte **vytvořit** a inicializujte nasazení. Dokončení nasazení může trvat 45 minut.

## <a name="configure-the-confluent-schema-registry"></a>Konfigurace registru schématu s nefluentí

V této části nakonfigurujeme registr Kafka schématu, který jsme nainstalovali na hraničním uzlu. Registr schématu s příbránou je umístěný na adrese  `/etc/schema-registry/schema-registry.properties` a mechanismy pro spouštění a zastavování spustitelných souborů služby jsou umístěné ve  `/usr/bin/` složce.

Registr schématu musí být známý, aby služba Zookeeper mohla spolupracovat s clusterem HDInsight Kafka. Pokud chcete získat podrobné informace o Zookeeper kvora, postupujte podle následujících kroků.

1. Pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte k hraničnímu uzlu clusteru. Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nastavte proměnnou hesla. Heslo pro přihlášení ke clusteru nahraďte heslem a pak zadejte příkaz:

    ```bash
    export password='PASSWORD'
    ```

1. Extrahujte správně název clusteru použita. Spusťte následující příkaz:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Rozbalte hostitele Kafka Zookeeper. Spusťte následující příkaz:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Tuto hodnotu si poznamenejte, protože se později použije.

1. Extrahujte hostitele zprostředkovatele Kafka. Spusťte následující příkaz:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. V režimu úprav otevřete soubory vlastností registru schématu. Spusťte následující příkaz:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Aktualizujte hodnotu pro `kafkastore.connection.url` řetězec Zookeeper, který jste identifikovali dříve.
    1. Aktualizujte hodnotu pro `debug` na `true` .

    Soubor s vlastnostmi teď vypadá nějak takto:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. Pokud chcete soubor uložit, použijte **CTRL + X** , **Y** a pak **Zadejte**.

1. Spusťte registr schématu a najeďte ho na použití aktualizovaného souboru vlastností registru schématu. Spusťte následující příkazy:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. Pomocí registru schématu spuštěného v jedné relaci SSH spusťte jiné okno SSH.  Zaregistrujte novou verzi schématu v rámci předmětu "Kafka-Key" a poznamenejte si výstup:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Zaregistrujte novou verzi schématu v předmětu "Kafka-value" a poznamenejte si výstup:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Vypíše všechny předměty a zkontroluje výstup:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

Možná budete chtít vyzkoušet další [uvedené rozšířené příkazy](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart).

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Posílání a využívání Avro dat z Kafka pomocí registru schématu

V této části načteme data ze standardního vstupu a zapíšeme je do Kafka tématu ve formátu. Pak načteme data z témat Kafka pomocí příjemce s formátovacím modulem Avro, který tato data transformuje do čitelného formátu.

1. Vytvoření tématu Kafka `agkafkaschemareg` .

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. K vytvoření schématu použijte **výrobce konzoly Kafka Avro** , přiřaďte schéma k tématu a začněte odesílat data do tématu ve formátu Avro. Ujistěte se, že téma Kafka v předchozím kroku bylo úspěšně vytvořeno a že **$KAFKABROKERS** má v něm hodnotu.

    Schéma, které posíláme, je pár klíč-hodnota

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Pomocí níže uvedeného příkazu spusťte **výrobce konzoly Kafka Avro** :

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. Když je producent připravený přijímat zprávy, začněte odesílat zprávy ve formátu předdefinovaného schématu Avro. Pomocí klávesy TAB Vytvořte mezery mezi klíčem a hodnotou.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Zkuste do výrobce konzoly zadat náhodná data mimo schéma, abyste viděli, jak producent teď povoluje jakákoli data, která neodpovídají předdefinovanému schématu Avro.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. Na jiné obrazovce spusťte uživatele konzoly Kafka Avro.

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    Měli byste začít zobrazit následující výstup:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>Další kroky

* [Použití rozhraní API pro Apache Kafka výrobce a příjemce](apache-kafka-producer-consumer-api.md)
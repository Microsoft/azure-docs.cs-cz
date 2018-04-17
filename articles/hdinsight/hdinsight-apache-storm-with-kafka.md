---
title: Pomocí Apache Kafka Storm v HDInsight - Azure | Microsoft Docs
description: Apache Kafka se instaluje s Apache Storm v HDInsight. Naučte se zapsat do Kafka a pak čtení z něj, pomocí KafkaBolt a KafkaSpout součásti, které jsou součástí Storm. Také další informace o použití rozhraní tok definovat a odeslání topologie Storm.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: larryfr
ms.openlocfilehash: be62705ce0217235b75ec5ad220ad6f32dfd3c10
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>Pomocí Apache Kafka Storm v HDInsight

Další informace o použití Apache Storm číst a zapisovat do Apache Kafka. Tento příklad také ukazuje, jak k uložení dat od topologie Storm do systému souborů HDFS kompatibilního používané HDInsight.

> [!NOTE]
> Kroky v tomto dokumentu vytvořte skupinu prostředků Azure, která obsahuje oba Storm v HDInsight a Kafka na clusteru HDInsight. Tyto clustery jsou obě nachází v rámci virtuální síť Azure, což umožňuje clusteru Storm přímo komunikovat s Kafka clusteru.
> 
> Po dokončení kroků v tomto dokumentu, nezapomeňte odstranit clustery nadbytečné náklady.

## <a name="get-the-code"></a>Získání kódu

Kód pro tento příklad v tomto dokumentu je k dispozici na [ https://github.com/Azure-Samples/hdinsight-storm-java-kafka ](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Kompilace projektu, potřebujete následující konfigurace pro vývojové prostředí:

* [Java JDK 1.8](http://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) nebo vyšší. HDInsight 3.5 nebo vyšší vyžadují Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Klientem SSH (je třeba `ssh` a `scp` příkazy) – informace najdete v tématu [použití SSH s HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Textového editoru nebo IDE.

Následující proměnné prostředí může být nastaven při instalaci Java a sadu JDK na pracovní stanici. Nicméně byste měli zkontrolovat, že existují a že obsahují správné hodnoty pro váš systém.

* `JAVA_HOME` -by měla odkazovat na adresář, kam nainstalovat sadu JDK.
* `PATH` -musí obsahovat následující cesty:
  
    * `JAVA_HOME` (nebo ekvivalentní cesta).
    * `JAVA_HOME\bin` (nebo ekvivalentní cesta).
    * Adresář, kde je nainstalován Maven.

## <a name="create-the-clusters"></a>Vytváření clusterů

Apache Kafka v HDInsight neposkytuje přístup k zprostředkovatelé Kafka prostřednictvím veřejného Internetu. Všechno, co komunikuje se Kafka musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu jsou Kafka i Storm clusterů umístěné v virtuální sítě Azure. Následující diagram znázorňuje tok komunikace mezi clustery:

![Diagram clustery Storm a Kafka v virtuální sítě Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Jiné služby v clusteru, například SSH a Ambari je přístupná prostřednictvím Internetu. Další informace o veřejné porty, které jsou k dispozici s HDInsight naleznete v tématu [porty a identifikátory URI používají v prostředí HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Když vytvoříte virtuální síť Azure, Kafka, a clusterů Storm se ručně, je jednodušší použít šablonu Azure Resource Manager. Použijte následující kroky k nasazení virtuální sítě Azure, Kafka a Storm clusterů do vašeho předplatného Azure.

1. Na následující tlačítko použijte pro přihlášení do Azure a otevřete šablonu na portálu Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Šablona Azure Resource Manager je umístěna ve **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**. Vytvoří v následujících zdrojích informací:
    
    * Skupina prostředků Azure
    * Azure Virtual Network
    * Účet služby Azure Storage
    * Kafka v HDInsight verze 3.6 (tři uzly pracovního procesu)
    * Storm v HDInsight verze 3.6 (tři uzly pracovního procesu)

  > [!WARNING]
  > Pokud chcete zajistit dostupnost Kafka v HDInsightu, musí cluster obsahovat aspoň tři pracovní uzly. Tato šablona vytvoří cluster Kafka, který obsahuje tři uzly pracovního procesu.

2. Použijte následující pokyny k naplnění položek na **vlastní nasazení** části:
   
    ![HDInsight vlastní nasazení](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Skupina prostředků**: vytvoření skupiny nebo vyberte nějaký existující. Tato skupina obsahuje clusteru HDInsight.
   
    * **Umístění**: Vyberte umístění geograficky blízko vás.

    * **Základní název clusteru**: Tato hodnota se používá jako základní název pro Storm a Kafka clusterů. Například zadáním **hdi** vytvoří cluster Storm s názvem **storm hdi** a Kafka clusteru s názvem **kafka hdi**.
   
    * **Uživatelské jméno přihlášení clusteru**: uživatelské jméno správce pro clustery Storm a Kafka.
   
    * **Heslo pro přihlášení clusteru**: uživatelské heslo správce pro clustery Storm a Kafka.
    
    * **Uživatelské jméno SSH**: SSH, aby uživatel vytvořil pro clustery Storm a Kafka.
    
    * **Heslo SSH**: heslo pro uživatele SSH pro clustery Storm a Kafka.

3. Pro čtení **podmínky a ujednání**a potom vyberte **souhlasím s podmínkami a ujednáními výše uvedených**.

4. Nakonec zkontrolujte **připnout na řídicí panel** a pak vyberte **nákupu**. Chcete-li vytvořit clustery trvá asi 20 minut.

Po vytvoření prostředky se zobrazí v části pro skupinu prostředků.

![Část skupiny prostředků pro virtuální síť a clustery](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Všimněte si, že jsou názvy clusterů HDInsight **storm BASENAME** a **kafka BASENAME**, kde BASENAME je jméno, které jste zadali v šabloně. Názvy těchto používat v dalších krocích při připojování k clustery.

## <a name="understanding-the-code"></a>Pochopení kódu

Tento projekt obsahuje dvě topologie:

* **KafkaWriter**: určené **writer.yaml** souborů, tato topologie zapíše náhodných věty Kafka pomocí KafkaBolt s Apache Storm.

    Tato topologie používá vlastní **SentenceSpout** součásti pro generování náhodných věty.

* **KafkaReader**: určené **reader.yaml** soubor, tato topologie čte data z Kafka pomocí KafkaSpout s Apache Storm a poté protokoluje data do stdout.

    Tato topologie Storm HdfsBolt používá k zápisu dat do výchozího úložiště pro Storm cluster.
### <a name="flux"></a>Tok

Uvedené topologie jsou definovány pomocí [tok](https://storm.apache.org/releases/1.1.2/flux.html). Tok byla zavedena v Storm 0.10.x a umožňuje oddělit konfiguraci topologie z kódu. Pro topologie, které používají rozhraní tok topologii je definována v souboru YAML. Soubor YAML může být součástí topologii. Také může být samostatný soubor používá při odesílání topologie. Tok také podporuje nahrazení proměnné za běhu, který se používá v tomto příkladu.

Následující parametry jsou nastavené v době běhu pro tyto topologie:

* `${kafka.topic}`: Název tématu Kafka, které topologie pro čtení a zápis k.

* `${kafka.broker.hosts}`: Hostitelů, které zprostředkovává Kafka na spustit. Zprostředkovatel informace využívají KafkaBolt při zápisu do Kafka.

* `${kafka.zookeeper.hosts}`: Hostitelů, které Zookeeper běží na clusteru Kafka.

Další informace o topologiích tok najdete v tématu [ https://storm.apache.org/releases/1.1.2/flux.html ](https://storm.apache.org/releases/1.1.2/flux.html).

## <a name="download-and-compile-the-project"></a>Stáhněte si a kompilaci projektu

1. Na vašem vývojovém prostředí, stáhněte si projekt z [ https://github.com/Azure-Samples/hdinsight-storm-java-kafka ](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), otevřete příkazového řádku a změňte adresáře na umístění, který jste si stáhli projektu.

2. Z **hdinsight storm java kafka** adresáře, použijte následující příkaz pro kompilaci projektu a vytvořit balíček pro nasazení:

  ```bash
  mvn clean package
  ```

    Proces balíčku vytvoří soubor s názvem `KafkaTopology-1.0-SNAPSHOT.jar` v `target` adresáře.

3. Použijte následující příkazy ke zkopírování balíčku pro váš cluster Storm v HDInsight. Nahraďte **uživatelské jméno** s uživatelským jménem SSH pro cluster. Nahraďte **BASENAME** s základní název, který jste použili při vytvoření clusteru.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Po zobrazení výzvy zadejte heslo, které jste použili při vytváření clusterů.

## <a name="configure-the-topology"></a>Konfigurace topologie

1. Použijte jednu z následujících metod ke zjišťování hostitelů Kafka zprostředkovatele pro **Kafka** na clusteru HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]
    > Následující příklad Bash předpokládá, že `$CLUSTERNAME` obsahuje název __Kafka__ název clusteru. Dále předpokládá, že [jq](https://stedolan.github.io/jq/) je nainstalována verze 1.5 nebo větší. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    Hodnota vrácená je podobná následující text:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Ačkoli může existovat více než dva hostitele zprostředkovatele pro váš cluster, není potřeba poskytnout úplný seznam všech hostitelů na klienty. Jeden nebo dva kusy je dost.

2. Použijte jednu z následujících metod zjišťování Zookeeper hostuje pro __Kafka__ na clusteru HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]
    > Následující příklad Bash předpokládá, že `$CLUSTERNAME` obsahuje název __Kafka__ clusteru. Dále předpokládá, že [jq](https://stedolan.github.io/jq/) je nainstalovaná. Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    Hodnota vrácená je podobná následující text:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Existuje více než dva uzly Zookeeper, není potřeba poskytnout úplný seznam všech hostitelů na klienty. Jeden nebo dva kusy je dost.

    Tato hodnota, uložte, protože se později používá.

3. Upravit `dev.properties` souboru v kořenovém adresáři projektu. Přidejte zprostředkovatele a Zookeeper informace hostitele pro __Kafka__ clusteru odpovídající řádky v tomto souboru. V následujícím příkladu je nakonfigurované použití ukázkové hodnoty z předchozích kroků:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Uložit `dev.properties` souboru a potom pomocí následujícího příkazu nahrajte ho do **Storm** clusteru:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Nahraďte **uživatelské jméno** s uživatelským jménem SSH pro cluster. Nahraďte **BASENAME** s základní název, který jste použili při vytvoření clusteru.

## <a name="start-the-writer"></a>Spusťte modul pro zápis

> [!IMPORTANT]
> Postup v této části se předpokládá, k vytvoření clusterů Storm a Kafka použít odkaz šablony Azure Resource Manager v tomto dokumentu. Tato šablona umožňuje automatické vytvoření témat pro Kafka cluster.
>
> Ve výchozím nastavení, Kafka v HDInsight nepovoluje automatické vytváření témat, takže pokud jste použili jinou metodu pro vytvoření clusteru Kafka, musíte ručně vytvořit téma. Informace o ruční vytváření tématu najdete v tématu [začínat Kafka v HDInsight](./kafka/apache-kafka-get-started.md) dokumentu.

1. Následující informace vám pomůžou připojit k **Storm** clusteru pomocí protokolu SSH. Nahraďte **uživatelské jméno** s uživatelským jménem SSH použít při vytváření clusteru. Nahraďte **BASENAME** s základní název použít při vytváření clusteru.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    Po zobrazení výzvy zadejte heslo, které jste použili při vytváření clusterů.
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Připojení SSH do clusteru Storm můžete spustit topologie zapisovače následující příkaz:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Parametry tohoto příkazu jsou:

    * `org.apache.storm.flux.Flux`: Pomocí tok pro konfiguraci a spuštění této topologii.

    * `--remote`: Odešlete topologii Nimbus. Topologie se distribuuje do uzlů pracovního procesu v clusteru.

    * `-R /writer.yaml`: Pomocí `writer.yaml` souboru ke konfiguraci topologie. `-R` Označuje, že tento prostředek je součástí na soubor jar. Proto je v kořenovém jar, `/writer.yaml` je cesta k němu.

    * `--filter`: Naplnění položek v `writer.yaml` topologie pomocí hodnot v `dev.properties` souboru. Například hodnota `kafka.topic` položku v souboru se používá k nahrazení `${kafka.topic}` položku v definici topologie.

5. Po zahájení topologii, použijte následující příkaz k ověření, že je zápis dat do tématu Kafka:

    > [!IMPORTANT]
    > Nahraďte `$KAFKAZKHOSTS` s Zookeeper hostitele informace __Kafka__ clusteru.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    Tento příkaz skriptu součástí Kafka používá k monitorování tématu. Po chvíli by se měl spustit vrací náhodné věty, které byly zapsány do tématu. Výstup se podobá následujícímu příkladu:

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Zastavit tento skript pomocí kombinace kláves Ctrl + c.

## <a name="start-the-reader"></a>Spustit program pro čtení

> [!NOTE]
> Při zobrazení čtečky v uživatelském rozhraní Storm, mohou se zobrazit __funkcích spouts topologie funkce lag chyba__ části. V tomto příkladu tuto chybu můžete ignorovat.

1. Z relace SSH do clusteru Storm použijte následující příkaz spusťte čtečky topologie:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Jakmile se spustí topologii, otevřete uživatelské rozhraní Storm. Tento web uživatelského rozhraní se nachází v `https://storm-BASENAME.azurehdinsight.net/stormui`. Nahraďte __BASENAME__ s základní název použita při vytvoření clusteru. 

    Pokud budete vyzváni, použijte přihlašovací jméno správce (výchozím `admin`) a heslo používané při vytvoření clusteru. Zobrazí na webové stránce, podobně jako na následujícím obrázku:

    ![Storm uživatelského rozhraní](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Uživatelské rozhraní Storm, vyberte __kafka čtečky__ na odkaz v __souhrn topologie__ části zobrazíte informace o __kafka čtečky__ topologie.

    ![Souhrn část topologie Storm webového uživatelského rozhraní](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Chcete-li zobrazit informace o instancích protokolovacího nástroje bolt součásti, vyberte __protokolovacího nástroje bolt__ na odkaz v __funkce Bolts (všechny čas)__ části.

    ![Protokolovač bolt odkaz v části funkce bolts](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. V __vykonavatelů__ vyberte pomocí odkazu v __Port__ sloupec pro zobrazení protokolování informací o tuto instanci součásti.

    ![Vykonavatelů odkaz](./media/hdinsight-apache-storm-with-kafka/executors.png)

    V protokolu obsahuje data načtená z tématu Kafka protokolu. Informace v protokolu je podobná následující text:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Zastavit uvedené topologie

Z relace SSH do clusteru Storm použijte následující příkazy k zastavení topologie Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vzhledem k tomu, že kroky v tomto dokumentu vytvořit oba clustery ve stejné skupině prostředků Azure, můžete odstranit skupinu prostředků na portálu Azure. Odstraňuje se skupina prostředků odebere všechny prostředky, které jsou vytvořené pomocí následujících tohoto dokumentu.

## <a name="next-steps"></a>Další postup

Další příklad topologie, které lze použít s Storm v HDInsight, naleznete v části [topologie Storm příklad a součásti](storm/apache-storm-example-topology.md).

Informace o nasazení a monitorování topologií v HDInsight se systémem Linux najdete v tématu [nasazení a správa topologií Apache Storm v HDInsight se systémem Linux](storm/apache-storm-deploy-monitor-topology-linux.md)

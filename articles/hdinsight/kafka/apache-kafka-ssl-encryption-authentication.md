---
title: Apache Kafka ověřování & šifrování TLS – Azure HDInsight
description: Nastavte šifrování TLS pro komunikaci mezi klienty Kafka a Kafka brokery i mezi zprostředkovateli Kafka. Nastavte ověřování SSL klientů.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: d061832022b983e4d5fd55e72c1d4789b82f6633
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863220"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Nastavení šifrování a ověřování TLS pro Apache Kafka ve službě Azure HDInsight

V tomto článku se dozvíte, jak nastavit šifrování TLS (Transport Layer Security), dříve označované jako šifrování SSL (Secure Sockets Layer) (SSL) mezi klienty Apache Kafka a Apache Kafkami zprostředkovateli. Také se dozvíte, jak nastavit ověřování klientů (někdy označovaného jako obousměrný protokol TLS).

> [!Important]
> Existují dva klienty, které lze použít pro aplikace Kafka: klienta Java a klienta konzoly nástroje. Protokol `ProducerConsumer.java` TLS může používat jenom klient Java pro vytváření i využívání. Klient výrobce konzoly `console-producer.sh` nepracuje s protokolem TLS.

> [!Note]
> Výrobce konzoly HDInsight Kafka s verzí 1,1 nepodporuje protokol SSL.

## <a name="apache-kafka-broker-setup"></a>Nastavení služby Apache Kafka Broker

Instalace zprostředkovatele Kafka TLS bude používat čtyři virtuální počítače clusteru HDInsight následujícím způsobem:

* hlavnímu uzlu 0 – certifikační autorita (CA)
* pracovní uzel 0, 1 a 2 – zprostředkovatelé

> [!Note] 
> Tato příručka bude používat certifikáty podepsané svým držitelem, ale nejbezpečnější řešení používá certifikáty vydané důvěryhodnými certifikačními autoritami.

Souhrn procesu instalace zprostředkovatele je následující:

1. Následující kroky se opakují na všech třech pracovních uzlech:

    1. Vygenerujte certifikát.
    1. Vytvořte žádost o podepsání certifikátu.
    1. Odešlete žádost o podepsání certifikátu certifikační autoritě (CA).
    1. Přihlaste se k certifikační autoritě a podepište žádost.
    1. Odhlásí podepsaný certifikát zpátky do pracovního uzlu.
    1. BOD připojení služby (SCP) veřejný certifikát certifikační autority do pracovního uzlu.

1. Až budete mít všechny certifikáty, uveďte certifikáty do úložiště certifikátů.
1. Přejděte na Ambari a změňte konfiguraci.

Pomocí následujících podrobných pokynů dokončete instalaci zprostředkovatele:

> [!Important]
> V následujících fragmentech kódu wnX je zkratka pro jeden ze tří pracovních uzlů a měla by být nahrazena `wn0` `wn1` nebo `wn2` podle potřeby. `WorkerNode0_Name` a `HeadNode0_Name` měly by být nahrazeny názvy příslušných počítačů.

1. Proveďte počáteční nastavení hlavního uzlu 0, který bude pro HDInsight plnit roli certifikační autority (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Proveďte stejné počáteční nastavení u každého zprostředkovatele (pracovní uzly 0, 1 a 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Na všech pracovních uzlech proveďte následující kroky pomocí níže uvedeného fragmentu kódu.
    1. Vytvořte úložiště klíčů a naplňte ho pomocí nového privátního certifikátu.
    1. Vytvořte žádost o podepsání certifikátu.
    1. Spojovací bod služby žádost o podepsání certifikátu certifikační autoritě (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Na počítači certifikační autority spusťte následující příkaz, který vytvoří soubory ca-cert a klíč certifikační autority:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Přejděte na počítač certifikační autority a podepište všechny přijaté žádosti o podepsání certifikátu:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Podepsané certifikáty můžete odeslat zpět do pracovních uzlů z certifikační autority (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Odešlete veřejný certifikát certifikační autority do každého pracovního uzlu.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Do každého pracovního uzlu přidejte veřejný certifikát CAs do úložiště truststore a klíčů. Pak přidejte vlastní podepsaný certifikát pracovního uzlu do úložiště klíčů.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Aktualizace konfigurace Kafka pro použití protokolu TLS a restartu zprostředkovatelů

Nyní jste nastavili jednotlivé zprostředkovatele Kafka s úložištěm klíčů a truststore a importovali jste správné certifikáty. Dále upravte související vlastnosti konfigurace Kafka pomocí Ambari a pak restartujte zprostředkovatele Kafka.

Chcete-li dokončit úpravu konfigurace, proveďte následující kroky:

1. Přihlaste se k Azure Portal a vyberte svůj cluster Azure HDInsight Apache Kafka.
1. Kliknutím na **Ambari domů** v části **řídicí panely clusteru** přejdete na uživatelské rozhraní Ambari.
1. V části **zprostředkovatel Kafka** nastavte vlastnost **Listeners** na `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. V části **Advanced Kafka-Broker** nastavte vlastnost **Security. Inter. Broker. Protocol** na `SSL`

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png" alt-text="Úprava vlastností konfigurace SSL Kafka v Ambari" border="true":::

1. V části **Custom Kafka-Broker** nastavte vlastnost **SSL. Client. auth** na `required` . Tento krok se vyžaduje jenom v případě, že nastavujete ověřování a šifrování.

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png" alt-text="Úprava vlastností konfigurace SSL Kafka v Ambari" border="true":::

1. V případě HDI verze 3,6 navštivte uživatelské rozhraní Ambari a přidejte následující konfigurace do části **Upřesnit Kafka-ENV** a vlastnost **šablony Kafka-ENV** .

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Tady je snímek obrazovky, který zobrazuje uživatelské rozhraní konfigurace Ambari s těmito změnami.

    Pro HDI verze 3,6:

    :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png" alt-text="Úprava vlastnosti šablony Kafka-ENV v Ambari" border="true":::

    Pro HDI verze 4,0:

     :::image type="content" source="./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png" alt-text="Úprava vlastnosti šablony Kafka-ENV v Ambari čtyřech" border="true":::

1. Restartujte všechny zprostředkovatele Kafka.

## <a name="client-setup-without-authentication"></a>Instalace klienta (bez ověřování)

Pokud nepotřebujete ověřování, souhrn kroků pro nastavení šifrování TLS:

1. Přihlaste se k certifikační autoritě (aktivnímu hlavnímu uzlu).
1. Zkopírujte certifikát CA do klientského počítače z počítače certifikační autority (wn0).
1. Přihlaste se ke klientskému počítači (HN1) a přejděte do `~/ssl` složky.
1. Importujte certifikát CA do truststore.
1. Importujte certifikát certifikační autority do úložiště klíčů.

Tyto kroky jsou podrobně popsané v následujících fragmentech kódu.

1. Přihlaste se k uzlu certifikační autority.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Zkopírujte certifikát CA-CERT do klientského počítače.

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Přihlaste se ke klientskému počítači (pohotovostní hlavní uzel).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importujte certifikát CA do truststore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importujte certifikát CA do úložiště klíčů.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Vytvořte soubor `client-ssl-auth.properties` na klientském počítači (HN1). Měl by mít následující řádky:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Spusťte klienta správce s možnostmi producent a příjemce a ověřte, zda pracují výrobci i spotřebitelé na portu 9093. Postup potřebný k ověření instalace pomocí výrobce nebo příjemce konzoly najdete níže v níže uvedené části [ověřování](apache-kafka-ssl-encryption-authentication.md#verification) .

## <a name="client-setup-with-authentication"></a>Instalace klienta (s ověřováním)

> [!Note]
> Následující kroky se vyžadují jenom v případě, že nastavujete šifrování TLS **i** ověřování. Pokud nastavujete jenom šifrování, přečtěte si téma [instalace klienta bez ověření](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Následující čtyři kroky shrnují úlohy potřebné k dokončení instalace klienta:

1. Přihlaste se ke klientskému počítači (pohotovostní hlavní uzel).
1. Vytvořte úložiště klíčů Java a získejte podepsaný certifikát pro zprostředkovatele. Pak zkopírujte certifikát do virtuálního počítače, ve kterém je certifikační autorita spuštěná.
1. Pro podepsání klientského certifikátu přepněte na počítač certifikační autority (aktivní hlavní uzel).
1. Přejděte do klientského počítače (pohotovostní hlavní uzel) a přejděte do `~/ssl` složky. Zkopírujte podepsaný certifikát do klientského počítače.

Podrobnosti o jednotlivých krocích jsou uvedeny níže.

1. Přihlaste se ke klientskému počítači (pohotovostní hlavní uzel).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Odeberte všechny existující adresáře SSL.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Vytvořte úložiště klíčů Java a vytvořte žádost o podepsání certifikátu. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Zkopírování žádosti o podepsání certifikátu certifikační autoritě

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Přepněte na počítač certifikační autority (aktivní hlavní uzel) a podepište certifikát klienta.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Zkopíruje podepsaný certifikát klienta z certifikační autority (aktivní hlavní uzel) do klientského počítače.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Zkopírujte certifikát CA-CERT do klientského počítače.

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Přihlaste se ke klientskému počítači (pohotovostní hlavní uzel) a přejděte do adresáře SSL.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Vytvoření úložiště klientů s podepsaným certifikátem a import certifikátu certifikační autority do úložiště klíčů a truststore na klientském počítači (HN1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Vytvoří soubor `client-ssl-auth.properties` na klientském počítači (HN1). Měl by mít následující řádky:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Ověření

Spusťte tyto kroky na klientském počítači.

> [!Note]
> Pokud je nainstalováno rozhraní HDInsight 4,0 a Kafka 2,1, můžete k ověření instalace použít konzolu výrobce/spotřebitelé. Pokud ne, spusťte producenta Kafka na portu 9092 a odešlete zprávy do tématu a pak použijte příjemce Kafka na portu 9093, který používá protokol TLS.

### <a name="kafka-21-or-above"></a>Kafka 2,1 nebo vyšší

1. Vytvořte téma, pokud již neexistuje.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Spusťte výrobce konzole a zadejte cestu ke `client-ssl-auth.properties` konfiguračnímu souboru pro daného výrobce.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Spusťte jiné připojení SSH ke klientskému počítači a spusťte klienta konzoly a zadejte cestu k `client-ssl-auth.properties` konfiguračnímu souboru pro příjemce.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Vytvořte téma, pokud již neexistuje.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Spusťte výrobce konzole a poskytněte cestu k souboru Client-SSL-auth. Properties jako konfigurační soubor pro producenta.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Spusťte jiné připojení SSH ke klientskému počítači a spusťte klienta konzoly a zadejte cestu k `client-ssl-auth.properties` konfiguračnímu souboru pro příjemce.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Další kroky

* [Co je Apache Kafka ve službě HDInsight?](apache-kafka-introduction.md)

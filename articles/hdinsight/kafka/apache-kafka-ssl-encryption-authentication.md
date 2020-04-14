---
title: Šifrování Apache Kafka TLS & ověřování - Azure HDInsight
description: Nastavte šifrování TLS pro komunikaci mezi klienty Kafka a makléři Kafka, stejně jako mezi makléři Kafka. Nastavte ověřování SSL klientů.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 02b64d77a4fb1af25e1022de3ac8e4775f916d9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261767"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Nastavení šifrování a ověřování TLS pro Apache Kafka v Azure HDInsight

Tento článek ukazuje, jak nastavit šifrování TLS (Transport Layer Security), dříve známé jako šifrování SSL (Secure Sockets Layer), mezi klienty Apache Kafka a zprostředkovateli Apache Kafka. Také ukazuje, jak nastavit ověřování klientů (někdy označované jako obousměrný TLS).

> [!Important]
> Existují dva klienti, které můžete použít pro aplikace Kafka: Java klient a konzolový klient. Pouze klient `ProducerConsumer.java` Java může používat TLS pro výrobu i spotřebu. Klient `console-producer.sh` výrobce konzoly nepracuje s tls.

> [!Note]
> Výrobce konzoly HDInsight Kafka s verzí 1.1 nepodporuje SSL.

## <a name="apache-kafka-broker-setup"></a>Apache Kafka broker nastavení

Nastavení zprostředkovatele Kafka TLS bude používat čtyři virtuální počítače clusteru HDInsight následujícím způsobem:

* headnode 0 - Certifikační autorita (CA)
* uzel pracovníka 0, 1 a 2 - zprostředkovatelé

> [!Note] 
> Tato příručka bude používat certifikáty podepsané svým držitelem, ale nejbezpečnějším řešením je použití certifikátů vydaných důvěryhodnými certifikačními autoritami.

Souhrn procesu nastavení brokera je následující:

1. Následující kroky se opakují na každém ze tří uzlů pracovníka:

    1. Vygenerujte certifikát.
    1. Vytvořte žádost o podepsání certifikátu.
    1. Odešlete žádost o podepsání certifikátu certifikační autoritě (CA).
    1. Přihlaste se k certifikační autoritě a přihlaste požadavek.
    1. SCP podepsaný certifikát zpět do pracovního uzlu.
    1. SCP veřejný certifikát certifikační autority k pracovnímu uzlu.

1. Jakmile budete mít všechny certifikáty, vložte certifikáty do obchodu s certifikáty.
1. Přejděte na Ambari a změňte konfigurace.

K dokončení nastavení zprostředkovatele použijte následující podrobné pokyny:

> [!Important]
> V následujícím kódu výstřižky wnX je zkratka pro jeden ze tří pracovních uzlů `wn0` `wn1` a `wn2` by měla být nahrazena , nebo podle potřeby. `WorkerNode0_Name`a `HeadNode0_Name` měly by být nahrazeny názvy příslušných strojů.

1. Proveďte počáteční nastavení na hlavním uzlu 0, který pro HDInsight bude plnit roli certifikační autority (CA).

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

1. Na každém z pracovních uzlů proveďte následující kroky pomocí fragmentu kódu níže.
    1. Vytvořte úložiště klíčů a naplňte ho novým privátním certifikátem.
    1. Vytvořte žádost o podepsání certifikátu.
    1. SCP žádost o podpis certifikátu do certifikační autority (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. V počítači certifikační autority spusťte následující příkaz k vytvoření souborů ca-cert a ca-key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Změňte na počítač certifikační autority a podepište všechny přijaté žádosti o podepsání certifikátu:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Odeslat podepsané certifikáty zpět do pracovních uzlů z certifikační autority (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Odešlete veřejný certifikát certifikační autority každému pracovnímu uzlu.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Na každém pracovním uzlu přidejte veřejný certifikát certifikačních autorit do úložiště důvěryhodnosti a úložiště klíčů. Potom přidejte vlastní podepsaný certifikát pracovního uzlu do úložiště klíčů

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Aktualizace konfigurace Kafka pro použití TLS a restartování zprostředkovatelů

Nyní jste nastavili každý zprostředkovatel Kafka s keystore a truststore a importované správné certifikáty. Dále upravte související vlastnosti konfigurace Kafka pomocí Ambari a restartujte zprostředkovatele Kafka.

Chcete-li dokončit změnu konfigurace, postupujte takto:

1. Přihlaste se k portálu Azure a vyberte cluster Azure HDInsight Apache Kafka.
1. Přejděte na ui Ambari kliknutím na **Domovskou položku Ambari** v **řídicích panelech clusteru**.
1. V části **Kafka Broker** nastavte vlastnost **posluchačů** na`PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. V části **Advanced kafka-broker** nastavte vlastnost **security.inter.broker.protocol** na`SSL`

    ![Úprava vlastností konfigurace Kafka ssl v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. V části **Vlastní kafka-broker** nastavte vlastnost **ssl.client.auth** na `required`. Tento krok je vyžadován pouze v případě, že nastavujete ověřování a šifrování.

    ![Úprava vlastností konfigurace kafka ssl v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Pro HDI verze 3.6 přejděte na uživatelské rozhraní Ambari a přidejte následující konfigurace pod **Advanced kafka-env** a vlastnost **šablony kafka-env.**

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

1. Zde je snímek obrazovky, který ukazuje, Ambari konfigurace UI s těmito změnami.

    Pro HDI verze 3.6:

    ![Úprava vlastnosti šablony kafka-env v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Pro HDI verze 4.0:

     ![Úprava vlastnosti šablony kafka-env ve čtyřech Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. Restartujte všechny makléře Kafka.

## <a name="client-setup-without-authentication"></a>Nastavení klienta (bez ověřování)

Pokud ověřování nepotřebujete, souhrn kroků k nastavení pouze šifrování TLS je následující:

1. Přihlaste se k certifikační autoritě (aktivní hlavní uzel).
1. Zkopírujte certifikát certifikační autority do klientského počítače z počítače certifikační autority (wn0).
1. Přihlaste se ke klientském počítači `~/ssl` (hn1) a přejděte do složky.
1. Importujte certifikát certifikační autority do úložiště důvěryhodných certifikátů.
1. Importujte certifikát certifikační autority do úložiště klíčů.

Tyto kroky jsou podrobně popsány v následujících fragmentech kódu.

1. Přihlaste se k uzlu certifikační autority.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Zkopírování ca-certu do klientského počítače

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Přihlaste se ke klientském počítači (uzel hlavy v pohotovostním režimu).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importujte certifikát certifikační autority do úložiště důvěryhodných certifikátů.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importujte certifikát certifikační autority do úložiště klíčů.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Vytvořte `client-ssl-auth.properties` soubor v klientském počítači (hn1) . Měl by mít následující řádky:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Spusťte admin klienta s výrobci a spotřebiteli možnosti ověřit, že výrobci i spotřebitelé pracují na portu 9093. Postup potřebný k ověření nastavení pomocí výrobce/spotřebitele konzoly naleznete v části [Ověření](apache-kafka-ssl-encryption-authentication.md#verification) níže.

## <a name="client-setup-with-authentication"></a>Nastavení klienta (s ověřováním)

> [!Note]
> Následující kroky jsou vyžadovány pouze v případě, že nastavujete šifrování **a** ověřování TLS. Pokud pouze nastavujete šifrování, přečtěte si informace [o nastavení klienta bez ověřování](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Následující čtyři kroky shrnují úkoly potřebné k dokončení instalace klienta:

1. Přihlaste se ke klientském počítači (uzel hlavy v pohotovostním režimu).
1. Vytvořte java keystore a získejte podepsaný certifikát pro makléře. Pak zkopírujte certifikát do virtuálního provozu, kde je spuštěncertifikační autorita.
1. Přepněte do počítače CERTIFIKAČNÍ autority (aktivní hlavní uzel) a podepište klientský certifikát.
1. Přejděte do klientského počítače (uzel hlavy v `~/ssl` pohotovostním režimu) a přejděte do složky. Zkopírujte podepsaný certifikát do klientského počítače.

Podrobnosti o každém kroku jsou uvedeny níže.

1. Přihlaste se ke klientském počítači (uzel hlavy v pohotovostním režimu).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Odeberte všechny existující adresáře ssl.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Vytvořte úložiště klíčů java a vytvořte žádost o podpis certifikátu. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Kopírování žádosti o podpis certifikátu do certifikačníautority

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Přepněte do počítače CA (aktivní hlavní uzel) a podepište klientský certifikát.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Zkopírujte podepsaný certifikát klienta z certifikační autority (aktivní hlavní uzel) do klientského počítače.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Zkopírování ca-certu do klientského počítače

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Přihlaste se ke klientském počítači (uzel head head) a přejděte do adresáře ssl.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Vytvořte úložiště klientů s podepsaným certifikátem a importujte certifikát ca do úložiště klíčů a úložiště důvěryhodnosti v klientském počítači (hn1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Vytvořte `client-ssl-auth.properties` soubor v klientském počítači (hn1) . Měl by mít následující řádky:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Ověření

Spusťte tyto kroky v klientském počítači.

> [!Note]
> Pokud je nainstalován hdinsight 4.0 a Kafka 2.1, můžete použít výrobce konzole/spotřebitele k ověření nastavení. Pokud ne, spusťte výrobce Kafka na portu 9092 a odesílejte zprávy na téma a potom použijte spotřebitele Kafka na portu 9093, který používá TLS.

### <a name="kafka-21-or-above"></a>Kafka 2.1 nebo vyšší

1. Vytvořte téma, pokud ještě neexistuje.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Spusťte výrobce konzoly `client-ssl-auth.properties` a zadejte cestu ke konfiguračnímu souboru pro producenta.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Otevřete další připojení ssh ke klientským počítačům `client-ssl-auth.properties` a spusťte konzolu příjemce a poskytněte cestu ke konfiguračnímu souboru pro spotřebitele.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Vytvořte téma, pokud ještě neexistuje.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Spusťte výrobce konzoly a zadejte cestu ke vlastnostem client-ssl-auth.properties jako konfiguračnímu souboru pro výrobce.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Otevřete další připojení ssh ke klientským počítačům `client-ssl-auth.properties` a spusťte konzolu příjemce a poskytněte cestu ke konfiguračnímu souboru pro spotřebitele.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Další kroky

* [Co je Apache Kafka ve službě HDInsight?](apache-kafka-introduction.md)

---
title: Nastavení šifrování a ověřování SSL pro Apache Kafka ve službě Azure HDInsight
description: Nastavte šifrování SSL pro komunikaci mezi klienty Kafka a Kafka brokery i mezi zprostředkovateli Kafka. Nastavte ověřování SSL klientů.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 19a817124afb9afcee25b5f2bff73b8a17e16519
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72431271"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Nastavení šifrování SSL (Secure Sockets Layer) (SSL) a ověřování pro Apache Kafka ve službě Azure HDInsight

V tomto článku se dozvíte, jak nastavit šifrování SSL mezi Apache Kafka klienty a Apache Kafka brokery. Také se dozvíte, jak nastavit ověřování klientů (někdy označovaného jako obousměrný protokol SSL).

> [!Important]
> Existují dva klienty, které lze použít pro aplikace Kafka: klienta Java a klienta konzoly nástroje. Pouze klient Java `ProducerConsumer.java` může používat protokol SSL pro vytváření i využívání. Klient výrobce konzoly @no__t – 0 nefunguje s protokolem SSL.

## <a name="apache-kafka-broker-setup"></a>Nastavení služby Apache Kafka Broker

Instalace zprostředkovatele SSL Kafka bude používat čtyři virtuální počítače clusteru HDInsight následujícím způsobem:

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
> V následujících fragmentech kódu wnX je zkratka pro jeden ze tří pracovních uzlů a v případě potřeby by se měla nahradit `wn0`, `wn1` nebo `wn2`. `WorkerNode0_Name` a `HeadNode0_Name` by měly být nahrazeny názvy příslušných počítačů, například `wn0-abcxyz` nebo `hn0-abcxyz`.

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

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Aktualizace konfigurace Kafka pro použití SSL a restartování zprostředkovatelů

Nyní jste nastavili jednotlivé zprostředkovatele Kafka s úložištěm klíčů a truststore a importovali jste správné certifikáty. Dále upravte související vlastnosti konfigurace Kafka pomocí Ambari a pak restartujte zprostředkovatele Kafka.

Chcete-li dokončit úpravu konfigurace, proveďte následující kroky:

1. Přihlaste se k Azure Portal a vyberte svůj cluster Azure HDInsight Apache Kafka.
1. Kliknutím na **Ambari domů** v části **řídicí panely clusteru**přejdete na uživatelské rozhraní Ambari.
1. V části **zprostředkovatel Kafka** nastavte vlastnost **listeners** na `PLAINTEXT://localhost:9092,SSL://localhost:9093`.
1. V části **Advanced Kafka-Broker** nastavte vlastnost **Security. Inter. Broker. Protocol** na `SSL`.

    ![Úprava vlastností konfigurace SSL Kafka v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. V části **Custom Kafka-Broker** nastavte vlastnost **SSL. Client. auth** na `required`. Tento krok se vyžaduje jenom v případě, že nastavujete ověřování a šifrování.

    ![Úprava vlastností konfigurace SSL Kafka v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Spusťte níže uvedené příkazy, které přidají konfigurační vlastnosti do souboru Kafka `server.properties` pro inzerování IP adres místo plně kvalifikovaného názvu domény (FQDN).

    ```bash
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

1. Chcete-li ověřit, zda byly předchozí změny provedeny správně, můžete volitelně zkontrolovat, zda jsou v souboru Kafka `server.properties` k dispozici následující řádky.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. Restartujte všechny zprostředkovatele Kafka.
1. Spusťte klienta správce s možnostmi producent a příjemce a ověřte, zda pracují výrobci i spotřebitelé na portu 9093.

## <a name="client-setup-with-authentication"></a>Instalace klienta (s ověřováním)

> [!Note]
> Následující kroky se vyžadují jenom v případě, že nastavujete šifrování SSL **i** ověřování. Pokud nastavujete jenom šifrování, přejděte prosím na [nastavení klienta bez ověření](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication) .

Instalaci klienta dokončíte provedením následujících kroků:

1. Přihlaste se ke klientskému počítači (HN1).
1. Vytvořte úložiště klíčů Java a získejte podepsaný certifikát pro zprostředkovatele. Pak zkopírujte certifikát do virtuálního počítače, ve kterém je certifikační autorita spuštěná.
1. Přepněte na počítač certifikační autority (hn0) a podepište certifikát klienta.
1. Přejděte na klientský počítač (HN1) a přejděte do složky `~/ssl`. Zkopírujte podepsaný certifikát do klientského počítače.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Nakonec zobrazte soubor `client-ssl-auth.properties` pomocí příkazu `cat client-ssl-auth.properties`. Měl by mít následující řádky:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Instalace klienta (bez ověřování)

Pokud nepotřebujete ověřování, postupujte podle kroků pro nastavení šifrování SSL:

1. Přihlaste se ke klientskému počítači (HN1) a přejděte do složky `~/ssl`.
1. Zkopírujte podepsaný certifikát do klientského počítače z počítače certifikační autority (wn0).
1. Import certifikátu CA do truststore
1. Import certifikátu certifikační autority do úložiště klíčů

Tyto kroky jsou uvedeny v následujícím fragmentu kódu.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Nakonec zobrazte soubor `client-ssl-auth.properties` pomocí příkazu `cat client-ssl-auth.properties`. Měl by mít následující řádky:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Další kroky

* [Co je Apache Kafka ve službě HDInsight?](apache-kafka-introduction.md)

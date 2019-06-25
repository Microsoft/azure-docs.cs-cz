---
title: Nastavení šifrování pomocí protokolu SSL a ověřování pro Apache Kafka v Azure HDInsight
description: Nastavení šifrování SSL pro komunikaci mezi klienty Kafka a zprostředkovatelům systému Kafka a jde o vztah mezi zprostředkovatelům systému Kafka. Nastavení SSL ověřování klientů.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5d567074a0038915cc43a585b34c9c71ccf3eb1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65464998"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Nastavení šifrování vrstvy SSL (Secure Sockets) a ověřování pro Apache Kafka v Azure HDInsight

V tomto článku se dozvíte, jak nastavit šifrování SSL mezi klienty Apache Kafka a zprostředkovatelům systému Apache Kafka. Je také ukazuje, jak nastavit ověřování klientů (někdy označované jako obousměrný SSL).

> [!Important]
> Existují dva klienti, které můžete použít pro aplikace systému Kafka: klientskou sadou Java a konzoly klienta. Pouze klientskou sadou Java `ProducerConsumer.java` SSL můžete využít pro vytváření a využívání. Výrobce klientské konzoly `console-producer.sh` nefunguje s protokolem SSL.

## <a name="apache-kafka-broker-setup"></a>Instalační program zprostředkovatele Apache Kafka

Instalační program zprostředkovatele Kafka SSL použije čtyři virtuální počítače clusteru HDInsight následujícím způsobem:

* hlavního uzlu 0 - certifikační autoritu (CA)
* pracovní uzel 0, 1 a 2 – zprostředkovatelé

> [!Note] 
> Tato příručka používat certifikáty podepsané svým držitelem, ale nejbezpečnější řešení, je použít certifikáty vystavené infrastrukturou důvěryhodných certifikačních autorit.

Přehled procesu instalace zprostředkovatele vypadá takto:

1. Následující kroky jsou opakována na každé tři pracovní uzly:

    1. Vygenerujte certifikát.
    1. Vytvoření žádost o podepsání certifikátu.
    1. Odešlete do certifikační autoritu (CA) žádost o podepsání certifikátu.
    1. Přihlaste se k certifikační Autoritě a podepsat žádost.
    1. Spojovací bod služby certifikát podepsaný držitelem zpět k pracovnímu uzlu.
    1. Spojovací bod služby veřejný certifikát certifikační autority k pracovnímu uzlu.

1. Jakmile budete mít všechny certifikáty umístit certifikáty do úložiště certifikátů.
1. Přejděte na Ambari a změnit konfigurace.

Použijte následující podrobné pokyny k dokončení instalace zprostředkovatele:

> [!Important]
> V následující fragmenty kódu wnX je zkratka pro jednu ze tří pracovních uzlů a by měla být nahrazena `wn0`, `wn1` nebo `wn2` podle potřeby. `WorkerNode0_Name` a `HeadNode0_Name` by měla být nahrazena s názvy odpovídajících počítačů, jako například `wn0-abcxyz` nebo `hn0-abcxyz`.

1. Proveďte počáteční nastavení na hlavního uzlu 0, což pro HDInsight vyplní roli z certifikační autoritu (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Proveďte stejné počáteční nastavení na všech zprostředkovatelů (pracovní uzly 0, 1 a 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Na každém uzlu pracovního procesu proveďte následující kroky pomocí následující fragment kódu.
    1. Vytvořit úložiště klíčů a přidejte do ní nový privátní certifikát.
    1. Vytvoření žádosti o podepsání certifikátu.
    1. Spojovací bod služby žádost o podepsání certifikátu certifikační autoritě (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Změnit na počítači certifikační Autority a podepište všechny přijaté žádosti o podepsání certifikátu:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Odesílat certifikáty podepsané držitelem zpět do pracovních uzlů z certifikační Autority (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Odeslat veřejný certifikát certifikační autority do každého pracovního uzlu.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Na každý pracovní uzel přidejte veřejný certifikát certifikační autority do truststore a úložiště klíčů. Obnovte certifikát podepsaný držitelem pracovního uzlu vlastní ke keystore.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Aktualizovat konfiguraci Kafka na používání protokolu SSL a restartujte zprostředkovatelů

Nyní máte nastavení všichni zprostředkovatelé Kafka s úložišti klíčů a truststore a importovat správné certifikáty. V dalším kroku změnit související vlastnosti konfigurace Kafka pomocí nástroje Ambari a restartujte zprostředkovatelům systému Kafka.

K dokončení změny konfigurace, proveďte následující kroky:

1. Přihlaste se k webu Azure portal a vyberte svůj cluster Azure HDInsight Apache Kafka.
1. Přejděte na uživatelské rozhraní Ambari kliknutím **Ambari domácí** pod **řídicí panely clusteru**.
1. V části **zprostředkovatelem Kafka** nastavit **naslouchacích procesů** vlastnost `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. V části **Advanced zprostředkovatelem kafka** nastavit **security.inter.broker.protocol** vlastnost `SSL`

    ![Úprava vlastností konfigurace ssl Kafka v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. V části **vlastní zprostředkovatele kafka** nastavit **ssl.client.auth** vlastnost `required`. Tento krok je jenom nutné, pokud jsou nastavení ověřování a šifrování.

    ![Úprava vlastností konfigurace ssl kafka v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Přidání vlastnosti konfigurace Kafka `server.properties` souboru inzerovat IP adresy místo plně kvalifikovaný název domény (FQDN).

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

1. Chcete-li ověřit, že předchozí změny byly provedeny správně, můžete volitelně zkontrolujte, že jsou v Kafka následující řádky `server.properties` souboru.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. Restartujte všechny zprostředkovatelům systému Kafka.
1. Spusťte správce klienta s producenta a konzumenta parametry a ověřte, zda jsou na portu 9093 funkční producenty a spotřebiteli.

## <a name="client-setup-with-authentication"></a>Instalace klienta (s ověřováním)

> [!Note]
> Následující kroky se vyžadují jenom v případě, že nastavujete i šifrování SSL **a** ověřování. Pokud jsou pouze nastavení šifrování, pokračujte k [instalací klientů bez ověřování](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Proveďte následující kroky k dokončení instalace klienta:

1. Přihlaste se do klientského počítače (hn1).
1. Vytvoření java keystore musí být a získat certifikát podepsaný držitelem pro zprostředkovatele. Potom zkopírujte certifikát do virtuálního počítače se spuštěným systémem certifikační Autority.
1. Přepnout na počítači certifikační Autority (hn0) k podepsání certifikátu klienta.
1. Přejděte do klientského počítače (hn1) a přejděte `~/ssl` složky. Zkopírujte certifikátu podepsaného držitelem na klientském počítači.

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

A konečně, zobrazení souboru `client-ssl-auth.properties` příkazem `cat client-ssl-auth.properties`. Měl by mít následující řádky:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Instalace klienta (bez ověřování)

Pokud nepotřebujete ověřování, se kroky k nastavení pouze šifrování SSL:

1. Přihlaste se do klientského počítače (hn1) a přejděte `~/ssl` složky
1. Zkopírujte certifikátu podepsaného držitelem do klientského počítače z počítače certifikační Autority (wn0).
1. Importovat certifikát certifikační Autority truststore
1. Importovat certifikát certifikační Autority ke keystore.

Tyto kroky jsou uvedeny v následující fragment kódu.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

A konečně, zobrazení souboru `client-ssl-auth.properties` příkazem `cat client-ssl-auth.properties`. Měl by mít následující řádky:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Další postup

* [Co je Apache Kafka v HDInsight?](apache-kafka-introduction.md)

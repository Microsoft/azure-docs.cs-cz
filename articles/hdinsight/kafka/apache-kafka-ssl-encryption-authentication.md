---
title: Nastavení šifrování pomocí protokolu SSL a ověřování pro Apache Kafka v Azure HDInsight
description: Nastavení šifrování SSL pro komunikaci mezi klienty Kafka a zprostředkovatelům systému Kafka a jde o vztah mezi zprostředkovatelům systému Kafka. Nastavení SSL ověřování klientů.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 665b439fb1ca0b907ea7385369f64d255e8e42e6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355492"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Nastavení šifrování vrstvy SSL (Secure Sockets) a ověřování pro Apache Kafka v Azure HDInsight

Tento článek popisuje, jak nastavit šifrování SSL od klientů Apache Kafka zprostředkovatelům systému Apache Kafka a také mezi zprostředkovatelům systému Apache Kafka. Poskytuje také kroky potřebné k instalaci ověřování klientů (někdy označované jako obousměrný SSL).

## <a name="server-setup"></a>Nastavení serveru

Prvním krokem je nastavení úložiště klíčů a truststore na zprostředkovatelům systému Kafka a importovat certifikační autoritu (CA) a zprostředkovatele certifikáty do těchto úložišť.

> [!Note] 
> Tato příručka používat certifikáty podepsané svým držitelem, ale nejbezpečnější řešení, je použít certifikáty vystavené infrastrukturou důvěryhodných certifikačních autorit.

1. Vytvořte složku s názvem protokolu ssl a hesla serveru exportovat jako proměnné prostředí. Pro zbývající část tohoto průvodce, nahraďte `<server_password>` heslem skutečné správce pro server.

    ```bash
    $export SRVPASS=<server_password>
    $mkdir ssl
    $cd ssl
    ```

2. Dále vytvořte úložiště klíčů java (kafka.server.keystore.jks) a certifikát certifikační Autority.

    ```bash
    $keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12
    ```

3. Vytvořte žádost o podpisový certifikát vytvořený v předchozím kroku podepsaný Certifikační autoritou.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
    ```

4. Nyní odeslat žádost o podepsání k certifikační Autoritě a získat tento certifikát podepsaný. Vzhledem k tomu můžeme používáte certifikát podepsaný svým držitelem, se nám odhlásit certifikát s naší pomocí certifikační Autority `openssl` příkazu.

    ```bash
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS
    ```

5. Vytvoření vztahu důvěryhodnosti úložiště a importovat certifikát certifikační autority.

    ```bash
    $keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

6. Importujte veřejný certifikát certifikační Autority do úložiště klíčů.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

7. Importujte certifikát podepsaný držitelem do úložiště klíčů.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt//output is "Certificate reply was added to keystore"
    ```

Import certifikátu podepsaného držitelem do úložiště klíčů je posledním krokem potřebné ke konfiguraci truststore a úložiště klíčů pro zprostředkovatelem Kafka.

## <a name="update-configuration-to-use-ssl-and-restart-brokers"></a>Aktualizovat konfiguraci na používání protokolu SSL a restartujte zprostředkovatelů

Máme k dispozici instalační program zprostředkovatele úložiště klíčů a truststore každý Kafka a importovat správné certifikáty.  V dalším kroku změnit související vlastnosti konfigurace Kafka pomocí nástroje Ambari a restartujte zprostředkovatelům systému Kafka.

1. Přihlaste se k webu Azure portal a vyberte svůj cluster Azure HDInsight Apache Kafka.
1. Přejděte na uživatelské rozhraní Ambari kliknutím **Ambari domácí** pod **řídicí panely clusteru**.
1. V části **zprostředkovatelem Kafka** nastavit **naslouchacích procesů** vlastnost `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. V části **Advanced zprostředkovatelem kafka** nastavit **security.inter.broker.protocol** vlastnost `SSL`

    ![Úprava vlastností konfigurace ssl kafka v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. V části **vlastní zprostředkovatele kafka** nastavit **ssl.client.auth** vlastnost `required`. Tento krok je jenom nutné, pokud jsou nastavení ověřování i šifrování.

    ![Úprava vlastností konfigurace ssl kafka v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Přidání vlastnosti konfigurace Kafka `server.properties` souboru inzerovat IP adresy místo plně kvalifikovaný název domény (FQDN).

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Chcete-li ověřit, že předchozí změny byly provedeny správně, můžete volitelně zkontrolujte, že jsou v Kafka následující řádky `server.properties` souboru.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. Restartujte všechny zprostředkovatelům systému Kafka.

## <a name="client-setup-with-authentication"></a>Instalace klienta (s ověřováním)

> [!Note]
> Následující kroky se vyžadují jenom v případě, že nastavujete i šifrování SSL **a** ověřování. Pokud jsou pouze nastavení šifrování, pokračujte k [instalací klientů bez ověřování](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

1. Exportujte heslo klienta. Nahraďte `<client_password>` heslem skutečné správce v klientském počítači Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Vytvoření java keystore musí být a získat certifikát podepsaný držitelem pro zprostředkovatele. Potom zkopírujte certifikát do virtuálního počítače se spuštěným systémem certifikační Autority.

    ```bash
    $keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

    $keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

    $scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request
    ```

1. Přepnout na počítači certifikační Autority (wn0) k podepsání certifikátu klienta.

    ```bash
    $cd ssl
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>
    ```

1. Přejděte do klientského počítače (hn1) a přejděte `~/ssl` složky. Zkopírujte certifikátu podepsaného držitelem na klientském počítači.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert

    #Import CA cert to trust store 
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import signed client (cert client-cert-signed1) to keystore
    $keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Zobrazit soubor `client-ssl-auth.properties` příkazem `$cat client-ssl-auth.properties`. Měl by mít následující řádky:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=<client_password>
    ssl.key.password=<client_password>
    ```

## <a name="client-setup-without-authentication"></a>Instalace klienta (bez ověřování)

1. Exportujte heslo klienta. Nahraďte `<client_password>` heslem skutečné správce v klientském počítači Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Přejděte do klientského počítače (hn1) a přejděte `~/ssl` složky. Zkopírujte certifikátu podepsaného držitelem na klientském počítači.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

    #NOW IMPORT CA cert to trust store
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Zobrazit soubor `client-ssl-auth.properties` příkazem `$cat client-ssl-auth.properties`. Měl by mít následující řádky:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ```

## <a name="next-steps"></a>Další postup

* [Co je Apache Kafka v HDInsight?](/../azure/hdinsight/kafka/apache-kafka-introduction)
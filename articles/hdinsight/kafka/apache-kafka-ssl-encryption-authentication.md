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
ms.openlocfilehash: decaf892d40de75638d05dc16e999a673dce21e5
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477025"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Nastavení šifrování vrstvy SSL (Secure Sockets) a ověřování pro Apache Kafka v Azure HDInsight

Tento článek popisuje, jak nastavit šifrování SSL mezi klienty Apache Kafka a zprostředkovatelům systému Apache Kafka. Poskytuje také kroky potřebné k instalaci ověřování klientů (někdy označované jako obousměrný SSL).

## <a name="server-setup"></a>Nastavení serveru

Prvním krokem je vytvoření úložiště klíčů a truststore na všichni zprostředkovatelé Kafka. Po tyto soubory jsou vytvořeny, naimportujte certifikáty certifikační autority (CA) a zprostředkovatele do těchto úložišť.

> [!Note] 
> Tato příručka používat certifikáty podepsané svým držitelem, ale nejbezpečnější řešení, je použít certifikáty vystavené infrastrukturou důvěryhodných certifikačních autorit.

Proveďte postup k dokončení instalace serveru:

1. Vytvořte složku s názvem protokolu ssl a hesla serveru exportovat jako proměnné prostředí. Pro zbývající část tohoto průvodce, nahraďte `<server_password>` heslem skutečné správce pro server.
1. Dále vytvořte úložiště klíčů java (kafka.server.keystore.jks) a certifikát certifikační Autority.
1. Vytvořte žádost o podpisový certifikát vytvořený v předchozím kroku podepsaný Certifikační autoritou.
1. Nyní odeslat žádost o podepsání k certifikační Autoritě a získat tento certifikát podepsaný. Vzhledem k tomu můžeme používáte certifikát podepsaný svým držitelem, se nám odhlásit certifikát s naší pomocí certifikační Autority `openssl` příkazu.
1. Vytvoření vztahu důvěryhodnosti úložiště a importovat certifikát certifikační autority.
1. Importujte veřejný certifikát certifikační Autority do úložiště klíčů.
1. Importujte certifikát podepsaný držitelem do úložiště klíčů.

Příkazy k dokončení těchto kroků jsou uvedeny v následující fragment kódu.

```bash
export SRVPASS=<server_password>
mkdir ssl
cd ssl

# Create a java keystore (kafka.server.keystore.jks) and a CA certificate.

keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12

# Create a signing request to get the certificate created in the previous step signed by the CA.

keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS

# Send the signing request to the CA and get this certificate signed.

openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS

# Create a trust store and import the certificate of the CA.

keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the public CA certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the signed certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# The output should say "Certificate reply was added to keystore"
```

Import certifikátu podepsaného držitelem do úložiště klíčů je posledním krokem potřebné ke konfiguraci truststore a úložiště klíčů pro zprostředkovatelem Kafka.

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Aktualizovat konfiguraci Kafka na používání protokolu SSL a restartujte zprostředkovatelů

Máte teď nastavený každý Kafka zprostředkovatele úložiště klíčů a truststore a importovat správné certifikáty.  V dalším kroku změnit související vlastnosti konfigurace Kafka pomocí nástroje Ambari a restartujte zprostředkovatelům systému Kafka. 

K dokončení změny konfigurace, proveďte následující kroky:

1. Přihlaste se k webu Azure portal a vyberte svůj cluster Azure HDInsight Apache Kafka.
1. Přejděte na uživatelské rozhraní Ambari kliknutím **Ambari domácí** pod **řídicí panely clusteru**.
1. V části **zprostředkovatelem Kafka** nastavit **naslouchacích procesů** vlastnost `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. V části **Advanced zprostředkovatelem kafka** nastavit **security.inter.broker.protocol** vlastnost `SSL`

    ![Úprava vlastností konfigurace ssl Kafka v Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

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

Proveďte následující kroky k dokončení instalace klienta:

1. Přihlaste se do klientského počítače (hn1).
1. Exportujte heslo klienta. Nahraďte `<client_password>` heslem skutečné správce v klientském počítači Kafka.
1. Vytvoření java keystore musí být a získat certifikát podepsaný držitelem pro zprostředkovatele. Potom zkopírujte certifikát do virtuálního počítače se spuštěným systémem certifikační Autority.
1. Přepnout na počítači certifikační Autority (wn0) k podepsání certifikátu klienta.
1. Přejděte do klientského počítače (hn1) a přejděte `~/ssl` složky. Zkopírujte certifikátu podepsaného držitelem na klientském počítači.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the vm where the CA is
scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request

# Switch to the CA machine (wn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

A konečně, zobrazení souboru `client-ssl-auth.properties` příkazem `cat client-ssl-auth.properties`. Měl by mít následující řádky:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>Instalace klienta (bez ověřování)

Pokud nepotřebujete ověřování, jsou postup, jak nastavit pouze šifrování SSL:

1. Přihlaste se do klientského počítače (hn1) a přejděte `~/ssl` složky
1. Exportujte heslo klienta. Nahraďte `<client_password>` heslem skutečné správce v klientském počítači Kafka.
1. Zkopírujte certifikátu podepsaného držitelem do klientského počítače z počítače certifikační Autority (wn0).
1. Importovat certifikát certifikační Autority truststore
1. Importovat certifikát certifikační Autority ke keystore.

Tyto kroky jsou uvedeny v následující fragment kódu.

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

A konečně, zobrazení souboru `client-ssl-auth.properties` příkazem `cat client-ssl-auth.properties`. Měl by mít následující řádky:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>Další postup

* [Co je Apache Kafka v HDInsight?](apache-kafka-introduction.md)
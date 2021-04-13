---
title: Šifrované připojení pomocí TLS/SSL v Azure Database for MySQL-flexibilním serveru
description: Pokyny a informace o tom, jak se připojit pomocí protokolu TLS/SSL v Azure Database for MySQL flexibilním serveru.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 6dbb1b46aef40986fc2d601aee152aed02591ac0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312599"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Připojení k Azure Database for MySQL-flexibilnímu serveru pomocí šifrovaných připojení

> [!IMPORTANT]
> Azure Database for MySQL flexibilní Server je momentálně ve verzi Public Preview.

Azure Database for MySQL flexibilní Server podporuje připojení klientských aplikací k serveru MySQL pomocí SSL (Secure Sockets Layer) (SSL) s šifrováním TLS (Transport Layer Security). TLS je průmyslový standardní protokol, který zajišťuje šifrovaná síťová připojení mezi databázovým serverem a klientskými aplikacemi, což vám umožní dodržovat požadavky na dodržování předpisů.

Azure Database for MySQL flexibilní Server podporuje ve výchozím nastavení šifrované připojení pomocí TLS 1,2) a všechna příchozí připojení s TLS 1,0 a TLS 1,1 budou standardně odepřená. Konfiguraci vynucení šifrovaného připojení nebo konfigurace verze TLS na flexibilním serveru se dá změnit jak je popsáno v tomto článku. 

V této části jsou k dispozici různé konfigurace nastavení SSL a TLS, které můžete mít pro váš flexibilní Server:

| Scenario   | Nastavení parametrů serveru      | Description                                    |
|------------|--------------------------------|------------------------------------------------|
|Zakázat vynucení SSL | require_secure_transport = vypnuto |Pokud vaše starší verze aplikace nepodporuje šifrovaná připojení k serveru MySQL, můžete zakázat vynucení šifrovaných připojení k flexibilnímu serveru nastavením require_secure_transport = OFF.|
|Vyvynuťte SSL pomocí TLS verze < 1,2 | require_secure_transport = ON a tls_version = TLSV1 nebo TLSV 1.1| Pokud vaše starší aplikace podporuje šifrovaná připojení, ale vyžaduje protokol TLS verze < 1,2, můžete povolit šifrovaná připojení, ale nakonfigurovat flexibilní server tak, aby povoloval připojení s verzí TLS (v 1.0 nebo v 1.1), která podporuje vaše aplikace.|
|Vynutil protokol SSL pomocí TLS verze = 1.2 (výchozí konfigurace)|require_secure_transport = ON a tls_version = TLSV 1.2| Toto je doporučená a výchozí konfigurace flexibilního serveru.|
|Vynutil protokol SSL pomocí TLS verze = 1.3 (podporováno s MySQL v 8.0 a novějším)| require_secure_transport = ON a tls_version = TLSV 1.3| To je užitečné a doporučuje se pro vývoj nových aplikací.|

> [!Note]
> Změny šifry SSL na flexibilním serveru se nepodporují. Šifrovací sady FIPS se ve výchozím nastavení vynutily, když je tls_version nastaveno na TLS verze 1,2. U jiných verzí protokolu TLS než verze 1,2 je šifra SSL nastavená na výchozí nastavení, které je součástí instalace komunity MySQL.

V tomto článku se dozvíte, jak:
* Konfigurace flexibilního serveru 
  * Se zakázaným protokolem SSL 
  * Se zabezpečením SSL vynutilo protokolem TLS verze < 1,2
* Připojení k flexibilnímu serveru pomocí příkazového řádku MySQL 
  * Se zakázanými šifrovanými připojeními
  * Se zapnutými šifrovanými připojeními
* Ověření stavu šifrování pro připojení
* Připojení k flexibilnímu serveru pomocí šifrovaných připojení pomocí různých aplikačních architektur

## <a name="disable-ssl-enforcement-on-your-flexible-server"></a>Zakázání vynucení SSL na flexibilním serveru
Pokud klientská aplikace nepodporuje šifrovaná připojení, budete muset Zakázat vynucení šifrovaných připojení na flexibilním serveru. Pokud chcete zakázat vynucení šifrovaných připojení, musíte nastavit require_secure_transport parametr serveru na vypnuto, jak je znázorněno na snímku obrazovky a uložit konfiguraci parametru serveru, aby se projevila. require_secure_transport je **parametr dynamického serveru** , který se projeví okamžitě a nepožaduje, aby se restart serveru projevil.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Snímek obrazovky ukazující, jak zakázat protokol SSL pomocí Azure Database for MySQL flexibilního serveru.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>Připojení pomocí klienta příkazového řádku MySQL se zakázaným protokolem SSL

Následující příklad ukazuje, jak se připojit k serveru pomocí rozhraní příkazového řádku MySQL. Pomocí `--ssl-mode=DISABLED` nastavení připojovacího řetězce zakažte připojení TLS/SSL z klienta MySQL. Nahraďte hodnoty skutečným názvem serveru a heslem. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
Je důležité si uvědomit, že nastavení require_secure_transport Vypnuto znamená, že šifrovaná připojení nebudou na straně serveru podporována. Pokud jste na flexibilním serveru nastavili require_secure_transport na hodnotu Vypnuto, ale pokud se klient připojuje pomocí šifrovaného připojení, bude se dál přijímat. Následující připojení pomocí klienta MySQL k flexibilnímu serveru nakonfigurovanému pomocí require_secure_transport = OFF bude fungovat i tak, jak je uvedeno níže.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

V okně Souhrn require_secure_transport = OFF nastavení se tím vynucuje vynucení šifrovaných připojení na flexibilním serveru a umožňuje nešifrovaná připojení k serveru z klienta kromě šifrovaných připojení.

## <a name="enforce-ssl-with-tls-version--12"></a>Vyvynuťte SSL pomocí TLS verze < 1,2

Pokud vaše aplikace podporuje připojení k serveru MySQL pomocí protokolu SSL, ale podporuje protokol TLS verze < 1,2, budete muset nastavit parametr serveru verzí TLS na flexibilním serveru. Pokud chcete nastavit verze TLS, které má flexibilní Server podporovat, budete muset nastavit tls_version parametr serveru na TLSV1, TLSV 1.1 nebo TLSV1 a TLSV 1.1, jak je znázorněno na snímku obrazovky a uložit konfiguraci parametru serveru, aby se projevila. tls_version je **statický parametr serveru** , který bude vyžadovat restart serveru, aby se tento parametr projevil.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Snímek obrazovky ukazující, jak nastavit verzi TLS pro Azure Database for MySQL flexibilní Server.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Připojení pomocí klienta příkazového řádku MySQL s protokolem TLS/SSL

### <a name="download-the-public-ssl-certificate"></a>Stáhnout veřejný certifikát SSL
Pokud chcete používat šifrovaná připojení ke svým klientským aplikacím, budete si muset stáhnout [veřejný certifikát SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) , který je dostupný také v okně Azure Portal sítě, jak je znázorněno na snímku obrazovky níže.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Snímek obrazovky ukazující, jak stáhnout veřejný certifikát SSL z Azure Portal.":::

Soubor certifikátu uložte do svého upřednostňovaného umístění. Tento kurz například používá `c:\ssl` nebo `\var\www\html\bin` ve vašem místním prostředí nebo klientském prostředí, kde je vaše aplikace hostovaná. To umožní zabezpečeně připojit aplikace k databázi přes SSL.

Pokud jste vytvořili flexibilní Server s *privátním přístupem (Integration VNET)*, budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do virtuální sítě vytvořené pomocí flexibilního serveru.

Pokud jste vytvořili flexibilní Server s *veřejným přístupem (povolenými IP adresami)*, můžete přidat místní IP adresu do seznamu pravidel brány firewall na serveru.

Můžete zvolit [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) nebo [MySQL Workbench](./connect-workbench.md)--> se připojit k serveru z místního prostředí. 

Následující příklad ukazuje, jak se připojit k serveru pomocí rozhraní příkazového řádku MySQL. Použijte `--ssl-mode=REQUIRED` nastavení připojovacího řetězce pro vymáhání ověření certifikátu TLS/SSL. Předat do parametru cestu k místnímu souboru certifikátu `--ssl-ca` . Nahraďte hodnoty skutečným názvem serveru a heslem. 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Potvrďte, že hodnota předaná, aby `--ssl-ca` odpovídala cestě k souboru pro certifikát, který jste uložili.

Pokud se pokusíte připojit k serveru pomocí nešifrovaných připojení, zobrazí se chybová zpráva s oznámením, že připojení pomocí nezabezpečeného přenosu je zakázané podobné tomu:

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>Ověření připojení TLS/SSL

Spuštěním příkazu pro **stav** MySQL ověřte, že jste se připojili k serveru MySQL pomocí protokolu TLS/SSL:

```dos
mysql> status
```
Ověřte, že je připojení zašifrované, a to tak, že zkontroluje výstup, který by měl zobrazovat: * * SSL: šifrování se používá * *. Tato šifrovací sada ukazuje příklad a je založena na klientovi, vidíte jinou šifrovací sadu.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Připojení k flexibilnímu serveru pomocí šifrovaných připojení pomocí různých aplikačních architektur

Připojovací řetězce, které jsou předdefinovány na stránce připojovací řetězce, které jsou k dispozici pro váš server v Azure Portal obsahují požadované parametry pro společné jazyky pro připojení k databázovému serveru pomocí protokolu TLS/SSL. Parametr TLS/SSL se liší v závislosti na konektoru. Například "useSSL = true", "sslmode = Required" nebo "ssl_verify_cert = true" a další variace.

K navázání šifrovaného připojení k flexibilnímu serveru přes protokol TLS/SSL z vaší aplikace použijte následující ukázky kódu:

### <a name="wordpress"></a>WordPress
Stáhněte si [veřejný certifikát SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) a přidejte následující řádky do wp-config. php za řádkem ```// ** MySQL settings - You can get this info from your web host ** //``` .

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (použití CHOP)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (MySQL Connector pro Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB Connector pro Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Další kroky
- [Připojení a dotazování dat v Azure Database for MySQL flexibilním serveru pomocí aplikace MySQL Workbench](./connect-workbench.md)
- [Připojení a dotazování dat v Azure Database for MySQL flexibilním serveru pomocí PHP](./connect-php.md)
- [Vytvářejte a spravujte Azure Database for MySQL flexibilní serverovou virtuální síť pomocí Azure CLI](./how-to-manage-virtual-network-cli.md).
- Další informace o [sítích v Azure Database for MySQL flexibilním serveru](./concepts-networking.md)
- Další informace o [Azure Database for MySQL flexibilních pravidlech brány firewall serveru](./concepts-networking.md#public-access-allowed-ip-addresses)

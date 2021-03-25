---
title: Šifrované připojení pomocí TLS/SSL v Azure Database for MySQL-flexibilním serveru
description: Pokyny a informace o tom, jak se připojit pomocí protokolu TLS/SSL v Azure Database for MySQL flexibilním serveru.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: ce6150cf404f1ca68c93285a2f4a29a6373a55c0
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110020"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-over-tls12ssl"></a>Připojení k Azure Database for MySQL flexibilnímu serveru přes TLS 1.2/SSL

> [!IMPORTANT]
> Azure Database for MySQL flexibilní Server je momentálně ve verzi Public Preview.

Azure Database for MySQL flexibilní Server podporuje připojení klientských aplikací ke službě MySQL pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL). TLS je průmyslový standardní protokol, který zajišťuje šifrovaná síťová připojení mezi databázovým serverem a klientskými aplikacemi, což vám umožní dodržovat požadavky na dodržování předpisů.

Azure Database for MySQL flexibilní Server podporuje pouze šifrovaná připojení pomocí protokolu TLS 1,2) a všechna příchozí připojení s TLS 1,0 a TLS 1,1 budou odepřena. Pro všechny flexibilní servery, které jsou vynucená pro připojení TLS, je povolený a protokol TLS/SSL nemůžete pro připojení k flexibilnímu serveru zakázat.

## <a name="download-the-public-ssl-certificate"></a>Stáhnout veřejný certifikát SSL
Pokud chcete používat s appliations, Stáhněte si prosím [veřejný certifikát SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem).

Soubor certifikátu uložte do svého upřednostňovaného umístění. Tento kurz například používá `c:\ssl` nebo `\var\www\html\bin` ve vašem místním prostředí nebo klientském prostředí, kde je vaše aplikace hostovaná. To umožní zabezpečeně připojit aplikace k databázi přes SSL. 

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Připojení pomocí klienta příkazového řádku MySQL s protokolem TLS/SSL

Pokud jste vytvořili flexibilní Server s *privátním přístupem (Integration VNET)*, budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do virtuální sítě vytvořené pomocí flexibilního serveru.

Pokud jste vytvořili flexibilní Server s *veřejným přístupem (povolenými IP adresami)*, můžete přidat místní IP adresu do seznamu pravidel brány firewall na serveru.

Můžete zvolit [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) nebo [MySQL Workbench](./connect-workbench.md)--> se připojit k serveru z místního prostředí. 

Následující příklad ukazuje, jak se připojit k serveru pomocí rozhraní příkazového řádku MySQL. Použijte `--ssl-mode=REQUIRED` nastavení připojovacího řetězce pro vymáhání ověření certifikátu TLS/SSL. Předat do parametru cestu k místnímu souboru certifikátu `--ssl-ca` . Nahraďte hodnoty skutečným názvem serveru a heslem. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Potvrďte, že hodnota předaná, aby `--ssl-ca` odpovídala cestě k souboru pro certifikát, který jste uložili.

### <a name="verify-the-tlsssl-connection"></a>Ověření připojení TLS/SSL

Spuštěním příkazu pro **stav** MySQL ověřte, že jste se připojili k serveru MySQL pomocí protokolu TLS/SSL:

```dos
mysql> status
```
Ověřte, že je připojení zašifrované, a to tak, že zkontroluje výstup, který by měl zobrazovat:  **SSL: šifrování se používá, AES256-SHA**. Tato šifrovací sada ukazuje příklad a je založena na klientovi, vidíte jinou šifrovací sadu.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení TLS.

Některé aplikační architektury, které používají MySQL pro své databázové služby, během instalace nepovolují protokol TLS ve výchozím nastavení. Server MySQL vynutil připojení TLS, ale pokud aplikace není nakonfigurovaná pro TLS, aplikace se nemusí podařit připojit k vašemu databázovému serveru. Informace o tom, jak povolit připojení TLS, najdete v dokumentaci k vaší aplikaci.

## <a name="sample-code"></a>Ukázka kódu
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

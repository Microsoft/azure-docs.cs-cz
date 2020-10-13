---
title: Konfigurace SSL-Azure Database for MariaDB
description: Pokyny, jak správně nakonfigurovat Azure Database for MariaDB a přidružené aplikace pro správné použití připojení SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 1b7127fd4807e1ddd3d5181d73c7f8e77f10cf77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055981"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MariaDB
Azure Database for MariaDB podporuje připojení Azure Database for MariaDBho serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="obtain-ssl-certificate"></a>Získat certifikát SSL


Stáhněte si certifikát potřebný ke komunikaci přes SSL s vaším serverem Azure Database for MariaDB [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) a uložte soubor certifikátu na místní disk (například tento kurz používá c:\ssl).
**Pro Microsoft Internet Explorer a Microsoft Edge:** Po dokončení stahování přejmenujte certifikát na BaltimoreCyberTrustRoot. CRT. pem.

> [!IMPORTANT] 
> Kořenový certifikát SSL je nastavený na vypršení platnosti od října 26, 2020 (10/26/2020). Aktualizujte prosím svoji aplikaci, aby používala [nový certifikát](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Další informace najdete v tématu [plánované aktualizace certifikátů](concepts-certificate-rotation.md) .

Podívejte se na následující odkazy pro certifikáty pro servery v cloudech v rámci svrchovaného cloudu: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Čína](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)a [Azure Německo](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

## <a name="bind-ssl"></a>Vázání SSL

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Připojení k serveru pomocí aplikace MySQL Workbench přes SSL
Nakonfigurujte aplikaci MySQL Workbench pro zabezpečené připojení přes SSL. 

1. V dialogovém okně nastavení nového připojení přejděte na kartu **SSL** . 

1. Aktualizujte pole **Použít SSL** na "vyžadovat".

1. Do pole **soubor CA SSL:** zadejte umístění souboru **BaltimoreCyberTrustRoot. CRT. pem**. 
    
    ![Uložit konfiguraci SSL](./media/howto-configure-ssl/mysql-workbench-ssl.png)

U existujících připojení můžete propojit SSL tak, že kliknete pravým tlačítkem na ikonu připojení a zvolíte upravit. Pak přejděte na kartu **SSL** a navažte na soubor certifikátu.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Připojení k serveru pomocí rozhraní příkazového řádku MySQL přes SSL
Dalším způsobem, jak vytvořit propojení s certifikátem SSL, je použití rozhraní příkazového řádku MySQL spuštěním následujících příkazů. 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Při použití rozhraní příkazového řádku MySQL v systému Windows se může zobrazit chyba `SSL connection error: Certificate signature check failed` . Pokud k tomu dojde, nahraďte `--ssl-mode=REQUIRED --ssl-ca={filepath}` parametry parametrem `--ssl` .

## <a name="enforcing-ssl-connections-in-azure"></a>Vynucování připojení SSL v Azure 

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Pomocí Azure Portal přejděte na server Azure Database for MariaDB a pak klikněte na **zabezpečení připojení**. Pomocí přepínacího tlačítka povolte nebo zakažte nastavení **Vynutilí připojení SSL** a pak klikněte na **Uložit**. Microsoft doporučuje vždy povolit nastavení **Vynutilí připojení SSL** pro rozšířené zabezpečení.
![povolení – SSL pro server MariaDB](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Použití Azure CLI
Parametr **SSL-Enforcement** můžete zapnout nebo vypnout pomocí povolených nebo zakázaných hodnot v Azure CLI.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Ověření připojení SSL
Spuštěním příkazu pro **stav** MySQL ověřte, že jste se připojili k serveru MariaDB pomocí protokolu SSL:
```sql
status
```
Ověřte, že je připojení zašifrované, a to tak, že zkontroluje výstup, který by měl zobrazovat:  **SSL: šifrování se používá, AES256-SHA.** 

## <a name="sample-code"></a>Ukázka kódu
K navázání zabezpečeného připojení k Azure Database for MariaDB přes SSL z vaší aplikace, přečtěte si následující ukázky kódu:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
        :ssl_mode => 'required'
    )
```
#### <a name="ruby-on-rails"></a>Ruby na železnici
```ruby
default: &default
  adapter: mysql2
  username: username@mydemoserver
  password: yourpassword
  host: mydemoserver.mariadb.database.azure.com
  sslca: BaltimoreCyberTrustRoot.crt.pem
  sslverify: true
```

### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->

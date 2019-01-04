---
title: Konfigurace připojení SSL pro zabezpečené připojení k Azure Database pro MariaDB
description: Pokyny, jak správně nakonfigurovat – Azure Database pro MariaDB a přidružených aplikací, aby správně používaly připojení SSL
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4bf18a44255903df09aae3382c0eb35a2a55eea5
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541808"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database pro MariaDB
Azure Database pro MariaDB podporuje připojení Azure Database pro MariaDB server ke klientským aplikacím pomocí vrstvy SSL (Secure Sockets). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="obtain-ssl-certificate"></a>Získat certifikát SSL
Stáhněte si certifikát nutný pro komunikaci pomocí protokolu SSL na váš server Azure Database for MariaDB z [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) a uložte soubor certifikátu na místním disku (v tomto kurzu používá c:\ssl třeba).
**Microsoft Internet Explorer nebo Microsoft Edge:** Po dokončení stahování certifikát přejmenujte BaltimoreCyberTrustRoot.crt.pem.

## <a name="bind-ssl"></a>Vytvoření vazby SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Připojení k serveru pomocí aplikace MySQL Workbench přes protokol SSL
Konfigurace aplikace MySQL Workbench se navázat zabezpečené připojení přes protokol SSL. V dialogu nastavení nového připojení, přejděte na **SSL** kartu. V **soubor SSL certifikační Autority:** zadejte umístění souboru **BaltimoreCyberTrustRoot.crt.pem**. 
![Uložit vlastní dlaždice](./media/howto-configure-ssl/mysql-workbench-ssl.png) pro existující připojení, můžete vytvořit vazbu SSL kliknutím pravým tlačítkem myši na ikonu připojení a klikněte na položku upravit. Potom přejděte **SSL** kartu a vytvořit vazbu na soubor certifikátu.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Připojování k serveru pomocí rozhraní příkazového řádku MySQL přes SSL
Dalším způsobem, jak vytvořit vazbu certifikátu SSL je použití rozhraní příkazového řádku MySQL spuštěním následujícího příkazu:
```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="enforcing-ssl-connections-in-azure"></a>Vynucení připojení SSL v Azure 
### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Pomocí webu Azure portal, navštivte vašeho serveru Azure Database for MariaDB a potom klikněte na **zabezpečení připojení**. Povolit nebo zakázat pomocí přepínacího tlačítka **vynucování SSL připojení** nastavení a potom klikněte na **Uložit**. Společnost Microsoft doporučuje vždy povolit **vynucování SSL připojení** nastavení pro zvýšení zabezpečení.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Použití Azure CLI
Můžete povolit nebo zakázat **vynucení ssl** parametr pomocí hodnot povolená nebo zakázaná v uvedeném pořadí v Azure CLI.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Ověřte připojení protokolem SSL
Spustit mysql **stav** příkazu ověřte, zda jste se připojili k MariaDB serveru pomocí SSL:
```sql
status
```
Potvrďte, že připojení je zašifrován kontrolou výstupu by se zobrazit:  **PROTOKOL SSL: Šifrování používá je AES256 SHA** 

## <a name="sample-code"></a>Ukázka kódu
Navázat zabezpečené připojení ke službě Azure Database pro MariaDB přes protokol SSL z vaší aplikace, podívejte se na následující ukázky kódu:

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
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mariadb.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mariadb.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
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

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->

---
title: Konfigurace připojení SSL k bezpečnému připojování k databázi Azure pro databázi MySQL
description: Pokyny, jak správně nakonfigurovat databázi Azure pro MySQL a přidružené aplikace správně používat připojení SSL
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ea4fd90670c2137fbe6127ba5c0b7641d846f407
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265423"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k databázi Azure pro databázi MySQL
Azure databáze pro databázi MySQL podporuje připojení databáze Azure pro server databáze MySQL pro klientské aplikace pomocí Secure Sockets Layer (SSL). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="step-1-obtain-ssl-certificate"></a>Krok 1: Získání certifikátu SSL
Stáhněte si certifikát potřebné pro komunikaci pomocí protokolu SSL s Azure databáze MySQL serveru od [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) a uložte soubor certifikátu na místní disk (Tento kurz používá c:\ssl třeba).
**Aplikace Microsoft Internet Explorer a Microsoft Edge:** po dokončení stahování, přejmenujte BaltimoreCyberTrustRoot.crt.pem certifikát.

## <a name="step-2-bind-ssl"></a>Krok 2: Vytvoření vazby SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Připojování k serveru pomocí nástroje MySQL Workbench přes protokol SSL
Nakonfigurujte MySQL Workbench pro zabezpečené připojení prostřednictvím protokolu SSL. V dialogu nastavit připojení k nové, přejděte na **SSL** kartě. V **souboru certifikační Autorita protokolu SSL:** pole, zadejte umístění souboru **BaltimoreCyberTrustRoot.crt.pem**. 
![Uložte vlastní dlaždici](./media/howto-configure-ssl/mysql-workbench-ssl.png) pro existující připojení, můžete vytvořit vazbu SSL kliknutím pravým tlačítkem myši na ikonu připojení a zvolte Upravit. Pak přejděte do **SSL** kartě a vytvořte vazbu soubor certifikátu.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Připojování k serveru pomocí rozhraní příkazového řádku MySQL přes protokol SSL
Další způsob vytvoření vazby certifikátu SSL je použití rozhraní příkazového řádku MySQL spuštěním následujícího příkazu:
```dos
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Krok 3: Vynucování připojení SSL v Azure 
### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Pomocí portálu Azure, Azure databáze pro server databáze MySQL navštěvovat a pak klikněte na tlačítko **zabezpečení připojení**. Přepínací tlačítko použít k povolení nebo zakázání **připojení SSL vynutit** nastavení a potom klikněte na **Uložit**. Microsoft doporučuje, vždy povolit **připojení SSL vynutit** nastavení pro lepší zabezpečení.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Použití Azure CLI
Můžete povolit nebo zakázat **ssl vynucení** parametr pomocí povoleno nebo zakázáno hodnoty v uvedeném pořadí v rozhraní příkazového řádku Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Krok 4: Ověření připojení protokolem SSL
Spuštění mysql **stav** příkazu ověřte, že jste připojení k serveru databáze MySQL pomocí protokolu SSL:
```dos
mysql> status
```
Zkontrolujte připojení se šifrují kontrolou výstupu, který by měl zobrazit: **SSL: šifer používán je AES256 SHA** 

## <a name="sample-code"></a>Ukázka kódu
Při navázat zabezpečené připojení k databázi Azure pro databázi MySQL přes protokol SSL z vaší aplikace, použijte následující ukázky kódu:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
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
        host='mydemoserver.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com', 
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
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool, InsecureSkipVerify: true})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>Java(MariaDB)
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
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

## <a name="next-steps"></a>Další postup
Přečtěte si následující různé možnosti připojení aplikace [knihovny připojení pro databázi Azure pro databázi MySQL](concepts-connection-libraries.md)

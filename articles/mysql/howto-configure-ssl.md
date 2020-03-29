---
title: Konfigurace protokolu SSL – databáze Azure pro MySQL
description: Pokyny pro správnou konfiguraci azure databáze pro MySQL a přidružené aplikace správně používat připojení SSL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 557986560eb27beddf939bcab7f4d72a66781e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062437"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Konfigurace připojení SSL ve vaší aplikaci pro bezpečné připojení k Azure Database for MySQL
Azure Database for MySQL podporuje připojení databáze Azure pro server MySQL ke klientským aplikacím pomocí ssl (Secure Sockets Layer). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

## <a name="step-1-obtain-ssl-certificate"></a>Krok 1: Získání certifikátu SSL
Stáhněte si certifikát potřebný ke komunikaci přes SSL [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) s azure database pro mysql server a uložte soubor certifikátu na místní jednotku (tento kurz používá například c:\ssl).
**Pro aplikaci Microsoft Internet Explorer a Microsoft Edge:** Po dokončení stahování přejmenujte certifikát na BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Krok 2: Vazba SSL

Konkrétní připojovací řetězce programovacího jazyka naleznete v [níže uvedeném ukázkovém kódu.](howto-configure-ssl.md#sample-code)

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Připojení k serveru pomocí MySQL Workbench přes SSL
Nakonfigurujte mysql workbench pro bezpečné připojení přes SSL. 

1. V dialogu Nastavení nového připojení přejděte na kartu **SSL.** 

1. Aktualizujte pole **Použít SSL** na "Vyžadovat".

1. Do pole **SSL CA File:** zadejte umístění souboru **baltimoreCyberTrustRoot.crt.pem**. 
    
    ![Uložit konfiguraci SSL](./media/howto-configure-ssl/mysql-workbench-ssl.png)

U existujících připojení můžete svázat ssl kliknutím pravým tlačítkem myši na ikonu připojení a zvolte upravit. Potom přejděte na kartu **SSL** a spojte soubor certifikátu.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Připojení k serveru pomocí příkazového příkazu CLI MySQL přes SSL
Dalším způsobem, jak svázat certifikát SSL, je použití rozhraní příkazového řádku MySQL provedením následujících příkazů. 

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Při použití rozhraní příkazového řádku MySQL v systému Windows se může zobrazit chyba `SSL connection error: Certificate signature check failed`. Pokud k tomu dojde, nahraďte `--ssl-mode=REQUIRED --ssl-ca={filepath}` parametry . `--ssl`

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Krok 3: Vynucení připojení SSL v Azure 
### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Na webu Azure najdete na serveru Azure Database for MySQL a klikněte na **zabezpečení připojení**. Pomocí přepínacího tlačítka můžete povolit nebo zakázat nastavení **Vynucení připojení SSL** a klepněte na tlačítko **Uložit**. Společnost Microsoft doporučuje vždy povolit nastavení **připojení Vynucení SSL** pro rozšířené zabezpečení.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Použití Azure CLI
Parametr **vynucení protokolu ssl** můžete povolit nebo zakázat pomocí hodnot Povoleno nebo Zakázáno v rozhraní příkazového řádku Azure.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Krok 4: Ověření připojení SSL
Spusťte příkaz **stav** mysql a ověřte, zda jste se připojili k serveru MySQL pomocí SSL:
```dos
mysql> status
```
Potvrďte, že připojení je šifrováno kontrolou výstupu, který by měl zobrazovat: **SSL: Používá se šifra je AES256-SHA** 

## <a name="sample-code"></a>Ukázka kódu
Chcete-li vytvořit zabezpečené připojení k databázi Azure pro MySQL přes SSL z vaší aplikace, naleznete v následujících ukázkách kódu:

Podívejte se na seznam [kompatibilních ovladačů](concepts-compatibility.md) podporovaných službou Azure Database for MySQL.

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP (Použití PDO)
```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
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
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-mysql-connector-for-java"></a>Java (MySQL konektor pro Java)
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
### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB konektor pro Java)
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

## <a name="next-steps"></a>Další kroky
Zkontrolujte různé možnosti připojení aplikací podle [knihoven připojení pro Azure Database for MySQL](concepts-connection-libraries.md)

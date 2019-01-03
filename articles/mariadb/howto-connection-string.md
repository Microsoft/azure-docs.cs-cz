---
title: Připojení aplikace ke službě Azure Database pro MariaDB
description: Tento dokument obsahuje seznam aktuálně podporovaných připojovacích řetězců pro aplikace pro připojení k Azure Database pro MariaDB, včetně technologie ADO.NET (C#), JDBC, Node.js, rozhraní ODBC, PHP, Python nebo Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 70cd25ff63101fa2a477cde2502d5d286b289366
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536570"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Postup připojení aplikací ke službě Azure Database pro MariaDB
Toto téma uvádí typy řetězce připojení, které jsou podporovány službou Azure Database pro MariaDB, spolu s příklady a šablony. V připojovacím řetězci může mít různé parametry a nastavení.

- Pokud chcete získat certifikát, najdete v článku [konfigurace SSL](./howto-configure-ssl.md).
- {your_host} =.mariadb.database.azure.com [servername]
- {your_user}@{servername} správně = formát ID uživatele pro ověřování.  Pokud používáte pouze ID uživatele, ověření se nezdaří.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

V tomto příkladu je název serveru `mydemoserver`, název databáze je `wpdb`, uživatelské jméno je `WPAdmin`, a heslo je `mypassword!2`. V důsledku toho by měl být připojovací řetězec:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Získat podrobnosti o řetězci připojení z webu Azure portal
V [webu Azure portal](https://portal.azure.com), přejděte k Azure Database pro MariaDB server a pak klikněte na tlačítko **připojovací řetězce** zobrazíte seznam řetězců pro vaši instanci: ![V podokně připojovací řetězce na webu Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Řetězec obsahuje podrobnosti, jako jsou ovladače, server a další databáze parametry připojení. Upravte tyto příklady použití vlastní parametry, jako je například název databáze, heslo a tak dále. Potom můžete tento řetězec pro připojení k serveru z vašeho kódu a aplikací.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->

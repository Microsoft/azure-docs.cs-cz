---
title: Připojovací řetězce – Azure Database for MariaDB
description: Tento dokument obsahuje seznam aktuálně podporovaných připojovacích řetězců pro aplikace pro připojení k Azure Database for MariaDB, včetně ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python a Ruby.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8119f0bfd1f9007cab0df93ad2c7ef22b4db2199
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662173"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Postup připojení aplikací k Azure Database for MariaDB
Toto téma uvádí typy připojovacích řetězců, které jsou podporovány Azure Database for MariaDB, spolu se šablonami a příklady. V připojovacím řetězci můžete mít různé parametry a nastavení.

- Informace o získání certifikátu najdete v tématu [Jak konfigurovat protokol SSL](./howto-configure-ssl.md).
- {your_host} = [servername]. MariaDB. Database. Azure. com
- {your_user} @ {servername} = formát userID pro ověřování správně.  Pokud použijete identifikátor userID, ověření se nezdaří.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

V tomto příkladu je název serveru `mydemoserver` , název databáze je `wpdb` , uživatelské jméno `WPAdmin` a heslo `mypassword!2` . V důsledku toho by měl být připojovací řetězec:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Získat podrobnosti o připojovacím řetězci z Azure Portal
V [Azure Portal](https://portal.azure.com)přejděte na Azure Database for MariaDB Server a pak klikněte na **připojovací řetězce** . zobrazí se seznam řetězců pro vaši instanci: ![ podokno připojovací řetězce v Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

Řetězec poskytuje podrobnosti, jako je například ovladač, server a další parametry připojení databáze. Upravte tyto příklady tak, aby používaly vlastní parametry, jako je třeba název databáze, heslo a tak dále. Pak můžete použít tento řetězec pro připojení k serveru z kódu a aplikací.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->

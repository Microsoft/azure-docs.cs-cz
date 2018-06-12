---
title: Připojení aplikace k databázi Azure pro databázi MySQL
description: Tento dokument obsahuje seznam aktuálně podporované připojovací řetězce pro aplikace pro připojení s Azure Database pro databázi MySQL, včetně ADO.NET (C#), JDBC, Node.js, rozhraní ODBC, PHP, Python a Ruby.
services: mysql
author: mswutao
ms.author: wuta
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 083c6850151340ba8cb307a237f489e5923761a5
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265679"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Postup připojení aplikace k databázi Azure pro databázi MySQL
Toto téma uvádí typy řetězec připojení, které podporuje Azure databáze pro databázi MySQL, spolu s šablonami a příklady. V připojovacím řetězci může mít různé parametry a nastavení.

- K získání certifikátu, najdete v části [postup konfigurace protokolu SSL](./howto-configure-ssl.md).
- {your_host} = <servername>.mysql.database.azure.com
- {your_user}@{servername} správně = formát ID uživatele pro ověřování.  Pokud používáte pouze ID uživatele, se ověřování nezdaří.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

V tomto příkladu je název serveru `mydemoserver`, název databáze je `wpdb`, uživatelské jméno je `WPAdmin`, a heslo je `mypassword!2`. V důsledku toho musí mít připojovací řetězec:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Získat podrobnosti o řetězce připojení z portálu Azure
V [portál Azure](https://portal.azure.com), přejděte k vaší databázi Azure pro server databáze MySQL a pak klikněte na tlačítko **připojovací řetězce** získat seznam řetězců pro instanci: ![podokně řetězce připojení ve službě Azure portál](./media/howto-connection-strings/connection-strings-on-portal.png)

Řetězec obsahuje podrobné informace jako jsou ovladače, serveru a jiné databázi parametrů připojení. Upravte tyto příklady, které chcete používat vlastní parametry, jako je název databáze, heslo a tak dále. Pak můžete tento řetězec pro připojení k serveru z vašeho kódu a aplikace.

## <a name="next-steps"></a>Další postup
- Další informace o knihovnách připojení najdete v tématu [koncepty - knihovny připojení](./concepts-connection-libraries.md).

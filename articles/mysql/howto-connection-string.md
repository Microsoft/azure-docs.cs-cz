---
title: Připojení aplikace ke službě Azure Database for MySQL
description: Tento dokument obsahuje seznam aktuálně podporovaných připojovacích řetězců pro aplikace pro připojení s využitím Azure Database for MySQL, včetně technologie ADO.NET (C#), JDBC, Node.js, rozhraní ODBC, PHP, Python nebo Ruby.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 06bd91adb0a86198f7709d0989624657ce00dfa9
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054412"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Postup připojení aplikací k Azure Database for MySQL
Toto téma uvádí typy řetězce připojení, které jsou podporovány službou Azure Database for MySQL, spolu s příklady a šablony. V připojovacím řetězci může mít různé parametry a nastavení.

- Pokud chcete získat certifikát, najdete v článku [konfigurace SSL](./howto-configure-ssl.md).
- {your_host} = <servername>.mysql.database.azure.com
- {your_user}@{servername} správně = formát ID uživatele pro ověřování.  Pokud používáte pouze ID uživatele, ověření se nezdaří.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

V tomto příkladu je název serveru `mydemoserver`, název databáze je `wpdb`, uživatelské jméno je `WPAdmin`, a heslo je `mypassword!2`. V důsledku toho by měl být připojovací řetězec:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Získat podrobnosti o řetězci připojení z webu Azure portal
V [webu Azure portal](https://portal.azure.com), přejděte na váš server Azure Database for MySQL a pak klikněte na tlačítko **připojovací řetězce** zobrazíte seznam řetězců pro vaši instanci: ![Connection řetězce podokně ve službě Azure portál](./media/howto-connection-strings/connection-strings-on-portal.png)

Řetězec obsahuje podrobnosti, jako jsou ovladače, server a další databáze parametry připojení. Upravte tyto příklady použití vlastní parametry, jako je například název databáze, heslo a tak dále. Potom můžete tento řetězec pro připojení k serveru z vašeho kódu a aplikací.

## <a name="next-steps"></a>Další postup
- Další informace o knihovnách připojení, najdete v části [Principy – knihovny připojení](./concepts-connection-libraries.md).

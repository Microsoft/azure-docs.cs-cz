---
title: Připojovací řetězce – Azure Database for MySQL
description: Tento dokument obsahuje seznam aktuálně podporovaných připojovacích řetězců pro aplikace pro připojení k Azure Database for MySQL, včetně ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python a Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ff257e101ca925a554f2343d357e2042d2930b45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062398"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Postup připojení aplikací k Azure Database for MySQL
Toto téma uvádí typy připojovacích řetězců, které jsou podporované Azure Database for MySQL, spolu se šablonami a příklady. V připojovacím řetězci můžete mít různé parametry a nastavení.

- Chcete-li získat certifikát, naleznete v tématu [Konfigurace protokolu SSL](./howto-configure-ssl.md).
- {your_host} \<= název serveru>.mysql.database.azure.com
- {your_user}@{název_serveru} = formát id uživatele pro správné ověřování.  Pokud používáte pouze ID uživatele, ověřování se nezdaří.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

V tomto příkladu je `mydemoserver`název serveru `wpdb`, název databáze `WPAdmin`je , `mypassword!2`uživatelské jméno je a heslo je . V důsledku toho by měl být připojovací řetězec:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Získání podrobností o připojovacím řetězci z webu Azure Portal
Na [webu Azure Portal](https://portal.azure.com)přejděte na server Azure Database for MySQL a potom kliknutím ![na **připojovací řetězce** získejte seznam řetězců pro vaši instanci: Podokno připojovacích řetězců na webu Azure Portal.](./media/howto-connection-strings/connection-strings-on-portal.png)

Řetězec obsahuje podrobnosti, jako je například ovladač, server a další parametry připojení databáze. Upravte tyto příklady tak, aby používaly vlastní parametry, například název databáze, heslo a tak dále. Tento řetězec pak můžete použít pro připojení k serveru z kódu a aplikací.

## <a name="next-steps"></a>Další kroky
- Další informace o knihovnách připojení naleznete v [tématu Koncepty – Knihovny připojení](./concepts-connection-libraries.md).

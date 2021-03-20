---
title: Připojovací řetězce – Azure Database for MySQL
description: Tento dokument obsahuje seznam aktuálně podporovaných připojovacích řetězců pro aplikace pro připojení k Azure Database for MySQL, včetně ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python a Ruby.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 40af2660f0052a876ef9310bc2426295ba67558b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541483"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Postup připojení aplikací k Azure Database for MySQL
Toto téma uvádí typy připojovacích řetězců, které jsou podporovány Azure Database for MySQL, spolu se šablonami a příklady. V připojovacím řetězci můžete mít různé parametry a nastavení.

- Informace o získání certifikátu najdete v tématu [Jak konfigurovat protokol SSL](./howto-configure-ssl.md).
- {your_host} = \<servername> . MySQL.Database.Azure.com
- {your_user} @ {servername} = formát userID pro ověřování správně.  Pokud použijete identifikátor userID, ověření se nezdaří.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

V tomto příkladu je název serveru `mydemoserver` , název databáze je `wpdb` , uživatelské jméno `WPAdmin` a heslo `mypassword!2` . V důsledku toho by měl být připojovací řetězec:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Získat podrobnosti o připojovacím řetězci z Azure Portal
V [Azure Portal](https://portal.azure.com)přejděte na Azure Database for MySQL server a pak klikněte na **připojovací řetězce** . zobrazí se seznam řetězců pro vaši instanci: :::image type="content" source="./media/howto-connection-strings/connection-strings-on-portal.png" alt-text="podokno připojovací řetězce v Azure Portal":::

Řetězec poskytuje podrobnosti, jako je například ovladač, server a další parametry připojení databáze. Upravte tyto příklady tak, aby používaly vlastní parametry, jako je třeba název databáze, heslo a tak dále. Pak můžete použít tento řetězec pro připojení k serveru z kódu a aplikací.

## <a name="next-steps"></a>Další kroky
- Další informace o knihovnách připojení najdete v tématu [Koncepty – knihovny připojení](./concepts-connection-libraries.md).

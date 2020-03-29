---
title: Knihovny připojení – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje několik knihoven a ovladačů, které můžete použít při kódování aplikací pro připojení a dotaz na Azure Database pro PostgreSQL - jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768890"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Knihovny připojení pro Azure Database for PostgreSQL – jeden server
Tento článek uvádí knihovny a ovladače, které vývojáři mohou použít k vývoji aplikací pro připojení a dotaz na Azure Database pro PostgreSQL.

## <a name="client-interfaces"></a>Klientská rozhraní
Většina jazykových klientských knihoven používaných pro připojení k PostgreSQL serveru jsou externí projekty a jsou distribuovány nezávisle. Uvedené knihovny jsou podporované na platformách Windows, Linux a Mac pro připojení k Azure Database for PostgreSQL. V části Další kroky je uvedeno několik příkladů rychlého startu.

| **Jazyk** | **Klientské rozhraní** | **Další informace** | **Stáhnout** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Kompatibilní s rozhraním DB API 2.0 | [Stáhnout](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Rozšíření databáze | [Nainstalovat](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm balíček](https://www.npmjs.com/package/pg) | Klient bez blokování javascriptu | [Nainstalovat](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Typ 4 JDBC ovladač | [Ke stažení](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg klenot](https://deveiate.org/code/pg/) | Rozhraní Ruby | [Stáhnout](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Přejít | [Balíček pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres řidič | [Nainstalovat](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | ADO.NET poskytovatel dat | [Stáhnout](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Ovladač ODBC | [Stáhnout](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primární jazykové rozhraní Jazyka C | Zahrnuje |
| C++ | [libpqxx řekl:](http://pqxx.org/) | Rozhraní C++ nového stylu | [Stáhnout](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Další kroky
Přečtěte si tyto rychlé starty o tom, jak se připojit k Azure Database pro PostgreSQL a dotazovat se na ně pomocí zvoleného jazyka:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Přejít](./connect-go.md)

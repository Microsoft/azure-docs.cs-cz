---
title: Knihovny připojení – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje několik knihoven a ovladačů, které můžete použít při kódování aplikací pro připojení a dotazování Azure Database for PostgreSQL-Single server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: e182ef6c5fb7bf1b76424fffdbc862775e93e29c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606296"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Knihovny připojení pro Azure Database for PostgreSQL – jeden server
Tento článek obsahuje seznam knihoven a ovladačů, které můžou vývojáři použít k vývoji aplikací pro připojení k Azure Database for PostgreSQL a dotazování na ně.

## <a name="client-interfaces"></a>Klientská rozhraní
Většina jazykových klientských knihoven používaných pro připojení k serveru PostgreSQL jsou nezávisle distribuované externí projekty. Uvedené knihovny jsou podporované na platformách Windows, Linux a Mac pro připojení k Azure Database for PostgreSQL. V části Další kroky jsou uvedeny některé příklady rychlých startů.

| **Jazyk** | **Rozhraní klienta** | **Další informace** | **Stáhnout** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2,0 – kompatibilní | [Stáhnout](http://initd.org/psycopg/download/) |
| PHP | [php – PgSQL](https://secure.php.net/manual/en/book.pgsql.php) | Rozšíření databáze | [Instalace](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Balíček pg npm](https://www.npmjs.com/package/pg) | Čistý JavaScript bez blokování klienta | [Instalace](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Typ 4 ovladač JDBC | [Stáhnout](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG Gem](https://deveiate.org/code/pg/) | Rozhraní Ruby | [Stáhnout](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [PQ balíčku](https://godoc.org/github.com/lib/pq) | Ovladač čistého přechodu na Postgres | [Instalace](https://github.com/lib/pq/blob/master/README.md) |
| C \# /.NET | [Npgsql](https://www.npgsql.org/) | ADO.NET Zprostředkovatel dat | [Stáhnout](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Ovladač ODBC | [Stáhnout](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primární rozhraní jazyka C | Zahrnuje |
| C++ | [libpqxx](http://pqxx.org/) | Rozhraní C++ pro nové styly | [Stáhnout](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Další kroky
Přečtěte si tyto rychlé starty, jak se připojit k a dotazovat Azure Database for PostgreSQL pomocí vašeho jazyka podle vlastního výběru:

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Jazyk Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  [Php](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md)  |  [Přejít](./connect-go.md)

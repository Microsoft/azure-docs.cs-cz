---
title: Připojení knihoven pro službu Azure Database for PostgreSQL
description: Tento článek popisuje několik knihoven a ovladače, že vývojáři mohou použít při psaní kódu aplikace k připojení a dotazování Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: edbd3b36e1c40cda3b39c85f3deb4c9e8540fd1b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984957"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Připojení knihoven pro službu Azure Database for PostgreSQL
Tento článek obsahuje seznam knihoven a ovladače, které mohou vývojáři k vývoji aplikací k připojení a dotazování Azure Database for PostgreSQL.

## <a name="client-interfaces"></a>Klientská rozhraní
Většina klientské knihovny jazyk používaný pro připojení k serveru PostgreSQL jsou externí projekty a distribuují se nezávisle na sobě. Knihovny uvedené se podporují na platformách Windows, Linux a Mac pro připojení k Azure Database for PostgreSQL. Několik příkladů rychlý Start jsou uvedeny v části Další kroky.

| **Jazyk** | **Rozhraní klienta** | **Další informace** | **Stáhnout** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Rozhraní API DB 2.0 nedodržující předpisy | [Stáhnout](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Rozšíření databáze | [Instalace](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Balíček npm PG](https://www.npmjs.com/package/pg) | Čistě javascriptový neblokující klient | [Instalace](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Ovladač JDBC typu 4 | [Stáhnout](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Nástroj gem PG](https://deveiate.org/code/pg/) | Ruby rozhraní | [Stáhnout](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Přejít | [Balíček pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres ovladače | [Instalace](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Zprostředkovatel dat ADO.NET | [Stáhnout](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Ovladač ODBC | [Stáhnout](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primární rozhraní jazyka C | Zahrnuje |
| C++ | [libpqxx](http://pqxx.org/) | Nový styl C++ rozhraní | [Stáhnout](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Další postup
Přečtěte si v těchto rychlých startech o tom, jak připojení a dotazování – Azure Database for PostgreSQL pomocí svůj jazyk:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [přejít](./connect-go.md)

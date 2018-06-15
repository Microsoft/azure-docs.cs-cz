---
title: Knihovny připojení pro databázi Azure pro PostgreSQL
description: Tento článek popisuje několik knihovny a ovladače, vývojáři mohou použít při kódování aplikace k připojení a dotazování databáze Azure pro PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7f0dd217a124f530ba009c9a5588691f604827e5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29687002"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Knihovny připojení pro databázi Azure pro PostgreSQL
V tomto článku jsou uvedené knihovny a ovladače, které mohou vývojáři použít k vývoji aplikací a připojení k dotazování databáze Azure pro PostgreSQL.

## <a name="client-interfaces"></a>Rozhraní klienta
Většina knihovny klienta jazyk používaný pro připojení k serveru PostgreSQL jsou externí projekty a distribuují nezávisle. Knihovny uvedené jsou podporovány na platformách systému Windows, Linuxu a Macu pro připojení k databázi Azure pro PostgreSQL. Několik příkladů rychlý Start jsou uvedeny v části Další kroky.

| **Jazyk** | **Rozhraní klienta** | **Další informace** | **Stáhnout** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Rozhraní API DB 2.0 kompatibilní | [Stáhnout](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Rozšíření databáze | [Instalace](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Balíčku npm PG](https://www.npmjs.com/package/pg) | Čistý JavaScript neblokující klienta | [Instalace](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Ovladač JDBC typu 4 | [Stáhnout](https://jdbc.postgresql.org/download.html)  |
| Ruby | [PG gem](https://deveiate.org/code/pg/) | Poznámky Ruby rozhraní | [Stáhnout](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Přejít | [Balíček pq](https://godoc.org/github.com/lib/pq) | Čistý přejděte postgres ovladačů | [Instalace](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Zprostředkovatel dat ADO.NET | [Stáhnout](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Ovladač ODBC | [Stáhnout](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primární rozhraní jazyka C | Zahrnuje |
| C++ | [libpqxx](http://pqxx.org/) | Nový styl rozhraní C++ | [Stáhnout](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Další postup
Přečtěte si tyto elementy QuickStart o tom, jak k připojení a dotazování databáze Azure pro PostgreSQL pomocí vámi zvolený jazyk:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [přejděte](./connect-go.md)

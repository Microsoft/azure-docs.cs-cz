---
title: Připojení a dotazování – PostgreSQL na jeden server
description: Odkazy na rychlé starty ukazující, jak se připojit k Azure Database for PostgreSQLmu jednomu serveru a spustit dotazy.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 413f5fc1f6579102b62042c1470816470c2e1449
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546547"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Přehled připojení a dotazování Azure Database for PostgreSQL – jeden server

Následující dokument obsahuje odkazy na příklady, které ukazují, jak se připojit a dotazovat se na Azure Database for PostgreSQL jednom serveru. Tato příručka také obsahuje doporučení a rozšíření TLS, pomocí kterých se můžete připojit k serveru v níže uvedených podporovaných jazycích.

## <a name="quickstarts"></a>Rychlé starty

| Rychlé zprovoznění | Description |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|Pomocí pgAdmin se můžete připojit k serveru a zjednoduší se tím vytváření, údržba a používání databázových objektů.|
|[psql v Azure Cloud Shell](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|Tento článek ukazuje, jak spustit [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) v [Azure Cloud Shell](../cloud-shell/overview.md) pro připojení k vašemu serveru a následné spuštění příkazů pro dotazování, vkládání, aktualizaci a odstraňování dat v databázi. **Psql** můžete spustit, pokud je nainstalovaná ve vašem vývojovém prostředí.|
|[PostgreSQL s VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|Rozšíření Azure databases pro VS Code (Preview) umožňuje procházet a dotazování serveru PostgreSQL lokálně i v cloudu pomocí scrapbooks s bohatou technologií IntelliSense. |
|[PHP](connect-php.md)|Tento rychlý Start ukazuje použití PHP k vytvoření programu pro připojení k databázi a použití práce s databázovými objekty k dotazování dat.|
|[Java](connect-java.md)|V tomto rychlém startu se dozvíte, jak se pomocí Java připojit k databázi a potom použít práci s databázovými objekty k dotazování dat.|
|[Node.js](connect-nodejs.md)|Tento rychlý Start ukazuje, jak použít Node.js k vytvoření programu pro připojení k databázi a použití práce s databázovými objekty k dotazování dat.|
|[.NET (C#)](connect-csharp.md)|Tento rychlý Start ukazuje, jak use.NET (C#) vytvořit program v jazyce C# pro připojení k databázi a použití práce s databázovými objekty k dotazování dat.|
|[Přejít](connect-go.md)|V tomto rychlém startu se dozvíte, jak použít Přejít k připojení k databázi. Ukazuje se také použití příkazů jazyka Transact-SQL k dotazování a úpravě dat.|
|[Python](connect-python.md)|Tento rychlý Start ukazuje použití Pythonu pro připojení k databázi a použití práce s databázovými objekty k dotazování dat. |
|[Ruby](connect-ruby.md)|Tento rychlý Start ukazuje použití Ruby k vytvoření programu pro připojení k databázi a použití práce s databázovými objekty k dotazování dat.|

## <a name="tls-considerations-for-database-connectivity"></a>Požadavky TLS pro připojení databáze

Protokol TLS (Transport Layer Security) se používá u všech ovladačů, které Microsoft poskytuje nebo podporuje pro připojení k databázím v Azure Database for PostgreSQL. Není potřeba žádná zvláštní konfigurace, ale vynutila TLS 1,2 pro nově vytvořené servery. Doporučujeme, abyste při použití TLS 1,0 a 1,1 aktualizovali verzi TLS pro vaše servery. Viz [jak nakonfigurovat TLS](howto-tls-configurations.md) .

## <a name="postgresql-extensions"></a>Rozšíření PostgreSQL

PostgreSQL poskytuje možnost rozšíření funkcí databáze pomocí rozšíření. Rozšíření je balíček, který vznikne sloučením několika souvisejících objektů SQL. Tento balíček můžete jedním příkazem do databáze načíst nebo ho z ní odebrat. Po načtení do databáze rozšíření fungují jako integrované funkce.

- [Rozšíření Postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [Rozšíření Postgres 10](./concepts-extensions.md#postgres-10-extensions)
- [Rozšíření Postgres 9,6](./concepts-extensions.md#postgres-96-extensions)
- [Rozšíření Postgres 9,5](./concepts-extensions.md#postgres-95-extensions)

Další podrobnosti najdete v článku [Jak používat rozšíření PostgreSQL na jednom serveru](concepts-extensions.md).

## <a name="next-steps"></a>Další kroky

- [Migrace dat pomocí výpisu a obnovení](howto-migrate-using-dump-and-restore.md)
- [Migrace dat pomocí importu a exportu](howto-migrate-using-export-and-import.md)

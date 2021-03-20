---
title: Připojení a dotazování – flexibilní server PostgreSQL
description: Odkazy na rychlé starty, které ukazují, jak se připojit k Azure Database for PostgreSQL flexibilnímu serveru a spouštět dotazy.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364563"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>Přehled připojení a dotazování pro Azure Database for PostgreSQL – flexibilní Server

Následující dokument obsahuje odkazy na příklady, které ukazují, jak se připojit a dotazovat se na Azure Database for PostgreSQL jednom serveru. Tato příručka také obsahuje doporučení a rozšíření TLS, pomocí kterých se můžete připojit k serveru v níže uvedených podporovaných jazycích.

>[!IMPORTANT]
> Azure Database for PostgreSQL flexibilní Server je ve **verzi Preview**.

## <a name="quickstarts"></a>Rychlé starty

| Rychlé zprovoznění | Description |
|---|---|
|[PgAdmin](https://www.pgadmin.org/)|Pomocí pgAdmin se můžete připojit k serveru a zjednoduší se tím vytváření, údržba a používání databázových objektů.|
|[psql v Azure Cloud Shell](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|Tento článek ukazuje, jak spustit [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) v [Azure Cloud Shell](../../cloud-shell/overview.md) pro připojení k vašemu serveru a následné spuštění příkazů pro dotazování, vkládání, aktualizaci a odstraňování dat v databázi. **Psql** můžete spustit, pokud je nainstalovaná ve vašem vývojovém prostředí.|
|[Python](connect-python.md)|Tento rychlý Start ukazuje použití Pythonu pro připojení k databázi a použití práce s databázovými objekty k dotazování dat. |
|[Django s App Service](tutorial-django-app-service-postgres.md)|Tento kurz ukazuje použití Ruby k vytvoření programu pro připojení k databázi a použití práce s databázovými objekty k dotazování dat.|

## <a name="tls-considerations-for-database-connectivity"></a>Požadavky TLS pro připojení databáze

Protokol TLS (Transport Layer Security) se používá u všech ovladačů, které Microsoft poskytuje nebo podporuje pro připojení k databázím v Azure Database for PostgreSQL. Není potřeba žádná zvláštní konfigurace, ale vynutila TLS 1,2 pro nově vytvořené servery. Doporučujeme, abyste při použití TLS 1,0 a 1,1 aktualizovali verzi TLS pro vaše servery. Viz [jak nakonfigurovat TLS](how-to-connect-tls-ssl.md) .

## <a name="postgresql-extensions"></a>Rozšíření PostgreSQL

PostgreSQL poskytuje možnost rozšíření funkcí databáze pomocí rozšíření. Rozšíření je balíček, který vznikne sloučením několika souvisejících objektů SQL. Tento balíček můžete jedním příkazem do databáze načíst nebo ho z ní odebrat. Po načtení do databáze rozšíření fungují jako integrované funkce.

- [Postgres 12 rozšíření](./concepts-extensions.md#postgres-12-extensions)
- [Rozšíření Postgres 11](./concepts-extensions.md#postgres-11-extensions)
- [dblink a postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

Další podrobnosti najdete v článku [Jak používat rozšíření PostgreSQL na flexibilním serveru](concepts-extensions.md).

## <a name="next-steps"></a>Další kroky

- [Migrace dat pomocí výpisu a obnovení](../howto-migrate-using-dump-and-restore.md)
- [Migrace dat pomocí importu a exportu](../howto-migrate-using-export-and-import.md)

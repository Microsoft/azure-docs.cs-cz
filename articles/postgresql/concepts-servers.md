---
title: Servery – databáze Azure pro PostgreSQL – jeden server
description: Tento článek obsahuje důležité informace a pokyny pro konfiguraci a správu Azure Database pro PostgreSQL – jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d86170a53b4bfbe712bbca12db4d6063214aba21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768159"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – Jeden server
Tento článek obsahuje důležité informace a pokyny pro práci s Azure Database for PostgreSQL – single server.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Co je server Azure Database for PostgreSQL?
Server v Azure Database for PostgreSQL – možnost nasazení jednoho serveru je centrálním bodem pro správu pro více databází. Jedná se o stejnou konstrukci serveru PostgreSQL, kterou můžete znát v místním světě. Konkrétně je služba PostgreSQL spravována, poskytuje záruky výkonu, zveřejňuje přístup a funkce na úrovni serveru.

Databáze Azure pro postgreSQL server:

- Vytvoří se v rámci předplatného Azure.
- Je nadřazený prostředek pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner se silnou životnost sémantiku - odstranit server a odstraní obsažené databáze.
- Collocates prostředky v oblasti.
- Poskytuje koncový bod připojení pro přístup k serveru a databázi. 
- Poskytuje obor zásady správy, které se vztahují k jeho databázím: přihlášení, brána firewall, uživatelé, role, konfigurace atd.
- Je k dispozici ve více verzích. Další informace naleznete v [tématu podporované verze databáze PostgreSQL](concepts-supported-versions.md).
- Je rozšiřitelný uživateli. Další informace naleznete v [tématu PostgreSQL extensions](concepts-extensions.md).

V rámci azure databáze pro postgresql server můžete vytvořit jednu nebo více databází. Můžete se rozhodnout vytvořit jednu databázi na server pro využití všech prostředků nebo vytvořit několik databází, které budou prostředky sdílet. Ceny jsou strukturované pro server na základě konfigurace cenové úrovně, virtuálních jader a úložiště (GB). Další informace naleznete v tématu [Cenové úrovně](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Jak se připojím a ověřím k databázi Azure pro postgreSQL server?
Následující prvky pomáhají zajistit bezpečný přístup k databázi:

|||
|:--|:--|
| **Ověřování a autorizace** | Server Azure Database for PostgreSQL podporuje nativní ověřování PostgreSQL. Můžete se připojit a ověřit na serveru přihlašovacími údaji správce serveru. |
| **Protokol** | Služba podporuje protokol založený na zprávě používaný PostgreSQL. |
| **TCP/IP** | Protokol je podporován přes TCP/IP a přes unixové domény. |
| **Brána firewall** | Z důvodu ochrany dat zabrání pravidlo brány firewall veškerému přístupu k serveru a k jeho databázím, dokud neurčíte, které počítače mají oprávnění. Viz [Azure Database for PostgreSQL Server firewall rules](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Správa serveru
Azure Database for PostgreSQL servery můžete spravovat pomocí [portálu Azure nebo](https://portal.azure.com) [Azure CLI](/cli/azure/postgres).

Při vytváření serveru nastavíte přihlašovací údaje pro uživatele správce. Správce je uživatel s nejvyššími oprávněními, které máte na serveru. Patří do role azure_pg_admin. Tato role nemá úplná oprávnění superuživatele. 

Atribut Superuser PostgreSQL je přiřazen azure_superuser, který patří do spravované služby. Nemáte přístup k této roli.

Azure Database for PostgreSQL server má výchozí databáze: 
- **postgres** - Výchozí databáze, ke které se můžete připojit po vytvoření serveru.
- **azure_maintenance** – Tato databáze se používá k oddělení procesů, které poskytují spravovanou službu, od akcí uživatelů. Nemáte přístup k této databázi.
- **azure_sys** – Databáze pro úložiště dotazů. Tato databáze neshromažďuje data, když je úložiště dotazů vypnuté. toto je výchozí nastavení. Další informace naleznete v přehledu [úložiště dotazů](concepts-query-store.md).


## <a name="server-parameters"></a>Parametry serveru
Parametry serveru PostgreSQL určují konfiguraci serveru. V Azure Database for PostgreSQL se seznam parametrů dá zobrazit a upravit pomocí portálu Azure nebo rozhraní příkazového řádku Azure. 

Jako spravovaná služba pro Postgres jsou konfigurovatelné parametry v Azure Database for PostgreSQL podmnožinou parametrů v místní instanci Postgres (Další informace o parametrech Postgres naleznete v [dokumentaci PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Váš Azure Database for PostgreSQL server je povolený s výchozími hodnotami pro každý parametr při vytváření. Některé parametry, které by vyžadovaly restartování serveru nebo přístup superuživatele, aby se změny projevily, nemohou být nakonfigurovány uživatelem.


## <a name="next-steps"></a>Další kroky
- Přehled služby najdete v tématu [Azure Database for PostgreSQL Overview](overview.md).
- Informace o konkrétních kvótách prostředků a omezeních na základě **vaší úrovně služeb**naleznete v [tématu Úrovně služeb](concepts-pricing-tiers.md).
- Informace o připojení ke službě najdete v [tématu Connection libraries for Azure Database for PostgreSQL](concepts-connection-libraries.md).
- Zobrazení a úprava parametrů serveru prostřednictvím [portálu Azure nebo](howto-configure-server-parameters-using-portal.md) [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).

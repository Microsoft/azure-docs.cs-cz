---
title: Servery – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje informace a pokyny pro konfiguraci a správu Azure Database for PostgreSQL-Single server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 1d1751ed56f63105ce7775ceaed9ea2dcca1f8bf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628131"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – Jeden server
Tento článek popisuje informace a pokyny pro práci s Azure Database for PostgreSQLm na jednom serveru.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Co je server Azure Database for PostgreSQL?
Server v možnosti nasazení Azure Database for PostgreSQL-Single server je centrálním bodem správy pro více databází. Je to stejná konstrukce PostgreSQL serveru, kterou můžete znát v místním světě. Konkrétně je spravovaná služba PostgreSQL, zajišťuje záruky na výkon, zpřístupňuje přístup a funkce na úrovni serveru.

Server Azure Database for PostgreSQL:

- Vytvoří se v rámci předplatného Azure.
- Je nadřazeným prostředkem pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner se sémantikou silné životnosti – odstraní Server a odstraní databáze s omezením.
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro server a přístup k databázi. 
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze: přihlašovací jméno, brána firewall, uživatelé, role, konfigurace atd.
- Je k dispozici ve více verzích. Další informace najdete v tématu [podporované verze databáze PostgreSQL](concepts-supported-versions.md).
- Je rozšiřitelný pro uživatele. Další informace najdete v tématu [rozšíření PostgreSQL](concepts-extensions.md).

V rámci Azure Database for PostgreSQL serveru můžete vytvořit jednu nebo více databází. Můžete se rozhodnout vytvořit jednu databázi na server pro využití všech prostředků nebo vytvořit několik databází, které budou prostředky sdílet. Ceny jsou strukturované na server, a to na základě konfigurace cenové úrovně, virtuální jádra a úložiště (GB). Další informace najdete v tématu [cenové úrovně](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Návody se připojit a ověřit pro Azure Database for PostgreSQL Server?
Následující prvky vám pomůžou zajistit bezpečný přístup k vaší databázi:

|Koncept zabezpečení|Description|
|:--|:--|
| **Ověřování a autorizace** | Server Azure Database for PostgreSQL podporuje nativní ověřování PostgreSQL. Můžete se připojit a ověřit na serveru přihlašovacími údaji správce serveru. |
| **Protokol** | Služba podporuje protokol založený na zprávách, který používá PostgreSQL. |
| **TCP/IP** | Protokol je podporován přes protokol TCP/IP a prostřednictvím soketů systému UNIX-doména. |
| **Brána firewall** | Pro lepší ochranu dat pravidlo brány firewall zabraňuje všem přístupům k vašemu serveru a jeho databázím, dokud neurčíte, které počítače mají oprávnění. Viz [pravidla brány firewall serveru Azure Database for PostgreSQL](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Správa serveru
Azure Database for PostgreSQL servery můžete spravovat pomocí [Azure Portal](https://portal.azure.com) nebo rozhraní příkazového [řádku Azure](/cli/azure/postgres).

Při vytváření serveru nastavíte přihlašovací údaje pro uživatele správce. Uživatel s oprávněními správce je nejvyšší oprávnění, které máte na serveru. Patří do role azure_pg_admin. Tato role nemá úplná oprávnění uživatele. 

Atribut PostgreSQL-User je přiřazen k azure_superuser, která patří do spravované služby. K této roli nemáte přístup.

Server Azure Database for PostgreSQL má výchozí databáze: 
- **Postgres** – výchozí databáze, ke které se můžete připojit, až se server vytvoří.
- **azure_maintenance** – Tato databáze slouží k oddělení procesů, které poskytují spravovanou službu z akcí uživatele. K této databázi nemáte přístup.
- **azure_sys** – databáze pro úložiště dotazů. Tato databáze neshromažďuje data v případě, že je úložiště dotazů vypnuté. Toto je výchozí nastavení. Další informace najdete v tématu [Přehled úložiště dotazů](concepts-query-store.md).


## <a name="server-parameters"></a>Parametry serveru
Parametry serveru PostgreSQL určují konfiguraci serveru. V Azure Database for PostgreSQL se seznam parametrů dá zobrazit a upravit pomocí Azure Portal nebo rozhraní příkazového řádku Azure. 

Konfigurovatelné parametry v Azure Database for PostgreSQL jsou jako spravovaná služba pro Postgres podmnožinou parametrů v místní instanci Postgres (Další informace o parametrech Postgres najdete v [dokumentaci PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Azure Database for PostgreSQL Server je povolen s výchozími hodnotami pro každý parametr při vytváření. Některé parametry, které by vyžadovaly, aby se změny projevily na serveru nebo v případě, že se změny projeví, nemůže nastavit uživatel.


## <a name="next-steps"></a>Další kroky
- Přehled služby najdete v tématu [přehled Azure Database for PostgreSQL](overview.md).
- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby** najdete v tématu [úrovně služeb](concepts-pricing-tiers.md).
- Informace o připojení ke službě najdete v tématu [knihovny připojení pro Azure Database for PostgreSQL](concepts-connection-libraries.md).
- Zobrazení a úprava parametrů serveru prostřednictvím [Azure Portal](howto-configure-server-parameters-using-portal.md) nebo rozhraní příkazového [řádku Azure](howto-configure-server-parameters-using-cli.md)

---
title: Koncepty serveru ve službě Azure Database for PostgreSQL
description: Tento článek obsahuje důležité informace a pokyny pro konfiguraci a správu databáze Azure pro servery PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: a5b6db9f4bfbe9a11119270e9236580b41fae8c7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540857"
---
# <a name="azure-database-for-postgresql-servers"></a>Servery Azure Database for PostgreSQL
Tento článek obsahuje důležité informace a pokyny pro pracovat se službou Azure Database for PostgreSQL servery.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Co je serveru Azure Database for PostgreSQL?
Serveru Azure Database for PostgreSQL je centrální administrativní bod pro více databází. Je to stejné konstrukci server PostgreSQL, která je pravděpodobně znáte místního prostředí. Konkrétně služba PostgreSQL je spravovaná, poskytuje záruky týkající se výkonu, poskytují přístup a funkce na úrovni serveru.

Azure Database for PostgreSQL server:

- Je vytvořen v rámci předplatného Azure.
- Je nadřazeným prostředkem pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner se silnou sémantikou životního cyklu – odstraníte server a odstraní databáze s omezením.
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro server a přístup k databázi 
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze: přihlášení, brána firewall, uživatelů, rolí, konfigurace atd.
- Je k dispozici ve více verzích. Další informace najdete v tématu [podporované verze databáze PostgreSQL](concepts-supported-versions.md).
- Je možné rozšířit uživateli. Další informace najdete v tématu [rozšíření PostgreSQL](concepts-extensions.md).

V rámci serveru Azure Database for PostgreSQL můžete vytvořit jednu nebo několik databází. Můžete se rozhodnout vytvořit jednu databázi na server pro využití všech prostředků nebo vytvořit několik databází, které budou prostředky sdílet. Ceny se strukturovaných jednotlivých serverů, na základě konfigurace cenová úroveň, virtuálních jader a úložiště (GB). Další informace najdete v tématu [cenové úrovně](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Jak se připojit a ověřit na serveru Azure Database for PostgreSQL?
Tyto prvky zajistit bezpečný přístup k databázi:

|||
|:--|:--|
| **Ověřování a autorizace** | Azure Database for PostgreSQL server podporuje nativní PostgreSQL ověřování. Můžete se připojit a ověřit na serveru s přihlašovací jméno správce serveru. |
| **Protokol** | Tato služba podporuje založenou na zprávách protokol používaný PostgreSQL. |
| **TCP/IP** | Protokol se podporuje přes TCP/IP a prostřednictvím soketů systému Unix domény. |
| **Brána firewall** | Pomáhá chránit vaše data, pravidlo brány firewall brání veškerému přístupu k vašemu serveru a jeho databázím, dokud neurčíte, které počítače mají oprávnění. Zobrazit [– Azure Database for PostgreSQL Server pravidla brány firewall](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Správa serveru
Správě Azure Database for PostgreSQL serverů s použitím [webu Azure portal](https://portal.azure.com) nebo [rozhraní příkazového řádku Azure](/cli/azure/postgres).

Při vytváření serveru, můžete nastavit přihlašovací údaje pro vaše uživatele s rolí správce. Uživatel s rolí správce je nejvyšší privilegovaným uživatelem, které máte na serveru. Patří do role azure_pg_admin. Tato role nemá oprávnění Úplné superuživatele. 

Atribut PostgreSQL superuživatele se přiřadí azure_superuser, který patří do spravované služby. Nemáte přístup k této roli.

Azure Database for PostgreSQL server má výchozí databáze: 
- **postgres** -vytvoří i výchozí databáze můžete připojit k jednou serveru se vytvoří.
- **azure_maintenance** – tato databáze slouží k oddělení procesy, které poskytují spravované služby z akce uživatele. Nemáte přístup k této databázi.
- **azure_sys** – databáze pro Query Store. Tato databáze není shromažďování dat, když Query Store je vypnuté; Toto je výchozí nastavení. Další informace najdete v tématu [Query Store přehled](concepts-query-store.md).


## <a name="server-parameters"></a>Parametry serveru
Parametry serveru PostgreSQL určit konfiguraci serveru. Ve službě Azure Database for PostgreSQL seznam parametrů lze prohlížet a upravovat pomocí webu Azure portal nebo rozhraní příkazového řádku Azure. 

Spravovaná služba pro Postgres, konfigurovatelné parametry ve službě Azure Database for PostgreSQL jsou podmnožinou parametry v místní instanci Postgres (Další informace o parametrech Postgres, najdete v článku [PostgreSQL dokumentaci](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Váš server Azure Database for PostgreSQL je povolená s výchozí hodnoty pro každý parametr při vytvoření. Některé parametry, které by vyžadovaly serveru restartujte nebo přístup superuživatele pro změny se projeví nelze nakonfigurovaná uživatelem.


## <a name="next-steps"></a>Další postup
- Přehled služby najdete v tématu [– Azure Database for postgresql – přehled](overview.md).
- Informace o konkrétní prostředek kvóty a omezení na základě vašich **úroveň služby**, naleznete v tématu [úrovně služeb](concepts-pricing-tiers.md).
- Informace o připojení ke službě naleznete v tématu [připojení knihoven pro službu Azure Database for PostgreSQL](concepts-connection-libraries.md).
- Zobrazit a upravit parametry serveru prostřednictvím [webu Azure portal](howto-configure-server-parameters-using-portal.md) nebo [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).

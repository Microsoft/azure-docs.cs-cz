---
title: Koncepty serveru v databázi Azure pro PostgreSQL
description: Tento článek obsahuje důležité informace a pokyny pro konfiguraci a správu databáze Azure pro servery PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/22/2018
ms.openlocfilehash: f877f6df51cd7aed29260331d27d5c96f0584afc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640009"
---
# <a name="azure-database-for-postgresql-servers"></a>Servery Azure Database for PostgreSQL
Tento článek obsahuje důležité informace a pokyny pro práci s databází Azure pro servery PostgreSQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Co je Azure databáze PostgreSQL serveru?
Databázi Azure pro PostgreSQL server je centrální administrativní bod pro více databází. Je stejné konstrukce serveru PostgreSQL, který může na světě místní být obeznámeni s. Konkrétně službu PostgreSQL je spravovaný, poskytuje záruku výkonu, zpřístupní přístup a funkce na úrovni serveru.

Databázi Azure pro PostgreSQL server:

- Je vytvořen v rámci předplatného Azure.
- Je nadřazený prostředek pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner s silné životnost sémantiku - odstranění serveru a odstraní databázích s omezením.
- Collocates prostředky v oblasti.
- Poskytuje koncového bodu připojení pro přístup k serveru a databází (. postgresql.database.azure.com).
- Poskytuje oboru pro zásady správy, které se vztahují k jeho databázím: přihlášení, brána firewall, uživatelů, rolí, konfigurace atd.
- Je k dispozici v několika verzích. Další informace najdete v tématu [podporované verze databáze PostgreSQL](concepts-supported-versions.md).
- Je rozšiřitelný uživatelé. Další informace najdete v tématu [PostgreSQL rozšíření](concepts-extensions.md).

V rámci Azure Database pro PostgreSQL server můžete vytvořit jeden nebo více databází. Můžete se rozhodnout vytvořit jednu databázi na server pro využití všech prostředků nebo vytvořit několik databází, které budou prostředky sdílet. Cenách je strukturovaných jednotlivých serverů, na základě konfigurace cenová úroveň, vCores a úložiště (GB). Další informace najdete v tématu [cenové úrovně](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Jak připojit a ověřit k databázi Azure pro PostgreSQL server?
Tyto prvky pomoct zajistit bezpečný přístup k vaší databázi:

|||
|:--|:--|
| **Ověřování a autorizace** | Azure databázi PostgreSQL serveru podporuje nativní PostgreSQL ověřování. Můžete se připojit a ověření serveru s přihlašovací jméno správce serveru. |
| **Protokol** | Služba podporuje protokol na základě zpráv používá PostgreSQL. |
| **TCP/IP** | Protokol je podporována přes TCP/IP a přes sockets Unix domény. |
| **Brána firewall** | Chrání vaše data, pravidlo brány firewall zabrání veškerý přístup k serveru a k jeho databázím, dokud nezadáte, které počítače mají oprávnění. V tématu [databáze Azure pro pravidla brány firewall serveru PostgreSQL](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Správa serveru
Databáze Azure pro servery PostgreSQL můžete spravovat pomocí [portál Azure](https://portal.azure.com) nebo [rozhraní příkazového řádku Azure](/cli/azure/postgres).

Při vytváření serveru, můžete nastavit přihlašovací údaje uživatele správce. Uživatel s oprávněními správce je nejvyšší uživatel oprávnění, které máte na serveru. Patří do role azure_pg_admin. Tato role nemá oprávnění Úplné superuživatel. 

Atribut superuživatel PostgreSQL je přiřazený k azure_superuser, který patří k spravované služby. Nemáte přístup k této roli.

Databázi PostgreSQL serveru služby Azure má dva výchozí databází: 
- **postgres** -výchozí databázi můžete připojit k jednou serveru se vytvoří.
- **azure_maintenance** – tato databáze slouží k oddělení procesů, které poskytují spravované služby z akce uživatele. Nemáte přístup k této databázi.


## <a name="server-parameters"></a>Parametry serveru
Parametry serveru PostgreSQL zjistit konfiguraci serveru. V databázi Azure pro PostgreSQL seznam parametrů lze zobrazit a upravit pomocí portálu Azure nebo Azure CLI. 

Konfigurovat parametry v databáze Azure pro PostgreSQL jako spravované služby pro Postgres, jsou podmnožinou parametrů v místní instanci Postgres (Další informace o parametrech Postgres najdete v tématu [PostgreSQL dokumentace](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Vaše databáze Azure pro PostgreSQL serveru je povolená s výchozí hodnoty pro každý parametr při vytváření. Některé parametry, které by vyžadovaly server restartovat nebo superuživatel přístup pro změny se projeví nelze konfigurovat uživatelem.


## <a name="next-steps"></a>Další postup
- Přehled služby najdete v tématu [databáze Azure pro přehled PostgreSQL](overview.md).
- Informace o konkrétní prostředek kvóty a omezení na základě vaší **vrstvy služby**, najdete v části [úrovních služeb](concepts-pricing-tiers.md).
- Informace o připojení ke službě najdete v tématu [knihovny připojení pro databázi Azure pro PostgreSQL](concepts-connection-libraries.md).
- Zobrazit a upravit parametry serveru prostřednictvím [portál Azure](howto-configure-server-parameters-using-portal.md) nebo [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).

---
title: Servery – databáze Azure pro MariaDB
description: Toto téma obsahuje důležité informace a pokyny pro práci se servery Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527788"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Koncepty serveru v Azure Database pro MariaDB
Tento článek obsahuje důležité informace a pokyny pro práci se servery Azure Database pro MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Co je databáze Azure pro server MariaDB?

Azure Database for MariaDB server je centrální bod pro správu pro více databází. Jedná se o stejnou konstrukci serveru MariaDB, kterou můžete znát v místním světě. Konkrétně azure databáze pro MariaDB služby se spravuje, poskytuje záruky výkonu a zpřístupňuje přístup a funkce na úrovni serveru.

Databáze Azure pro server MariaDB:

- Vytvoří se v rámci předplatného Azure.
- Je nadřazený prostředek pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner se silnou životnost sémantiku - odstranit server a odstraní obsažené databáze.
- Collocates prostředky v oblasti.
- Poskytuje koncový bod připojení pro přístup k serveru a databázi.
- Poskytuje obor zásady správy, které se vztahují k jeho databázím: přihlášení, brána firewall, uživatelé, role, konfigurace atd.
- Je k dispozici ve verzi 10.2 motoru MariaDB. Další informace naleznete v [tématu Podporovaná databáze Azure pro verze databáze MariaDB](./concepts-supported-versions.md).

V rámci serveru Azure Database for MariaDB můžete vytvořit jednu nebo několik databází. Můžete se rozhodnout vytvořit jednu databázi na server pro použití všech prostředků nebo vytvořit více databází pro sdílení prostředků. Ceny jsou strukturované pro server na základě konfigurace cenové úrovně, virtuálních jader a úložiště (GB). Další informace naleznete v tématu [Cenové úrovně](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Jak zabezpečit azure databázi pro server MariaDB?

Následující prvky pomáhají zajistit bezpečný přístup k databázi.

|||
| :--| :--|
| **Ověřování a autorizace** | Azure Database for MariaDB server podporuje nativní ověřování MySQL. Můžete se připojit a ověřit na server pomocí přihlášení správce serveru. |
| **Protokol** | Služba podporuje protokol založený na zprávě používaný MySQL. |
| **TCP/IP** | Protokol je podporován přes TCP/IP a přes unixové domény. |
| **Brána firewall** | Z důvodu ochrany dat zabrání pravidlo brány firewall veškerému přístupu k databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Viz [Azure Database for MariaDB Server firewall rules](./concepts-firewall-rules.md). |
| **SSL** | Služba podporuje vynucení připojení SSL mezi aplikacemi a databázovým serverem. Viz [Konfigurace připojení SSL ve vaší aplikaci pro bezpečné připojení k Azure Database for MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak mohu spravovat server?
Azure Database for MariaDB můžete spravovat pomocí portálu Azure nebo azure cli.

## <a name="next-steps"></a>Další kroky
- Přehled služby najdete v tématu [Azure Database for MariaDB Overview](./overview.md)
- Informace o konkrétních kvótách prostředků a omezeních na základě **vaší úrovně služeb**naleznete v [tématu Úrovně služeb](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->

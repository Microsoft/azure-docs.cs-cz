---
title: Koncepty serveru – Databáze Azure pro MySQL
description: Toto téma obsahuje důležité informace a pokyny pro práci se servery Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 300470b2e8fb10fda7cfc59517cef00c07bf0632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537002"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Koncepty serveru v Azure Database for MySQL

Tento článek obsahuje důležité informace a pokyny pro práci s Azure Database pro servery MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Co je databáze Azure pro mysql server?

Azure Database for MySQL server je centrální bod pro správu pro více databází. Jedná se o stejnou konstrukci serveru MySQL, kterou můžete znát v místním světě. Konkrétně služba Azure Database for MySQL je spravována, poskytuje záruky výkonu a poskytuje přístup a funkce na úrovni serveru.

Databáze Azure pro server MySQL:

- Vytvoří se v rámci předplatného Azure.
- Je nadřazený prostředek pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner se silnou životnost sémantiku - odstranit server a odstraní obsažené databáze.
- Collocates prostředky v oblasti.
- Poskytuje koncový bod připojení pro přístup k serveru a databázi.
- Poskytuje obor zásady správy, které se vztahují k jeho databázím: přihlášení, brána firewall, uživatelé, role, konfigurace atd.
- Je k dispozici ve více verzích. Další informace naleznete v [tématu Supported Azure Database for MySQL database versions](./concepts-supported-versions.md).

V rámci serveru Azure Database for MySQL můžete vytvořit jednu nebo několik databází. Můžete se rozhodnout vytvořit jednu databázi na server pro použití všech prostředků nebo vytvořit více databází pro sdílení prostředků. Ceny jsou strukturované pro server na základě konfigurace cenové úrovně, virtuálních jader a úložiště (GB). Další informace naleznete v tématu [Cenové úrovně](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Jak se připojím a ověřím k databázi Azure pro server MySQL?

Následující prvky pomáhají zajistit bezpečný přístup k databázi.

|     |     |
| :-- | :-- |
| **Ověřování a autorizace** | Azure Database for MySQL server podporuje nativní ověřování MySQL. Můžete se připojit a ověřit na server pomocí přihlášení správce serveru. |
| **Protokol** | Služba podporuje protokol založený na zprávě používaný MySQL. |
| **TCP/IP** | Protokol je podporován přes TCP/IP a přes unixové domény. |
| **Brána firewall** | Z důvodu ochrany dat zabrání pravidlo brány firewall veškerému přístupu k databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Viz [Pravidla brány firewall Azure Database for MySQL Server](./concepts-firewall-rules.md). |
| **SSL** | Služba podporuje vynucení připojení SSL mezi aplikacemi a databázovým serverem.  Podívejte se na téma [Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak mohu spravovat server?

Azure Database for MySQL můžete spravovat pomocí portálu Azure nebo azure cli.

## <a name="next-steps"></a>Další kroky

- Přehled služby najdete v tématu [Azure Database for MySQL Overview](./overview.md)
- Informace o konkrétních kvótách prostředků a omezeních na základě **vaší úrovně služeb**naleznete v [tématu Úrovně služeb](./concepts-service-tiers.md)
- Informace o připojení ke službě najdete v [tématu Knihovny připojení pro Azure Database for MySQL](./concepts-connection-libraries.md).

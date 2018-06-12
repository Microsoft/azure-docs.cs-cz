---
title: Koncepty serveru ve službě Azure Database pro databázi MySQL
description: Toto téma obsahuje důležité informace a pokyny pro práci s databází Azure pro servery, MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9d94f897546ea1e1190aab91e80eb9868224e5a7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265455"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Koncepty serveru ve službě Azure Database pro databázi MySQL
Tento článek obsahuje důležité informace a pokyny pro práci s databází Azure pro servery, MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Co je Azure databáze MySQL serveru?

Databázi Azure pro server databáze MySQL je centrální administrativní bod pro více databází. Je stejné konstrukce serveru MySQL, který může na světě místní být obeznámeni s. Konkrétně databáze Azure pro službu MySQL je spravovaný, poskytuje záruku výkonu a zveřejňuje přístup a funkce na úrovni serveru.

Databáze MySQL serveru Azure:

- Je vytvořen v rámci předplatného Azure.
- Je nadřazený prostředek pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner s silné životnost sémantiku - odstranění serveru a odstraní databázích s omezením.
- Collocates prostředky v oblasti.
- Poskytuje koncového bodu připojení pro server a přístup k databázi.
- Poskytuje oboru pro zásady správy, které se vztahují k jeho databázím: přihlášení, brána firewall, uživatelů, rolí, konfigurace atd.
- Je k dispozici v několika verzích. Další informace najdete v tématu [podporované databáze Azure pro verze databáze MySQL](./concepts-supported-versions.md).

V rámci serveru Azure Database for MySQL můžete vytvořit jednu nebo několik databází. Můžete se rozhodnout vytvořit jednu databázi na jeden server pro všechny prostředky nebo vytvořit více databází sdílelo prostředky. Cenách je strukturovaných jednotlivých serverů, na základě konfigurace cenová úroveň, vCores a úložiště (GB). Další informace najdete v tématu [cenové úrovně](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Jak připojit a ověření do Azure databáze MySQL serveru?

Tyto prvky pomoct zajistit bezpečný přístup k vaší databázi.
|||
| :-- | :-- |
| **Ověřování a autorizace** | Databáze MySQL serveru Azure podporuje nativní MySQL ověřování. Můžete se připojit a ověřit na serveru s přihlašovací jméno správce serveru. |
| **Protokol** | Služba podporuje protokol na základě zpráv používané MySQL. |
| **TCP/IP** | Protokol je podporován přes TCP/IP a přes sockets Unix domény. |
| **Brána firewall** | K ochraně dat, pravidlo brány firewall brání veškerý přístup k databázovému serveru, dokud nezadáte, které počítače mají oprávnění. V tématu [databáze Azure pro pravidla brány firewall serveru MySQL](./concepts-firewall-rules.md). |
| **SSL** | Služba podporuje vynucení připojení SSL mezi aplikací a databázový server.  Podívejte se na téma [Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak lze spravovat server?
Databáze Azure pro servery, MySQL můžete spravovat pomocí portálu Azure nebo Azure CLI.

## <a name="next-steps"></a>Další postup
- Přehled služby najdete v tématu [Azure databáze MySQL přehled](./overview.md)
- Informace o konkrétní prostředek kvóty a omezení na základě vaší **vrstvy služby**, najdete v části [úrovních služeb](./concepts-service-tiers.md)
- Informace o připojení ke službě najdete v tématu [knihovny připojení pro databázi Azure pro databázi MySQL](./concepts-connection-libraries.md).

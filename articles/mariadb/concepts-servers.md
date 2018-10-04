---
title: Koncepty serveru ve službě Azure Database pro MariaDB
description: Toto téma obsahuje důležité informace a pokyny pro práci se službou Azure Database pro MariaDB servery.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4fd2f28f52c45a0ec3128302b02ad5ba498c982b
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247767"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Koncepty serveru ve službě Azure Database pro MariaDB
Tento článek obsahuje důležité informace a pokyny pro práci se službou Azure Database pro MariaDB servery.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Co je Azure Database pro MariaDB server?

Azure Database pro MariaDB server je centrální administrativní bod pro více databází. Je stejný konstrukci server MariaDB, která je pravděpodobně znáte místního prostředí. Azure Database pro MariaDB službu konkrétně spravuje, poskytuje záruky týkající se výkonu a poskytuje přístup a funkce na úrovni serveru.

Azure Database pro MariaDB server:

- Je vytvořen v rámci předplatného Azure.
- Je nadřazeným prostředkem pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner se silnou sémantikou životního cyklu – odstraníte server a odstraní databáze s omezením.
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro server a přístup k databázi.
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze: přihlášení, brána firewall, uživatelů, rolí, konfigurace atd.
- Je k dispozici ve verzi modulu MariaDB 10.2. Další informace najdete v tématu [podporované – Azure Database pro MariaDB verze databáze](./concepts-supported-versions.md).

V rámci serveru Azure Database for MariaDB můžete vytvořit jednu nebo několik databází. Můžete se rozhodnout vytvořit jednu databázi na serveru použijte všechny prostředky nebo vytvořit více databází, které budou prostředky sdílet. Ceny se strukturovaných jednotlivých serverů, na základě konfigurace cenová úroveň, virtuálních jader a úložiště (GB). Další informace najdete v tématu [cenové úrovně](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Jak zabezpečit serveru Azure Database for MariaDB

Tyto prvky zajistit bezpečný přístup k vaší databázi.
|||
| :--| :--|
| **Ověřování a autorizace** | MariaDB server Azure Database for podporuje nativní MySQL ověřování. Můžete se připojit a ověřit na serveru s přihlašovací jméno správce serveru. |
| **Protokol** | Tato služba podporuje založenou na zprávách protokol používaný MySQL. |
| **TCP/IP** | Protokol se podporuje přes TCP/IP a prostřednictvím soketů systému Unix domény. |
| **Brána firewall** | Pomáhá chránit vaše data, pravidlo brány firewall brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Zobrazit [– Azure Database for pravidla brány firewall serveru MariaDB](./concepts-firewall-rules.md). |
| **SSL** | Tato služba podporuje vynucení připojení SSL mezi vaší aplikací a databázový server. Zobrazit [konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database pro MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak můžu spravovat server?
– Azure Database pro MariaDB servery můžete spravovat pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.

## <a name="next-steps"></a>Další postup
- Přehled služby najdete v tématu [– Azure Database pro MariaDB přehled](./overview.md)
- Informace o konkrétní prostředek kvóty a omezení na základě vašich **úroveň služby**, naleznete v tématu [úrovně služeb](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->

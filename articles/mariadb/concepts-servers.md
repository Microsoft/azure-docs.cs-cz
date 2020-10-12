---
title: Servery – Azure Database for MariaDB
description: V tomto tématu najdete informace a pokyny pro práci s Azure Database for MariaDB servery.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79527788"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Koncepty serveru v Azure Database for MariaDB
Tento článek popisuje informace a pokyny pro práci s Azure Database for MariaDB servery.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Co je server Azure Database for MariaDB?

Server Azure Database for MariaDB je centrálním bodem správy pro více databází. Je to stejná konstrukce MariaDB serveru, kterou můžete znát v místním světě. Konkrétně je spravovaná služba Azure Database for MariaDB, poskytuje záruky na výkon a zpřístupňuje přístup a funkce na úrovni serveru.

Server Azure Database for MariaDB:

- Vytvoří se v rámci předplatného Azure.
- Je nadřazeným prostředkem pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner se sémantikou silné životnosti – odstraní Server a odstraní databáze s omezením.
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro server a přístup k databázi.
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze: přihlašovací jméno, brána firewall, uživatelé, role, konfigurace atd.
- Je k dispozici v modulu MariaDB verze 10,2. Další informace najdete v tématu [podporované verze databáze Azure Database for MariaDB](./concepts-supported-versions.md).

V rámci serveru Azure Database for MariaDB můžete vytvořit jednu nebo několik databází. Můžete se rozhodnout, že vytvoříte izolovanou databázi na jeden server, abyste mohli použít všechny prostředky, nebo vytvořit více databází pro sdílení prostředků. Ceny jsou strukturované na server, a to na základě konfigurace cenové úrovně, virtuální jádra a úložiště (GB). Další informace najdete v tématu [cenové úrovně](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Návody zabezpečit Azure Database for MariaDB Server?

Následující prvky vám pomůžou zajistit bezpečný přístup k vaší databázi.

|||
| :--| :--|
| **Ověřování a autorizace** | Azure Database for MariaDB Server podporuje nativní ověřování MySQL. K serveru se můžete připojit a ověřit pomocí přihlašovacích údajů správce serveru. |
| **Protokol** | Služba podporuje protokol založený na zprávách, který používá MySQL. |
| **TCP/IP** | Protokol je podporován přes protokoly TCP/IP a přes rozhraní UNIX-Domain Sockets. |
| **Brána firewall** | Pro lepší ochranu dat pravidlo brány firewall zabraňuje všem přístupům k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Viz [pravidla brány firewall serveru Azure Database for MariaDB](./concepts-firewall-rules.md). |
| **SSL** | Služba podporuje vynucování připojení SSL mezi vašimi aplikacemi a vaším databázovým serverem. Pokud chcete bezpečně připojit k Azure Database for MariaDB, přečtěte si téma [Konfigurace připojení SSL ve vaší aplikaci](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Návody spravovat Server?
Azure Database for MariaDB servery můžete spravovat pomocí Azure Portal nebo rozhraní příkazového řádku Azure.

## <a name="next-steps"></a>Další kroky
- Přehled služby najdete v tématu [přehled Azure Database for MariaDB](./overview.md) .
- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby**najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md) .

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->

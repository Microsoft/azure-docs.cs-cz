---
title: Koncepty serveru – Azure Database for MySQL
description: V tomto tématu najdete informace a pokyny pro práci s Azure Database for MySQL servery.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9a2e2eb022d96af1437ea4189d11f5fa69339325
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769995"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Koncepty serveru v Azure Database for MySQL

Tento článek popisuje informace a pokyny pro práci s Azure Database for MySQL servery.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Co je server Azure Database for MySQL?

Server Azure Database for MySQL je centrálním bodem správy pro více databází. Je to stejná konstrukce serveru MySQL, kterou můžete znát v místním světě. Konkrétně je spravovaná služba Azure Database for MySQL, poskytuje záruky na výkon a zpřístupňuje přístup a funkce na úrovni serveru.

Server Azure Database for MySQL:

- Vytvoří se v rámci předplatného Azure.
- Je nadřazeným prostředkem pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner se sémantikou silné životnosti – odstraní Server a odstraní databáze s omezením.
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro server a přístup k databázi.
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze: přihlašovací jméno, brána firewall, uživatelé, role, konfigurace atd.
- Je k dispozici ve více verzích. Další informace najdete v tématu [podporované verze databáze Azure Database for MySQL](./concepts-supported-versions.md).

V rámci serveru Azure Database for MySQL můžete vytvořit jednu nebo několik databází. Můžete se rozhodnout, že vytvoříte izolovanou databázi na jeden server, abyste mohli použít všechny prostředky, nebo vytvořit více databází pro sdílení prostředků. Ceny jsou strukturované na server, a to na základě konfigurace cenové úrovně, virtuální jádra a úložiště (GB). Další informace najdete v tématu [cenové úrovně](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Návody se připojit a ověřit pro Azure Database for MySQL server?

Následující prvky vám pomůžou zajistit bezpečný přístup k vaší databázi.

|     |     |
| :-- | :-- |
| **Ověřování a autorizace** | Azure Database for MySQL server podporuje nativní ověřování MySQL. K serveru se můžete připojit a ověřit pomocí přihlašovacích údajů správce serveru. |
| **Protokol** | Služba podporuje protokol založený na zprávách, který používá MySQL. |
| **PROTOKOL TCP/IP** | Protokol je podporován přes protokoly TCP/IP a přes rozhraní UNIX-Domain Sockets. |
| **Brána firewall** | Pro lepší ochranu dat pravidlo brány firewall zabraňuje všem přístupům k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Viz [pravidla brány firewall serveru Azure Database for MySQL](./concepts-firewall-rules.md). |
| **ZABEZPEČENÍ** | Služba podporuje vynucování připojení SSL mezi vašimi aplikacemi a vaším databázovým serverem.  Podívejte se na téma [Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Návody spravovat Server?

Azure Database for MySQL servery můžete spravovat pomocí Azure Portal nebo rozhraní příkazového řádku Azure.

## <a name="next-steps"></a>Další kroky

- Přehled služby najdete v tématu [přehled Azure Database for MySQL](./overview.md) .
- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby**najdete v tématu [úrovně služeb](./concepts-service-tiers.md) .
- Informace o připojení ke službě najdete v tématu [knihovny připojení pro Azure Database for MySQL](./concepts-connection-libraries.md).

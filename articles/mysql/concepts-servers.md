---
title: Koncepty serveru ve službě Azure Database for MySQL
description: Toto téma obsahuje důležité informace a pokyny pro pracovat se službou Azure Database for MySQL servery.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0ddab6a982f54f0309e87d3b74a7f21c0bb67ced
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955509"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Koncepty serveru ve službě Azure Database for MySQL

Tento článek obsahuje důležité informace a pokyny pro pracovat se službou Azure Database for MySQL servery.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Co je Azure Database for MySQL server?

Azure Database for MySQL server je centrální administrativní bod pro více databází. Je stejný konstrukce server MySQL, kterou můžete znát do místního prostředí. Konkrétně služba Azure Database for MySQL je spravovaná, poskytuje záruky týkající se výkonu a poskytuje přístup a funkce na úrovni serveru.

Azure Database for MySQL server:

- Je vytvořen v rámci předplatného Azure.
- Je nadřazeným prostředkem pro databáze.
- Poskytuje obor názvů pro databáze.
- Je kontejner se silnou sémantikou životního cyklu – odstraníte server a odstraní databáze s omezením.
- Uspořádává prostředky v oblasti.
- Poskytuje koncový bod připojení pro server a přístup k databázi.
- Poskytuje obor pro zásady správy, které se vztahují na jeho databáze: přihlášení, brána firewall, uživatelů, rolí, konfigurace atd.
- Je k dispozici ve více verzích. Další informace najdete v tématu [podporované – Azure Database for MySQL database verze](./concepts-supported-versions.md).

V rámci serveru Azure Database for MySQL můžete vytvořit jednu nebo několik databází. Můžete se rozhodnout vytvořit jednu databázi na serveru použijte všechny prostředky nebo vytvořit více databází, které budou prostředky sdílet. Ceny se strukturovaných jednotlivých serverů, na základě konfigurace cenová úroveň, virtuálních jader a úložiště (GB). Další informace najdete v tématu [cenové úrovně](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Jak se připojit a ověřit na serveru Azure Database for MySQL?

Tyto prvky zajistit bezpečný přístup k vaší databázi.
|     |     |
| :-- | :-- |
| **Ověřování a autorizace** | Azure Database for MySQL server podporuje nativní MySQL ověřování. Můžete se připojit a ověřit na serveru s přihlašovací jméno správce serveru. |
| **Protokol** | Tato služba podporuje založenou na zprávách protokol používaný MySQL. |
| **TCP/IP** | Protokol se podporuje přes TCP/IP a prostřednictvím soketů systému Unix domény. |
| **Brána firewall** | Pomáhá chránit vaše data, pravidlo brány firewall brání veškerému přístupu k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Zobrazit [– Azure Database for MySQL Server firewall pravidla](./concepts-firewall-rules.md). |
| **SSL** | Tato služba podporuje vynucení připojení SSL mezi vaší aplikací a databázový server.  Podívejte se na téma [Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak můžu spravovat server?

Azure Database pro servery MySQL můžete spravovat pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.

## <a name="next-steps"></a>Další postup

- Přehled služby najdete v tématu [– Azure Database for MySQL – přehled](./overview.md)
- Informace o konkrétní prostředek kvóty a omezení na základě vašich **úroveň služby**, naleznete v tématu [úrovně služeb](./concepts-service-tiers.md)
- Informace o připojení ke službě naleznete v tématu [připojení knihoven pro službu Azure Database for MySQL](./concepts-connection-libraries.md).

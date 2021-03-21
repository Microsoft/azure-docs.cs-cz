---
title: Koncepty serveru – Azure Database for MySQL
description: V tomto tématu najdete informace a pokyny pro práci s Azure Database for MySQL servery.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed6d5d676fd2c6eefd3288b7609446eb61611ed6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517973"
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

V rámci serveru Azure Database for MySQL můžete vytvořit jednu nebo několik databází. Můžete se rozhodnout, že vytvoříte izolovanou databázi na jeden server, abyste mohli použít všechny prostředky, nebo vytvořit více databází pro sdílení prostředků. Ceny jsou strukturované na server, a to na základě konfigurace cenové úrovně, virtuální jádra a úložiště (GB). Další informace najdete v tématu [cenové úrovně](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Návody se připojit a ověřit pro Azure Database for MySQL server?

Následující prvky vám pomůžou zajistit bezpečný přístup k vaší databázi.

|     |     |
| :-- | :-- |
| **Ověřování a autorizace** | Azure Database for MySQL server podporuje nativní ověřování MySQL. K serveru se můžete připojit a ověřit pomocí přihlašovacích údajů správce serveru. |
| **Protokol** | Služba podporuje protokol založený na zprávách, který používá MySQL. |
| **TCP/IP** | Protokol je podporován přes protokoly TCP/IP a přes rozhraní UNIX-Domain Sockets. |
| **Brána firewall** | Pro lepší ochranu dat pravidlo brány firewall zabraňuje všem přístupům k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Viz [pravidla brány firewall serveru Azure Database for MySQL](./concepts-firewall-rules.md). |
| **SSL** | Služba podporuje vynucování připojení SSL mezi vašimi aplikacemi a vaším databázovým serverem.  Podívejte se na téma [Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mysql"></a>Zastavení/spuštění Azure Database for MySQL

Azure Database for MySQL vám umožní **zastavit** Server, pokud se nepoužívá, a **Spustit** Server při obnovení aktivity. To je v podstatě hotové, aby se ušetřily náklady na databázové servery a platily jenom za prostředek, který se používá. To je ještě důležitější pro úlohy vývoje a testování, a pokud server používáte jenom během dne. Po zastavení serveru budou všechna aktivní připojení vyhozena. Později, pokud chcete převést Server zpátky do režimu online, můžete použít [Azure Portal](how-to-stop-start-server.md) nebo [CLI](how-to-stop-start-server.md).

Pokud je server v **zastaveném** stavu, výpočetní služba se neúčtuje. Úložiště se ale nadále bude účtovat, protože úložiště serveru zůstává, aby se zajistilo, že datové soubory budou k dispozici, až se server znovu spustí.

> [!IMPORTANT]
> Když **zastavíte** Server, zůstane v tomto stavu po dobu dalších 7 dnů roztažení. Pokud ji během této doby **nespustíte** ručně, server bude automaticky spuštěn na konci 7 dnů. Pokud nepoužíváte server, můžete ho znovu **zastavit** .

Během tohoto časového serveru nelze na serveru provádět žádné operace správy. Aby bylo možné změnit nastavení konfigurace na serveru, bude nutné [spustit server](how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Omezení operace zastavení/spuštění
- Nepodporováno s konfigurací repliky pro čtení (zdroj a repliky).

## <a name="how-do-i-manage-a-server"></a>Návody spravovat Server?

Azure Database for MySQL servery můžete spravovat pomocí Azure Portal nebo rozhraní příkazového řádku Azure.

## <a name="next-steps"></a>Další kroky

- Přehled služby najdete v tématu [přehled Azure Database for MySQL](./overview.md) .
- Informace o konkrétních kvótách prostředků a omezeních založených na **cenové úrovni** najdete v tématu [cenové úrovně](./concepts-pricing-tiers.md) .
- Informace o připojení ke službě najdete v tématu [knihovny připojení pro Azure Database for MySQL](./concepts-connection-libraries.md).

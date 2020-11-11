---
title: Servery – Azure Database for MariaDB
description: V tomto tématu najdete informace a pokyny pro práci s Azure Database for MariaDB servery.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f519ac30468d197c14fcf53d386168ebde5cf8ac
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504352"
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

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Zastavení/spuštění Azure Database for MariaDB (Preview)
Azure Database for MariaDB vám umožní **zastavit** Server, pokud se nepoužívá, a **Spustit** Server při obnovení aktivity. To je v podstatě hotové, aby se ušetřily náklady na databázové servery a platily jenom za prostředek, který se používá. To je ještě důležitější pro úlohy vývoje a testování, a pokud server používáte jenom během dne. Po zastavení serveru budou všechna aktivní připojení vyhozena. Později, pokud chcete převést Server zpátky do režimu online, můžete použít [Azure Portal](../mysql/how-to-stop-start-server.md) nebo [CLI](../mysql/how-to-stop-start-server.md).

Pokud je server v **zastaveném** stavu, výpočetní služba se neúčtuje. Úložiště se ale nadále bude účtovat, protože úložiště serveru zůstává, aby se zajistilo, že datové soubory budou k dispozici, až se server znovu spustí.

> [!IMPORTANT]
> Když **zastavíte** Server, zůstane v tomto stavu po dobu dalších 7 dnů roztažení. Pokud ji během této doby **nespustíte** ručně, server bude automaticky spuštěn na konci 7 dnů. Pokud nepoužíváte server, můžete ho znovu **zastavit** .

Během tohoto časového serveru nelze na serveru provádět žádné operace správy. Aby bylo možné změnit nastavení konfigurace na serveru, bude nutné [spustit server](../mysql/how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Omezení operace zastavení/spuštění
- Nepodporováno s konfigurací repliky pro čtení (zdroj a repliky).

## <a name="how-do-i-manage-a-server"></a>Návody spravovat Server?
Azure Database for MariaDB servery můžete spravovat pomocí Azure Portal nebo rozhraní příkazového řádku Azure.

## <a name="next-steps"></a>Další kroky
- Přehled služby najdete v tématu [přehled Azure Database for MariaDB](./overview.md) .
- Informace o konkrétních kvótách prostředků a omezeních založených na vaší **úrovni služby** najdete v tématu [úrovně služeb](./concepts-pricing-tiers.md) .

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->

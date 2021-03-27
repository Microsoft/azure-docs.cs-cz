---
title: Koncepty serveru – Azure Database for MySQL
description: V tomto tématu najdete informace a pokyny pro práci s Azure Database for MySQL servery.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: cb8394de49c2c5daeae156a9316466928eded148
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628471"
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

| Koncept zabezpečení | Description     |
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

Můžete spravovat vytváření, odstraňování, konfiguraci parametrů serveru (My. CNF), škálování, sítě, zabezpečení, vysokou dostupnost, zálohování & obnovení a monitorování serverů Azure Database for MySQL pomocí Azure Portal nebo rozhraní příkazového řádku Azure CLI. Kromě toho jsou k dispozici následující uložené procedury v Azure Database for MySQL k provádění určitých úloh správy databáze vyžadovaných jako oprávnění SUPERUŽIVATELE není na serveru podporováno.

|**Název uložené procedury**|**Vstupní parametry**|**Výstupní parametry**|**Poznámka k použití**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|–|Ekvivalent [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) příkazu Ukončí připojení přidružené k poskytnutému processlist_id po ukončení všech příkazů, které připojení provádí.|
|*mysql.az_kill_query*|processlist_id|–|Ekvivalent [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) příkazu Ukončí příkaz, který připojení právě provádí. Ponechá připojení aktivní.|
|*mysql.az_load_timezone*|N/A|N/A|Načte [tabulky časového pásma](howto-server-parameters.md#working-with-the-time-zone-parameter) , které umožňují `time_zone` nastavit parametr na pojmenované hodnoty (např. "US/Tichomoří").|

## <a name="next-steps"></a>Další kroky

- Přehled služby najdete v tématu [přehled Azure Database for MySQL](./overview.md) .
- Informace o konkrétních kvótách prostředků a omezeních založených na **cenové úrovni** najdete v tématu [cenové úrovně](./concepts-pricing-tiers.md) .
- Informace o připojení ke službě najdete v tématu [knihovny připojení pro Azure Database for MySQL](./concepts-connection-libraries.md).

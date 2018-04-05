---
title: Postup konfigurace serveru parametry v databáze Azure pro databázi MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MySQL v Azure Database pro databázi MySQL pomocí portálu Azure.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6865663bebc84df288f4c7e2564ddb4870667c6f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Postup konfigurace serveru parametry v Azure Database pro databázi MySQL pomocí portálu Azure

Azure databáze pro databázi MySQL podporuje konfiguraci některé parametry serveru. Tento článek popisuje, jak nakonfigurovat tyto parametry pomocí portálu Azure. Ne všechny parametry serveru můžete upravit. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Přejděte na parametry serveru, na portálu Azure
1. Přihlaste se k portálu Azure a potom vyhledejte vaší databázi Azure pro server databáze MySQL.
2. V části **nastavení** klikněte na tlačítko **parametry serveru** chcete otevřít stránku parametry Server pro databázi Azure pro databázi MySQL.
![Stránka parametry server portálu Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Najděte všechna nastavení, které je potřeba upravit. Zkontrolujte **popis** sloupec pochopit účel a povolené hodnoty. 
![Zobrazení výčtu rozevírací dolů](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klikněte na tlačítko **Uložit** uložte provedené změny.
![Uložit nebo zahodit změny](./media/howto-server-parameters/4-save_parameters.png)
5. Pokud jste uložili nové hodnoty pro parametry, můžete vždy obnovit vše zpět na výchozí hodnoty výběrem **resetovat všechny výchozí**.
![Všechny resetovat na výchozí](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>Seznam parametrů konfigurovat server

Seznam parametrů podporovaných serveru stále roste. Na kartě Parametry server na portálu Azure a získat definici konfigurace parametry serveru, na základě požadavků vaší aplikace. 

## <a name="nonconfigurable-server-parameters"></a>Parametry Nonconfigurable serveru
InnoDB fondu vyrovnávací paměti a maximální počet připojení nejsou konfigurovatelné a vázanou na vaše [cenová úroveň](concepts-service-tiers.md). 

|**Cenová úroveň**| **Výpočetní generování**|**vCore(s)**|**Fond InnoDB vyrovnávací paměti (MB)**| **Maximální počet připojení**|
|---|---|---|---|--|
|Basic| Gen 4| 1| 1024| 50|
|Basic| Gen 4| 2| 2560| 100|
|Basic| Gen 5| 1| 1024| 50|
|Basic| Gen 5| 2| 2560| 100|
|Obecné použití| Gen 4| 2| 3584| 300|
|Obecné použití| Gen 4| 4| 7680| 625|
|Obecné použití| Gen 4| 8| 15360| 1250|
|Obecné použití| Gen 4| 16| 31232| 2500|
|Obecné použití| Gen 4| 32| 62976| 5000|
|Obecné použití| Gen 5| 2| 3584| 300|
|Obecné použití| Gen 5| 4| 7680| 625|
|Obecné použití| Gen 5| 8| 15360| 1250|
|Obecné použití| Gen 5| 16| 31232| 2500|
|Obecné použití| Gen 5| 32| 62976| 5000|
|Paměťově optimalizované| Gen 5| 2| 7168| 600|
|Paměťově optimalizované| Gen 5| 4| 15360| 1250|
|Paměťově optimalizované| Gen 5| 8| 30720| 2500|
|Paměťově optimalizované| Gen 5| 16| 62464| 5000|

Tyto další server parametry nejsou konfigurovatelné v systému:

|**Parameter**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table v základní vrstvě|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Další parametry serveru, které zde nejsou uvedeny jsou nastaveny na MySQL out-of-box výchozí hodnoty pro verze [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) a [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>Další postup
- [Knihovny připojení pro databázi Azure pro databázi MySQL](concepts-connection-libraries.md).

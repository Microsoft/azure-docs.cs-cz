---
title: Konfigurace parametrů serveru-Azure Portal-Azure Database for MariaDB
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MariaDB v Azure Database for MariaDB pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: ba091d05aa243fab08138c96827d2f657d9755de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251502"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Postup konfigurace parametrů serveru v Azure Database for MariaDB pomocí Azure Portal

Azure Database for MariaDB podporuje konfiguraci některých parametrů serveru. Tento článek popisuje, jak nakonfigurovat tyto parametry s využitím webu Azure portal. Ne všechny parametry serveru je možné upravit.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Přejděte do parametrů serveru na webu Azure portal

1. Přihlaste se k Azure Portal a potom vyhledejte server Azure Database for MariaDB.
2. V části **Nastavení** klikněte na **parametry serveru** a otevřete stránku parametry serveru pro Azure Database for MariaDB Server.
Stránka parametrů serveru ![Azure Portal](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Vyhledejte nastavení, které je potřeba upravit. Přečtěte si sloupec **Description (popis** ), abyste pochopili účel a povolené hodnoty.
](./media/howto-server-parameters/3-toggle_parameter.png) rozevírací seznam ![výčtu
4. Kliknutím na **Uložit** uložte změny.
![Uložit nebo zahodit změny](./media/howto-server-parameters/4-save_parameters.png)
5. Pokud jste uložili nové hodnoty pro parametry, můžete kdykoli vrátit zpět všechny výchozí hodnoty výběrem možnosti **Obnovit vše na výchozí**.
![obnovit výchozí](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Seznam konfigurovatelných server parametrů

Seznam parametrů podporovaný server s neustále roste. Na kartě parametrů serveru na webu Azure portal k získání definice a konfigurace parametrů serveru na základě požadavků aplikace.

## <a name="non-configurable-server-parameters"></a>Parametry Nekonfigurovatelné serveru

InnoDB fondu vyrovnávací paměti a maximální počet připojení se nedají konfigurovat a připojovat se k vaší [cenové úrovni](concepts-pricing-tiers.md).

|**Cenová úroveň**| **vCore (celkem)**|**Fond vyrovnávacích pamětí InnoDB (MB)**|
|---|---|---|
|Basic| 1| 1024|
|Basic| 2| 2560|
|Obecné účely| 2| 3584|
|Obecné účely| 4| 7680|
|Obecné účely| 8| 15360|
|Obecné účely| 16| 31232|
|Obecné účely| 32| 62976|
|Obecné účely| 64| 125952|
|Optimalizováno pro paměť| 2| 7168|
|Optimalizováno pro paměť| 4| 15360|
|Optimalizováno pro paměť| 8| 30720|
|Optimalizováno pro paměť| 16| 62464|
|Optimalizováno pro paměť| 32| 125952|

Tyto další server parametry nejsou konfigurovatelné v systému:

|**Ukazatele**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table v úrovni Basic|VYPNOUT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Další parametry serveru, které zde nejsou uvedeny, jsou nastaveny na jejich MariaDB výchozí výchozí hodnoty pro [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časové pásmo

Tabulky časových pásem na vašem serveru se dají naplnit voláním `az_load_timezone` uložené procedury z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud používáte příkaz `az_load_timezone` z MySQL Workbench, možná budete muset nejdřív vypnout režim bezpečné aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Je nutné restartovat server, aby bylo zajištěno, že tabulky časových pásem budou správně vyplněny. K restartování serveru použijte [Azure Portal](howto-restart-server-portal.md) nebo [CLI](howto-restart-server-cli.md).
Chcete-li zobrazit hodnoty dostupné časové pásmo, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení na globální úrovni časové pásmo

Časové pásmo globální úrovně lze nastavit na stránce **parametry serveru** v Azure Portal. Níže Nastaví globální časovém pásmu na hodnotu "US / Tichomoří".

![Parametr časového pásma set](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovni relace

Časové pásmo úrovně relace můžete nastavit spuštěním příkazu `SET time_zone` z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo na časové pásmo **USA/Tichomoří** .

```sql
SET time_zone = 'US/Pacific';
```

Informace o [funkcích data a času](https://mariadb.com/kb/en/library/convert_tz/)najdete v dokumentaci k MariaDB.

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->

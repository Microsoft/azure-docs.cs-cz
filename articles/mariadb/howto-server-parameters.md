---
title: Konfigurace parametrů serveru – portál Azure – databáze Azure pro MariaDB
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MariaDB v Azure Database for MariaDB pomocí portálu Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 9d057a4be02d8d93d3ef02ee3153baebe9146ff1
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632712"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Jak nakonfigurovat parametry serveru v Azure Database pro MariaDB pomocí portálu Azure

Azure Database for MariaDB podporuje konfiguraci některých parametrů serveru. Tento článek popisuje, jak nakonfigurovat tyto parametry pomocí portálu Azure. Nelze upravit všechny parametry serveru.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Přechod na parametry serveru na webu Azure Portal

1. Přihlaste se k portálu Azure a vyhledejte azure databázi pro server MariaDB.
2. V části **NASTAVENÍ** klikněte na **parametry serveru** a otevřete stránku parametrů serveru Azure Database for MariaDB.
![Stránka parametry serveru portálu Azure](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Vyhledejte všechna nastavení, která je třeba upravit. Zkontrolujte sloupec **Popis,** abyste pochopili účel a povolené hodnoty.
![Rozevírací vypočet](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknutím na **Uložit** uložte změny.
![Uložit nebo zahodit změny](./media/howto-server-parameters/4-save_parameters.png)
5. Pokud jste uložili nové hodnoty parametrů, můžete vždy vrátit vše zpět na výchozí hodnoty výběrem **možnosti Obnovit vše na výchozí**.
![Obnovit výchozí hodnotu](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Seznam konfigurovatelných parametrů serveru

Seznam podporovaných parametrů serveru neustále roste. Pomocí karty parametry serveru na webu Azure Portal získáte definici a nakonfigurujete parametry serveru na základě požadavků vaší aplikace.

## <a name="non-configurable-server-parameters"></a>Nekonfigurovatelné parametry serveru

Fond vyrovnávacích pamětí InnoDB a Maximální počet připojení nelze konfigurovat a jsou vázány na [cenovou úroveň](concepts-pricing-tiers.md).

|**Cenová úroveň**| **virtuální jádra (hlavní)**|**Fond vyrovnávací paměti InnoDB (MB)**|
|---|---|---|
|Základní| 1| 1024|
|Základní| 2| 2560|
|Pro obecné účely| 2| 3584|
|Pro obecné účely| 4| 7680|
|Pro obecné účely| 8| 15360|
|Pro obecné účely| 16| 31232|
|Pro obecné účely| 32| 62976|
|Pro obecné účely| 64| 125952|
|Optimalizováno pro paměť| 2| 7168|
|Optimalizováno pro paměť| 4| 15360|
|Optimalizováno pro paměť| 8| 30720|
|Optimalizováno pro paměť| 16| 62464|
|Optimalizováno pro paměť| 32| 125952|

Tyto další parametry serveru nelze v systému konfigurovat:

|**Parametr**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table v základní úrovni|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 MB|

Ostatní parametry serveru, které zde nejsou uvedeny, jsou nastaveny na výchozí hodnoty MariaDB pro [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Vyplnění tabulek časových pásem

Tabulky časových pásem na serveru mohou `mysql.az_load_timezone` být naplněny voláním uložené procedury z nástroje, jako je příkazový řádek MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud používáte `mysql.az_load_timezone` příkaz z MySQL Workbench, možná budete muset nejprve vypnout režim nouzové aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`aplikace .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Měli byste restartovat server, abyste zajistili, že tabulky časových pásem jsou správně naplněny. Chcete-li restartovat server, použijte [portál Azure](howto-restart-server-portal.md) nebo [cli](howto-restart-server-cli.md).
Chcete-li zobrazit dostupné hodnoty časových pásem, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení časového pásma globální úrovně

Časové pásmo globální úrovně lze nastavit na stránce **Parametry serveru** na webu Azure Portal. Níže nastaví globální časové pásmo na hodnotu "US/Pacific".

![Nastavit parametr časového pásma](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovně relace

Časové pásmo úrovně relace lze `SET time_zone` nastavit spuštěním příkazu z nástroje, jako je příkazový řádek MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo na **us/tichomořské** časové pásmo.

```sql
SET time_zone = 'US/Pacific';
```

Funkce [data a času](https://mariadb.com/kb/en/library/convert_tz/)naleznete v dokumentaci MariaDB .

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->

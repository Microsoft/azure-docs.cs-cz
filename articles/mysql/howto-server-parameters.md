---
title: Konfigurace parametrů serveru – portál Azure – databáze Azure pro MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MySQL v Azure Database for MySQL pomocí portálu Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: bd0a867cce9b2a9ad793b491b9042034ef5810f5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605156"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Jak nakonfigurovat parametry serveru v Azure Database for MySQL pomocí portálu Azure

Azure Database for MySQL podporuje konfiguraci některých parametrů serveru. Tento článek popisuje, jak nakonfigurovat tyto parametry pomocí portálu Azure. Nelze upravit všechny parametry serveru.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Přechod na parametry serveru na webu Azure Portal

1. Přihlaste se k portálu Azure portal a vyhledejte azure databázi pro server MySQL.
2. V části **NASTAVENÍ** klikněte na **Parametry serveru** a otevřete stránku parametrů serveru Azure Database for MySQL.
![Stránka parametry serveru portálu Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Vyhledejte všechna nastavení, která je třeba upravit. Zkontrolujte sloupec **Popis,** abyste pochopili účel a povolené hodnoty.
![Rozevírací vypočet](./media/howto-server-parameters/3-toggle_parameter.png)
4. Kliknutím na **Uložit** uložte změny.
![Uložit nebo zahodit změny](./media/howto-server-parameters/4-save_parameters.png)
5. Pokud jste uložili nové hodnoty parametrů, můžete vždy vrátit vše zpět na výchozí hodnoty výběrem **možnosti Obnovit vše na výchozí**.
![Obnovit výchozí hodnotu](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Seznam konfigurovatelných parametrů serveru

Seznam podporovaných parametrů serveru neustále roste. Pomocí karty parametry serveru na webu Azure Portal získáte definici a nakonfigurujete parametry serveru na základě požadavků vaší aplikace.

## <a name="non-configurable-server-parameters"></a>Nekonfigurovatelné parametry serveru

Velikost fondu vyrovnávací paměti InnoDB není konfigurovatelná a je vázána na [cenovou úroveň](concepts-service-tiers.md).

|**Cenová úroveň**|**virtuální jádra (hlavní)**|**Velikost fondu vyrovnávací paměti <br>InnoDB v MB (servery podporující až 4 TB úložiště)**| **Velikost fondu vyrovnávací paměti <br>InnoDB v MB (servery podporující úložiště s kapacitou až 16 TB)**|
|:---|---:|---:|---:|
|Základní| 1| 832| |
|Základní| 2| 2560| |
|Pro obecné účely| 2| 3584| 7168|
|Pro obecné účely| 4| 7680| 15360|
|Pro obecné účely| 8| 15360| 30720|
|Pro obecné účely| 16| 31232| 62464|
|Pro obecné účely| 32| 62976| 125952|
|Pro obecné účely| 64| 125952| 251904|
|Optimalizováno pro paměť| 2| 7168| 14336|
|Optimalizováno pro paměť| 4| 15360| 30720|
|Optimalizováno pro paměť| 8| 30720| 61440|
|Optimalizováno pro paměť| 16| 62464| 124928|
|Optimalizováno pro paměť| 32| 125952| 251904|

Tyto další parametry serveru nelze v systému konfigurovat:

|**Parametr**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table v základní úrovni|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Ostatní parametry serveru, které zde nejsou uvedeny, jsou nastaveny na výchozí hodnoty MySQL pro verze [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) a [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

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

Funkce [data a času](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)naleznete v dokumentaci mySQL .

## <a name="next-steps"></a>Další kroky

- [Knihovny připojení pro Azure Database for MySQL](concepts-connection-libraries.md).

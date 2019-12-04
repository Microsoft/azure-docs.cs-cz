---
title: Konfigurace parametrů serveru-Azure Portal-Azure Database for MariaDB
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MariaDB v Azure Database for MariaDB pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3477820cb20d856c2e979cdfbe5528113bf4b562
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769400"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Postup konfigurace parametrů serveru v Azure Database for MariaDB pomocí Azure Portal

Azure Database for MariaDB podporuje konfiguraci některých parametrů serveru. Tento článek popisuje, jak nakonfigurovat tyto parametry pomocí Azure Portal. Ne všechny parametry serveru lze upravit.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Přejít na parametry serveru na Azure Portal

1. Přihlaste se k Azure Portal a potom vyhledejte server Azure Database for MariaDB.
2. V části **Nastavení** klikněte na **parametry serveru** a otevřete stránku parametry serveru pro Azure Database for MariaDB Server.
Stránka parametrů serveru ![Azure Portal](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Vyhledejte všechna nastavení, která potřebujete upravit. Přečtěte si sloupec **Description (popis** ), abyste pochopili účel a povolené hodnoty.
](./media/howto-server-parameters/3-toggle_parameter.png) rozevírací seznam ![výčtu
4. Kliknutím na **Uložit** uložte změny.
![Uložit nebo zahodit změny](./media/howto-server-parameters/4-save_parameters.png)
5. Pokud jste uložili nové hodnoty pro parametry, můžete kdykoli vrátit zpět všechny výchozí hodnoty výběrem možnosti **Obnovit vše na výchozí**.
![obnovit výchozí](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Seznam konfigurovatelných parametrů serveru

Seznam podporovaných parametrů serveru se neustále zvětšuje. Použijte kartu parametry serveru v Azure Portal k získání definice a konfiguraci parametrů serveru podle požadavků vaší aplikace.

## <a name="non-configurable-server-parameters"></a>Nekonfigurovatelné parametry serveru

InnoDB fondu vyrovnávací paměti a maximální počet připojení se nedají konfigurovat a připojovat se k vaší [cenové úrovni](concepts-pricing-tiers.md).

|**Cenová úroveň**| **vCore (celkem)**|**Fond vyrovnávacích pamětí InnoDB (MB)**| **Maximální počet připojení**|
|---|---|---|---|
|Úroveň Basic| 1\. místo| 1024| 50|
|Úroveň Basic| 2| 2560| 100|
|Obecné použití| 2| 3584| 300|
|Obecné použití| 4| 7680| 625|
|Obecné použití| 8| 15360| 1250|
|Obecné použití| 16| 31232| 2500|
|Obecné použití| 32| 62976| 5000|
|Obecné použití| 64| 125952| 10000|
|Paměťově optimalizované| 2| 7168| 600|
|Paměťově optimalizované| 4| 15360| 1250|
|Paměťově optimalizované| 8| 30720| 2500|
|Paměťově optimalizované| 16| 62464| 5000|
|Paměťově optimalizované| 32| 125952| 10000|

Tyto další parametry serveru není možné v systému konfigurovat:

|**Ukazatele**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table na úrovni Basic|OFF|
|innodb_flush_log_at_trx_commit|1\. místo|
|sync_binlog|1\. místo|
|innodb_log_file_size|512MB|

Další parametry serveru, které zde nejsou uvedeny, jsou nastaveny na jejich MariaDB výchozí výchozí hodnoty pro [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časových pásem

Tabulky časových pásem na vašem serveru se dají naplnit voláním `az_load_timezone` uložené procedury z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud používáte příkaz `az_load_timezone` z MySQL Workbench, možná budete muset nejdřív vypnout režim bezpečné aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Chcete-li zobrazit dostupné hodnoty časového pásma, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení časového pásma globální úrovně

Časové pásmo globální úrovně lze nastavit na stránce **parametry serveru** v Azure Portal. Následující nastavení nastaví globální časové pásmo na hodnotu "US/Tichomoří".

![Nastavit parametr časového pásma](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovně relace

Časové pásmo úrovně relace můžete nastavit spuštěním příkazu `SET time_zone` z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo na časové pásmo **USA/Tichomoří** .

```sql
SET time_zone = 'US/Pacific';
```

Informace o [funkcích data a času](https://mariadb.com/kb/en/library/convert_tz/)najdete v dokumentaci k MariaDB.

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->

---
title: Konfigurace parametrů serveru-Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MySQL v Azure Database for MySQL pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f803b7cccf3520c309e6b33d99b5565cfc4fdd01
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764912"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Postup konfigurace parametrů serveru v Azure Database for MySQL pomocí Azure Portal

Azure Database for MySQL podporuje konfiguraci některých parametrů serveru. Tento článek popisuje, jak nakonfigurovat tyto parametry pomocí Azure Portal. Ne všechny parametry serveru lze upravit.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Přejít na parametry serveru na Azure Portal

1. Přihlaste se k Azure Portal a potom vyhledejte server Azure Database for MySQL.
2. V části **Nastavení** klikněte na **parametry serveru** a otevřete stránku parametry serveru pro Azure Database for MySQL server.
Stránka parametrů serveru ![Azure Portal](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Vyhledejte všechna nastavení, která potřebujete upravit. Přečtěte si sloupec **Description (popis** ), abyste pochopili účel a povolené hodnoty.
](./media/howto-server-parameters/3-toggle_parameter.png) rozevírací seznam ![výčtu
4. Kliknutím na **Uložit** uložte změny.
![Uložit nebo zahodit změny](./media/howto-server-parameters/4-save_parameters.png)
5. Pokud jste uložili nové hodnoty pro parametry, můžete kdykoli vrátit zpět všechny výchozí hodnoty výběrem možnosti **Obnovit vše na výchozí**.
![obnovit výchozí](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Seznam konfigurovatelných parametrů serveru

Seznam podporovaných parametrů serveru se neustále zvětšuje. Použijte kartu parametry serveru v Azure Portal k získání definice a konfiguraci parametrů serveru podle požadavků vaší aplikace.

## <a name="non-configurable-server-parameters"></a>Nekonfigurovatelné parametry serveru

Velikost fondu vyrovnávací paměti InnoDB není konfigurovatelná a je vázaná na vaši [cenovou úroveň](concepts-service-tiers.md).

|**Cenová úroveň**|**vCore (celkem)**|**Velikost fondu vyrovnávací paměti InnoDB v MB <br>(servery podporující až 4 TB úložiště)**| **Velikost fondu vyrovnávací paměti InnoDB v MB <br>(servery podporující až 16 TB úložiště)**|
|:---|---:|---:|---:|
|Úroveň Basic| 1\. místo| 832| |
|Úroveň Basic| 2| 2560| |
|Obecné použití| 2| 3584| 7168|
|Obecné použití| 4| 7680| 15360|
|Obecné použití| 8| 15360| 30720|
|Obecné použití| 16| 31232| 62464|
|Obecné použití| 32| 62976| 125952|
|Obecné použití| 64| 125952| 251904|
|Paměťově optimalizované| 2| 7168| 14336|
|Paměťově optimalizované| 4| 15360| 30720|
|Paměťově optimalizované| 8| 30720| 61440|
|Paměťově optimalizované| 16| 62464| 124928|
|Paměťově optimalizované| 32| 125952| 251904|

Tyto další parametry serveru není možné v systému konfigurovat:

|**Ukazatele**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table na úrovni Basic|OFF|
|innodb_flush_log_at_trx_commit|1\. místo|
|sync_binlog|1\. místo|
|innodb_log_file_size|512MB|

Další parametry serveru, které zde nejsou uvedeny, jsou nastaveny na výchozí hodnoty, které jsou ve výchozím nastavení pro databáze MySQL pro verze [5,7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) a [5,6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

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

Informace o [funkcích pro datum a čas](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)najdete v dokumentaci k MySQL.

## <a name="next-steps"></a>Další kroky

- [Knihovny připojení pro Azure Database for MySQL](concepts-connection-libraries.md).

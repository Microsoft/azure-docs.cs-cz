---
title: Konfigurace parametrů serveru-Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MySQL ve službě Azure Database for MySQL pomocí webu Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 8ec6f32d7db0161cef00330aa38601ba9bdb309d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893140"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Postup konfigurace parametrů serveru ve službě Azure Database for MySQL pomocí webu Azure portal

Azure Database for MySQL podporuje konfiguraci některé parametry serveru. Tento článek popisuje, jak nakonfigurovat tyto parametry s využitím webu Azure portal. Ne všechny parametry serveru je možné upravit.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Přejděte do parametrů serveru na webu Azure portal

1. Přihlaste se k webu Azure portal a potom najděte váš server Azure Database for MySQL.
2. V části **nastavení** klikněte na tlačítko **parametry serveru** otevřete stránku parametry serveru pro Azure Database for MySQL server.
![Stránka parametry Azure portálový server](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Vyhledejte nastavení, které je potřeba upravit. Zkontrolujte **popis** sloupce, které vám pomohou pochopit účel a povolených hodnot.
![Zobrazení výčtu rozevírací seznam](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klikněte na tlačítko **Uložit** uložte provedené změny.
![Uložit nebo zahodit změny](./media/howto-server-parameters/4-save_parameters.png)
5. Pokud jste uložili nové hodnoty pro parametry, můžete se vždycky vrátit vše zpět na výchozí hodnoty tak, že vyberete **Resetovat vše na výchozí**.
![Resetovat vše na výchozí](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Seznam konfigurovatelných server parametrů

Seznam parametrů podporovaný server s neustále roste. Na kartě parametrů serveru na webu Azure portal k získání definice a konfigurace parametrů serveru na základě požadavků aplikace.

## <a name="non-configurable-server-parameters"></a>Parametry Nekonfigurovatelné serveru

Velikost fondu vyrovnávací paměti InnoDB není konfigurovatelná a je vázaná na vaši [cenovou úroveň](concepts-service-tiers.md).

|**Cenová úroveň**|**počet virtuálních jader:**|**Velikost fondu vyrovnávací paměti InnoDB v MB <br>(servery podporující až 4 TB úložiště)**| **Velikost fondu vyrovnávací paměti InnoDB v MB <br>(servery podporující až 16 TB úložiště)**|
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

Tyto další server parametry nejsou konfigurovatelné v systému:

|**Parametr**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table v úrovni Basic|OFF|
|innodb_flush_log_at_trx_commit|1\. místo|
|sync_binlog|1\. místo|
|innodb_log_file_size|512MB|

Další parametry serveru, které zde nejsou uvedeny jsou nastaveny na MySQL out-of-box výchozí hodnoty pro verze [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) a [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časové pásmo

Časové pásmo tabulky na vašem serveru je možné naplnit s voláním `az_load_timezone` uloženou proceduru z některého nástroje, například na příkazovém řádku MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud používáte `az_load_timezone` příkaz z aplikace MySQL Workbench, budete muset nejprve vypnout režim bezpečného aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`.

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

Lze nastavit na globální úrovni časové pásmo **parametry serveru** stránky na webu Azure Portal. Níže Nastaví globální časovém pásmu na hodnotu "US / Tichomoří".

![Parametr časového pásma set](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovni relace

Relace spuštěním můžete nastavit úroveň časového pásma `SET time_zone` z některého nástroje, například na příkazovém řádku MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo **US / Tichomoří** časové pásmo.

```sql
SET time_zone = 'US/Pacific';
```

Naleznete v dokumentaci MySQL pro [funkce data a času](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Další kroky

- [Připojení knihoven pro službu Azure Database for MySQL](concepts-connection-libraries.md).

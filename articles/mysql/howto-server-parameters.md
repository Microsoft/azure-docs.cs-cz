---
title: Postup konfigurace parametrů serveru ve službě Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MySQL ve službě Azure Database for MySQL pomocí webu Azure portal.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: 686db4dc15eb42abead3eef6798ed2b83e248811
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136447"
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

InnoDB vyrovnávací paměti fondu a maximální počet připojení nejsou konfigurovatelné a vázané na vaše [cenovou úroveň](concepts-service-tiers.md).

|**Cenová úroveň**| **Generace výpočetních funkcí**|**počet virtuálních jader:**|**Fond vyrovnávacích pamětí InnoDB (MB)**| **Maximální počet připojení**|
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

|**Parametr**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table v úrovni Basic|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
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

## <a name="next-steps"></a>Další postup

- [Připojení knihoven pro službu Azure Database for MySQL](concepts-connection-libraries.md).

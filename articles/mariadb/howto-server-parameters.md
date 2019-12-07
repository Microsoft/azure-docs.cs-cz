---
title: Konfigurace parametrů serveru-Azure Portal-Azure Database for MariaDB
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MariaDB v Azure Database for MariaDB pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 59d18ea11699ed77763c162e4930b159fcd19fe2
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888661"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Postup konfigurace parametrů serveru v Azure Database for MariaDB pomocí Azure Portal

Azure Database for MariaDB podporuje konfiguraci některých parametrů serveru. Tento článek popisuje, jak nakonfigurovat tyto parametry s využitím webu Azure portal. Ne všechny parametry serveru je možné upravit.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Přejděte do parametrů serveru na webu Azure portal

1. Přihlaste se k Azure Portal a potom vyhledejte server Azure Database for MariaDB.
2. V části **Nastavení** klikněte na **parametry serveru** a otevřete stránku parametry serveru pro Azure Database for MariaDB Server.
![Stránka parametry Azure portálový server](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Vyhledejte nastavení, které je potřeba upravit. Zkontrolujte **popis** sloupce, které vám pomohou pochopit účel a povolených hodnot.
![Zobrazení výčtu rozevírací seznam](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klikněte na tlačítko **Uložit** uložte provedené změny.
![Uložit nebo zahodit změny](./media/howto-server-parameters/4-save_parameters.png)
5. Pokud jste uložili nové hodnoty pro parametry, můžete se vždycky vrátit vše zpět na výchozí hodnoty tak, že vyberete **Resetovat vše na výchozí**.
![Resetovat vše na výchozí](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Seznam konfigurovatelných server parametrů

Seznam parametrů podporovaný server s neustále roste. Na kartě parametrů serveru na webu Azure portal k získání definice a konfigurace parametrů serveru na základě požadavků aplikace.

## <a name="non-configurable-server-parameters"></a>Parametry Nekonfigurovatelné serveru

InnoDB vyrovnávací paměti fondu a maximální počet připojení nejsou konfigurovatelné a vázané na vaše [cenovou úroveň](concepts-pricing-tiers.md).

|**Cenová úroveň**| **počet virtuálních jader:**|**Fond vyrovnávacích pamětí InnoDB (MB)**| **Maximální počet připojení**|
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

Tyto další server parametry nejsou konfigurovatelné v systému:

|**Parametr**|**Pevná hodnota**|
| :------------------------ | :-------- |
|innodb_file_per_table v úrovni Basic|OFF|
|innodb_flush_log_at_trx_commit|1\. místo|
|sync_binlog|1\. místo|
|innodb_log_file_size|512MB|

Další parametry serveru, které zde nejsou uvedeny, jsou nastaveny na jejich MariaDB výchozí výchozí hodnoty pro [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

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

Informace o [funkcích data a času](https://mariadb.com/kb/en/library/convert_tz/)najdete v dokumentaci k MariaDB.

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->

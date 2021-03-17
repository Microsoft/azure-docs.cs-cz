---
title: Konfigurace parametrů serveru-Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MySQL v Azure Database for MySQL pomocí Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: 363be8b34f230b812bc24276e1f3925faf0cdc1c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540837"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Konfigurace parametrů serveru v Azure Database for MySQL pomocí Azure Portal

Azure Database for MySQL podporuje konfiguraci některých parametrů serveru. Tento článek popisuje, jak nakonfigurovat tyto parametry pomocí Azure Portal. Ne všechny parametry serveru lze upravit.

>[!Note]
> Parametry serveru je možné aktualizovat globálně na úrovni serveru pomocí [Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShellu](./howto-configure-server-parameters-using-powershell.md) nebo webu [Azure Portal](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Konfigurace parametrů serveru

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a potom vyhledejte server Azure Database for MySQL.
2. V části **Nastavení** klikněte na **parametry serveru** a otevřete stránku parametry serveru pro Azure Database for MySQL server.
:::image type="content" source="./media/howto-server-parameters/auzre-portal-server-parameters.png" alt-text="Stránka parametrů serveru Azure Portal":::
3. Vyhledejte všechna nastavení, která potřebujete upravit. Přečtěte si sloupec **Description (popis** ), abyste pochopili účel a povolené hodnoty.
:::image type="content" source="./media/howto-server-parameters/3-toggle_parameter.png" alt-text="Výčet rozevíracích seznamu":::
4. Kliknutím na  **Uložit** uložte změny.
:::image type="content" source="./media/howto-server-parameters/4-save_parameters.png" alt-text="Uložit nebo zahodit změny":::
5. Pokud jste uložili nové hodnoty pro parametry, můžete kdykoli vrátit zpět všechny výchozí hodnoty výběrem možnosti **Obnovit vše na výchozí**.
:::image type="content" source="./media/howto-server-parameters/5-reset_parameters.png" alt-text="Obnovit výchozí":::

## <a name="setting-parameters-not-listed"></a>Nastavení neuvedených parametrů

Pokud parametr serveru, který chcete aktualizovat, není uveden v Azure Portal, můžete volitelně nastavit parametr na úrovni připojení pomocí `init_connect` . Tím se nastaví parametry serveru pro každého klienta, který se připojuje k serveru. 

1. V části **Nastavení** klikněte na **parametry serveru** a otevřete stránku parametry serveru pro Azure Database for MySQL server.
2. Hledat `init_connect`
3. Přidejte parametry serveru ve formátu: `SET parameter_name=YOUR_DESIRED_VALUE` hodnota ve sloupci hodnota.

    Například můžete změnit znakovou sadu serveru nastavením `init_connect` na `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Kliknutím na **Uložit** změny uložte.

>[!Note]
> Pomocí příkazu `init_connect` je možné měnit parametry, které nevyžadují oprávnění SUPER na úrovni relace. Pokud chcete ověřit, jestli můžete nastavit parametr pomocí příkazu `init_connect`, spusťte příkaz `set session parameter_name=YOUR_DESIRED_VALUE;` – pokud selže s chybou **Přístup byl odepřen, potřebujete oprávnění SUPER** , znamená to, že tento parametr nemůžete nastavit pomocí příkazu init_connect.

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časových pásem

Tabulky časových pásem na vašem serveru se dají naplnit voláním `mysql.az_load_timezone` uložené procedury z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud spouštíte `mysql.az_load_timezone` příkaz z aplikace MySQL Workbench, možná bude nutné nejprve vypnout režim bezpečné aktualizace pomocí nástroje `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Je nutné restartovat server, aby bylo zajištěno, že tabulky časových pásem budou správně vyplněny. K restartování serveru použijte [Azure Portal](howto-restart-server-portal.md) nebo [CLI](howto-restart-server-cli.md).

Chcete-li zobrazit dostupné hodnoty časového pásma, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení časového pásma globální úrovně

Časové pásmo globální úrovně lze nastavit na stránce **parametry serveru** v Azure Portal. Následující nastavení nastaví globální časové pásmo na hodnotu "US/Tichomoří".

:::image type="content" source="./media/howto-server-parameters/timezone.png" alt-text="Nastavit parametr časového pásma":::

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovně relace

Časové pásmo úrovně relace můžete nastavit spuštěním `SET time_zone` příkazu z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo na časové pásmo **USA/Tichomoří** .

```sql
SET time_zone = 'US/Pacific';
```

Informace o [funkcích pro datum a čas](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)najdete v dokumentaci k MySQL.

## <a name="next-steps"></a>Další kroky

- [Knihovny připojení pro Azure Database for MySQL](concepts-connection-libraries.md).

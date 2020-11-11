---
title: Konfigurace parametrů serveru-Azure Portal-Azure Database for MySQL flexibilní Server
description: Tento článek popisuje, jak nakonfigurovat parametry serveru MySQL v Azure Database for MySQL flexibilním serveru pomocí Azure Portal.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 7733a6211363b4f1c9e9006f757b4d152c7af7f5
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489552"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Konfigurace parametrů serveru v Azure Database for MySQL-flexibilním serveru pomocí Azure Portal

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Azure Database for MySQL flexibilní konfiguraci serveru můžete spravovat pomocí parametrů serveru. Při vytváření serveru jsou parametry serveru nastaveny s výchozí a doporučenou hodnotou.  

Tento článek popisuje, jak zobrazit a nakonfigurovat parametry serveru pomocí Azure Portal. Okno parametr serveru na Azure Portal zobrazuje jak upravitelný, tak i neupravitelný parametr serveru. Parametry serveru, které nelze upravovat, jsou šedé.

>[!Note]
> Parametry serveru se dají aktualizovat globálně na úrovni serveru pomocí [Azure CLI](./how-to-configure-server-parameters-cli.md) nebo [Azure Portal](./how-to-configure-server-parameters-portal.md).

## <a name="configure-server-parameters"></a>Konfigurace parametrů serveru

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a vyhledejte Azure Database for MySQL flexibilní Server.
2. V části **Nastavení** klikněte na **parametry serveru** a otevřete stránku parametry serveru pro Azure Database for MySQL flexibilní Server.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Stránka parametrů serveru Azure Portal":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Vyhledejte libovolný parametr serveru, který budete muset upravit. Přečtěte si sloupec **Description (popis** ), abyste pochopili účel a povolené hodnoty.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Výčet rozevíracích seznamu":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Kliknutím na  **Uložit** uložte změny.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Uložit nebo zahodit změny":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. Statické parametry jsou ta, která vyžaduje, aby se restart serveru projevil. Pokud upravujete statický parametr, zobrazí se výzva k **restartování počítače** nebo **restartování později**.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Restartovat při uložení statického parametru":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Pokud jste uložili nové hodnoty pro parametry, můžete kdykoli vrátit zpět všechny výchozí hodnoty výběrem možnosti **Obnovit vše na výchozí**.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Obnovit výchozí":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Nastavení neupravitelných parametrů serveru

Pokud parametr serveru, který chcete aktualizovat, není upravitelný, můžete volitelně nastavit parametr na úrovni připojení pomocí `init_connect` . Tím se nastaví parametry serveru pro každého klienta, který se připojuje k serveru. 

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
> Je nutné restartovat server, aby bylo zajištěno, že tabulky časových pásem budou správně vyplněny.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Chcete-li zobrazit dostupné hodnoty časového pásma, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení časového pásma globální úrovně

Časové pásmo globální úrovně lze nastavit na stránce **parametry serveru** v Azure Portal. Následující nastavení nastaví globální časové pásmo na hodnotu "US/Tichomoří".

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Nastavit parametr časového pásma":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovně relace

Časové pásmo úrovně relace můžete nastavit spuštěním `SET time_zone` příkazu z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo na časové pásmo **USA/Tichomoří** .

```sql
SET time_zone = 'US/Pacific';
```

Informace o [funkcích pro datum a čas](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)najdete v dokumentaci k MySQL.

## <a name="next-steps"></a>Další kroky

- Postup konfigurace [parametrů serveru v Azure CLI](./how-to-configure-server-parameters-cli.md)

---
title: Přístup k protokolům pomalým dotazům-Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k pomalým protokolům v Azure Database for MySQL z Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 3/15/2021
ms.openlocfilehash: 91569780aa71861e07c7e96bec5eac879642760d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496214"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Konfigurace a přístup k protokolům pomalým dotazům z Azure Portal

V Azure Portal můžete nakonfigurovat, vypsat a stáhnout [protokoly pomalých dotazů Azure Database for MySQL](concepts-server-logs.md) .

## <a name="prerequisites"></a>Požadavky
Postup v tomto článku vyžaduje, abyste měli [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurovat protokolování
Nakonfigurujte přístup k protokolu pomalého dotazu MySQL. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Vyberte server Azure Database for MySQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. 
   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Snímek obrazovky s možnostmi protokolu serveru":::

4. Pokud chcete zobrazit parametry serveru, vyberte **kliknutím sem povolíte protokoly a nakonfigurujete parametry protokolu**.

5. Zapněte **slow_query_log** na **zapnuto**.

6. Vyberte, kde se mají protokoly výstupovat, aby se používaly **log_output**. Pokud chcete odesílat protokoly do místního úložiště i Azure Monitor diagnostické protokoly, vyberte **soubor**.

7. Zvažte nastavení "long_query_time", která představuje mezní hodnotu doby dotazu pro dotazy, které budou shromážděny v souboru protokolu pomalého dotazu, minimální a výchozí hodnoty long_query_time jsou 0 a 10 v uvedeném pořadí.

8. Upravte jiné parametry, například log_slow_admin_statements k protokolování příkazů pro správu. Ve výchozím nastavení nejsou příkazy správy protokolovány, ani dotazy, které nepoužívají indexy pro vyhledávání. 

9. Vyberte **Uložit**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Snímek obrazovky s pomalými parametry protokolu dotazů a uložení.":::

Na stránce **parametry serveru** se můžete vrátit do seznamu protokolů tím, že stránku zavřete.

## <a name="view-list-and-download-logs"></a>Zobrazit seznam a stáhnout protokoly
Po zahájení protokolování můžete zobrazit seznam dostupných protokolů pomalých dotazů a stahovat jednotlivé soubory protokolů.

1. Otevřete web Azure Portal.

2. Vyberte server Azure Database for MySQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. Stránka zobrazuje seznam souborů protokolu.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Snímek obrazovky se stránkou protokolů serveru se zvýrazněným seznamem protokolů":::

   > [!TIP]
   > Konvence pojmenování protokolu je **MySQL-pomalé – < názvu serveru>-yyyymmddhh. log**. Datum a čas použití v názvu souboru je čas, kdy byl protokol vydán. Soubory protokolu se otočí každých 24 hodin nebo 7,5 GB, podle toho, co nastane dřív. 

4. V případě potřeby se pomocí vyhledávacího pole rychle zúží na konkrétní protokol, a to na základě data a času. Hledání je na názvu protokolu.

5. Chcete-li stáhnout jednotlivé soubory protokolu, vyberte ikonu šipky vedle každého souboru protokolu v řádku tabulky.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-download.png" alt-text="Snímek obrazovky se stránkou protokolů serveru s zvýrazněnou ikonou šipky":::

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky**  >  **Přidat nastavení diagnostiky**.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png" alt-text="Snímek obrazovky s možnostmi nastavení diagnostiky":::

2. Zadejte název nastavení diagnostiky.

3. Určete, které datové jímky mají Odeslat protokoly pomalých dotazů (účet úložiště, centrum událostí nebo Log Analytics pracovní prostor).

4. Jako typ protokolu vyberte **MySqlSlowLogs** .
:::image type="content" source="./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png" alt-text="Snímek obrazovky s možnostmi konfigurace nastavení diagnostiky":::

5. Po nakonfigurování datových umyvadel pro přesměrování protokolů pomalých dotazů na vyberte **Uložit**.
:::image type="content" source="./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png" alt-text="Snímek obrazovky s možnostmi konfigurace nastavení diagnostiky s zvýrazněným možností Uložit":::

6. Přístup k protokolům pomalým dotazům můžete prozkoumat v datech, která jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak programově stahovat protokoly pomalých dotazů, najdete [v tématu přístup k protokolům pomalým dotazů](howto-configure-server-logs-in-cli.md)
- Přečtěte si další informace o [protokolech pomalých dotazů](concepts-server-logs.md) v Azure Database for MySQL.
- Další informace o definicích parametrů a protokolování MySQL najdete v dokumentaci k MySQL v [protokolech](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
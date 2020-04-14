---
title: Přístup k protokolům pomalých dotazů – portál Azure – databáze Azure pro MySQL
description: Tento článek popisuje, jak nakonfigurovat pomalé protokoly v Azure Database for MySQL z portálu Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 59faf63312bd7cc657f8b96ca3110707ea997c02
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273596"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Konfigurace a přístup k pomalým protokolům dotazů z webu Azure Portal

Můžete nakonfigurovat, seznam a stáhnout [Azure Database for MySQL pomalé protokoly dotazů](concepts-server-logs.md) z webu Azure Portal.

## <a name="prerequisites"></a>Požadavky
Kroky v tomto článku vyžadují, abyste měli [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurace protokolování
Nakonfigurujte přístup k protokolu pomalých dotazů MySQL. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte databázi Azure pro server MySQL.

3. V části **Monitorování** na postranním panelu vyberte **protokoly serveru**. 
   ![Snímek obrazovky s možnostmi protokolů serveru](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Chcete-li zobrazit parametry serveru, vyberte klepnutím **sem povolit protokoly a konfigurovat parametry protokolu**.

5. Zapněte **slow_query_log** **na ZAPNUTO**.

6. Vyberte, kde se mají protokoly vyprosit pomocí **log_output**. Pokud chcete odeslat protokoly do místního úložiště i do diagnostických protokolů monitorování Azure, vyberte **Soubor**. 

7. Změňte všechny další potřebné parametry. 

8. Vyberte **Uložit**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Snímek obrazovky s parametry protokolu pomalých dotazů a uložení":::

Na stránce **Parametry serveru** se můžete vrátit do seznamu protokolů zavřením stránky.

## <a name="view-list-and-download-logs"></a>Zobrazení seznamů a protokolů ke stažení
Po zahájení protokolování můžete zobrazit seznam dostupných protokolů pomalých dotazů a stáhnout jednotlivé soubory protokolu.

1. Otevřete web Azure Portal.

2. Vyberte databázi Azure pro server MySQL.

3. V části **Monitorování** na postranním panelu vyberte **protokoly serveru**. Na stránce se zobrazí seznam souborů protokolu.

   ![Snímek obrazovky stránky Protokoly serveru se zvýrazněným seznamem protokolů](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konvence pojmenování protokolu je **mysql-slow-< název serveru>-yyymmddhh.log**. Datum a čas použitý v názvu souboru je čas, kdy byl protokol vydán. Soubory protokolu se otáčejí každých 24 hodin nebo 7,5 GB podle toho, co nastane dříve. 

4. V případě potřeby použijte vyhledávací pole k rychlému zúžení na konkrétní protokol na základě data a času. Hledání je na název protokolu.

5. Chcete-li stáhnout jednotlivé soubory protokolu, vyberte ikonu šipky dolů vedle každého souboru protokolu v řádku tabulky.

   ![Snímek obrazovky stránky Protokoly serveru se zvýrazněnou ikonou šipky dolů](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **Monitorování** na postranním panelu vyberte **Nastavení** > diagnostiky**Přidat nastavení diagnostiky**.

   ![Snímek obrazovky s možnostmi nastavení diagnostiky](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Zadejte název diagnostického nastavení.

1. Určete, která data se propadá k odeslání pomalých protokolů dotazů (účet úložiště, centrum událostí nebo pracovní prostor Log Analytics).

1. Jako typ protokolu **vyberte MySqlSlowLogs.**
![Snímek obrazovky s možnostmi konfigurace nastavení diagnostiky](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. Po nakonfigurování jímek dat tak, aby se pomalé protokoly dotazů napíchlo, vyberte **Uložit**.
![Snímek obrazovky s možnostmi konfigurace nastavení diagnostiky se zvýrazněnou možností uložit](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. Přístup k protokoly pomalé dotazu jejich prozkoumáním v jímky dat, které jste nakonfigurovali. Může trvat až 10 minut, než se protokoly zobrazí.

## <a name="next-steps"></a>Další kroky
- Informace o tom, jak programově stahovat pomalé protokoly dotazů, naleznete [v tématu Protokoly pomalých dotazů v rozhraní CLI aplikace Access.](howto-configure-server-logs-in-cli.md)
- Další informace o [protokolech pomalých dotazů](concepts-server-logs.md) v Azure Database for MySQL.
- Další informace o definicích parametrů a protokolování MySQL naleznete v dokumentaci mysql k [protokolům](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
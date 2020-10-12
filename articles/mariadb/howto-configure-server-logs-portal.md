---
title: Přístup k protokolům pomalým dotazům-Azure Portal-Azure Database for MariaDB
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům pomalým dotazům v Azure Database for MariaDB z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 4/13/2020
ms.openlocfilehash: a2642ebbad7c8d7a2e092a6e00929e32b72b52d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86104975"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-from-the-azure-portal"></a>Konfigurace a přístup k protokolům Azure Database for Marie DB pomalým dotazům z Azure Portal

V Azure Portal můžete nakonfigurovat, vypsat a stáhnout [protokoly pomalých dotazů Azure Database for MariaDB](concepts-server-logs.md) .

## <a name="prerequisites"></a>Požadavky
Postup v tomto článku vyžaduje, abyste měli [Azure Database for MariaDB Server](quickstart-create-mariadb-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurovat protokolování
Nakonfigurujte přístup k protokolu pomalých dotazů. 

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. Vyberte server Azure Database for MariaDB.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. 
   ![Snímek obrazovky s možnostmi protokolu serveru](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Pokud chcete zobrazit parametry serveru, vyberte **kliknutím sem povolíte protokoly a nakonfigurujete parametry protokolu**.

5. Zapněte **slow_query_log** na **zapnuto**.

6. Vyberte, kde se mají protokoly výstupovat, aby se používaly **log_output**. Pokud chcete odesílat protokoly do místního úložiště i Azure Monitor diagnostické protokoly, vyberte **soubor**. 

7. Změňte všechny potřebné parametry. 

8. Vyberte **Uložit**. 

   :::image type="content" source="./media/howto-configure-server-logs-portal/3-save-discard.png" alt-text="Snímek obrazovky s pomalými parametry protokolu dotazů a uložení.":::

Na stránce **parametry serveru** se můžete vrátit do seznamu protokolů tím, že stránku zavřete.

## <a name="view-list-and-download-logs"></a>Zobrazit seznam a stáhnout protokoly
Po zahájení protokolování můžete zobrazit seznam dostupných protokolů pomalých dotazů a stahovat jednotlivé soubory protokolů. 

1. Otevřete web Azure Portal.

2. Vyberte server Azure Database for MariaDB.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. Stránka zobrazuje seznam souborů protokolu.

   ![Snímek obrazovky se stránkou protokolů serveru se zvýrazněným seznamem protokolů](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Konvence pojmenování protokolu je **MySQL-pomalé – < názvu serveru>-yyyymmddhh. log**. Datum a čas použití v názvu souboru je čas, kdy byl protokol vydán. Soubory protokolu se otočí každých 24 hodin nebo 7,5 GB, podle toho, co nastane dřív.

4. V případě potřeby se pomocí vyhledávacího pole rychle zúží na konkrétní protokol, a to na základě data a času. Hledání je na názvu protokolu.

5. Chcete-li stáhnout jednotlivé soubory protokolu, vyberte ikonu šipky vedle každého souboru protokolu v řádku tabulky.

   ![Snímek obrazovky se stránkou protokolů serveru s zvýrazněnou ikonou šipky](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky**  >  **Přidat nastavení diagnostiky**.

   ![Snímek obrazovky s možnostmi nastavení diagnostiky](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Zadejte název nastavení diagnostiky.

1. Určete, které datové jímky mají Odeslat protokoly pomalých dotazů (účet úložiště, centrum událostí nebo Log Analytics pracovní prostor).

1. Jako typ protokolu vyberte **MySqlSlowLogs** .
![Snímek obrazovky s možnostmi konfigurace nastavení diagnostiky](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Po nakonfigurování datových umyvadel pro přesměrování protokolů pomalých dotazů na vyberte **Uložit**.
![Snímek obrazovky s možnostmi konfigurace nastavení diagnostiky s zvýrazněným možností Uložit](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. Přístup k protokolům pomalým dotazům můžete prozkoumat v datech, která jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak programově stahovat protokoly pomalých dotazů, najdete [v tématu přístup k protokolům pomalým dotazů](howto-configure-server-logs-cli.md)
- Přečtěte si další informace o [protokolech pomalých dotazů](concepts-server-logs.md) v Azure Database for MariaDB.
- Další informace o definicích parametrů a protokolování najdete v dokumentaci k MariaDB v [protokolech](https://mariadb.com/kb/en/library/slow-query-log-overview/).
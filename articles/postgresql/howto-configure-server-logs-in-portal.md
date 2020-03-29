---
title: Správa protokolů – portál Azure – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům serveru (.soubory protokolu) v Azure Database for PostgreSQL – single server z portálu Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763685"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Konfigurace a přístup k databázi Azure pro PostgreSQL – protokoly jednoho serveru z portálu Azure

Můžete nakonfigurovat, seznam a stáhnout [Azure Database pro PostgreSQL protokoly](concepts-server-logs.md) z webu Azure Portal.

## <a name="prerequisites"></a>Požadavky
Kroky v tomto článku vyžadují, abyste měli [Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Konfigurace protokolování
Nakonfigurujte přístup k protokolům dotazů a protokolům chyb. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **Monitorování** na postranním panelu vyberte **protokoly serveru**. 

   ![Snímek obrazovky s možnostmi protokolů serveru](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Chcete-li zobrazit parametry serveru, vyberte klepnutím **sem povolit protokoly a konfigurovat parametry protokolu**.

5. Změňte parametry, které je třeba upravit. Všechny změny provedené v této relaci jsou zvýrazněny fialovou barvou.

   Po změně parametrů vyberte **Uložit**. Nebo můžete zahodit změny. 

   ![Snímek obrazovky s možnostmi parametrů serveru](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na stránce **Parametry serveru** se můžete vrátit do seznamu protokolů zavřením stránky.

## <a name="view-list-and-download-logs"></a>Zobrazení seznamů a protokolů ke stažení
Po zahájení protokolování můžete zobrazit seznam dostupných protokolů a stáhnout jednotlivé soubory protokolu. 

1. Otevřete web Azure Portal.

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **Monitorování** na postranním panelu vyberte **protokoly serveru**. Na stránce se zobrazí seznam souborů protokolu.

   ![Snímek obrazovky stránky Protokoly serveru se zvýrazněným seznamem protokolů](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konvence pojmenování protokolu je **postgresql-yyyy-mm-dd_hh0000.log**. Datum a čas použitý v názvu souboru je čas, kdy byl protokol vydán. Soubory protokolu otáčet každou hodinu nebo 100 MB, podle toho, co nastane dříve.

4. V případě potřeby použijte vyhledávací pole k rychlému zúžení na konkrétní protokol na základě data a času. Hledání je na název protokolu.

   ![Snímek obrazovky se stránkou Protokoly serveru se zvýrazněným vyhledávacím polem a výsledky](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Chcete-li stáhnout jednotlivé soubory protokolu, vyberte ikonu šipky dolů vedle každého souboru protokolu v řádku tabulky.

   ![Snímek obrazovky stránky Protokoly serveru se zvýrazněnou ikonou šipky dolů](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Další kroky
- Informace o tom, jak protokoly stahovat programově, naleznete [v protokolech serveru Access v rozhraní CLI.](howto-configure-server-logs-using-cli.md)
- Další informace o [protokolech serverů](concepts-server-logs.md) v Azure Database for PostgreSQL. 
- Další informace o definicích parametrů a protokolování PostgreSQL naleznete v dokumentaci PostgreSQL o [zasílání zpráv o chybách a protokolování](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).


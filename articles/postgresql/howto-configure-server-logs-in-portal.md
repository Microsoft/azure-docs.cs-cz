---
title: Správa protokolů-Azure Portal-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům serveru (soubory. log) v Azure Database for PostgreSQL-Single server z Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3da48a9b6d3acb1f2811bc279de7963fa1d83918
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763685"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Konfigurace a přístup k protokolům Azure Database for PostgreSQL-Single server z Azure Portal

V Azure Portal můžete nakonfigurovat, vypsat a stáhnout [protokoly Azure Database for PostgreSQL](concepts-server-logs.md) .

## <a name="prerequisites"></a>Předpoklady
Postup v tomto článku vyžaduje, abyste měli [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Konfigurovat protokolování
Nakonfigurujte přístup k protokolům dotazů a protokolům chyb. 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. 

   ![Snímek obrazovky s možnostmi protokolu serveru](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Pokud chcete zobrazit parametry serveru, vyberte **kliknutím sem povolíte protokoly a nakonfigurujete parametry protokolu**.

5. Změňte parametry, které je třeba upravit. Všechny změny, které provedete v této relaci, jsou zvýrazněné fialově.

   Po změně parametrů vyberte **Save (Uložit**). Nebo můžete změny zrušit. 

   ![Snímek obrazovky s možnostmi parametrů serveru](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na stránce **parametry serveru** se můžete vrátit do seznamu protokolů tím, že stránku zavřete.

## <a name="view-list-and-download-logs"></a>Zobrazit seznam a stáhnout protokoly
Po zahájení protokolování můžete zobrazit seznam dostupných protokolů a stáhnout jednotlivé soubory protokolů. 

1. Otevřete web Azure Portal.

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. Stránka zobrazuje seznam souborů protokolu.

   ![Snímek obrazovky se stránkou protokolů serveru se zvýrazněným seznamem protokolů](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konvence pojmenování protokolu je **PostgreSQL-rrrr-mm-dd_hh0000. log**. Datum a čas použití v názvu souboru je čas, kdy byl protokol vydán. Soubory protokolu se otáčejí každou hodinu nebo 100 MB, podle toho, co nastane dřív.

4. V případě potřeby se pomocí vyhledávacího pole rychle zúží na konkrétní protokol, a to na základě data a času. Hledání je na názvu protokolu.

   ![Snímek obrazovky se stránkou protokolů serveru s vybraným vyhledávacím polem a výsledky](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Chcete-li stáhnout jednotlivé soubory protokolu, vyberte ikonu šipky vedle každého souboru protokolu v řádku tabulky.

   ![Snímek obrazovky se stránkou protokolů serveru s zvýrazněnou ikonou šipky](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Další kroky
- Informace o tom, jak programově stahovat protokoly, najdete [v tématu Access Server Logs v](howto-configure-server-logs-using-cli.md) rozhraní příkazového řádku.
- Přečtěte si další informace o [protokolech serveru](concepts-server-logs.md) v Azure Database for PostgreSQL. 
- Další informace o definicích parametrů a protokolování PostgreSQL najdete v dokumentaci k PostgreSQL v tématu [zasílání zpráv o chybách a protokolování](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).


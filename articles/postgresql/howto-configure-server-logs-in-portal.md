---
title: Správa protokolů-Azure Portal-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům serveru (soubory. log) v Azure Database for PostgreSQL-Single server z Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3e44377ecb734f0036d05a347596f1ff003ae28a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604324"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Konfigurace a přístup k protokolům Azure Database for PostgreSQL-Single server z Azure Portal

V Azure Portal můžete nakonfigurovat, vypsat a stáhnout [protokoly Azure Database for PostgreSQL](concepts-server-logs.md) .

## <a name="prerequisites"></a>Požadavky
Postup v tomto článku vyžaduje, abyste měli [Azure Database for PostgreSQL Server](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>Konfigurovat protokolování
Nakonfigurujte přístup k protokolům dotazů a protokolům chyb. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png" alt-text="Snímek obrazovky s možnostmi protokolu serveru":::

4. Pokud chcete zobrazit parametry serveru, vyberte **kliknutím sem povolíte protokoly a nakonfigurujete parametry protokolu**.

5. Změňte parametry, které je třeba upravit. Všechny změny, které provedete v této relaci, jsou zvýrazněné fialově.

   Po změně parametrů vyberte **Save (Uložit**). Nebo můžete změny zrušit. 

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/3-save-discard.png" alt-text="Snímek obrazovky s možnostmi parametrů serveru":::

Na stránce **parametry serveru** se můžete vrátit do seznamu protokolů tím, že stránku zavřete.

## <a name="view-list-and-download-logs"></a>Zobrazit seznam a stáhnout protokoly
Po zahájení protokolování můžete zobrazit seznam dostupných protokolů a stáhnout jednotlivé soubory protokolů. 

1. Otevřete web Azure Portal.

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. Stránka zobrazuje seznam souborů protokolu.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/4-server-logs-list.png" alt-text="Snímek obrazovky se stránkou protokolů serveru se zvýrazněným seznamem protokolů":::

   > [!TIP]
   > Konvence pojmenování protokolu je **PostgreSQL-rrrr-mm-dd_hh0000. log**. Datum a čas použití v názvu souboru je čas, kdy byl protokol vydán. Soubory protokolu se otáčejí každou hodinu nebo 100 MB, podle toho, co nastane dřív.

4. V případě potřeby se pomocí vyhledávacího pole rychle zúží na konkrétní protokol, a to na základě data a času. Hledání je na názvu protokolu.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/5-search.png" alt-text="Snímek obrazovky se stránkou protokolů serveru s vybraným vyhledávacím polem a výsledky":::

5. Chcete-li stáhnout jednotlivé soubory protokolu, vyberte ikonu šipky vedle každého souboru protokolu v řádku tabulky.

   :::image type="content" source="./media/howto-configure-server-logs-in-portal/6-download.png" alt-text="Snímek obrazovky se stránkou protokolů serveru s zvýrazněnou ikonou šipky":::

## <a name="next-steps"></a>Další kroky
- Informace o tom, jak programově stahovat protokoly, najdete [v tématu Access Server Logs v](howto-configure-server-logs-using-cli.md) rozhraní příkazového řádku.
- Přečtěte si další informace o [protokolech serveru](concepts-server-logs.md) v Azure Database for PostgreSQL. 
- Další informace o definicích parametrů a protokolování PostgreSQL najdete v dokumentaci k PostgreSQL v tématu [zasílání zpráv o chybách a protokolování](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).


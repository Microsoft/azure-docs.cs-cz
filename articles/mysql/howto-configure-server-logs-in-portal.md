---
title: Konfigurace a přístup k protokolům pomalým dotazům v Azure Database for MySQL z Azure Portal
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k pomalým protokolům v Azure Database for MySQL z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 7eeeb729973e484e9acb26f3ac8cc42693f72eea
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841582"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Konfigurace a přístup k protokolům pomalým dotazům z Azure Portal

V Azure Portal můžete nakonfigurovat, vypsat a stáhnout [protokoly pomalých dotazů Azure Database for MySQL](concepts-server-logs.md) .

## <a name="prerequisites"></a>Předpoklady
Postup v tomto článku vyžaduje, abyste měli [Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md).

## <a name="configure-logging"></a>Konfigurovat protokolování
Nakonfigurujte přístup k protokolu pomalého dotazu MySQL. 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

2. Vyberte server Azure Database for MySQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. 
   @no__t – 0Screenshot možností protokolu serveru @ no__t-1

4. Pokud chcete zobrazit parametry serveru, vyberte **kliknutím sem povolíte protokoly a nakonfigurujete parametry protokolu**.

5. Změňte parametry, které je třeba upravit. Všechny změny, které provedete v této relaci, jsou zvýrazněné fialově. 

   Po změně parametrů vyberte **Save (Uložit**). Nebo můžete změny zrušit.

   ![Snímek obrazovky s možnostmi parametrů serveru](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Na stránce **parametry serveru** se můžete vrátit do seznamu protokolů tím, že stránku zavřete.

## <a name="view-list-and-download-logs"></a>Zobrazit seznam a stáhnout protokoly
Po zahájení protokolování můžete zobrazit seznam dostupných protokolů pomalých dotazů a stahovat jednotlivé soubory protokolů.

1. Otevřete web Azure Portal.

2. Vyberte server Azure Database for MySQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. Stránka zobrazuje seznam souborů protokolu.

   ![Snímek obrazovky se stránkou protokolů serveru se zvýrazněným seznamem protokolů](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konvence pojmenování protokolu je **MySQL-pomalé – < názvu serveru >-yyyymmddhh. log**. Datum a čas použití v názvu souboru je čas, kdy byl protokol vydán. Soubory protokolu se otočí každých 24 hodin nebo 7,5 GB, podle toho, co nastane dřív. 

4. V případě potřeby se pomocí vyhledávacího pole rychle zúží na konkrétní protokol, a to na základě data a času. Hledání je na názvu protokolu.

5. Chcete-li stáhnout jednotlivé soubory protokolu, vyberte ikonu šipky vedle každého souboru protokolu v řádku tabulky.

   ![Snímek obrazovky se stránkou protokolů serveru s zvýrazněnou ikonou šipky](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky** > **Přidat nastavení diagnostiky**.

   ![Snímek obrazovky s možnostmi nastavení diagnostiky](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. Zadejte název nastavení diagnostiky.

1. Určete, které datové jímky mají Odeslat protokoly pomalých dotazů (účet úložiště, centrum událostí nebo Log Analytics pracovní prostor).

1. Jako typ protokolu vyberte **MySqlSlowLogs** .
@no__t 0Screenshot možností konfigurace nastavení diagnostiky @ no__t-1

1. Po nakonfigurování datových umyvadel pro přesměrování protokolů pomalých dotazů na vyberte **Uložit**.
@no__t 0Screenshot možností konfigurace nastavení diagnostiky s zvýrazněným parametrem @ no__t-1

1. Přístup k protokolům pomalým dotazům můžete prozkoumat v datech, která jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak programově stahovat protokoly pomalých dotazů, najdete [v tématu přístup k protokolům pomalým dotazů](howto-configure-server-logs-in-cli.md)
- Přečtěte si další informace o [protokolech pomalých dotazů](concepts-server-logs.md) v Azure Database for MySQL.
- Další informace o definicích parametrů a protokolování MySQL najdete v dokumentaci k MySQL v [protokolech](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
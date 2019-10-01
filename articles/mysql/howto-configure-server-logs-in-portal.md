---
title: Konfigurace a přístup k protokolům pomalým dotazům pro Azure Database for MySQL v Azure Portal
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k pomalým protokolům v Azure Database for MySQL z Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: b3986c19ec008437f3230b3674ce60d1dfba2024
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703441"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>Konfigurace a přístup k protokolům pomalým dotazům v Azure Portal

V Azure Portal můžete nakonfigurovat, vypsat a stáhnout [protokoly pomalých dotazů Azure Database for MySQL](concepts-server-logs.md) .

## <a name="prerequisites"></a>Předpoklady
Pokud chcete projít tento průvodce, budete potřebovat:
- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurovat protokolování
Nakonfigurujte přístup k protokolu pomalého dotazu MySQL. 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

2. Vyberte server Azure Database for MySQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. 
   @no__t – protokoly serveru 0Select kliknutím nakonfigurujete @ no__t-1.

4. Vyberte záhlaví **kliknutím sem povolíte protokoly a nakonfigurujete parametry protokolu** pro zobrazení parametrů serveru.

5. Změňte parametry, které je třeba upravit. Všechny změny, které provedete v této relaci, jsou zvýrazněné fialově. 

   Po změně parametrů můžete kliknout na **Uložit**. Nebo můžete změny **Zrušit** .

   ![Klikněte na Uložit nebo zahodit.](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Vraťte se do seznamu protokolů kliknutím na **tlačítko Zavřít** (ikona X) na stránce **parametry serveru** .

## <a name="view-list-and-download-logs"></a>Zobrazit seznam a stáhnout protokoly
Po zahájení protokolování můžete zobrazit seznam dostupných protokolů pomalých dotazů a stahovat jednotlivé soubory protokolů v podokně protokoly serveru.

1. Otevřete web Azure Portal.

2. Vyberte server Azure Database for MySQL.

3. V části **monitorování** na bočním panelu vyberte **protokoly serveru**. Stránka obsahuje seznam souborů protokolu, jak je znázorněno níže:

   ![Seznam protokolů](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Konvence pojmenování protokolu je **MySQL-pomalé – < názvu serveru >-yyyymmddhh. log**. Datum a čas použití v názvu souboru je čas, kdy byl protokol vydán. Soubory protokolů se otočí každých 24 hodin nebo 7,5 GB, podle toho, co nastane dřív.

4. V případě potřeby pomocí **vyhledávacího pole** rychle upřesněte konkrétní protokol na základě data a času. Hledání je na názvu protokolu.

5. Jednotlivé soubory protokolu si můžete stáhnout pomocí tlačítka **Stáhnout** (ikona šipky dolů) vedle každého souboru protokolu v řádku tabulky, jak je znázorněno níže:

   ![Klikněte na ikonu stáhnout.](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů

1. V části **monitorování** na bočním panelu vyberte **nastavení diagnostiky**.

1. Klikněte na + Přidat nastavení diagnostiky ![Add nastavení diagnostiky @ no__t-1.

1. Zadejte název nastavení diagnostiky.

1. Určete, které datové jímky mají Odeslat protokoly pomalých dotazů (účet úložiště, centrum událostí a pracovní prostor Log Analytics).

1. Jako typ protokolu vyberte "MySqlSlowLogs".
![Configure nastavení diagnostiky @ no__t-1

1. Po nakonfigurování datových umyvadel pro přesměrování protokolů pomalých dotazů na můžete kliknout na **Uložit**.
![Save nastavení diagnostiky @ no__t-1

1. Přístup k protokolům pomalým dotazům můžete prozkoumat v datech, která jste nakonfigurovali. Zobrazení protokolů může trvat až 10 minut.

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak programově stahovat protokoly pomalých dotazů, najdete [v tématu přístup k protokolům pomalým dotazů](howto-configure-server-logs-in-cli.md)
- Přečtěte si další informace o [protokolech pomalých dotazů](concepts-server-logs.md) v Azure Database for MySQL.
- Další informace o definicích parametrů a protokolování MySQL najdete v dokumentaci k MySQL v [protokolech](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).
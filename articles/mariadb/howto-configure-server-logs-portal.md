---
title: Konfigurace a získat přístup k protokolům serveru pro službu Azure Database pro MariaDB na webu Azure Portal
description: Tento článek popisuje, jak nakonfigurovat a přístup k protokolům serveru ve službě Azure Database pro MariaDB z webu Azure Portal.
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0784dffc48aeddc854aa540cba24f1834cd41827
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952826"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurace a přístup k serveru přihlášení na webu Azure portal

Můžete nakonfigurovat, seznamu a stáhněte si [– Azure Database pro MariaDB serverové protokoly](concepts-server-logs.md) z portálu Azure portal.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [Azure Database pro MariaDB server](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurace protokolování
Konfigurace přístupu k protokolu pomalých dotazů. 

1. Přihlaste se k [portálu Azure](http://portal.azure.com/).

2. Vyberte váš server Azure Database for MariaDB.

3. V části **monitorování** části na bočním panelu, vyberte **protokoly serveru**. 
   ![Vyberte protokolů serveru, klikněte na konfigurace](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. Vyberte záhlaví **kliknutím sem můžete povolit protokoly a nakonfigurovat parametry protokolů** zobrazíte parametry serveru.

5. Změníte parametry, které je potřeba upravit, včetně zapnutí "slow_query_log" na "ON". Všechny změny provedené v této relaci jsou zvýrazněny znázorněný fialovou barvou. 

   Jakmile změníte parametry, můžete kliknout na **Uložit**. Nebo můžete **zahodit** provedené změny.

   ![Klikněte na Uložit nebo zahodit](./media/howto-configure-server-logs-portal/3-save-discard.png)

6. Přejděte zpět na seznam protokolů po kliknutí **tlačítko Zavřít** (X ikonu) na **parametry serveru** stránky.

## <a name="view-list-and-download-logs"></a>Zobrazení seznamu a stáhnout protokoly
Po zahájení protokolování, můžete zobrazit seznam dostupných protokolů a stažení jednotlivých souborů protokolu v podokně protokolů serveru. 

1. Otevřete web Azure Portal.

2. Vyberte váš server Azure Database for MariaDB.

3. V části **monitorování** části na bočním panelu, vyberte **protokoly serveru**. Na stránce se zobrazí seznam souborů protokolu, jak je znázorněno:

   ![Seznam protokolů](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > Zásady vytváření názvů protokolu je **mysql - pomalé – < název serveru >-yyyymmddhh.log**. Datum a čas, použít v názvu souboru je čas je v případě protokolu byl vydán. Soubory protokolů jsou otočeny každých 24 hodin nebo 7.5 GB, co nastane dříve.

4. V případě potřeby použijte **vyhledávacího pole** můžete rychle omezit na konkrétní protokol podle data a času. Hledání je na název protokolu.

5. Stáhněte si pomocí jednotlivých protokolových souborů **Stáhnout** tlačítko (ikona šipky pro sestupné) vedle každého souboru protokolu v řádku, jak je znázorněno:

   ![Klikněte na ikonu stažení](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="next-steps"></a>Další postup
- Další informace o [protokoly serveru](concepts-server-logs.md) ve službě Azure Database pro MariaDB.
- Další informace o definicemi parametrů a protokolování, najdete v dokumentaci MariaDB na [protokoly](https://mariadb.com/kb/en/library/slow-query-log-overview/).

<!-- - See [Access Server Logs in CLI](howto-configure-server-logs-in-cli.md) to learn how to download logs programmatically. -->
---
title: Konfigurace a získat přístup k protokolům server for PostgreSQL na webu Azure Portal
description: Tento článek popisuje, jak nakonfigurovat a přístup k protokolům serveru ve službě Azure Database for PostgreSQL na webu Azure Portal.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: be889d7b0bf6060b3b93244da6746fa6f13024da
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985501"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurace a přístup k serveru přihlášení na webu Azure portal

Můžete nakonfigurovat, seznamu a stáhněte si [– Azure Database for postgresql – protokoly serveru](concepts-server-logs.md) z portálu Azure portal.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [Azure Database for PostgreSQL server](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Konfigurace protokolování
Konfigurace přístupu k dotazu protokoly a protokoly chyb. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **monitorování** části na bočním panelu, vyberte **protokoly serveru**. 

   ![Vyberte protokoly serveru a vyberte možnost "Klikněte zde pro povolení..."](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Vyberte záhlaví **kliknutím sem můžete povolit protokoly a nakonfigurovat parametry protokolů** zobrazíte parametry serveru.

5. Změníte parametry, které je potřeba upravit. Všechny změny provedené v této relaci jsou zvýrazněny znázorněný fialovou barvou.

   Jakmile změníte parametry, můžete kliknout na **Uložit**. Nebo můžete **zahodit** provedené změny. 

   ![Dlouhý seznam parametrů k ukládání nebo zahazování změn](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Přejděte zpět na seznam protokolů po kliknutí **tlačítko Zavřít** (X ikonu) na **parametry serveru** stránky.

## <a name="view-list-and-download-logs"></a>Zobrazení seznamu a stáhnout protokoly
Po zahájení protokolování, můžete zobrazit seznam dostupných protokolů a stažení jednotlivých souborů protokolu v podokně protokolů serveru. 

1. Otevřete web Azure Portal.

2. Vyberte svůj server Azure Database for PostgreSQL.

3. V části **monitorování** části na bočním panelu, vyberte **protokoly serveru**. Na stránce se zobrazí seznam souborů protokolu, jak je znázorněno:

   ![Seznam serverů protokoly](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Zásady vytváření názvů protokolu je **postgresql rrrr mm-dd_hh0000.log**. Datum a čas, použít v názvu souboru je čas je v případě protokolu byl vydán. Soubory protokolů otočit každou hodinu nebo velikost 100 MB, podle toho, co nastane dřív.

4. V případě potřeby použijte **vyhledávacího pole** můžete rychle omezit na konkrétní protokol podle data a času. Hledání je na název protokolu.

   ![Příklad prohledávání protokolu názvů](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Stáhněte si pomocí jednotlivých protokolových souborů **Stáhnout** tlačítko (ikona šipky pro sestupné) vedle každého souboru protokolu v řádku, jak je znázorněno:

   ![Klikněte na ikonu stažení](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Další postup
- Zobrazit [přístup k protokolům serveru v rozhraní příkazového řádku](howto-configure-server-logs-using-cli.md) Další informace o stažení protokolů prostřednictvím kódu programu.
- Další informace o [protokoly serveru](concepts-server-logs.md) ve službě Azure DB for PostgreSQL. 
- Další informace o protokolování PostgreSQL a definicemi parametrů najdete v dokumentaci PostgreSQL na [zpráv o chybách a protokolování](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).


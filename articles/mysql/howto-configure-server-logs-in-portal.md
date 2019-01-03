---
title: Konfigurace a získat přístup k protokolům serveru pro službu Azure Database for MySQL na webu Azure Portal
description: Tento článek popisuje, jak nakonfigurovat a přístup k protokolům serveru ve službě Azure Database for MySQL z webu Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: e0701d2e10b366a6bf849512484fb216c42823bc
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544953"
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Konfigurace a přístup k serveru přihlášení na webu Azure portal

Můžete nakonfigurovat, seznamu a stáhněte si [– Azure Database for MySQL – protokoly serveru](concepts-server-logs.md) z portálu Azure portal.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [Azure Database for MySQL serveru](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Konfigurace protokolování
Konfigurace přístupu k protokolu pomalých dotazů MySQL. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte váš server Azure Database for MySQL.

3. V části **monitorování** části na bočním panelu, vyberte **protokoly serveru**. 
   ![Vyberte protokolů serveru, klikněte na konfigurace](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Vyberte záhlaví **kliknutím sem můžete povolit protokoly a nakonfigurovat parametry protokolů** zobrazíte parametry serveru.

5. Změníte parametry, které je potřeba upravit. Všechny změny provedené v této relaci jsou zvýrazněny znázorněný fialovou barvou. 

   Jakmile změníte parametry, můžete kliknout na **Uložit**. Nebo můžete **zahodit** provedené změny.

   ![Klikněte na Uložit nebo zahodit](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Přejděte zpět na seznam protokolů po kliknutí **tlačítko Zavřít** (X ikonu) na **parametry serveru** stránky.

## <a name="view-list-and-download-logs"></a>Zobrazení seznamu a stáhnout protokoly
Po zahájení protokolování, můžete zobrazit seznam dostupných protokolů a stažení jednotlivých souborů protokolu v podokně protokolů serveru. 

1. Otevřete web Azure Portal.

2. Vyberte váš server Azure Database for MySQL.

3. V části **monitorování** části na bočním panelu, vyberte **protokoly serveru**. Na stránce se zobrazí seznam souborů protokolu, jak je znázorněno:

   ![Seznam protokolů](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Zásady vytváření názvů protokolu je **mysql - pomalé – < název serveru >-yyyymmddhh.log**. Datum a čas, použít v názvu souboru je čas je v případě protokolu byl vydán. Soubory protokolů jsou otočeny každých 24 hodin nebo 7.5 GB, co nastane dříve.

4. V případě potřeby použijte **vyhledávacího pole** můžete rychle omezit na konkrétní protokol podle data a času. Hledání je na název protokolu.

5. Stáhněte si pomocí jednotlivých protokolových souborů **Stáhnout** tlačítko (ikona šipky pro sestupné) vedle každého souboru protokolu v řádku, jak je znázorněno:

   ![Klikněte na ikonu stažení](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>Další postup
- Zobrazit [přístup k protokolům serveru v rozhraní příkazového řádku](howto-configure-server-logs-in-cli.md) Další informace o stažení protokolů prostřednictvím kódu programu.
- Další informace o [protokoly serveru](concepts-server-logs.md) ve službě Azure Database for MySQL. 
- Další informace o protokolování MySQL a definicemi parametrů najdete v dokumentaci MySQL na [protokoly](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).


---
title: Restart serveru-Azure Portal-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak můžete pomocí Azure Portal restartovat server s Azure Database for PostgreSQL a jedním serverem.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 12/20/2020
ms.openlocfilehash: faa61ff477f44347755890dc59ebf4b917afda6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97882938"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Restartování Azure Database for PostgreSQL – jeden server pomocí Azure Portal
Toto téma popisuje, jak můžete restartovat server Azure Database for PostgreSQL. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.
 
> [!NOTE] 
> Čas potřebný k dokončení restartování závisí na procesu obnovení PostgreSQL. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním. Můžete taky chtít zvýšit frekvenci kontrolního bodu. Můžete také ladit hodnoty parametrů souvisejících s kontrolními body `max_wal_size` , včetně. Doporučuje se také spustit `CHECKPOINT` příkaz před restartováním serveru.

## <a name="prerequisites"></a>Předpoklady
K dokončení tohoto průvodce budete potřebovat:
- [Server Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Provést restart serveru

Následující kroky restartují server PostgreSQL:

1. V [Azure Portal](https://portal.azure.com/)vyberte server Azure Database for PostgreSQL.

2. Na panelu nástrojů na stránce **Přehled** serveru klikněte na **restartovat**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL – přehled – tlačítko restartovat":::

3. Kliknutím na **Ano** potvrďte restartování serveru.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for PostgreSQL – potvrzení restartování":::

4. Všimněte si, že se stav serveru změní na restart.

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for PostgreSQL – stav restartování":::

5. Potvrzení restartování serveru bylo úspěšné.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for PostgreSQL – úspěšné restartování":::

## <a name="next-steps"></a>Další kroky

Informace o [Nastavení parametrů v Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)

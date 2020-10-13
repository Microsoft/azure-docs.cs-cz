---
title: Restart serveru-Azure Portal-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak můžete pomocí Azure Portal restartovat server s Azure Database for PostgreSQL a jedním serverem.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: d25c8908b46608ac64bcf135404a070740413fe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907379"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Restartování Azure Database for PostgreSQL – jeden server pomocí Azure Portal
Toto téma popisuje, jak můžete restartovat server Azure Database for PostgreSQL. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.
 
Čas potřebný k dokončení restartování závisí na procesu obnovení PostgreSQL. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Server Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Provést restart serveru

Následující kroky restartují server PostgreSQL:

1. V [Azure Portal](https://portal.azure.com/)vyberte server Azure Database for PostgreSQL.

2. Na panelu nástrojů na stránce **Přehled** serveru klikněte na **restartovat**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL – přehled – tlačítko restartovat":::

3. Kliknutím na **Ano** potvrďte restartování serveru.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for PostgreSQL – přehled – tlačítko restartovat":::

4. Všimněte si, že se stav serveru změní na restart.

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for PostgreSQL – přehled – tlačítko restartovat":::

5. Potvrzení restartování serveru bylo úspěšné.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for PostgreSQL – přehled – tlačítko restartovat":::

## <a name="next-steps"></a>Další kroky

Informace o [Nastavení parametrů v Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)
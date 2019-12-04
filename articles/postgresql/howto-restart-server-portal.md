---
title: Restart serveru-Azure Portal-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak můžete pomocí Azure Portal restartovat server s Azure Database for PostgreSQL a jedním serverem.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770080"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Restartování Azure Database for PostgreSQL – jeden server pomocí Azure Portal
Toto téma popisuje, jak můžete restartovat server Azure Database for PostgreSQL. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.
 
Čas potřebný k dokončení restartování závisí na procesu obnovení PostgreSQL. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Předpoklady
K dokončení tohoto průvodce budete potřebovat:
- [Server Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Provést restart serveru

Následující kroky restartují server PostgreSQL:

1. V [Azure Portal](https://portal.azure.com/)vyberte server Azure Database for PostgreSQL.

2. Na panelu nástrojů na stránce **Přehled** serveru klikněte na **restartovat**.

   ![Azure Database for PostgreSQL – přehled – tlačítko restartovat](./media/howto-restart-server-portal/2-server.png)

3. Kliknutím na **Ano** potvrďte restartování serveru.

   ![Azure Database for PostgreSQL – potvrzení restartování](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Všimněte si, že se stav serveru změní na restart.

   ![Azure Database for PostgreSQL – stav restartování](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potvrzení restartování serveru bylo úspěšné.

   ![Azure Database for PostgreSQL – úspěšné restartování](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Další kroky

Informace o [Nastavení parametrů v Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)
---
title: Restartovat server – portál Azure – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete restartovat azure databáze pro PostgreSQL – jeden server pomocí portálu Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770080"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Restartování Azure Database for PostgreSQL – jeden server pomocí portálu Azure
Toto téma popisuje, jak můžete restartovat Azure Database pro PostgreSQL server. Pravděpodobně bude nutné restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server provádí operaci.

Restartování serveru bude blokováno, pokud je služba zaneprázdněna. Služba může například zpracovávat dříve požadovanou operaci, jako je například škálování virtuálních jader.
 
Doba potřebná k dokončení restartování závisí na procesu obnovení PostgreSQL. Chcete-li zkrátit dobu restartování, doporučujeme minimalizovat množství aktivity, ke kterým na serveru dochází před restartováním.

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro postgreSQL server](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Provedení restartování serveru

Následující kroky restartují server PostgreSQL:

1. Na [webu Azure Portal](https://portal.azure.com/)vyberte azure databázi pro postgreSQL server.

2. Na panelu nástrojů na stránce **Přehled** serveru klepněte na **tlačítko Restartovat**.

   ![Databáze Azure pro PostgreSQL – přehled – tlačítko Restartovat](./media/howto-restart-server-portal/2-server.png)

3. Klepnutím na tlačítko **Ano** potvrďte restartování serveru.

   ![Databáze Azure pro PostgreSQL – potvrzení restartování](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Všimněte si, že stav serveru se změní na "Restartování".

   ![Azure Database for PostgreSQL – stav restartování](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potvrďte, že restartování serveru bylo úspěšné.

   ![Databáze Azure pro PostgreSQL – úspěch restartování](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Další kroky

Informace o [nastavení parametrů v Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)
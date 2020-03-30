---
title: Restartovat server – portál Azure – databáze Azure pro MariaDB
description: Tento článek popisuje, jak můžete restartovat azure databáze pro mariadb server pomocí portálu Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed95304807a1a03880cc824c1a58f010203d418d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534707"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Restartování serveru Azure Database for MariaDB pomocí portálu Azure
Toto téma popisuje, jak můžete restartovat azure databáze pro mariadb server. Pravděpodobně bude nutné restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server provádí operaci.

Restartování serveru bude blokováno, pokud je služba zaneprázdněna. Služba může například zpracovávat dříve požadovanou operaci, jako je například škálování virtuálních jader.

Doba potřebná k dokončení restartování závisí na procesu obnovení MariaDB. Chcete-li zkrátit dobu restartování, doporučujeme minimalizovat množství aktivity, ke kterým na serveru dochází před restartováním.

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro server MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Provedení restartování serveru

Následující kroky restartují server MariaDB:

1. Na webu Azure Portal vyberte azure databázi pro server MariaDB.

2. Na panelu nástrojů na stránce **Přehled** serveru klepněte na **tlačítko Restartovat**.

   ![Databáze Azure pro MariaDB – přehled – tlačítko Restartovat](./media/howto-restart-server-portal/2-server.png)

3. Klepnutím na tlačítko **Ano** potvrďte restartování serveru.

   ![Databáze Azure pro MariaDB – potvrzení restartování](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Všimněte si, že stav serveru se změní na "Restartování".

   ![Azure Database for MariaDB – stav restartování](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potvrďte, že restartování serveru bylo úspěšné.

   ![Databáze Azure pro MariaDB – úspěšné restartování](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Další kroky

[Úvodní příručka: Vytvoření azure databáze pro server MariaDB pomocí portálu Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)
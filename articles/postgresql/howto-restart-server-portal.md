---
title: Restartovat – Azure Database for PostgreSQL pomocí webu Azure portal
description: Tento článek popisuje, jak je možné restartovat Azure Database for PostgreSQL pomocí webu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2018
ms.openlocfilehash: 7d409db839f94e27ac036550c22302188f37cc90
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545863"
---
# <a name="restart-azure-database-for-postgresql-server-using-azure-portal"></a>Restartovat – Azure Database for PostgreSQL pomocí webu Azure portal
Toto téma popisuje, jak je možné restartovat server Azure Database for PostgreSQL. Budete muset restartovat server z důvodů údržby, což způsobuje krátké výpadky, jak server provádí operaci.

Restartování serveru se zablokuje, pokud služba je zaneprázdněna. Například služba může zpracovávat dříve požadovaná operace, jako je například škálování virtuálních jader.
 
Čas potřebný k dokončení restartování závisí na proces obnovení PostgreSQL. Pokud chcete snížit čas restartování, doporučujeme, abyste že minimalizovali množství aktivit, k nimž došlo na serveru ještě před jejich restartování.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database for PostgreSQL server a databáze](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Provést restartování serveru

PostgreSQL server restartovat následující kroky:

1. Na webu Azure Portal vyberte váš server Azure Database for PostgreSQL.

2. Na panelu nástrojů na server **přehled** klikněte na **restartovat**.

   ![Azure Database for PostgreSQL – přehled – restartování tlačítko](./media/howto-restart-server-portal/2-server.png)

3. Klikněte na tlačítko **Ano** potvrďte restartování serveru.

   ![Azure Database for PostgreSQL – potvrzení restartování ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Podívejte se, že stav serveru změní na "Restartování".

   ![Azure Database for PostgreSQL – restartování stav ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potvrďte úspěšné restartování serveru.

   ![Azure Database for PostgreSQL – úspěšné restartování ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Další postup

[Rychlý start: Vytvoření Azure Database for PostgreSQL pomocí webu Azure portal](./quickstart-create-server-database-portal.md)
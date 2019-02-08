---
title: Restartovat – Azure Database for PostgreSQL pomocí webu Azure portal
description: Tento článek popisuje, jak je možné restartovat Azure Database for PostgreSQL pomocí webu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 28e99f64fdee414549c55f9666bfd53f07fb3efb
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892690"
---
# <a name="restart-azure-database-for-postgresql-server-using-azure-portal"></a>Restartovat – Azure Database for PostgreSQL pomocí webu Azure portal
Toto téma popisuje, jak je možné restartovat serveru Azure Database for PostgreSQL. Budete muset restartovat server z důvodů údržby, což způsobuje krátké výpadky, jak server provádí operaci.

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

[Rychlé zprovoznění: Vytvoření Azure Database for PostgreSQL pomocí webu Azure portal](./quickstart-create-server-database-portal.md)
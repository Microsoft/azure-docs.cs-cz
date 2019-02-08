---
title: Restartování – Azure Database pro MariaDB serveru pomocí webu Azure portal
description: Tento článek popisuje, jak je možné restartovat serveru Azure Database for MariaDB pomocí webu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 4b530ed2ffd32e2433a744b05aaab2219bf2b15a
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55896657"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Restartování – Azure Database pro MariaDB serveru pomocí webu Azure portal
Toto téma popisuje, jak můžete Azure Database pro MariaDB server restartovat. Budete muset restartovat server z důvodů údržby, což způsobuje krátké výpadky, jak server provádí operaci.

Restartování serveru se zablokuje, pokud služba je zaneprázdněna. Například služba může zpracovávat dříve požadovaná operace, jako je například škálování virtuálních jader.

Čas potřebný k dokončení restartování závisí na MariaDB proces obnovení. Pokud chcete snížit čas restartování, doporučujeme, abyste že minimalizovali množství aktivit, k nimž došlo na serveru ještě před jejich restartování.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database pro MariaDB serveru a databáze](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Provést restartování serveru

MariaDB server restartovat následující kroky:

1. Na webu Azure Portal vyberte váš server Azure Database for MariaDB.

2. Na panelu nástrojů na server **přehled** klikněte na **restartovat**.

   ![Azure Database pro MariaDB – přehled – restartování tlačítko](./media/howto-restart-server-portal/2-server.png)

3. Klikněte na tlačítko **Ano** potvrďte restartování serveru.

   ![Azure Database pro MariaDB – potvrzení restartování ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Podívejte se, že stav serveru změní na "Restartování".

   ![Azure Database pro MariaDB – restartování stav ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potvrďte úspěšné restartování serveru.

   ![Azure Database pro MariaDB – úspěšné restartování ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Další postup

[Rychlé zprovoznění: Vytvoření Azure Database pro MariaDB server pomocí webu Azure portal](./quickstart-create-mariadb-server-database-using-azure-portal.md)
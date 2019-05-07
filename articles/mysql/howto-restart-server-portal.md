---
title: Restartovat – Azure Database for MySQL pomocí webu Azure portal
description: Tento článek popisuje, jak je možné restartovat Azure Database for MySQL pomocí webu Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: a20030a1dc6cd8b89064731c283f9b462d30ec8f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "61422531"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Restartovat – Azure Database for MySQL pomocí webu Azure portal
Toto téma popisuje, jak je možné restartovat serveru Azure Database for MySQL. Budete muset restartovat server z důvodů údržby, což způsobuje krátké výpadky, jak server provádí operaci.

Restartování serveru se zablokuje, pokud služba je zaneprázdněna. Například služba může zpracovávat dříve požadovaná operace, jako je například škálování virtuálních jader.

Čas potřebný k dokončení restartování závisí na proces obnovení MySQL. Pokud chcete snížit čas restartování, doporučujeme, abyste že minimalizovali množství aktivit, k nimž došlo na serveru ještě před jejich restartování.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database for MySQL serveru](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Provést restartování serveru

MySQL server restartovat následující kroky:

1. Na webu Azure Portal vyberte váš server Azure Database for MySQL.

2. Na panelu nástrojů na server **přehled** klikněte na **restartovat**.

   ![Azure Database for MySQL – přehled – restartování tlačítko](./media/howto-restart-server-portal/2-server.png)

3. Klikněte na tlačítko **Ano** potvrďte restartování serveru.

   ![Azure Database for MySQL – potvrzení restartování](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Podívejte se, že stav serveru změní na "Restartování".

   ![Azure Database for MySQL – restartování stav](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potvrďte úspěšné restartování serveru.

   ![Azure Database for MySQL – úspěšné restartování](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Další postup

[Rychlé zprovoznění: Vytvoření Azure Database for MySQL pomocí webu Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md)

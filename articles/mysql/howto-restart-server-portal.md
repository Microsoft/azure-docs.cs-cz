---
title: Restartovat – Azure Database for MySQL pomocí webu Azure portal
description: Tento článek popisuje, jak je možné restartovat Azure Database for MySQL pomocí webu Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 6cf6679dc6398b112ffc964f50986b2ab30aba47
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882480"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Restartovat – Azure Database for MySQL pomocí webu Azure portal
Toto téma popisuje, jak je možné restartovat serveru Azure Database for MySQL. Budete muset restartovat server z důvodů údržby, což způsobuje krátké výpadky, jak server provádí operaci.

Restartování serveru se zablokuje, pokud služba je zaneprázdněna. Například služba může zpracovávat dříve požadovaná operace, jako je například škálování virtuálních jader.

Čas potřebný k dokončení restartování závisí na proces obnovení MySQL. Pokud chcete snížit čas restartování, doporučujeme, abyste že minimalizovali množství aktivit, k nimž došlo na serveru ještě před jejich restartování.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database for MySQL serveru a databáze](quickstart-create-mysql-server-database-using-azure-portal.md)

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
---
title: Restart serveru – Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak můžete restartovat server Azure Database for MySQL pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: aa79d1ba2cba86bf3f381ff78fd630ce455128d9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107865"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Restartování serveru Azure Database for MySQL pomocí Azure Portal
Toto téma popisuje, jak můžete restartovat server Azure Database for MySQL. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.

Čas potřebný k dokončení restartování závisí na procesu obnovení MySQL. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Provést restart serveru

Následující kroky restartují Server MySQL:

1. V Azure Portal vyberte server Azure Database for MySQL.

2. Na panelu nástrojů na stránce **Přehled** serveru klikněte na **restartovat**.

   ![Azure Database for MySQL – přehled – tlačítko restartovat](./media/howto-restart-server-portal/2-server.png)

3. Kliknutím na **Ano** potvrďte restartování serveru.

   ![Azure Database for MySQL – potvrzení restartování](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Všimněte si, že se stav serveru změní na restart.

   ![Azure Database for MySQL – stav restartování](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potvrzení restartování serveru bylo úspěšné.

   ![Azure Database for MySQL – úspěšné restartování](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Další kroky

[Rychlý Start: vytvoření serveru Azure Database for MySQL pomocí Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)

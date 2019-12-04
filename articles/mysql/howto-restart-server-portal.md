---
title: Restart serveru – Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak můžete restartovat server Azure Database for MySQL pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 1857c67a77b9600f3d8f7c222a8e06f899fba728
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774047"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Restartování serveru Azure Database for MySQL pomocí Azure Portal
Toto téma popisuje, jak můžete restartovat server Azure Database for MySQL. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.

Čas potřebný k dokončení restartování závisí na procesu obnovení MySQL. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Předpoklady
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

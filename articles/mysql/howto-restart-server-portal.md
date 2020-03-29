---
title: Restartovat server – portál Azure – azure databáze pro MySQL
description: Tento článek popisuje, jak můžete restartovat Azure Database for MySQL server pomocí portálu Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d7e158124347b302492364df46ccf5b5e78e75c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063290"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Restartujte Azure Database for MySQL server pomocí portálu Azure
Toto téma popisuje, jak můžete restartovat Azure Database for MySQL server. Pravděpodobně bude nutné restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server provádí operaci.

Restartování serveru bude blokováno, pokud je služba zaneprázdněna. Služba může například zpracovávat dříve požadovanou operaci, jako je například škálování virtuálních jader.

Doba potřebná k dokončení restartování závisí na procesu obnovení MySQL. Chcete-li zkrátit dobu restartování, doporučujeme minimalizovat množství aktivity, ke kterým na serveru dochází před restartováním.

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Provedení restartování serveru

Následující kroky restartují server MySQL:

1. Na webu Azure Portal vyberte azure databázi pro mysql server.

2. Na panelu nástrojů na stránce **Přehled** serveru klepněte na **tlačítko Restartovat**.

   ![Databáze Azure pro MySQL – přehled – tlačítko Restartovat](./media/howto-restart-server-portal/2-server.png)

3. Klepnutím na tlačítko **Ano** potvrďte restartování serveru.

   ![Azure Database for MySQL – potvrzení restartování](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Všimněte si, že stav serveru se změní na "Restartování".

   ![Azure Database for MySQL – stav restartování](./media/howto-restart-server-portal/4-restarting-status.png)

5. Potvrďte, že restartování serveru bylo úspěšné.

   ![Azure Database for MySQL – úspěšné restartování](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Další kroky

[Úvodní příručka: Vytvoření azure databáze pro server MySQL pomocí portálu Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)

---
title: Restart serveru-Azure Portal-Azure Database for MySQL-flexibilní Server
description: Tento článek popisuje, jak můžete restartovat Azure Database for MySQL flexibilní Server pomocí Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: c44e1da46b969b2d359a225e9d310160ce2092ce
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681470"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Restartování Azure Database for MySQL flexibilního serveru pomocí Azure Portal
Toto téma popisuje, jak můžete restartovat Azure Database for MySQL flexibilní Server. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.

Čas potřebný k dokončení restartování závisí na procesu obnovení MySQL. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Předpoklady
K dokončení tohoto průvodce budete potřebovat:
- [Azure Database for MySQL flexibilní Server](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Provést restart serveru

Následující kroky restartují Server MySQL:

1. V Azure Portal vyberte Azure Database for MySQL flexibilní Server.

2. Na panelu nástrojů na stránce **Přehled** serveru klikněte na **restartovat** .

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL – přehled – tlačítko restartovat":::

3. Kliknutím na **Ano** potvrďte restartování serveru.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL – přehled – tlačítko restartovat":::

4. Všimněte si, že se stav serveru změní na restart.

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL – přehled – tlačítko restartovat":::

5. Potvrzení restartování serveru bylo úspěšné.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL – přehled – tlačítko restartovat":::

## <a name="next-steps"></a>Další kroky

[Rychlý Start: vytvoření Azure Database for MySQL flexibilního serveru pomocí Azure Portal](quickstart-create-server-portal.md)

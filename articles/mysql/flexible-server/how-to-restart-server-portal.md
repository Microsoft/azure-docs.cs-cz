---
title: Restart serveru-Azure Portal-Azure Database for MySQL-flexibilní Server
description: Tento článek popisuje, jak můžete restartovat Azure Database for MySQL flexibilní Server pomocí Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: 88a1524875f168b49f50f1684c650d5bc178bf38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542656"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Restartování Azure Database for MySQL flexibilního serveru pomocí Azure Portal
Toto téma popisuje, jak můžete restartovat Azure Database for MySQL flexibilní Server. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.

Čas potřebný k dokončení restartování závisí na procesu obnovení MySQL. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Azure Database for MySQL flexibilní Server](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Provést restart serveru

Následující kroky restartují Server MySQL:

1. V Azure Portal vyberte Azure Database for MySQL flexibilní Server.

2. Na panelu nástrojů na stránce **Přehled** serveru klikněte na **restartovat**.

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL – přehled – tlačítko restartovat":::

3. Kliknutím na **Ano** potvrďte restartování serveru.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL – potvrzení restartování":::

4. Všimněte si, že se stav serveru změní na restart.

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL – stav restartování":::

5. Potvrzení restartování serveru bylo úspěšné.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL – úspěšné restartování":::

## <a name="next-steps"></a>Další kroky

[Rychlý Start: vytvoření Azure Database for MySQL flexibilního serveru pomocí Azure Portal](quickstart-create-server-portal.md)

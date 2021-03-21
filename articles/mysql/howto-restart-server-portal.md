---
title: Restart serveru – Azure Portal-Azure Database for MySQL
description: Tento článek popisuje, jak můžete restartovat server Azure Database for MySQL pomocí Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 2d19c0bd4ef5c49b8be82ffa11115ff6a1c6b302
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541789"
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

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL – přehled – tlačítko restartovat":::

3. Kliknutím na **Ano** potvrďte restartování serveru.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL – potvrzení restartování":::

4. Všimněte si, že se stav serveru změní na restart.

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL – stav restartování":::

5. Potvrzení restartování serveru bylo úspěšné.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL – úspěšné restartování":::

## <a name="next-steps"></a>Další kroky

[Rychlý Start: vytvoření serveru Azure Database for MySQL pomocí Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)

---
title: Správa serveru-Azure Portal-Azure Database for MySQL
description: Naučte se spravovat Azure Database for MySQL server z Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: e29e823834ec813a8389cea220cffc7633aa7103
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541449"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Správa serveru Azure Database for MySQL pomocí Azure Portal

V tomto článku se dozvíte, jak spravovat servery Azure Database for MySQL. Úlohy správy zahrnují výpočetní výkon a škálování úložiště, resetování hesla správce a zobrazení podrobností serveru.

## <a name="sign-in"></a>Přihlásit se

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-server"></a>Vytvoření serveru

Další informace o tom, jak vytvořit a začít s Azure Database for MySQL serverem, najdete v [rychlém](quickstart-create-mysql-server-database-using-azure-portal.md) startu.

## <a name="scale-compute-and-storage"></a>Škálování výpočetních prostředků a úložiště

Po vytvoření serveru můžete škálovat mezi Pro obecné účely a paměťově optimalizovanými úrovněmi podle vašich potřeb. Můžete také škálovat výpočetní výkon a paměť tím, že zvýšíte nebo zmenšíte virtuální jádra. Úložiště je možné škálovat nahoru (úložiště ale nemůžete škálovat dolů).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Škálování mezi Pro obecné účelymi a paměťově optimalizovanými úrovněmi

Můžete škálovat z Pro obecné účely na paměťově optimalizované a naopak. Změna na úroveň Basic až po vytvoření serveru není podporovaná.

1. V Azure Portal vyberte svůj server. Vyberte **cenovou úroveň** , která se nachází v části **Nastavení** .

2. Vyberte **optimalizované** **pro obecné účely** nebo paměť v závislosti na tom, na co se škáluje.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Snímek obrazovky Azure Portal pro výběr úrovně optimalizované pro paměť na úrovni Basic, Pro obecné účely nebo paměť v Azure Database for MySQL":::

   > [!NOTE]
   > Změna úrovní způsobí restart serveru.

3. Kliknutím na **tlačítko OK** uložte změny.

### <a name="scale-vcores-up-or-down"></a>Škálování virtuální jádra nahoru nebo dolů

1. V Azure Portal vyberte svůj server. Vyberte **cenovou úroveň** , která se nachází v části **Nastavení** .

2. Změňte nastavení **Vcore** přesunutím posuvníku na požadovanou hodnotu.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Snímek obrazovky Azure Portal pro výběr možnosti vCore v Azure Database for MySQL":::

    > [!NOTE]
    > Škálování virtuální jádra způsobí restartování serveru.

3. Kliknutím na **tlačítko OK** uložte změny.

### <a name="scale-storage-up"></a>Škálování úložiště nahoru

1. V Azure Portal vyberte svůj server. Vyberte **cenovou úroveň** , která se nachází v části **Nastavení** .

2. Změňte nastavení **úložiště** přesunutím posuvníku na požadovanou hodnotu.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Snímek obrazovky Azure Portal pro výběr škálování úložiště v Azure Database for MySQL":::

   > [!NOTE]
   > Velikost úložiště nejde škálovat.

3. Kliknutím na **tlačítko OK** uložte změny.

## <a name="update-admin-password"></a>Aktualizovat heslo správce

Můžete změnit heslo role správce pomocí Azure Portal.

1. V Azure Portal vyberte svůj server. V okně **Přehled** vyberte **resetovat heslo**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Snímek obrazovky Azure Portal pro resetování hesla v Azure Database for MySQL":::

2. Zadejte nové heslo a potvrďte ho. Textové pole vás vyzve k zadání požadavků na složitost hesla.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Snímek obrazovky Azure Portal pro resetování hesla a jeho uložení do Azure Database for MySQL":::

3. Kliknutím na **tlačítko OK** uložte nové heslo.

## <a name="delete-a-server"></a>Odstranění serveru

Server můžete odstranit, pokud ho už nepotřebujete.

1. V Azure Portal vyberte svůj server. V okně **Přehled** vyberte **Odstranit**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Snímek obrazovky Azure Portal, ze kterého se má server odstranit Azure Database for MySQL":::

2. Zadáním názvu serveru do vstupního pole potvrďte, že se jedná o server, který chcete odstranit.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Snímek obrazovky Azure Portal pro potvrzení odstranění serveru v Azure Database for MySQL":::

   > [!NOTE]
   > Odstranění serveru je nevratné.

3. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Další informace o [zálohování a obnovení serveru](howto-restore-server-portal.md)
- Seznamte [se s možnostmi ladění a monitorování v Azure Database for MySQL](concepts-monitoring.md)

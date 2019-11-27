---
title: Správa Azure Database for PostgreSQL-Azure Portal
description: Naučte se spravovat Azure Database for PostgreSQL Server z Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: bcddd64afca29ac9fdd5d284fc8f809ff9e2477d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535688"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Správa serveru Azure Database for PostgreSQL pomocí Azure Portal
V tomto článku se dozvíte, jak spravovat servery Azure Database for PostgreSQL. Úlohy správy zahrnují výpočetní výkon a škálování úložiště, resetování hesla správce a zobrazení podrobností serveru.

## <a name="sign-in"></a>Přihlášení
Přihlaste se na web [Azure Portal ](https://portal.azure.com).

## <a name="create-a-server"></a>Vytvoření serveru
Další informace o tom, jak vytvořit a začít s Azure Database for PostgreSQL serverem, najdete v [rychlém](quickstart-create-server-database-portal.md) startu.

## <a name="scale-compute-and-storage"></a>Škálování výpočetních prostředků a úložiště

Po vytvoření serveru můžete škálovat mezi Pro obecné účely a paměťově optimalizovanými úrovněmi podle vašich potřeb. Můžete také škálovat výpočetní výkon a paměť tím, že zvýšíte nebo zmenšíte virtuální jádra. Úložiště je možné škálovat nahoru (úložiště ale nemůžete škálovat dolů).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Škálování mezi Pro obecné účelymi a paměťově optimalizovanými úrovněmi

Můžete škálovat z Pro obecné účely na paměťově optimalizované a naopak. Změna na úroveň Basic až po vytvoření serveru není podporovaná. 

1. V Azure Portal vyberte svůj server. Vyberte **cenovou úroveň**, která se nachází v části **Nastavení** .

2. Vyberte **optimalizované** **pro obecné účely** nebo paměť v závislosti na tom, na co se škáluje. 

    ![Změna – cenová úroveň](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Změna úrovní způsobí restart serveru.

4. Kliknutím na **tlačítko OK** uložte změny.


### <a name="scale-vcores-up-or-down"></a>Škálování virtuální jádra nahoru nebo dolů

1. V Azure Portal vyberte svůj server. Vyberte **cenovou úroveň**, která se nachází v části **Nastavení** .

2. Změňte nastavení **Vcore** přesunutím posuvníku na požadovanou hodnotu.

    ![škálování – COMPUTE](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Škálování virtuální jádra způsobí restartování serveru.

3. Kliknutím na **tlačítko OK** uložte změny.


### <a name="scale-storage-up"></a>Škálování úložiště nahoru

1. V Azure Portal vyberte svůj server. Vyberte **cenovou úroveň**, která se nachází v části **Nastavení** .

2. Změňte nastavení **úložiště** přesunutím posuvníku na požadovanou hodnotu.

    ![škálování – úložiště](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Velikost úložiště nejde škálovat.

3. Kliknutím na **tlačítko OK** uložte změny.


## <a name="update-admin-password"></a>Aktualizovat heslo správce
Můžete změnit heslo role správce pomocí Azure Portal.

1. V Azure Portal vyberte svůj server. V okně **Přehled** vyberte **resetovat heslo**.

   ![overview](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Zadejte nové heslo a potvrďte ho. Textové pole vás vyzve k zadání požadavků na složitost hesla.

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Kliknutím na **tlačítko OK** uložte nové heslo.


## <a name="delete-a-server"></a>Odstranění serveru

Server můžete odstranit, pokud ho už nepotřebujete. 

1. V Azure Portal vyberte svůj server. V okně **Přehled** vyberte **Odstranit**.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Zadáním názvu serveru do vstupního pole potvrďte, že se jedná o server, který chcete odstranit.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Odstranění serveru je nevratné.

3. Vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky
- Další informace o [zálohování a obnovení serveru](howto-restore-server-portal.md)
- Seznamte [se s možnostmi ladění a monitorování v Azure Database for PostgreSQL](concepts-monitoring.md)

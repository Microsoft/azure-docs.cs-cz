---
title: 'Rychlý Start: Konfigurace izolace úloh – portál'
description: Pomocí Azure Portal můžete nakonfigurovat izolaci úloh pro vyhrazený fond SQL.
services: synapse-analytics
author: ronortloff
ms.author: rortloff
manager: craigg
ms.reviewer: jrasnick
ms.date: 05/04/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- azure-synapse
- mode-portal
ms.openlocfilehash: c15f21064012c195315a7f91908b3160591dc6f8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534253"
---
# <a name="quickstart-configure-dedicated-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Rychlý Start: Konfigurace vyhrazené úlohy fondu SQL pomocí skupiny úloh v Azure Portal

V tomto rychlém startu nakonfigurujete [izolaci úloh](sql-data-warehouse-workload-isolation.md) vytvořením skupiny úloh pro rezervaci prostředků.  Pro účely tohoto kurzu vytvoříme skupinu úloh pro volání načítání dat `DataLoads` . Skupina úloh bude rezervovat 20% systémových prostředků.  Díky 20% izolaci pro zatížení dat jsou zaručené prostředky, které jim umožňují vysáhnout SLA.  Po vytvoření skupiny úloh [vytvořte klasifikátor úloh](quickstart-create-a-workload-classifier-portal.md) pro přiřazení dotazů do této skupiny úloh.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.


## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Vytvoření vyhrazené instance fondu SQL ve službě Azure synapse Analytics může mít za následek novou fakturovatelnou službu.  Další informace najdete v tématu [ceny služby Azure synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu se předpokládá, že v synapse SQL už máte vyhrazenou instanci fondu SQL a že máte oprávnění k řízení databáze. Pokud ho potřebujete vytvořit, použijte [rychlý Start: vytvoření vyhrazeného fondu SQL – portál](../quickstart-create-sql-pool-portal.md) pro vytvoření datového skladu s názvem **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Aby bylo možné nakonfigurovat správu úloh, musí být váš vyhrazený fond SQL online. 

## <a name="configure-workload-isolation"></a>Konfigurovat izolaci úloh

Vyhrazené prostředky fondu SQL je možné izolovat a rezervovat pro konkrétní úlohy vytvořením skupin úloh.  Další podrobnosti o tom, jak skupiny úloh vám pomůžou při správě vašich úloh, najdete v dokumentaci k konceptu [izolace úloh](sql-data-warehouse-workload-isolation.md) .  Rychlý Start pro [Vytvoření a připojení – portál](create-data-warehouse-portal.md) vytvořil **mySampleDataWarehouse** a inicializuje ho na DW100c. Následující kroky vytvoří skupinu úloh v **mySampleDataWarehouse**.

Vytvoření skupiny úloh s izolací 20%:
1.  Přejděte na stránku **mySampleDataWarehouse** vyhrazený fond SQL..
1.  Vyberte možnost  **Správa úloh**.
1.  Vyberte **Nová skupina úloh**.
1.  Vyberte možnost **vlastní**.

    ![Kliknout na vlastní](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Jako `DataLoads` **skupinu úloh** zadejte.
7.  Zadejte `20` pro **min. Resources%**.
8.  Zadejte `5` pro **min. Resources% na požadavek**.
9.  Zadejte `100` pro **prostředky Cap%**.
10. Zadejte **Uložit**.

   ![Kliknutí na Uložit](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Po vytvoření skupiny úloh se zobrazí oznámení na portálu.  Prostředky skupiny úloh se zobrazí v grafech pod nakonfigurovanými hodnotami.

   ![Kliknout na finální](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odstranit `DataLoads` skupinu úloh vytvořenou v tomto kurzu:
1. Klikněte na **`...`** napravo od `DataLoads` skupiny úloh.
2. Klikněte na **Odstranit skupinu úloh**.
3. Po zobrazení výzvy k potvrzení odstranění skupiny úloh klikněte na **Ano** .
4. Klikněte na **Uložit**.

   ![Klikněte na Odstranit.](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Účtují se vám poplatky za jednotky datového skladu a data uložená v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky v době, kdy datový sklad nepoužíváte. Když pozastavíte výpočetní prostředky, bude se vám účtovat jenom úložiště dat. Až budete připraveni pracovat s daty, obnovte výpočetní výkon.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit.

Pomocí těchto kroků vyčistěte prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a vyberte vyhrazený fond SQL.

    ![Vyčištění prostředků](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, vyberte tlačítko **pozastavit** . Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Pokud chcete pokračovat v výpočetních prostředích, vyberte **Spustit**.

3. Pokud chcete odebrat datový sklad, aby se vám neúčtovaly výpočetní výkon nebo úložiště, vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Chcete-li použít `DataLoads` skupinu úloh, je nutné vytvořit [třídění úloh](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , aby bylo možné směrovat požadavky do skupiny úloh.  Pokračujte v kurzu [Vytvoření klasifikátoru úloh](quickstart-create-a-workload-classifier-portal.md) a vytvořte třídění úloh pro `DataLoads` .

## <a name="see-also"></a>Viz také
Podrobnosti o tom, jak monitorovat úlohy pro správu úloh, najdete v článku Jak [Spravovat a monitorovat správu úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) .

---
title: 'Rychlý Start: Konfigurace izolace úloh – portál'
description: Ke konfiguraci izolace úloh použijte Azure Portal.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 30862a0c16995e143df72f2a243419819941f54e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85213036"
---
# <a name="quickstart-configure-synapse-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Rychlý Start: Konfigurace izolace úloh synapse fondu SQL pomocí skupiny úloh v Azure Portal

V tomto rychlém startu nakonfigurujete [izolaci úloh](sql-data-warehouse-workload-isolation.md) vytvořením skupiny úloh pro rezervaci prostředků.  Pro účely tohoto kurzu vytvoříme skupinu úloh pro volání načítání dat `DataLoads` . Skupina úloh bude rezervovat 20% systémových prostředků.  Díky 20% izolaci pro zatížení dat jsou zaručené prostředky, které jim umožňují vysáhnout SLA.  Po vytvoření skupiny úloh [vytvořte klasifikátor úloh](quickstart-create-a-workload-classifier-portal.md) pro přiřazení dotazů do této skupiny úloh.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.


## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Vytvoření instance fondu SQL ve službě Azure synapse Analytics může mít za následek novou fakturovatelnou službu.  Další informace najdete v tématu [ceny služby Azure synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Předpoklady

V tomto rychlém startu se předpokládá, že již máte instanci fondu SQL v synapse SQL a máte kontrolu nad oprávněními databáze. Pokud ho potřebujete vytvořit, postupujte podle pokynů v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) a vytvořte datový sklad s názvem **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Aby bylo možné konfigurovat správu úloh, musí být váš fond SQL online. 

## <a name="configure-workload-isolation"></a>Konfigurovat izolaci úloh
Prostředky fondu SQL je možné izolovat a rezervovat pro konkrétní úlohy vytvořením skupin úloh.  Další podrobnosti o tom, jak skupiny úloh vám pomůžou při správě vašich úloh, najdete v dokumentaci k konceptu [izolace úloh](sql-data-warehouse-workload-isolation.md) .  Rychlý Start pro [Vytvoření a připojení – portál](create-data-warehouse-portal.md) vytvořil **mySampleDataWarehouse** a inicializoval ho pomocí 400 DWU. Následující kroky vytvoří skupinu úloh v **mySampleDataWarehouse**.

Vytvoření skupiny úloh s izolací 20%:
1.  Na levé straně Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
2.  Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** . Otevře se fond SQL.
3.  Klikněte na **Správa úloh**.
4.  Klikněte na **Nová skupina úloh**.
5.  Klikněte na **vlastní**.

    ![Kliknout na vlastní](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Jako `DataLoads` **skupinu úloh**zadejte.
7.  Zadejte `20` pro **min. Resources%**.
8.  Zadejte `5` pro **min. Resources% na požadavek**.
9.  Zadejte `100` pro **prostředky Cap%**.
10.   Klikněte na **Uložit**.

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

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a vyberte svůj datový sklad.

    ![Vyčištění prostředků](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, vyberte tlačítko **pozastavit** . Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Pokud chcete pokračovat v výpočetních prostředích, vyberte **Spustit**.

3. Pokud chcete odebrat datový sklad, aby se vám neúčtovaly výpočetní výkon nebo úložiště, vyberte **Odstranit**.

4. Pokud chcete odebrat vytvořený SQL Server, vyberte na předchozím obrázku **sqlpoolservername.Database.Windows.NET** a pak vyberte **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, vyberte **myResourceGroup**a pak vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Chcete-li použít `DataLoads` skupinu úloh, je nutné vytvořit [třídění úloh](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby bylo možné směrovat požadavky do skupiny úloh.  Pokračujte v kurzu [Vytvoření klasifikátoru úloh](quickstart-create-a-workload-classifier-portal.md) a vytvořte třídění úloh pro `DataLoads` .

## <a name="see-also"></a>Viz také
Podrobnosti o tom, jak monitorovat úlohy pro správu úloh, najdete v článku Jak [Spravovat a monitorovat správu úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) .

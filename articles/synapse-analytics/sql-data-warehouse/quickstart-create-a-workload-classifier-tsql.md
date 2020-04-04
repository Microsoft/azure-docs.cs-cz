---
title: 'Úvodní příručka: Vytvoření třídění pracovního vytížení – T-SQL'
description: Pomocí T-SQL vytvořte třídění úloh s vysokou důležitostí.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bcac6af9efd18ef8abeea7d82961fd8f2fe70ba3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633755"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Úvodní příručka: Vytvoření třídění pracovního vytížení pomocí T-SQL

V tomto rychlém startu rychle vytvoříte klasifikátor pracovního vytížení s vysokou důležitostí pro generálního ředitele vaší organizace. Tento třídění úlohy umožní dotazy generálního ředitele mít přednost před jinými dotazy s nižší význam ve frontě.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

> [!NOTE]
> Vytvoření synapse SQL fond instance v Azure Synapse Analytics může mít za následek novou fakturovatelnou službu.  Další informace najdete v [tématu Azure Synapse Analytics ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Požadavky

Tento rychlý start předpokládá, že již máte sql datový sklad a že máte oprávnění databáze control. Pokud ho potřebujete vytvořit, postupujte podle pokynů v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) a vytvořte datový sklad s názvem **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Vytvořit přihlášení pro THECEO

Vytvořte přihlášení ověřování sql `master` serveru v databázi pomocí [create login](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pro 'THECEO'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Vytvořit uživatele

[Vytvořit uživatele](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), "TheCEO", v mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Vytvoření třídění pracovního vytížení

Vytvořte [třídění pracovního vytížení](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pro "TheCEO" s vysokou důležitostí.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Zobrazit existující klasifikátory

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Účtují se vám jednotky datového skladu a data uložená v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky v době, kdy datový sklad nepoužíváte. Pozastavením výpočetních prostředků se vám bude účtovat jenom úložiště dat. Až budete připraveni pracovat s daty, pokračujte v výpočtu.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit.

Chcete-li vyčistit prostředky, postupujte takto.

1. Přihlaste se na [portál Azure](https://portal.azure.com), vyberte ve svém datovém skladu.

    ![Vyčištění prostředků](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Chcete-li pozastavit výpočetní výkon, vyberte tlačítko **Pozastavit.** Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Chcete-li pokračovat v výpočtu, vyberte **možnost Start**.

3. Pokud chcete datový sklad odebrat, abyste se neúčtovali za výpočetní prostředky nebo úložiště, vyberte **Odstranit**.

4. Chcete-li odebrat vytvořený server SQL, vyberte **mynewserver-20180430.database.windows.net** v předchozím obrázku a pak vyberte **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Chcete-li skupinu prostředků odebrat, vyberte **položku myResourceGroup**a potom vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

- Nyní jste vytvořili třídění úlohy. Spusťte několik dotazů jako THECEO a zjistěte, jak fungují. Viz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pro zobrazení dotazů a přiřazený význam.
- Další informace o správě úloh synapse SQL naleznete v [tématu Úloha důležitost](sql-data-warehouse-workload-importance.md) a [klasifikace pracovního vytížení](sql-data-warehouse-workload-classification.md).
- Podívejte se na články s postupy [pro konfiguraci důležitosti pracovního vytížení](sql-data-warehouse-how-to-configure-workload-importance.md) a [jak spravovat a monitorovat správu úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).

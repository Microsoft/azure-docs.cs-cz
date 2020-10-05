---
title: 'Rychlý Start: vytvoření klasifikátoru úloh – portál'
description: Pomocí Azure Portal můžete vytvořit klasifikátor úloh s vysokou důležitostí.
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
ms.openlocfilehash: 691cdcb525f8e9e3d1fb914372b9f62366f4bfba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85213019"
---
# <a name="quickstart-create-a-synapse-sql-pool-workload-classifier-using-the-azure-portal"></a>Rychlý Start: vytvoření klasifikátoru úloh synapse fondu SQL pomocí Azure Portal

V tomto rychlém startu vytvoříte [klasifikátor úloh](sql-data-warehouse-workload-classification.md) pro přiřazení dotazů ke skupině úloh.  Klasifikátor přiřadí požadavky od `ELTLogin` uživatele SQL do `DataLoads` skupiny úloh.   Postupujte podle kurzu [rychlý Start: Konfigurace izolace úloh](quickstart-configure-workload-isolation-portal.md) a vytvořte `DataLoads` skupinu úloh.  V tomto kurzu se vytvoří klasifikátor úloh s možností WLM_LABEL, aby bylo možné lépe klasifikovat požadavky správně.  Třídění bude také přiřazovat `HIGH` [význam úloh](sql-data-warehouse-workload-importance.md) i pro tyto požadavky.


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.


## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Vytvoření instance fondu SQL ve službě Azure synapse Analytics může mít za následek novou fakturovatelnou službu.  Další informace najdete v tématu [ceny služby Azure synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Předpoklady

V tomto rychlém startu se předpokládá, že již máte instanci fondu SQL v synapse SQL a máte kontrolu nad oprávněními databáze. Pokud ho potřebujete vytvořit, postupujte podle pokynů v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) a vytvořte datový sklad s názvem **mySampleDataWarehouse**.
<br><br>
Skupina úloh `DataLoads` existuje.  Informace o vytvoření skupiny úloh najdete v kurzu [rychlý Start: Konfigurace izolace úloh](quickstart-configure-workload-isolation-portal.md) .
<br><br>
>[!IMPORTANT] 
>Aby bylo možné konfigurovat správu úloh, musí být váš fond SQL online. 


## <a name="create-a-login-for-eltlogin"></a>Vytvoření přihlašovacích údajů pro ELTLogin

Vytvořte přihlašovací jméno SQL Server ověřování v `master` databázi pomocí [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) údajů pro `ELTLogin` .

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Vytvoření uživatele a udělení oprávnění

Po vytvoření přihlášení musí být v databázi vytvořen uživatel.  Pomocí [vytvořit uživatele](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) vytvořte uživatele SQL `ELTRole` v **mySampleDataWarehouse**.  Vzhledem k tomu, že během tohoto kurzu budeme testovat klasifikaci, udělte jim `ELTLogin` oprávnění k **mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Konfigurace klasifikace úloh
Klasifikace umožňuje směrovat požadavky na základě sady pravidel na skupinu úloh.  V části [rychlý Start: Konfigurace kurzu pro izolaci úloh](quickstart-configure-workload-isolation-portal.md) jsme vytvořili `DataLoads` skupinu úloh.  Nyní vytvoříte klasifikátor úloh pro směrování dotazů do `DataLoads` skupiny úloh.


1.  Na levé straně Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
2.  Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** . Otevře se fond SQL.
3.  Klikněte na **Správa úloh**.

    ![Klikněte na nabídku](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Klikněte na **nastavení & klasifikátory** na pravé straně `DataLoads` skupiny úloh.

    ![Kliknutí na Vytvořit](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Klikněte na **klasifikátory**.
6. Klikněte na **Přidat klasifikátor**.

    ![Klikněte na tlačítko Přidat.](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  `ELTLoginDataLoads`Jako **název**zadejte.
8.  Zadejte `ELTLogin` pro **člena**.
9.  Vyberte možnost `High` pro **důležitost požadavku**.  *Volitelné*, Normální důležitost je výchozí.
10. Zadejte `fact_loads` pro **popisek**.
11. Klikněte na **Přidat**.
12. Klikněte na **Uložit**.

    ![Klikněte na konfigurace.](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Ověřit a otestovat klasifikaci
Zkontrolujte zobrazení katalogu [Sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest) a ověřte existenci `ELTLoginDataLoads` třídění.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Zkontrolujte zobrazení katalogu [Sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest) a ověřte podrobnosti třídění.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Spusťte následující příkazy pro otestování klasifikace.  Ujistěte se, že jste připojeni jako ``ELTLogin`` a používáte ``Label`` v dotazu.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

Ověřte `CREATE TABLE` příkaz klasifikovaný ke `DataLoads` skupině úloh pomocí `ELTLoginDataLoads` klasifikátoru úloh.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```



## <a name="clean-up-resources"></a>Vyčištění prostředků

Postup odstranění `ELTLoginDataLoads` klasifikátoru úloh vytvořeného v tomto kurzu:

1. Na pravé straně skupiny úloh klikněte na **1 klasifikátor** `DataLoads` .

    ![Klikněte na Odstranit.](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Klikněte na **klasifikátory**.
3. Klikněte na tlačítko **`...`** napravo od `ELTLoginDataLoads` klasifikátoru úloh.
4. Klikněte na **Odstranit**.
5. Klikněte na **Uložit**.

    ![Kliknutí na Uložit](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

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

Monitorujte své úlohy pomocí Azure Portal metrik monitorování.  Podrobnosti najdete v tématu [Správa úloh a monitorování správy úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) .

---
title: 'Rychlý start: Vytvoření úlohy třídění – T-SQL | Dokumentace Microsoftu'
description: Použít T-SQL k vytvoření klasifikátoru úlohy s vysokou důležitostí
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 198faf6791a4a2caa2cefee2181a13ed8185310e
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617333"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql-preview"></a>Rychlý start: Vytvoření úlohy třídění pomocí jazyka T-SQL (Preview)

V tomto rychlém startu rychle vytvoříte klasifikátor úlohy s vysokou důležitostí pro generální ředitel vaší organizace. Tato úloha třídění vám umožní dotazy generální ředitel pro přednost před další dotazy s důležitostí nižší ve frontě.

> [!Note]
> Úloha klasifikace je dostupná ve verzi preview na SQL Data Warehouse Gen2. Klasifikace úlohy správy a význam ve verzi preview je pro sestavení s datem vydání verze z 9. dubna 2019 nebo novější.  Uživatelé byste neměli používat sestavení starší než toto datum pro testování úloh správy.  K určení, zda je sestavení umožňující správu úloh, spustit, vyberte @@version při připojení k vaší instanci SQL Data Warehouse.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!NOTE]
> Vytvoření služby SQL Data Warehouse může znamenat, že se vám začne fakturovat nová služba.  Další informace najdete v tématu [SQL Data Warehouse – ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Požadavky

Tento rychlý start předpokládá už máte datový sklad SQL a zda máte oprávnění CONTROL DATABASE. Pokud ho potřebujete vytvořit, postupujte podle pokynů v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) a vytvořte datový sklad s názvem **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Vytvořit přihlášení pro TheCEO

Vytvořit účet ověřování SQL serveru v `master` databázi s použitím [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) pro "TheCEO".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Vytvořit uživatele

[Vytvoření uživatele](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "TheCEO" v mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Vytvoření úlohy třídění

Vytvoření [úlohy třídění](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) pro "TheCEO" s vysokou důležitostí.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Zobrazit existující třídění

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Už se neúčtují poplatky za jednotky datového skladu a data uložená v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky v době, kdy datový sklad nepoužíváte. Když pozastavíte výpočetní prostředky, jen za úložný prostor. Až budete připraveni začít pracovat s daty, obnovit výpočty.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit.

Postupujte podle těchto kroků pro vyčištění prostředků.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com), vyberte na váš datový sklad.

    ![Vyčištění prostředků](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Chcete-li pozastavit výpočetní prostředky, vyberte **pozastavit** tlačítko. Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Chcete-li obnovit výpočetní prostředky, vyberte **Start**.

3. Chcete-li odebrat datový sklad, aby se vám neúčtovaly výpočetní prostředky ani prostředky úložiště, vyberte **odstranit**.

4. Chcete-li odstranit server SQL, který jste vytvořili, vyberte **mynewserver-20180430.database.windows.net** v předchozím obrázku a pak vyberte **odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Chcete-li odebrat skupinu prostředků, vyberte **myResourceGroup**a pak vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

Právě jste vytvořili úlohu třídění. Spustili jste několik dotazů jako TheCEO zobrazíte jejich výkon. Zobrazit [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) k zobrazení dotazů a závažnosti přiřazené.

Další informace o správě úloh datového skladu SQL, najdete v části [SQL Data Warehouse úlohy význam](sql-data-warehouse-workload-importance.md) a [SQL Data Warehouse úloh klasifikace](sql-data-warehouse-workload-classification.md).

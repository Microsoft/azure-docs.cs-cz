---
title: 'Úvodní příručka: Konfigurace izolace pracovního vytížení – T-SQL'
description: Pomocí T-SQL nakonfigurujte izolaci pracovního vytížení.
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
ms.openlocfilehash: 555d437fb0ee898473b37febb1774924b55bfa1d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350847"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Rychlý start: Konfigurace izolace pracovního vytížení pomocí T-SQL

V tomto rychlém startu rychle vytvoříte skupinu úloh a třídění pro rezervaci prostředků pro načítání dat. Skupina pracovního vytížení přidělí 20 % systémových prostředků nanačtení dat.  Třídění úlohpřiřuje požadavky skupině zatížení zatížení dat.  S 20% izolace pro zatížení dat, jsou zaručené prostředky pro přístupové služby SLA.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

> [!NOTE]
> Vytvoření instance SQL Analytics v Azure Synapse Analytics může mít za následek novou fakturovatelnou službu.  Další informace najdete v [tématu Azure Synapse Analytics ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Požadavky
 
Tento rychlý start předpokládá, že už máte instanci SQL Analytics v Azure Synapse a že máte oprávnění databáze CONTROL. Pokud ho potřebujete vytvořit, postupujte podle pokynů v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) a vytvořte datový sklad s názvem **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Vytvořit přihlášení pro DataLoads

Vytvořte přihlášení ověřování sql `master` serveru v databázi pomocí [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) pro 'ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Vytvořit uživatele

[Vytvořit uživatele](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "ELTLogin", v mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Vytvoření skupiny pracovních vytížení
Vytvořte [skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) pro DataLoads s 20 % izolace.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Vytvoření třídění pracovního vytížení

Vytvořte [třídění úlohy](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) pro mapování ELTLogin na skupinu zatížení DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Zobrazení existujících skupin úloh a klasifikátorů a hodnot za běhu

```sql
--Workload groups
SELECT * FROM 
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM 
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM 
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Účtují se vám jednotky datového skladu a data uložená v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky, když nepoužíváte fond SQL. Pozastavením výpočetních prostředků se vám bude účtovat jenom úložiště dat. Až budete připraveni pracovat s daty, pokračujte v výpočtu.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit.

Chcete-li vyčistit prostředky, postupujte takto.

1. Přihlaste se na [portál Azure](https://portal.azure.com), vyberte ve svém datovém skladu.

    ![Vyčištění prostředků](./media/quickstart-configure-workload-isolation-tsql/clean-up-resources.png)

2. Chcete-li pozastavit výpočetní výkon, vyberte tlačítko **Pozastavit.** Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Chcete-li pokračovat v výpočtu, vyberte **možnost Start**.

3. Pokud chcete datový sklad odebrat, abyste se neúčtovali za výpočetní prostředky nebo úložiště, vyberte **Odstranit**.

4. Chcete-li odebrat vytvořený server SQL, vyberte **mynewserver-20180430.database.windows.net** v předchozím obrázku a pak vyberte **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Chcete-li skupinu prostředků odebrat, vyberte **položku myResourceGroup**a potom vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

- Nyní jste vytvořili skupinu úloh. Spusťte několik dotazů jako ELTLogin a zjistěte, jak si vedou. Viz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) pro zobrazení dotazů a přiřazené skupiny úloh.
- Další informace o správě úloh SQL Analytics najdete v [tématu Správa pracovních vytížení](sql-data-warehouse-workload-management.md) a [Izolace pracovních vytížek](sql-data-warehouse-workload-isolation.md).

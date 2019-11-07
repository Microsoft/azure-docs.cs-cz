---
title: 'Rychlý Start: Konfigurace izolace úloh – T-SQL'
description: Pomocí T-SQL nakonfigurujte izolaci úloh.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 10/29/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 92f8aaad1cc3279142d419faa2852406c2956595
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685984"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Rychlý Start: Konfigurace izolace úloh pomocí T-SQL

V tomto rychlém startu budete rychle vytvořit skupinu úloh a třídění pro rezervaci prostředků pro načtení dat. Skupina úloh přidělí 20% systémových prostředků k načtení dat.  Klasifikátor úlohy přiřadí požadavky do skupiny úloh načíst data.  Díky 20% izolaci pro zatížení dat jsou zaručeny prostředky k Slaí.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!NOTE]
> Vytvoření služby SQL Data Warehouse může znamenat, že se vám začne fakturovat nová služba.  Další informace najdete v tématu [SQL Data Warehouse – ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Požadavky
 
V tomto rychlém startu se předpokládá, že již máte SQL Data Warehouse a že máte oprávnění k řízení databáze. Pokud ho potřebujete vytvořit, postupujte podle pokynů v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) a vytvořte datový sklad s názvem **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlásit se na [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Vytvoření přihlašovacích údajů pro dataloades

Vytvořte přihlašovací jméno SQL Server ověřování v databázi `master` pomocí příkazu [vytvořit přihlášení](/sql/t-sql/statements/create-login-transact-sql) pro ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Vytvořit uživatele

[Vytvoření uživatele](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "ELTLogin", v mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Vytvoření skupiny úloh
Vytvořte [skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) pro dataloades s izolací 20%.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Vytvoření klasifikátoru úloh

Vytvořte [klasifikátor úloh](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) pro mapování ELTLogin na skupinu úloh dataloads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'ELTLogin'
      ,MEMBERNAME = 'DataLoads')
;
```

## <a name="view-existing-workload-groups-and-classifiers"></a>Zobrazení existujících skupin úloh a klasifikátorů

```sql
SELECT * FROM 
sys.workload_management_workload_groups

SELECT * FROM 
sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Účtují se vám poplatky za jednotky datového skladu a data uložená v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete zachovat data v úložišti, můžete pozastavit výpočetní prostředky v době, kdy datový sklad nepoužíváte. Když pozastavíte výpočetní prostředky, bude se vám účtovat jenom úložiště dat. Až budete připraveni pracovat s daty, obnovte výpočetní výkon.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit.

Pomocí těchto kroků vyčistěte prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a vyberte svůj datový sklad.

    ![Vyčištění prostředků](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Pokud chcete pozastavit výpočetní prostředky, vyberte tlačítko **pozastavit** . Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Pokud chcete pokračovat v výpočetních prostředích, vyberte **Spustit**.

3. Pokud chcete odebrat datový sklad, aby se vám neúčtovaly výpočetní výkon nebo úložiště, vyberte **Odstranit**.

4. Pokud chcete odebrat vytvořený SQL Server, vyberte na předchozím obrázku **MyNewServer-20180430.Database.Windows.NET** a pak vyberte **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, vyberte **myResourceGroup**a pak vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

- Nyní jste vytvořili skupinu úloh. Spusťte několik dotazů jako ELTLogin, abyste viděli, jak fungují. V tématu [Sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) můžete zobrazit dotazy a přiřazenou skupinu úloh.
- Další informace o správě úloh Azure SQL Data Warehouse najdete v tématu věnovaném [správě](sql-data-warehouse-workload-management.md) úloh a [izolaci úloh](sql-data-warehouse-workload-isolation.md).

---
title: Zablokování příkazů T-SQL k vytvoření nebo úpravě prostředků Azure SQL
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: Tento článek podrobně popisuje funkci, která správcům Azure umožňuje blokovat příkazy T-SQL k vytvoření nebo úpravě prostředků Azure SQL.
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556080"
---
# <a name="what-is-block-t-sql-crud-feature"></a>Co je funkce pro CRUD T-SQL?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


Tato funkce umožňuje správcům Azure blokovat vytváření nebo úpravu prostředků Azure SQL prostřednictvím T-SQL. To se vynutilo na úrovni předplatného, aby se zablokovaly příkazy T-SQL, které mají vliv na prostředky SQL v jakékoli databázi Azure SQL nebo ve spravované instanci.

## <a name="overview"></a>Přehled

Chcete-li zablokovat vytváření nebo úpravu prostředků prostřednictvím T-SQL a vymáhat správu prostředků prostřednictvím šablony Azure Resource Manager (šablona ARM) pro dané předplatné, můžete použít funkce verze Preview v Azure Portal. To je užitečné hlavně v případě, že používáte [zásady Azure](/azure/governance/policy/overview) k prosazování standardů organizace prostřednictvím šablon ARM. Vzhledem k tomu, že T-SQL nedodržuje zásady Azure, je možné použít blok pro operace vytvoření nebo změny T-SQL. Zablokovaná syntaxe obsahuje příkazy CRUD (Create, Update, DELETE) pro databáze v Azure SQL, konkrétně v příkazech `CREATE DATABASE` `ALTER DATABASE` a `DROP DATABASE` . 

Operace CRUD jazyka T-SQL lze zablokovat prostřednictvím Azure Portal, [PowerShellu](/powershell/module/az.resources/register-azproviderfeature)nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/feature#az_feature_register).

## <a name="permissions"></a>Oprávnění

Aby bylo možné tuto funkci zaregistrovat nebo odebrat, musí být uživatel Azure členem role vlastníka nebo přispěvatele daného předplatného.

## <a name="examples"></a>Příklady

Následující část popisuje, jak můžete zaregistrovat nebo zrušit registraci funkce verze Preview u poskytovatele prostředků Microsoft. SQL v Azure Portal: 

### <a name="register-block-t-sql-crud"></a>Registrovat blok T-SQL CRUD

1. V Azure Portal můžete přejít na své předplatné.
2. Vyberte kartu **funkce ve verzi Preview** . 
3. Vyberte možnost **zablokovat CRUD T-SQL**.
4. Jakmile vyberete možnost **zablokovat CRUD T-SQL**, otevře se nové okno, vyberte **Registrovat** a zaregistrujte tento blok u poskytovatele prostředků Microsoft. SQL.

![V seznamu funkcí verze Preview vyberte "zablokovat CRUD T-SQL".](./media/block-tsql-crud/block-tsql-crud.png)

![Pokud je zaškrtnuté políčko zablokovat CRUD T-SQL CRUD, vyberte registrovat.](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Znovu zaregistrujte poskytovatele prostředků Microsoft. SQL. 
Po registraci bloku T-SQL CRUD pomocí poskytovatele prostředků Microsoft. SQL je nutné znovu zaregistrovat poskytovatele prostředků Microsoft. SQL, aby se změny projevily. Opětovné zaregistrování poskytovatele prostředků Microsoft. SQL:

1. V Azure Portal můžete přejít na své předplatné.
2. Vyberte kartu **poskytovatelé prostředků** .
3. Vyhledejte a vyberte poskytovatele prostředků **Microsoft. SQL** .
4. Vyberte **znovu registrovat**. 

> [!NOTE]
> Krok pro opakovanou registraci je povinný pro použití bloku T-SQL pro vaše předplatné. 

![Znovu zaregistrujte poskytovatele prostředků Microsoft. SQL.](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>Odebírá se blok CRUD T-SQL.
Pokud chcete odebrat blok operací T-SQL Create nebo Modify z vašeho předplatného, nejdřív zrušte registraci dříve registrovaného bloku T-SQL. Potom znovu zaregistrujte poskytovatele prostředků Microsoft. SQL, jak je znázorněno výše, aby se projevilo odebrání bloku T-SQL. 


## <a name="next-steps"></a>Další kroky

- [Přehled možností zabezpečení Azure SQL Database](security-overview.md)
- [Osvědčené postupy zabezpečení Azure SQL Database](security-best-practice.md)

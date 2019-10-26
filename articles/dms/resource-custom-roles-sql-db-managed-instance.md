---
title: Vlastní role pro SQL Server pro SQL Database migrace spravované instance online | Microsoft Docs
description: Naučte se používat vlastní role pro SQL Server k SQL Database migrací spravované instance online.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: 8148e029bf343613a230b20d0397fa7851c96712
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952352"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Vlastní role pro SQL Server pro SQL Database migrace spravovaných instancí online

Azure Database Migration Service používá ID aplikace pro interakci se službami Azure. ID aplikace vyžaduje buď roli přispěvatel na úrovni předplatného (kterou nepovoluje mnoho podnikových bezpečnostních oddělení), nebo vytváření vlastních rolí, které udělují specifická oprávnění, která služba Azure Database Migration vyžaduje. Vzhledem k tomu, že v Azure Active Directory existuje limit 2 000 vlastních rolí, možná budete chtít zkombinovat všechna oprávnění požadovaná konkrétně ID aplikace do jedné nebo dvou vlastních rolí a potom udělit ID aplikace vlastní roli pro konkrétní objekty nebo skupiny prostředků (vs. v rámci předplatných. úroveň Popi). Pokud počet vlastních rolí není obavou, můžete vlastní role rozdělit podle typu prostředku a vytvořit tak tři vlastní role celkem, jak je popsáno níže.

Část AssignableScopes řetězce JSON definice role umožňuje řídit, kde se oprávnění zobrazí v uživatelském rozhraní **přiřazení role** na portálu. Pravděpodobně budete chtít definovat roli na skupině prostředků nebo dokonce na úrovni prostředků, abyste zabránili zbytečnému uživatelskému rozhraní s dalšími rolemi. Všimněte si, že se neprovádí skutečné přiřazení role.

## <a name="minimum-number-of-roles"></a>Minimální počet rolí

V současnosti doporučujeme vytvořit minimálně dvě vlastní role pro ID aplikace, jednu na úrovni prostředku a druhou na úrovni předplatného.

> [!NOTE]
> Poslední požadavek na vlastní roli možná nebude možné odebrat, protože se do Azure nasadí nový kód spravované instance SQL Database.

**Vlastní role pro ID aplikace** Tato role se vyžaduje pro Azure Database Migration Service migrace na úrovni *prostředku* nebo *skupiny prostředků* (Další informace o ID aplikace najdete v článku [použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, která má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)).

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Vlastní role pro ID aplikace – předplatné** Tato role je nutná pro Azure Database Migration Service migrace na úrovni *předplatného* .

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

Výše uvedený formát JSON musí být uložen ve třech textových souborech a můžete použít buď AzureRM, AZ PowerShell rutiny nebo Azure CLI, a vytvořit tak role pomocí rutiny **New-AzureRmRoleDefinition (AzureRM)** nebo **New-AzRoleDefinition (AZ)** .

Další informace najdete v článku [vlastní role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Po vytvoření těchto vlastních rolí je nutné přidat přiřazení rolí uživatelům a ID aplikací k příslušným prostředkům nebo skupinám prostředků:

* Roli "DMS role – ID aplikace" musí být udělené ID aplikace, které se bude používat pro migrace, a také na úrovni účtu úložiště, Azure Database Migration Service instance a SQL Database úrovně prostředků spravované instance.
* Role "DMS role – ID aplikace-sub" musí být udělena ID aplikace na úrovni předplatného (přidělení u prostředku nebo skupiny prostředků se nezdaří). Tento požadavek je dočasný, dokud není nasazena aktualizace kódu.

## <a name="expanded-number-of-roles"></a>Rozšířený počet rolí

Pokud se počet vlastních rolí v Azure Active Directory netýká, doporučujeme vytvořit celkem tři role. Pořád budete potřebovat roli "DMS role-App ID – sub", ale výše uvedená role "DMS role-App ID" je rozdělená podle typu prostředku do dvou různých rolí.

**Vlastní role pro ID aplikace SQL Database Managed instance**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Vlastní role pro ID aplikace pro úložiště**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Přiřazení role

Pokud chcete přiřadit roli uživatelům nebo ID aplikace, otevřete Azure Portal, proveďte následující kroky:

1. Přejděte do skupiny prostředků nebo prostředku (s výjimkou role, kterou je třeba pro předplatné udělit), přejděte na **Access Control**a potom se posuňte, abyste našli vlastní role, které jste právě vytvořili.

2. Vyberte příslušnou roli, vyberte ID aplikace a pak změny uložte.

  Vaše ID vaší aplikace se teď zobrazují na kartě **přiřazení rolí** .

## <a name="next-steps"></a>Další kroky

* Přečtěte si pokyny k migraci vašeho scénáře v [příručce k migraci databáze](https://datamigration.microsoft.com/)Microsoft.

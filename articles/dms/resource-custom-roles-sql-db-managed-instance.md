---
title: 'Vlastní role: Migrace instancí spravované online SQL Serverem na SQL'
titleSuffix: Azure Database Migration Service
description: Naučte se používat vlastní role pro SQL Server na Azure SQL Database spravované instance online migrace.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254948"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Vlastní role pro migrace online instancí spravované službou SQL Server do SQL Database Database

Služba migrace databáze Azure používá ID aplikace k interakci se službami Azure Services. ID aplikace vyžaduje buď roli přispěvatele na úrovni předplatného (což mnoho podnikových oddělení zabezpečení nepovolí) nebo vytvoření vlastních rolí, které udělují konkrétní oprávnění, která vyžaduje služba Migrace databáze Azure. Vzhledem k tomu, že ve službě Azure Active Directory je limit 2 000 vlastních rolí, můžete chtít zkombinovat všechna oprávnění vyžadovaná konkrétně ID aplikace do jedné nebo dvou vlastních rolí a pak udělit ID aplikace vlastní roli pro konkrétní objekty nebo skupiny prostředků (vs. v aplikaci předplatného). Pokud počet vlastních rolí není problém, můžete rozdělit vlastní role podle typu prostředku a vytvořit celkem tři vlastní role, jak je popsáno níže.

Část AssignableScopes řetězce json definice role umožňuje určit, kde se oprávnění zobrazí v uzlení **Přidat přiřazení role** na portálu. Pravděpodobně budete chtít definovat roli ve skupině prostředků nebo dokonce na úrovni prostředků, abyste zabránili zahlcení hlavního rozhraní s dalšími rolemi. Všimněte si, že to neprovádí skutečné přiřazení role.

## <a name="minimum-number-of-roles"></a>Minimální počet rolí

V současné době doporučujeme vytvořit minimálně dvě vlastní role pro ID aplikace, jednu na úrovni prostředků a druhou na úrovni předplatného.

> [!NOTE]
> Poslední požadavek na vlastní roli může být nakonec odebrán, protože nový kód spravované instance SQL Database se nasazuje do Azure.

**Vlastní role pro ID aplikace**. Tato role je vyžadována pro migraci služby Migrace databáze Azure na úrovni *prostředků* nebo *skupiny prostředků* (další informace o ID aplikace najdete v článku [Pomocí portálu vytvořte aplikaci Azure AD a instanční objekt, který má přístup k prostředkům).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

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

**Vlastní role pro ID aplikace - předplatné**. Tato role je vyžadována pro migraci služby Migrace databáze Azure na úrovni *předplatného.*

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

Výše uvedený json musí být uložen ve třech textových souborech a můžete použít rutiny AzureRM, AZ PowerShell nebo Azure CLI k vytvoření rolí pomocí **new-azurermroledefinition (AzureRM)** nebo **new-azroledefinition (AZ)**.

Další informace najdete v článku [Vlastní role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Po vytvoření těchto vlastních rolí je nutné přidat přiřazení rolí uživatelům a ID aplikací k příslušným prostředkům nebo skupinám prostředků:

* Role "DMS Role – ID aplikace" musí být udělena ID aplikace, které se bude používat pro migrace, a také na úrovni prostředků storage account, Azure Database Migration Service a SQL Database spravované instance.
* Role "DMS Role - App ID - Sub" musí být udělena ID aplikace na úrovni předplatného (udělení na prostředek nebo skupinu prostředků se nezdaří). Tento požadavek je dočasný, dokud není nasazena aktualizace kódu.

## <a name="expanded-number-of-roles"></a>Rozšířený počet rolí

Pokud počet vlastních rolí ve službě Azure Active Directory není problém, doporučujeme vytvořit celkem tři role. Stále budete potřebovat roli "Role DMS – ID aplikace – sub", ale výše uvedená role "DMS Role – ID aplikace" je rozdělena podle typu prostředku do dvou různých rolí.

**Vlastní role pro instanci spravované aplikací APP ID pro databázi SQL**

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

Pokud chcete přiřadit roli uživatelům/ID aplikace APP, otevřete portál Azure, proveďte následující kroky:

1. Přejděte do skupiny prostředků nebo prostředku (s výjimkou role, která musí být udělena v předplatném), přejděte na **řízení přístupu**a potom přejděte a vyhledejte vlastní role, které jste právě vytvořili.

2. Vyberte příslušnou roli, vyberte ID aplikace a uložte změny.

  Vaše ID aplikace se nyní zobrazí na kartě **Přiřazení rolí.**

## <a name="next-steps"></a>Další kroky

* Přečtěte si pokyny pro migraci pro váš scénář v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft .

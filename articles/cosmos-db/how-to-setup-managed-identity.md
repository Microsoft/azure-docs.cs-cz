---
title: Konfigurace spravovaných identit pomocí Azure AD pro účet Azure Cosmos DB
description: Naučte se konfigurovat spravované identity pomocí Azure Active Directory pro účet Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231390"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Konfigurace spravovaných identit pomocí Azure Active Directory pro účet Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro účty Azure Cosmos DB.

> [!NOTE]
> Azure Cosmos DB aktuálně podporuje pouze spravované identity přiřazené systémem.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si téma [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md). Další informace o spravovaných typech identit najdete v tématu [typy spravovaných identit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Aby bylo možné nastavit spravované identity, musí mít váš účet [roli Přispěvatel účtu DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor).

## <a name="add-a-system-assigned-identity"></a>Přidat identitu přiřazenou systémem

### <a name="using-an-azure-resource-manager-arm-template"></a>Použití šablony Azure Resource Manager (ARM)

> [!IMPORTANT]
> `apiVersion` `2021-03-15` Při práci se spravovanými identitami nezapomeňte použít nebo vyšší.

Pokud chcete pro nový nebo existující účet Azure Cosmos DB povolit identitu přiřazenou systémem, zahrňte do definice prostředku tuto vlastnost:

```json
"identity": {
    "type": "SystemAssigned"
}
```

`resources`Oddíl šablony ARM by měl vypadat takto:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

Po vytvoření nebo aktualizaci účtu Azure Cosmos DB se zobrazí následující vlastnost:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="next-steps"></a>Další kroky

- Další informace o [spravovaných identitách pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md)
- Další informace o [klíčích spravovaných zákazníkem na Azure Cosmos DB](how-to-setup-cmk.md)

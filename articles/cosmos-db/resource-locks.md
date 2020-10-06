---
title: Zabránit odstranění nebo změně prostředků Azure Cosmos DB
description: Pomocí zámků prostředků Azure zabráníte odstranění nebo změně prostředků Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: b23fb22a99705e1bf37de7a7982513ff692d8f6a
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771034"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Zabránit odstranění nebo změně prostředků Azure Cosmos DB

Jako správce možná budete muset Uzamknout účet Azure Cosmos, databázi nebo kontejner, aby ostatní uživatelé ve vaší organizaci nemohli omylem odstranit nebo upravit důležité prostředky. Zámek můžete nastavit na úroveň CanNotDelete nebo ReadOnly.

- **CanNotDelete** znamená, že autorizovaní uživatelé můžou pořád číst a upravovat prostředek, ale nemůžou prostředek odstranit.
- **ReadOnly** znamená, že autorizovaní uživatelé můžou číst prostředek, ale nemůžou prostředek odstranit ani aktualizovat. Použití tohoto zámku je podobné jako omezení všech autorizovaných uživatelů na oprávnění udělené rolí čtenář.

## <a name="how-locks-are-applied"></a>Jak se používají zámky

Když použijete zámek v nadřazeném oboru, všechny prostředky v tomto oboru zdědí stejný zámek. I prostředky, které později přidáte, zdědí zámek nadřazeného objektu. Nejvíce omezující zámek v dědičnosti má přednost.

Na rozdíl od řízení přístupu na základě role slouží zámky správy k nastavení daného omezení u všech uživatelů a rolí. Další informace o RBAC pro Azure Cosmos DB najdete [v tématu řízení přístupu na základě role v Azure Cosmos DB](role-based-access-control.md).

Zámky služby Resource Manager se vztahují jen na operace, které probíhají v rovině správy, tedy operace odesílané na https://management.azure.com. Tyto zámky nijak neomezují, jak prostředky vykonávají své vlastní funkce. Omezené jsou změny prostředků, ale ne jejich operace. Například zámek jen pro čtení na kontejneru Azure Cosmos vám znemožní odstranit nebo upravit kontejner. Nebrání v vytváření, aktualizaci nebo odstraňování dat v kontejneru. Datové transakce jsou povolené, protože tyto operace se neodesílají na https://management.azure.com.

## <a name="manage-locks"></a>Spravovat zámky

> [!WARNING]
> Zámky prostředků nefungují pro změny provedené uživateli, kteří přistupují k Azure Cosmos DB pomocí klíčů účtu, pokud není účet Azure Cosmos nejdříve uzamčen tím, že povolíte vlastnost disableKeyBasedMetadataWriteAccess. Před tím, než povolíte tuto vlastnost, je třeba dbát na to, aby nedošlo k přerušení stávajících aplikací, které provedou změny prostředků pomocí sady SDK, Azure Portal nebo nástrojů třetích stran, které se připojují prostřednictvím klíčů účtů a mění prostředky, jako je například změna propustnosti, aktualizace zásad indexování atd. Další informace a Projděte si kontrolní seznam, abyste měli jistotu, že vaše aplikace budou dál fungovat, najdete v tématu [zabránění změnám v sadách Azure Cosmos DB SDK](role-based-access-control.md#prevent-sdk-changes) .

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure CLI

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Šablona

Při použití zámku na prostředek Azure Cosmos DB použijte následující formáty:

- název `{resourceName}/Microsoft.Authorization/{lockName}`
- textový `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> Když upravujete existující účet Azure Cosmos, nezapomeňte při redploying s touto vlastností zahrnout do svého účtu a podřízených prostředků další vlastnosti. Tuto šablonu nesaďte tak, jak je, nebo se resetují všechny vlastnosti vašeho účtu.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Další kroky

- [Přehled zámků Azure Resource Manager](../azure-resource-manager/management/lock-resources.md)

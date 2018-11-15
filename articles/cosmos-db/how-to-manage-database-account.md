---
title: Informace o správě účtů databáze ve službě Azure Cosmos DB
description: Informace o správě účtů databáze ve službě Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 0683516d16bf1501eee83901c5171811b8c0e44d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621543"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>Správa účtů databáze ve službě Azure Cosmos DB

Tento článek popisuje, jak spravovat váš účet Azure Cosmos DB nastavit vícenásobné navádění, přidat nebo odebrat oblasti, nakonfigurovat více oblastí zápisu a nastavení priorit převzetí služeb při selhání. 

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Konfigurace klientů pro vícenásobné navádění

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK

```csharp
// Create a new Connection Policy
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to Preferred locations
// The name of the location will match what you see in the portal/etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the Connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java Sync SDK

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Set up the connection policy with your preferred regions
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Přidání oblastí do účtu databáze nebo jejich odebrání

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Přejděte ke svému účtu služby Azure Cosmos DB a otevřete nabídku **Globální replikace dat**.

2. Pokud chcete přidat oblasti, vyberte na mapě jednu nebo několik oblastí kliknutím na prázdné šestiúhelníky s popiskem **+**, které odpovídají požadovaným oblastem. Oblast můžete přidat také výběrem možnosti **+ Přidat oblast** a zvolením oblasti z rozevírací nabídky.

3. Pokud chcete odebrat oblasti, zrušte na mapě výběr jedné nebo několika oblastí kliknutím na modré šestiúhelníky se značkou zaškrtnutí nebo vyberte ikonu odpadkového koše (🗑) vedle požadované oblasti na pravé straně.

4. Kliknutím na Uložit uložte provedené změny.

   ![Nabídka Přidat nebo odebrat oblasti](./media/how-to-manage-database-account/add-region.png)

V režimu zápisu do jedné oblasti není možné odebrat oblast zápisu. Před odstraněním aktuální oblasti zápisu je potřeba provést převzetí služeb při selhání do jiné oblasti.

V režimu zápisu do více oblastí můžete přidat nebo odebrat libovolnou oblast, pokud máte alespoň jednu oblast.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>Konfigurace několika oblastí zápisu

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Při vytváření účtu databáze se ujistěte, že je povolené nastavení **Zápisy do více oblastí**.

![Snímek obrazovky vytvoření účtu Azure Cosmos](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>Šablona Resource Manageru

Následující kód JSON je příkladem šablony Resource Manageru. Slouží k nasazení účtu Azure Cosmos pomocí zásad konzistence jako omezená Neaktuálnost, maximální neaktuálnost interval 5 sekund a maximální počet zastaralých požadavků tolerovat na 100. Informace o formátu a syntaxi šablon Resource Manageru najdete v dokumentaci k [Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>Povolit ruční převzetí služeb při selhání pro váš účet Azure Cosmos

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Přejděte ke svému účtu Azure Cosmos a otevřete **"Globální replikace dat"** nabídky.

2. V horní části nabídky klikněte na tlačítko **Ruční převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. V nabídce **Ruční převzetí služeb při selhání** vyberte novou oblast zápisu a zaškrtnutím políčka vyjádřete, že chápete, že se výběrem této možnosti změní oblast zápisu.

4. Kliknutím na OK aktivujte převzetí služeb při selhání.

   ![Nabídka portálu Ruční převzetí služeb při selhání](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a id="automatic-failover"></a>Povolit automatické převzetí služeb při selhání pro váš účet Azure Cosmos

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Ze svého účtu Azure Cosmos, otevřete **"Globální replikace dat"** podokně. 

2. V horní části podokna klikněte na tlačítko **Automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. V podokně **Automatické převzetí služeb při selhání** se ujistěte, že je možnost **Povolit automatické převzetí služeb při selhání** nastavená na **ZAPNUTO**. 

4. V dolní části nabídky klikněte na Uložit.

   ![Nabídka portálu Automatické převzetí služeb při selhání](./media/how-to-manage-database-account/automatic-failover.png)

V této nabídce můžete také nastavit své priority převzetí služeb při selhání.

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Nastavení priorit převzetí služeb při selhání pro váš účet Azure Cosmos

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Ze svého účtu Azure Cosmos, otevřete **"Globální replikace dat"** podokně. 

2. V horní části podokna klikněte na tlačítko **Automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. V podokně **Automatické převzetí služeb při selhání** se ujistěte, že je možnost **Povolit automatické převzetí služeb při selhání** nastavená na **ZAPNUTO**. 

4. Prioritu převzetí služeb při selhání můžete upravit kliknutím na tři tečky, které se zobrazí po najetí myši na levé straně řádku, a přetažením oblastí čtení. 

5. V dolní části nabídky klikněte na Uložit.

   ![Nabídka portálu Automatické převzetí služeb při selhání](./media/how-to-manage-database-account/automatic-failover.png)

V této nabídce není možné změnit oblast zápisu. Pokud chcete změnit oblast zápisu ručně, je potřeba provést ruční převzetí služeb při selhání.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>Další postup

Informace o správě konzistence konfliktů úrovně a data ve službě Azure Cosmos DB pomocí následující dokumentace:

* [Správa konzistence](how-to-manage-consistency.md)
* [Správa konfliktů mezi oblastmi](how-to-manage-conflicts.md)


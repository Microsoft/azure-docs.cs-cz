---
title: Informace o správě účtů databáze ve službě Azure Cosmos DB
description: Informace o správě účtů databáze ve službě Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 1486ab3240036abedea2cbb3cbe93e5c061691d8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467403"
---
# <a name="manage-an-azure-cosmos-account"></a>Správa účtu Azure Cosmos

Tento článek popisuje, jak spravovat váš účet Azure Cosmos DB. Zjistíte, jak nastavit vícenásobné navádění, přidat nebo odebrat oblasti, nakonfigurovat více oblastí zápisu a nastavte priority převzetí služeb při selhání. 

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
// Create a new connection policy.
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults.
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to preferred locations.
// The name of the location will match what you see in the portal, etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the connection policy with the preferred locations on it to the client.
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
// Set up the connection policy with your preferred regions.
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client.
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

1. Přejděte ke svému účtu Azure Cosmos DB a otevřete **globální replikace dat** nabídky.

2. Přidat oblasti, vyberte Šestiúhelníky na mapě s **+** popisek, který odpovídá požadované oblasti. Chcete-li přidat oblast, **+ přidat oblast** a z rozevírací nabídky vyberte oblast.

3. Pokud chcete odebrat oblasti, zrušte tak, že vyberete modré Šestiúhelníky se zaškrtnutím jedné nebo několika oblastech z mapy. Nebo vyberte "Koš" (🗑) ikonu vedle oblast na pravé straně.

4. Chcete-li uložit změny, vyberte **OK**.

   ![Přidání nebo odebrání oblastí nabídky](./media/how-to-manage-database-account/add-region.png)

V režimu jedné oblasti zápisu nelze odebrat oblasti pro zápis. Musíte převzetí služeb při selhání do jiné oblasti než budete moct odstranit tuto aktuální oblasti pro zápis.

Ve více oblastech režimu, můžete přidat nebo odebrat všechny oblasti, pokud máte alespoň jedné oblasti zápisu.

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

Následující kód JSON je příklad šablony Azure Resource Manageru. Slouží k nasazení účtu Azure Cosmos DB s zásadu konzistenci omezená neaktuálnost. Maximální neaktuálnost interval je nastavený na 5 sekund. Maximální počet zastaralých požadavků, které je tolerovat nastavený na 100. Další informace o formátu šablony Resource Manageru a syntaxe, naleznete v tématu [Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).

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


## <a id="manual-failover"></a>Povolit ruční převzetí služeb při selhání pro váš účet Azure Cosmos DB

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Přejděte ke svému účtu Azure Cosmos DB a otevřete **globální replikace dat** nabídky.

2. V horní nabídce vyberte **ruční převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **ruční převzetí služeb při selhání** nabídku, vyberte novou oblast zápisu. Zaškrtněte políčko označující, že chápete, že se že tato možnost změní vaše oblast pro zápis.

4. Chcete-li aktivovat převzetí služeb při selhání, vyberte **OK**.

   ![Nabídka portálu Ruční převzetí služeb při selhání](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a id="automatic-failover"></a>Povolit automatické převzetí služeb při selhání pro váš účet Azure Cosmos DB

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Z vašeho účtu služby Azure Cosmos DB, otevřete **globální replikace dat** podokně. 

2. V horní části podokna vyberte **automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **automatické převzetí služeb při selhání** podokno, ujistěte se, že **povolit automatické převzetí služeb při selhání** je nastavena na **ON**. 

4. Vyberte **Uložit**.

   ![Nabídka portálu Automatické převzetí služeb při selhání](./media/how-to-manage-database-account/automatic-failover.png)

Můžete také nastavit priorit převzetí služeb při selhání v této nabídce.

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-db-account"></a>Nastavení priorit převzetí služeb při selhání pro váš účet Azure Cosmos DB

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Z vašeho účtu služby Azure Cosmos DB, otevřete **globální replikace dat** podokně. 

2. V horní části podokna vyberte **automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **automatické převzetí služeb při selhání** podokno, ujistěte se, že **povolit automatické převzetí služeb při selhání** je nastavena na **ON**. 

4. Změna priority převzetí služeb při selhání, přetáhněte oblastí pro čtení přes na tři tečky na levé straně, který se zobrazí při najetí myši na řádek. 

5. Vyberte **Uložit**.

   ![Nabídka portálu Automatické převzetí služeb při selhání](./media/how-to-manage-database-account/automatic-failover.png)

Nelze změnit oblast zápisu v této nabídce. Pokud chcete změnit oblast zápisu ručně, je potřeba provést ruční převzetí služeb při selhání.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>Další postup

Další informace o správě konzistence konfliktů úrovně a data ve službě Azure Cosmos DB. Viz následující články:

* [Správa konzistence](how-to-manage-consistency.md)
* [Správa konfliktů mezi oblastmi](how-to-manage-conflicts.md)


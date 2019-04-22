---
title: Informace o správě účtů databáze ve službě Azure Cosmos DB
description: Informace o správě účtů databáze ve službě Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: b2b5e58ca480aa3abaa0766319977b8d1160ebeb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282997"
---
# <a name="manage-an-azure-cosmos-account"></a>Správa účtu Azure Cosmos

Tento článek popisuje, jak spravovat váš účet Azure Cosmos. Se dozvíte, jak nastavit vícenásobné navádění, přidat nebo odebrat oblasti, nakonfigurovat více oblastí zápisu a nastavte priority převzetí služeb při selhání. 

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Konfigurace klientů pro vícenásobné navádění

### <a id="configure-clients-multi-homing-dotnet"></a>Sady .NET SDK v2

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-dotnet-v3"></a>Sady .NET SDK v3 (preview)

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="addremove-regions-from-your-database-account"></a>Přidání oblastí do účtu databáze nebo jejich odebrání

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Přejděte ke svému účtu Azure Cosmos a otevřete **globální replikace dat** nabídky.

2. Přidat oblasti, vyberte Šestiúhelníky na mapě s **+** popisek, který odpovídá požadované oblasti. Můžete také přidat oblast, vyberte **+ přidat oblast** a z rozevírací nabídky vyberte oblast.

3. Pokud chcete odebrat oblasti, zrušte tak, že vyberete modré Šestiúhelníky se zaškrtnutím jedné nebo několika oblastech z mapy. Nebo vyberte "Koš" (🗑) ikonu vedle oblast na pravé straně.

4. Chcete-li uložit změny, vyberte **OK**.

   ![Přidání nebo odebrání oblastí nabídky](./media/how-to-manage-database-account/add-region.png)

V jedné oblasti zápisu režimu, že nelze odebrat oblasti pro zápis. Musíte převzetí služeb při selhání do jiné oblasti než budete moct odstranit aktuální oblasti pro zápis.

Ve více oblastech režimu, můžete přidat nebo odebrat libovolnou oblast, pokud máte alespoň jedné oblasti zápisu.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Create an account with 1 region
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0

# Add a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations eastus=0 westus=1

# Remove a region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0
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

Následující kód JSON je příkladem [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) šablony. Slouží k nasazení účtu Azure Cosmos pomocí [omezená neaktuálnost úrovně konzistence](consistency-levels.md). Interval maximální neaktuálnost je nastavena na 5 sekund. Maximální počet zastaralých požadavků, které je tolerovat je nastavena na hodnotu 100. Další informace o formátu šablony Resource Manageru a syntaxe, naleznete v tématu [Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).

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

1. Přejděte ke svému účtu Azure Cosmos a otevřete **globální replikace dat** nabídky.

2. V horní nabídce vyberte **ruční převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **ruční převzetí služeb při selhání** nabídku, vyberte novou oblast zápisu. Zaškrtněte políčko označující, že chápete, že se že tato možnost změní vaše oblast pro zápis.

4. Chcete-li aktivovat převzetí služeb při selhání, vyberte **OK**.

   ![Nabídka portálu Ruční převzetí služeb při selhání](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations westus=0 eastus=1
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

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Nastavení priorit převzetí služeb při selhání pro váš účet Azure Cosmos

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Ze svého účtu Azure Cosmos, otevřete **globální replikace dat** podokně. 

2. V horní části podokna vyberte **automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Na **automatické převzetí služeb při selhání** podokno, ujistěte se, že **povolit automatické převzetí služeb při selhání** je nastavena na **ON**. 

4. Změna priority převzetí služeb při selhání, přetáhněte oblastí pro čtení přes na tři tečky na levé straně, který se zobrazí při najetí myši na řádek. 

5. Vyberte **Uložit**.

   ![Nabídka portálu Automatické převzetí služeb při selhání](./media/how-to-manage-database-account/automatic-failover.png)

V této nabídce není možné změnit oblast zápisu. Pokud chcete změnit oblast zápisu ručně, je potřeba provést ruční převzetí služeb při selhání.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
# Assume region order is initially eastus=0 westus=1 automatic failover on account creation
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies westus=0 eastus=1
```

## <a name="next-steps"></a>Další postup

V následujících článcích:

* [Správa konzistence](how-to-manage-consistency.md)
* [Správa konfliktů mezi oblastmi](how-to-manage-conflicts.md)
* [Globální distribuce - pod pokličkou](global-dist-under-the-hood.md)
* [Jak nakonfigurovat více hlavních databází ve svých aplikacích](how-to-multi-master.md)
* [Konfigurace klientů pro multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Přidat nebo odebrat oblasti ze svého účtu Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Vytvoření zásady překladu IP adres vlastní konflikt](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)


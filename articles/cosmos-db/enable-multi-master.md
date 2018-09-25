---
title: Povolit více hlavních databází pro účty služby Azure Cosmos DB
description: Tento článek popisuje, jak povolit podporu více hlavních při vytváření účtu služby Azure Cosmos DB pomocí webu Azure portal, Powershellu, CLI nebo šablony Azure Resource Manageru.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 71579e28f389d91498ef5f37c5d43dc9d5140234
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963215"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Povolit více hlavních databází pro účty služby Azure Cosmos DB

Při vytváření účtu služby Azure Cosmos DB je povolena podpora několika hlavními databázemi. Účet služby Azure Cosmos DB můžete vytvořit pomocí webu Azure portal, Powershellu, CLI nebo šablony Azure Resource Manageru.

> [!IMPORTANT]
> V současné době podpora více hlavních serverů se dá povolit jenom nové účty služby Azure Cosmos DB. Funkci nelze používat existující účty služby Azure Cosmos DB. Jsme ve spolupráci a poskytuje podporu pro existující účty a oznámí tuto podporu, když je k dispozici.

Po vytvoření účtu služby Azure Cosmos DB s podporou více hlavních vytváření databází, kontejnerů a odeslat dokumenty přiřazení zásad řešení konfliktů. Řešení konfliktů ve více hlavních databází a ukázky kódu, naleznete v tématu [multimasterovou ukázky](multi-master-conflict-resolution.md#code-samples) článku.

## <a name="enable-multi-master-using-azure-portal"></a>Povolit více hlavních databází pomocí webu Azure portal

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

2. Klikněte na vybrat **vytvořit prostředek > databáze > Azure Cosmos DB**.

3. V **nový účet** podokně zadejte následující nastavení pro nový účet Azure Cosmos DB:

   |**Nastavení**  |**Navrhovaná hodnota** |**Popis**|
   |---------|---------|---------|
   |Předplatné   | {Subscription}  |Vyberte předplatné Azure, které chcete použít pro tento účet služby Azure Cosmos DB.  |
   |Skupina prostředků  |   {Název skupiny prostředků}    |  Vyberte existující skupinu prostředků nebo vyberte **vytvořit nový**, zadejte nový název skupiny prostředků pro váš účet. |
   |Název účtu | {název svého účtu}   |  Zadejte jedinečný název pro identifikaci účtu služby Azure Cosmos DB.        |
   |Rozhraní API  |   Všechny   |  Vyberte typ rozhraní API.   |
   |Umístění  | Vybrat libovolnou oblast   | Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Můžete vybrat libovolnou oblast, protože tento účet bude v několika oblastech.  |
   |Povolit geografickou redundanci   |  Povolení  |  Vyberte pro povolení více hlavní výběr níže.   |
   |Povolit Multi Master | Povolení  | Vyberte pro povolení více správce pro tento účet. |


## <a name="using-multi-master-in-sdks"></a>Použití více hlavních databází v sadách SDK

S účtem povoleno více hlavních databází v rámci vašich aplikací můžete využít výhod více mastering s využitím ConnectionPolicy, jak je znázorněno níže.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add("West US");
policy.PreferredLocations.Add("North Europe");
policy.PreferredLocations.Add("Southeast Asia");
```

## <a name="enable-multi-master-using-powershell"></a>Povolení několika hlavními databázemi, pomocí Powershellu

Multimasterovou povolený účet služby Cosmos DB můžete vytvořit také tak, že nastavíte `enableMultipleWriteLocations` parametr na hodnotu "true". Vytvoření účtu služby Azure Cosmos DB s několika hlavními uzly povolena, otevřete okno Powershellu a spusťte následující skript:

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>Povolení několika hlavními databázemi, pomocí rozhraní příkazového řádku

Můžete povolit více hlavních databází tím, že nastavíte parametr Povolit více zápisu umístění na hodnotu "true". Otevřete rozhraní příkazového řádku Azure k vytvoření účtu služby Azure Cosmos DB s několika hlavními uzly povolena, nebo cloud shell a spuštěním následujícího příkazu:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Povolení několika hlavními databázemi pomocí šablony Resource Manageru

Je následující kód JSON šablony Resource Manageru příklad, který můžete použít k nasazení účtu služby Azure Cosmos DB. Další informace o formátu šablony Resource Manageru a syntaxe, naleznete v tématu [Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) dokumentaci. Parametr klíče na Všimněte si, že v této šabloně je "enableMultipleWriteLocations": true.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak povolit multimasterovou podporu pro účty služby Azure Cosmos DB. Podívejte se v dalším kroku v následujících článcích:

* [Použití více hlavních databází s databázemi NoSQL otevřít zdroj](multi-master-oss-nosql.md)

* [Principy řešení konfliktů ve službě Azure Cosmos DB](multi-master-conflict-resolution.md)

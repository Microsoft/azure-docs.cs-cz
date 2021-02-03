---
title: K nakonfigurování průběžného zálohování a obnovení bodu v čase v Azure Cosmos DB použijte šablonu ARM.
description: Naučte se zřídit účet s průběžným zálohováním a obnovením dat pomocí šablon Azure Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6c388a08a589cc89d83b7178e31e3f4497b924bb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527393"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-resource-manager-templates"></a>Konfigurace a Správa průběžného zálohování a obnovení k určitému bodu v čase – používání šablon Azure Resource Manager
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Funkce obnovení k určitému bodu v čase umožňuje obnovení z náhodné změny v rámci kontejneru, obnovení odstraněného účtu, databáze nebo kontejneru nebo obnovení do libovolné oblasti (kde zálohy existovaly). Azure Cosmos DB Režim průběžné zálohování umožňuje obnovení do libovolného časového okamžiku za posledních 30 dní.

Tento článek popisuje, jak zřídit účet s průběžným zálohováním a obnovením dat pomocí šablon Azure Resource Manager.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Zřízení účtu s průběžným zálohováním

Pomocí Azure Resource Manager šablon můžete nasadit účet Azure Cosmos DB s nepřetržitým režimem. Při definování šablony pro zřízení účtu přidejte parametr "backupPolicy", jak je znázorněno v následujícím příkladu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Dále nasaďte šablonu pomocí Azure PowerShell nebo rozhraní příkazového řádku. Následující příklad ukazuje, jak nasadit šablonu pomocí příkazu CLI:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Obnovení pomocí šablony Správce prostředků

Účet můžete také obnovit pomocí šablony Správce prostředků. Při definování šablony se zahrnou následující parametry:

* Nastavte parametr createMode na Restore.
* Zadejte "restoreParameters", Všimněte si, že hodnota "restoreSource" je extrahována z výstupu `az cosmosdb restorable-database-account list` příkazu pro váš zdrojový účet. K provedení obnovení se používá atribut ID instance pro název vašeho účtu.
* Nastavte parametr "restoreMode" na "PointInTime" a nakonfigurujte hodnotu "restoreTimestampInUtc".

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Dále nasaďte šablonu pomocí Azure PowerShell nebo rozhraní příkazového řádku. Následující příklad ukazuje, jak nasadit šablonu pomocí příkazu CLI:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure CLI](continuous-backup-restore-command-line.md), [powershellu](continuous-backup-restore-command-line.md)nebo [Azure Portal](continuous-backup-restore-portal.md).
* [Model prostředků režimu průběžné zálohování](continuous-backup-restore-resource-model.md)
* [Správa oprávnění](continuous-backup-restore-permissions.md) potřebných pro obnovení dat pomocí režimu průběžné zálohování.
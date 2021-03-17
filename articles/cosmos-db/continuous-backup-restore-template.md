---
title: K nakonfigurování průběžného zálohování a obnovení bodu v čase v Azure Cosmos DB použijte šablonu ARM.
description: Naučte se zřídit účet s průběžným zálohováním a obnovením dat pomocí šablon Azure Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4abfdd0209bd9f13fb7bd902b27a53f65156da2e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381813"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>Konfigurace a Správa průběžného zálohování a obnovení k určitému bodu v čase (Preview) – používání šablon Azure Resource Manager
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkce obnovení bodu v čase (režim průběžného zálohování) pro Azure Cosmos DB je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkce obnovení k určitému bodu v čase služby Azure Cosmos DB (Preview) vám pomůže s obnovením z náhodné změny v rámci kontejneru, k obnovení odstraněného účtu, databáze nebo kontejneru nebo k obnovení do libovolné oblasti (kde zálohy existovaly). Režim průběžné zálohování umožňuje obnovení do libovolného časového okamžiku za posledních 30 dní.

Tento článek popisuje, jak zřídit účet s průběžným zálohováním a obnovením dat pomocí šablon Azure Resource Manager.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Zřízení účtu s průběžným zálohováním

Pomocí Azure Resource Manager šablon můžete nasadit účet Azure Cosmos DB s nepřetržitým režimem. Při definování šablony pro zřízení účtu přidejte `backupPolicy` parametr, jak je znázorněno v následujícím příkladu:

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

* Nastavte `createMode` parametr, který se má *obnovit* .
* Definujte `restoreParameters` , Všimněte si, že `restoreSource` hodnota je extrahována z výstupu `az cosmosdb restorable-database-account list` příkazu pro váš zdrojový účet. K provedení obnovení se používá atribut ID instance pro název vašeho účtu.
* Nastavte `restoreMode` parametr na *PointInTime* a nakonfigurujte `restoreTimestampInUtc` hodnotu.

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
---
title: Zkontrolujte využití prostředků služby Azure pomocí rozhraní REST API | Dokumentace Microsoftu
description: Zjistěte, jak použít rozhraní Azure REST API ke kontrole využití prostředků služby Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: erikre
ms.openlocfilehash: 297b1d5e683a8ad505a37c43ab74c8b888ec87f2
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265749"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Zkontrolujte využití prostředků Azure pomocí rozhraní REST API

Azure Cost Management API pomáhají prohlížet a spravovat využití vašich prostředků Azure.

V tomto článku se dozvíte, jak vytvořit denní sestavu, která bude generovat dokument hodnot oddělených čárkami se po hodinách informace o použití a jak používat filtry umožní přizpůsobit sestavu, takže můžete dát dotaz na využití virtuálních počítačů, databází a označí prostředky ve skupině prostředků Azure.

>[!NOTE]
> Rozhraní API pro správu nákladů je aktuálně ve verzi private preview.

## <a name="create-a-basic-cost-management-report"></a>Vytvoření sestavy základní náklady na správu

Použití `reports` operace v rozhraní API pro správu nákladů definovat způsob generování sestav nákladů a kde sestavy bude publikován.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

`{subscriptionGuid}` Parametr je vyžadován a musí obsahovat ID předplatného, který může číst pomocí pověření poskytnutých ve token rozhraní API. Na `{reportName}`

Vyžadují se následující hlavičky: 

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Typ obsahu:*| Povinná hodnota. Nastavte na `application/json`. |  
|*Autorizace:*| Povinná hodnota. Nastaven na platné `Bearer` token. |

Konfigurujte parametry sestavy v textu požadavku HTTP. V následujícím příkladu je nastavena sestavy ke generování každý den, kdy aktivní, je soubor CSV se zapisují do kontejneru objektů blob v Azure Storage a obsahuje informace o nákladech pro všechny prostředky ve skupině prostředků každou hodinu `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Na

## <a name="filtering-reports"></a>Filtrování sestavy

`filter` a `dimensions` část obsahu žádosti při vytváření sestavy vám umožní soustředit se v na náklady na konkrétní typy prostředků. Předchozí text požadavku ukazuje, jak filtrovat podle všech prostředků v oblasti. 

### <a name="get-all-compute-usage"></a>Získat všechny využití služby compute

Použití `ResourceType` dimenze sestavy nákladů na virtuální počítač Azure v rámci vašeho předplatného ve všech oblastech.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Získat všechny databáze využití

Použití `ResourceType` dimenze sestava náklady na Azure SQL Database ve vašem předplatném ve všech oblastech.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Ohlásit na určité instance

`Resource` Dimenze, které vám umožní nahlásit náklady pro konkrétní prostředky.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Změna časové rámce

Nastavte `timeframe` definici `Custom` nastavit časový rámec mimo týden k datu a měsíci pro integrované možnosti data.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Další postup
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   

---
title: Kontrola využití prostředků služby Azure pomocí REST API | Microsoft Docs
description: Naučte se používat rozhraní Azure REST API ke kontrole využití prostředků služby Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: banders
ms.openlocfilehash: 47e19fae26d6e3bc465799980c587d7bb7ed5e92
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443064"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Projděte si využití prostředků Azure pomocí REST API

Rozhraní API pro Azure Cost Management vám pomůžou zkontrolovat a spravovat spotřebu vašich prostředků Azure.

V tomto článku se dozvíte, jak vytvořit denní sestavu, která bude generovat dokument s hodnotami oddělenými čárkou s informacemi o hodinovém použití, a pak pomocí filtrů přizpůsobit sestavu, abyste mohli zadávat dotazy na využití virtuálních počítačů, databází a značek. prostředky ve skupině prostředků Azure.

>[!NOTE]
> Rozhraní Cost Management API je aktuálně v privátní verzi Preview.

## <a name="create-a-basic-cost-management-report"></a>Vytvoření sestavy základní správy nákladů

`reports` Pomocí operace v rozhraní cost Management API definujte způsob, jakým se generují náklady na generování sestav a kde budou sestavy publikovány.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

`{subscriptionGuid}` Parametr je povinný a měl by obsahovat ID předplatného, které se dá číst pomocí přihlašovacích údajů zadaných v tokenu rozhraní API. Okně`{reportName}`

Jsou vyžadovány následující hlavičky: 

|Hlavička žádosti|Popis|  
|--------------------|-----------------|  
|*Content-Type:*| Povinný parametr. Nastavte na `application/json`. |  
|*Authorization:*| Povinný parametr. Nastavte na platný `Bearer` token. |

Nakonfigurujte parametry sestavy v textu požadavku HTTP. V následujícím příkladu je sestava nastavená tak, aby se vygenerovala každý den, pokud je aktivní, jedná se o soubor CSV zapsaný do Azure Storage kontejneru objektů BLOB a obsahuje hodinové informace o `westus`nákladech pro všechny prostředky ve skupině prostředků.

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

Rozhraní

## <a name="filtering-reports"></a>Filtrování sestav

Oddíl `filter` a`dimensions` v těle požadavku při vytváření sestavy vám umožní soustředit se na náklady na konkrétní typy prostředků. Předchozí text žádosti ukazuje, jak filtrovat podle všech prostředků v oblasti. 

### <a name="get-all-compute-usage"></a>Získat veškeré využití výpočtů

`ResourceType` Dimenzi můžete použít k hlášení nákladů na virtuální počítače Azure v rámci předplatného ve všech oblastech.

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

### <a name="get-all-database-usage"></a>Získat všechny využití databáze

`ResourceType` Dimenzi můžete použít k hlášení Azure SQL Database nákladů ve vašem předplatném napříč všemi oblastmi.

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

### <a name="report-on-specific-instances"></a>Sestava konkrétních instancí

`Resource` Dimenze vám umožní nahlásit náklady na konkrétní prostředky.

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

### <a name="changing-timeframes"></a>Změna časových období

Nastavte definici na `Custom` hodnotu pro nastavení časového období mimo týden do předdefinovaných možností data a měsíc. `timeframe`

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Další postup
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   

---
title: Kontrola využití prostředků služeb Azure s využitím rozhraní REST API
description: Zjistěte, jak pomocí rozhraní Azure REST API zkontrolovat využití prostředků služeb Azure. Vytvořte sestavu správy nákladů a vyfiltrujte konkrétní typy prostředků.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: ed1d360b5e816627bf161f479addcd6e0eb776a1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058845"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Kontrola využití prostředků Azure pomocí rozhraní REST API

Rozhraní API služby Azure Cost Management pomáhají s kontrolou a správou spotřeby prostředků Azure.

V tomto článku zjistíte, jak vytvořit denní sestavu, která bude generovat dokument hodnot oddělených čárkami s informacemi o hodinovém využití. Dále se naučíte, jak sestavu přizpůsobit pomocí filtrů tak, abyste se mohli dotazovat na využití virtuálních počítačů, databází a označených prostředků ve skupině prostředků Azure.

>[!NOTE]
> Rozhraní API služby Cost Management je v současné době v privátní verzi Preview.

## <a name="create-a-basic-cost-management-report"></a>Vytvoření základní sestavy správy nákladů

Pomocí operace `reports` v rozhraní API služby Cost Management definujte způsob generování sestav nákladů a umístění, ve kterém se sestavy budou publikovat.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

Parametr `{subscriptionGuid}` je povinný a měl by obsahovat ID předplatného, ze kterého je možné číst s použitím přihlašovacích údajů uvedených v tokenu rozhraní API. 

Parametr `{reportName}` určuje název sestavy. K získání seznamu názvů sestav můžete použít operaci Reports_List: `/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/reports`. Příklad výstupu najdete na [GitHubu](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/cost-management/resource-manager/Microsoft.CostManagement/preview/2018-08-01-preview/examples/ReportList.json).

Jsou vyžadovány následující hlavičky:

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*| Povinná hodnota. Nastavte na `application/json`. |  
|*Authorization:*| Povinná hodnota. Nastavte na platný token `Bearer`. |

Nakonfigurujte parametry sestavy v textu požadavku HTTP. V následujícím příkladu je sestava nastavená tak, aby se generovala každý den, kdy je aktivní, v podobě souboru CSV, který se zapíše do kontejneru objektů blob služby Azure Storage, a obsahovala informace o hodinových nákladech na všechny prostředky ve skupině prostředků `westus`.

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

Prostředek

## <a name="filtering-reports"></a>Filtrování sestav

Při vytváření sestavy se s využitím oddílů `filter` a `dimensions` v textu požadavku můžete zaměřit na náklady na konkrétní typy prostředků. Předchozí text požadavku ukazuje, jak vyfiltrovat všechny prostředky v určité oblasti.

### <a name="get-all-compute-usage"></a>Získání využití všech výpočetních prostředků

Pokud chcete vytvořit sestavu nákladů na virtuální počítače Azure ve vašem předplatném ve všech oblastech, použijte dimenzi `ResourceType`.

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

### <a name="get-all-database-usage"></a>Získání využití všech databází

Pokud chcete vytvořit sestavu nákladů na službu Azure SQL Database ve vašem předplatném ve všech oblastech, použijte dimenzi `ResourceType`.

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

### <a name="report-on-specific-instances"></a>Vytvoření sestavy pro konkrétní instance

Dimenze `Resource` umožňuje vytvořit sestavu nákladů na konkrétní prostředky.

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

Pokud chcete nastavit jiné časové období, než jsou předdefinované možnosti Od začátku týdne a Od začátku měsíce, nastavte definici `timeframe` na hodnotu `Custom`.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Další kroky
- [Začínáme s Azure REST API](/rest/api/azure/)
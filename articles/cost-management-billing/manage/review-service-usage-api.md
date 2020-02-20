---
title: Kontrola využití prostředků služeb Azure pomocí rozhraní REST API | Microsoft Docs
description: Zjistěte, jak pomocí rozhraní Azure REST API zkontrolovat využití prostředků služeb Azure.
author: lleonard-msft
ms.service: cost-management-billing
ms.topic: article
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: f421ddc7cd509527053b099c7e4e538ab84b814e
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200567"
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

Parametr `{subscriptionGuid}` je povinný a měl by obsahovat ID předplatného, ze kterého je možné číst s použitím přihlašovacích údajů uvedených v tokenu rozhraní API. Parametr `{reportName}` nahraďte názvem sestavy.

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
- [Začínáme s Azure REST API](https://docs.microsoft.com/rest/api/azure/)   

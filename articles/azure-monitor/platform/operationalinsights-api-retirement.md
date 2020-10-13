---
title: Vyřazení rozhraní API Azure Monitor
description: Popisuje vyřazení starších verzí rozhraní API poskytovatele prostředků OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: cf48e26133326d43754b38df6f3b2caaf7a587ab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978084"
---
# <a name="operationalinsights-api-version-retirement"></a>Vyřazení verze rozhraní API OperationalInsights
Microsoft poskytuje oznámení alespoň 12 měsíců před vyřazením rozhraní API z důvodu hladkého přechodu na novější/podporovanou verzi. Vydali jsme novou verzi (2020-08-01) pro rozhraní API poskytovatele prostředků **OperationalInsights** a všechny starší verze rozhraní API vyřadíme 31. října 2023. Vzhledem k tomu, že se nové funkce a funkce a optimalizace přidávají jenom k aktuálnímu rozhraní API, měli byste upgradovat na nejnovější verzi rozhraní API co nejdříve.

Po 31. října 2023 Azure Monitor už nebude podporovat starší verze rozhraní API než 2020-08-01. Pokud nechcete upgradovat, požadavky odeslané z dřívějších verzí budou nadále obsluhovány službou Azure Monitor do 31. října 2023.

## <a name="migration-steps"></a>Kroky migrace
V závislosti na metodě konfigurace, kterou používáte, byste měli aktualizovat novou verzi v žádostech **REST** a **Správce prostředků šablonách**. Podle následujících příkladů aktualizujte verzi rozhraní API:

1. REST API požadavky používají verzi rozhraní API v adrese URL požadavku. Nahraďte tuto verzi nejnovější verzí (2020-08-01), jak je znázorněno v následujícím příkladu.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager šablony používají verzi rozhraní API ve vlastnosti **apiVersion** prostředku. Nahraďte tuto verzi nejnovější verzí (2020-08-01), jak je znázorněno v následujícím příkladu.


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


## <a name="next-steps"></a>Další kroky

- Podívejte se na [Reference k rozhraní OperationalInsights API](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/allversions).

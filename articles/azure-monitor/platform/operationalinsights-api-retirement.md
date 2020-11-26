---
title: Vyřazení rozhraní API Azure Monitor
description: Popisuje vyřazení starších verzí rozhraní API poskytovatele prostředků OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: e2b12d7a2206ab369328563af438c6ef1ea39327
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184106"
---
# <a name="operationalinsights-api-version-retirement"></a>Vyřazení verze rozhraní API OperationalInsights
Microsoft poskytuje oznámení alespoň 12 měsíců před vyřazením rozhraní API z důvodu hladkého přechodu na novější/podporovanou verzi. Vydali jsme novou verzi (2020-08-01) pro rozhraní API poskytovatele prostředků **OperationalInsights** a vyřadíme všechny starší verze API z 29. února 2024.

Doporučujeme vám hned začít používat verzi 2020-08-01, abyste získali výhody nových funkcí, jako je například [vyhrazený cluster](../log-query/logs-dedicated-clusters.md), [klíče spravované zákazníky](./customer-managed-keys.md), [soukromý odkaz](./private-link-security.md) a [Export dat](./logs-data-export.md). Nové funkce a funkce a optimalizace se navíc přidávají jenom do aktuálního rozhraní API.

Po 29. února 2024 Azure Monitor už nebude podporovat starší verze rozhraní API než 2020-08-01. Pokud nechcete upgradovat, požadavky odeslané z dřívějších verzí budou nadále obsluhovány službou Azure Monitor až do 29. února 2024.

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

- Podívejte se na [referenční informace pro rozhraní API pracovního prostoru OperationalInsights](/rest/api/loganalytics/workspaces).
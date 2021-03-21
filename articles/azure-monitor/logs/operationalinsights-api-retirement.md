---
title: Vyřazení rozhraní API Azure Monitor
description: Popisuje vyřazení starších verzí rozhraní API poskytovatele prostředků OperationalInsights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 7b5d105f6a93102ff1b9142fbdf690c3873d872f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032020"
---
# <a name="operationalinsights-api-version-retirement"></a>Vyřazení verze rozhraní API OperationalInsights
Microsoft poskytuje oznámení alespoň 12 měsíců před vyřazením rozhraní API z důvodu hladkého přechodu na novější/podporovanou verzi. Vydali jsme novou verzi (2020-08-01) pro rozhraní API poskytovatele prostředků **OperationalInsights** a vyřadíme všechny starší verze API z 29. února 2024.

Doporučujeme vám hned začít používat verzi 2020-08-01, abyste získali výhody nových funkcí, jako je například [vyhrazený cluster](./logs-dedicated-clusters.md), [klíče spravované zákazníky](../logs/customer-managed-keys.md), [soukromý odkaz](./private-link-security.md) a [Export dat](./logs-data-export.md). Nové funkce a funkce a optimalizace se navíc přidávají jenom do aktuálního rozhraní API.

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


### <a name="more-information"></a>Další informace
Pokud máte nějaké otázky, Získejte odpovědi od [expertů pro odborníky na technickou komunitu]( https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor). Pokud máte plán podpory a potřebujete technickou pomoc, vytvořte [žádost o podporu]( https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest): 
1.  V části *typ problému* vyberte **technický**. 
2.  V části *Předplatné* vyberte své předplatné. 
3.  V části *Služba* vyberte **moje služby** a pak vyberte **Log Analytics**. 
4.  V části *Souhrn* zadejte popis problému. 
5.  V části *typ problému* vyberte **Log Analytics Správa pracovního prostoru**.  
6.  V části *problémový typ* vyberte **šablony ARM, PowerShell a** rozhraní příkazového řádku. 

## <a name="next-steps"></a>Další kroky

- Podívejte se na [referenční informace pro rozhraní API pracovního prostoru OperationalInsights](/rest/api/loganalytics/workspaces).
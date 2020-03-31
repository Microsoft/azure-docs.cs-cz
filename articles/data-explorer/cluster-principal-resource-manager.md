---
title: Přidání objektů zabezpečení clusteru pro Azure Data Explorer pomocí šablony Azure Resource Manageru
description: V tomto článku se dozvíte, jak přidat objekty zabezpečení clusteru pro Azure Data Explorer pomocí šablony Azure Resource Manager.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 22423568ab0b3b55d8d9566df4829eb6070b5f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965044"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-an-azure-resource-manager-template"></a>Přidání objektů zabezpečení clusteru pro Azure Data Explorer pomocí šablony Azure Resource Manageru

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Šablona Azure Resource Manageru](cluster-principal-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. V tomto článku přidáte objekty zabezpečení clusteru pro Azure Data Explorer pomocí šablony Azure Resource Manager.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvořte cluster](create-cluster-database-portal.md).

## <a name="azure-resource-manager-template-for-adding-a-cluster-principal"></a>Šablona Azure Resource Manager pro přidání objektu zabezpečení clusteru

Následující příklad ukazuje šablonu Azure Resource Manager pro přidání objektu zabezpečení clusteru.  Šablonu můžete [upravit a nasadit na webu Azure Portal](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) pomocí formuláře.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterPrincipalAssignmentName": {
            "type": "string",
            "defaultValue": "principalAssignment1",
            "metadata": {
                "description": "Specifies the name of the principal assignment"
            }
        },
        "clusterName": {
            "type": "string",
            "defaultValue": "mykustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "principalIdForCluster": {
            "type": "string",
            "metadata": {
                "description": "Specifies the principal id. It can be user email, application (client) ID, security group name"
            }
        },
        "roleForClusterPrincipal": {
            "type": "string",
            "defaultValue": "AllDatabasesViewer",
            "metadata": {
                "description": "Specifies the cluster principal role. It can be 'AllDatabasesAdmin', 'AllDatabasesViewer'"
            }
        },
        "tenantIdForClusterPrincipal": {
            "type": "string",
            "metadata": {
                "description": "Specifies the tenantId of the principal"
            }
        },
        "principalTypeForCluster": {
            "type": "string",
            "defaultValue": "User",
            "metadata": {
                "description": "Specifies the principal type. It can be 'User', 'App', 'Group'"
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/principalAssignments",
            "apiVersion": "2019-11-09",
            "name": "[concat(parameters('clusterName'), '/', parameters('clusterPrincipalAssignmentName'))]",
            "properties": {
                "principalId": "[parameters('principalIdForCluster')]",
                "role": "[parameters('roleForClusterPrincipal')]",
                "tenantId": "[parameters('tenantIdForClusterPrincipal')]",
                "principalType": "[parameters('principalTypeForCluster')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky

* [Přidání objektů zabezpečení databáze](database-principal-resource-manager.md)

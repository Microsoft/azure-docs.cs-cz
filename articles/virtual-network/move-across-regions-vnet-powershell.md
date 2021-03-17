---
title: Přesuňte virtuální síť Azure do jiné oblasti Azure pomocí Azure PowerShell
description: Přesuňte virtuální síť Azure z jedné oblasti Azure do jiné pomocí Správce prostředků šablony a Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fa89a458b23d18bc6fddfca9cf6d9f2a16f91669
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934925"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Přesuňte virtuální síť Azure do jiné oblasti pomocí Azure PowerShell

Existují různé scénáře pro přesun existující virtuální sítě Azure z jedné oblasti do druhé. Například můžete chtít vytvořit virtuální síť se stejnou konfigurací pro testování a dostupnost jako stávající virtuální síť. Případně můžete chtít přesunout produkční virtuální síť do jiné oblasti v rámci plánování zotavení po havárii.

K dokončení přesunu virtuální sítě do jiné oblasti můžete použít šablonu Azure Resource Manager. Provedete to tak, že virtuální síť vyexportujete do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti. Další informace o šablonách Správce prostředků najdete v tématu [Export skupin prostředků do šablon](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je vaše virtuální síť v oblasti Azure, ze které chcete přejít.

- Pokud chcete exportovat virtuální síť a nasadit šablonu pro vytvoření virtuální sítě v jiné oblasti, musíte mít roli Přispěvatel sítě nebo vyšší.

- Partnerské vztahy virtuálních sítí se znovu nevytvoří a selžou, pokud jsou v šabloně pořád přítomné. Před exportem šablony musíte odebrat všechny partnerské uzly virtuální sítě. Po přesunu virtuální sítě je pak můžete znovu vytvořit.
    
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (skupin zabezpečení sítě) a veřejné IP adresy.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální sítě v cílové oblasti. Pokud chcete povolit požadovanou kvótu, obraťte se na podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání virtuálních sítí pro tento proces. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Příprava na přesun
V této části připravíte virtuální síť pro přesun pomocí šablony Správce prostředků. Poté přesunete virtuální síť do cílové oblasti pomocí příkazů Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete exportovat virtuální síť a nasadit cílovou virtuální síť pomocí PowerShellu, udělejte toto:

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a pak postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Získejte ID prostředku virtuální sítě, kterou chcete přesunout do cílové oblasti, a pak ji umístěte do proměnné pomocí [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exportujte zdrojovou virtuální síť do souboru. JSON v adresáři, ve kterém spustíte příkaz [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Stažený soubor má stejný název jako skupina prostředků, ze které byl prostředek exportován. Vyhledejte soubor *\<resource-group-name> . JSON* , který jste exportovali pomocí příkazu, a pak ho otevřete v editoru:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Pokud chcete upravit parametr názvu virtuální sítě, změňte vlastnost **DefaultValue** názvu zdrojové virtuální sítě na název vaší cílové virtuální sítě. Nezapomeňte název uzavřít do uvozovek.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Pokud chcete upravit cílovou oblast, do které se virtuální síť přesune, změňte vlastnost **Location** v části prostředky:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. Pokud chcete získat kódy umístění oblastí, můžete použít rutinu Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. Volitelné V závislosti na vašich požadavcích můžete také změnit další parametry v souboru *\<resource-group-name> . JSON* :

    * **Adresní prostor**: před uložením souboru můžete změnit adresní prostor virtuální sítě změnou   >  části **addressSpace** prostředků a změnou vlastnosti **addressPrefixes** :

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **Podsíť**: můžete změnit nebo přidat k názvu podsítě a adresnímu prostoru podsítě změnou oddílu **podsítě** souboru. Název podsítě můžete změnit změnou vlastnosti **název** . Adresní prostor podsítě můžete změnit tak, že změníte vlastnost **addressPrefix** :

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Chcete-li změnit předponu adresy, upravte soubor na dvou místech: v kódu v předchozí části a v části **typ** následujícího kódu. Změňte vlastnost **addressPrefix** v následujícím kódu tak, aby odpovídala vlastnosti **addressPrefix** v kódu v předchozí části.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Uložte soubor *\<resource-group-name> . JSON* .

1. Vytvořte skupinu prostředků v cílové oblasti pro nasazení cílové virtuální sítě pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Nasaďte upravený soubor *\<resource-group-name> . JSON* do skupiny prostředků, kterou jste vytvořili v předchozím kroku, pomocí [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Pokud chcete ověřit, že se prostředky vytvořily v cílové oblasti, použijte příkaz [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Odstranit virtuální síť nebo skupinu prostředků 

Po nasazení virtuální sítě, pokud chcete začít znovu nebo zrušit virtuální síť v cílové oblasti, odstraňte skupinu prostředků, kterou jste vytvořili v cílové oblasti, a přesunutou virtuální síť se odstraní. 

Chcete-li odebrat skupinu prostředků, použijte [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun virtuální sítě, proveďte jednu z následujících akcí:

* Odstraňte skupinu prostředků pomocí [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Odstraňte zdrojovou virtuální síť pomocí [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli virtuální síť z jedné oblasti do druhé pomocí prostředí PowerShell a pak vyčistili nepotřebné zdrojové prostředky. Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:

- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesunutí virtuálních počítačů Azure do jiné oblasti](../site-recovery/azure-to-azure-tutorial-migrate.md)

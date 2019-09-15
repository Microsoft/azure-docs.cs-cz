---
title: Přesunout Azure Virtual Network do jiné oblasti Azure pomocí Azure PowerShell
description: Pomocí šablony Azure Resource Manager můžete přesunout Azure Virtual Network z jedné oblasti Azure do jiné pomocí Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0037419570ceffcd11dbc8dfe85b45d7ec0ee8ce
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997442"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>Přesunutí Azure Virtual Network do jiné oblasti pomocí Azure PowerShellu

Existují různé scénáře, ve kterých byste chtěli přesunout stávající virtuální sítě Azure (virtuální sítě) z jedné oblasti do druhé. Například můžete chtít vytvořit virtuální síť se stejnou konfigurací pro testování a dostupnost stávající virtuální sítě. V rámci plánování zotavení po havárii možná budete chtít přesunout produkční virtuální síť do jiné oblasti.

K dokončení přesunu virtuální sítě do jiné oblasti můžete použít šablonu Azure Resource Manager. Provedete to tak, že virtuální síť vyexportujete do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [Export skupin prostředků do šablon](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) .


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je Azure Virtual Network v oblasti Azure, ze které chcete přejít.

- Pokud chcete exportovat virtuální síť a nasadit šablonu pro vytvoření virtuální sítě v jiné oblasti, budete potřebovat roli Přispěvatel sítě nebo vyšší.

- Partnerské vztahy virtuálních sítí se znovu nevytvoří a selžou, pokud jsou v šabloně pořád přítomné.  Před exportem šablony bude nutné odebrat všechny partnerské vztahy virtuální sítě a potom po přesunu virtuální sítě znovu vytvořit partnerské uzly.
    
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (skupin zabezpečení sítě) a veřejné IP adresy.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální sítě v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání virtuálních sítí pro tento proces.  Viz [limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit virtuální síť pro přesun pomocí šablony Správce prostředků a přesunout virtuální síť do cílové oblasti pomocí příkazů Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>Exportujte virtuální síť a nasaďte cílovou virtuální síť pomocí PowerShellu.

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Získejte ID předplatného, do kterého chcete nasadit cílovou virtuální síť, a umístěte ji do proměnné pomocí [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):
   
    ```azurepowershell-interactive
    Get-AzSubscription | format-table

    $Subscription = "MySubscriptionID"
    ```
3. Získejte ID prostředku virtuální sítě, kterou chcete přesunout do cílové oblasti a umístit ji do proměnné pomocí [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
4. Exportujte zdrojovou virtuální síť do souboru. JSON do adresáře, ve kterém spustíte příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

5. Stažený soubor se pojmenuje po vytvoření skupiny prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem **< Resource-Group-name >. JSON** a otevřete jej v editoru podle vlastního výběru:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

6. Pokud chcete upravit parametr názvu virtuální sítě, změňte vlastnost **DefaultValue** názvu zdrojové virtuální sítě na název cílové virtuální sítě, ujistěte se, že je název v uvozovkách:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

7.  Pokud chcete upravit cílovou oblast, kam se virtuální síť přesune, změňte vlastnost **umístění** v části prostředky:

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
  
8. Pokud chcete získat kódy umístění oblastí, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
9.  Můžete také změnit další parametry v souboru **< Resource-Group-name >. JSON** , pokud zvolíte možnost a jsou volitelné v závislosti na vašich požadavcích:

    * **Adresní prostor** – adresní prostor virtuální sítě je možné změnit před uložením úpravou části**addressSpace** **prostředků** > a změnou vlastnosti **addressPrefixes** v **< Resource-Group-Name Soubor >. JSON** :
    
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

    * **Podsíť** – název podsítě a adresní prostor podsítě je možné změnit nebo přidat k úpravou části **podsítě** v souboru **< resource-group-name >. JSON** . Název podsítě lze změnit změnou vlastnosti **název** . Adresní prostor podsítě lze změnit změnou vlastnosti **addressPrefix** v souboru **< resource-group-name >. JSON** :
    
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
                    ],
    ```

    Chcete-li změnit předponu adresy v souboru **< Resource-Group-name >. JSON** , je nutné ji upravit na dvou místech, v části uvedené výše a v níže uvedené části **typ** .  Změňte vlastnost **addressPrefix** tak, aby odpovídala hodnotě výše:
                
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

10. Uložte soubor **< Resource-Group-name >. JSON** .

11. Vytvoření skupiny prostředků v cílové oblasti pro nasazení cílové virtuální sítě pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
12. Pomocí [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)nasaďte < upravený soubor **resource-group-name >. JSON** do skupiny prostředků vytvořené v předchozím kroku:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

13. Pokud chcete ověřit, že se prostředky vytvořily v cílové oblasti, použijte příkaz [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Zahodit 

Pokud po nasazení chcete začít znovu nebo zrušit virtuální síť v cíli, odstraňte skupinu prostředků, která byla vytvořena v cíli a přesunutá virtuální síť bude odstraněna.  Chcete-li odebrat skupinu prostředků, použijte [příkaz Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun virtuální sítě, odstraňte zdrojovou virtuální síť nebo skupinu prostředků, použijte příkaz [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) nebo [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste přesunuli Azure Virtual Network z jedné oblasti na jinou a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

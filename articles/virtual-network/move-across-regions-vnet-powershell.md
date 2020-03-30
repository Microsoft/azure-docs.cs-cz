---
title: Přesunutí virtuální sítě Azure do jiné oblasti Azure pomocí Azure PowerShellu
description: Přesuňte virtuální síť Azure z jedné oblasti Azure do jiné pomocí šablony Správce prostředků a Azure PowerShellu.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646706"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Přesunutí virtuální sítě Azure do jiné oblasti pomocí Azure PowerShellu

Existují různé scénáře pro přesunutí existující virtuální sítě Azure z jedné oblasti do druhé. Můžete například chtít vytvořit virtuální síť se stejnou konfigurací pro testování a dostupnost jako stávající virtuální síť. Nebo můžete chtít přesunout produkční virtuální síť do jiné oblasti jako součást plánování zotavení po havárii.

K dokončení přesunu virtuální sítě do jiné oblasti můžete použít šablonu Azure Resource Manager. To provést exportem virtuální sítě do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a následným nasazením šablony do nové oblasti. Další informace o šablonách Správce prostředků naleznete v [tématu Export skupin prostředků do šablon](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že vaše virtuální síť je v oblasti Azure, ze které chcete přejít.

- Chcete-li exportovat virtuální síť a nasadit šablonu k vytvoření virtuální sítě v jiné oblasti, musíte mít roli přispěvatele sítě nebo vyšší.

- Partnerské servery virtuální sítě se znovu nevytvoří a nezdaří se, pokud jsou v šabloně stále k dispozici. Před exportem šablony je třeba odebrat všechny partnery virtuální sítě. Potom je můžete obnovit po přesunutí virtuální sítě.
    
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné vykladače zatížení, skupiny zabezpečení sítě (NSG) a veřejné IP adresy.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální sítě v cílové oblasti. Chcete-li povolit požadovanou kvótu, obraťte se na podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání virtuálních sítí pro tento proces. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Připravte se na přesun
V této části připravíte virtuální síť pro přesun pomocí šablony Správce prostředků. Potom přesunete virtuální síť do cílové oblasti pomocí příkazů Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete exportovat virtuální síť a nasadit cílovou virtuální síť pomocí PowerShellu, postupujte takto:

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Získejte ID prostředku virtuální sítě, kterou chcete přesunout do cílové oblasti, a umístěte ji do proměnné pomocí [get-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Export zdrojové virtuální sítě do souboru JSON v adresáři, ve kterém spouštíte příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Stažený soubor má stejný název jako skupina prostředků, ze které byl prostředek exportován. Vyhledejte soubor>.json, který jste exportovali pomocí příkazu, a otevřete ho v editoru: * \<*
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Chcete-li upravit parametr názvu virtuální sítě, změňte vlastnost **defaultValue** názvu zdrojové virtuální sítě na název cílové virtuální sítě. Název nezapomeňte uzavřít do uvozovek.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Chcete-li upravit cílovou oblast, do které bude virtuální síť přesunuta, změňte vlastnost **umístění** v části prostředky:

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
  
1. Chcete-li získat kódy umístění oblasti, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Nepovinné) V závislosti na požadavcích můžete také změnit další parametry v souboru * \<>.json* se skupinovým názvem prostředků:

    * **Adresní prostor**: Před uložením souboru můžete změnit adresní prostor virtuální sítě úpravou oddílu**adresní prostor** **prostředků a** > změnou vlastnosti **addressPrefixes:**

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

    * **Podsíť**: Název podsítě a adresní prostor podsítě můžete změnit změnou oddílu **podsítí.** Název podsítě můžete změnit změnou vlastnosti **name.** A můžete změnit adresní prostor podsítě změnou vlastnosti **addressPrefix:**

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

        Chcete-li změnit předponu adresy, upravte soubor na dvou místech: v kódu v předchozí části a v části **typu** následujícího kódu. Změňte vlastnost **addressPrefix** v následujícím kódu tak, aby odpovídala vlastnosti **addressPrefix** v kódu v předchozí části.

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

1. Uložte soubor * \<>.json se názvem skupiny prostředků.*

1. Vytvořte skupinu prostředků v cílové oblasti pro cílovou virtuální síť, která má být nasazena pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Nasazení upraveného * \<souboru>.json* s názvem skupiny prostředků do skupiny prostředků, kterou jste vytvořili v předchozím kroku pomocí [příkazu New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Chcete-li ověřit, zda byly prostředky vytvořeny v cílové oblasti, použijte [get-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [get-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Odstranění virtuální sítě nebo skupiny prostředků 

Po nasazení virtuální sítě chcete virtuální síť spustit nebo zahodit v cílové oblasti, odstraňte skupinu prostředků, kterou jste vytvořili v cílové oblasti, a přesunutá virtuální síť se odstraní. 

Chcete-li skupinu prostředků odebrat, použijte [použít skupinu Odebrat azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun virtuální sítě, proveďte některou z následujících akcí:

* Odstranění skupiny prostředků pomocí [skupiny Odebrat azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Odstranění zdrojové virtuální sítě pomocí [funkce Odebrat virtuální síť](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli virtuální síť z jedné oblasti do druhé pomocí prostředí PowerShell a potom vyčistit nepotřebné zdrojové prostředky. Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:

- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesunutí virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

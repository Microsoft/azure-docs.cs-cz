---
title: Přesuňte skupinu zabezpečení sítě Azure (NSG) do jiné oblasti Azure pomocí Azure PowerShell
description: Pomocí šablony Azure Resource Manager můžete přesunout skupinu zabezpečení sítě Azure z jedné oblasti Azure do jiné pomocí Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 04abc051cec8a6fb38ce6aa8f5347ae06cb8bd1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84688445"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Přesuňte skupinu zabezpečení sítě Azure (NSG) do jiné oblasti pomocí Azure PowerShell

Existují různé scénáře, ve kterých byste chtěli přesunout stávající skupin zabezpečení sítě z jedné oblasti do druhé. Například může být vhodné vytvořit NSG se stejnou konfigurací a pravidly zabezpečení pro testování. V rámci plánování zotavení po havárii možná budete chtít přesunout NSG do jiné oblasti.

Skupiny zabezpečení Azure nejde přesunout z jedné oblasti do druhé. K exportu existující konfigurace a pravidel zabezpečení NSG ale můžete použít šablonu Azure Resource Manager.  Pak můžete vytvořit prostředek v jiné oblasti tak, že NSG exportujete do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [Export skupin prostředků do šablon](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je skupina zabezpečení sítě Azure v oblasti Azure, ze které chcete přejít.

- Skupiny zabezpečení sítě Azure nelze přesouvat mezi oblastmi.  Bude nutné přidružit nový NSG k prostředkům v cílové oblasti.

- Pokud chcete exportovat konfiguraci NSG a nasadit šablonu pro vytvoření NSG v jiné oblasti, budete potřebovat roli Přispěvatel sítě nebo vyšší.
   
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, veřejné IP adresy a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet skupin zabezpečení sítě v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání skupin zabezpečení sítě pro tento proces.  Viz [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit skupinu zabezpečení sítě pro pravidlo konfigurace a zabezpečení přesunout pomocí šablony Správce prostředků a přesunout konfiguraci NSG a pravidla zabezpečení do cílové oblasti pomocí Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Export šablony a nasazení ze skriptu

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Získejte ID prostředku NSG, který chcete přesunout do cílové oblasti a umístit ho do proměnné pomocí [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportujte zdrojové NSG do souboru. JSON do adresáře, ve kterém spustíte příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Stažený soubor se pojmenuje po vytvoření skupiny prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem ** \<resource-group-name> . JSON** , a otevřete jej v editoru podle vlastního výběru:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Pokud chcete upravit parametr názvu NSG, změňte vlastnost **DefaultValue** zdrojového NSG názvu na název cílového NSGu, ujistěte se, že je název v uvozovkách:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Chcete-li upravit cílovou oblast, kde budou přesunuta konfigurace NSG a pravidla zabezpečení, změňte vlastnost **umístění** v části **prostředky**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Pokud chcete získat kódy umístění oblastí, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Můžete také změnit další parametry v ** \<resource-group-name> . JSON** , pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:

    * **Pravidla zabezpečení** – pravidla, která se nasazují do cílových NSG, můžete upravit přidáním nebo odebráním pravidel do oddílu **securityRules** v souboru ** \<resource-group-name> . JSON** :

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Chcete-li dokončit sčítání nebo odebírání pravidel v cílovém NSG, je nutné také upravit typy vlastních pravidel na konci souboru ** \<resource-group-name> . JSON** ve formátu níže uvedeného příkladu:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Uložte soubor ** \<resource-group-name> . JSON** .

10. Vytvořte skupinu prostředků v cílové oblasti pro nasazení cílového NSG pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Nasaďte upravený soubor ** \<resource-group-name> . JSON** do skupiny prostředků vytvořené v předchozím kroku pomocí [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Pokud chcete ověřit, že se prostředky vytvořily v cílové oblasti, použijte příkaz [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Zahodit 

Pokud po nasazení chcete začít znovu nebo zrušit NSG v cíli, odstraňte skupinu prostředků, která byla vytvořena v cíli, a přesunuté NSG se odstraní.  Chcete-li odebrat skupinu prostředků, použijte [příkaz Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Vyčištění

Pokud chcete potvrdit změny a dokončit přesun NSG, odstraňte zdrojový NSG nebo skupinu prostředků, použijte [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) nebo [Remove-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli skupinu zabezpečení sítě Azure z jedné oblasti na jinou a vyčistili jste zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

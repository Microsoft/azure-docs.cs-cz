---
title: Přesunutí skupiny zabezpečení sítě Azure (NSG) do jiné oblasti Azure pomocí Azure PowerShellu
description: Pomocí šablony Azure Resource Manager můžete pomocí Azure PowerShellu přesunout skupinu zabezpečení sítě Azure z jedné oblasti Azure do druhé.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641464"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Přesunutí skupiny zabezpečení sítě Azure (NSG) do jiné oblasti pomocí Azure PowerShellu

Existují různé scénáře, ve kterých byste chtěli přesunout existující nsg z jedné oblasti do druhé. Například můžete chtít vytvořit skupinu zabezpečení se stejnou konfigurací a bezpečnostními pravidly pro testování. Můžete také přesunout nsg do jiné oblasti jako součást plánování zotavení po havárii.

Skupiny zabezpečení Azure nelze přesunout z jedné oblasti do druhé. Šablonu Azure Resource Manager však můžete použít k exportu existujícíkonfigurace a bezpečnostních pravidel skupiny zabezpečení.  Potom můžete zinscenovat prostředek v jiné oblasti exportem skupiny nsg do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a potom šablonu nasadit do nové oblasti.  Další informace o Správci prostředků a šablonách naleznete v [tématu Export skupin prostředků do šablon](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že skupina zabezpečení sítě Azure je v oblasti Azure, ze které chcete přesunout.

- Skupiny zabezpečení sítě Azure nelze přesunout mezi oblastmi.  Budete muset přidružit nový skupiny nsg k prostředkům v cílové oblasti.

- Chcete-li exportovat konfiguraci skupiny zabezpečení sítě a nasadit šablonu k vytvoření skupiny zabezpečení sítě v jiné oblasti, budete potřebovat roli přispěvatele sítě nebo vyšší.
   
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné nástroje pro vyrovnávání zatížení, veřejné IP adresy a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet skupiny zabezpečení zabezpečení v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání nsg pro tento proces.  Viz [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit skupinu zabezpečení sítě pro přesunutí pravidla konfigurace a zabezpečení pomocí šablony Správce prostředků a přesunout pravidla konfigurace a zabezpečení skupiny zabezpečení sítě do cílové oblasti pomocí prostředí Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Export šablony a nasazení ze skriptu

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Získejte ID prostředku skupiny zabezpečení sítě, kterou chcete přesunout do cílové oblasti, a umístěte ji do proměnné pomocí [get-aznetworksecuritygroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Export zdrojového souboru nsg do souboru JSon do adresáře, ve kterém provedete příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Stažený soubor bude pojmenován po skupině prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem ** \<název skupiny prostředků>.json,** a otevřete jej v editoru podle vašeho výběru:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Chcete-li upravit parametr názvu skupiny zabezpečení zabezpečení, změňte vlastnost **defaultValue** zdrojového názvu skupiny zabezpečení zabezpečení na název cílové skupiny zabezpečení zabezpečení, ujistěte se, že je název v uvozovkách:
    
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


6. Chcete-li upravit cílovou oblast, ve které budou přesunuta pravidla konfigurace a zabezpečení skupiny zabezpečení, změňte vlastnost **umístění** v části **prostředky**:

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
  
7. Chcete-li získat kódy umístění oblasti, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Můžete také změnit další parametry v ** \<názvu skupiny prostředků>.json,** pokud zvolíte, a jsou volitelné v závislosti na vašich požadavcích:

    * **Pravidla zabezpečení** – Můžete upravit, která pravidla jsou nasazena do cílové skupiny zabezpečení nsg přidáním nebo odebráním pravidel do oddílu **SecurityRules** v souboru ** \<>.json název skupiny prostředků:**

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

        Chcete-li dokončit přidání nebo odebrání pravidel v cílové mj ** \<>.**

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

9. Uložte soubor ** \<>.json se názvem skupiny prostředků.**

10. Vytvořte skupinu prostředků v cílové oblasti pro cílovou skupinu nsg, která má být nasazena pomocí [new-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Nasazení upraveného ** \<souboru název skupiny prostředků>.json** do skupiny prostředků vytvořené v předchozím kroku pomocí [aplikace New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Chcete-li ověřit, zda byly prostředky vytvořeny v cílové oblasti, použijte [get-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [get-aznetworksecuritygroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Zahodit 

After the deployment, if you wish to start over or discard the NSG in the target, delete the resource group that was created in the target and the moved NSG will be deleted.  Chcete-li skupinu prostředků odebrat, použijte [použít skupinu Odebrat azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun skupiny zabezpečení sítě, odstraňte zdrojový soubor nsg nebo skupinu prostředků, použijte [odebrat-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) nebo [Remove-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli skupinu zabezpečení sítě Azure z jedné oblasti do druhé a vyčistili zdrojové prostředky.  Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

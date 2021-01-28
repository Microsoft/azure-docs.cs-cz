---
title: Přesuňte konfiguraci veřejné IP adresy Azure do jiné oblasti Azure pomocí Azure PowerShell
description: Pomocí šablony Azure Resource Manager můžete přesunout konfiguraci veřejné IP adresy Azure z jedné oblasti Azure do jiné pomocí Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: a21d088680855b74e7259028ed7ef55165707c56
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938684"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Přesuňte konfiguraci veřejné IP adresy Azure do jiné oblasti pomocí Azure PowerShell

Existují různé scénáře, ve kterých byste chtěli přesunout existující konfigurace veřejné IP adresy Azure z jedné oblasti do druhé. Například můžete chtít vytvořit veřejnou IP adresu se stejnou konfigurací a skladovou jednotkou pro testování. V rámci plánování zotavení po havárii možná budete chtít přesunout konfiguraci veřejné IP adresy do jiné oblasti.

**Veřejné IP adresy Azure jsou specifické pro oblast a nejde je přesunout z jedné oblasti do druhé.** Můžete ale použít šablonu Azure Resource Manager k exportu stávající konfigurace veřejné IP adresy.  Potom můžete prostředek připravit v jiné oblasti tak, že do šablony vyexportujete veřejnou IP adresu, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [Export skupin prostředků do šablon](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) .


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je veřejná IP adresa Azure v oblasti Azure, ze které chcete přejít.

- Veřejné IP adresy Azure se nedají přesouvat mezi oblastmi.  Novou veřejnou IP adresu budete muset přidružit k prostředkům v cílové oblasti.

- K exportu konfigurace veřejné IP adresy a nasazení šablony pro vytvoření veřejné IP adresy v jiné oblasti budete potřebovat roli Přispěvatel sítě nebo vyšší.
   
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (skupin zabezpečení sítě) a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet veřejné IP adresy v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu Přidání veřejných IP adres pro tento proces.  Viz [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit veřejnou IP adresu pro přesunutí konfigurace pomocí šablony Správce prostředků a přesunout konfiguraci veřejné IP adresy do cílové oblasti pomocí Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Export šablony a nasazení ze skriptu

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Získejte ID prostředku veřejné IP adresy, kterou chcete přesunout do cílové oblasti a umístit ji do proměnné pomocí [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportujte zdrojovou virtuální síť do souboru. JSON do adresáře, ve kterém spustíte příkaz [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Stažený soubor se pojmenuje po vytvoření skupiny prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem **\<resource-group-name> . JSON** , a otevřete jej v editoru podle vlastního výběru:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Pokud chcete upravit parametr názvu veřejné IP adresy, změňte vlastnost **DefaultValue** zdrojového názvu veřejné IP adresy na název cílové veřejné IP adresy, ujistěte se, že je název v uvozovkách:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Chcete-li upravit cílovou oblast, kam bude přesunuta veřejná IP adresa, změňte vlastnost **umístění** v části prostředky:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Pokud chcete získat kódy umístění oblastí, můžete použít rutinu Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:

    * **SKU** -SKU veřejné IP adresy můžete změnit v konfiguraci z úrovně Standard na Basic nebo Basic na standard, a to změnou   >  vlastnosti **název** SKU v souboru **\<resource-group-name> . JSON** :

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Další informace o rozdílech mezi veřejnými IP adresami Basic a Standard SKU najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](./virtual-network-public-ip-address.md).

    * **Metoda přidělování veřejných IP adres** a **časový limit nečinnosti** – obě tyto možnosti v šabloně můžete změnit tak, že změníte vlastnost **publicIPAllocationMethod** z **dynamické** na **statickou** nebo **statickou** na **dynamickou**. Časový limit nečinnosti lze změnit změnou vlastnosti **idleTimeoutInMinutes** na požadovanou hodnotu.  Výchozí hodnota je **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Další informace o metodách přidělování a hodnotách časového limitu nečinnosti najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](./virtual-network-public-ip-address.md).


9. Uložte soubor **\<resource-group-name> . JSON** .

10. Vytvořte skupinu prostředků v cílové oblasti pro nasazení cílové veřejné IP adresy pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Nasaďte upravený soubor **\<resource-group-name> . JSON** do skupiny prostředků vytvořené v předchozím kroku pomocí [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Pokud chcete ověřit, že se prostředky vytvořily v cílové oblasti, použijte příkaz [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Zahodit 

Pokud po nasazení chcete začít znovu nebo zrušit veřejnou IP adresu v cíli, odstraňte skupinu prostředků, která byla vytvořena v cíli a přesunutá veřejná IP adresa bude odstraněna.  Chcete-li odebrat skupinu prostředků, použijte [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun virtuální sítě, odstraňte zdrojovou virtuální síť nebo skupinu prostředků, použijte příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) nebo [Remove-AzPublicIPAddress](/powershell/module/az.network/remove-azpublicipaddress):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli veřejnou IP adresu Azure z jedné oblasti na jinou a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesun virtuálních počítačů Azure do jiné oblasti](../site-recovery/azure-to-azure-tutorial-migrate.md)

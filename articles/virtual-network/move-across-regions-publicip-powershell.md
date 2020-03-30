---
title: Přesunutí veřejné IP adresy Azure do jiné oblasti Azure pomocí Azure PowerShellu
description: Pomocí šablony Azure Resource Manager můžete přesunout veřejnou IP adresu Azure z jedné oblasti Azure do jiné pomocí Azure PowerShellu.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641533"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Přesunutí veřejné IP adresy Azure do jiné oblasti pomocí Azure PowerShellu

Existují různé scénáře, ve kterých byste chtěli přesunout existující veřejné IP adresy Azure z jedné oblasti do druhé. Můžete například vytvořit veřejnou IP adresu se stejnou konfigurací a sku pro testování. Můžete také přesunout veřejnou IP adresu do jiné oblasti jako součást plánování zotavení po havárii.

Veřejné IP adresy Azure jsou specifické pro oblast a nelze je přesunout z jedné oblasti do druhé. Šablonu Azure Resource Manager však můžete použít k exportu existující konfigurace veřejné IP adresy.  Potom můžete zinscenovat prostředek v jiné oblasti exportem veřejné IP adresy do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a potom šablonu nasadit do nové oblasti.  Další informace o Správci zdrojů a šablonách naleznete v [tématu Export skupin prostředků do šablon.](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že veřejná IP adresa Azure je v oblasti Azure, ze které chcete přesunout.

- Veřejné IP adresy Azure nelze přesunout mezi oblastmi.  Budete muset přidružit novou veřejnou IP adresu k prostředkům v cílové oblasti.

- Chcete-li exportovat konfiguraci veřejné IP adresy a nasadit šablonu k vytvoření veřejné IP adresy v jiné oblasti, budete potřebovat roli přispěvatele sítě nebo vyšší.
   
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (NSG) a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet veřejné IP adresy v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání veřejných IP adresy pro tento proces.  Viz [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit veřejnou IP adresu pro přesunutí konfigurace pomocí šablony Správce prostředků a přesunout konfiguraci veřejné IP adresy do cílové oblasti pomocí Azure PowerShellu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Export šablony a nasazení ze skriptu

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Získejte ID prostředku veřejné IP adresy, kterou chcete přesunout do cílové oblasti, a umístěte ji do proměnné pomocí [get-azPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Export zdrojové virtuální sítě do souboru JSON do adresáře, ve kterém spouštíte příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Stažený soubor bude pojmenován po skupině prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem ** \<název skupiny prostředků>.json,** a otevřete jej v editoru podle vašeho výběru:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Chcete-li upravit parametr veřejného názvu IP, změňte vlastnost **defaultValue** zdrojového veřejného názvu IP na název cílové veřejné IP adresy, ujistěte se, že je název v uvozovkách:
    
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

6. Chcete-li upravit cílovou oblast, do které bude veřejná IP adresa přesunuta, změňte vlastnost **umístění** v části zdroje:

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
  
7. Chcete-li získat kódy umístění oblasti, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Můžete také změnit další parametry v šabloně, pokud se rozhodnete, a jsou volitelné v závislosti na vašich požadavcích:

    * **Sku** - Můžete změnit sku veřejné IP v konfiguraci ze standardní ho základní nebo základní na standardní změnou **sku** > **název** vlastnost v souboru ** \<název skupiny prostředků>.json:**

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

         Další informace o rozdílech mezi základními a standardními veřejnými ip soubory sku naleznete v [tématu Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Metoda přidělení veřejné IP** adresy a **časový limit nečinnosti** – obě tyto možnosti v šabloně můžete změnit změnou vlastnosti **publicIPAllocationMethod** z **dynamické** na **statickou** nebo **statickou** na **dynamickou**. Časový limit nečinnosti lze změnit změnou vlastnosti **idleTimeoutInMinutes** na požadovanou částku.  Výchozí hodnota je **4**:

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

        Další informace o metodách přidělení a hodnotách časového limitu nečinnosti naleznete v [tématu Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Uložte soubor ** \<>.json se názvem skupiny prostředků.**

10. Vytvořte skupinu prostředků v cílové oblasti pro cílovou veřejnou IP adresu, která má být nasazena pomocí [new-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Nasazení upraveného ** \<souboru název skupiny prostředků>.json** do skupiny prostředků vytvořené v předchozím kroku pomocí [aplikace New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Chcete-li ověřit, zda byly prostředky vytvořeny v cílové oblasti, použijte [get-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [get-azpublicIPaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Zahodit 

Pokud chcete po nasazení začít znovu nebo zahodit veřejnou IP adresu v cíli, odstraňte skupinu prostředků, která byla vytvořena v cíli, a přesunutá veřejná IP adresa bude odstraněna.  Chcete-li skupinu prostředků odebrat, použijte [použít skupinu Odebrat azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun virtuální sítě, odstraňte zdrojovou virtuální síť nebo skupinu prostředků, použijte [odebrat-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) nebo [Remove-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli veřejnou IP adresu Azure z jedné oblasti do druhé a vyčistili zdrojové prostředky.  Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

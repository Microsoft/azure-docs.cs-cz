---
title: Přesunutí externího služby Provyrovnávání zatížení Azure do jiné oblasti Azure pomocí Azure PowerShellu
description: Pomocí šablony Azure Resource Manager můžete pomocí Azure PowerShellu přesunout externí nástroje pro vyrovnávání zatížení Azure z jedné oblasti Azure do jiné.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644813"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Přesunutí externího služby Provyrovnávání zatížení Azure do jiné oblasti pomocí Azure PowerShellu

Existují různé scénáře, ve kterých byste chtěli přesunout existující externí vyrovnávání zatížení z jedné oblasti do druhé. Můžete například vytvořit externí nástroj pro vyrovnávání zatížení se stejnou konfigurací pro testování. Můžete také přesunout externí vyrovnávání zatížení do jiné oblasti jako součást plánování zotavení po havárii.

Externí vyvyčovávače zatížení Azure nelze přesunout z jedné oblasti do druhé. Šablonu Azure Resource Manager však můžete použít k exportu existující konfigurace a veřejné IP adresy externího nástroje pro vyrovnávání zatížení.  Potom můžete zinscenovat prostředek v jiné oblasti exportem vyrovnávání zatížení a veřejné IP adresy do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a pak nasadit šablony do nové oblasti.  Další informace o Správci zdrojů a šablonách naleznete v [tématu Export skupin prostředků do šablon.](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že externí vyrovnávání zatížení Azure je v oblasti Azure, ze kterého chcete přesunout.

- Externí vyvyčažatelé zatížení Azure nelze přesunout mezi oblastmi.  Budete muset přidružit nový systém vyrovnávání zatížení k prostředkům v cílové oblasti.

- Chcete-li exportovat externí konfiguraci nástroje pro vyrovnávání zatížení a nasadit šablonu k vytvoření externího nástroje pro vyrovnávání zatížení v jiné oblasti, budete potřebovat roli přispěvatele sítě nebo vyšší.
   
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, veřejné IP adresy a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet externí vyrovnávání zatížení v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání vyrovnávání zatížení pro tento proces.  Viz [Omezení předplatného a služeb Azure, kvóty a omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit externí nástroj pro vyrovnávání zatížení pro přesun pomocí šablony Správce prostředků a přesunout konfiguraci externího nástroje pro vyrovnávání zatížení do cílové oblasti pomocí prostředí Azure PowerShell.  V rámci tohoto procesu musí být zahrnuta veřejná konfigurace IP externího nástroje pro vyrovnávání zatížení a musí být provedena nejprve před přesunutím externího nástroje pro vyrovnávání zatížení.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Export veřejné šablony IP a nasazení z Azure PowerShellu

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Získejte ID prostředku veřejné IP adresy, kterou chcete přesunout do cílové oblasti, a umístěte ji do proměnné pomocí [get-azPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportzdrojové veřejné IP adresy do souboru JSON do adresáře, do kterého spouštíte příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Stažený soubor bude pojmenován po skupině prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem ** \<název skupiny prostředků>.json,** a otevřete jej v editoru podle vašeho výběru:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
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

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Export externí šablony pro vyrovnávání zatížení a nasazení z Azure PowerShellu

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Získejte ID prostředku externího zařízení pro vyrovnávání zatížení, které chcete přesunout do cílové oblasti, a umístěte jej do proměnné pomocí [služby Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportujte konfiguraci zdrojového externího nástroje pro vyrovnávání zatížení do souboru JSON do adresáře, ve kterém provedete příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Stažený soubor bude pojmenován po skupině prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem ** \<název skupiny prostředků>.json,** a otevřete jej v editoru podle vašeho výběru:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Chcete-li upravit parametr názvu externího správce zatížení, změňte vlastnost **defaultValue** názvu zdrojového externího správce zatížení na název cílového externího systému vyrovnávání zatížení, ujistěte se, že je název v uvozovkách:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Chcete-li upravit hodnotu cílové veřejné IP adresy, která byla přesunuta výše, musíte nejprve získat ID prostředku a potom jej zkopírovat a vložit do souboru ** \<>.json.**  Chcete-li získat ID, použijte [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Zadejte proměnnou a stiskněte klávesu ENTER, chcete-li zobrazit ID zdroje.  Zvýrazněte cestu ID a zkopírujte ji do schránky:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  V souboru ** \<>.json název skupiny prostředků** vložte **ID prostředku** z proměnné místo **defaultValue** do druhého parametru pro externí ID veřejné IP adresy, ujistěte se, že cestu uzavřete do uvozovek:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Pokud jste nakonfigurovali odchozí NAT a odchozí pravidla pro nástroje pro vyrovnávání zatížení, bude v tomto souboru k dispozici třetí položka pro externí ID odchozí veřejné IP adresy.  Opakováním výše uvedených kroků v **cílové oblasti** získáte ID odchozího veřejného iP a vložte tuto položku do souboru ** \<>.json,** který je název skupiny prostředků:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Chcete-li upravit cílovou oblast, ve které bude přesunuta externí konfigurace nástroje pro vyrovnávání zatížení, změňte vlastnost **umístění** pod **prostředky** v souboru ** \<>.json s názvem skupiny prostředků:**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Chcete-li získat kódy umístění oblasti, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Můžete také změnit další parametry v šabloně, pokud se rozhodnete, a jsou volitelné v závislosti na vašich požadavcích:
    
    * **Sku** - Můžete změnit sku externí nástroj pro vyrovnávání zatížení v konfiguraci ze standardního nebo základní ho standardní ho diazměnit vlastnost**název** **sku** > v souboru ** \<název skupiny prostředků>.json:**

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Další informace o rozdílech mezi základními a standardními nástroje pro vyrovnávání zatížení sku najdete [v tématu Přehled nástroje pro vyrovnávání zatížení azure standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Pravidla vyrovnávání zatížení** – Můžete přidat nebo odebrat pravidla vyrovnávání zatížení v konfiguraci přidáním nebo odebráním položek do části **loadBalancingRules** ** \<>.json** souboru skupiny:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Další informace o pravidlech vyrovnávání zatížení najdete v tématu [Co je Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondy** – sondu pro nástroj pro vyrovnávání zatížení v konfiguraci můžete **probes** přidat nebo odebrat přidáním nebo odebráním položek do oddílu sond ** \<>.json:**

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Další informace o sondách stavu služby Azure Balancer najdete v tématu [sondy stavu vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Příchozí pravidla PŘEKLADU –** Můžete přidat nebo odebrat příchozí pravidla NAT pro vyrovnávání zatížení přidáním nebo odebráním položek do oddílu **inboundNatRules** v souboru ** \<>.json, který je název skupiny prostředků:**

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Chcete-li dokončit přidání nebo odebrání pravidla příchozího překladu nanesení, musí být pravidlo přítomno nebo odebráno jako vlastnost **typu** na konci souboru ** \<>.json, který je název skupiny prostředků:**

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Další informace o příchozích pravidlech NAT najdete v tématu [Co je Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Odchozí pravidla** – Odchozí pravidla v konfiguraci můžete přidat nebo odebrat úpravou vlastnosti **outboundRules** v souboru ** \<>.json, který je název skupiny prostředků:**

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Další informace o odchozích pravidlech naleznete v tématu [Odchozí pravidla vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. Uložte soubor ** \<>.json se názvem skupiny prostředků.**
    
10. Vytvořte nebo vytvořte skupinu prostředků v cílové oblasti pro cílový externí systém vyrovnávání zatížení, který má být nasazen pomocí [skupiny New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Existující skupinu prostředků shora lze také znovu použít jako součást tohoto procesu:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Nasazení upraveného ** \<souboru název skupiny prostředků>.json** do skupiny prostředků vytvořené v předchozím kroku pomocí [aplikace New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Chcete-li ověřit, zda byly prostředky vytvořeny v cílové oblasti, použijte [get-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Zahodit 

Pokud chcete po nasazení začít znovu nebo zahodit veřejnou IP adresu a vyvyčovánek zatížení v cíli, odstraňte skupinu prostředků, která byla vytvořena v cíli, a přesunutá veřejná IP adresa a likvidátor zatížení budou odstraněny.  Chcete-li skupinu prostředků odebrat, použijte [použít skupinu Odebrat azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun skupiny nsg, odstraňte zdrojový skupinu nsg nebo skupinu prostředků, použijte [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) nebo [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) a [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli skupinu zabezpečení sítě Azure z jedné oblasti do druhé a vyčistili zdrojové prostředky.  Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

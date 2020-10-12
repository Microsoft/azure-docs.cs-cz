---
title: Přesunout externí Load Balancer Azure do jiné oblasti Azure pomocí Azure PowerShell
description: Pomocí šablony Azure Resource Manager můžete přesunout externí Load Balancer Azure z jedné oblasti Azure do jiné pomocí Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: be1971c9184d0b2b406b669ae9d1ea61598b201f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84809433"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Přesunout externí Load Balancer Azure do jiné oblasti pomocí Azure PowerShell

Existují různé scénáře, ve kterých byste chtěli přesunout existující externí nástroj pro vyrovnávání zatížení z jedné oblasti do druhé. Například může být vhodné vytvořit externí nástroj pro vyrovnávání zatížení se stejnou konfigurací pro testování. Externí nástroj pro vyrovnávání zatížení můžete také přesunout do jiné oblasti v rámci plánování zotavení po havárii.

Externí nástroje pro vyrovnávání zatížení Azure nejde přesunout z jedné oblasti do druhé. Můžete ale použít šablonu Azure Resource Manager k exportu existující konfigurace a veřejné IP adresy externího nástroje pro vyrovnávání zatížení.  Potom můžete prostředek připravit v jiné oblasti tak, že vyexportujete Nástroj pro vyrovnávání zatížení a veřejnou IP adresu do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablony nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [Export skupin prostředků do šablon](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) .


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je externí nástroj pro vyrovnávání zatížení Azure v oblasti Azure, ze které chcete přejít.

- Externí nástroje pro vyrovnávání zatížení Azure se nedají přesouvat mezi oblastmi.  Nový nástroj pro vyrovnávání zatížení bude nutné přidružit k prostředkům v cílové oblasti.

- Pokud chcete exportovat konfiguraci externího nástroje pro vyrovnávání zatížení a nasadit šablonu pro vytvoření externího nástroje pro vyrovnávání zatížení v jiné oblasti, budete potřebovat roli Přispěvatel sítě nebo vyšší.
   
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, veřejné IP adresy a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet externí nástroje pro vyrovnávání zatížení v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání nástrojů pro vyrovnávání zatížení pro tento proces.  Viz [limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit externí nástroj pro vyrovnávání zatížení pro přesun pomocí šablony Správce prostředků a přesunout konfiguraci externího nástroje pro vyrovnávání zatížení do cílové oblasti pomocí Azure PowerShell.  V rámci tohoto procesu musí být před přesunutím externího nástroje pro vyrovnávání zatížení zahrnutá konfigurace veřejné IP adresy externího nástroje pro vyrovnávání zatížení a musí se nejdřív udělat.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Exportujte šablonu veřejné IP adresy a nasaďte ji z Azure PowerShell

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Získejte ID prostředku veřejné IP adresy, kterou chcete přesunout do cílové oblasti a umístit ji do proměnné pomocí [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportujte zdrojovou veřejnou IP adresu do souboru. JSON do adresáře, ve kterém spustíte příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Stažený soubor se pojmenuje po vytvoření skupiny prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem ** \<resource-group-name> . JSON** , a otevřete jej v editoru podle vlastního výběru:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
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
  
7. Pokud chcete získat kódy umístění oblastí, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:

    * **SKU** -SKU veřejné IP adresy můžete změnit v konfiguraci z úrovně Standard na Basic nebo Basic na standard, a to změnou **sku**  >  vlastnosti**název** SKU v souboru ** \<resource-group-name> . JSON** :

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

         Další informace o rozdílech mezi veřejnými IP adresami Basic a Standard SKU najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

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

        Další informace o metodách přidělování a hodnotách časového limitu nečinnosti najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Uložte soubor ** \<resource-group-name> . JSON** .

10. Vytvořte skupinu prostředků v cílové oblasti pro nasazení cílové veřejné IP adresy pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Nasaďte upravený soubor ** \<resource-group-name> . JSON** do skupiny prostředků vytvořené v předchozím kroku pomocí [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Pokud chcete ověřit, že se prostředky vytvořily v cílové oblasti, použijte příkaz [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportujte šablonu externího nástroje pro vyrovnávání zatížení a nasaďte ji z Azure PowerShell

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Získejte ID prostředku externího nástroje pro vyrovnávání zatížení, který chcete přesunout do cílové oblasti a umístit ho do proměnné pomocí [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportujte konfiguraci externího nástroje pro vyrovnávání zatížení do souboru. JSON do adresáře, kde spustíte příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Stažený soubor se pojmenuje po vytvoření skupiny prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem ** \<resource-group-name> . JSON** , a otevřete jej v editoru podle vlastního výběru:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Chcete-li upravit parametr názvu externí služby Vyrovnávání zatížení, změňte vlastnost **DefaultValue** zdrojového externího nástroje pro vyrovnávání zatížení na název vašeho cílového externího nástroje pro vyrovnávání zatížení, ujistěte se, že je název v uvozovkách:

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

6.  Pokud chcete upravit hodnotu cílové veřejné IP adresy, která se přesunula výše, musíte nejdřív získat ID prostředku a pak ho zkopírovat a vložit do souboru ** \<resource-group-name> . JSON** .  K získání ID použijte [příkaz Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Zadejte proměnnou a stiskněte Enter pro zobrazení ID prostředku.  Zvýrazněte cestu ID a zkopírujte ji do schránky:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  V souboru ** \<resource-group-name> . JSON** vložte **ID prostředku** z proměnné místo parametru **DEFAULTVALUE** ve druhém parametru pro externí ID veřejné IP adresy, nezapomeňte uzavřít cestu v uvozovkách:

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

8.  Pokud jste nakonfigurovali odchozí NAT a odchozí pravidla pro nástroj pro vyrovnávání zatížení, v tomto souboru se objeví třetí položka pro externí ID odchozí veřejné IP adresy.  Opakujte výše uvedené kroky v **cílové oblasti** , abyste získali ID odchozí veřejné IP adresy a vložili tuto položku do souboru ** \<resource-group-name> . JSON** :

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

10. Chcete-li upravit cílovou oblast, kde bude přesunuta konfigurace externího nástroje pro vyrovnávání zatížení, změňte vlastnost **umístění** v části **prostředky** v souboru ** \<resource-group-name> . JSON** :

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

11. Pokud chcete získat kódy umístění oblastí, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:
    
    * **SKU** -SKU externí služby Load Balancer můžete změnit v konfiguraci z úrovně Standard na Basic nebo Basic na úroveň Standard, a to změnou vlastnosti název **SKU**  >  **name** v souboru ** \<resource-group-name> . JSON** :

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
      Další informace o rozdílech mezi nástroji pro vyrovnávání zatížení Basic a Standard SKU najdete v tématu [Přehled služby Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) .

    * **Pravidla vyrovnávání zatížení** – v konfiguraci můžete přidat nebo odebrat pravidla vyrovnávání zatížení přidáním nebo odebráním položek do oddílu **loadBalancingRules** souboru ** \<resource-group-name> . JSON** :

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
       Další informace o pravidlech vyrovnávání zatížení najdete v tématu [co je Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Sondy** – pro nástroj pro vyrovnávání zatížení v konfiguraci můžete přidat nebo odebrat test tak, že do části **sondy** v souboru ** \<resource-group-name> . JSON** přidáte nebo odeberete položky.

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
       Další informace o sondách stavu Azure Load Balancer najdete v tématu [Load Balancer sondy stavu](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) .

    * **Příchozí pravidla NAT** – pravidla příchozího překladu adres (NAT) pro nástroj pro vyrovnávání zatížení můžete přidat nebo odebrat tak, že do části **inboundNatRules** souboru ** \<resource-group-name> . JSON** přidáte nebo odeberete položky.

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
        Aby bylo možné dokončit přidání nebo odebrání pravidla příchozího překladu adres (NAT), musí být pravidlo přítomno nebo odebráno jako vlastnost **typu** na konci souboru ** \<resource-group-name> . JSON** :

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
        Další informace o příchozích pravidlech NAT najdete v tématu [co je Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Odchozí pravidla** – v konfiguraci můžete přidat nebo odebrat odchozí pravidla úpravou vlastnosti **outboundRules** v souboru ** \<resource-group-name> . JSON** :

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

         Další informace o odchozích pravidlech najdete v tématu [Load Balancer odchozích pravidel](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview) .

13. Uložte soubor ** \<resource-group-name> . JSON** .
    
10. Vytvořte nebo skupinu prostředků v cílové oblasti pro cílový externí nástroj pro vyrovnávání zatížení, který se má nasadit pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). V rámci tohoto procesu je možné znovu použít stávající skupinu prostředků z výše uvedeného:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Nasaďte upravený soubor ** \<resource-group-name> . JSON** do skupiny prostředků vytvořené v předchozím kroku pomocí [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Pokud chcete ověřit, že se prostředky vytvořily v cílové oblasti, použijte příkaz [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Zahodit 

Pokud po nasazení chcete začít znovu nebo zahodit veřejnou IP a nástroj pro vyrovnávání zatížení v cíli, odstraňte skupinu prostředků vytvořenou v cíli a přesunutou veřejnou IP adresu a nástroj pro vyrovnávání zatížení odstraní.  Chcete-li odebrat skupinu prostředků, použijte [příkaz Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Vyčištění

Pokud chcete potvrdit změny a dokončit přesun NSG, odstraňte zdrojový NSG nebo skupinu prostředků, použijte [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) nebo [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) a [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0) .

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli skupinu zabezpečení sítě Azure z jedné oblasti na jinou a vyčistili jste zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

---
title: Přesunutí interních Load Balancer Azure do jiné oblasti Azure pomocí Azure PowerShell
description: Pomocí šablony Azure Resource Manager můžete přesunout interní Load Balancer Azure z jedné oblasti Azure do jiné pomocí Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 73a9356de555e33996b92f05c3bbbabb651f1c9f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014223"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Přesunutí interních Load Balancer Azure do jiné oblasti pomocí prostředí PowerShell

Existují různé scénáře, ve kterých byste chtěli přesunout stávající interní nástroj pro vyrovnávání zatížení z jedné oblasti do druhé. Například může být vhodné vytvořit interní nástroj pro vyrovnávání zatížení se stejnou konfigurací pro testování. Interní nástroj pro vyrovnávání zatížení můžete také přesunout do jiné oblasti v rámci plánování zotavení po havárii.

Interní nástroje pro vyrovnávání zatížení Azure nejde přesunout z jedné oblasti do druhé. Můžete ale použít šablonu Azure Resource Manager k exportu existující konfigurace a virtuální sítě interního nástroje pro vyrovnávání zatížení.  Pak můžete tento prostředek připravit v jiné oblasti tak, že vyexportujete Nástroj pro vyrovnávání zatížení a virtuální síť do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablony nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [Export skupin prostředků do šablon](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) .


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že interní nástroj pro vyrovnávání zatížení Azure je v oblasti Azure, ze které chcete přejít.

- Interní nástroje pro vyrovnávání zatížení Azure se nedají přesouvat mezi oblastmi.  Nový nástroj pro vyrovnávání zatížení bude nutné přidružit k prostředkům v cílové oblasti.

- Pokud chcete exportovat konfiguraci interního nástroje pro vyrovnávání zatížení a nasadit šablonu pro vytvoření interního nástroje pro vyrovnávání zatížení v jiné oblasti, budete potřebovat roli Přispěvatel sítě nebo vyšší.
   
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, virtuální počítače a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet interní nástroje pro vyrovnávání zatížení v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání nástrojů pro vyrovnávání zatížení pro tento proces.  Viz [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit interní nástroj pro vyrovnávání zatížení pro přesun pomocí šablony Správce prostředků a přesunout konfiguraci interního nástroje pro vyrovnávání zatížení do cílové oblasti pomocí Azure PowerShell.  V rámci tohoto procesu musí být před přesunutím interního nástroje pro vyrovnávání zatížení součástí konfigurace virtuální sítě interního nástroje pro vyrovnávání zatížení a musí se nejdřív provést.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Exportovat šablonu virtuální sítě a nasadit z Azure PowerShell

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Získejte ID prostředku virtuální sítě, kterou chcete přesunout do cílové oblasti a umístit ji do proměnné pomocí [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportujte zdrojovou virtuální síť do souboru. JSON do adresáře, ve kterém spustíte příkaz [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Stažený soubor se pojmenuje po vytvoření skupiny prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem **\<resource-group-name> . JSON** , a otevřete jej v editoru podle vlastního výběru:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Pokud chcete upravit parametr názvu virtuální sítě, změňte vlastnost **DefaultValue** názvu zdrojové virtuální sítě na název cílové virtuální sítě, ujistěte se, že je název v uvozovkách:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Pokud chcete upravit cílovou oblast, kam se virtuální síť přesune, změňte vlastnost **umístění** v části prostředky:

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
  
7. Pokud chcete získat kódy umístění oblastí, můžete použít rutinu Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Můžete také změnit další parametry v souboru **\<resource-group-name> . JSON** , pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:

    * **Adresní prostor** – adresní prostor virtuální sítě je možné změnit před uložením úpravou části addressSpace **prostředků**  >  **addressSpace** a změnou vlastnosti **addressPrefixes** v souboru **\<resource-group-name> . JSON** :

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

    * **Podsíť** – název podsítě a adresní prostor podsítě lze změnit nebo přidat k úpravou části **podsítě** souboru **\<resource-group-name> . JSON** . Název podsítě lze změnit změnou vlastnosti **název** . Adresní prostor podsítě lze změnit změnou vlastnosti **addressPrefix** v souboru **\<resource-group-name> . JSON** :

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

         Chcete-li změnit předponu adresy, je třeba v souboru **\<resource-group-name> . JSON** upravit na dvou místech, v části uvedené výše a v níže uvedené části **typ** .  Změňte vlastnost **addressPrefix** tak, aby odpovídala hodnotě výše:

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

9.  Uložte soubor **\<resource-group-name> . JSON** .

10. Vytvoření skupiny prostředků v cílové oblasti pro nasazení cílové virtuální sítě pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Nasaďte upravený soubor **\<resource-group-name> . JSON** do skupiny prostředků vytvořené v předchozím kroku pomocí [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Pokud chcete ověřit, že se prostředky vytvořily v cílové oblasti, použijte příkaz [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportujte šablonu interního nástroje pro vyrovnávání zatížení a nasaďte ji z Azure PowerShell

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Získejte ID prostředku interního nástroje pro vyrovnávání zatížení, který chcete přesunout do cílové oblasti a umístit ho do proměnné pomocí [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportujte konfiguraci interního nástroje pro vyrovnávání zatížení do souboru. JSON do adresáře, kde spustíte příkaz [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Stažený soubor se pojmenuje po vytvoření skupiny prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem **\<resource-group-name> . JSON** , a otevřete jej v editoru podle vlastního výběru:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Chcete-li upravit parametr názvu interního nástroje pro vyrovnávání zatížení, změňte vlastnost **DefaultValue** zdrojového interního nástroje pro vyrovnávání zatížení na název cílového interního nástroje pro vyrovnávání zatížení, ujistěte se, že je název v uvozovkách:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Pokud chcete upravit hodnotu cílové virtuální sítě, která se přesunula výše, musíte nejdřív získat ID prostředku a pak ho zkopírovat a vložit do souboru **\<resource-group-name> . JSON** .  K získání ID použijte [příkaz Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Zadejte proměnnou a stiskněte Enter pro zobrazení ID prostředku.  Zvýrazněte cestu ID a zkopírujte ji do schránky:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  V souboru **\<resource-group-name> . JSON** vložte **ID prostředku** z proměnné místo parametru **DefaultValue** ve druhém parametru pro ID cílové virtuální sítě, nezapomeňte uzavřít cestu do uvozovek:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Chcete-li upravit cílovou oblast, kde bude přesunuta konfigurace interního nástroje pro vyrovnávání zatížení, změňte vlastnost **umístění** v části **prostředky** v souboru **\<resource-group-name> . JSON** :

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Pokud chcete získat kódy umístění oblastí, můžete použít rutinu Azure PowerShell [Get-AzLocation](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:
    
    * **SKU** -SKU interního nástroje pro vyrovnávání zatížení v konfiguraci z úrovně Standard na Basic nebo Basic na standard změňte změnou **sku**  >  vlastnosti **název** SKU v souboru **\<resource-group-name> . JSON** :

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Další informace o rozdílech mezi nástroji pro vyrovnávání zatížení Basic a Standard SKU najdete v tématu [Přehled služby Azure Standard Load Balancer](./load-balancer-overview.md) .

    * **Pravidla vyrovnávání zatížení** – v konfiguraci můžete přidat nebo odebrat pravidla vyrovnávání zatížení přidáním nebo odebráním položek do oddílu **loadBalancingRules** souboru **\<resource-group-name> . JSON** :

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
       Další informace o pravidlech vyrovnávání zatížení najdete v tématu [co je Azure Load Balancer?](./load-balancer-overview.md)

    * **Sondy** – pro nástroj pro vyrovnávání zatížení v konfiguraci můžete přidat nebo odebrat test tak, že do části **sondy** v souboru **\<resource-group-name> . JSON** přidáte nebo odeberete položky.

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
       Další informace o sondách stavu Azure Load Balancer najdete v tématu [Load Balancer sondy stavu](./load-balancer-custom-probe-overview.md) .

    * **Příchozí pravidla NAT** – pravidla příchozího překladu adres (NAT) pro nástroj pro vyrovnávání zatížení můžete přidat nebo odebrat tak, že do části **inboundNatRules** souboru **\<resource-group-name> . JSON** přidáte nebo odeberete položky.

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
        Aby bylo možné dokončit přidání nebo odebrání pravidla příchozího překladu adres (NAT), musí být pravidlo přítomno nebo odebráno jako vlastnost **typu** na konci souboru **\<resource-group-name> . JSON** :

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
        Další informace o příchozích pravidlech NAT najdete v tématu [co je Azure Load Balancer?](./load-balancer-overview.md)
    
13. Uložte soubor **\<resource-group-name> . JSON** .
    
10. Vytvořte nebo skupinu prostředků v cílové oblasti pro cílový interní nástroj pro vyrovnávání zatížení, který se má nasadit pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). V rámci tohoto procesu je možné znovu použít stávající skupinu prostředků z výše uvedeného:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Nasaďte upravený soubor **\<resource-group-name> . JSON** do skupiny prostředků vytvořené v předchozím kroku pomocí [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Pokud chcete ověřit, že se prostředky vytvořily v cílové oblasti, použijte příkaz [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Zahodit 

Pokud po nasazení chcete začít znovu nebo zrušit virtuální síť a nástroj pro vyrovnávání zatížení v cíli, odstraňte skupinu prostředků vytvořenou v cíli a přesunutou virtuální síť a nástroj pro vyrovnávání zatížení se odstraní.  Chcete-li odebrat skupinu prostředků, použijte [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Vyčištění

Pokud chcete potvrdit změny a dokončit přesun NSG, odstraňte zdrojový NSG nebo skupinu prostředků, použijte [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) nebo [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) a [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0) .

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli interní nástroj pro vyrovnávání zatížení Azure z jedné oblasti na jiný a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesun virtuálních počítačů Azure do jiné oblasti](../site-recovery/azure-to-azure-tutorial-migrate.md)
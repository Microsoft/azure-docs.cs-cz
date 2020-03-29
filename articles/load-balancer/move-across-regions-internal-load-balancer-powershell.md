---
title: Přesunutí interního služby Provynaci zatížení Azure do jiné oblasti Azure pomocí Azure PowerShellu
description: Použití šablony Azure Resource Manager u přesunu Azure internal Load Balancer z jedné oblasti Azure do jiné pomocí Azure PowerShellu
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644269"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Přesunutí interního vyvažovače zatížení Azure do jiné oblasti pomocí Prostředí PowerShell

Existují různé scénáře, ve kterých byste chtěli přesunout stávající interní vyrovnávání zatížení z jedné oblasti do druhé. Můžete například vytvořit interní nástroj pro vyrovnávání zatížení se stejnou konfigurací pro testování. Můžete také přesunout interní vyrovnávání zatížení do jiné oblasti jako součást plánování zotavení po havárii.

Interní vynakladače zatížení Azure nelze přesunout z jedné oblasti do druhé. Šablonu Azure Resource Manager však můžete použít k exportu existující konfigurace a virtuální sítě interního nástroje pro vyrovnávání zatížení.  Potom můžete nastínit prostředek v jiné oblasti exportem nástroje pro vyrovnávání zatížení a virtuální sítě do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a následným nasazením šablon do nové oblasti.  Další informace o Správci zdrojů a šablonách naleznete v [tématu Export skupin prostředků do šablon.](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že azure interní vyrovnávání zatížení je v oblasti Azure, ze kterého chcete přesunout.

- Interní vynakladače zatížení Azure nelze přesunout mezi oblastmi.  Budete muset přidružit nový systém vyrovnávání zatížení k prostředkům v cílové oblasti.

- Chcete-li exportovat konfiguraci interního nástroje pro vyrovnávání zatížení a nasadit šablonu k vytvoření interního nástroje pro vyrovnávání zatížení v jiné oblasti, budete potřebovat roli přispěvatele sítě nebo vyšší.
   
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, virtuální počítače a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet interní vyrovnávání zatížení v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání vyrovnávání zatížení pro tento proces.  Viz [Omezení předplatného a služeb Azure, kvóty a omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit interní nástroj pro vyrovnávání zatížení pro přesun pomocí šablony Správce prostředků a přesunout konfiguraci interního nástroje pro vyrovnávání zatížení do cílové oblasti pomocí prostředí Azure PowerShell.  V rámci tohoto procesu musí být zahrnuta konfigurace virtuální sítě interního nástroje pro vyrovnávání zatížení a musí být provedena nejprve před přesunutím interního nástroje pro vyrovnávání zatížení.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Export šablony virtuální sítě a nasazení z Azure PowerShellu

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Získejte ID prostředku virtuální sítě, kterou chcete přesunout do cílové oblasti, a umístěte ji do proměnné pomocí [get-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Export zdrojové virtuální sítě do souboru JSON do adresáře, ve kterém spouštíte příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Stažený soubor bude pojmenován po skupině prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem ** \<název skupiny prostředků>.json,** a otevřete jej v editoru podle vašeho výběru:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Chcete-li upravit parametr názvu virtuální sítě, změňte vlastnost **defaultValue** názvu zdrojové virtuální sítě na název cílové virtuální sítě, ujistěte se, že je název v uvozovkách:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Chcete-li upravit cílovou oblast, do které bude virtuální síť přesunuta, změňte vlastnost **umístění** v části prostředky:

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
  
7. Chcete-li získat kódy umístění oblasti, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Můžete také změnit další parametry v souboru ** \<název skupiny prostředků>.json,** pokud zvolíte, a jsou volitelné v závislosti na vašich požadavcích:

    * **Adresní prostor** – Adresní prostor virtuální sítě lze před uložením změnit úpravou oddílu**adresní prostor** **prostředků** > a změnou vlastnosti **addressPrefixes** v souboru ** \<>.json, který je název skupiny prostředků:**

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

    * **Podsíť** - Název podsítě a adresní prostor podsítě lze změnit nebo přidat úpravou oddílu **podsítí** v souboru ** \<>.json, který je název skupiny prostředků.** Název podsítě lze změnit změnou vlastnosti **názvu.** Adresní prostor podsítě lze změnit změnou vlastnosti **addressPrefix** v souboru ** \<>.json s názvem skupiny prostředků:**

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

         Chcete-li změnit předponu adresy, musí být v souboru ** \<>.json** název skupiny prostředků upravena na dvou místech, výše uvedené části a v části **typu** uvedené níže.  Změňte vlastnost **addressPrefix** tak, aby odpovídala výše uvedené vlastnosti:

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

9.  Uložte soubor ** \<>.json se názvem skupiny prostředků.**

10. Vytvoření skupiny prostředků v cílové oblasti pro cílovou virtuální síť, která má být nasazena pomocí [skupiny New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Nasazení upraveného ** \<souboru název skupiny prostředků>.json** do skupiny prostředků vytvořené v předchozím kroku pomocí [aplikace New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Chcete-li ověřit, zda byly prostředky vytvořeny v cílové oblasti, použijte [get-azresourcegroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) a [get-azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportovat interní šablonu pro vyrovnávání zatížení a nasadit z Azure PowerShellu

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Získejte ID prostředku interního zařízení pro vyrovnávání zatížení, které chcete přesunout do cílové oblasti, a umístěte jej do proměnné pomocí [služby Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportujte konfiguraci správce interního nástroje načítání do souboru JSON do adresáře, ve kterém provedete příkaz [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Stažený soubor bude pojmenován po skupině prostředků, ze které byl prostředek exportován.  Vyhledejte soubor, který byl exportován z příkazu s názvem ** \<název skupiny prostředků>.json,** a otevřete jej v editoru podle vašeho výběru:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Chcete-li upravit parametr názvu interního správce zatížení, změňte vlastnost **defaultValue** názvu správce vnitřního zatížení na název cílového interního systému vyrovnávání zatížení, ujistěte se, že je název v uvozovkách:

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
 
6. Chcete-li upravit hodnotu cílové virtuální sítě, která byla přesunuta výše, musíte nejprve získat ID prostředku a potom jej zkopírovat a vložit do souboru ** \<>.json.**  Chcete-li získat ID, použijte [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Zadejte proměnnou a stiskněte klávesu ENTER, chcete-li zobrazit ID zdroje.  Zvýrazněte cestu ID a zkopírujte ji do schránky:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  V souboru ** \<název skupiny prostředků>.json** vložte **ID prostředku** z proměnné namísto **defaultValue** do druhého parametru pro ID cílové virtuální sítě a uzavřete cestu do uvozovek:
   
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

8. Chcete-li upravit cílovou oblast, ve které bude přesunuta interní konfigurace nástroje pro vyrovnávání zatížení, změňte vlastnost **umístění** pod **prostředky** v souboru ** \<>.json>.json:**

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

11. Chcete-li získat kódy umístění oblasti, můžete použít rutinu Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) spuštěním následujícího příkazu:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Můžete také změnit další parametry v šabloně, pokud se rozhodnete, a jsou volitelné v závislosti na vašich požadavcích:
    
    * **Sku** - Můžete změnit sku vnitřní nástroj pro vyrovnávání zatížení v konfiguraci ze standardního nebo základní ho standardní ho diazměnit vlastnost**název** **sku** > v souboru ** \<název skupiny prostředků>.json:**

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
    
13. Uložte soubor ** \<>.json se názvem skupiny prostředků.**
    
10. Vytvořte nebo skupinu prostředků v cílové oblasti pro cílový interní vyrovnávání zatížení, které mají být nasazeny pomocí [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Existující skupinu prostředků shora lze také znovu použít jako součást tohoto procesu:
    
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

Pokud chcete po nasazení začít znovu nebo zahodit virtuální síť a nástroj pro vyrovnávání zatížení v cíli, odstraňte skupinu prostředků, která byla vytvořena v cíli, a přesunutá virtuální síť a nástroj pro vyrovnávání zatížení budou odstraněny.  Chcete-li skupinu prostředků odebrat, použijte [použít skupinu Odebrat azResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun skupiny zabezpečení sítě, odstraňte zdrojový soubor nsg nebo skupinu prostředků, použijte [příkazY Odebrat-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) nebo [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) a [Remove-AzLoadBalancer.](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli azure interní vyrovnávání zatížení z jedné oblasti do druhé a vyčistit zdrojové prostředky.  Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

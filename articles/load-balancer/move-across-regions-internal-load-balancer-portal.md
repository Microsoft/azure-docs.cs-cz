---
title: Přesunutí interního služby Provynaci zatížení Azure do jiné oblasti Azure pomocí portálu Azure
description: Použití šablony Azure Resource Manager u přesunu interního nástroje pro vyrovnávání zatížení Azure z jedné oblasti Azure do jiné pomocí portálu Azure
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638802"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Přesunutí interního vyvažovače zatížení Azure do jiné oblasti pomocí portálu Azure

Existují různé scénáře, ve kterých byste chtěli přesunout stávající interní vyrovnávání zatížení z jedné oblasti do druhé. Můžete například vytvořit interní nástroj pro vyrovnávání zatížení se stejnou konfigurací pro testování. Můžete také přesunout interní vyrovnávání zatížení do jiné oblasti jako součást plánování zotavení po havárii.

Interní vynakladače zatížení Azure nelze přesunout z jedné oblasti do druhé. Šablonu Azure Resource Manager však můžete použít k exportu existující konfigurace a virtuální sítě interního nástroje pro vyrovnávání zatížení.  Potom můžete nastínit prostředek v jiné oblasti exportem nástroje pro vyrovnávání zatížení a virtuální sítě do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a následným nasazením šablon do nové oblasti.  Další informace o Správci prostředků a šablonách najdete v [tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že azure interní vyrovnávání zatížení je v oblasti Azure, ze kterého chcete přesunout.

- Interní vynakladače zatížení Azure nelze přesunout mezi oblastmi.  Budete muset přidružit nový systém vyrovnávání zatížení k prostředkům v cílové oblasti.

- Chcete-li exportovat konfiguraci interního nástroje pro vyrovnávání zatížení a nasadit šablonu k vytvoření interního nástroje pro vyrovnávání zatížení v jiné oblasti, budete potřebovat roli přispěvatele sítě nebo vyšší.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, virtuální počítače a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet interní vyrovnávání zatížení v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání vyrovnávání zatížení pro tento proces.  Viz [Omezení předplatného a služeb Azure, kvóty a omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit interní nástroj pro vyrovnávání zatížení pro přesun pomocí šablony Správce prostředků a přesunout konfiguraci interního nástroje pro vyrovnávání zatížení do cílové oblasti pomocí portálu Azure.  V rámci tohoto procesu musí být zahrnuta konfigurace virtuální sítě interního nástroje pro vyrovnávání zatížení a musí být provedena nejprve před přesunutím interního nástroje pro vyrovnávání zatížení.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Export šablony virtuální sítě a nasazení z webu Azure Portal

1. Přihlaste se ke**skupinám prostředků** [portálu](https://portal.azure.com) > Azure .
2. Vyhledejte skupinu prostředků, která obsahuje zdrojovou virtuální síť, a klikněte na ni.
3. Vyberte > **nastavení** > **exportovat šablonu**.
4. V okně **Exportovat šablonu** zvolte **Nasadit.**
5. Kliknutím na**parametry pro úpravy** **šablony** > otevřete soubor **parameters.json** v online editoru.
6. Chcete-li upravit parametr názvu virtuální sítě, změňte **vlastnost hodnoty** pod **parametry**:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Změňte hodnotu názvu zdrojové virtuální sítě v editoru na název, který si vyberete pro cílovou virtuální síť. Ujistěte se, že jste název uzavřete do uvozovek.

8. V editoru klikněte na **Uložit.**

9. Kliknutím na **Šablona** > **Upravit šablonu** otevřete soubor **template.json** v online editoru.

10. Chcete-li upravit cílovou oblast, do které bude virtuální síť přesunuta, změňte vlastnost **umístění** v části prostředky:

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

11. Kódy umístění oblasti najdete v [tématu Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód pro oblast je název oblasti bez mezer, **centrální US** = **centralus**.

12. Pokud se rozhodnete, můžete také změnit další parametry v souboru **template.json** a jsou volitelné v závislosti na vašich požadavcích:

    * **Adresní prostor** – Adresní prostor virtuální sítě lze před uložením změnit úpravou oddílu**adresní prostor** **prostředků a** > změnou vlastnosti **addressPrefixes** v souboru **template.json:**

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

    * **Podsíť** - Název podsítě a adresní prostor podsítě lze změnit nebo přidat úpravou oddílu **podsítí** souboru **template.json.** Název podsítě lze změnit změnou vlastnosti **názvu.** Adresní prostor podsítě lze změnit změnou vlastnosti **addressPrefix** v souboru **template.json:**

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

         Chcete-li změnit předponu adresy v souboru **template.json,** je nutné ji upravit na dvou místech, výše uvedenou v části a v níže uvedeném **oddílu s typem.**  Změňte vlastnost **addressPrefix** tak, aby odpovídala výše uvedené vlastnosti:

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

13. V online editoru klikněte na **Uložit.**

14. Kliknutím na**předplatné** **BASICS** > vyberte předplatné, ve kterém bude nasazena cílová virtuální síť.

15. Kliknutím na**skupinu prostředků** **BASICS** > zvolte skupinu prostředků, do které bude nasazena cílová virtuální síť.  Kliknutím na **Vytvořit nový** můžete vytvořit novou skupinu prostředků pro cílovou virtuální síť.  Ujistěte se, že název není stejný jako zdrojová skupina prostředků existující virtuální sítě.

16. Ověřte, že**umístění** **BASICS** > je nastaveno na cílové umístění, kde chcete virtuální síť nasadit.

17. V části **NASTAVENÍ** ověřte, zda název odpovídá názvu, který jste zadali do výše uvedeného editoru parametrů.

18. Zaškrtněte políčko **v**podmínkách .

19. Kliknutím na tlačítko **Koupit** nasadíte cílovou virtuální síť.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportovat interní šablonu pro vyrovnávání zatížení a nasadit z Azure PowerShellu

1. Přihlaste se ke**skupinám prostředků** [portálu](https://portal.azure.com) > Azure .
2. Vyhledejte skupinu prostředků, která obsahuje zdrojový interní systém vyrovnávání zatížení, a klikněte na něj.
3. Vyberte > **nastavení** > **exportovat šablonu**.
4. V okně **Exportovat šablonu** zvolte **Nasadit.**
5. Kliknutím na**parametry pro úpravy** **šablony** > otevřete soubor **parameters.json** v online editoru.

6. Chcete-li upravit parametr názvu interního správce zatížení, změňte vlastnost **defaultValue** názvu správce vnitřního zatížení na název cílového interního systému vyrovnávání zatížení, ujistěte se, že je název v uvozovkách:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Chcete-li upravit hodnotu cílové virtuální sítě, která byla přesunuta výše, musíte nejprve získat ID prostředku a potom jej zkopírovat a vložit do souboru **parameters.json.** Chcete-li získat ID:

    1. Přihlaste se ke**skupinám prostředků** [portálu](https://portal.azure.com) > Azure v jiné záložce nebo okně prohlížeče.
    2. Vyhledejte cílovou skupinu prostředků, která obsahuje přesunutou virtuální síť, z výše uvedených kroků a klikněte na ni.
    3. Vyberte**možnost vlastnosti** **nastavení** > > .
    4. V noži vpravo **zvýrazněte ID prostředku** a zkopírujte ho do schránky.  Případně můžete kliknout na tlačítko **kopírovat do schránky** napravo od cesty **ID prostředku.**
    5. Vložte ID prostředku do vlastnosti **defaultValue** do editoru **Upravit parametry,** který se otevře v druhém okně prohlížeče nebo na kartě:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. V online editoru klikněte na **Uložit.**

7. Kliknutím na **Šablona** > **Upravit šablonu** otevřete soubor **template.json** v online editoru.
8. Chcete-li upravit cílovou oblast, ve které bude přesunuta interní konfigurace nástroje pro vyrovnávání zatížení, změňte vlastnost **umístění** pod **prostředky** v souboru **template.json:**

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

9.  Kódy umístění oblasti najdete v [tématu Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód pro oblast je název oblasti bez mezer, **centrální US** = **centralus**.

10. Můžete také změnit další parametry v šabloně, pokud se rozhodnete, a jsou volitelné v závislosti na vašich požadavcích:

    * **Sku** - Můžete změnit sku vnitřní nástroj pro vyrovnávání zatížení v konfiguraci ze standardního nebo základní ho standardní ho diazměnit vlastnost **sku** > **název** v souboru **template.json:**

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

    * **Pravidla vyrovnávání zatížení** – Můžete přidat nebo odebrat pravidla vyrovnávání zatížení v konfiguraci přidáním nebo odebráním položek do oddílu **loadBalancingRules** souboru **template.json:**

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

    * **Sondy** – sondu pro nástroj pro vyrovnávání zatížení v konfiguraci můžete přidat nebo odebrat přidáním nebo odebráním položek do části **sond** souboru **template.json:**

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

    * **Příchozí pravidla NAT** – Můžete přidat nebo odebrat příchozí pravidla NAT pro vyrovnávání zatížení přidáním nebo odebráním položek do oddílu **inboundNatRules** souboru **template.json:**

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
        Chcete-li dokončit přidání nebo odebrání pravidla příchozího nat, musí být pravidlo přítomno nebo odebráno jako vlastnost **typu** na konci souboru **template.json:**

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

12. V online editoru klikněte na **Uložit.**

13. Kliknutím na**předplatné** **BASICS** > zvolte předplatné, ve kterém bude nasazen cílový interní vyvažovač zatížení.

15. Kliknutím na**skupinu prostředků** **BASICS** > zvolte skupinu prostředků, do které bude nasazen cílový systém vyrovnávání zatížení.  Kliknutím na **Vytvořit nový** můžete vytvořit novou skupinu prostředků pro cílový interní nástroj pro vyrovnávání zatížení nebo zvolit existující skupinu prostředků, která byla vytvořena výše pro virtuální síť.  Ujistěte se, že název není stejný jako zdrojová skupina prostředků existujícího zdrojového interního systému vyrovnávání zatížení.

16. Ověřte, zda je**umístění** **BASICS** > nastaveno na cílové umístění, kde chcete nasadit interní systém vyrovnávání zatížení.

17. V části **NASTAVENÍ** ověřte, zda název odpovídá názvu, který jste zadali do výše uvedeného editoru parametrů.  Ověřte, zda jsou ID prostředků naplněna pro všechny virtuální sítě v konfiguraci.

18. Zaškrtněte políčko **v**podmínkách .

19. Kliknutím na tlačítko **Koupit** nasadíte cílovou virtuální síť.

## <a name="discard"></a>Zahodit

Pokud chcete zahodit cílovou virtuální síť a interní nástroj pro vyrovnávání zatížení, odstraňte skupinu prostředků, která obsahuje cílovou virtuální síť a interní nástroj pro vyrovnávání zatížení.  Chcete-li tak učinit, vyberte skupinu prostředků z řídicího panelu na portálu a v horní části stránky přehledu vyberte **Odstranit.**

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun virtuální sítě a interního nástroje pro vyrovnávání zatížení, odstraňte zdrojovou virtuální síť a interní nástroj pro vyrovnávání zatížení nebo skupinu prostředků. Chcete-li tak učinit, vyberte virtuální síť a interní nástroj pro vyrovnávání zatížení nebo skupinu prostředků z řídicího panelu na portálu a v horní části každé stránky vyberte **Odstranit.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli azure interní vyrovnávání zatížení z jedné oblasti do druhé a vyčistit zdrojové prostředky.  Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

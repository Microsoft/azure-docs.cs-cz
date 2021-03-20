---
title: Přesunout interní Load Balancer Azure do jiné oblasti Azure pomocí Azure Portal
description: Pomocí šablony Azure Resource Manager můžete přesunout interní Load Balancer Azure z jedné oblasti Azure do jiné pomocí Azure Portal
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 68a2cb6926cb41956711a9e3c15d21c250d27f0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94698466"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Přesunout interní Load Balancer Azure do jiné oblasti pomocí Azure Portal

Existují různé scénáře, ve kterých byste chtěli přesunout stávající interní nástroj pro vyrovnávání zatížení z jedné oblasti do druhé. Například může být vhodné vytvořit interní nástroj pro vyrovnávání zatížení se stejnou konfigurací pro testování. Interní nástroj pro vyrovnávání zatížení můžete také přesunout do jiné oblasti v rámci plánování zotavení po havárii.

Interní nástroje pro vyrovnávání zatížení Azure nejde přesunout z jedné oblasti do druhé. Můžete ale použít šablonu Azure Resource Manager k exportu existující konfigurace a virtuální sítě interního nástroje pro vyrovnávání zatížení.  Pak můžete tento prostředek připravit v jiné oblasti tak, že vyexportujete Nástroj pro vyrovnávání zatížení a virtuální síť do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablony nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [rychlý Start: vytvoření a nasazení Azure Resource Manager šablon pomocí Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Předpoklady

- Ujistěte se, že interní nástroj pro vyrovnávání zatížení Azure je v oblasti Azure, ze které chcete přejít.

- Interní nástroje pro vyrovnávání zatížení Azure se nedají přesouvat mezi oblastmi.  Nový nástroj pro vyrovnávání zatížení bude nutné přidružit k prostředkům v cílové oblasti.

- Pokud chcete exportovat konfiguraci interního nástroje pro vyrovnávání zatížení a nasadit šablonu pro vytvoření interního nástroje pro vyrovnávání zatížení v jiné oblasti, budete potřebovat roli Přispěvatel sítě nebo vyšší.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, virtuální počítače a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet interní nástroje pro vyrovnávání zatížení v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání nástrojů pro vyrovnávání zatížení pro tento proces.  Viz [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit interní nástroj pro vyrovnávání zatížení pro přesun pomocí šablony Správce prostředků a přesunout konfiguraci interního nástroje pro vyrovnávání zatížení do cílové oblasti pomocí Azure Portal.  V rámci tohoto procesu musí být před přesunutím interního nástroje pro vyrovnávání zatížení součástí konfigurace virtuální sítě interního nástroje pro vyrovnávání zatížení a musí se nejdřív provést.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Exportujte šablonu virtuální sítě a nasaďte ji z Azure Portal

1. Přihlaste [](https://portal.azure.com)se ke  >  **skupinám prostředků** Azure Portal.
2. Vyhledejte skupinu prostředků, která obsahuje zdrojovou virtuální síť, a klikněte na ni.
3. Vyberte > **Nastavení**  >  **Exportovat šablonu**.
4. V okně **Exportovat šablonu** vyberte **nasadit** .
5. Kliknutím na **šablonu**  >  **Upravit parametry** otevřete **parameters.js** v souboru v online editoru.
6. Chcete-li upravit parametr názvu virtuální sítě, změňte vlastnost **Value** v části **parametry**:

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
7. V editoru změňte hodnotu název zdrojové virtuální sítě na název, který si zvolíte pro cílovou virtuální síť. Zajistěte, aby byl název uzavřen v uvozovkách.

8. V Editoru klikněte na **Uložit** .

9. Kliknutím na **šablonu**  >  **Upravit šablonu** otevřete **template.js** v souboru v online editoru.

10. Pokud chcete upravit cílovou oblast, kam se virtuální síť přesune, změňte vlastnost **umístění** v části prostředky:

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

11. Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer, **střed USA**  =  **centralus**.

12. V případě, že zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích, můžete také změnit jiné parametry v **template.jsv** souboru.

    * **Adresní prostor** – adresní prostor virtuální sítě je možné změnit před uložením úpravou části addressSpace **prostředků**  >   a změnou vlastnosti **addressPrefixes** v **template.js** souboru:

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

    * **Podsíť** – název podsítě a adresní prostor podsítě lze změnit nebo přidat k úpravou části **podsítě** v **template.jsv** souboru. Název podsítě lze změnit změnou vlastnosti **název** . Adresní prostor podsítě lze změnit změnou vlastnosti **addressPrefix** v **template.js** souboru:

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

         Pokud chcete změnit předponu adresy, musí být v **template.js** v souboru upravena na dvou místech, výše uvedenou část a níže uvedený oddíl **typ** .  Změňte vlastnost **addressPrefix** tak, aby odpovídala hodnotě výše:

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

13. V online Editoru klikněte na **Uložit** .

14. Klikněte na **základy**  >  **předplatné** a vyberte předplatné, ve kterém bude cílová síť VNet nasazená.

15. Klikněte na **základy**  >  **Skupina prostředků** a vyberte skupinu prostředků, do které se nasadí Cílová síť VNET.  Kliknutím na **vytvořit novou** můžete vytvořit novou skupinu prostředků pro cílovou síť VNET.  Ujistěte se, že název není stejný jako zdrojová skupina prostředků stávající virtuální sítě.

16. Ověření **základních**  >  **umístění** je nastaveno na cílové umístění, ve kterém chcete virtuální síť nasadit.

17. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali v editoru parametrů výše.

18. Zaškrtněte políčko v části **podmínky a ujednání**.

19. Kliknutím na tlačítko **koupit** nasadíte cílovou virtuální síť.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportujte šablonu interního nástroje pro vyrovnávání zatížení a nasaďte ji z Azure PowerShell

1. Přihlaste [](https://portal.azure.com)se ke  >  **skupinám prostředků** Azure Portal.
2. Vyhledejte skupinu prostředků, která obsahuje zdrojový interní nástroj pro vyrovnávání zatížení, a klikněte na ni.
3. Vyberte > **Nastavení**  >  **Exportovat šablonu**.
4. V okně **Exportovat šablonu** vyberte **nasadit** .
5. Kliknutím na **šablonu**  >  **Upravit parametry** otevřete **parameters.js** v souboru v online editoru.

6. Chcete-li upravit parametr názvu interního nástroje pro vyrovnávání zatížení, změňte vlastnost **DefaultValue** zdrojového interního nástroje pro vyrovnávání zatížení na název cílového interního nástroje pro vyrovnávání zatížení, ujistěte se, že je název v uvozovkách:

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

6. Pokud chcete upravit hodnotu cílové virtuální sítě, která se přesunula výše, musíte nejdřív získat ID prostředku a pak ho zkopírovat a vložit do **parameters.js** do souboru. Získání ID:

    1. Přihlaste se k [Azure Portal](https://portal.azure.com)  >  **skupinám prostředků** na jiné kartě nebo okně prohlížeče.
    2. Vyhledejte cílovou skupinu prostředků, která obsahuje přesunutou virtuální síť z výše uvedených kroků, a klikněte na ni.
    3. Vyberte >   >  **vlastnosti** nastavení.
    4. V okně vpravo zvýrazněte **ID prostředku** a zkopírujte ho do schránky.  Případně můžete kliknout na tlačítko **Kopírovat do schránky** napravo od cesty k **ID prostředku** .
    5. Vložte ID prostředku do vlastnosti **DefaultValue** v editoru **úprav parametrů** otevřeném v jiném okně nebo na kartě prohlížeče:

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
    6. V online Editoru klikněte na **Uložit** .

7. Kliknutím na **šablonu**  >  **Upravit šablonu** otevřete **template.js** v souboru v online editoru.
8. Chcete-li upravit cílovou oblast, kde bude přesunuta konfigurace interního nástroje pro vyrovnávání zatížení, změňte vlastnost **Location** v části **prostředky** v **template.js** souboru:

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

9.  Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer, **střed USA**  =  **centralus**.

10. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:

    * **SKU** -SKU interního nástroje pro vyrovnávání zatížení můžete změnit v konfiguraci z úrovně Standard na Basic nebo Basic na standard změnou   >  vlastnosti **název** SKU v **template.jsv** souboru:

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

    * **Pravidla vyrovnávání zatížení** – můžete přidat nebo odebrat pravidla vyrovnávání zatížení v konfiguraci přidáním nebo odebráním položek do části **loadBalancingRules** **template.js** souboru:

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

    * **Sondy** – pro nástroj pro vyrovnávání zatížení můžete v konfiguraci přidat nebo odebrat test, a to přidáním nebo odebráním položek do části **sondy** **template.jsv** souboru:

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

    * **Příchozí pravidla NAT** – pravidla příchozího překladu adres (NAT) pro nástroj pro vyrovnávání zatížení můžete přidat nebo odebrat zadáním nebo odebráním položek do části **inboundNatRules** **template.jsv** souboru:

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
        Aby bylo možné doplnit nebo odebrat pravidlo příchozího překladu adres (NAT), musí být pravidlo přítomno nebo odebráno jako vlastnost **typu** na konci **template.js** souboru:

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

12. V online Editoru klikněte na **Uložit** .

13. Kliknutím na položku **základní**  >  **předplatné** zvolíte předplatné, ve kterém bude nasazený cílový interní nástroj pro vyrovnávání zatížení.

15. Klikněte na **základy**  >  **Skupina prostředků** a vyberte skupinu prostředků, ve které bude nasazený cílový Nástroj pro vyrovnávání zatížení.  Kliknutím na **vytvořit novou** můžete vytvořit novou skupinu prostředků pro cílový interní nástroj pro vyrovnávání zatížení nebo vybrat existující skupinu prostředků vytvořenou výše pro virtuální síť.  Ujistěte se, že název není stejný jako zdrojová skupina prostředků stávajícího zdrojového interního nástroje pro vyrovnávání zatížení.

16. Ověření **základních**  >  **umístění** je nastaveno na cílové umístění, kam chcete nasadit nástroj pro vyrovnávání zatížení interního nástroje pro vyrovnávání zatížení.

17. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali v editoru parametrů výše.  Ověřte, že ID prostředků se naplní pro všechny virtuální sítě v konfiguraci.

18. Zaškrtněte políčko v části **podmínky a ujednání**.

19. Kliknutím na tlačítko **koupit** nasadíte cílovou virtuální síť.

## <a name="discard"></a>Zahodit

Pokud chcete zrušit cílovou virtuální síť a interní nástroj pro vyrovnávání zatížení, odstraňte skupinu prostředků, která obsahuje cílovou virtuální síť a interní nástroj pro vyrovnávání zatížení.  Provedete to tak, že vyberete skupinu prostředků z řídicího panelu na portálu a v horní části stránky přehled vyberete **Odstranit** .

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun virtuální sítě a interního nástroje pro vyrovnávání zatížení, odstraňte zdrojovou virtuální síť a interní nástroj pro vyrovnávání zatížení nebo skupinu prostředků. Provedete to tak, že vyberete virtuální síť a interní nástroj pro vyrovnávání zatížení nebo skupinu prostředků z řídicího panelu na portálu a v horní části každé stránky vyberete **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli interní nástroj pro vyrovnávání zatížení Azure z jedné oblasti na jiný a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesun virtuálních počítačů Azure do jiné oblasti](../site-recovery/azure-to-azure-tutorial-migrate.md)
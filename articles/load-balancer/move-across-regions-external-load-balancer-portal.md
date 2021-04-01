---
title: Přesuňte externí nástroj pro vyrovnávání zatížení Azure do jiné oblasti Azure pomocí Azure Portal
description: Pomocí šablony Azure Resource Manager můžete přesunout externí nástroj pro vyrovnávání zatížení z jedné oblasti Azure do jiné pomocí Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f83ff3d1d03354daef3466c1f48eaa505e378634
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94693745"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Přesunout externí nástroj pro vyrovnávání zatížení do jiné oblasti pomocí Azure Portal

Existují různé scénáře, ve kterých byste chtěli přesunout externí nástroj pro vyrovnávání zatížení z jedné oblasti do druhé. Můžete například chtít vytvořit jiný externí nástroj pro vyrovnávání zatížení se stejnou konfigurací pro testování. V rámci plánování zotavení po havárii možná budete chtít přesunout externí nástroj pro vyrovnávání zatížení do jiné oblasti.

Ve smyslu literálu nemůžete externí nástroj pro vyrovnávání zatížení Azure přesunout z jedné oblasti do jiné. K exportu existující konfigurace a veřejné IP adresy externího nástroje pro vyrovnávání zatížení ale můžete použít šablonu Azure Resource Manager. Potom můžete prostředek připravit v jiné oblasti tak, že vyexportujete Nástroj pro vyrovnávání zatížení a veřejnou IP adresu do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti. Další informace o Správce prostředků a šablonách najdete v tématu [Export skupin prostředků do šablon](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je externí nástroj pro vyrovnávání zatížení Azure v oblasti Azure, ze které chcete přejít.

- Externí nástroje pro vyrovnávání zatížení Azure se nedají přesouvat mezi oblastmi. Nový nástroj pro vyrovnávání zatížení bude nutné přidružit k prostředkům v cílové oblasti.

- Pokud chcete exportovat konfiguraci externího nástroje pro vyrovnávání zatížení a nasadit šablonu pro vytvoření externího nástroje pro vyrovnávání zatížení v jiné oblasti, bude nutné přiřadit roli Přispěvatel sítě nebo vyšší.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, veřejné IP adresy a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet externí služby Vyrovnávání zatížení v cílové oblasti. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání nástrojů pro vyrovnávání zatížení. Viz [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

## <a name="prepare-and-move"></a>Příprava a přesun
Následující postupy ukazují, jak připravit externí nástroj pro vyrovnávání zatížení pro přesun pomocí šablony Správce prostředků a přesunout konfiguraci externího nástroje pro vyrovnávání zatížení do cílové oblasti pomocí Azure Portal. Nejdřív musíte Exportovat konfiguraci veřejné IP adresy externího nástroje pro vyrovnávání zatížení.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Exportujte šablonu veřejné IP adresy a nasaďte veřejnou IP adresu z portálu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.
2. Vyhledejte skupinu prostředků, která obsahuje zdrojovou veřejnou IP adresu, a vyberte ji.
3. Vyberte **Nastavení**  >  **Exportovat šablonu**.
4. V části **Exportovat šablonu** vyberte **nasadit** .
5. Vyberte **šablonu**  >  **Upravit parametry** a otevřete parameters.jsv souboru v online editoru.
8. Chcete-li upravit parametr názvu veřejné IP adresy, změňte **hodnotu vlastnosti Value** z **názvu** veřejné IP adresy zdroje na název vaší cílové veřejné IP adresy. Název uzavřete do uvozovek.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    V editoru vyberte **Save (Uložit** ).

9.  Vyberte **šablonu**  >  **Upravit šablonu** a otevřete template.jsv souboru v online editoru.

10. Chcete-li upravit cílovou oblast, do které bude přesunuta veřejná IP adresa, změňte vlastnost **umístění** v části **prostředky**:

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
  
    Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti bez mezer. Například kód pro Střed USA je **centralus**.
    
12. V závislosti na vašich požadavcích můžete také změnit další parametry v šabloně, pokud chcete nebo potřebujete:

    * **SKU**. SKU veřejné IP adresy můžete změnit v konfiguraci z úrovně Standard na Basic nebo z úrovně Basic na standard, a to změnou vlastnosti **Name** v poli **SKU** v template.jssouboru:

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

        Informace o rozdílech mezi veřejnými IP adresami Basic a Standard SKU najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md).

    * **Metoda přidělení veřejné IP adresy** a **časový limit nečinnosti**. Metodu přidělení veřejné IP adresy můžete změnit tak, že změníte vlastnost **publicIPAllocationMethod** z **dynamické** na **statickou** nebo ze **statické** na **dynamickou**. Časový limit nečinnosti můžete změnit tak, že změníte vlastnost **idleTimeoutInMinutes** na požadovanou hodnotu. Výchozí hodnota je **4**.

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

        ```

        Informace o metodách přidělování a hodnotách časového limitu nečinnosti najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md).

 
13. V online editoru vyberte **Uložit** .

14. Vyberte předplatná **základní**  >   a vyberte předplatné, ve kterém se bude nasazovat cílová veřejná IP adresa.

15. Vyberte možnost  >  **Skupina prostředků** základy a zvolte skupinu prostředků, do které bude nasazena cílová veřejná IP adresa. Můžete vybrat **vytvořit novou** a vytvořit novou skupinu prostředků pro cílovou veřejnou IP adresu. Ujistěte se, že název není stejný jako zdrojová skupina prostředků stávající zdrojové veřejné IP adresy.

16. Ověřte, zda je umístění **základních**  >  **umístění** nastaveno na cílové umístění, kam chcete nasadit veřejnou IP adresu.

17. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali dříve v editoru parametrů.

18. Zaškrtněte políčko **podmínky a ujednání** .

19. Vyberte **koupit** pro nasazení cílové veřejné IP adresy.

20. Pokud máte jinou veřejnou IP adresu, která se používá pro odchozí překlad adres (NAT) pro přemístění nástroje pro vyrovnávání zatížení, opakujte předchozí kroky pro export a nasazení druhé odchozí veřejné IP adresy do cílové oblasti.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Exportujte šablonu externího nástroje pro vyrovnávání zatížení a nasaďte Nástroj pro vyrovnávání zatížení z Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.
2. Vyhledejte skupinu prostředků, která obsahuje zdrojový externí nástroj pro vyrovnávání zatížení, a vyberte ji.
3. Vyberte **Nastavení**  >  **Exportovat šablonu**.
4. V části **Exportovat šablonu** vyberte **nasadit** .
5. Vyberte **šablonu**  >  **Upravit parametry** a otevřete parameters.jsv souboru v online editoru.

5. Chcete-li upravit parametr názvu externí služby Vyrovnávání zatížení, změňte vlastnost **Value** zdrojového externího nástroje pro vyrovnávání zatížení na název vašeho cílového externího nástroje pro vyrovnávání zatížení. Název uzavřete do uvozovek.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Pokud chcete upravit hodnotu cílové veřejné IP adresy, kterou jste přesunuli v předchozích krocích, musíte nejdřív získat ID prostředku a pak ho vložit do parameters.jsdo souboru. Získání ID:

    1. Na jiné kartě nebo okně prohlížeče se přihlaste k [Azure Portal](https://portal.azure.com) a vyberte **skupiny prostředků**.
    2. Vyhledejte cílovou skupinu prostředků, která obsahuje veřejnou IP adresu, kterou jste přesunuli v předchozích krocích. Vyberte ji.
    3. Vyberte **Nastavení** > **Vlastnosti**.
    4. V okně vpravo zvýrazněte **ID prostředku** a zkopírujte ho do schránky. Alternativně můžete vybrat možnost **Kopírovat do schránky** napravo od cesty k **ID prostředku** .
    5. Vložte ID prostředku do vlastnosti **hodnota** v editoru **úprav parametrů** , který je otevřený v jiném okně prohlížeče nebo na kartě:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. V online editoru vyberte **Uložit** .


7.  Pokud jste nakonfigurovali odchozí NAT a odchozí pravidla pro nástroj pro vyrovnávání zatížení, zobrazí se v tomto souboru třetí položka pro externí ID odchozí veřejné IP adresy. Opakujte předchozí kroky v **cílové oblasti** , abyste získali ID odchozí veřejné IP adresy. Vložit toto ID do parameters.jsdo souboru:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Vyberte **šablonu**  >  **Upravit šablonu** a otevřete template.jsv souboru v online editoru.
9.  Chcete-li upravit cílovou oblast, do které bude přesunuta konfigurace externího nástroje pro vyrovnávání zatížení, změňte vlastnost **umístění** v části **prostředky** v template.jssouboru:

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

10. Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti bez mezer. Například kód pro Střed USA je **centralus**.

11. V závislosti na vašich požadavcích můžete také změnit další parametry v šabloně, pokud chcete nebo potřebujete:

    * **SKU**. SKLADOVOU položku externího nástroje pro vyrovnávání zatížení můžete změnit v konfiguraci z úrovně Standard na Basic nebo z úrovně Basic na standard, a to změnou vlastnosti **Name** v poli **SKU** v template.jssouboru:

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
      Informace o rozdílech mezi nástroji pro vyrovnávání zatížení Basic a Standard SKU najdete v tématu [Přehled služby Azure Standard Load Balancer](./load-balancer-overview.md).

    * **Pravidla vyrovnávání zatížení**. Můžete přidat nebo odebrat pravidla vyrovnávání zatížení v konfiguraci přidáním nebo odebráním položek v části **loadBalancingRules** template.jssouboru:

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
       Informace o pravidlech vyrovnávání zatížení najdete v tématu [co je Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Sondy**. Můžete přidat nebo odebrat sondu pro nástroj pro vyrovnávání zatížení v konfiguraci přidáním nebo odebráním položek v části **sondy** template.jsv souboru:

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
       Další informace najdete v tématu [Load Balancer sondy stavu](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Příchozí pravidla NAT**. Můžete přidat nebo odebrat příchozí pravidla překladu adres (NAT) pro nástroj pro vyrovnávání zatížení přidáním nebo odebráním položek v části **inboundNatRules** souboru template.js:

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
        Aby bylo možné doplnit nebo odebrat pravidlo příchozího překladu adres (NAT), musí být pravidlo přítomno nebo odebráno jako vlastnost **typu** na konci template.jssouboru:

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
        Informace o příchozích pravidlech NAT najdete v tématu [co je Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Odchozí pravidla**. Můžete přidat nebo odebrat odchozí pravidla v konfiguraci úpravou vlastnosti **outboundRules** v souboru template.js:

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

         Další informace najdete v tématu [Load Balancer odchozích pravidel](./load-balancer-outbound-connections.md#outboundrules).

12. V online editoru vyberte **Uložit** .

13. Vyberte předplatná **základní**  >   a zvolte předplatné, ve kterém bude nasazený cílový externí nástroj pro vyrovnávání zatížení.

15. Vyberte možnost  >  **Skupina prostředků** základy a zvolte skupinu prostředků, do které bude nasazený cílový Nástroj pro vyrovnávání zatížení. Můžete vybrat **vytvořit novou** a vytvořit novou skupinu prostředků pro cílový externí nástroj pro vyrovnávání zatížení. Nebo můžete vybrat existující skupinu prostředků, kterou jste vytvořili dříve pro veřejnou IP adresu. Ujistěte se, že název není stejný jako zdrojová skupina prostředků existujícího zdrojového externího nástroje pro vyrovnávání zatížení.

16. Ověřte, zda  >  je umístění základních **umístění** nastaveno na cílové umístění, do kterého chcete nasadit externí nástroj pro vyrovnávání zatížení.

17. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali dříve v editoru parametrů. Ověřte, jestli se v konfiguraci naplní ID prostředků pro všechny veřejné IP adresy.

18. Zaškrtněte políčko **podmínky a ujednání** .

19. Vyberte **koupit** pro nasazení cílové veřejné IP adresy.

## <a name="discard"></a>Zahodit

Pokud chcete zrušit cílovou veřejnou IP adresu a externí nástroj pro vyrovnávání zatížení, odstraňte skupinu prostředků, která je obsahuje. Provedete to tak, že na portálu vyberete skupinu prostředků z řídicího panelu a v horní části stránky přehled vyberete **Odstranit** .

## <a name="clean-up"></a>Vyčištění

Pokud chcete potvrdit změny a dokončit přesun veřejné IP adresy a externího nástroje pro vyrovnávání zatížení, odstraňte zdrojovou veřejnou IP adresu a externí nástroj pro vyrovnávání zatížení nebo skupinu prostředků. Provedete to tak, že vyberete tuto skupinu prostředků z řídicího panelu na portálu a pak v horní části každé stránky vyberete **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli externí nástroj pro vyrovnávání zatížení Azure z jedné oblasti na jiný a vyčistili zdrojové prostředky. Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesun virtuálních počítačů Azure do jiné oblasti](../site-recovery/azure-to-azure-tutorial-migrate.md)
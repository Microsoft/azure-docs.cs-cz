---
title: Přesunutí externího správce zatížení Azure do jiné oblasti Azure pomocí portálu Azure
description: Pomocí šablony Azure Resource Manager přesunout externí nástroje pro vyrovnávání zatížení z jedné oblasti Azure do jiné pomocí portálu Azure.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638489"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Přesunutí externího správce zatížení do jiné oblasti pomocí portálu Azure

Existují různé scénáře, ve kterých byste chtěli přesunout externí vyrovnávání zatížení z jedné oblasti do druhé. Můžete například vytvořit jiný externí nástroj pro vyrovnávání zatížení se stejnou konfigurací pro testování. Můžete také chtít přesunout externí vyrovnávání zatížení do jiné oblasti jako součást plánování zotavení po havárii.

V doslovném smyslu nelze přesunout externí vyrovnávání zatížení Azure z jedné oblasti do druhé. Ale můžete použít šablonu Azure Resource Manager k exportu existující konfigurace a veřejné IP adresy externího nástroje pro vyrovnávání zatížení. Potom můžete zinscenovat prostředek v jiné oblasti exportem vyrovnávání zatížení a veřejné IP adresy do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a následným nasazením šablony do nové oblasti. Další informace o Správci prostředků a šablonách naleznete v [tématu Export skupin prostředků do šablon](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že externí vyrovnávání zatížení Azure je v oblasti Azure, ze které chcete přesunout.

- Externí vyvyčažatelé zatížení Azure nelze přesunout mezi oblastmi. Budete muset přidružit nový systém vyrovnávání zatížení k prostředkům v cílové oblasti.

- Chcete-li exportovat externí konfiguraci nástroje pro vyrovnávání zatížení a nasadit šablonu pro vytvoření externího nástroje pro vyrovnávání zatížení v jiné oblasti, musíte vám přiřadit roli přispěvatele sítě nebo vyšší.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, veřejné IP adresy a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet externí vyrovnávání zatížení v cílové oblasti. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání vyrovnávání zatížení. Viz [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Příprava a přesun
Následující postupy ukazují, jak připravit externí nástroj pro vyrovnávání zatížení pro přesun pomocí šablony Správce prostředků a přesunout konfiguraci externího nástroje pro vyrovnávání zatížení do cílové oblasti pomocí portálu Azure. Nejprve je nutné exportovat veřejnou konfiguraci IP externího nástroje pro vyrovnávání zatížení.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Export veřejné šablony IP adres a nasazení veřejné IP adresy z portálu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.
2. Vyhledejte skupinu prostředků, která obsahuje zdrojovou veřejnou IP adresu, a vyberte ji.
3. Vyberte **Nastavení** > **exportu šablony**.
4. V části **Exportovat šablonu**vyberte **Nasadit** .
5. Výběrem**možnosti Parametry pro úpravu** **šablony** > otevřete soubor parameters.json v online editoru.
8. Chcete-li upravit parametr veřejného názvu IP, změňte **vlastnost hodnoty** pod **parametry** ze zdrojového veřejného názvu IP na název cílové veřejné IP adresy. Název uzavřete do uvozovek.

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

    V editoru vyberte **Uložit.**

9.  Vyberte **Šablona** > **Upravit šablonu,** chcete-li otevřít soubor template.json v online editoru.

10. Chcete-li upravit cílovou oblast, do které bude veřejná ip adresa přesunuta, změňte vlastnost **umístění** v části **zdroje**:

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
  
    Kódy umístění oblasti najdete v [tématu Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód pro oblast je název oblasti bez mezer. Například kód pro střední USA je **centralus**.
    
12. Můžete také změnit další parametry v šabloně, pokud chcete nebo potřebujete, v závislosti na vašich požadavcích:

    * **Skladová položka**. Skladovou položku veřejné IP adresy v konfiguraci můžete změnit ze standardní na základní nebo ze základní na standardní změnou vlastnosti **name** pod **sku** v souboru template.json:

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

        Informace o rozdílech mezi základními a standardními veřejnými IP adresami skladových řad v uvedených tématech naleznete v [tématu Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Metoda přidělení veřejné IP adresy** a časový limit **nečinnosti**. Metodu přidělení veřejné IP adresy můžete změnit změnou vlastnosti **publicIPAllocationMethod** z **dynamické** na **statickou** nebo ze **statické** na **dynamickou**. Časový limit nečinnosti můžete změnit změnou vlastnosti **idleTimeoutInMinutes** na požadovanou hodnotu. Výchozí hodnota je **4**.

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

        Informace o metodách přidělení a hodnotách časového limitu nečinnosti naleznete v [tématu Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. V online editoru vyberte **Uložit.**

14. Vyberte**předplatné** **BASICS** > a zvolte předplatné, ve kterém bude nasazena cílová veřejná IP adresa.

15. Vyberte**skupinu prostředků** **BASICS** > a zvolte skupinu prostředků, do které bude nasazena cílová veřejná IP adresa. Můžete vybrat **Vytvořit nový** a vytvořit novou skupinu prostředků pro cílovou veřejnou IP adresu. Ujistěte se, že název není stejný jako zdrojová skupina prostředků existující zdrojové veřejné IP adresy.

16. Ověřte, zda je**umístění** **BASICS** > nastaveno na cílové umístění, kam chcete nasadit veřejnou IP adresu.

17. V části **NASTAVENÍ**ověřte, zda název odpovídá názvu, který jste zadali dříve v editoru parametrů.

18. Zaškrtněte políčko **PODMÍNKY.**

19. Vyberte **Nákup,** chcete-li nasadit cílovou veřejnou IP adresu.

20. Pokud máte jinou veřejnou IP adresu, která se používá pro odchozí NAT pro přesouvání vyrovnávání zatížení, opakujte předchozí kroky k exportu a nasazení druhé odchozí veřejné IP adresy do cílové oblasti.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Export externí šablony pro vyrovnávání zatížení a nasazení správce zatížení z webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.
2. Vyhledejte skupinu prostředků, která obsahuje zdrojový externí ekvizér zatížení, a vyberte ji.
3. Vyberte **Nastavení** > **exportu šablony**.
4. V části **Exportovat šablonu**vyberte **Nasadit** .
5. Výběrem**možnosti Parametry pro úpravu** **šablony** > otevřete soubor parameters.json v online editoru.

5. Chcete-li upravit parametr názvu externího správce zatížení, změňte **vlastnost hodnoty** názvu zdrojového externího správce zatížení na název cílového externího systému vyrovnávání zatížení. Název uzavřete do uvozovek.

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

6.  Chcete-li upravit hodnotu cílové veřejné IP adresy, kterou jste přesunuli v předchozích krocích, musíte nejprve získat ID prostředku a potom jej vložit do souboru parameters.json. Chcete-li získat ID:

    1. V jiném okně prohlížeče se přihlaste na [portál Azure](https://portal.azure.com) a vyberte **skupiny prostředků**.
    2. Vyhledejte cílovou skupinu prostředků obsahující veřejnou IP adresu, kterou jste přesunuli v předchozích krocích. Vyberte ji.
    3. Vyberte **Vlastnosti** > **nastavení**.
    4. V noži vpravo **zvýrazněte ID prostředku** a zkopírujte ho do schránky. Alternativně můžete vybrat **kopii do schránky** napravo od cesty **ID prostředku.**
    5. Vložte ID prostředku do **vlastnosti value** v editoru **Upravit parametry,** který je otevřený v druhém okně prohlížeče nebo na kartě:

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
    6. V online editoru vyberte **Uložit.**


7.  Pokud jste pro nástroje pro vyrovnávání zatížení nakonfigurovali odchozí nat a odchozí pravidla, zobrazí se v tomto souboru třetí položka pro externí ID odchozí veřejné IP adresy. Opakováním předchozích kroků v **cílové oblasti** získáte ID odchozí veřejné IP adresy. Vložte toto ID do souboru parameters.json:

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

8.  Vyberte **Šablona** > **Upravit šablonu,** chcete-li otevřít soubor template.json v online editoru.
9.  Chcete-li upravit cílovou oblast, do které bude přesunuta externí konfigurace nástroje pro vyrovnávání zatížení, změňte vlastnost **umístění** pod **prostředky** v souboru template.json:

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

10. Kódy umístění oblasti najdete v [tématu Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód pro oblast je název oblasti bez mezer. Například kód pro střední USA je **centralus**.

11. Můžete také změnit další parametry v šabloně, pokud chcete nebo potřebujete, v závislosti na vašich požadavcích:

    * **Skladová položka**. Skladovou položku externího nástroje pro vyrovnávání zatížení v konfiguraci můžete změnit ze standardního na základní nebo ze základního na standardní změnou vlastnosti **name** pod **sku** v souboru template.json:

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
      Informace o rozdílech mezi základními a standardními nástroje pro vyrovnávání zatížení skladových účto vsazení skladových p. [Azure najdete v tématu Přehled nástroje pro vyrovnávání zatížení azure standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

    * **Pravidla vyrovnávání zatížení**. V konfiguraci můžete přidat nebo odebrat pravidla vyrovnávání zatížení přidáním nebo odebráním položek v části **loadBalancingRules** souboru template.json:

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
       Informace o pravidlech vyrovnávání zatížení najdete v tématu [Co je Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Sondy**. Sondu pro nástroj pro vyrovnávání zatížení v konfiguraci můžete přidat nebo odebrat přidáním nebo odebráním položek v části **sondy** souboru template.json:

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
       Další informace naleznete v [tématu Sondy stavu vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Příchozí pravidla NAT**. Můžete přidat nebo odebrat příchozí pravidla NAT pro vyrovnávání zatížení přidáním nebo odebráním položek v části **inboundNatRules** souboru template.json:

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
        Chcete-li dokončit přidání nebo odebrání pravidla příchozího nat, musí být pravidlo přítomno nebo odebráno jako vlastnost **typu** na konci souboru template.json:

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
        Informace o příchozích pravidlech NAT najdete v tématu [Co je Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Odchozí pravidla**. Odchozí pravidla v konfiguraci můžete přidat nebo odebrat úpravou vlastnosti **outboundRules** v souboru template.json:

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

         Další informace naleznete v tématu [Odchozí pravidla vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. V online editoru vyberte **Uložit.**

13. Vyberte**předplatné** **BASICS** > a zvolte předplatné, ve kterém bude nasazen cílový externí systém vyrovnávání zatížení.

15. Vyberte**skupinu prostředků** **BASICS** > a zvolte skupinu prostředků, do které bude nasazen cílový systém vyrovnávání zatížení. Můžete vybrat **Vytvořit nový** a vytvořit novou skupinu prostředků pro cílový externí ekvizér zatížení. Nebo můžete zvolit existující skupinu prostředků, kterou jste vytvořili dříve pro veřejnou IP adresu. Ujistěte se, že název není stejný jako zdrojová skupina prostředků existujícího zdrojového externího vytápěče zatížení.

16. Ověřte, zda je**umístění** **BASICS** > nastaveno na cílové umístění, kam chcete nasadit externí systém vyrovnávání zatížení.

17. V části **NASTAVENÍ**ověřte, zda název odpovídá názvu, který jste zadali dříve v editoru parametrů. Ověřte, zda jsou ID prostředků naplněna pro všechny veřejné IP adresy v konfiguraci.

18. Zaškrtněte políčko **PODMÍNKY.**

19. Vyberte **Nákup,** chcete-li nasadit cílovou veřejnou IP adresu.

## <a name="discard"></a>Zahodit

Pokud chcete zahodit cílovou veřejnou IP adresu a externí vyvyčován, odstraňte skupinu prostředků, která je obsahuje. Chcete-li tak učinit, vyberte skupinu prostředků z řídicího panelu na portálu a pak v horní části stránky přehledu vyberte **Odstranit.**

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun veřejné IP adresy a externího vykladače zatížení, odstraňte zdrojovou veřejnou IP adresu a externí vyvyřizovač zatížení nebo skupinu prostředků. Chcete-li tak učinit, vyberte tuto skupinu prostředků z řídicího panelu na portálu a pak v horní části každé stránky vyberte **Odstranit.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli externí vyrovnávání zatížení Azure z jedné oblasti do druhé a vyčistit zdrojové prostředky. Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

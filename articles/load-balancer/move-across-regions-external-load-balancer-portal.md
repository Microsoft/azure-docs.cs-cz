---
title: Přesunout externí Load Balancer Azure do jiné oblasti Azure pomocí Azure Portal
description: Pomocí šablony Azure Resource Manager můžete přesunout externí Load Balancer Azure z jedné oblasti Azure do jiné pomocí Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: eda0d6e8fe56b985c3b29fa80cee880444d63741
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105287"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-the-azure-portal"></a>Přesunout externí Load Balancer Azure do jiné oblasti pomocí Azure Portal

Existují různé scénáře, ve kterých byste chtěli přesunout existující externí nástroj pro vyrovnávání zatížení z jedné oblasti do druhé. Například může být vhodné vytvořit externí nástroj pro vyrovnávání zatížení se stejnou konfigurací pro testování. Externí nástroj pro vyrovnávání zatížení můžete také přesunout do jiné oblasti v rámci plánování zotavení po havárii.

Externí nástroje pro vyrovnávání zatížení Azure nejde přesunout z jedné oblasti do druhé. Můžete ale použít šablonu Azure Resource Manager k exportu existující konfigurace a veřejné IP adresy externího nástroje pro vyrovnávání zatížení.  Potom můžete prostředek připravit v jiné oblasti tak, že vyexportujete Nástroj pro vyrovnávání zatížení a veřejnou IP adresu do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablony nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [Export skupin prostředků do šablon](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) .


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je externí nástroj pro vyrovnávání zatížení Azure v oblasti Azure, ze které chcete přejít.

- Externí nástroje pro vyrovnávání zatížení Azure se nedají přesouvat mezi oblastmi.  Nový nástroj pro vyrovnávání zatížení bude nutné přidružit k prostředkům v cílové oblasti.

- Pokud chcete exportovat konfiguraci externího nástroje pro vyrovnávání zatížení a nasadit šablonu pro vytvoření externího nástroje pro vyrovnávání zatížení v jiné oblasti, budete potřebovat roli Přispěvatel sítě nebo vyšší.
   
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, veřejné IP adresy a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet externí nástroje pro vyrovnávání zatížení v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání nástrojů pro vyrovnávání zatížení pro tento proces.  Viz [limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit externí nástroj pro vyrovnávání zatížení pro přesun pomocí šablony Správce prostředků a přesunout konfiguraci externího nástroje pro vyrovnávání zatížení do cílové oblasti pomocí Azure Portal.  V rámci tohoto procesu musí být před přesunutím externího nástroje pro vyrovnávání zatížení zahrnutá konfigurace veřejné IP adresy externího nástroje pro vyrovnávání zatížení a musí se nejdřív udělat.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-the-portal"></a>Exportujte šablonu veřejné IP adresy a nasaďte ji z portálu.

1. Přihlaste se ke**skupinám prostředků** [Azure Portal](http://portal.azure.com) > .
2. Vyhledejte skupinu prostředků, která obsahuje zdrojovou veřejnou IP adresu, a klikněte na ni.
3. Vyberte > **Nastavení** > **Exportovat šablonu**.
4. V okně **Exportovat šablonu** vyberte **nasadit** .
5. Kliknutím na **šablonu** > **Upravit parametry** otevřete soubor **Parameters. JSON** v online editoru.
8. Pokud chcete upravit parametr názvu veřejné IP adresy, změňte vlastnost v části **parametry** > **hodnota** ze zdrojového názvu veřejné IP adresy na název cílové veřejné IP adresy, ujistěte se, že je název v uvozovkách:

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

    V Editoru klikněte na **Uložit** .

9.  Kliknutím na **Šablona** > **Upravit šablonu** otevřete soubor **template. JSON** v online editoru. 

10. Chcete-li upravit cílovou oblast, kam bude přesunuta veřejná IP adresa, změňte vlastnost **umístění** v části **prostředky**:

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
  
11. Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer, **střed USA** = **centralus**.
    
12. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:

    * **SKU** -SKU veřejné IP adresy můžete změnit v konfiguraci z úrovně Standard na Basic nebo Basic na standard, a to změnou vlastnosti**název** **SKU** > v souboru **template. JSON** :

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

        Další informace o rozdílech mezi veřejnými IP adresami Basic a Standard SKU najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Metoda přidělování veřejných IP adres** a **časový limit nečinnosti** – obě tyto možnosti v šabloně můžete změnit tak, že změníte vlastnost **publicIPAllocationMethod** z **dynamické** na **statickou** nebo **statickou** na **dynamickou.** . Časový limit nečinnosti lze změnit změnou vlastnosti **idleTimeoutInMinutes** na požadovanou hodnotu.  Výchozí hodnota je **4**:

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

        Další informace o metodách přidělování a hodnotách časového limitu nečinnosti najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. V online Editoru klikněte na **Uložit** .

14. Klikněte na **základy** > **předplatné** a vyberte předplatné, ve kterém se nasadí cílová veřejná IP adresa.

15. Klikněte na **základy** > **Skupina prostředků** a vyberte skupinu prostředků, do které se bude nasadit cílová veřejná IP adresa.  Kliknutím na **vytvořit novou** můžete vytvořit novou skupinu prostředků pro cílovou veřejnou IP adresu.  Ujistěte se, že název není stejný jako zdrojová skupina prostředků stávající zdrojové veřejné IP adresy. 

16. Ověření **základních** > **umístění** je nastaveno na cílové umístění, ve kterém chcete nasadit veřejnou IP adresu.

17. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali v editoru parametrů výše.

18. Zaškrtněte políčko v části **podmínky a ujednání**.

19. Kliknutím na tlačítko **koupit** nasadíte cílovou veřejnou IP adresu.
20. Pokud máte jinou veřejnou IP adresu, která se používá pro odchozí překlad adres (NAT) pro přemístění nástroje pro vyrovnávání zatížení, opakujte výše uvedené kroky a exportujte a nasaďte druhou odchozí veřejnou IP adresu do cílové oblasti.

### <a name="export-the-external-load-balancer-template-and-deploy-from-the-azure-portal"></a>Exportujte šablonu externího nástroje pro vyrovnávání zatížení a nasaďte ji z Azure Portal

1. Přihlaste se ke**skupinám prostředků** [Azure Portal](http://portal.azure.com) > .
2. Vyhledejte skupinu prostředků, která obsahuje zdrojový externí nástroj pro vyrovnávání zatížení, a klikněte na ni.
3. Vyberte > **Nastavení** > **Exportovat šablonu**.
4. V okně **Exportovat šablonu** vyberte **nasadit** .
5. Kliknutím na **šablonu** > **Upravit parametry** otevřete soubor **Parameters. JSON** v online editoru.

5. Chcete-li upravit parametr názvu externí služby Vyrovnávání zatížení, změňte **hodnotu** vlastnosti zdrojového externího nástroje pro vyrovnávání zatížení na název vašeho cílového externího nástroje pro vyrovnávání zatížení, ujistěte se, že je název v uvozovkách:

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

6.  Pokud chcete upravit hodnotu cílové veřejné IP adresy, která se přesunula výše, musíte nejdřív získat ID prostředku a pak ho zkopírovat a vložit do souboru **Parameters. JSON** . Získání ID:
    
    1. Přihlaste se k [Azure Portal](http://portal.azure.com) > **skupinám prostředků** na jiné kartě nebo okně prohlížeče.
    2. Vyhledejte cílovou skupinu prostředků, která obsahuje přesunutou veřejnou IP adresu z výše uvedeného postupu, a klikněte na ni.
    3. Vyberte >**vlastnosti** **Nastavení** > .
    4. V okně vpravo zvýrazněte **ID prostředku** a zkopírujte ho do schránky.  Případně můžete kliknout na tlačítko **Kopírovat do schránky** napravo od cesty k **ID prostředku** .
    5. Vložte ID prostředku do vlastnosti **hodnota** v editoru **úprav parametrů** otevřeném v jiném okně nebo na kartě prohlížeče:

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
    6. V online Editoru klikněte na **Uložit** .
   

7.  Pokud jste nakonfigurovali odchozí NAT a odchozí pravidla pro nástroj pro vyrovnávání zatížení, v tomto souboru se objeví třetí položka pro externí ID odchozí veřejné IP adresy.  Opakujte výše uvedené kroky v **cílové oblasti** , abyste získali ID odchozí veřejné IP adresy a vložili tuto položku do souboru **Parameters. JSON** :

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

8.  Kliknutím na **Šablona** > **Upravit šablonu** otevřete soubor **template. JSON** v online editoru.
9.  Chcete-li upravit cílovou oblast, kde bude přesunuta konfigurace externího nástroje pro vyrovnávání zatížení, změňte vlastnost **umístění** v části **prostředky** v souboru **template. JSON** :

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

10. Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer, **střed USA** = **centralus**.

11. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:
    
    * **SKU** -SKU externí služby Load Balancer můžete změnit v konfiguraci z úrovně Standard na Basic nebo Basic na úroveň Standard, a to změnou vlastnosti**název** **SKU** > v souboru **template. JSON** :

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

    * **Pravidla vyrovnávání zatížení** – můžete přidat nebo odebrat pravidla vyrovnávání zatížení v konfiguraci přidáním nebo odebráním položek do oddílu **loadBalancingRules** souboru **template. JSON** :

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

    * **Sondy** – pro nástroj pro vyrovnávání zatížení v konfiguraci můžete přidat nebo odebrat test tak, že do části **sondy** v souboru **template. JSON** přidáte nebo odeberete položky.

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

    * **Pravidla příchozího překladu adres (NAT)** – můžete přidat nebo odebrat příchozí pravidla NAT pro nástroj pro vyrovnávání zatížení přidáním nebo odebráním položek do oddílu **inboundNatRules** souboru **template. JSON** :

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
        Aby bylo možné dokončit přidání nebo odebrání pravidla příchozího překladu adres (NAT), musí být pravidlo přítomno nebo odebráno jako vlastnost **typu** na konci souboru **template. JSON** :

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

    * **Odchozí pravidla** – v konfiguraci můžete přidat nebo odebrat odchozí pravidla úpravou vlastnosti **outboundRules** v souboru **template. JSON** :

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

12. V online Editoru klikněte na **Uložit** .
    
13. Kliknutím na položku **základní** > **předplatné** zvolíte předplatné, kde bude nasazen cílový externí nástroj pro vyrovnávání zatížení.

15. Klikněte na **základy** > **Skupina prostředků** a vyberte skupinu prostředků, ve které bude nasazený cílový Nástroj pro vyrovnávání zatížení.  Kliknutím na **vytvořit novou** můžete vytvořit novou skupinu prostředků pro cílový externí nástroj pro vyrovnávání zatížení nebo vybrat existující skupinu prostředků, která byla vytvořena výše pro veřejnou IP adresu.  Ujistěte se, že název není stejný jako zdrojová skupina prostředků existujícího zdrojového externího nástroje pro vyrovnávání zatížení. 

16. Ověření **základních** > **umístění** je nastaveno na cílové umístění, do kterého chcete nasadit externí nástroj pro vyrovnávání zatížení.

17. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali v editoru parametrů výše.  Ověřte, že ID prostředků se naplní pro všechny veřejné IP adresy v konfiguraci.

18. Zaškrtněte políčko v části **podmínky a ujednání**.

19. Kliknutím na tlačítko **koupit** nasadíte cílovou veřejnou IP adresu.

## <a name="discard"></a>Zahodit 

Pokud chcete zrušit cílovou veřejnou IP adresu a externí nástroj pro vyrovnávání zatížení, odstraňte skupinu prostředků, která obsahuje cílovou veřejnou IP adresu a externí nástroj pro vyrovnávání zatížení.  Provedete to tak, že vyberete skupinu prostředků z řídicího panelu na portálu a v horní části stránky přehled vyberete **Odstranit** .

## <a name="clean-up"></a>Vyčištění

Pokud chcete potvrdit změny a dokončit přesun veřejné IP adresy a externího nástroje pro vyrovnávání zatížení, odstraňte zdrojovou veřejnou IP adresu a externí nástroj pro vyrovnávání zatížení nebo skupinu prostředků. Provedete to tak, že na portálu vyberete možnost Veřejná IP adresa a externí nástroj pro vyrovnávání zatížení nebo skupinu prostředků z řídicího panelu a v horní části každé stránky vyberete **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli externí nástroj pro vyrovnávání zatížení Azure z jedné oblasti na jiný a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

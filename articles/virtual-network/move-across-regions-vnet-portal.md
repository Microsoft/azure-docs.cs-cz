---
title: Přesuňte Azure Virtual Network do jiné oblasti Azure pomocí Azure Portal.
description: Pomocí šablony Azure Resource Manager můžete přesunout Azure Virtual Network z jedné oblasti Azure do jiné pomocí Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 2a1ee358a6c97b721ec6f0da3eb70269239b0737
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077669"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Přesunout Azure Virtual Network do jiné oblasti pomocí Azure Portal

Existují různé scénáře, ve kterých byste chtěli přesunout stávající virtuální sítě Azure (virtuální sítě) z jedné oblasti do druhé. Například můžete chtít vytvořit virtuální síť se stejnou konfigurací pro testování a dostupnost stávající virtuální sítě. V rámci plánování zotavení po havárii možná budete chtít přesunout produkční virtuální síť do jiné oblasti.

K dokončení přesunu virtuální sítě do jiné oblasti můžete použít šablonu Azure Resource Manager. Provedete to tak, že virtuální síť vyexportujete do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [rychlý Start: Vytváření a nasazování šablon Azure Resource Manager pomocí Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je Azure Virtual Network v oblasti Azure, ze které chcete přejít.

- Pokud chcete exportovat virtuální síť a nasadit šablonu pro vytvoření virtuální sítě v jiné oblasti, budete potřebovat roli Přispěvatel sítě nebo vyšší.

- Partnerské vztahy virtuálních sítí se znovu nevytvoří a selžou, pokud jsou v šabloně pořád přítomné.  Před exportem šablony bude nutné odebrat všechny partnerské vztahy virtuální sítě a potom po přesunu virtuální sítě znovu vytvořit partnerské uzly.
    
- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (skupin zabezpečení sítě) a veřejné IP adresy.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální sítě v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání virtuálních sítí pro tento proces.  Viz [limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit virtuální síť pro přesun pomocí šablony Správce prostředků a přesunout virtuální síť do cílové oblasti pomocí portálu.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Export šablony a nasazení z portálu

1. Přihlaste se ke**skupinám prostředků** [Azure Portal](http://portal.azure.com) > .
2. Vyhledejte skupinu prostředků, která obsahuje zdrojovou virtuální síť, a klikněte na ni.
3. Vyberte > **Nastavení** > **Exportovat šablonu**.
4. V okně **Exportovat šablonu** vyberte **nasadit** .
5. Kliknutím na **šablonu** > **Upravit parametry** otevřete soubor **Parameters. JSON** v online editoru.
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

8.  V Editoru klikněte na **Uložit** .

9.  Kliknutím na **Šablona** > **Upravit šablonu** otevřete soubor **template. JSON** v online editoru. 

10. Pokud chcete upravit cílovou oblast, kam se virtuální síť přesune, změňte vlastnost **Location** v části **prostředky** v online Editoru:

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
 
11. Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer, **střed USA** = **centralus**.
 
12. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:
    
    * **Adresní prostor** – adresní prostor virtuální sítě je možné změnit před uložením úpravou části**addressSpace** **prostředků** > a změnou vlastnosti **addressPrefixes** v souboru **template. JSON** :

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

    * **Podsíť** – název podsítě a adresní prostor podsítě lze změnit nebo přidat k úpravou části **podsítě** souboru **template. JSON** . Název podsítě lze změnit změnou vlastnosti **název** . Adresní prostor podsítě lze změnit změnou vlastnosti **addressPrefix** v souboru **template. JSON** :

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

         Chcete-li změnit předponu adresy v souboru **template. JSON** , je nutné ji upravit na dvou místech, v části uvedené výše a v níže uvedené části **typ** .  Změňte vlastnost **addressPrefix** tak, aby odpovídala hodnotě výše:

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

14. Klikněte na **základy** > **předplatné** a vyberte předplatné, ve kterém bude cílová síť VNet nasazená.

15. Klikněte na **základy** > **Skupina prostředků** a vyberte skupinu prostředků, do které se nasadí Cílová síť VNET.  Kliknutím na **vytvořit novou** můžete vytvořit novou skupinu prostředků pro cílovou síť VNET.  Ujistěte se, že název není stejný jako zdrojová skupina prostředků stávající virtuální sítě. 

16. Ověření **základních** > **umístění** je nastaveno na cílové umístění, ve kterém chcete virtuální síť nasadit.

17. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali v editoru parametrů výše.

18. Zaškrtněte políčko v části **podmínky a ujednání**.

19. Kliknutím na tlačítko **koupit** nasadíte cílovou virtuální síť.

## <a name="discard"></a>Zahodit 

Pokud chcete zrušit cílovou virtuální síť, odstraňte skupinu prostředků, která obsahuje cílovou virtuální síť.  Provedete to tak, že vyberete skupinu prostředků z řídicího panelu na portálu a v horní části stránky přehled vyberete **Odstranit** .

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun virtuální sítě, odstraňte zdrojovou virtuální síť nebo skupinu prostředků. Provedete to tak, že vyberete virtuální síť nebo skupinu prostředků z řídicího panelu na portálu a v horní části každé stránky vyberete **Odstranit** .

## <a name="next-steps"></a>Další postup

V tomto kurzu jste přesunuli Azure Virtual Network z jedné oblasti na jinou a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

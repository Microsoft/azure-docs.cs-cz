---
title: Přesuňte virtuální síť Azure do jiné oblasti Azure pomocí portálu Azure.
description: Přesuňte virtuální síť Azure z jedné oblasti Azure do jiné pomocí šablony Správce prostředků a portálu Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640784"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Přesunutí virtuální sítě Azure do jiné oblasti pomocí portálu Azure

Existují různé scénáře pro přesunutí existující virtuální sítě Azure z jedné oblasti do druhé. Můžete například chtít vytvořit virtuální síť se stejnou konfigurací pro testování a dostupnost jako stávající virtuální síť. Nebo můžete chtít přesunout produkční virtuální síť do jiné oblasti jako součást plánování zotavení po havárii.

K dokončení přesunu virtuální sítě do jiné oblasti můžete použít šablonu Azure Resource Manager. To provést exportem virtuální sítě do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a následným nasazením šablony do nové oblasti. Další informace o šablonách Správce prostředků najdete [v tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že vaše virtuální síť je v oblasti Azure, ze které chcete přejít.

- Chcete-li exportovat virtuální síť a nasadit šablonu k vytvoření virtuální sítě v jiné oblasti, musíte mít roli přispěvatele sítě nebo vyšší.

- Partnerské servery virtuální sítě se znovu nevytvoří a nezdaří se, pokud jsou v šabloně stále k dispozici. Před exportem šablony je třeba odebrat všechny partnery virtuální sítě. Potom je můžete obnovit po přesunutí virtuální sítě.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné vykladače zatížení, skupiny zabezpečení sítě (NSG) a veřejné IP adresy.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální sítě v cílové oblasti. Chcete-li povolit požadovanou kvótu, obraťte se na podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání virtuálních sítí pro tento proces. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Připravte se na přesun
V této části připravíte virtuální síť pro přesun pomocí šablony Správce prostředků. Potom přesunete virtuální síť do cílové oblasti pomocí portálu Azure.

Pokud chcete exportovat virtuální síť a nasadit cílovou virtuální síť pomocí portálu Azure, postupujte takto:

1. Přihlaste se k [portálu Azure a](https://portal.azure.com)vyberte **Skupiny prostředků**.
1. Vyhledejte skupinu prostředků, která obsahuje zdrojovou virtuální síť, a vyberte ji.
1. Vyberte **Nastavení** > **exportu šablony**.
1. V podokně **předlohy Export** vyberte **Nasadit**.
1. Chcete-li otevřít soubor *parameters.json* v online editoru, vyberte**parametry úpravy** **šablony** > .
1. Chcete-li upravit parametr názvu virtuální sítě, změňte **vlastnost hodnoty** pod **parametry**:

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

1. V editoru změňte hodnotu názvu zdrojové virtuální sítě v editoru na název, který chcete pro cílovou virtuální síť. Název nezapomeňte uzavřít do uvozovek.

1. V editoru vyberte **Uložit.**

1. Chcete-li otevřít soubor *template.json* v online editoru, vyberte **šablonu** > **Upravit šablonu**.

1. Chcete-li v online editoru upravit cílovou oblast, do které bude virtuální síť přesunuta, změňte vlastnost **umístění** v části **prostředky**:

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

1. Kódy umístění oblasti najdete v [tématu Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód pro oblast je název oblasti, bez mezer (například **central US** = **centralus**).

1. (Nepovinné) Můžete také změnit další parametry v šabloně, v závislosti na vašich požadavcích:

    * **Adresní prostor**: Před uložením souboru můžete změnit adresní prostor virtuální sítě úpravou oddílu**adresní prostor** **prostředků a** > změnou vlastnosti **addressPrefixes:**

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

    * **Podsíť**: Název podsítě a adresní prostor podsítě můžete změnit změnou oddílu **podsítí** šablony. Název podsítě můžete změnit změnou vlastnosti **name.** A můžete změnit adresní prostor podsítě změnou vlastnosti **addressPrefix:**

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

        Chcete-li změnit předponu adresy v souboru *template.json,* upravte ji na dvou místech: v kódu v předchozí části a v části **typu** následujícího kódu. Změňte vlastnost **addressPrefix** v následujícím kódu tak, aby odpovídala vlastnosti **addressPrefix** v kódu v předchozí části.

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

1. V online editoru vyberte **Uložit**.

1. Pokud chcete vybrat předplatné, ve kterém bude nasazená cílová virtuální síť, vyberte **Základní** > **předplatné**.

1. Chcete-li vybrat skupinu prostředků, do které bude nasazena cílová virtuální síť, vyberte **základní** > **skupina prostředků**. 

    Pokud potřebujete vytvořit novou skupinu prostředků pro cílovou virtuální síť, vyberte **Vytvořit nový**. Ujistěte se, že název není stejný jako název skupiny zdrojových prostředků v existující virtuální síti.

1. Ověřte, zda je**umístění** **Základy** > nastaveno na cílové umístění, kam chcete virtuální síť nasadit.

1. V části **Nastavení**ověřte, zda název odpovídá názvu, který jste zadali dříve v editoru parametrů.

1. Zaškrtněte políčko **Smluvní podmínky.**

1. Pokud chcete nasadit cílovou virtuální síť, vyberte **Koupit**.

## <a name="delete-the-target-virtual-network"></a>Odstranění cílové virtuální sítě

Chcete-li zahodit cílovou virtuální síť, odstraňte skupinu prostředků, která obsahuje cílovou virtuální síť. Postupujte následovně:
1. Na řídicím panelu portálu Azure vyberte skupinu prostředků.
1. V horní části podokna **Přehled** vyberte **Odstranit**.

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesunutí virtuální sítě, odstraňte zdrojovou virtuální síť nebo skupinu prostředků. Postupujte následovně:
1. Na řídicím panelu portálu Azure vyberte virtuální síť nebo skupinu prostředků.
1. V horní části každého podokna vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli virtuální síť Azure z jedné oblasti do druhé pomocí portálu Azure a potom vyčistit nepotřebné zdrojové prostředky. Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesunutí virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

---
title: Přesuňte virtuální síť Azure do jiné oblasti Azure pomocí Azure Portal.
description: Přesuňte virtuální síť Azure z jedné oblasti Azure do jiné pomocí Správce prostředků šablony a Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0a80c731e4245b1a295364e5b8c87f90290f7f74
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223304"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Přesuňte virtuální síť Azure do jiné oblasti pomocí Azure Portal

Existují různé scénáře pro přesun existující virtuální sítě Azure z jedné oblasti do druhé. Například můžete chtít vytvořit virtuální síť se stejnou konfigurací pro testování a dostupnost jako stávající virtuální síť. Případně můžete chtít přesunout produkční virtuální síť do jiné oblasti v rámci plánování zotavení po havárii.

K dokončení přesunu virtuální sítě do jiné oblasti můžete použít šablonu Azure Resource Manager. Provedete to tak, že virtuální síť vyexportujete do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti. Další informace o šablonách Správce prostředků najdete v tématu [rychlý Start: vytvoření a nasazení Azure Resource Manager šablon pomocí Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je vaše virtuální síť v oblasti Azure, ze které chcete přejít.

- Pokud chcete exportovat virtuální síť a nasadit šablonu pro vytvoření virtuální sítě v jiné oblasti, musíte mít roli Přispěvatel sítě nebo vyšší.

- Partnerské vztahy virtuálních sítí se znovu nevytvoří a selžou, pokud jsou v šabloně pořád přítomné. Před exportem šablony musíte odebrat všechny partnerské uzly virtuální sítě. Po přesunu virtuální sítě je pak můžete znovu vytvořit.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (skupin zabezpečení sítě) a veřejné IP adresy.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální sítě v cílové oblasti. Pokud chcete povolit požadovanou kvótu, obraťte se na podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání virtuálních sítí pro tento proces. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Příprava na přesun
V této části připravíte virtuální síť pro přesun pomocí šablony Správce prostředků. Poté přesunete virtuální síť do cílové oblasti pomocí Azure Portal.

Pokud chcete exportovat virtuální síť a nasadit cílovou virtuální síť pomocí Azure Portal, udělejte toto:

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak vyberte **skupiny prostředků**.
1. Vyhledejte skupinu prostředků, která obsahuje zdrojovou virtuální síť, a pak ji vyberte.
1. Vyberte **Nastavení**  >  **Exportovat šablonu**.
1. V podokně **Exportovat šablonu** vyberte **nasadit**.
1. Chcete-li otevřít *parameters.js* v souboru v editoru online, vyberte možnost **Šablona**  >  **Upravit parametry**.
1. Chcete-li upravit parametr názvu virtuální sítě, změňte vlastnost **Value** v části **parametry**:

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

1. V editoru změňte hodnotu název zdrojové virtuální sítě v editoru na název, který chcete pro cílovou virtuální síť. Nezapomeňte název uzavřít do uvozovek.

1. V editoru vyberte **Save (Uložit** ).

1. Chcete-li otevřít *template.js* v souboru v editoru online, vyberte **šablonu**  >  **Upravit šablonu**.

1. V online editoru můžete upravit cílovou oblast, do které se virtuální síť přesune, a změnit vlastnost **Location** v části **prostředky**:

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

1. Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti, bez mezer (například **střed USA**  =  **centralus**).

1. Volitelné V závislosti na vašich požadavcích můžete také změnit jiné parametry v šabloně.

    * **Adresní prostor**: před uložením souboru můžete změnit adresní prostor virtuální sítě změnou   >  části **addressSpace** prostředků a změnou vlastnosti **addressPrefixes** :

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

    * **Podsíť**: můžete změnit nebo přidat k názvu podsítě a adresnímu prostoru podsítě změnou oddílu **podsítě** šablony. Název podsítě můžete změnit změnou vlastnosti **název** . Adresní prostor podsítě můžete změnit tak, že změníte vlastnost **addressPrefix** :

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

        Chcete-li změnit předponu adresy v *template.js* souboru, upravte ji na dvou místech: v kódu v předchozí části a v části **typ** následujícího kódu. Změňte vlastnost **addressPrefix** v následujícím kódu tak, aby odpovídala vlastnosti **addressPrefix** v kódu v předchozí části.

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

1. V online editoru vyberte Save ( **Uložit**).

1. Pokud chcete zvolit předplatné, ve kterém se bude nasazovat cílová virtuální síť , vyberte  >  **předplatné** základní.

1. Chcete-li zvolit skupinu prostředků, do které bude nasazena cílová virtuální síť , vyberte možnost  >  **Skupina prostředků** základní. 

    Pokud pro cílovou virtuální síť potřebujete vytvořit novou skupinu prostředků, vyberte **vytvořit novou**. Ujistěte se, že název není stejný jako název zdrojové skupiny prostředků ve stávající virtuální síti.

1. Ověřte, zda  >  je umístění základních **umístění** nastaveno na cílové umístění, do kterého chcete virtuální síť nasadit.

1. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali dříve v editoru parametrů.

1. Zaškrtněte políčko **podmínky a ujednání** .

1. Pokud chcete nasadit cílovou virtuální síť, vyberte **koupit**.

## <a name="delete-the-target-virtual-network"></a>Odstranit cílovou virtuální síť

Pokud chcete zrušit cílovou virtuální síť, odstraňte skupinu prostředků, která obsahuje cílovou virtuální síť. Postupujte následovně:
1. Na řídicím panelu Azure Portal vyberte skupinu prostředků.
1. V horní části podokna **přehledu** vyberte **Odstranit**.

## <a name="clean-up"></a>Vyčištění

Pokud chcete potvrdit změny a dokončit přesun virtuální sítě, odstraňte zdrojovou virtuální síť nebo skupinu prostředků. Postupujte následovně:
1. Na řídicím panelu Azure Portal vyberte virtuální síť nebo skupinu prostředků.
1. V horní části každého podokna vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli virtuální síť Azure z jedné oblasti do druhé pomocí Azure Portal a pak vyčistili nepotřebné zdrojové prostředky. Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesunutí virtuálních počítačů Azure do jiné oblasti](../site-recovery/azure-to-azure-tutorial-migrate.md)
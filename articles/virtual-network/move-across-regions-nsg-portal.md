---
title: Přesuňte skupinu zabezpečení sítě Azure (NSG) do jiné oblasti Azure pomocí Azure Portal
description: Pomocí šablony Azure Resource Manager můžete přesunout skupinu zabezpečení sítě Azure z jedné oblasti Azure do jiné pomocí Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 11b0260eb6c1f5edfb246dcf97a43bdd874bf337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217273"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Přesuňte skupinu zabezpečení sítě Azure (NSG) do jiné oblasti pomocí Azure Portal

Existují různé scénáře, ve kterých byste chtěli přesunout stávající skupin zabezpečení sítě z jedné oblasti do druhé. Například může být vhodné vytvořit NSG se stejnou konfigurací a pravidly zabezpečení pro testování. V rámci plánování zotavení po havárii možná budete chtít přesunout NSG do jiné oblasti.

Skupiny zabezpečení Azure není možné přesunout z jedné oblasti do jiné. K exportu existující konfigurace a pravidel zabezpečení NSG ale můžete použít šablonu Azure Resource Manager.  Pak můžete vytvořit prostředek v jiné oblasti tak, že NSG exportujete do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [rychlý Start: vytvoření a nasazení Azure Resource Manager šablon pomocí Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je skupina zabezpečení sítě Azure v oblasti Azure, ze které chcete přejít.

- Skupiny zabezpečení sítě Azure nelze přesouvat mezi oblastmi.  Bude nutné přidružit nový NSG k prostředkům v cílové oblasti.

- Pokud chcete exportovat konfiguraci NSG a nasadit šablonu pro vytvoření NSG v jiné oblasti, budete potřebovat roli Přispěvatel sítě nebo vyšší.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, veřejné IP adresy a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet skupin zabezpečení sítě v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu přidání skupin zabezpečení sítě pro tento proces.  Viz [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit skupinu zabezpečení sítě pro pravidlo konfigurace a pravidla zabezpečení s použitím šablony Správce prostředků a přesunutím konfigurace a pravidel zabezpečení NSG do cílové oblasti pomocí portálu.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Export šablony a nasazení z portálu

1. Přihlaste [](https://portal.azure.com)se ke  >  **skupinám prostředků** Azure Portal.
2. Vyhledejte skupinu prostředků obsahující zdrojové NSG a klikněte na ni.
3. Vyberte > **Nastavení**  >  **Exportovat šablonu**.
4. V okně **Exportovat šablonu** vyberte **nasadit** .
5. Kliknutím na **šablonu**  >  **Upravit parametry** otevřete **parameters.js** v souboru v online editoru.
6. Chcete-li upravit parametr názvu NSG, změňte hodnotu vlastnosti **Value** v části **parametry**:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Změňte hodnotu vlastnosti source NSG v editoru na název, který si zvolíte pro cílový NSG. Zajistěte, aby byl název uzavřen v uvozovkách.

8.  V Editoru klikněte na **Uložit** .

9.  Kliknutím na **šablonu**  >  **Upravit šablonu** otevřete **template.js** v souboru v online editoru.

10. Chcete-li upravit cílovou oblast, kde budou přesunuta konfigurace NSG a pravidla zabezpečení, změňte vlastnost **umístění** v části **prostředky** v online Editoru:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer, **střed USA**  =  **centralus**.

12. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:

    * **Pravidla zabezpečení** – pravidla, která se nasazují do cílových NSG, můžete upravit přidáním nebo odebráním pravidel do oddílu **securityRules** v **template.js** souboru:

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Chcete-li dokončit sčítání nebo odebírání pravidel v cílovém NSG, je nutné také upravit typy vlastních pravidel na konci **template.jsv** souboru ve formátu níže uvedeného příkladu:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. V online Editoru klikněte na **Uložit** .

14. Klikněte na **základy**  >  **předplatné** a vyberte předplatné, ve kterém se bude nasazovat cílový NSG.

15. Klikněte na **základy**  >  **Skupina prostředků** a vyberte skupinu prostředků, do které bude nasazený cílový NSG.  Kliknutím na **vytvořit novou** můžete vytvořit novou skupinu prostředků pro cílový NSG.  Ujistěte se, že název není stejný jako zdrojová skupina prostředků existující NSG.

16. Ověření **základních**  >  **umístění** je nastaveno na cílové umístění, ve kterém chcete NSG nasadit.

17. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali v editoru parametrů výše.

18. Zaškrtněte políčko v části **podmínky a ujednání**.

19. Kliknutím na tlačítko **koupit** nasaďte cílovou skupinu zabezpečení sítě.

## <a name="discard"></a>Zahodit

Pokud chcete zrušit cílovou NSG, odstraňte skupinu prostředků, která obsahuje cílovou NSG.  Provedete to tak, že vyberete skupinu prostředků z řídicího panelu na portálu a v horní části stránky přehled vyberete **Odstranit** .

## <a name="clean-up"></a>Vyčištění

Pokud chcete potvrdit změny a dokončit přesun NSG, odstraňte zdrojový NSG nebo skupinu prostředků. Provedete to tak, že vyberete skupinu zabezpečení sítě nebo skupinu prostředků z řídicího panelu na portálu a v horní části každé stránky vyberete **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli skupinu zabezpečení sítě Azure z jedné oblasti na jinou a vyčistili jste zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesun virtuálních počítačů Azure do jiné oblasti](../site-recovery/azure-to-azure-tutorial-migrate.md)
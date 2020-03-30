---
title: Přesunutí skupiny zabezpečení sítě Azure (NSG) do jiné oblasti Azure pomocí portálu Azure
description: Pomocí šablony Azure Resource Manager můžete pomocí webu Azure Portal přesunout skupinu zabezpečení sítě Azure z jedné oblasti Azure do jiné.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647182"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Přesunutí skupiny zabezpečení sítě Azure (NSG) do jiné oblasti pomocí portálu Azure

Existují různé scénáře, ve kterých byste chtěli přesunout existující nsg z jedné oblasti do druhé. Například můžete chtít vytvořit skupinu zabezpečení se stejnou konfigurací a bezpečnostními pravidly pro testování. Můžete také přesunout nsg do jiné oblasti jako součást plánování zotavení po havárii.

Skupiny zabezpečení Azure nelze přesunout z jedné oblasti do druhé. Šablonu Azure Resource Manager však můžete použít k exportu existujícíkonfigurace a bezpečnostních pravidel skupiny zabezpečení.  Potom můžete zinscenovat prostředek v jiné oblasti exportem skupiny nsg do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a potom šablonu nasadit do nové oblasti.  Další informace o Správci prostředků a šablonách najdete v [tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že skupina zabezpečení sítě Azure je v oblasti Azure, ze které chcete přesunout.

- Skupiny zabezpečení sítě Azure nelze přesunout mezi oblastmi.  Budete muset přidružit nový skupiny nsg k prostředkům v cílové oblasti.

- Chcete-li exportovat konfiguraci skupiny zabezpečení sítě a nasadit šablonu k vytvoření skupiny zabezpečení sítě v jiné oblasti, budete potřebovat roli přispěvatele sítě nebo vyšší.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné nástroje pro vyrovnávání zatížení, veřejné IP adresy a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet skupiny zabezpečení zabezpečení v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání nsg pro tento proces.  Viz [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit skupinu zabezpečení sítě pro přesunutí pravidla konfigurace a zabezpečení pomocí šablony Správce prostředků a přesunout pravidla konfigurace a zabezpečení skupiny zabezpečení sítě do cílové oblasti pomocí portálu.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Export šablony a nasazení z portálu

1. Přihlaste se ke**skupinám prostředků** [portálu](https://portal.azure.com) > Azure .
2. Vyhledejte skupinu prostředků, která obsahuje zdrojový soubor nsg, a klikněte na ni.
3. Vyberte > **nastavení** > **exportovat šablonu**.
4. V okně **Exportovat šablonu** zvolte **Nasadit.**
5. Kliknutím na**parametry pro úpravy** **šablony** > otevřete soubor **parameters.json** v online editoru.
6. Chcete-li upravit parametr názvu souboru NSG, změňte **vlastnost hodnoty** pod **parametry**:

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

7. Změňte zdrojovou hodnotu skupiny nsg v editoru na název, který si vyberete pro cílový soubor nsg. Ujistěte se, že jste název uzavřete do uvozovek.

8.  V editoru klikněte na **Uložit.**

9.  Kliknutím na **Šablona** > **Upravit šablonu** otevřete soubor **template.json** v online editoru.

10. Chcete-li upravit cílovou oblast, ve které budou přesunuta pravidla konfigurace a zabezpečení skupiny zabezpečení, změňte vlastnost **umístění** pod **prostředky** v online editoru:

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

11. Kódy umístění oblasti najdete v [tématu Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód pro oblast je název oblasti bez mezer, **centrální US** = **centralus**.

12. Můžete také změnit další parametry v šabloně, pokud se rozhodnete, a jsou volitelné v závislosti na vašich požadavcích:

    * **Pravidla zabezpečení** – Můžete upravit, která pravidla jsou nasazena do cílové skupiny zabezpečení nsg přidáním nebo odebráním pravidel do oddílu **securityRules** v souboru **template.json:**

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

      Chcete-li dokončit přidání nebo odebrání pravidel v cílovém souboru nsg, musíte také upravit vlastní typy pravidel na konci souboru **template.json** ve formátu následujícího příkladu:

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

13. V online editoru klikněte na **Uložit.**

14. Kliknutím na**předplatné** **BASICS** > vyberte předplatné, ve kterém bude nasadit cílový soubor nsg.

15. Kliknutím na**skupinu prostředků** **BASICS** > zvolte skupinu prostředků, do které bude nasadit cílový soubor nsg.  Chcete-li vytvořit novou skupinu prostředků pro cílovou skupinu nsg, můžete klepnout na tlačítko **Vytvořit nový.**  Ujistěte se, že název není stejný jako zdrojová skupina prostředků existující skupiny nsg.

16. Ověřte, že**umístění** **BASICS** > je nastaveno na cílové umístění, kde chcete nasadit skupinu nsg.

17. V části **NASTAVENÍ** ověřte, zda název odpovídá názvu, který jste zadali do výše uvedeného editoru parametrů.

18. Zaškrtněte políčko **v**podmínkách .

19. Klepnutím na tlačítko **Koupit** nasadíte cílovou skupinu zabezpečení sítě.

## <a name="discard"></a>Zahodit

Pokud chcete zahodit cílový skupinu nsg, odstraňte skupinu prostředků, která obsahuje cílovou skupinu nsg.  Chcete-li tak učinit, vyberte skupinu prostředků z řídicího panelu na portálu a v horní části stránky přehledu vyberte **Odstranit.**

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun skupiny nsg, odstraňte zdrojový soubor nSG nebo skupinu prostředků. Chcete-li tak učinit, vyberte skupinu zabezpečení sítě nebo skupinu prostředků z řídicího panelu na portálu a v horní části každé stránky vyberte **Odstranit.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli skupinu zabezpečení sítě Azure z jedné oblasti do druhé a vyčistili zdrojové prostředky.  Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

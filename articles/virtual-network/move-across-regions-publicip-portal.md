---
title: Přesunutí veřejné IP adresy Azure do jiné oblasti Azure pomocí portálu Azure
description: Pomocí šablony Azure Resource Manager můžete přesunout veřejnou IP adresu Azure z jedné oblasti Azure do jiné pomocí portálu Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6d10265e8383b68ebe13c95d8b2a9632668e85da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641393"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Přesunutí veřejné IP adresy Azure do jiné oblasti pomocí portálu Azure

Existují různé scénáře, ve kterých byste chtěli přesunout existující veřejné IP adresy Azure z jedné oblasti do druhé. Můžete například vytvořit veřejnou IP adresu se stejnou konfigurací a sku pro testování. Můžete také přesunout veřejnou IP adresu do jiné oblasti jako součást plánování zotavení po havárii.

Veřejné IP adresy Azure jsou specifické pro oblast a nelze je přesunout z jedné oblasti do druhé. Šablonu Azure Resource Manager však můžete použít k exportu existující konfigurace veřejné IP adresy.  Potom můžete zinscenovat prostředek v jiné oblasti exportem veřejné IP adresy do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a potom šablonu nasadit do nové oblasti.  Další informace o Správci prostředků a šablonách najdete v [tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že veřejná IP adresa Azure je v oblasti Azure, ze které chcete přesunout.

- Veřejné IP adresy Azure nelze přesunout mezi oblastmi.  Budete muset přidružit novou veřejnou IP adresu k prostředkům v cílové oblasti.

- Chcete-li exportovat konfiguraci veřejné IP adresy a nasadit šablonu k vytvoření veřejné IP adresy v jiné oblasti, budete potřebovat roli přispěvatele sítě nebo vyšší.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které právě používáte. Toto rozložení zahrnuje mimo jiné nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (NSG) a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet veřejné IP adresy v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu přidání veřejných IP adresy pro tento proces.  Viz [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit veřejnou IP adresu pro přesunutí konfigurace pomocí šablony Správce prostředků a přesunout konfiguraci veřejné IP adresy do cílové oblasti pomocí portálu Azure.

### <a name="export-the-template-and-deploy-from-a-script"></a>Export šablony a nasazení ze skriptu

1. Přihlaste se ke**skupinám prostředků** [portálu](https://portal.azure.com) > Azure .
2. Vyhledejte skupinu prostředků, která obsahuje zdrojovou veřejnou IP adresu, a klikněte na ni.
3. Vyberte > **nastavení** > **exportovat šablonu**.
4. V okně **Exportovat šablonu** zvolte **Nasadit.**
5. Kliknutím na**parametry pro úpravy** **šablony** > otevřete soubor **parameters.json** v online editoru.
8. Chcete-li upravit parametr veřejného názvu IP, změňte vlastnost pod**hodnotou** **parametrů** > ze zdrojového veřejného názvu IP na název cílové veřejné IP adresy, ujistěte se, že je název v uvozovkách:

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
8.  V editoru klikněte na **Uložit.**

9.  Kliknutím na **Šablona** > **Upravit šablonu** otevřete soubor **template.json** v online editoru.

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

11. Kódy umístění oblasti najdete v [tématu Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód pro oblast je název oblasti bez mezer, **centrální US** = **centralus**.

12. Můžete také změnit další parametry v šabloně, pokud se rozhodnete, a jsou volitelné v závislosti na vašich požadavcích:

    * **Sku** - Můžete změnit sku veřejné IP v konfiguraci ze standardního nebo základní ho standardu změnou vlastnosti **sku** > **name** v souboru **template.json:**

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

        Další informace o rozdílech mezi základními a standardními veřejnými ip soubory sku naleznete v [tématu Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Metoda přidělení veřejné IP** adresy a **časový limit nečinnosti** – obě tyto možnosti v šabloně můžete změnit změnou vlastnosti **publicIPAllocationMethod** z **dynamické** na **statickou** nebo **statickou** na **dynamickou**. Časový limit nečinnosti lze změnit změnou vlastnosti **idleTimeoutInMinutes** na požadovanou částku.  Výchozí hodnota je **4**:

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

        Další informace o metodách přidělení a hodnotách časového limitu nečinnosti naleznete v [tématu Vytvoření, změna nebo odstranění veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. V online editoru klikněte na **Uložit.**

14. Kliknutím na**předplatné** **BASICS** > vyberte předplatné, ve kterém bude nasazena cílová veřejná IP adresa.

15. Kliknutím na**skupinu prostředků** **BASICS** > zvolte skupinu prostředků, do které bude nasazena cílová veřejná IP adresa.  Chcete-li vytvořit novou skupinu prostředků pro cílovou veřejnou IP adresu, můžete klepnout na tlačítko **Vytvořit nový.**  Ujistěte se, že název není stejný jako zdrojová skupina prostředků existující zdrojové veřejné IP adresy.

16. Ověřte, zda je**umístění** **BASICS** > nastaveno na cílové umístění, kde chcete nasadit veřejnou IP adresu.

17. V části **NASTAVENÍ** ověřte, zda název odpovídá názvu, který jste zadali do výše uvedeného editoru parametrů.

18. Zaškrtněte políčko **v**podmínkách .

19. Kliknutím na tlačítko **Koupit** nasadíte cílovou veřejnou IP adresu.

## <a name="discard"></a>Zahodit

Pokud chcete zahodit cílovou veřejnou IP adresu, odstraňte skupinu prostředků, která obsahuje cílovou veřejnou IP adresu.  Chcete-li tak učinit, vyberte skupinu prostředků z řídicího panelu na portálu a v horní části stránky přehledu vyberte **Odstranit.**

## <a name="clean-up"></a>Vyčištění

Chcete-li potvrdit změny a dokončit přesun veřejné IP adresy, odstraňte zdrojovou veřejnou IP adresu nebo skupinu prostředků. Chcete-li tak učinit, vyberte veřejnou IP adresu nebo skupinu prostředků z řídicího panelu na portálu a v horní části každé stránky vyberte **Odstranit.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli veřejnou IP adresu Azure z jedné oblasti do druhé a vyčistili zdrojové prostředky.  Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)

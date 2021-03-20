---
title: Přesuňte konfiguraci veřejné IP adresy Azure do jiné oblasti Azure Azure Portal
description: Pomocí šablony můžete přesunout konfiguraci veřejné IP adresy Azure z jedné oblasti Azure do jiné pomocí Azure Portal.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: b00fca8cf39bc44e0e53a112a332e6f6c5f0194e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98218599"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Přesuňte konfiguraci veřejné IP adresy Azure do jiné oblasti pomocí Azure Portal

Existují různé scénáře, ve kterých byste chtěli přesunout existující konfigurace veřejné IP adresy Azure z jedné oblasti do druhé. Například můžete chtít vytvořit veřejnou IP adresu se stejnou konfigurací a skladovou jednotkou pro testování. V rámci plánování zotavení po havárii možná budete chtít přesunout konfiguraci veřejné IP adresy do jiné oblasti.

**Veřejné IP adresy Azure jsou specifické pro oblast a nejde je přesunout z jedné oblasti do druhé.** Můžete ale použít šablonu Azure Resource Manager k exportu stávající konfigurace veřejné IP adresy.  Potom můžete prostředek připravit v jiné oblasti tak, že do šablony vyexportujete veřejnou IP adresu, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti.  Další informace o Správce prostředků a šablonách najdete v tématu [rychlý Start: vytvoření a nasazení Azure Resource Manager šablon pomocí Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Předpoklady

- Ujistěte se, že je veřejná IP adresa Azure v oblasti Azure, ze které chcete přejít.

- Veřejné IP adresy Azure se nedají přesouvat mezi oblastmi.  Novou veřejnou IP adresu budete muset přidružit k prostředkům v cílové oblasti.

- K exportu konfigurace veřejné IP adresy a nasazení šablony pro vytvoření veřejné IP adresy v jiné oblasti budete potřebovat roli Přispěvatel sítě nebo vyšší.

- Identifikujte rozložení zdrojové sítě a všechny prostředky, které aktuálně používáte. Toto rozložení zahrnuje, ale není omezené na nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě (skupin zabezpečení sítě) a virtuální sítě.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet veřejné IP adresy v cílové oblasti, která se používá. O povolení požadované kvóty požádejte podporu.

- Ujistěte se, že vaše předplatné má dostatek prostředků na podporu Přidání veřejných IP adres pro tento proces.  Viz [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Příprava a přesun
Následující kroky ukazují, jak připravit veřejnou IP adresu pro přesunutí konfigurace pomocí šablony Správce prostředků a přesunout konfiguraci veřejné IP adresy do cílové oblasti pomocí Azure Portal.

### <a name="export-the-template-and-deploy-from-a-script"></a>Export šablony a nasazení ze skriptu

1. Přihlaste [](https://portal.azure.com)se ke  >  **skupinám prostředků** Azure Portal.
2. Vyhledejte skupinu prostředků, která obsahuje zdrojovou veřejnou IP adresu, a klikněte na ni.
3. Vyberte > **Nastavení**  >  **Exportovat šablonu**.
4. V okně **Exportovat šablonu** vyberte **nasadit** .
5. Kliknutím na **šablonu**  >  **Upravit parametry** otevřete **parameters.js** v souboru v online editoru.
8. Pokud chcete upravit parametr názvu veřejné IP adresy, změňte vlastnost v části **parametry**  >  **hodnota** ze zdrojového názvu veřejné IP adresy na název cílové veřejné IP adresy, ujistěte se, že je název v uvozovkách:

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
8.  V Editoru klikněte na **Uložit** .

9.  Kliknutím na **šablonu**  >  **Upravit šablonu** otevřete **template.js** v souboru v online editoru.

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

11. Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer, **střed USA**  =  **centralus**.

12. Můžete také změnit jiné parametry v šabloně, pokud zvolíte možnost a jsou nepovinné v závislosti na vašich požadavcích:

    * **SKU** – vlastnost název SKU pro veřejnou IP adresu můžete změnit v konfiguraci z úrovně Standard na Basic nebo Basic na standard, a to změnou   >  vlastnosti **název** SKU v **template.js** souboru:

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

        Další informace o rozdílech mezi veřejnými IP adresami Basic a Standard SKU najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](./virtual-network-public-ip-address.md):

    * **Metoda přidělování veřejných IP adres** a **časový limit nečinnosti** – obě tyto možnosti v šabloně můžete změnit tak, že změníte vlastnost **publicIPAllocationMethod** z **dynamické** na **statickou** nebo **statickou** na **dynamickou**. Časový limit nečinnosti lze změnit změnou vlastnosti **idleTimeoutInMinutes** na požadovanou hodnotu.  Výchozí hodnota je **4**:

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

        Další informace o metodách přidělování a hodnotách časového limitu nečinnosti najdete v tématu [Vytvoření, změna nebo odstranění veřejné IP adresy](./virtual-network-public-ip-address.md).


13. V online Editoru klikněte na **Uložit** .

14. Klikněte na **základy**  >  **předplatné** a vyberte předplatné, ve kterém se nasadí cílová veřejná IP adresa.

15. Klikněte na **základy**  >  **Skupina prostředků** a vyberte skupinu prostředků, do které se bude nasadit cílová veřejná IP adresa.  Kliknutím na **vytvořit novou** můžete vytvořit novou skupinu prostředků pro cílovou veřejnou IP adresu.  Ujistěte se, že název není stejný jako zdrojová skupina prostředků stávající zdrojové veřejné IP adresy.

16. Ověření **základních**  >  **umístění** je nastaveno na cílové umístění, ve kterém chcete nasadit veřejnou IP adresu.

17. V části **Nastavení** ověřte, že se název shoduje s názvem, který jste zadali v editoru parametrů výše.

18. Zaškrtněte políčko v části **podmínky a ujednání**.

19. Kliknutím na tlačítko **koupit** nasadíte cílovou veřejnou IP adresu.

## <a name="discard"></a>Zahodit

Pokud chcete zrušit cílovou veřejnou IP adresu, odstraňte skupinu prostředků, která obsahuje cílovou veřejnou IP adresu.  Provedete to tak, že vyberete skupinu prostředků z řídicího panelu na portálu a v horní části stránky přehled vyberete **Odstranit** .

## <a name="clean-up"></a>Vyčištění

Pokud chcete potvrdit změny a dokončit přesun veřejné IP adresy, odstraňte zdrojovou veřejnou IP adresu nebo skupinu prostředků. Provedete to tak, že na portálu vyberete veřejnou IP adresu nebo skupinu prostředků z řídicího panelu a v horní části každé stránky vyberete **Odstranit** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli veřejnou IP adresu Azure z jedné oblasti na jinou a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesun virtuálních počítačů Azure do jiné oblasti](../site-recovery/azure-to-azure-tutorial-migrate.md)
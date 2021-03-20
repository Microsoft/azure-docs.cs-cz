---
title: Přesunout Azure Event Grid obory názvů partnerů do jiné oblasti
description: V tomto článku se dozvíte, jak přesunout Azure Event Grid obory názvů partnerů z jedné oblasti do jiné oblasti.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89084545"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Přesunout Azure Event Grid obory názvů partnerů do jiné oblasti
Prostředky můžete chtít přesunout do jiné oblasti z řady důvodů. Například pro využití nové oblasti Azure, abyste splnili požadavky na interní zásady a požadavky zásad správného řízení nebo v reakci na požadavky na plánování kapacity. 

Tady je postup vysoké úrovně, který je popsaný v tomto článku: 

- **Exportujte prostředek oboru názvů partnera** do šablony Azure Resource Manager. Odstraňte definice prostředků kanálu událostí v šabloně. Kanál událostí může mít odkaz na ID Azure Resource Manager partnerského tématu, které vlastní zákazník. Proto se nedají vytvořit pomocí šablony v cílové oblasti.  
- **Pomocí šablony nasaďte partnerský obor názvů** do cílové oblasti. Pak vytvořte kanály událostí v novém oboru názvů partnera v cílové oblasti. 
- Pokud chcete **Dokončit přesunutí**, odstraňte obor názvů partnera ze zdrojové oblasti. 

    > [!NOTE]
    > - Export **témat pro partnery** do šablony Azure Resource Manager není podporován, protože zákazníci nemůžou vytvořit partnerské téma přímo. 
    > - **Registrace partnerů** jsou globální prostředky (nevázané na žádnou konkrétní oblast), takže jejich přesunutí z jedné oblasti do jiné oblasti není možné. 

## <a name="prerequisites"></a>Předpoklady
- Ujistěte se, že je služba Event Grid v cílové oblasti dostupná. Zobrazit [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Příprava
Začněte tím, že vyexportujete šablonu Správce prostředků pro obor názvů partnera. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na panelu hledání v horní části zadejte **Event Grid obory názvů partnerů** a v seznamu výsledků vyberte **Event Grid obory názvů partnerů** . 
3. Vyberte **obor názvů partnera** , který chcete exportovat do šablony Správce prostředků. 
4. Na stránce **obor názvů partnera Event Grid** v nabídce **Nastavení** v nabídce vlevo vyberte **Exportovat šablonu** a pak na panelu nástrojů vyberte **Stáhnout** . 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="Exportovat šablonu – > stáhnout" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. Vyhledejte soubor **. zip** , který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru. Tento soubor zip obsahuje šablony a parametry soubory JSON. 
1. Otevřete **template.js** v editoru podle svého výběru. 
8. Aktualizace `location` pro prostředek **tématu** do cílové oblasti nebo umístění. Pokud chcete získat kódy umístění, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti bez mezer, například `West US` je rovno `westus` .

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **Uložte** šablonu. 

## <a name="recreate"></a>Znovu vytvořit 
Nasaďte šablonu pro vytvoření oboru názvů partnera v cílové oblasti. 

1. V Azure Portal vyberte **vytvořit prostředek**.
2. V **části Hledat na Marketplace** zadejte **šablonu Deployment** a potom stiskněte **ENTER**.
3. Vyberte **template Deployment**.
4. Vyberte **Vytvořit**.
5. **V editoru vyberte vytvořit vlastní šablonu**.
6. Vyberte **načíst soubor** a potom podle pokynů načtěte **template.js** do souboru, který jste stáhli v poslední části.
7. Vyberte **Uložit** a šablonu uložte. 
8. Na stránce **vlastní nasazení** proveďte tyto kroky: 
    1. Vyberte **předplatné** Azure. 
    1. Vyberte existující **skupinu prostředků** v cílové oblasti nebo ji vytvořte. 
    1. V poli **umístění** vyberte cílovou oblast. Pokud jste vybrali existující skupinu prostředků, toto nastavení je jen pro čtení. 
    1. Jako **název oboru názvů partnera** zadejte název nového oboru názvů partnera. 
    1. Pro externí ID registrace partnera zadejte ID prostředku registrace partnera v následujícím formátu: `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` .
    1. Zaškrtněte políčko Souhlasím **s podmínkami a ujednáními uvedenými nahoře** .     
    1. Vyberte **zkontrolovat + vytvořit** a spusťte proces nasazení. 
    1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a vyberte **vytvořit**. 

## <a name="discard-or-clean-up"></a>Zahození nebo vyčištění
Pokud chcete dokončit přesunutí, odstraňte obor názvů partnera ve zdrojové oblasti.  

Pokud chcete začít znovu, odstraňte obor názvů partnera v cílové oblasti a opakujte kroky v částech [Příprava](#prepare) a [opětovné vytvoření](#recreate) v tomto článku.

Postup odstranění oboru názvů partnera pomocí Azure Portal:

1. Do okna Hledat v horní části Azure Portal zadejte **Event Grid obory názvů partnerů** a z výsledků hledání vyberte **Event Grid obory názvů partnerů** . 
2. Vyberte obor názvů partnera, který chcete odstranit, a vyberte **Odstranit** z panelu nástrojů. 
3. **Potvrďte** odstranění a odstraňte obor názvů partnera. 

## <a name="next-steps"></a>Další kroky
Zjistili jste, jak přesunout Event Grid partnerský obor názvů z jedné oblasti do jiné oblasti. Přečtěte si následující články pro přesun systémových témat, vlastních témat a domén v různých oblastech.

- [Přesuňte systémová témata mezi oblastmi](move-system-topics-across-regions.md). 
- [Přesuňte vlastní témata napříč oblastmi](move-custom-topics-across-regions.md). 
- [Přesune domény mezi oblasti](move-domains-across-regions.md).

Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete v následujícím článku: [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).
---
title: Přesunutí domén Azure Event Grid do jiné oblasti
description: V tomto článku se dozvíte, jak přesunout Azure Event Grid domény z jedné oblasti do jiné oblasti.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89084548"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Přesunutí domén Azure Event Grid do jiné oblasti
Prostředky můžete chtít přesunout do jiné oblasti z řady důvodů. Například pro využití nové oblasti Azure, abyste splnili požadavky na interní zásady a požadavky zásad správného řízení nebo v reakci na požadavky na plánování kapacity. 

Tady je postup vysoké úrovně, který je popsaný v tomto článku: 

- **Exportujte prostředek domény** do šablony Azure Resource Manager. 

    > [!IMPORTANT]
    > Prostředek domény a témata v doméně jsou exportovány do šablony. Předplatná pro doménová témata nejsou exportována. 
- **Použijte šablonu k nasazení domény** do cílové oblasti. 
- V cílové oblasti **můžete ručně vytvořit odběry pro témata v doméně** . Při exportu domény do šablony v aktuální oblasti nejsou exportována předplatná pro témata domény. Proto je vytvořte po vytvoření témat domény a domény v cílové oblasti. 
- **Ověřte nasazení**. Odešle událost do tématu domény v doméně a ověří, zda je vyvolána obslužná rutina události přidružená k předplatnému. 
- Chcete-li **Dokončit přesunutí**, odstraňte doménu ze zdrojové oblasti. 

## <a name="prerequisites"></a>Požadavky
- Ujistěte se, že je služba Event Grid v cílové oblasti dostupná. Zobrazit [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Příprava
Začněte tím, že vyexportujete šablonu Správce prostředků pro doménu. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na panelu hledání zadejte **Event Grid domény** a v seznamu výsledků vyberte **Event Grid domény** . 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Vyhledat a vybrat Event Grid domény":::
3. Vyberte **doménu** , kterou chcete exportovat do šablony Správce prostředků. 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="Vyberte doménu.":::   
4. Na stránce **Event Grid doména** vyberte v části **Nastavení** v nabídce vlevo položku **Exportovat šablonu** a pak na panelu nástrojů vyberte **Stáhnout** . 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="Exportovat šablonu – > stáhnout" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > Témata týkající se domény a domény se exportují. Předplatná pro témata domény nejsou exportována. Proto je třeba vytvořit odběry pro témata domény po přesunutí témat domény. 
5. Vyhledejte soubor **. zip** , který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru. Tento soubor zip obsahuje šablony a parametry soubory JSON. 
1. Otevřete **template.js** v editoru podle svého výběru. 
8. Aktualizujte `location` prostředek **domény** do cílové oblasti nebo umístění. Pokud chcete získat kódy umístění, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti bez mezer, například `West US` je rovno `westus` .

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **Uložte** šablonu. 

## <a name="recreate"></a>Znovu vytvořit 
Nasaďte šablonu pro vytvoření témat domény a domény v cílové oblasti. 

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
    1. V poli **oblast** vyberte cílovou oblast. Pokud jste vybrali existující skupinu prostředků, toto nastavení je jen pro čtení. 
    1. Pro **název domény** zadejte nový název domény. 
    1. Vyberte **Zkontrolovat a vytvořit**. 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="Nasazení šablony":::        
    1. Po úspěšném ověření šablony vyberte **vytvořit** v dolní části stránky a nasaďte prostředek. 
    1. Po úspěšném nasazení vyberte **Přejít do skupiny prostředků** a přejděte na stránku skupiny prostředků. Potvrďte, že je ve skupině prostředků doména. Vyberte doménu. Ověřte, že doména obsahuje témata. 

## <a name="discard-or-clean-up"></a>Zahození nebo vyčištění
Chcete-li dokončit přesunutí, odstraňte doménu ve zdrojové oblasti.  

Pokud chcete začít znovu, odstraňte doménu v cílové oblasti a opakujte kroky v částech [Příprava](#prepare) a [opětovné vytvoření](#recreate) v tomto článku.

Odstranění domény pomocí Azure Portal:

1. V okně hledání v horní části Azure Portal zadejte **Event Grid domény** a vyberte **Event Grid domény** z výsledků hledání. 
2. Vyberte doménu, kterou chcete odstranit, a vyberte **Odstranit** z panelu nástrojů. 
3. Na stránce potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.  

Odstranění skupiny prostředků, která obsahuje doménu, pomocí Azure Portal:

1. V okně hledání v horní části Azure Portal zadejte **skupiny prostředků** a z výsledků hledání vyberte **skupiny prostředků** . 
2. Vyberte skupinu prostředků, kterou chcete odstranit, a vyberte **Odstranit** z panelu nástrojů. 
3. Na stránce potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.  

## <a name="next-steps"></a>Další kroky
Zjistili jste, jak přesunout Event Grid domény z jedné oblasti do jiné oblasti. Přečtěte si následující články pro přesun systémových témat, vlastní témata a obory názvů partnerů napříč oblastmi.

- [Přesuňte systémová témata mezi oblastmi](move-system-topics-across-regions.md). 
- [Přesuňte vlastní témata napříč oblastmi](move-custom-topics-across-regions.md). 
- [Přesuňte obory názvů partnerů mezi oblasti](move-partner-namespaces-across-regions.md).

Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete v následujícím článku: [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).
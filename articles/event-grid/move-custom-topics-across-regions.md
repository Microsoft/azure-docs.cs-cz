---
title: Přesunout Azure Event Grid vlastní témata do jiné oblasti
description: V tomto článku se dozvíte, jak přesunout Azure Event Grid vlastní témata z jedné oblasti do jiné oblasti.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89145331"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Přesunout Azure Event Grid vlastní témata do jiné oblasti
Prostředky můžete chtít přesunout do jiné oblasti z řady důvodů. Například pro využití nové oblasti Azure, abyste splnili požadavky na interní zásady a požadavky zásad správného řízení nebo v reakci na požadavky na plánování kapacity. 

Tady je postup vysoké úrovně, který je popsaný v tomto článku: 

- **Exportujte prostředek vlastního tématu** do šablony Azure Resource Manager. 

    > [!IMPORTANT]
    > Do šablony je exportováno pouze vlastní téma. Žádná předplatná pro téma nejsou exportována.
- **Pomocí šablony můžete nasadit vlastní téma** do cílové oblasti. 
- K **ručnímu vytvoření odběrů** v cílové oblasti. Když jste vlastní téma exportovali do šablony v aktuální oblasti, bude exportováno pouze téma. Odběry nejsou zahrnuty v šabloně, proto je vytvořte ručně po vytvoření vlastního tématu v cílové oblasti. 
- **Ověřte nasazení**. Ověřte, zda je vlastní téma vytvořeno v cílové oblasti. 
- Chcete-li **Dokončit přesunutí**, odstraňte vlastní téma ze zdrojové oblasti. 

## <a name="prerequisites"></a>Požadavky
- Dokončete [rychlé zprovoznění: směrování vlastních událostí do webového koncového bodu](custom-event-quickstart-portal.md) ve zdrojové oblasti. Proveďte tento krok, abyste mohli testovat kroky v tomto článku. 
- Ujistěte se, že je služba Event Grid v cílové oblasti dostupná. Zobrazit [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Příprava
Začněte tím, že vyexportujete šablonu Správce prostředků pro vlastní téma. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na panelu hledání zadejte **Event Grid témata** a v seznamu výsledků vyberte **Event Grid témata** . 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Vyhledat a vybrat Event Grid témata":::
3. Vyberte **téma** , které chcete exportovat do šablony Správce prostředků. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Výběr vlastního tématu":::   
4. Na stránce **Event Grid téma** vyberte v části **Nastavení** v nabídce vlevo položku **Exportovat šablonu** a pak na panelu nástrojů vyberte **Stáhnout** . 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Exportovat šablonu – > stáhnout":::   

    > [!IMPORTANT]
    > Do šablony je exportováno pouze téma. Předplatná pro téma nejsou exportována. Proto je třeba vytvořit odběry pro téma po přesunutí tématu do cílové oblasti. 
5. Vyhledejte soubor **. zip** , který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru. Tento soubor zip obsahuje šablony a parametry soubory JSON. 
1. Otevřete **template.js** v editoru podle svého výběru. 
8. Aktualizace `location` pro prostředek **tématu** do cílové oblasti nebo umístění. Pokud chcete získat kódy umístění, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti bez mezer, například `West US` je rovno `westus` .

    ```json
    "type": "Microsoft.EventGrid/topics",
    "apiVersion": "2020-06-01",
    "name": "[parameters('topics_mytopic0130_name')]",
    "location": "westus"
    ```
1. **Uložte** šablonu. 

## <a name="recreate"></a>Znovu vytvořit 
Nasaďte šablonu pro vytvoření vlastního tématu v cílové oblasti. 

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
    1. Pro **název tématu** zadejte nový název tématu. 
    1. V dolní části stránky vyberte **zkontrolovat + vytvořit** . 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Vlastní nasazení":::
    1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a vyberte **vytvořit**. 

## <a name="verify"></a>Ověření

1. Po úspěšném nasazení vyberte **Přejít k prostředku**. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Přejít k prostředku":::
1. Potvrďte, že se na vlastním tématu zobrazí stránka **Event Grid téma** .   
1. Pokud chcete odesílat události do tématu, postupujte podle kroků v tématu [Směrování vlastních událostí do webového koncového bodu](custom-event-quickstart-portal.md#send-an-event-to-your-topic) . Ověřte, zda je vyvolána obslužná rutina události Webhooku. 

## <a name="discard-or-clean-up"></a>Zahození nebo vyčištění
Chcete-li dokončit přesunutí, odstraňte vlastní téma ve zdrojové oblasti.  

Pokud chcete začít znovu, odstraňte téma v cílové oblasti a opakujte kroky v částech [Příprava](#prepare) a [opětovné vytvoření](#recreate) v tomto článku.

Postup odstranění vlastního tématu pomocí Azure Portal:

1. V okně hledání v horní části Azure Portal zadejte **Event Grid témata** a vyberte **Event Grid témata** z výsledků hledání. 
2. Vyberte téma, které chcete odstranit, a vyberte **Odstranit** z panelu nástrojů. 
3. Na stránce potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.  

Pokud chcete odstranit skupinu prostředků, která obsahuje vlastní téma, pomocí Azure Portal:

1. V okně hledání v horní části Azure Portal zadejte **skupiny prostředků** a z výsledků hledání vyberte **skupiny prostředků** . 
2. Vyberte skupinu prostředků, kterou chcete odstranit, a vyberte **Odstranit** z panelu nástrojů. 
3. Na stránce potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.  

## <a name="next-steps"></a>Další kroky
Zjistili jste, jak přesunout Event Grid vlastní téma z jedné oblasti do jiné oblasti. Přečtěte si následující články pro přesun systémových témat, domén a oborů názvů partnerů napříč oblastmi.

- [Přesuňte systémová témata mezi oblastmi](move-system-topics-across-regions.md). 
- [Přesune domény mezi oblasti](move-domains-across-regions.md). 
- [Přesuňte obory názvů partnerů mezi oblasti](move-partner-namespaces-across-regions.md).

Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete v následujícím článku: [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).
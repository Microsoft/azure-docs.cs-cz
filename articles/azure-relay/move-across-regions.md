---
title: Přesunutí oboru názvů Azure Relay do jiné oblasti
description: V tomto článku se dozvíte, jak přesunout Azure Relay obor názvů z aktuální oblasti do jiné oblasti.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89463608"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Přesunutí oboru názvů Azure Relay do jiné oblasti
V tomto článku se dozvíte, jak přesunout Azure Relay obor názvů z jedné oblasti do jiné oblasti. Tady je postup vysoké úrovně:

1. **Exportujte** obor názvů přenosu do šablony Azure Resource Manager.
1. **Aktualizujte umístění (oblast)** pro prostředky v šabloně. Odstraňte také všechna **Dynamická** přenosová rozhraní WCF ze šablony. 

    Přenosy WCF mají dva režimy. V prvním režimu je přenos WCF explicitně vytvořen pomocí šablony Azure Portal nebo Azure Resource Manager. Na stránce přenosů **WCF** v Azure Portal uvidíte vlastnost **Dynamic** v tomto režimu nastavenou na **hodnotu false** pro Relay. 

    V druhém režimu se přenos WCF generuje automaticky, když se k dané adrese koncového bodu připojí naslouchací proces (Server). Pokud je naslouchací proces připojen ke službě Relay, zobrazí se seznam přenosů WCF v Azure Portal. Pro přenos v tomto režimu je vlastnost- **Dynamic** nastavena na **hodnotu true** , protože je dynamicky generována. Dynamický přenos WCF nezmizí, když se naslouchací proces odpojí. 
1. **Nasazení** prostředků pomocí šablony do cílové oblasti.

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že je služba Azure Relay v cílové oblasti dostupná. Zobrazit [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all). 
 
## <a name="prepare"></a>Příprava
Začněte tím, že vyexportujete šablonu Správce prostředků. Tato šablona obsahuje nastavení, která popisují váš obor názvů Azure Relay.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny prostředky** a pak vyberte svůj obor názvů Azure Relay.
3. V části **Nastavení** v nabídce vlevo vyberte **Exportovat šablonu** .
4. Na stránce **Exportovat šablonu** klikněte na **Stáhnout** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Stažení šablony Správce prostředků":::
5. Vyhledejte soubor. zip, který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru. Tento soubor zip obsahuje soubory JSON Template a Parameters. 
1. Otevřete **template.js** v souboru z extrahované složky v editoru dle vašeho výběru.
1. Vyhledejte `location` a nahraďte hodnotu vlastnosti novým názvem oblasti. Pokud chcete získat kódy umístění, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti bez mezer, například `West US` je rovno `westus` .
1. Odeberte definice **dynamických prostředků WCF Relay** (typ: `Microsoft.Relay/namespaces/WcfRelays` ). Dynamické přenosy WCF jsou ty, které mají vlastnost **Dynamic** nastavenou na **hodnotu true** na stránce **Relay** . V následujícím příkladu je **EchoService** dynamický přenos přes WCF a jeho definice by se měla odebrat ze šablony. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Dynamické Relay":::

## <a name="move"></a>Přesunout
Nasaďte šablonu pro vytvoření oboru názvů přenosu v cílové oblasti. 

1. V Azure Portal vyberte **vytvořit prostředek**.
2. V **části Hledat na Marketplace** zadejte do pole hledaný text **šablonu nasazení** , vyberte možnost **template Deployment (nasadit pomocí vlastních šablon)** a potom stiskněte klávesu **ENTER**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Nasazení nové šablony":::    
1. Na stránce **template Deployment** vyberte **vytvořit**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Nasazení nové šablony – tlačítko vytvořit":::        
1. Na stránce **vlastní nasazení** vyberte **v editoru vytvořit vlastní šablonu**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Vytvoření vlastní šablony v editoru – odkaz":::            
1. Na stránce **Upravit šablonu** vyberte **načíst soubor** na panelu nástrojů a potom podle pokynů načtěte **template.js** do souboru, který jste stáhli v poslední části.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Vybrat šablonu":::                
1. Vyberte **Uložit** a šablonu uložte. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Uložení šablony":::                    
1. Na stránce **vlastní nasazení** proveďte tyto kroky: 
    1. Vyberte **předplatné** Azure. 
    2. Vyberte existující **skupinu prostředků** nebo ji vytvořte. 
    3. Vyberte cílové **umístění** nebo oblast. Pokud jste vybrali existující skupinu prostředků, toto nastavení je jen pro čtení. 
    4. Zadejte nový **název oboru názvů**.
    1. Vyberte **Zkontrolovat a vytvořit**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Nasazení šablony Správce prostředků":::
    1. Na stránce **Revize + vytvořit** vyberte **vytvořit** v dolní části stránky. 
    
## <a name="verify"></a>Ověření
1. Po úspěšném nasazení vyberte **Přejít do skupiny prostředků**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Přejít na odkaz skupiny prostředků":::    
1. Na stránce **Skupina prostředků** vyberte obor názvů Azure Relay. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Vybrat Azure Relay obor názvů":::    
1. Na stránce **Azure Relay obor názvů** vyberte v nabídce vlevo **Hybrid Connections** nebo předávání **WCF** , abyste ověřili, že se vytvořila hybridní připojení a přenosy WCF. Pokud jste zapomněli odstranit definice pro dynamickou technologii WCF Relay před importem šablony, odstraňte je na stránce přenosů **WCF** . Dynamické přenosy WCF se vytvoří automaticky, když se klienti připojí k oboru názvů Relay. 

## <a name="discard-or-clean-up"></a>Zahození nebo vyčištění
Pokud po nasazení chcete začít znovu, můžete **cílový obor názvů Azure Relay** odstranit a zopakovat postup, který je popsaný v části [Příprava](#prepare) a [Přesun](#move) v tomto článku.

Chcete-li potvrdit změny a dokončit přesunutí oboru názvů, odstraňte **obor názvů Azure Relay** ve zdrojové oblasti. 

Odstranění oboru názvů Azure Relay (zdroj nebo cíl) pomocí Azure Portal:

1. Do okna Hledat v horní části Azure Portal zadejte text **Relay** a ve výsledcích hledání vyberte **Relay** ze **služeb** . V seznamu se zobrazí všechny obory názvů Azure Relay.
2. Vyberte cílový obor názvů, který chcete odstranit, a otevřete stránku **Relay** . 
1. Na stránce **Relay** vyberte z panelu nástrojů **Odstranit** . 

    ![Odstranit obor názvů – tlačítko](./media/move-across-regions/delete-namespace-button.png)
3. Na stránce **Odstranit obor názvů** zadejte název oboru názvů Azure Relay pro potvrzení odstranění a pak vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste přesunuli Azure Relay obor názvů z jedné oblasti do jiné oblasti. Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:

- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)

---
title: Přesunutí oboru názvů Service Bus do jiné oblasti | Microsoft Docs
description: V tomto článku se dozvíte, jak přesunout Azure Service Bus obor názvů z aktuální oblasti do jiné oblasti.
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88860893"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Přesunutí oboru názvů Azure Service Bus do jiné oblasti
Existují různé scénáře, ve kterých byste chtěli přesunout existující Service Bus obor názvů z jedné oblasti do druhé. Například můžete chtít vytvořit obor názvů se stejnou konfigurací pro testování. V rámci [Plánování zotavení po havárii](service-bus-geo-dr.md)možná budete chtít vytvořit také sekundární obor názvů v jiné oblasti.

Tady je postup vysoké úrovně:

1. Exportujte obor názvů Service Bus v aktuální oblasti do šablony Azure Resource Manager. 
1. Aktualizujte umístění prostředků v šabloně. Odstraňte také výchozí filtr předplatného ze šablony, protože nemůžete vytvořit výchozí pravidlo, které se automaticky vytvoří za vás. 
1. Pomocí šablony nasaďte obor názvů Service Bus do cílové oblasti. 
1. Ověřte nasazení, aby se zajistilo, že obor názvů, fronty, témata a odběry pro témata jsou vytvořené v cílové oblasti. 
1. Dokončete přesunutí odstraněním oboru názvů ze zdrojové oblasti po zpracování všech zpráv. 

## <a name="prerequisites"></a>Předpoklady
Ujistěte se, že Azure Service Bus a funkce, které váš účet používá, jsou podporované v cílové oblasti.
 
## <a name="prepare"></a>Příprava
Začněte tím, že vyexportujete šablonu Správce prostředků. Tato šablona obsahuje nastavení, která popisují váš obor názvů Service Bus.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny prostředky** a pak vyberte svůj obor názvů Service Bus.
3. Vyberte > **Nastavení**  >  **Exportovat šablonu**.
4. Na stránce **Exportovat šablonu** vyberte **Stáhnout** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Stažení šablony Správce prostředků":::
5. Vyhledejte soubor. zip, který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru. Tento soubor zip obsahuje soubory JSON Template a Parameters. 
1. Otevřete template.jsv souboru v extrahované složce. 
1. Vyhledejte `location` a nahraďte hodnotu vlastnosti novým názvem oblasti nebo umístění. Pokud chcete získat kódy umístění, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti bez mezer, například `West US` je rovno `westus` .
1. Odebrat definice prostředků typu: `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` . Nezapomeňte odebrat znak čárky ( `,` ) předcházející této části, aby byl kód JSON platný.  

    > [!NOTE]
    > Nemůžete vytvořit výchozí pravidlo pro odběr pomocí šablony Správce prostředků. Výchozí pravidlo se vytvoří automaticky při vytvoření odběru v cílové oblasti. 

## <a name="move"></a>Přesunout
Nasaďte šablonu pro vytvoření oboru názvů Service Bus v cílové oblasti. 

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
1. Na stránce **Skupina prostředků** vyberte obor názvů Service Bus. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Vybrat Service Bus obor názvů":::    
1. Na stránce **Service Bus obor názvů** ověřte, že se zobrazují fronty, témata a odběry ze zdrojové oblasti. 
    1. Ve spodní části pravého podokna se v oboru názvů zobrazí **fronty** .         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="Fronty v oboru názvů":::
    2. Chcete-li zobrazit témata v oboru názvů, přepněte na kartu **témata** .
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="Témata v oboru názvů":::
    3. Vyberte téma pro ověření předplatných. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="Odběry tématu":::      
    
    

## <a name="discard-or-clean-up"></a>Zahození nebo vyčištění
Pokud po nasazení chcete začít znovu, můžete **cílový obor názvů Service Bus** odstranit a postup opakovat postupem popsaným v části [Příprava](#prepare) a [Přesun](#move) v tomto článku.

Chcete-li potvrdit změny a dokončit přesun Service Bus oboru názvů, odstraňte **zdrojový obor názvů Service Bus**. Před odstraněním oboru názvů se ujistěte, že jsou všechny zprávy zpracovány. 

Chcete-li odstranit Service Bus obor názvů (zdroj nebo cíl) pomocí Azure Portal:

1. V okně hledání v horní části Azure Portal zadejte **Service Bus** a vyberte **Service Bus** z výsledků hledání. V seznamu se zobrazí Service Bus obory názvů.
2. Vyberte cílový obor názvů, který chcete odstranit, a vyberte **Odstranit** z panelu nástrojů. 

    ![Odstranit obor názvů – tlačítko](./media/move-across-regions/delete-namespace-button.png)
3. Na stránce **Odstranit prostředky** ověřte vybrané prostředky a potvrďte odstranění zadáním **Ano** a pak vyberte **Odstranit**. 

    Další možností je odstranit skupinu prostředků, která má obor názvů Service Bus. Na stránce **Skupina prostředků** vyberte na panelu nástrojů **Odstranit skupinu prostředků** a potvrďte odstranění. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli Azure Service Bus obor názvů z jedné oblasti na jiný a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:

- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)

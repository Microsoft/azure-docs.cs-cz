---
title: Přesunutí clusteru vyhrazeného pro Azure Event Hubs do jiné oblasti | Microsoft Docs
description: V tomto článku se dozvíte, jak přesunout vyhrazený cluster Azure Event Hubs z aktuální oblasti do jiné oblasti.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89380704"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Přesunutí clusteru vyhrazeného pro Azure Event Hubs do jiné oblasti
V tomto článku se dozvíte, jak exportovat šablonu Azure Resource Manager pro existující Event Hubs vyhrazený cluster a pak pomocí šablony vytvořit cluster se stejným nastavením konfigurace v jiné oblasti. 

Pokud máte další prostředky, jako jsou obory názvů a centra událostí ve skupině prostředků Azure, která obsahuje cluster Event Hubs, možná budete chtít exportovat šablonu na úrovni skupiny prostředků, aby se všechny související prostředky mohly přesunout do nové oblasti v jednom kroku. Kroky v tomto článku vám ukážou, jak exportovat **cluster Event Hubs** do šablony. Postup pro export **skupiny prostředků** do šablony je podobný. 

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že se vyhrazený cluster dá vytvořit v cílové oblasti. Nejjednodušší způsob, jak to zjistit, je použít Azure Portal k pokusu o [vytvoření Event Hubs vyhrazeného clusteru](event-hubs-dedicated-cluster-create-portal.md). Zobrazí se seznam oblastí, které jsou v daném okamžiku pro vytvoření clusteru podporovány. 

## <a name="prepare"></a>Příprava
Začněte tím, že vyexportujete šablonu Správce prostředků. Tato šablona obsahuje nastavení, která popisují váš Event Hubs vyhrazený cluster.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny prostředky** a pak vyberte svůj vyhrazený cluster Event Hubs.
3. Vyberte > **Nastavení**  >  **Exportovat šablonu**.
4. Na stránce **Exportovat šablonu** vyberte **Stáhnout** .

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Stažení šablony Správce prostředků" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. Vyhledejte soubor. zip, který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru.

   Tento soubor zip obsahuje soubory. JSON, které obsahují šablonu a skripty pro nasazení šablony.


## <a name="move"></a>Přesunout

Nasaďte šablonu k vytvoření Event Hubs vyhrazeného clusteru v cílové oblasti. 


1. V Azure Portal vyberte **vytvořit prostředek**.
2. V **části Hledat na Marketplace** zadejte **šablonu deployment** a vyberte **template Deployment (nasazení pomocí vlastních šablon)**.
5. **V editoru vyberte vytvořit vlastní šablonu**.
6. Vyberte **načíst soubor** a potom podle pokynů načtěte **template.js** do souboru, který jste stáhli v poslední části.
1. Aktualizujte hodnotu `location` vlastnosti tak, aby odkazovala na novou oblast. Pokud chcete získat kódy umístění, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Kód oblasti je název oblasti bez mezer, například `West US` je rovno `westus` .
1. Vyberte **Uložit** a šablonu uložte. 
1. Na stránce **vlastní nasazení** proveďte tyto kroky: 
    1. Vyberte **předplatné** Azure. 
    2. Vyberte existující **skupinu prostředků** nebo ji vytvořte. 
    3. Vyberte cílové **umístění** nebo oblast. Pokud jste vybrali existující skupinu prostředků, toto nastavení je jen pro čtení. 
    4. V části **Nastavení** proveďte následující kroky:    
        1. Zadejte nový **název clusteru**. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Nasazení šablony Správce prostředků":::
    5. V dolní části stránky vyberte **zkontrolovat + vytvořit** . 
    1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a pak vyberte **vytvořit**.  

## <a name="discard-or-clean-up"></a>Zahození nebo vyčištění
Pokud po nasazení chcete začít znovu, můžete **cílový Event Hubs vyhrazený cluster** odstranit a postup opakovat postupem popsaným v části [Příprava](#prepare) a [Přesun](#move) v tomto článku.

Pokud chcete potvrdit změny a dokončit přesun Event Hubsho clusteru, odstraňte **cluster Event Hubs** v původní oblasti. 

Postup odstranění clusteru Event Hubs (zdroje nebo cíle) pomocí Azure Portal:

1. V okně hledání v horní části Azure Portal zadejte **Event Hubs clustery** a vyberte **Event Hubs clustery** z výsledků hledání. V seznamu se zobrazí cluster Event Hubs.
2. Vyberte cluster, který chcete odstranit, a vyberte **Odstranit** z panelu nástrojů. 
3. Na stránce **Odstranit cluster** potvrďte odstranění zadáním **názvu clusteru** a pak vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak přesunout Event Hubs vyhrazený cluster z jedné oblasti do druhé. 

Pokyny k přesunu oboru názvů z jedné oblasti do jiné oblasti najdete v článku [přesunutí Event Hubs oborů názvů napříč](move-across-regions.md) oblastmi. 

Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:

- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesun virtuálních počítačů Azure do jiné oblasti](../site-recovery/azure-to-azure-tutorial-migrate.md)

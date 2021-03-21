---
title: Vytváření, zobrazování a Správa systémových témat v Azure Event Grid (portál)
description: V tomto článku se dozvíte, jak zobrazit existující systémové téma, vytváření Azure Event Grid systémových témat pomocí Azure Portal.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86115107"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>Vytváření, zobrazování a Správa systémových témat Event Grid v Azure Portal
V tomto článku se dozvíte, jak vytvořit a spravovat systémová témata pomocí Azure Portal. Přehled systémových témat najdete v tématu [Systémová témata](system-topics.md).

## <a name="create-a-system-topic"></a>Vytvořit systémové téma
Můžete vytvořit systémové téma pro prostředek Azure (účet úložiště, Event Hubs obor názvů atd.) dvěma způsoby:

- Použijte stránku **události** prostředku, například účet úložiště nebo obor názvů Event Hubs. Když použijete stránku **události** v Azure Portal k vytvoření odběru událostí pro událost vyvolanou zdrojem Azure (například účet Azure Storage), portál vytvoří systémové téma pro prostředek Azure a pak vytvoří odběr pro systémové téma. Pokud vytváříte odběr událostí v prostředku Azure poprvé, zadejte název systémového tématu. Od druhé výše se v režimu jen pro čtení zobrazí název tématu systému. Podrobné pokyny najdete v tématu [rychlý Start: směrování událostí služby Blob Storage do webového koncového bodu pomocí Azure Portal](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) .
- Použití stránky **témata Event Grid systému** . Následující postup slouží pro vytváření systémových témat pomocí stránky **Event Grid systému** . 

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Do vyhledávacího pole v horní části zadejte **Event Grid systémová témata** a potom stiskněte klávesu **ENTER**. 

    ![Vyhledat systémová témata](./media/create-view-manage-system-topics/search-system-topics.png)
3. Na stránce **Event Grid systémová témata** vyberte **+ Přidat** na panelu nástrojů.

    ![Přidat systémové téma – tlačítko panelu nástrojů](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. Na stránce **vytvořit Event Grid systému** proveďte následující kroky:
    1. Vyberte **typ tématu**. V následujícím příkladu je vybraná možnost **účty úložiště** . 
    2. Vyberte **předplatné Azure** , které má váš prostředek účtu úložiště. 
    3. Vyberte **skupinu prostředků** , která má účet úložiště. 
    4. Vyberte **účet úložiště**. 
    5. Zadejte **název** systémového tématu, které se má vytvořit. 
    
        > [!NOTE]
        > Pomocí tohoto názvu systémového tématu můžete vyhledat metriky a diagnostické protokoly.
    6. Vyberte **Zkontrolovat a vytvořit**.

        ![Vytvořit systémové téma](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. Zkontrolujte nastavení a vyberte **vytvořit**. 
        
        ![Zkontrolovat a vytvořit systémové téma](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. Po úspěšném nasazení vyberte **Přejít k prostředku** a zobrazí se stránka s **tématem Event Grid systému** pro téma, které jste vytvořili. 

        ![Stránka tématu systém](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>Zobrazit všechna systémová témata
Pomocí těchto kroků zobrazíte všechna existující Event Grid systémová témata. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Do vyhledávacího pole v horní části zadejte **Event Grid systémová témata** a potom stiskněte klávesu **ENTER**. 

    ![Vyhledat systémová témata](./media/create-view-manage-system-topics/search-system-topics.png)
3. Na stránce **témata týkající se Event Grid systému** uvidíte všechna systémová témata. 

    ![Seznam systémových témat](./media/create-view-manage-system-topics/list-system-topics.png)
4. V seznamu vyberte **téma systému** , ve kterém se zobrazí podrobnosti. 

    ![Podrobnosti o tématu systému](./media/create-view-manage-system-topics/system-topic-details.png)

    Na této stránce jsou uvedeny podrobnosti o tématu systému, například následující informace: 
    - Zdrojová. Název prostředku, na kterém se vytvořilo systémové téma
    - Typ zdroje Typ prostředku. Například: `Microsoft.Storage.StorageAccounts` , `Microsoft.EventHub.Namespaces` , `Microsoft.Resources.ResourceGroups` a tak dále.
    - Všechna předplatná vytvořená pro systémové téma.

    Tato stránka umožňuje operace, jako jsou následující:
    - Vytvořte předplatné události a na panelu nástrojů vyberte **+ odběr události** . 
    - Odstraní odběr události. Na panelu nástrojů vyberte **Odstranit** . 
    - Přidejte značky pro systémové téma. V nabídce vlevo vyberte **značky** a zadejte názvy značek a jejich hodnoty. 


## <a name="delete-a-system-topic"></a>Odstranit systémové téma
1. Podle pokynů v části [zobrazení systémových témat](#view-all-system-topics) můžete zobrazit všechna systémová témata a vybrat systémové téma, které chcete ze seznamu odstranit. 
2. Na stránce **Event Gridho systému** vyberte možnost **Odstranit** na panelu nástrojů. 

    ![Systémové téma – tlačítko Odstranit](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. Na stránce potvrzení vyberte **OK** a potvrďte odstranění. Odstraní téma System a také všechna předplatná událostí pro systémové téma.  

## <a name="create-an-event-subscription"></a>Vytvoření odběru událostí
1. Podle pokynů v části [zobrazení systémových témat](#view-all-system-topics) můžete zobrazit všechna systémová témata a vybrat systémové téma, které chcete ze seznamu odstranit. 
2. Na stránce **Event Grid systémové téma** vyberte **+ odběr události** z panelu nástrojů. 

    ![Téma systému – tlačítko Přidat odběr události](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. Potvrďte, že se automaticky vyplní **typ tématu**, **zdrojový prostředek** a **název tématu** . Zadejte název, vyberte **Typ koncového bodu** a zadejte **koncový bod**. Pak vyberte **vytvořit** a vytvořte odběr události. 

    ![Systémové téma – Vytvoření odběru událostí](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>Další kroky
V části [Systémová témata v Azure Event Grid](system-topics.md) najdete další informace o systémových tématech a typech témat, které podporuje Azure Event Grid. 

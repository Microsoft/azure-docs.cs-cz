---
title: Rychlý start Azure – vytvoření clusteru vyhrazená Služba Event Hubs pomocí webu Azure portal | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster služby Azure Event Hubs pomocí webu Azure portal.
services: event-hubs
documentationcenter: ''
author: xurui203
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/02/2019
ms.author: xurui
ms.openlocfilehash: 269ecca8683229a56d40dfacc354abbd7ce10762
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688583"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Rychlý start: Vytvořit cluster vyhrazená Služba Event Hubs pomocí webu Azure portal 
Clustery centra událostí nabízejí nasazení jednoho tenanta pro zákazníky s nejnáročnější požadavky na datových proudů. Tuto nabídku má zaručené smlouvy SLA 99,99 % a jsou dostupné jenom na naše Dedicated cenovou úroveň. [Clusteru služby Event Hubs](event-hubs-dedicated-overview.md) můžete příchozího přenosu dat milionů událostí za sekundu s garantovanou latencí kapacity a subsecond. Obory názvů a centra událostí v rámci clusteru zahrnují všechny funkce standardní nabídky a další, ale žádné neomezené příchozí. Vyhrazené nabídka zahrnuje také oblíbené [Event Hubs Capture](event-hubs-capture-overview.md) funkce bez dalších poplatků, abyste mohli automaticky služby batch a protokolu datových proudů [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) nebo [ Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md).

Vyhrazené clustery jsou zřízené a účtuje se podle **kapacitních jednotek (CUs)** , předběžně přidělit objem prostředků procesoru a paměti. Si můžete koupit 1, 2, 4, 8, 12, 16 nebo 20 kapacitní jednotky pro každý cluster. V tomto rychlém startu jsme se vás provede vytvořením 1 Kapacitní jednotka Event Hubs cluster na webu Azure portal.

> [!NOTE]
> Toto samoobslužné prostředí je aktuálně dostupná ve verzi preview na [webu Azure Portal](https://aka.ms/eventhubsclusterquickstart). Pokud máte nějaké dotazy o vyhrazenou nabídku, kontaktujte prosím [tým služby Event Hubs](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Požadavky
Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Účet Azure. Pokud ho nemáte, [si účet Zakupte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) předtím, než začnete. Tato funkce není podporována s bezplatným účtem Azure. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 s aktualizací 3 (verze 15.3, 26730.01) nebo novější.
- [NET Standard SDK](https://dotnet.microsoft.com/download) verze 2.0 nebo novější.
- [Vytvoří skupinu prostředků](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Vytvořit vyhrazenou clusteru služby Event Hubs
Cluster služby Event Hubs poskytuje jedinečné oboru kontejner, ve kterém můžete vytvořit jeden nebo více oborů názvů. V téhle verzi Preview prostředí samoobslužného portálu můžete vytvořit 1 Kapacitní jednotka clusterů ve vybraných oblastech. Pokud potřebujete cluster větší než 1 Kapacitní jednotka, můžete odeslat požadavek na podporu Azure vertikální navýšení kapacity clusteru po jeho vytvoření.

K vytvoření clusteru ve vaší skupině prostředků pomocí webu Azure portal, proveďte následující kroky:

1. Postupujte podle [tento odkaz](https://aka.ms/eventhubsclusterquickstart) vytvořit cluster na webu Azure portal. Naopak, vyberte **všechny služby** v levém navigačním podokně zadejte "Clustery centra událostí" na vyhledávacím panelu a vyberte "Clustery centra událostí" ze seznamu výsledků.
2. Na **vytvořit Cluster** proveďte následující:
    1. Zadejte **název pro cluster**. Systém okamžitě kontroluje, jestli je název dostupný.
    2. Vyberte **předplatné** ve kterém chcete cluster vytvořit.
    3. Vyberte **skupiny prostředků** ve kterém chcete cluster vytvořit.
    4. Vyberte **umístění** pro cluster. Pokud vaše upřednostňovaná oblast je zobrazena šedě, jsou dočasně vyčerpány kapacity a můžete odeslat [žádost o podporu](#submit-a-support-request) týmu služby Event Hubs.
    5. Vyberte **Další: Značky** tlačítko v dolní části stránky. Možná budete muset několik minut počkat, než systém úplně zřídí prostředky.

        ![Vytvořit Cluster centra událostí – základní informace o stránce](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na **značky** proveďte následující:
    1. Zadejte **název** a **hodnotu** značky, které chcete přidat. Tento krok je **volitelný**.  
    2. Vyberte **revize + vytvořit** tlačítko.

        ![Vytvořit Cluster centra událostí stránka – značky](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na **zkontrolovat a vytvořit** stránky, zkontrolujte podrobnosti a vyberte **vytvořit**. 

    ![Vytvořit Cluster centra událostí stránka – zkontrolovat a vytvořit stránku](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Vytvořit obor názvů a Centrum událostí v rámci clusteru

1. Vytvoření oboru názvů v rámci clusteru, na **Cluster centra událostí** stránky pro váš cluster, vyberte **+ Namespace** z hlavní nabídky.

    ![Stránka pro správu clusteru – obor názvů tlačítko Přidat](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na stránce vytvořit obor názvů proveďte následující kroky:
    1. Zadejte **název pro obor názvů**.  Systém zkontroluje, jestli je název k dispozici.
    2. Obor názvů zdědí tyto vlastnosti:
        1. ID předplatného
        2. Skupina prostředků
        3. Location
        4. Název clusteru
    3. Vyberte **vytvořit** k vytvoření oboru názvů. Nyní můžete spravovat cluster.  

        ![Vytvoření oboru názvů v clusteru stránky](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Po vytvoření vašeho oboru názvů můžete [vytvořit Centrum událostí](event-hubs-create.md#create-an-event-hub) by obvykle vytvoříte jednu v oboru názvů. 


## <a name="submit-a-support-request"></a>Odeslání žádosti o podporu

Pokud chcete změnit velikost vašeho clusteru po vytvoření nebo pokud není k dispozici upřednostňovaná oblast, odešlete prosím žádost o podporu pomocí následujících kroků:

1. V [webu Azure portal](https://portal.azure.com)vyberte **Nápověda a podpora** v levé nabídce.
2. Vyberte **+ nová žádost o podporu** z nabídky podpory.
3. Na stránce podpory postupujte podle těchto kroků:
    1. Pro **typ problému**vyberte **technické** z rozevíracího seznamu.
    2. V části **Předplatné** vyberte své předplatné.
    3. Pro **služby**vyberte **Moje služby**a pak vyberte **Event Hubs**.
    4. Pro **prostředků**, vyberte svůj cluster, pokud již existuje, v opačném případě vyberte **obecný dotaz/prostředek není k dispozici**.
    5. Pro **typ problému**vyberte **kvóty**.
    6. Pro **problém podtyp**, vyberte jednu z následujících hodnot z rozevíracího seznamu:
        1. Vyberte **žádost o vyhrazené SKU** vyžádat pro funkci tak, aby se nepodporuje ve vaší oblasti.
        2. Vyberte **požadavek na vertikálně navýšit kapacitu nebo škálování dolů vyhrazeném clusteru** Pokud chcete vertikálně navýšit kapacitu vertikálně zvýšit nebo snížit vyhrazeném clusteru. 
    7. Pro **subjektu**, popište problém.

        ![Stránka lístku podpory](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Odstranit vyhrazenou clusteru
 
1. Chcete-li odstranit cluster, vyberte **odstranit** z hlavní nabídky. Všimněte si, že cluster bude účtovat minimálně 4 hodiny využití po jeho vytvoření. 
2. Potvrzují se vaše přání cluster odstranit, zobrazí se zpráva.
3. Typ **název clusteru** a vyberte **odstranit** cluster odstranit.

    ![Odstranění clusteru stránky](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili cluster služby Event Hubs. Podrobné pokyny týkající se odesílání a příjem událostí z centra událostí a zaznamenání událostí do Azure storage nebo Azure Data Lake Store najdete v následujících kurzech:

- [Odesílání a příjmu událostí v rozhraní .NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Povolení funkce Event Hubs Capture pomocí webu Azure portal](event-hubs-capture-enable-through-portal.md)
- [Použití služby Azure Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

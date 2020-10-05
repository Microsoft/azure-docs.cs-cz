---
title: Vytvoření vyhrazeného clusteru Event Hubs pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster Azure Event Hubs pomocí Azure Portal.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 2759d1e25519b69311c369f3f58239cc0889a9a7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88927761"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Rychlý Start: vytvoření vyhrazeného clusteru Event Hubs pomocí Azure Portal 
Clustery Event Hubs nabízejí nasazení s jedním nájemcem pro zákazníky s nejnáročnějšími požadavky na streamování. Tato nabídka má garantovanou smlouvu SLA 99,99% a je dostupná jenom na naší vyhrazené cenové úrovni. [Cluster Event Hubs](event-hubs-dedicated-overview.md) může příchozí miliony událostí za sekundu se zaručenou kapacitou a latencí druhé sekundy. Obory názvů a centra událostí vytvořené v rámci clusteru obsahují všechny funkce standardní nabídky a další, ale bez omezení příchozího přenosu dat. Vyhrazená nabídka také zahrnuje oblíbenou funkci [Event Hubs Capture](event-hubs-capture-overview.md) bez dalších poplatků, což vám umožní automaticky dávkovat a zasílat datové proudy do [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) nebo [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md).

Vyhrazené clustery se zřídí a účtují podle **kapacitních jednotek (kapacitní jednotky)**, což je předem přidělené množství prostředků procesoru a paměti. Pro každý cluster můžete koupit 1, 2, 4, 8, 12, 16 nebo 20 kapacitní jednotky. V tomto rychlém startu Vás provedeme vytvořením Event Hubs clusteru 1 CU pomocí Azure Portal.

> [!NOTE]
> Toto samoobslužné prostředí je teď dostupné ve verzi Preview na webu [Azure Portal](https://aka.ms/eventhubsclusterquickstart). Pokud máte nějaké dotazy týkající se vyhrazené nabídky, obraťte se prosím na [tým Event Hubs](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Předpoklady
Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Účet Azure: Pokud ho ještě nemáte, [Zakupte si účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) ještě před tím, než začnete. Tato funkce není podporována s bezplatným účtem Azure. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (verze 15,3, 26730,01) nebo novější.
- [NET Standard SDK](https://dotnet.microsoft.com/download) verze 2.0 nebo novější.
- [Vytvořila se skupina prostředků](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Vytvoření clusteru Event Hubs úrovně Dedicated
Cluster Event Hubs poskytuje jedinečný obor kontejneru, ve kterém můžete vytvořit jeden nebo více oborů názvů. V této fázi Preview prostředí samoobslužného ovládání portálu můžete vytvořit 1 CU clustery ve vybraných oblastech. Pokud potřebujete cluster větší než 1, můžete odeslat žádost o podporu Azure pro horizontální navýšení kapacity clusteru po jeho vytvoření.

Pokud chcete ve skupině prostředků vytvořit cluster pomocí Azure Portal, dokončete následující kroky:

1. Pomocí [tohoto odkazu](https://aka.ms/eventhubsclusterquickstart) můžete vytvořit cluster na Azure Portal. Naopak v levém navigačním podokně vyberte **všechny služby** a pak na panelu hledání zadejte "Event Hubs clustery" a v seznamu výsledků vyberte Event Hubs clustery.
2. Na stránce **vytvořit cluster** nakonfigurujte následující nastavení:
    1. Zadejte **název clusteru**. Systém okamžitě kontroluje, jestli je název dostupný.
    2. Vyberte **předplatné** , ve kterém chcete cluster vytvořit.
    3. Vyberte **skupinu prostředků** , ve které chcete cluster vytvořit.
    4. Vyberte **umístění** pro cluster. Pokud je vaše upřednostňovaná oblast šedá, je dočasně mimo kapacitu a můžete odeslat [žádost o podporu](#submit-a-support-request) týmu Event Hubs.
    5. V dolní části stránky vyberte tlačítko **Další: značky** . Možná budete muset několik minut počkat, než systém úplně zřídí prostředky.

        ![Vytvoření stránky základy Event Hubs clusteru](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na stránce **značky** nakonfigurujte následující nastavení:
    1. Zadejte **název** a **hodnotu** značky, kterou chcete přidat. Tento krok je **nepovinný**.  
    2. Vyberte tlačítko **Revize + vytvořit** .

        ![Stránka pro vytvoření clusteru Event Hubs – stránka značek](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na stránce **Revize + vytvořit** si přečtěte podrobnosti a vyberte **vytvořit**. 

    ![Stránka vytvoření clusteru Event Hubs-zkontrolovat + vytvořit stránku](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Vytvoření oboru názvů a centra událostí v rámci clusteru

1. Pokud chcete vytvořit obor názvů v rámci clusteru, na stránce **Event Hubs clusteru** pro váš cluster Vyberte **+ obor názvů** v horní nabídce.

    ![Stránka správy clusteru – tlačítko Přidat obor názvů](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na stránce vytvořit obor názvů proveďte následující kroky:
    1. Zadejte **název oboru názvů**.  Systém zkontroluje, jestli je název k dispozici.
    2. Obor názvů dědí následující vlastnosti:
        1. ID předplatného
        2. Skupina prostředků
        3. Umístění
        4. Název clusteru
    3. Vyberte **vytvořit** a vytvořte obor názvů. Teď můžete cluster spravovat.  

        ![Vytvoření oboru názvů na stránce clusteru](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Po vytvoření oboru názvů můžete [vytvořit centrum událostí](event-hubs-create.md#create-an-event-hub) tak, jak byste ho normálně vytvořili v rámci oboru názvů. 


## <a name="submit-a-support-request"></a>Odeslat žádost o podporu

Pokud chcete změnit velikost clusteru po vytvoření nebo když vaše upřednostňovaná oblast není k dispozici, odešlete žádost o podporu pomocí následujících kroků:

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **help + podpora** .
2. V nabídce podpora vyberte **+ Nová žádost o podporu** .
3. Na stránce Podpora proveďte tyto kroky:
    1. V rozevíracím seznamu **typ problému**vyberte možnost **Technical** .
    2. V části **Předplatné** vyberte své předplatné.
    3. V případě **služby**vyberte **moje služby**a pak vyberte **Event Hubs**.
    4. V případě **prostředku**vyberte svůj cluster, pokud již existuje, jinak vyberte možnost **obecný dotaz nebo prostředek není k dispozici**.
    5. Jako **typ problému**vyberte **kvóta**.
    6. V části **problémový typ**vyberte jednu z následujících hodnot z rozevíracího seznamu:
        1. Vyberte **požadavek na vyhrazenou SKU** , který bude vyžadovat, aby se funkce podporovala ve vaší oblasti.
        2. Vyberte **požadavek pro horizontální navýšení nebo snížení kapacity vyhrazeného clusteru** , pokud chcete škálovat nebo snížit velikost vyhrazeného clusteru. 
    7. Pro **Předmět**Popište problém.

        ![Stránka lístku podpory](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Odstranění vyhrazeného clusteru
 
1. Pokud chcete cluster odstranit, v horní nabídce vyberte **Odstranit** . Upozorňujeme, že po vytvoření se váš cluster bude účtovat po dobu minimálně 4 hodin používání. 
2. Zobrazí se zpráva s potvrzením, že chcete cluster odstranit.
3. Zadejte **název clusteru** a vyberte **Odstranit** , aby se cluster odstranil.

    ![Stránka odstranit cluster](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili cluster Event Hubs. Podrobné pokyny k posílání a přijímání událostí z centra událostí a zachycení událostí do úložiště Azure nebo Azure Data Lake Store najdete v následujících kurzech:

- Odesílání a příjem událostí 
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
- [Povolit Event Hubs zachycení pomocí Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Použít Azure Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

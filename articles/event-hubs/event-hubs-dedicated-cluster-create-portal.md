---
title: Vytvoření vyhrazeného clusteru Centra událostí pomocí portálu Azure
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster Azure Event Hubs pomocí portálu Azure.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 5b1574eaac8771043e09500225b65e4835c8e627
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77157478"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Úvodní příručka: Vytvoření vyhrazeného clusteru Centra událostí pomocí portálu Azure 
Clustery Event Hubs nabízejí nasazení pro jednoho klienta pro zákazníky s nejnáročnějšími potřebami streamování. Tato nabídka má garantovanou 99.99% SLA a je k dispozici pouze na naší vyhrazené cenové úrovni. [Cluster centra událostí](event-hubs-dedicated-overview.md) může příchozí přenos milionů událostí za sekundu s garantovanou kapacitou a latencí podsekund. Obory názvů a centra událostí vytvořená v rámci clusteru zahrnují všechny funkce standardní nabídky a další, ale bez omezení příchozího přenosu dat. Vyhrazená nabídka také obsahuje oblíbenou funkci [zachycení centra událostí](event-hubs-capture-overview.md) bez dalších nákladů, což vám umožní automaticky dávkovat a protokolovat datové proudy do azure [blob storage](../storage/blobs/storage-blobs-introduction.md) nebo Azure Data Lake Storage Gen [1](../data-lake-store/data-lake-store-overview.md).

Vyhrazené clustery jsou zřízeny a účtovány **jednotkami kapacity (CU)**, předem přidělené množství prostředků procesoru a paměti. Pro každý cluster můžete zakoupit 1, 2, 4, 8, 12, 16 nebo 20 CU. V tomto rychlém startu vás provedeme vytvořením clusteru 1 CU Event Hubs prostřednictvím portálu Azure.

> [!NOTE]
> Toto samoobslužné prostředí je momentálně dostupné ve verzi Preview na [Azure Portal](https://aka.ms/eventhubsclusterquickstart). Máte-li jakékoli dotazy týkající se vyhrazené nabídky, obraťte se na [tým Event Hubs](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Požadavky
Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Účet Azure. Pokud ho nemáte, [kupte si účet](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) ještě před začátkem. Tato funkce není podporovaná s bezplatným účtem Azure. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (verze 15.3, 26730.01) nebo novější.
- [NET Standard SDK](https://dotnet.microsoft.com/download) verze 2.0 nebo novější.
- [Byla vytvořena skupina prostředků](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Vytvoření vyhrazeného clusteru centra událostí
Cluster Centra událostí poskytuje jedinečný kontejner oboru, ve kterém můžete vytvořit jeden nebo více oborů názvů. V této fázi náhledu samoobslužného prostředí portálu můžete vytvořit 1 clustery CU ve vybraných oblastech. Pokud potřebujete cluster větší než 1 CU, můžete odeslat žádost o podporu Azure pro navýšení kapacity clusteru po jeho vytvoření.

Pokud chcete vytvořit cluster ve skupině prostředků pomocí portálu Azure, proveďte následující kroky:

1. Pomocí [tohoto odkazu](https://aka.ms/eventhubsclusterquickstart) vytvořte cluster na webu Azure Portal. Naopak vyberte **všechny služby** z levého navigačního podokna, zadejte do panelu hledání text "Clustery center událostí" a ze seznamu výsledků vyberte "Clustery centra událostí".
2. Na stránce **Vytvořit cluster** nakonfigurujte následující:
    1. Zadejte **název clusteru**. Systém okamžitě kontroluje, jestli je název dostupný.
    2. Vyberte **předplatné,** ve kterém chcete vytvořit cluster.
    3. Vyberte **skupinu prostředků,** do které chcete vytvořit cluster.
    4. Vyberte **umístění** clusteru. Pokud je upřednostňovaná oblast zobrazena šedě, je dočasně mimo kapacitu a můžete odeslat [žádost o podporu](#submit-a-support-request) týmu Event Hubs.
    5. Vdolní části stránky vyberte tlačítko **Další: Značky.** Možná budete muset několik minut počkat, než systém úplně zřídí prostředky.

        ![Vytvořit cluster center rozbočovačů událostí – stránka Základy](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na stránce **Značky** nakonfigurujte následující:
    1. Zadejte **název** a **hodnotu** značky, kterou chcete přidat. Tento krok je **volitelný**.  
    2. Vyberte tlačítko **Revize + Vytvořit.**

        ![Stránka Clusteru Vytvořit centra událostí – stránka Značky](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na stránce **Revize + Vytvoření** zkontrolujte podrobnosti a vyberte **Vytvořit**. 

    ![Vytvořit clusterová stránka Centra událostí – zkontrolovat + vytvořit stránku](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Vytvoření oboru názvů a centra událostí v clusteru

1. Chcete-li vytvořit obor názvů v clusteru, vyberte na stránce **Clusteru centra událostí** pro váš cluster v horní nabídce **+Obor názvů.**

    ![Stránka správy clusteru – tlačítko Přidat obor názvů](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na stránce oboru názvů proveďte následující kroky:
    1. Zadejte **název oboru názvů**.  Systém zkontroluje, zda je název k dispozici.
    2. Obor názvů dědí následující vlastnosti:
        1. ID předplatného
        2. Skupina prostředků
        3. Umístění
        4. Název clusteru
    3. Vyberte **Vytvořit,** chcete-li vytvořit obor názvů. Nyní můžete spravovat svůj cluster.  

        ![Vytvoření oboru názvů na stránce clusteru](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Po vytvoření oboru názvů můžete [vytvořit centrum událostí](event-hubs-create.md#create-an-event-hub) tak, jako byste ho normálně vytvořili v oboru názvů. 


## <a name="submit-a-support-request"></a>Odeslání žádosti o podporu

Pokud chcete změnit velikost clusteru po vytvoření nebo pokud upřednostňovaná oblast není k dispozici, odešlete žádost o podporu následujícím postupem:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **nápovědu + podporu** v levé nabídce.
2. V nabídce Podpora vyberte **+ Nová žádost o podporu.**
3. Na stránce podpory postupujte takto:
    1. V **rozevíracím**seznamu vyberte **možnost Technický.**
    2. V části **Předplatné** vyberte své předplatné.
    3. V **popřípadě Služba**vyberte **Moje služby**a pak vyberte **Centra událostí**.
    4. V **aplikaci Resource**vyberte cluster, pokud již existuje, jinak vyberte **možnost Obecná otázka/Prostředek není k dispozici**.
    5. V **popřípadě typ Problém**vyberte možnost **Kvóta**.
    6. V **části Problém ový podtyp**vyberte jednu z následujících hodnot z rozevíracího seznamu:
        1. Vyberte **Požadavek na vyhrazenou skladovou položku** a požádejte o podporu funkce ve vaší oblasti.
        2. Pokud chcete vertikálně navýšit kapacitu nebo vertikálně navýšit kapacitu **vyhrazeného clusteru, vyberte požadavek na navýšení kapacity nebo na vertikálně navýšit** kapacitu. 
    7. V **tématu Předmět**popište problém.

        ![Stránka lístku podpory](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Odstranění vyhrazeného clusteru
 
1. Chcete-li cluster odstranit, vyberte v horní nabídce příkaz **Odstranit.** Vezměte prosím na vědomí, že vašemu clusteru se bude účtovat minimálně 4 hodiny používání po vytvoření. 
2. Zobrazí se zpráva potvrzující vaše přání cluster uzaminit.
3. Zadejte **název clusteru** a výběrem **možnosti Odstranit** clusteru odstraníte.

    ![Odstranit stránku clusteru](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili cluster centra událostí. Podrobné pokyny k odesílání a přijímání událostí z centra událostí a zachycení událostí do úložiště Azure nebo úložiště Azure Data Lake Store najdete v následujících kurzech:

- Odesílání a příjem událostí 
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
- [Povolení digitalizace centra událostí pomocí portálu Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Použití Centra událostí Azure pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

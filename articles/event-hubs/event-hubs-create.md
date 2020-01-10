---
title: Rychlý Start Azure – vytvoření centra událostí pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak pomocí webu Azure Portal vytvořit centrum událostí Azure a pak odesílat a přijímat události pomocí sady .NET Standard SDK.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 8b6a9978d6db1a8978f993abf16d7cfbfb2f44a9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771015"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Rychlý start: Vytvoření centra událostí pomocí webu Azure Portal
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte centrum událostí pomocí webu [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Visual Studio 2019)](https://www.visualstudio.com/vs) nebo novější.
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logická kolekce prostředků Azure. Všechny prostředky se nasazují a spravují ve skupině prostředků. Vytvoření skupiny prostředků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Na levém navigačním panelu klikněte na **Skupiny prostředků**. Pak klikněte na **Přidat**.

   ![Skupiny prostředků – tlačítko Přidat](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. V poli **předplatné**vyberte název předplatného Azure, ve kterém chcete vytvořit skupinu prostředků.
3. Zadejte jedinečný **název pro skupinu prostředků**. Systém okamžitě zkontroluje, jestli je název dostupný v aktuálně vybraném předplatném Azure.
4. Vyberte **oblast** pro skupinu prostředků.
5. Vyberte **zkontrolovat + vytvořit**.

   ![Skupina prostředků – Vytvořit](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Na stránce **Revize + vytvořit** vyberte **vytvořit**. 

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Obor názvů služby Event Hubs poskytuje jedinečný kontejner oboru, na který se odkazuje jeho plně kvalifikovaným názvem domény a ve kterém můžete vytvořit jedno nebo více center událostí. Pokud chcete vytvořit obor názvů ve své skupině prostředků pomocí portálu, postupujte následovně:

1. Na webu Azure Portal v levém horním rohu obrazovky klikněte na **Vytvořit prostředek**.
2. V nabídce vlevo vyberte **všechny služby** a v kategorii **Analýza** vyberte **hvězdičku (`*`)** vedle **Event Hubs** . Potvrďte, že je do **oblíbených položek** v levé navigační nabídce přidáno **Event Hubs** . 
    
   ![Hledat Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. V levé navigační nabídce vyberte **Event Hubs** v části **Oblíbené** a na panelu nástrojů vyberte **Přidat** .

   ![Tlačítko Přidat](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Na stránce **vytvořit obor názvů** proveďte následující kroky:
    1. Zadejte **název** oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.
    2. Vyberte **cenovou úroveň** (Basic nebo Standard).
    3. Všimněte si, že možnost **Povolit Kafka** je automaticky povolená. Azure Event Hubs poskytuje koncový bod Kafka. Umožňuje tento koncový bod vašeho oboru názvů služby Event Hubs nativně pochopit [Apache Kafka](https://kafka.apache.org/intro) zprávy protokolu a rozhraní API. Díky této funkci může komunikovat s event hubs jako při použití témat Kafka bez změny vašim klientům protokolu nebo spuštění vlastní clustery. Podporuje Služba Event Hubs [Apache Kafka verze 1.0](https://kafka.apache.org/10/documentation.html) a novější.
    4. Vyberte **předplatné** , ve kterém chcete vytvořit obor názvů.
    5. Vyberte existující **skupinu prostředků** nebo vytvořte novou skupinu prostředků. 
    4. Vyberte **umístění** pro obor názvů.
    5. Vyberte **Vytvořit**. Možná budete muset několik minut počkat, než systém úplně zřídí prostředky.

       ![Vytvoření oboru názvů centra událostí](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Aktualizujte stránku **Event Hubs** , aby se zobrazil obor názvů centra událostí. V upozorněních můžete zjistit stav vytváření centra událostí. 

    ![Vytvoření oboru názvů centra událostí](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Vyberte obor názvů. Na portálu se zobrazí domovská stránka **oboru názvů Event Hubs** . 

   ![Domovská stránka oboru názvů](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Pokud chcete v rámci oboru názvů vytvořit centrum událostí, postupujte následovně:

1. Na stránce Event Hubs obor názvů vyberte v nabídce vlevo možnost **Event Hubs** .
1. V horní části okna klikněte na **+ Centrum událostí**.
   
    ![Tlačítko Přidat centrum událostí](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Zadejte název centra událostí a pak klikněte na **Vytvořit**.
   
    ![Vytvoření centra událostí](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Stav vytvoření centra událostí můžete kontrolovat v části výstrahy. Až se centrum událostí vytvoří, zobrazí se v seznamu Center událostí, jak je znázorněno na následujícím obrázku:

    ![Centrum událostí se vytvořilo.](./media/event-hubs-quickstart-portal/event-hub-created.png)

Blahopřejeme! Pomocí portálu jste vytvořili obor názvů služby Event Hubs a v něm centrum událostí. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili skupinu prostředků, obor názvů služby Event Hubs a centrum událostí. Podrobné pokyny k odesílání událostí do (nebo) přijímání událostí z centra událostí najdete v tématu kurzy pro **odesílání a příjem** : 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (pouze odeslat)](event-hubs-c-getstarted-send.md)
- [Apache Storm (pouze příjem)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png

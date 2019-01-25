---
title: Rychlý start Azure – Vytvoření centra událostí pomocí webu Azure Portal | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak pomocí webu Azure Portal vytvořit centrum událostí Azure a pak odesílat a přijímat události pomocí sady .NET Standard SDK.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/23/2019
ms.author: shvija
ms.openlocfilehash: 073d1b54b1c6da2b1178f482d17b4de7abcc8a51
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888919"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Rychlý start: Vytvoření centra událostí pomocí webu Azure portal
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte centrum událostí pomocí webu [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější.
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logická kolekce prostředků Azure. Všechny prostředky se nasazují a spravují ve skupině prostředků. Chcete-li vytvořit skupinu prostředků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levém navigačním panelu klikněte na **Skupiny prostředků**. Pak klikněte na **Přidat**.

   ![Skupiny prostředků – tlačítko Přidat](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Pro **předplatné**, vyberte název předplatného Azure, ve kterém chcete vytvořit skupinu prostředků.
3. Zadejte jedinečný **název skupiny prostředků**. Systém okamžitě zkontroluje, jestli je název dostupný v aktuálně vybraném předplatném Azure.
4. Vyberte **oblasti** pro skupinu prostředků.
5. Vyberte **zkontrolovat a vytvořit**.

   ![Skupina prostředků – Vytvořit](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Na **revize + vytvořit** stránce **vytvořit**. 

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Obor názvů služby Event Hubs poskytuje jedinečný kontejner oboru, na který se odkazuje jeho plně kvalifikovaným názvem domény a ve kterém můžete vytvořit jedno nebo více center událostí. Pokud chcete vytvořit obor názvů ve své skupině prostředků pomocí portálu, postupujte následovně:

1. Na webu Azure Portal v levém horním rohu obrazovky klikněte na **Vytvořit prostředek**.
2. Vyberte **všechny služby** v levé nabídce a vyberte **hvězdičkami (`*`)** vedle **Event Hubs** v **Analytics** kategorie. Ujistěte se, že **Event Hubs** se přidá do **Oblíbené** v levé navigační nabídce. 
    
   ![Vyhledávání pro službu Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Vyberte **Event Hubs** pod **Oblíbené** v levé navigační nabídce a vyberte **přidat** na panelu nástrojů.

   ![Přidání tlačítka panelu nástrojů](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Na **vytvoření oboru názvů** stránce, proveďte následující kroky:
    1. Zadejte název pro obor názvů. Systém okamžitě kontroluje, jestli je název dostupný.
    2. Shoose cenovou úroveň (Basic nebo Standard).
    3. Vyberte **předplatné** ve kterém chcete vytvořit obor názvů.
    4. Vyberte **umístění** pro obor názvů.
    5. Vyberte **Vytvořit**. Možná budete muset několik minut počkat, než systém úplně zřídí prostředky.

       ![Vytvoření oboru názvů centra událostí](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Aktualizovat **Event Hubs** stránku, abyste zobrazili obor názvů centra událostí. Můžete zkontrolovat stav vytvoření centra událostí v upozorněních. 

    ![Vytvoření oboru názvů centra událostí](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Vyberte obor názvů. Zobrazí se domovská stránka pro vaše **oboru názvů Event Hubs** na portálu. 

   ![Domovská stránka pro obor názvů](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Pokud chcete v rámci oboru názvů vytvořit centrum událostí, postupujte následovně:

1. Na stránce Event Hubs Namespace vyberte **Event Hubs** v levé nabídce.
1. V horní části okna klikněte na **+ Centrum událostí**.
   
    ![Tlačítko Přidat centrum událostí](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Zadejte název centra událostí a pak klikněte na **Vytvořit**.
   
    ![Vytvoření centra událostí](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Můžete zkontrolovat stav vytvoření centra událostí ve výstrahách. Po vytvoření centra událostí se zobrazí v seznamu event hubs jak je znázorněno na následujícím obrázku:

    ![Vytvoření centra událostí](./media/event-hubs-quickstart-portal/event-hub-created.png)

Blahopřejeme! Pomocí portálu jste vytvořili obor názvů služby Event Hubs a v něm centrum událostí. 

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili skupinu prostředků, obor názvů služby Event Hubs a centrum událostí. Podrobné pokyny k odesílání událostí do centra událostí nebo příjmu událostí z centra událostí najdete v následujících kurzech:  

- **Odesílání událostí do centra událostí**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md), [rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Přejít](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Příjem událostí z centra událostí**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md), [rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js ](event-hubs-node-get-started-receive.md), [Přejít](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png

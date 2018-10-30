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
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: d053edaa187a3e0626f5ea0864d778f44f394bd7
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455772"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Rychlý start: Vytvoření centra událostí pomocí webu Azure Portal
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte centrum událostí pomocí webu [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](http://www.visualstudio.com/vs) nebo novější.
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logická kolekce prostředků Azure. Všechny prostředky se nasazují a spravují ve skupině prostředků. Skupinu prostředků vytvoříte následujícím postupem:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levém navigačním panelu klikněte na **Skupiny prostředků**. Pak klikněte na **Přidat**.

   ![Skupiny prostředků – tlačítko Přidat](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Zadejte jedinečný název skupiny prostředků. Systém okamžitě zkontroluje, jestli je název dostupný v aktuálně vybraném předplatném Azure.

3. V části **Předplatné** klikněte na název předplatného Azure, ve kterém chcete skupinu prostředků vytvořit.

4. Vyberte geografické umístění skupiny prostředků.

5. Klikněte na možnost **Vytvořit**.

   ![Skupina prostředků – Vytvořit](./media/event-hubs-quickstart-portal/resource-groups2.png)

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Obor názvů služby Event Hubs poskytuje jedinečný kontejner oboru, na který se odkazuje jeho plně kvalifikovaným názvem domény a ve kterém můžete vytvořit jedno nebo více center událostí. Pokud chcete vytvořit obor názvů ve své skupině prostředků pomocí portálu, postupujte následovně:

1. Na webu Azure Portal v levém horním rohu obrazovky klikněte na **Vytvořit prostředek**.

2. Klikněte na **Internet věcí** a pak na **Event Hubs**.

3. V části **Vytvořit obor názvů** zadejte název oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.

   ![Vytvoření oboru názvů centra událostí](./media/event-hubs-create/create-event-hub1.png)

4. Po kontrole, že je název oboru názvů k dispozici, zvolte cenovou úroveň (Basic nebo Standard). Zvolte také předplatné Azure, skupinu prostředků a umístění, ve kterém se má prostředek vytvořit.
 
5. Kliknutím na **Vytvořit** vytvoříte obor názvů. Možná budete muset několik minut počkat, než systém úplně zřídí prostředky.
6. Vyberte **Upozornění** a pak vyberte **nasazení** se stejným názvem jako obor názvů centra událostí. 

   ![Skupina prostředků – upozornění na vytvoření](./media/event-hubs-quickstart-portal/create-alert.png)
6. V seznamu prostředků vytvořených v tomto nasazení vyberte svůj obor názvů centra událostí. 

   ![Výběr oboru názvů z nasazení](./media/event-hubs-quickstart-portal/deployment-namespace.png)
7. Na stránce **Obor názvů služby Event Hubs** vyberte **Zásady sdíleného přístupu** a pak klikněte na **RootManageSharedAccessKey**.
    
8. Kliknutím na tlačítko kopírovat zkopírujte připojovací řetězec **RootManageSharedAccessKey** do schránky. Pro pozdější použití si tento připojovací řetězec uložte do dočasného umístění, například do Poznámkového bloku.
    
## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Pokud chcete v rámci oboru názvů vytvořit centrum událostí, postupujte následovně:

1. Na stránce Obory názvů služby Event Hubs klikněte na **Event Hubs**.
   
    ![Výběr možnosti Event Hubs v levé nabídce](./media/event-hubs-quickstart-portal/create-event-hub3.png)

1. V horní části okna klikněte na **+ Centrum událostí**.
   
    ![Tlačítko Přidat centrum událostí](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Zadejte název centra událostí a pak klikněte na **Vytvořit**.
   
    ![Vytvoření centra událostí](./media/event-hubs-quickstart-portal/create-event-hub5.png)


Blahopřejeme! Pomocí portálu jste vytvořili obor názvů služby Event Hubs a v něm centrum událostí. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili skupinu prostředků, obor názvů služby Event Hubs a centrum událostí. Podrobné pokyny k odesílání událostí do centra událostí nebo příjmu událostí z centra událostí najdete v následujících kurzech:  

- **Odesílání událostí do centra událostí:** [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Příjem událostí z centra událostí:** [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png

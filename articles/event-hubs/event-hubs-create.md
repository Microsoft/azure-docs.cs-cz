---
title: Rychlý Start Azure – vytvoření centra událostí pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak pomocí webu Azure Portal vytvořit centrum událostí Azure a pak odesílat a přijímat události pomocí sady .NET Standard SDK.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bc9190bba6b21e59f10f51bd0eb7da2426f6d1b4
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902112"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Rychlý start: Vytvoření centra událostí pomocí webu Azure Portal
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte centrum událostí pomocí webu [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Visual Studio 2019](https://www.visualstudio.com/vs) nebo novější.
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logická kolekce prostředků Azure. Všechny prostředky se nasazují a spravují ve skupině prostředků. Vytvoření skupiny prostředků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
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
2. V nabídce vlevo vyberte **všechny služby** a v kategorii **Analýza** vyberte **hvězdičku ( `*` )** vedle **Event Hubs** . Potvrďte, že je do **oblíbených položek** v levé navigační nabídce přidáno **Event Hubs** . 
    
   ![Hledat Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. V levé navigační nabídce vyberte **Event Hubs** v části **Oblíbené** a na panelu nástrojů vyberte **Přidat** .

   ![Tlačítko Přidat](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Na stránce **vytvořit obor názvů** proveďte následující kroky:
    1. Vyberte **předplatné** , ve kterém chcete vytvořit obor názvů.
    2. Vyberte **skupinu prostředků** , kterou jste vytvořili v předchozím kroku. 
    3. Zadejte **název** oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.
    4. Vyberte **umístění** pro obor názvů.    
    5. Vyberte **cenovou úroveň** (Basic nebo Standard).  
    6. Nastavení **jednotek propustnosti** nechte v takovém případě. Další informace o jednotkách propustnosti najdete v tématu [škálovatelnost Event Hubs](event-hubs-scalability.md#throughput-units) .  
    5. V dolní části stránky vyberte **zkontrolovat + vytvořit** .

       ![Vytvoření oboru názvů pro centrum událostí](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   6. Na stránce **Revize + vytvořit** zkontrolujte nastavení a vyberte **vytvořit**. Počkejte, až se nasazení dokončí. 

       ![Zkontrolovat + vytvořit stránku](./media/event-hubs-quickstart-portal/review-create.png)
   7. Na stránce **nasazení** vyberte **Přejít k prostředku** a přejděte na stránku pro váš obor názvů. 

      ![Nasazení je hotové – přejít k prostředku](./media/event-hubs-quickstart-portal/deployment-complete.png)
   8. Potvrďte, že se zobrazí stránka **Event Hubs obor názvů** podobná následujícímu příkladu: 

       ![Domovská stránka oboru názvů](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

       > [!NOTE]
       > Azure Event Hubs poskytuje koncový bod Kafka. Tento koncový bod umožňuje vašemu oboru názvů Event Hubs nativně rozumět protokolům a rozhraním API [Apache Kafka](https://kafka.apache.org/intro) zpráv. Díky této funkci můžete komunikovat s centry událostí stejně, jako byste používali témata Kafka, aniž byste museli měnit klienty protokolu nebo spouštět vlastní clustery. Event Hubs podporuje [Apache Kafka verze 1,0](https://kafka.apache.org/10/documentation.html) a novější. Další informace najdete v tématu [použití Event Hubs z Apache Kafkach aplikací](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Pokud chcete v rámci oboru názvů vytvořit centrum událostí, postupujte následovně:

1. Na stránce Event Hubs obor názvů vyberte v nabídce vlevo možnost **Event Hubs** .
1. V horní části okna klikněte na **+ Centrum událostí**.
   
    ![Tlačítko Přidat centrum událostí](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Zadejte název centra událostí a pak klikněte na **vytvořit**.
   
    ![Vytvoření centra událostí](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Stav vytvoření centra událostí můžete kontrolovat v části výstrahy. Až se centrum událostí vytvoří, zobrazí se v seznamu Center událostí, jak je znázorněno na následujícím obrázku:

    ![Centrum událostí se vytvořilo.](./media/event-hubs-quickstart-portal/event-hub-created.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili skupinu prostředků, obor názvů služby Event Hubs a centrum událostí. Podrobné pokyny k odesílání událostí do (nebo) přijímání událostí z centra událostí najdete v tématu kurzy pro **odesílání a příjem** : 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
- [Přejít](event-hubs-go-get-started-send.md)
- [C (jenom odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (jenom příjem)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png

---
title: Rychlý Start Azure – vytvoření centra událostí pomocí Azure Portal
description: V tomto rychlém startu se dozvíte, jak pomocí webu Azure Portal vytvořit centrum událostí Azure a pak odesílat a přijímat události pomocí sady .NET Standard SDK.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 84cafcc86142cb9b97639c023971e7d290fc79fc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88927880"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Rychlý start: Vytvoření centra událostí pomocí webu Azure Portal
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte centrum událostí pomocí webu [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Předpoklady

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logická kolekce prostředků Azure. Všechny prostředky se nasazují a spravují ve skupině prostředků. Vytvoření skupiny prostředků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levém navigačním panelu klikněte na **Skupiny prostředků**. Pak klikněte na **Přidat**.

   ![Skupiny prostředků – tlačítko Přidat](./media/event-hubs-quickstart-portal/resource-groups1.png)

1. V poli **předplatné**vyberte název předplatného Azure, ve kterém chcete vytvořit skupinu prostředků.
1. Zadejte jedinečný **název pro skupinu prostředků**. Systém okamžitě zkontroluje, jestli je název dostupný v aktuálně vybraném předplatném Azure.
1. Vyberte **oblast** pro skupinu prostředků.
1. Vyberte **zkontrolovat + vytvořit**.

   ![Skupina prostředků – Vytvořit](./media/event-hubs-quickstart-portal/resource-groups2.png)
1. Na stránce **Revize + vytvořit** vyberte **vytvořit**. 

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Obor názvů služby Event Hubs poskytuje jedinečný kontejner oboru, na který se odkazuje jeho plně kvalifikovaným názvem domény a ve kterém můžete vytvořit jedno nebo více center událostí. Pokud chcete vytvořit obor názvů ve své skupině prostředků pomocí portálu, postupujte následovně:

1. Na webu Azure Portal v levém horním rohu obrazovky klikněte na **Vytvořit prostředek**.
1. V nabídce vlevo vyberte **všechny služby** a v kategorii **Analýza** vyberte **hvězdičku ( `*` )** vedle **Event Hubs** . Potvrďte, že je do **oblíbených položek** v levé navigační nabídce přidáno **Event Hubs** . 
    
   ![Hledat Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
1. V levé navigační nabídce vyberte **Event Hubs** v části **Oblíbené** a na panelu nástrojů vyberte **Přidat** .

   ![Tlačítko Přidat](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
1. Na stránce **vytvořit obor názvů** proveďte následující kroky:  
   1. Vyberte **předplatné** , ve kterém chcete vytvořit obor názvů.  
   1. Vyberte **skupinu prostředků** , kterou jste vytvořili v předchozím kroku.   
   1. Zadejte **název** oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný.  
   1. Vyberte **umístění** pro obor názvů.      
   1. Vyberte **cenovou úroveň** (Basic nebo Standard).    
   1. Nastavení **jednotek propustnosti** nechte v takovém případě. Další informace o jednotkách propustnosti najdete v tématu [Event Hubs škálovatelnost](event-hubs-scalability.md#throughput-units).  
   1. V dolní části stránky vyberte **zkontrolovat + vytvořit** .
      
      ![Vytvoření oboru názvů pro centrum událostí](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   1. Na stránce **Revize + vytvořit** zkontrolujte nastavení a vyberte **vytvořit**. Počkejte, až se nasazení dokončí. 
      
      ![Zkontrolovat + vytvořit stránku](./media/event-hubs-quickstart-portal/review-create.png)
      
   1. Na stránce **nasazení** vyberte **Přejít k prostředku** a přejděte na stránku pro váš obor názvů. 
      
      ![Nasazení je hotové – přejít k prostředku](./media/event-hubs-quickstart-portal/deployment-complete.png)  
   1. Potvrďte, že se zobrazí stránka **Event Hubs obor názvů** podobná následujícímu příkladu:   
      
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
1. Stav vytvoření centra událostí můžete kontrolovat v části výstrahy. Až se centrum událostí vytvoří, zobrazí se v seznamu Center událostí, jak je znázorněno na následujícím obrázku:

    ![Centrum událostí se vytvořilo.](./media/event-hubs-quickstart-portal/event-hub-created.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili skupinu prostředků, obor názvů služby Event Hubs a centrum událostí. Podrobné pokyny k odesílání událostí do (nebo) přijímání událostí z centra událostí najdete v tématu kurzy pro **odesílání a příjem** : 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Přejít](event-hubs-go-get-started-send.md)
- [C (jenom odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (jenom příjem)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png

---
title: Použití Průzkumníka Azure Service Bus k provádění operací s daty v Service Bus (Preview)
description: Tento článek poskytuje informace o tom, jak používat Azure Service Bus Průzkumník na portálu pro přístup k datům Azure Service Bus.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: e5e97c6860c2cc01048f4f7caf9f40f9e07592d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295596"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>Použití Průzkumníka Service Bus k provádění operací s daty v Service Bus (Preview)

## <a name="overview"></a>Přehled

Azure Service Bus umožňuje klientským aplikacím odesílatele a přijímači oddělit svoji obchodní logiku s využitím známých sémantických znalostí typu Point-to-Point (fronta) a publikační odběr (téma-předplatné).

Operace prováděné s oborem názvů Azure Service Bus mají dva druhy. 
   * Operace správy – vytvořit, aktualizovat, odstranit Service Bus obor názvů, fronty, témata a odběry.
   * Operace s daty – odesílání a příjem zpráv z front, témat a odběrů.

Průzkumník Azure Service Bus rozšiřuje funkce portálu nad rámec operací správy za účelem podpory datových operací (odeslání, přijetí, prohlížení) ve frontách, tématech a předplatných (a jejich podentit nedoručených zpráv) – přímo ze samotné Azure Portal.

> [!NOTE]
> V tomto článku se zvýrazní funkčnost Azure Service Bus Exploreru, která se nachází na Azure Portal.
>
> Nástroj Azure Service Bus Explorer ***není ve vlastnictví*** komunity OSS [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).
>

## <a name="prerequisites"></a>Požadavky

Chcete-li použít nástroj Service Bus Explorer, budete muset zřídit obor názvů Azure Service Bus. 

Po zřízení oboru názvů Service Bus je třeba vytvořit frontu pro odeslání a přijetí zprávy z nebo tématu s odběrem k otestování funkčnosti.

Další informace o tom, jak vytvořit fronty, témata a odběry, najdete v následujících odkazech.
   * [Rychlý Start – vytvoření front](service-bus-quickstart-portal.md)
   * [Rychlý Start – vytvoření témat](service-bus-quickstart-topics-subscriptions-portal.md)


## <a name="using-the-service-bus-explorer"></a>Použití Průzkumníka Service Bus

Chcete-li použít Průzkumníka Azure Service Bus, je nutné přejít na obor názvů Service Bus, na kterém chcete provádět operace odeslání, prohlížení a příjmu.

Pokud chcete provádět operace s frontou, vyberte v navigační nabídce **možnost fronty** . Pokud chcete provádět operace s tématem (a souvisejícími předplatnými), vyberte **témata**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="Výběr entity":::

Po výběru možnosti **fronty** nebo **témata**vyberte konkrétní frontu nebo téma.

V levé navigační nabídce vyberte **Service Bus Explorer (Preview).**

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Výběr entity":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Odeslání zprávy do fronty nebo tématu

Pokud chcete odeslat zprávu do **fronty** nebo **tématu**, klikněte na kartu ***Odeslat*** v Průzkumníkovi Service Bus.

Vytvoření zprávy sem – 

1. Vyberte **typ obsahu** , který se bude nacházet buď jako "text/prostý", "Application/XML" nebo "Application/JSON".
2. Přidejte **obsah**zprávy. Zajistěte, aby odpovídaly dříve nastavenému **typu obsahu** .
3. Nastavení **rozšířených vlastností** (volitelné) – patří mezi ně ID korelace, ID zprávy, popisek, ReplyTo, doba TTL (Time to Live) a plánovaná doba zařazení do fronty (pro naplánované zprávy).
4. Nastavení **vlastních vlastností** – může to být jakákoli vlastnost uživatele nastavená na klíč slovníku.

Po sestavení zprávy stiskněte tlačítko Odeslat.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Výběr entity":::

Po úspěšném dokončení operace odeslání se 

* Při odesílání do fronty se zvýší čítač metrik **aktivních zpráv** .

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Výběr entity":::

* Při odesílání do tématu se čítač metrik **aktivních zpráv** zvýší na odběr, na který byla zpráva směrována.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="Výběr entity":::

### <a name="receiving-a-message-from-a-queue"></a>Příjem zprávy z fronty

Funkce Receive v Průzkumníkovi Service Bus umožňuje příjem jedné zprávy současně. Operace Receive se provádí pomocí režimu **ReceiveAndDelete** .

> [!IMPORTANT]
> Počítejte s tím, že operace Receive prováděná průzkumníkem Service Bus je ***destruktivní příjem***, tj. zpráva je odebrána z fronty, když se zobrazí v nástroji Service Bus Explorer.
>
> Pokud chcete procházet zprávy bez jejich odebrání z fronty, zvažte použití funkce ***prohlížení*** .
>

Příjem zprávy z fronty (nebo z podfronty nedoručených zpráv) 

1. V Průzkumníkovi Service Bus klikněte na kartu ***přijmout*** .
2. Podívejte se na metriky a zjistěte, jestli neexistují **aktivní zprávy** nebo **zprávy nedoručených zpráv** .

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Výběr entity":::

3. Vyberte mezi ***frontou*** nebo podfrontou ***nedoručených zpráv*** .

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="Výběr entity":::

4. Klikněte na tlačítko ***přijmout*** a pak na ***Ano*** a potvrďte operaci přijmout a odstranit.


Po úspěšném dokončení operace Receive se v mřížce zobrazí podrobnosti zprávy, jak je uvedeno níže. Můžete vybrat zprávu z mřížky a zobrazit její podrobnosti.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Výběr entity":::


### <a name="peeking-a-message-from-a-queue"></a>Prohlížení zprávy z fronty

Pomocí funkce prohlížení můžete pomocí Průzkumníka Service Bus zobrazit prvních 32 zpráv ve frontě nebo ve frontě nedoručených zpráv.

1. Chcete-li prohlížet zprávy ve frontě, klikněte na kartu ***Náhled*** v Průzkumníkovi Service Bus.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="Výběr entity":::

2. Podívejte se na metriky a zjistěte, jestli k prohlížení neexistují **aktivní zprávy** nebo **zprávy nedoručených zpráv** .

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="Výběr entity":::

3. Pak vyberte mezi ***frontou*** nebo podfrontou ***nedoručených zpráv*** .

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="Výběr entity":::

4. Klikněte na tlačítko ***Náhled*** . 

Po dokončení operace prohlížet se v mřížce zobrazí až 32 zpráv, jak je uvedeno níže. Chcete-li zobrazit podrobnosti konkrétní zprávy, vyberte ji z mřížky. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="Výběr entity":::

> [!NOTE]
>
> Vzhledem k tomu, že Náhled není destruktivní operace, zpráva **nebude** odebrána z fronty.
>

### <a name="receiving-a-message-from-a-subscription"></a>Příjem zprávy z předplatného

Stejně jako u fronty se operace Receive dá provést na základě předplatného (nebo jeho nedoručené ***zprávy*** ). Vzhledem k tomu, že předplatné bydlí v rámci kontextu tématu, je operace přijetí provedena přechodem do Průzkumníka Service Bus pro dané téma.

> [!IMPORTANT]
> Počítejte s tím, že operace Receive prováděná průzkumníkem Service Bus je ***destruktivní příjem***, tj. zpráva je odebrána z fronty, když se zobrazí v nástroji Service Bus Explorer.
>
> Pokud chcete procházet zprávy bez jejich odebrání z fronty, zvažte použití funkce ***prohlížení*** .
>

1. Klikněte na kartu ***přijmout*** a vyberte konkrétní ***předplatné*** z rozevíracího selektoru.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="Výběr entity":::

2. Vyberte si z ***předplatného*** nebo z dílčí entity ***nedoručených zpráv*** .

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="Výběr entity":::

3. Klikněte na tlačítko ***přijmout*** a pak na ***Ano*** a potvrďte operaci přijmout a odstranit.

Po úspěšném dokončení operace Receive se v mřížce zobrazí přijatá zpráva, jak je uvedeno níže. Chcete-li zobrazit podrobnosti zprávy, klikněte na zprávu.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Výběr entity":::

### <a name="peeking-a-message-from-a-subscription"></a>Prohlížení zprávy z předplatného

Chcete-li jednoduše procházet zprávy v rámci předplatného nebo její podentita s nedoručenými zprávami, můžete také využít funkci ***prohlížení*** v rámci předplatného.

1. Klikněte na kartu ***Náhled*** a v rozevíracím selektoru vyberte konkrétní ***předplatné*** .

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="Výběr entity":::

2. Vyberte si mezi ***předplatným*** nebo podentitou ***nedoručených zpráv*** .

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="Výběr entity":::

3. Klikněte na tlačítko ***Náhled*** .

Po dokončení operace prohlížet se v mřížce zobrazí až 32 zpráv, jak je uvedeno níže. Chcete-li zobrazit podrobnosti konkrétní zprávy, vyberte ji z mřížky. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="Výběr entity":::

> [!NOTE]
>
> Vzhledem k tomu, že Náhled není destruktivní operace, zpráva **nebude** odebrána z fronty.
>

## <a name="next-steps"></a>Další kroky

   * Další informace o Service Bus [frontách](service-bus-queues-topics-subscriptions.md#queues) a [tématech](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * Další informace o vytváření [front Service Bus pomocí Azure Portal](service-bus-quickstart-portal.md)
   * Další informace o vytváření [Service Bus témata a předplatných prostřednictvím Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md)
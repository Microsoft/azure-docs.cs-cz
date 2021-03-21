---
title: Použití Průzkumníka Azure Service Bus k provádění operací s daty v Service Bus (Preview)
description: Tento článek poskytuje informace o tom, jak používat Azure Service Bus Průzkumník na portálu pro přístup k datům Azure Service Bus.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 0b5274c492a1dfb2523c52d7aea2b7ebf8eae675
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738952"
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

## <a name="prerequisites"></a>Předpoklady

Chcete-li použít nástroj Service Bus Explorer, budete muset provést následující úlohy: 

- Zřídí obor názvů Azure Service Bus.
- Vytvořte frontu pro odeslání a přijetí zprávy z nebo tématu s předplatným, abyste mohli otestovat funkčnost. Informace o tom, jak vytvořit fronty, témata a odběry, najdete v následujících článcích: 
    - [Rychlý Start – vytvoření front](service-bus-quickstart-portal.md)
    - [Rychlý Start – vytvoření témat](service-bus-quickstart-topics-subscriptions-portal.md)
- Ujistěte se, že jste členem jedné z těchto rolí v oboru názvů: 
    - [Service Bus vlastník dat](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner) 
    - [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) 
    - [Vlastník](../role-based-access-control/built-in-roles.md#owner)


## <a name="using-the-service-bus-explorer"></a>Použití Průzkumníka Service Bus

Chcete-li použít Průzkumníka Azure Service Bus, je nutné přejít na obor názvů Service Bus, na kterém chcete provádět operace odeslání, prohlížení a příjmu.

Pokud chcete provádět operace s frontou, vyberte v navigační nabídce **možnost fronty** . Pokud chcete provádět operace s tématem (a souvisejícími předplatnými), vyberte **témata**. 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png" alt-text="Výběr entity":::

Po výběru možnosti **fronty** nebo **témata** vyberte konkrétní frontu nebo téma.

V levé navigační nabídce vyberte **Service Bus Explorer (Preview).**

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="Levá navigační nabídka Průzkumníka SB":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>Odeslání zprávy do fronty nebo tématu

Pokud chcete odeslat zprávu do **fronty** nebo **tématu**, klikněte na kartu **_Odeslat_** v Průzkumníkovi Service Bus.

Vytvoření zprávy sem – 

1. Vyberte **typ obsahu** , který se bude nacházet buď jako "text/prostý", "Application/XML" nebo "Application/JSON".
2. Přidejte **obsah** zprávy. Zajistěte, aby odpovídaly dříve nastavenému **typu obsahu** .
3. Nastavení **rozšířených vlastností** (volitelné) – patří mezi ně ID korelace, ID zprávy, popisek, ReplyTo, doba TTL (Time to Live) a plánovaná doba zařazení do fronty (pro naplánované zprávy).
4. Nastavení **vlastních vlastností** – může to být jakákoli vlastnost uživatele nastavená na klíč slovníku.

Po sestavení zprávy stiskněte tlačítko Odeslat.

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="Napsat zprávu":::

Po úspěšném dokončení operace odeslání se 

* Při odesílání do fronty se zvýší čítač metrik **aktivních zpráv** .

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

* Při odesílání do tématu se čítač metrik **aktivních zpráv** zvýší na odběr, na který byla zpráva směrována.

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="TopicAfterSendMetrics":::

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

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Vyberte mezi podfrontou ***Queue** _ nebo _ *_nedoručených zpráv_**.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Kliknutím na tlačítko ***přijmout** _ následovaným znakem _ *_Ano_** potvrďte operaci přijmout a odstranit.


Po úspěšném dokončení operace Receive se v mřížce zobrazí podrobnosti zprávy, jak je uvedeno níže. Můžete vybrat zprávu z mřížky a zobrazit její podrobnosti.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="Snímek obrazovky okna fronty v Průzkumníkovi Azure Service Bus s podrobnostmi o zprávě zobrazené v aktivní zprávě, která je vybrána ve frontě.":::


### <a name="peeking-a-message-from-a-queue"></a>Prohlížení zprávy z fronty

Pomocí funkce prohlížení můžete pomocí Průzkumníka Service Bus zobrazit prvních 32 zpráv ve frontě nebo ve frontě nedoručených zpráv.

1. Chcete-li prohlížet zprávy ve frontě, klikněte na kartu ***Náhled*** v Průzkumníkovi Service Bus.

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="PeekTab":::

2. Podívejte se na metriky a zjistěte, jestli k prohlížení neexistují **aktivní zprávy** nebo **zprávy nedoručených zpráv** .

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="QueueAfterSendMetrics":::

3. Pak vyberte mezi **frontou * Queue** _ nebo _ *_nedoručených zpráv_**.

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="QueueOrDeadletter":::

4. Klikněte na tlačítko ***Náhled*** . 

Po dokončení operace prohlížet se v mřížce zobrazí až 32 zpráv, jak je uvedeno níže. Chcete-li zobrazit podrobnosti konkrétní zprávy, vyberte ji z mřížky. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="PeekMessageFromQueue":::

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

1. Klikněte na kartu ***přijmout** _ a vyberte konkrétní *_předplatné_* _ * z rozevíracího selektoru.

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="ReceiveTabSelected":::

2. Vyberte mezi ***předplatným** _ nebo _ *_nedoručených_* × subentity.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Kliknutím na tlačítko ***přijmout** _ následovaným znakem _ *_Ano_** potvrďte operaci přijmout a odstranit.

Po úspěšném dokončení operace Receive se v mřížce zobrazí přijatá zpráva, jak je uvedeno níže. Chcete-li zobrazit podrobnosti zprávy, klikněte na zprávu.

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="Snímek obrazovky s kartou Receive v Průzkumníkovi Azure Service Bus s podrobnostmi o zprávě zobrazenými v aktivní přijaté zprávě":::

### <a name="peeking-a-message-from-a-subscription"></a>Prohlížení zprávy z předplatného

Chcete-li jednoduše procházet zprávy v rámci předplatného nebo její podentita s nedoručenými zprávami, můžete také využít funkci ***prohlížení*** v rámci předplatného.

1. Klikněte na kartu ***Náhled** _ a vyberte konkrétní *_předplatné_* _ * z rozevíracího selektoru.

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="PeekTabSelected":::

2. Vyberte mezi podentitou ***předplatné** _ nebo _ *_nedoručené_**.

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="SubscriptionOrDeadletter":::

3. Klikněte na tlačítko ***Náhled*** .

Po dokončení operace prohlížet se v mřížce zobrazí až 32 zpráv, jak je uvedeno níže. Chcete-li zobrazit podrobnosti konkrétní zprávy, vyberte ji z mřížky. 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="PeekMessageFromSubscription":::

> [!NOTE]
>
> Vzhledem k tomu, že Náhled není destruktivní operace, zpráva **nebude** odebrána z fronty.
>

## <a name="next-steps"></a>Další kroky

   * Další informace o Service Bus [frontách](service-bus-queues-topics-subscriptions.md#queues) a [tématech](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * Další informace o vytváření [front Service Bus pomocí Azure Portal](service-bus-quickstart-portal.md)
   * Další informace o vytváření [Service Bus témata a předplatných prostřednictvím Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md)
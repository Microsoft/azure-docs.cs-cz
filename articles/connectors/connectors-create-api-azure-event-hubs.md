---
title: Nastavení sledování událostí s Azure Event Hubs pro Azure Logic Apps | Microsoft Docs
description: Monitorování datových proudů pro příjem událostí a odesílání událostí s logic apps pomocí Azure Event Hubs
services: logic-apps
keywords: datový proud, monitorování událostí, služba event hubs
author: ecfan
manager: anneta
editor: ''
documentationcenter: ''
tags: connectors
ms.assetid: ''
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 8de56cd64f38791fb27d9bcce1e16641fb162c2f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Monitorování, příjem a odesílání událostí s konektorem služby Event Hubs

Chcete-li nastavit monitorování událostí tak, aby aplikace logiky můžete zjišťovat události, přijímat události a odesílat události, připojte se ke [centra událostí Azure](https://azure.microsoft.com/services/event-hubs) z aplikace logiky. Další informace o [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) a [fungování ceny pro konektory Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Požadavky

Než budete moct použít konektor služby Event Hubs, musíte mít tyto položky:

* [Azure Event Hubs obor názvů a centra událostí](../event-hubs/event-hubs-create.md)
* A [aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-event-hubs"></a>Připojení k Azure Event Hubs

Než se aplikace logiky k jakékoli služby, je nutné vytvořit [ *připojení* ](./connectors-overview.md) mezi svou aplikaci logiky a služby, pokud jste tak ještě neučinili. Toto připojení autorizuje aplikace logiky pro přístup k datům. Aplikace logiky pro přístup k Centrum událostí zkontrolujte svá oprávnění a získat připojovací řetězec pro obor názvů služby Event Hubs.

1.  Přihlaste se na web [Azure Portal](https://portal.azure.com "Azure Portal"). 

2.  Přejděte do vašeho centra událostí *obor názvů*, nikoli konkrétní Centrum událostí. Na stránce obor názvů v části **nastavení**, zvolte **zásady sdíleného přístupu**. V části **deklarace identity**, zkontrolujte, zda máte **spravovat** oprávnění pro tento obor názvů.

    ![Správa oprávnění pro obor názvů centra událostí](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Pokud chcete zadat informace o připojení později ručně, získáte připojovací řetězec pro obor názvů služby Event Hubs. Zvolte **RootManageSharedAccessKey**. Vedle primární klíče připojovacího řetězce vyberte tlačítko Kopírovat. Uložte připojovací řetězec pro pozdější použití.

    ![Zkopírujte Event Hubs obor názvů připojovacího řetězce](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Chcete-li ověřit, zda je připojovací řetězec přidružené vašeho oboru názvů služby Event Hubs nebo s konkrétní události rozbočovače, zkontrolujte připojovací řetězec pro `EntityPath` parametr. Pokud zjistíte, tento parametr, připojovací řetězec je pro konkrétní Centrum událostí "entita" a není správný řetězec, který má používat s svou aplikaci logiky.

## <a name="trigger-workflow-when-your-event-hub-gets-new-events"></a>Aktivují pracovní postup, když se Centrum událostí získá nové události

A [ *aktivační událost* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) je událost, která se spouští v aplikaci logiky pracovního postupu. Spuštění pracovního postupu odeslání nové události do vašeho centra událostí, postupujte podle těchto kroků pro přidání aktivační událost, která zjistí tuto událost.

1. V [portál Azure](https://portal.azure.com "portál Azure"), přejděte do existující aplikace logiky nebo vytvoření aplikace logiky prázdné.

2. V návrháři aplikace logiky zadejte do vyhledávacího pole "event hubs" jako filtr. Vyberte této aktivační události: **když jsou události dostupné v Centru událostí**

   ![Vyberte aktivační událost pro Jakmile Centrum událostí obdrží nové události](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

   1. Pokud již nemáte připojení k oboru názvů služby Event Hubs, se zobrazí výzva k vytvoření nyní toto připojení. Zadejte název připojení a vyberte obor názvů služby Event Hubs, který chcete použít.

      ![Vytvoření připojení k Centru událostí](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

      Nebo, pokud chcete ručně zadat připojovací řetězec, zvolte **ručně zadat informace o připojení**. 
      Další informace [jak najít připojovací řetězec](#permissions-connection-string).

   2. Nyní vyberte zásadu Event Hubs, a klikněte na **vytvořit**.

      ![Vytvoření připojení k Centru událostí, část 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Vyberte centra událostí, monitorování a nastavte interval a četnost pro kdy mají být vyhledávána centra událostí.

    ![Zadejte centra událostí nebo skupiny uživatelů](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)
    
    > [!NOTE]
    > Všechny aktivační události centra událostí jsou *dlouho dotazování* aktivační události, které znamená, že když aktivační událost se aktivuje, aktivační událost zpracovává všechny události a potom počká, než 30 sekund pro další události se zobrazí události rozbočovače.
    > Pokud žádné události přijme 30 sekund, spustit aktivační události je přeskočeno. Aktivační událost, jinak hodnota pokračuje v načítání událostí až do centra událostí je prázdný.
    > Další cyklického dotazování aktivační události je založena na opakování intervalu zadaném ve vlastnosti aktivační události.


4. Chcete-li volitelně vyberte některé z možností pokročilé aktivační událost, zvolte **zobrazit rozšířené možnosti**.

    ![Aktivační událost rozšířené možnosti](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

    | Vlastnost | Podrobnosti |
    | --- | --- |
    | Typ obsahu  |V rozevíracím seznamu vyberte typ obsahu, který se události. Ve výchozím nastavení je zaškrtnuté application/octet-stream. |
    | Obsahu schématu |Zadejte schéma obsahu ve formátu JSON pro události, které se načítají z centra událostí. |
    | Název skupiny uživatelů |Zadejte centra událostí [název skupiny příjemců](../event-hubs/event-hubs-features.md#consumer-groups) číst události. Když není zadaný název skupiny uživatelů, použije se výchozí skupina příjemců. |
    | Klíč oddílu minimální |Zadejte minimální [oddílu](../event-hubs/event-hubs-features.md#partitions) ID ke čtení. Ve výchozím nastavení jsou všechny oddíly číst. |
    | Klíč oddílu maximální |Zadejte maximální počet [oddílu](../event-hubs/event-hubs-features.md#partitions) ID ke čtení. Ve výchozím nastavení jsou všechny oddíly číst. |
    | Počet maximální událostí |Zadejte hodnotu pro maximální počet událostí. Aktivační událost vrátí mezi jeden a počet událostí této vlastnosti. |
    |||

5. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

Teď když aplikace logiky ověří vybrané centra událostí a najde nové události, aktivační událost spustí akce v aplikaci logiky nachází události.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Odesílání událostí do vašeho centra událostí z aplikace logiky

[*Akce*](../logic-apps/logic-apps-overview.md#logic-app-concepts) je úloha, kterou provádí pracovní postup vaší aplikace logiky. Když do aplikace logiky přidáte trigger, můžete přidat akci, která bude provádět určitou operaci s daty generovanými triggerem. Pro odeslání události do vašeho centra událostí z aplikace logiky, postupujte podle těchto kroků.

1. V návrháři aplikace logiky, vyberte v části aktivační událost, **nový krok** > **přidat akci**.

2. Do vyhledávacího pole zadejte "event hubs" jako filtr.
Vyberte tuto akci: **Event Hubs - odesílání událostí**

   ![Vyberte "Event Hubs – událost odeslání"](./media/connectors-create-api-azure-event-hubs/select-event-hubs-send-event-action.png)

3. Vyberte centra událostí, kam můžete odeslat událost. Potom zadejte obsah, události a další podrobnosti.

   ![Vyberte název centra událostí a poskytování obsahu událostí](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

4. Uložte svou aplikaci logiky.

Nyní jste nastavili akci, která odesílá události z aplikace logiky. 

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Další informace o aktivační události a akce definované těmito souboru Swagger a všechny omezení, podívejte se [connector – podrobnosti](/connectors/eventhubs/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o [ostatní konektory pro Azure Logic apps](../connectors/apis-list.md)
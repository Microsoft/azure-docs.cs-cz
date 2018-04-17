---
title: Nastavení zasílání zpráv s Azure Service Bus pro Azure Logic Apps | Microsoft Docs
description: Odesílat a přijímat zprávy s logic apps pomocí Azure Service Bus
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: d5a4760e1e0f38fd81fd779786985f5753d77eab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Odesílat a přijímat zprávy pomocí konektoru služby Azure Service Bus

Pokud chcete odesílat a přijímat zprávy pomocí aplikace logiky, připojte se ke [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Můžete provádět akce, například odeslání do fronty, odesílat do tématu, přijímat z fronty a přijímat z odběru. Další informace o [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) a [jak ceny funguje Logic Apps aktivuje](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Požadavky

Než budete moct použít konektor Service Bus, musíte mít tyto položky, které musí existovat ve stejném předplatném Azure tak, že jsou na sobě navzájem viditelný:

* A [oboru názvů Service Bus a zasílání zpráv entity, jako je například fronty](../service-bus-messaging/service-bus-create-namespace-portal.md)
* A [aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Připojení k Azure Service Bus

Než se aplikace logiky k jakékoli služby, je nutné vytvořit [ *připojení* ](./connectors-overview.md) mezi svou aplikaci logiky a služby, pokud jste tak ještě neučinili. Toto připojení autorizuje aplikace logiky pro přístup k datům. Aplikace logiky pro přístup k účtu služby Service Bus zkontrolujte svá oprávnění.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. Přejděte na Service Bus *obor názvů*, nikoli konkrétní "entity přenosu zpráv". Na stránce obor názvů v části **nastavení**, zvolte **zásady sdíleného přístupu**. V části **deklarace identity**, zkontrolujte, zda máte **spravovat** oprávnění pro tento obor názvů.

   ![Správa oprávnění pro obor názvů Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Pokud chcete zadat informace o připojení později ručně, získáte připojovací řetězec pro obor názvů sběrnice. Zvolte **RootManageSharedAccessKey**. Vedle primární klíče připojovacího řetězce vyberte tlačítko Kopírovat. Uložte připojovací řetězec pro pozdější použití.

   ![Zkopírujte připojovací řetězec sběrnice služeb obor názvů](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Chcete-li ověřit, zda je připojovací řetězec přidružené vašeho oboru názvů Service Bus nebo s konkrétní entitu, zkontrolujte připojovací řetězec pro `EntityPath` parametr. Pokud zjistíte, tento parametr, připojovací řetězec je pro konkrétní entitu a není správný řetězec, který má používat s svou aplikaci logiky.

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>Aktivují pracovní postup, když Service Bus získá nové zprávy

A [ *aktivační událost* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) je událost, která se spouští v aplikaci logiky pracovního postupu. Spuštění pracovního postupu odeslání nové zprávy do služby Service Bus, postupujte podle těchto kroků pro přidání aktivační událost, která rozpoznává tyto zprávy.

1. V [portál Azure](https://portal.azure.com "portál Azure"), přejděte do existující aplikace logiky nebo vytvoření aplikace logiky prázdné.

2. V návrháři aplikace logiky zadejte do vyhledávacího pole jako filtr "service bus". Vyberte **Service Bus** konektor. 

   ![Vyberte konektor služby Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. Vyberte aktivační událost, která chcete použít. Můžete například spustit aplikace logiky po vytvoření nové položky se odešlou do fronty Service Bus, vybrat možnost této aktivační události: **Service Bus - při příjmu zprávy ve frontě (automatické dokončování)**

   ![Vyberte aktivační události služby Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Některé se aktivuje návratový jeden nebo zprávy, jako *Service Bus - při obdržení jeden nebo více zpráv ve frontě (automatické dokončování)* aktivační událost.
   > Pokud tyto triggery fire, vracejí mezi jeden a počet zpráv zadaný má aktivační procedura **maximální počet zpráv** vlastnost.

   1. Pokud již nemáte připojení k oboru názvů Service Bus, se zobrazí výzva k vytvoření nyní toto připojení. Zadejte název připojení a vyberte obor názvů Service Bus, který chcete použít.

      ![Vytvoření připojení k Service Bus](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Nebo, pokud chcete ručně zadat připojovací řetězec, zvolte **ručně zadat informace o připojení**. 
      Další informace [jak najít připojovací řetězec](#permissions-connection-string).
      

   2. Nyní vyberte zásadu Service Bus, a klikněte na **vytvořit**.

      ![Vytvoření připojení k Service Bus, část 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. Vyberte fronty Service Bus použít, a nastavte interval a četnost pro doby kontroly fronty.

   ![Vybrat fronty Service Bus, nastavení intervalu dotazování](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

   > [!NOTE]
   > Všechny aktivační události služby Service Bus jsou **dlouho dotazování** aktivační události, které znamená, že když aktivační událost se aktivuje, aktivační událost zpracovávat všechny zprávy a potom počká, než 30 sekund pro další zprávy zobrazit v rámci předplatného fronta nebo téma.
   > Pokud nejsou přijaty žádné zprávy v 30 sekund, je přeskočen spustit aktivační událost. Aktivační událost, jinak hodnota pokračuje čtení zpráv, dokud se fronta nebo téma předplatné je prázdné.
   > Další cyklického dotazování aktivační události je založena na opakování intervalu zadaném ve vlastnosti aktivační události.

5. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

Teď když aplikace logiky ověří vybrané frontu a najde nové zprávy, aktivační událost spustí akce v aplikaci logiky nalezena zpráva.

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>Odesílání zpráv z aplikace logiky k Service Bus

[*Akce*](../logic-apps/logic-apps-overview.md#logic-app-concepts) je úloha, kterou provádí pracovní postup vaší aplikace logiky. Když do aplikace logiky přidáte trigger, můžete přidat akci, která bude provádět určitou operaci s daty generovanými triggerem. K odeslání zprávy do vaší služby Service Bus zasílání zpráv entita z aplikace logiky, postupujte podle těchto kroků.

1. V návrháři aplikace logiky, vyberte v části aktivační událost, **+ nový krok** > **přidat akci**.

2. Do vyhledávacího pole zadejte "service bus" jako filtr. Vyberte tento konektor: **Service Bus**

   ![Vyberte konektor služby Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. Vyberte tuto akci: **Service Bus – odesílání zprávy**

   ![Vyberte "Service Bus – odesílání zprávy"](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. Vyberte entity zasílání zpráv, což je název fronta nebo téma o tom, kde k odeslání zprávy. Potom zadejte obsah zprávy a další podrobnosti.

   ![Vyberte entity zasílání zpráv a zadejte podrobnosti zprávy](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. Uložte svou aplikaci logiky. 

Nyní jste nastavili akci, která odesílá zprávy z vaší aplikace logiky. 

## <a name="connector-specific-details"></a>Podrobnosti o konkrétní konektor

Další informace o aktivační události a akce definované těmito souboru Swagger a všechny omezení, podívejte se [connector – podrobnosti](/connectors/servicebus/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o [ostatní konektory pro Azure Logic apps](../connectors/apis-list.md)
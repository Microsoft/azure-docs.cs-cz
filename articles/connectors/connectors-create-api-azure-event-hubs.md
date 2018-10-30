---
title: Připojení k Azure Event Hubs – Azure Logic Apps | Dokumentace Microsoftu
description: Správa a sledování událostí pomocí Azure Event Hubs a Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: a91daf08a56470e4d1e112e37b51150c2c5f00ef
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230314"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorování, příjem a odesílání událostí pomocí Azure Event Hubs a Azure Logic Apps 

Tento článek popisuje, jak můžete monitorovat a spravovat událostí odeslaných do [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) z uvnitř aplikace logiky pomocí konektoru služby Azure Event Hubs. Tímto způsobem můžete vytvořit aplikace logiky pro automatizaci úloh a pracovních postupů pro kontrolu, odesílání a přijímání událostí z centra událostí. 

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Technické informace specifické pro konektor najdete v tématu <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">referenční informace ke konektorům Azure Event Hubs</a>.

## <a name="prerequisites"></a>Požadavky

* [Oboru názvů Azure Event Hubs a centra událostí](../event-hubs/event-hubs-create.md)

* Aplikace logiky, ve které chcete přístup k Centru událostí. Spuštění aplikace logiky se aktivační událost Azure Event Hubs, je nutné [prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Zkontrolujte oprávnění a získání připojovacího řetězce

Pro aplikace logiky pro přístup k Centru událostí zkontrolujte svoje oprávnění a získání připojovacího řetězce pro váš obor názvů služby Event Hubs.

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. 

2. Přejděte do služby Event Hubs *obor názvů*, nikoli konkrétní Centrum událostí. Na stránce obor názvů v rámci **nastavení**, zvolte **zásady sdíleného přístupu**. V části **deklarace identity**, zkontrolujte, jestli máte **spravovat** oprávnění pro tento obor názvů.

   ![Spravovat oprávnění pro váš obor názvů centra událostí](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Pokud chcete zadat informace o připojení později ručně, získání připojovacího řetězce pro váš obor názvů služby Event Hubs. 

   1. V části **zásady**, zvolte **RootManageSharedAccessKey**. 

   2. Najdete váš primární klíč připojovacího řetězce. Klikněte na tlačítko kopírování a uložení připojovacího řetězce pro pozdější použití.

      ![Zkopírujte připojovací řetězec služby Event Hubs oboru názvů](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Potvrďte, zda je připojovací řetězec přidružený váš obor názvů služby Event Hubs nebo s konkrétní Centrum událostí, ujistěte se, že připojovací řetězec nemá `EntityPath`  parametru. Pokud tento parametr najít, připojovací řetězec je pro konkrétní Centrum událostí "osoba" a není správný řetězec, který má používat s aplikací logiky.

4. Teď pokračujte [přidat aktivační událost služby Event Hubs](#add-trigger) nebo [přidat akci služby Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Přidání triggeru Event Hubs

V Azure Logic Apps, musí začínat každá aplikace logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje při určité události nebo když je splněna konkrétní podmínka. Pokaždé, když se trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup vaší aplikace.

Tento příklad ukazuje, jak spustit pracovní postup aplikace logiky odeslání nové události do vašeho centra událostí. 

1. Webu Azure portal nebo Visual Studio vytvořte prázdné aplikace logiky, otevře se návrhář pro Logic Apps. Tento příklad používá na webu Azure portal.

2. Do vyhledávacího pole zadejte jako filtr "event hubs". Ze seznamu triggerů vyberte trigger, který chcete. 

   Tento příklad používá tento trigger: 
   **Event Hubs – když jsou události dostupné v Centru událostí**

   ![Vybrat trigger](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení služby Event Hubs](#create-connection). Nebo, pokud už připojení existuje, zadejte informace potřebné pro aktivační událost.

   1. Z **název centra událostí** vyberte Centrum událostí, které chcete monitorovat.

      ![Určete Centrum událostí nebo skupina uživatelů](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Vyberte interval a frekvenci jak často chcete, aby aktivační událost ke kontrole centra událostí.
 
   3. Chcete-li volitelně vyberte některé možnosti pokročilé triggeru, zvolte **zobrazit pokročilé možnosti**.
   
      ![Pokročilá nastavení aktivační události](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Vlastnost | Podrobnosti | 
      |----------|---------| 
      | Typ obsahu  | Vyberte typ obsahu události. Výchozí hodnota je "application/octet-stream". |
      | Schéma obsahu | Zadejte schéma obsahu JSON pro události, které se načítají z centra událostí. |
      | Název skupiny uživatelů | Zadejte centra událostí [název skupiny příjemců](../event-hubs/event-hubs-features.md#consumer-groups) pro čtení událostí. Pokud není zadán, je použít výchozí skupinu příjemců. |
      | Klíč oddílu minimální | Zadejte minimální [oddílu](../event-hubs/event-hubs-features.md#partitions) ID ke čtení. Všechny oddíly jsou ve výchozím nastavení, pro čtení. |
      | Klíč oddílu maximální | Zadejte maximální [oddílu](../event-hubs/event-hubs-features.md#partitions) ID ke čtení. Všechny oddíly jsou ve výchozím nastavení, pro čtení. |
      | Události maximální počet | Zadejte hodnotu pro maximální počet událostí. Aktivační událost vrátí rozsahu od 1 do počtu událostí této vlastnosti. |
      |||

4. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

5. Teď pokračujte v přidávání jednu nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky aktivační události.

> [!NOTE]
> Všechny aktivační události centra událostí jsou *s dlouhým intervalem dotazování* aktivačních událostí, což znamená, že když se trigger aktivuje, aktivační událost zpracovává všechny události a potom počká 30 sekund pro další události se zobrazí v Centru událostí.
> Pokud žádné události nejsou přijímána v 30 sekund, spuštění aktivační události je přeskočeno. V opačném případě se aktivační událost pokračuje v čtení události do vašeho centra událostí je prázdný.
> Další cyklického dotazování aktivační událost se stane, na základě na, který zadáte v aktivační události vlastnosti interval opakování.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Přidání akce služby Event Hubs

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná triggerem Event Hubs, která vyhledává nové události do vašeho centra událostí. 

1. Webu Azure portal nebo Visual Studio otevřete aplikaci logiky v návrháři pro Logic Apps. Tento příklad používá na webu Azure portal.

2. V části triggeru nebo akce, zvolte **nový krok** > **přidat akci**.

   Přidání akce mezi stávající kroky, najeďte myší na připojení šipku. 
   Vyberte znaménko plus (**+**), který se zobrazí a klikněte na tlačítko **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "event hubs".
Ze seznamu akcí vyberte požadovanou akci. 

   V tomto příkladu vyberte tuto akci: **Event Hubs – odeslání události**

   ![Vyberte "Event Hubs – událost odeslání"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení služby Event Hubs](#create-connection). Nebo pokud už připojení existuje, uveďte potřebné informace pro akci.

   | Vlastnost | Požaduje se | Popis | 
   |----------|----------|-------------|
   | Název centra událostí | Ano | Vyberte Centrum událostí, ve které chcete odesílat události | 
   | Událost obsahu | Ne | Obsah události, kterou chcete odeslat | 
   | Vlastnosti | Ne | Vlastnosti aplikace a hodnoty k odeslání | 
   |||| 

   Příklad: 

   ![Vyberte název centra událostí a poskytování obsahu události](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Připojte se do vašeho centra událostí

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Po zobrazení výzvy pro informace o připojení, uveďte následující údaje:

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|----------|-------|-------------|
   | Název připojení | Ano | <*Název připojení*> | Název pro vytvoření připojení |
   | Obor názvů služby Event Hubs | Ano | <*obor názvů Event hubs*> | Vyberte obor názvů služby Event Hubs, kterou chcete použít. | 
   |||||  

   Příklad:

   ![Vytvoření připojení k Centru událostí](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Chcete-li ručně zadat připojovací řetězec, zvolte **ručně zadat informace o připojení**. 
   Přečtěte si [jak zjistit připojovací řetězec](#permissions-connection-string).

2. Vyberte zásady služby Event Hubs chcete použít, pokud ještě není vybraná. Zvolte **Vytvořit**.

   ![Vytvoření připojení k Centru událostí, část 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po vytvoření připojení pokračujte [přidat služby Event Hubs aktivovat](#add-trigger) nebo [akci přidat služby Event Hubs](#add-action).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete v článku [konektoru referenční stránce](/connectors/eventhubs/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
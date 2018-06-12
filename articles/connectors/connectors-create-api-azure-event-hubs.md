---
title: Připojení k Azure Event Hubs - Azure Logic Apps | Microsoft Docs
description: Správa a sledování událostí s Azure Event Hubs a Azure Logic Apps
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
ms.openlocfilehash: 86fc1c3542bea1be840041bb73df15631c066c7e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294968"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorování, příjem a odesílání události s Azure Event Hubs a Azure Logic Apps 

Tento článek ukazuje, jak můžete sledovat a spravovat událostí odeslaných do [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) z uvnitř aplikaci logiky s konektorem služby Azure Event Hubs. Tímto způsobem můžete vytvořit aplikace logiky, které automatizují úlohy a pracovních postupů pro kontrolu, odesílání a přijímání událostí z vašeho centra událostí. 

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. Pokud jste nové aplikace logiky, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Technické informace specifické pro konektor najdete v tématu <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">odkaz konektor Azure Event Hubs</a>.

## <a name="prerequisites"></a>Požadavky

* [Azure Event Hubs obor názvů a centra událostí](../event-hubs/event-hubs-create.md)

* Aplikace logiky, kde chcete získat přístup vašeho centra událostí. Chcete-li spustit aplikaci logiky s aktivační procedury Azure Event Hubs, musíte [aplikace logiky prázdné](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Zkontrolujte oprávnění a získat připojovací řetězec

Aplikace logiky pro přístup k Centrum událostí zkontrolujte svá oprávnění a získat připojovací řetězec pro obor názvů služby Event Hubs.

1. Přihlaste se k <a href="https://portal.azure.com" target="_blank">portálu Azure</a>. 

2. Přejděte do vašeho centra událostí *obor názvů*, nikoli konkrétní Centrum událostí. Na stránce obor názvů v části **nastavení**, zvolte **zásady sdíleného přístupu**. V části **deklarace identity**, zkontrolujte, zda máte **spravovat** oprávnění pro tento obor názvů.

   ![Správa oprávnění pro obor názvů centra událostí](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Pokud chcete zadat informace o připojení později ručně, získáte připojovací řetězec pro obor názvů služby Event Hubs. 

   1. V části **zásad**, zvolte **RootManageSharedAccessKey**. 

   2. Najděte připojovací řetězec primární klíč. Zvolte tlačítko Kopírovat a uložit připojovací řetězec pro pozdější použití.

      ![Zkopírujte Event Hubs obor názvů připojovacího řetězce](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Potvrďte, zda je připojovací řetězec přidružené vašeho oboru názvů služby Event Hubs nebo s konkrétní události rozbočovače, ujistěte se, nemá připojovací řetězec `EntityPath` parametr. Pokud zjistíte, tento parametr, připojovací řetězec je pro konkrétní Centrum událostí "entita" a není správný řetězec, který má používat s svou aplikaci logiky.

4. Teď pokračujte [přidat aktivační procedury Event Hubs](#add-trigger) nebo [přidat akci Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Přidat aktivační události Event Hubs

V Azure Logic Apps, musí začínat každou aplikaci logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se stane, aktivuje se při určité události, nebo když je splněna určitá podmínka. Pokaždé, když aktivační událost je aktivována, modul Logic Apps vytvoří instanci aplikace logiky a spuštění pracovního postupu vaší aplikace.

Tento příklad ukazuje, jak můžete spustit pracovní postup aplikace logiky, odeslání nové události do vašeho centra událostí. 

1. V portálu Azure nebo v sadě Visual Studio vytvořte prázdnou logiku aplikace, která otevře Návrhář aplikace logiky. Tento příklad používá portál Azure.

2. Do vyhledávacího pole zadejte "event hubs" jako filtr. Ze seznamu aktivační události vyberte aktivační události, které chcete. 

   Tento příklad používá této aktivační události: **Event Hubs – když jsou události dostupné v Centru událostí**

   ![Vybrat trigger](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Pokud se zobrazí výzva k podrobnosti připojení [vytvořit připojením Event Hubs](#create-connection). Nebo, pokud připojení již existuje, zadejte informace potřebné pro aktivační událost.

   1. Z **název centra událostí** vyberte centra událostí, kterou chcete sledovat.

      ![Zadejte centra událostí nebo skupiny uživatelů](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Vyberte interval a četnost pro požadovanou četnost aktivační událost zkontrolujte centra událostí.
 
   3. Chcete-li volitelně vyberte některé z možností pokročilé aktivační událost, zvolte **zobrazit rozšířené možnosti**.
   
      ![Aktivační událost rozšířené možnosti](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Vlastnost | Podrobnosti | 
      |----------|---------| 
      | Typ obsahu  | Vyberte typ obsahu k události. Výchozí hodnota je "application/octet-stream". |
      | Obsahu schématu | Zadejte schéma obsahu ve formátu JSON pro události, které se načítají z centra událostí. |
      | Název skupiny uživatelů | Zadejte centra událostí [název skupiny příjemců](../event-hubs/event-hubs-features.md#consumer-groups) pro čtení událostí. Pokud není zadaný, použije se výchozí skupina příjemců. |
      | Klíč oddílu minimální | Zadejte minimální [oddílu](../event-hubs/event-hubs-features.md#partitions) ID ke čtení. Ve výchozím nastavení jsou všechny oddíly číst. |
      | Klíč oddílu maximální | Zadejte maximální počet [oddílu](../event-hubs/event-hubs-features.md#partitions) ID ke čtení. Ve výchozím nastavení jsou všechny oddíly číst. |
      | Počet maximální událostí | Zadejte hodnotu pro maximální počet událostí. Aktivační událost vrátí mezi jeden a počet událostí této vlastnosti. |
      |||

4. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**.

5. Teď pokračujte v přidávání jednoho nebo více akcí do aplikace logiky pro úlohy, které chcete provést ve výsledcích aktivační události.

> [!NOTE]
> Všechny aktivační události centra událostí jsou *dlouho dotazování* aktivační události, které znamená, že když aktivační událost se aktivuje, aktivační událost zpracovává všechny události a potom počká, než 30 sekund pro další události se zobrazí v Centru událostí.
> Pokud žádné události přijme 30 sekund, spustit aktivační události je přeskočeno. Aktivační událost, jinak hodnota pokračuje v načítání událostí až do vašeho centra událostí je prázdný.
> Další cyklického dotazování aktivační události dochází na základě interval opakování, který určíte ve vlastnostech aktivační události.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Přidat akci Event Hubs

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok ve svém pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná Event Hubs aktivační událost, která vyhledává nové události v Centru událostí. 

1. V portálu Azure nebo Visual Studio otevřete aplikaci logiky v návrháři aplikace logiky. Tento příklad používá portál Azure.

2. V části o aktivační události nebo akce, zvolte **nový krok** > **přidat akci**.

   A přidejte akci mezi existující kroky, přesuňte ukazatel myši nad připojování šipkou. 
   Vyberte znaménko plus (**+**), se zobrazí a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte "event hubs" jako filtr.
Ze seznamu Akce vyberte akci, kterou chcete. 

   V tomto příkladu vyberte tuto akci: **Event Hubs - odesílání událostí**

   ![Vyberte "Event Hubs – událost odeslání"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Pokud se zobrazí výzva k podrobnosti připojení [vytvořit připojením Event Hubs](#create-connection). Nebo, pokud připojení již existuje, zadejte informace potřebné pro akci.

   | Vlastnost | Požaduje se | Popis | 
   |----------|----------|-------------|
   | Název centra událostí | Ano | Vyberte centra událostí, ve které chcete odeslat událost | 
   | Obsah událostí | Ne | Obsah pro události, kterou chcete odeslat | 
   | Vlastnosti | Ne | Vlastnosti aplikace a hodnoty pro odesílání | 
   |||| 

   Příklad: 

   ![Vyberte název centra událostí a poskytování obsahu událostí](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Připojení do vašeho centra událostí

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Když se zobrazí výzva k informace o připojení, uveďte následující informace:

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|----------|-------|-------------|
   | Název připojení | Ano | <*Název připojení*> | Název, který chcete vytvořit pro připojení k |
   | Obor názvů služby Event Hubs | Ano | <*názvový prostor události rozbočovače*> | Vyberte obor názvů služby Event Hubs, které chcete použít. | 
   |||||  

   Příklad:

   ![Vytvoření připojení k Centru událostí](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Chcete-li ručně zadat připojovací řetězec, zvolte **ručně zadat informace o připojení**. 
   Další informace [jak najít připojovací řetězec](#permissions-connection-string).

2. Vyberte zásady služby Event Hubs chcete použít, pokud ještě nebyla vybrána. Zvolte **Vytvořit**.

   ![Vytvoření připojení k Centru událostí, část 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po vytvoření připojení, pokračujte [aktivovat přidat Event Hubs](#add-trigger) nebo [přidat Event Hubs akce](#add-action).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako je například aktivačních událostí, akcí a omezení, jak je popsáno pomocí souboru Swagger konektoru, najdete v článku [stránka s referencemi k konektoru](/connectors/eventhubs/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
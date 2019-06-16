---
title: Připojení k Azure Event Hubs – Azure Logic Apps
description: Správa a sledování událostí pomocí Azure Event Hubs a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 882bae14678d8bfff15b35c63c666a20aeee3d1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720043"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorování, příjem a odesílání událostí pomocí Azure Event Hubs a Azure Logic Apps

Tento článek popisuje, jak můžete monitorovat a spravovat událostí odeslaných do [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) z uvnitř aplikace logiky pomocí konektoru služby Azure Event Hubs. Tímto způsobem můžete vytvořit aplikace logiky pro automatizaci úloh a pracovních postupů pro kontrolu, odesílání a přijímání událostí z centra událostí. Technické informace specifické pro konektor najdete v tématu [referenční informace ke konektorům Azure Event Hubs](https://docs.microsoft.com/connectors/eventhubs/)</a>.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* [Oboru názvů Azure Event Hubs a centra událostí](../event-hubs/event-hubs-create.md)

* Aplikace logiky, ve které chcete přístup k Centru událostí. Spuštění aplikace logiky se aktivační událost Azure Event Hubs, je nutné [prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Zkontrolujte oprávnění a získání připojovacího řetězce

Pro aplikace logiky pro přístup k Centru událostí zkontrolujte svoje oprávnění a získání připojovacího řetězce pro váš obor názvů služby Event Hubs.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Přejděte do služby Event Hubs *obor názvů*, nikoli konkrétní Centrum událostí. 

1. V nabídce oboru názvů v rámci **nastavení**vyberte **zásady sdíleného přístupu**. V části **deklarace identity**, zkontrolujte, jestli máte **spravovat** oprávnění pro tento obor názvů.

   ![Spravovat oprávnění pro váš obor názvů centra událostí](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Pokud chcete zadat informace o připojení později ručně, získání připojovacího řetězce pro váš obor názvů služby Event Hubs.

   1. V části **zásady**, zvolte **RootManageSharedAccessKey**.

   1. Najdete váš primární klíč připojovacího řetězce. Klikněte na tlačítko kopírování a uložení připojovacího řetězce pro pozdější použití.

      ![Zkopírujte připojovací řetězec služby Event Hubs oboru názvů](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Potvrďte, zda je připojovací řetězec přidružený váš obor názvů služby Event Hubs nebo s konkrétní Centrum událostí, ujistěte se, že připojovací řetězec nemá `EntityPath`  parametru. Pokud tento parametr najít, připojovací řetězec je pro konkrétní Centrum událostí "osoba" a není správný řetězec, který má používat s aplikací logiky.

1. Teď pokračujte [přidat aktivační událost služby Event Hubs](#add-trigger) nebo [přidat akci služby Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Přidání triggeru služby Event Hubs

V Azure Logic Apps, musí začínat každá aplikace logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje při určité události nebo když je splněna konkrétní podmínka. Pokaždé, když se trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup vaší aplikace.

Tento příklad ukazuje, jak spustit pracovní postup aplikace logiky odeslání nové události do vašeho centra událostí. 

1. Webu Azure portal nebo Visual Studio vytvořte prázdné aplikace logiky, otevře se návrhář pro Logic Apps. Tento příklad používá na webu Azure portal.

1. Do vyhledávacího pole zadejte jako filtr "event hubs". Ze seznamu triggerů vyberte tento trigger: **Když jsou události dostupné v Centru událostí – Event Hubs**

   ![Výběr triggeru](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení služby Event Hubs](#create-connection). 

1. V triggeru poskytují informace o Centru událostí, který chcete monitorovat. Další vlastnosti, otevřete **přidat nový parametr** seznamu. Vyberte parametr přidá tuto vlastnost na kartě aktivační události.

   ![Vlastnosti aktivační události](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název centra událostí** | Ano | Název centra událostí, který chcete monitorovat |
   | **Typ obsahu** | Ne | Typ obsahu události. Výchozí formát je `application/octet-stream`. |
   | **Název skupiny uživatelů** | Ne | [Název pro skupinu uživatelů centra událostí](../event-hubs/event-hubs-features.md#consumer-groups) pro čtení událostí. Pokud není zadán, je použít výchozí skupinu příjemců. |
   | **Události maximální počet** | Ne | Maximální počet událostí. Aktivační událost vrátí rozsahu od 1 do počtu událostí této vlastnosti. |
   | **Interval** | Ano | Kladné celé číslo, které popisují, jak často pracovní postup spouští na základě frekvence |
   | **Frekvence** | Ano | Jednotka času pro opakování |
   ||||

   **Další vlastnosti**

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Schéma obsahu** | Ne | Schéma obsahu JSON události ke čtení z centra událostí. Například pokud chcete zadat schéma obsahu, můžete aktivovat aplikaci logiky pouze pro události, které odpovídají schématu. |
   | **Klíč oddílu minimální** | Ne | Zadejte minimální [oddílu](../event-hubs/event-hubs-features.md#partitions) ID ke čtení. Všechny oddíly jsou ve výchozím nastavení, pro čtení. |
   | **Klíč oddílu maximální** | Ne | Zadejte maximální [oddílu](../event-hubs/event-hubs-features.md#partitions) ID ke čtení. Všechny oddíly jsou ve výchozím nastavení, pro čtení. |
   | **Časové pásmo** | Ne | Platí jenom v případě, že zadáte čas spuštění, protože tento trigger nepřijme posun UTC. Vyberte časové pásmo, které chcete použít. <p>Další informace najdete v tématu [vytvořit a spouštět opakované úlohy a pracovní postupy s Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Čas spuštění** | Ne | Zadejte čas spuštění v tomto formátu: <p>RRRR-MM-ddTHH Pokud vyberte časové pásmo<p>-nebo-<p>RRRR-MM-: ssZ Pokud nevyberete časové pásmo<p>Další informace najdete v tématu [vytvořit a spouštět opakované úlohy a pracovní postupy s Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

1. Teď pokračujte v přidávání jednu nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky aktivační události. 

   Například chcete-li filtrovat události podle konkrétní hodnoty, jako je například kategorie, můžete přidat podmínky tak, aby **odesílat události – Event Hubs** akce odešle jenom události, které splňují podmínku. 

> [!NOTE]
> Všechny aktivační události centra událostí jsou *s dlouhým intervalem dotazování* aktivačních událostí, což znamená, že když se trigger aktivuje, aktivační událost zpracovává všechny události a potom počká 30 sekund pro další události se zobrazí v Centru událostí.
> Pokud žádné události nejsou přijímána v 30 sekund, spuštění aktivační události je přeskočeno. V opačném případě se aktivační událost pokračuje v čtení události do vašeho centra událostí je prázdný.
> Další cyklického dotazování aktivační událost se stane, na základě na, který zadáte v aktivační události vlastnosti interval opakování.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Přidání akce služby Event Hubs

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná triggerem Event Hubs, která vyhledává nové události do vašeho centra událostí.

1. Webu Azure portal nebo Visual Studio otevřete aplikaci logiky v návrháři pro Logic Apps. Tento příklad používá na webu Azure portal.

1. V části triggeru nebo akce, zvolte **nový krok**.

   Přidání akce mezi stávající kroky, najeďte myší na připojení šipku. 
   Vyberte znaménko plus ( **+** ), který se zobrazí a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "event hubs".
Ze seznamu akcí vyberte tuto akci: **Odeslání události – Event Hubs**

   ![Vyberte akci "Odeslat událost"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení služby Event Hubs](#create-connection). 

1. V akci poskytují informace o událostech, které chcete odeslat. Další vlastnosti, otevřete **přidat nový parametr** seznamu. Vyberte parametr přidá tuto vlastnost na kartě akce.

   ![Vyberte název centra událostí a poskytování obsahu události](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název centra událostí** | Ano | Centrum událostí, ve které chcete odesílat události |
   | **Obsah** | Ne | Obsah události, kterou chcete odeslat |
   | **Vlastnosti** | Ne | Vlastnosti aplikace a hodnoty k odeslání |
   | **Klíč oddílu** | Ne | [Oddílu](../event-hubs/event-hubs-features.md#partitions) ID se má odeslat událost |
   ||||

   Můžete například odeslat výstup z trigger služby Event Hubs do jiného centra událostí:

   ![Odeslat příklad události](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Připojte se do vašeho centra událostí

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Po zobrazení výzvy pro informace o připojení, uveďte následující údaje:

   | Vlastnost | Požaduje se | Value | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Ano | <*Název připojení*> | Název pro vytvoření připojení |
   | **Event Hubs Namespace** | Ano | <*event-hubs-namespace*> | Vyberte obor názvů služby Event Hubs, kterou chcete použít. |
   |||||  

   Příklad:

   ![Vytvoření připojení k Centru událostí](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Chcete-li ručně zadat připojovací řetězec, zvolte **ručně zadat informace o připojení**. 
   Přečtěte si [jak zjistit připojovací řetězec](#permissions-connection-string).

2. Vyberte zásady služby Event Hubs chcete použít, pokud ještě není vybraná. Zvolte **Vytvořit**.

   ![Vytvoření připojení k Centru událostí, část 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po vytvoření připojení pokračujte [přidat služby Event Hubs aktivovat](#add-trigger) nebo [akci přidat služby Event Hubs](#add-action).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v konektoru OpenAPI (dříve Swagger) souboru, najdete v článku [konektoru referenční stránce](/connectors/eventhubs/).

## <a name="next-steps"></a>Další postup

Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
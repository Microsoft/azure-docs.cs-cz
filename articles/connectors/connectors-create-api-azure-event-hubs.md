---
title: Připojení k Centru událostí Azure
description: Vytvářejte automatizované úlohy a pracovní postupy, které monitorují a spravují události pomocí Azure Event Hubs a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 32fa54ef0d8eccaf8745ee37cb028d4f3c6d73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247290"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorování, příjem a odesílání událostí pomocí Azure Event Hubs a Azure Logic Apps

Tento článek ukazuje, jak můžete sledovat a spravovat události odeslané do [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) z aplikace logiky s konektorem Azure Event Hubs. Můžete tak vytvářet aplikace logiky, které automatizují úlohy a pracovní postupy kontroly, odesílání a příjmu událostí z centra událostí. Technické informace specifické pro konektor najdete v [tématu odkaz na](https://docs.microsoft.com/connectors/eventhubs/)</a>konektor centra událostí Azure .

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* [Obor názvů Centra událostí Azure a Centrum událostí](../event-hubs/event-hubs-create.md)

* Aplikace logiky, kde chcete získat přístup k centru událostí. Chcete-li spustit aplikaci logiky s aktivační událostí Azure Event Hubs, potřebujete [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a úvodní [příručka: Vytvořte si první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Kontrola oprávnění a získání připojovacího řetězce

Chcete-li se ujistit, že vaše aplikace logiky přístup k centru událostí, zkontrolujte oprávnění a získat připojovací řetězec pro obor názvů Event Hubs.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Přejděte do *oboru názvů*Centra událostí , ne do konkrétního centra událostí. 

1. V nabídce oboru názvů vyberte v části **Nastavení** **zásady sdíleného přístupu**. V části **Deklarace identity**zkontrolujte, zda máte **oprávnění Spravovat** pro tento obor názvů.

   ![Správa oprávnění pro obor názvů centra událostí](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Pokud chcete později ručně zadat informace o připojení, získejte připojovací řetězec pro obor názvů Event Hubs.

   1. V části **Zásady**zvolte **RootManageSharedAccessKey**.

   1. Najděte připojovací řetězec primárního klíče. Zvolte tlačítko kopírování a uložte připojovací řetězec pro pozdější použití.

      ![Kopírovat připojovací řetězec oboru názvů Centra událostí](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Chcete-li ověřit, zda je připojovací řetězec přidružený k oboru názvů Event Hubs nebo `EntityPath`  k určitému centru událostí, ujistěte se, že připojovací řetězec nemá parametr. Pokud najdete tento parametr, připojovací řetězec je pro konkrétní event hub "entity" a není správný řetězec pro použití s vaší aplikace logiky.

1. Nyní [pokračujte aktivací Přidat centra událostí](#add-trigger) nebo [přidat akci Centra událostí](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Aktivační událost Přidat centra událostí

V Azure Logic Apps musí každá aplikace logiky začínat [aktivační událostí](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se aktivuje, když dojde k určité události nebo když je splněna určitá podmínka. Pokaždé, když se spustí aktivační událost, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup aplikace.

Tento příklad ukazuje, jak můžete spustit pracovní postup aplikace logiky při odeslání nových událostí do centra událostí. 

1. Na webu Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře Návrhář aplikací logiky. Tento příklad používá portál Azure.

1. Do vyhledávacího pole zadejte jako filtr "centra událostí". Ze seznamu aktivačních událostí vyberte tuto aktivační událost: **Když jsou události k dispozici v Centru událostí – centra událostí**

   ![Vybrat aktivační událost](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte nyní připojení centra událostí](#create-connection). 

1. V aktivační události zadejte informace o centru událostí, které chcete sledovat. Další vlastnosti zobrazíte v seznamu **Přidat nový parametr.** Výběrem parametru přidáte tuto vlastnost na spouštěcí kartu.

   ![Vlastnosti aktivační události](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název centra událostí** | Ano | Název centra událostí, který chcete sledovat |
   | **Typ obsahu** | Ne | Typ obsahu události. Výchozí formát je `application/octet-stream`. |
   | **Název skupiny uživatelů** | Ne | [Název skupiny spotřebitelů centra událostí,](../event-hubs/event-hubs-features.md#consumer-groups) který se má použít pro čtení událostí. Pokud není zadán, použije se výchozí skupina příjemce. |
   | **Maximální počet událostí** | Ne | Maximální počet událostí. Aktivační událost vrátí mezi jeden a počet událostí určených touto vlastností. |
   | **Interval** | Ano | Kladné celé číslo, které popisuje, jak často se pracovní postup spouští na základě četnosti |
   | **Frequency** | Ano | Jednotka času pro opakování |
   ||||

   **Další vlastnosti**

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Schéma obsahu** | Ne | Schéma obsahu JSON pro události ke čtení z centra událostí. Pokud například zadáte schéma obsahu, můžete aktivovat aplikaci logiky pouze pro ty události, které odpovídají schématu. |
   | **Minimální klíč oddílu** | Ne | Zadejte minimální ID [oddílu](../event-hubs/event-hubs-features.md#partitions) ke čtení. Ve výchozím nastavení jsou čteny všechny oddíly. |
   | **Maximální klíč oddílu** | Ne | Zadejte maximální ID [oddílu](../event-hubs/event-hubs-features.md#partitions) ke čtení. Ve výchozím nastavení jsou čteny všechny oddíly. |
   | **Časové pásmo** | Ne | Platí pouze v případě, že zadáte počáteční čas, protože tato aktivační událost nepřijímá posun UTC. Vyberte časové pásmo, které chcete použít. <p>Další informace najdete [v tématu Vytváření a spouštění opakovaných úloh a pracovních postupů pomocí aplikací Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Čas spuštění** | Ne | Zadejte čas zahájení v tomto formátu: <p>YYYY-MM-DDThh:mm:ss, pokud vyberete časové pásmo<p>-nebo-<p>YYYY-MM-DDThh:mm:ssZ, pokud nevyberete časové pásmo<p>Další informace najdete [v tématu Vytváření a spouštění opakovaných úloh a pracovních postupů pomocí aplikací Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. Až budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

1. Teď pokračujte v přidávání jedné nebo více akcí do aplikace logiky pro úkoly, které chcete provést s výsledky aktivační události. 

   Chcete-li například filtrovat události na základě určité hodnoty, například kategorie, můžete přidat podmínku tak, aby akce **Odeslat událost** odeslala pouze události, které splňují vaši podmínku. 

> [!NOTE]
> Všechny aktivační události centra událostí jsou aktivační události *s dlouhým dotazováním,* což znamená, že když se aktivuje aktivační událost, aktivační událost zpracuje všechny události a pak čeká 30 sekund, než se v centru událostí zobrazí další události.
> Pokud nejsou přijaty žádné události za 30 sekund, spuštění aktivační události je přeskočeno. V opačném případě aktivační událost pokračuje čtení událostí, dokud vaše Centrum událostí je prázdný.
> K dalšímu dotazování aktivačních událostí dochází na základě intervalu opakování, který zadáte ve vlastnostech aktivační události.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Akce Přidat centra událostí

V Azure Logic Apps [je akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) krokem ve vašem pracovním postupu, který následuje po aktivační události nebo jiné akci. V tomto příkladu aplikace logiky začíná aktivační událost Centra událostí, která kontroluje nové události ve vašem Centru událostí.

1. Na webu Azure Portal nebo Visual Studiu otevřete aplikaci logiky v Návrháři logických aplikací. Tento příklad používá portál Azure.

1. Pod aktivační událostí nebo akcí zvolte **Nový krok**.

   Chcete-li přidat akci mezi existující kroky, přesuňte ukazatel myši na spojovací šipku. 
   Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Do vyhledávacího pole zadejte jako filtr "centra událostí".
Ze seznamu akcí vyberte tuto akci: **Odeslat událost – Centra událostí**

   ![Vybrat akci Odeslat událost](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte nyní připojení centra událostí](#create-connection). 

1. V akci zadejte informace o událostech, které chcete odeslat. Další vlastnosti zobrazíte v seznamu **Přidat nový parametr.** Výběrem parametru přidáte tuto vlastnost na kartu akce.

   ![Vyberte název centra událostí a zadejte obsah události.](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název centra událostí** | Ano | Centrum událostí, kam chcete událost odeslat |
   | **Obsah** | Ne | Obsah události, kterou chcete odeslat |
   | **Vlastnosti** | Ne | Vlastnosti a hodnoty aplikace k odeslání |
   | **Klíč oddílu** | Ne | ID [oddílu,](../event-hubs/event-hubs-features.md#partitions) kam chcete událost odeslat |
   ||||

   Můžete například odeslat výstup z aktivační události Centra událostí do jiného centra událostí:

   ![Příklad odeslat událost](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Až budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Připojení k centru událostí

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Až budete vyzváni k zadání informací o připojení, zadejte tyto podrobnosti:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Ano | <*název připojení*> | Název, který chcete vytvořit pro připojení |
   | **Obor názvů rozbočovačů událostí** | Ano | <*obor názvů event-hubs-center*> | Vyberte obor názvů Event Hubs, který chcete použít. |
   |||||  

   Například:

   ![Vytvořit připojení centra událostí](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Chcete-li připojovací řetězec zadat ručně, vyberte **ručně zadat informace o připojení**. 
   Přečtěte [si, jak najít připojovací řetězec](#permissions-connection-string).

2. Vyberte zásadu Centra událostí, která se má použít, pokud ještě není vybraná. Zvolte **Vytvořit**.

   ![Vytvořit připojení Centra událostí, část 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po vytvoření připojení pokračujte [aktivací Přidat centra událostí](#add-trigger) nebo Přidat akci Centra [událostí](#add-action).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou aktivační události, akce a omezení, jak je popsáno v souboru Swagger konektoru, naleznete [na referenční stránce konektoru](https://docs.microsoft.com/connectors/eventhubs/).

> [!NOTE]
> Pro aplikace logiky v [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), tento konektor je ise označené verze používá [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
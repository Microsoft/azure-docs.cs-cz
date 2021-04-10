---
title: Připojení k Azure Event Hubs
description: Vytváření automatizovaných úloh a pracovních postupů, které sledují a spravují události pomocí Azure Event Hubs a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 65da8e07c01561577fe7eff449bfc10348c7f277
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101716597"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorování, příjem a odesílání událostí s využitím služby Azure Event Hubs a Azure Logic Apps

V tomto článku se dozvíte, jak můžete monitorovat a spravovat události odeslané do [Azure Event Hubs](../event-hubs/event-hubs-about.md) z aplikace logiky pomocí konektoru Azure Event Hubs. Můžete tak vytvářet aplikace logiky, které automatizují úlohy a pracovní postupy kontroly, odesílání a příjmu událostí z centra událostí. Technické informace specifické pro konektor najdete v referenčních informacích k [Azure Event Hubs Connectoru](/connectors/eventhubs/) </a> .

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* [Obor názvů a centrum událostí pro Azure Event Hubs](../event-hubs/event-hubs-create.md)

* Aplikace logiky, ve které chcete získat přístup k centru událostí. Chcete-li spustit aplikaci logiky pomocí triggeru Event Hubs služby Azure, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Ověřte oprávnění a získejte připojovací řetězec.

Abyste se ujistili, že vaše aplikace logiky bude mít přístup k centru událostí, zkontrolujte svá oprávnění a získejte připojovací řetězec pro obor názvů Event Hubs.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Přejít na *obor názvů* Event Hubs, ne na konkrétní centrum událostí. 

1. V nabídce obor názvů v části **Nastavení** vyberte **zásady sdíleného přístupu**. V části **deklarace identity** ověřte, že máte oprávnění ke **správě** tohoto oboru názvů.

   ![Správa oprávnění pro obor názvů centra událostí](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Pokud budete chtít později ručně zadat informace o připojení, Získejte připojovací řetězec pro obor názvů Event Hubs.

   1. V části **zásady** vyberte **RootManageSharedAccessKey**.

   1. Najděte připojovací řetězec primárního klíče. Klikněte na tlačítko Kopírovat a uložte připojovací řetězec pro pozdější použití.

      ![Zkopírování připojovacího řetězce oboru názvů Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Pokud chcete ověřit, jestli je připojovací řetězec přidružený k vašemu oboru názvů Event Hubs nebo konkrétnímu centru událostí, ujistěte se, že připojovací řetězec nemá `EntityPath` parametr. Pokud tento parametr vyhledáte, připojovací řetězec bude pro konkrétní centrum událostí "entita" a není to správný řetězec pro použití s vaší aplikací logiky.

1. Nyní pokračujte [přidáním triggeru Event Hubs](#add-trigger) nebo [přidejte akci Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Přidání triggeru Event Hubs

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a začne spouštět pracovní postup vaší aplikace.

Tento příklad ukazuje, jak můžete spustit pracovní postup aplikace logiky při posílání nových událostí do centra událostí. 

> [!NOTE]
> Všechny triggery centra událostí jsou triggery *s dlouhým dotazem* , což znamená, že Trigger zpracuje všechny události a potom počká 30 sekund na oddíl, aby se v centru událostí zobrazily další události. Takže pokud je aktivační událost nastavená se čtyřmi oddíly, může zpoždění trvat až dvě minuty, než se Trigger dokončí dotazování všech oddílů. Pokud v rámci této prodlevy neobdrží žádné události, spuštění triggeru se přeskočí. V opačném případě aktivační událost pokračuje v čtení událostí, dokud vaše centrum událostí nebude prázdné. Následující dotaz na Trigger probíhá na základě intervalu opakování, který zadáte ve vlastnostech triggeru.

1. V Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře návrháře Logic Apps. V tomto příkladu se používá Azure Portal.

1. Do vyhledávacího pole zadejte jako filtr "centra událostí". V seznamu triggery vyberte tuto aktivační událost: **když jsou události k dispozici v centru událostí – Event Hubs**

   ![Vybrat aktivační událost](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, [Vytvořte si připojení Event Hubs nyní](#create-connection). 

1. V aktivační události zadejte informace o centru událostí, které chcete monitorovat. Pro další vlastnosti otevřete seznam **Přidat nový parametr** . Výběr parametru přidá tuto vlastnost na kartu triggeru.

   ![Vlastnosti aktivační události](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název centra událostí** | Yes | Název centra událostí, které chcete monitorovat |
   | **Typ obsahu** | No | Typ obsahu události Výchozí formát je `application/octet-stream`. |
   | **Název skupiny uživatelů** | No | [Název skupiny uživatelů centra událostí](../event-hubs/event-hubs-features.md#consumer-groups) , která se má použít pro čtení událostí. Pokud není zadaný, použije se výchozí skupina uživatelů. |
   | **Maximální počet událostí** | No | Maximální počet událostí. Aktivační událost se vrátí mezi 1 a počtem událostí určených touto vlastností. |
   | **Interval** | Yes | Kladné celé číslo, které popisuje, jak často se Workflow spouští na základě frekvence |
   | **Frekvence** | Yes | Jednotka času opakování |
   ||||

   **Další vlastnosti**

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Schéma obsahu** | No | Schéma obsahu JSON pro události, které se mají číst z centra událostí. Pokud například zadáte schéma obsahu, můžete spustit aplikaci logiky jenom pro události, které odpovídají schématu. |
   | **Minimální klíč oddílu** | No | Zadejte minimální ID [oddílu](../event-hubs/event-hubs-features.md#partitions) , který se má přečíst. Ve výchozím nastavení jsou všechny oddíly čteny. |
   | **Maximální klíč oddílu** | No | Zadejte maximální ID [oddílu](../event-hubs/event-hubs-features.md#partitions) , který se má přečíst. Ve výchozím nastavení jsou všechny oddíly čteny. |
   | **Časové pásmo** | No | Platí pouze v případě, že zadáte čas spuštění, protože tato aktivační událost nepřijímá posun UTC. Vyberte časové pásmo, které chcete použít. <p>Další informace najdete v tématu [vytváření a spouštění opakujících se úloh a pracovních postupů s Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Čas spuštění** | No | Zadejte čas spuštění v tomto formátu: <p>RRRR-MM-DDThh: mm: SS Pokud vyberete časové pásmo<p>-nebo-<p>RRRR-MM-DDThh: mm: ssZ, pokud nevyberete časové pásmo<p>Další informace najdete v tématu [vytváření a spouštění opakujících se úloh a pracovních postupů s Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.

1. Nyní pokračujte v přidávání jedné nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky triggeru. 

   Pokud například chcete filtrovat události na základě konkrétní hodnoty, jako je například kategorie, můžete přidat podmínku tak, aby akce **Odeslat událost** odeslala pouze události, které splňují vaši podmínku. 

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Přidání akce Event Hubs

V Azure Logic Apps [Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje po triggeru nebo jiné akci. V tomto příkladu se aplikace logiky spustí s triggerem Event Hubs, který kontroluje nové události v centru událostí.

1. V Azure Portal nebo Visual Studiu otevřete aplikaci logiky v Návrháři Logic Apps. V tomto příkladu se používá Azure Portal.

1. V aktivační události nebo akci vyberte **Nový krok**.

   Pokud chcete přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku připojení. 
   Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "centra událostí".
V seznamu akce vyberte tuto akci: **Odeslat událost – Event Hubs**

   ![Vybrat akci odeslat událost](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Pokud se zobrazí výzva k zadání podrobností o připojení, [Vytvořte si připojení Event Hubs nyní](#create-connection). 

1. V akci zadejte informace o událostech, které chcete odeslat. Pro další vlastnosti otevřete seznam **Přidat nový parametr** . Výběr parametru přidá tuto vlastnost na kartu akce.

   ![Vyberte název centra událostí a zadejte obsah události.](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Vlastnost | Povinné | Popis |
   |----------|----------|-------------|
   | **Název centra událostí** | Yes | Centrum událostí, do kterého chcete událost odeslat |
   | **Obsah** | No | Obsah události, kterou chcete odeslat |
   | **Vlastnosti** | No | Vlastnosti a hodnoty aplikace, které se mají odeslat |
   | **Klíč oddílu** | No | ID [oddílu](../event-hubs/event-hubs-features.md#partitions) , kde má být událost odeslána |
   ||||

   Můžete třeba poslat výstup z aktivační události Event Hubs do jiného centra událostí:

   ![Příklad odeslání události](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Připojení k centru událostí

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Po zobrazení výzvy k zadání informací o připojení zadejte tyto podrobnosti:

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název připojení** | Yes | <*název připojení*> | Název, který se má pro připojení vytvořit |
   | **Obor názvů Event Hubs** | Yes | <*Event-Centers – obor názvů*> | Vyberte obor názvů Event Hubs, který chcete použít. |
   |||||  

   Například:

   ![Vytvořit připojení centra událostí](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Chcete-li ručně zadat připojovací řetězec, vyberte možnost **ručně zadat informace o připojení**. 
   Přečtěte si, [Jak najít připojovací řetězec](#permissions-connection-string).

2. Vyberte zásadu Event Hubs, která se má použít, pokud ještě není vybraná. Vyberte **vytvořit**.

   ![Vytvořit připojení centra událostí, část 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Po vytvoření připojení pokračujte pomocí možnosti [přidat Event Hubs Trigger](#add-trigger) nebo [přidejte Event Hubs akci](#add-action).

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/eventhubs/).

> [!NOTE]
> V případě Logic Apps v [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Tato verze konektoru ISE-Label používá místo toho [omezení zpráv ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

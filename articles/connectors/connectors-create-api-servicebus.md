---
title: Zprávy Exchange s Azure Service Bus
description: Vytváření automatizovaných úloh a pracovních postupů, které odesílají a přijímají zprávy pomocí Azure Service Bus v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 26609bc95bf6fd49ded23d8f117911a02577405a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445900"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Výměna zpráv v cloudu pomocí Azure Logic Apps a Azure Service Bus

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a konektoru [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) můžete vytvářet automatizované úlohy a pracovní postupy, které přenášejí data, jako jsou prodejní a nákupní objednávky, deníky a pohyby inventáře napříč aplikacemi pro vaši organizaci. Konektor nejen monitoruje, odesílá a spravuje zprávy, ale také provádí akce s frontami, relacemi, tématy, předplatnými a tak dále, například:

* Monitorování, když přicházejí zprávy (automatické dokončování) nebo jsou přijímány (prohlížet zámky) ve frontách, tématech a předplatných tématu.
* Odesílat zprávy.
* Vytvořte a odstraňte odběry témat.
* Spravujte zprávy ve frontách a předplatných tématu, například získat, získat odložené, úplné, odložit, opustit a nedoručené písmeno.
* Obnovte zámky u zpráv a relací ve frontách a předplatných tématu.
* Zavřete relace ve frontách a tématech.

Můžete použít triggery, které získávají odpovědi z Service Bus a zpřístupnit výstup ostatním akcím ve vašich aplikacích logiky. Můžete také použít jiné akce výstup z Service Bus akce. Pokud Service Bus a Logic Apps začínáte, přečtěte si téma [co je Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) a [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Obor názvů Service Bus a entita zasílání zpráv, jako je například fronta. Tyto položky a aplikace logiky potřebují použít stejné předplatné Azure. Pokud tyto položky nemáte, přečtěte si, jak [vytvořit obor názvů Service Bus a frontu](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde se používá Service Bus obor názvů a entita zasílání zpráv Vaše aplikace logiky a služba Service Bus musí používat stejné předplatné Azure. Pokud chcete pracovní postup spustit pomocí triggeru Service Bus, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete ve svém pracovním postupu použít akci Service Bus, spusťte aplikaci logiky s jinou triggerem, například [triggerem opakování](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Kontrola oprávnění

Potvrďte, že vaše aplikace logiky má oprávnění pro přístup k vašemu oboru názvů Service Bus.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. Přejít na *obor názvů*Service Bus. Na stránce obor názvů v části **Nastavení**vyberte **zásady sdíleného přístupu**. V části **deklarace identity**ověřte, že máte oprávnění ke **správě** tohoto oboru názvů.

   ![Správa oprávnění pro Service Bus obor názvů](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Získejte připojovací řetězec pro obor názvů Service Bus. Tento řetězec budete potřebovat, když zadáte informace o připojení do aplikace logiky.

   1. V podokně **zásady sdíleného přístupu** vyberte **RootManageSharedAccessKey**.
   
   1. Vedle primárního připojovacího řetězce vyberte tlačítko Kopírovat. Uložte připojovací řetězec pro pozdější použití.

      ![Zkopírování připojovacího řetězce oboru názvů Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Chcete-li ověřit, zda je připojovací řetězec přidružen k vašemu Service Bus oboru názvů nebo entitě zasílání zpráv, jako je například fronta, vyhledejte v připojovacím řetězci parametr `EntityPath` . Pokud tento parametr vyhledáte, připojovací řetězec je pro konkrétní entitu a není správným řetězcem pro použití s vaší aplikací logiky.

## <a name="add-service-bus-trigger"></a>Přidat aktivační událost Service Bus

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. Do vyhledávacího pole zadejte jako filtr "Azure Service Bus". V seznamu triggery vyberte Trigger, který chcete.

   Chcete-li například aktivovat aplikaci logiky při odeslání nové položky do fronty Service Bus, vyberte možnost **při přijetí zprávy v aktivační události fronty (automatické dokončení)** .

   ![Vybrat aktivační událost Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Všechny triggery Service Bus jsou triggery *s dlouhým dotazem* . Tento popis znamená, že když se Trigger aktivuje, Trigger zpracuje všechny zprávy a potom počká 30 sekund, než se další zprávy zobrazí v předplatném fronty nebo tématu. Pokud se během 30 sekund nezobrazí žádné zprávy, spuštění triggeru se přeskočí. V opačném případě bude aktivační událost dál číst zprávy, dokud není předplatné fronty nebo tématu prázdné. Dotaz na další Trigger vychází z intervalu opakování zadaného ve vlastnostech triggeru.

   Některé triggery, například **když přijde jedna nebo více zpráv do aktivační události Queue (AutoComplete)** , můžou vracet jednu nebo více zpráv. Když se tyto triggery aktivují, vrátí se mezi sebou a počtem zpráv, které jsou zadané ve vlastnosti **maximální počet zpráv** triggeru.

1. Pokud se Trigger připojuje k vašemu Service Bus oboru názvů poprvé, postupujte podle těchto kroků, když vás návrhář aplikace logiky vyzve k zadání informací o připojení.

   1. Zadejte název připojení a vyberte svůj obor názvů Service Bus.

      ![Vytvoření připojení Service Bus, část 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Chcete-li místo toho zadat připojovací řetězec ručně, vyberte možnost **ručně zadat informace o připojení**. Pokud nemáte připojovací řetězec, přečtěte si, [Jak najít připojovací řetězec](#permissions-connection-string).

   1. Vyberte zásady Service Bus a vyberte **vytvořit**.

      ![Vytvoření připojení Service Bus, 2. část](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Vyberte požadovanou entitu zasílání zpráv, například frontu nebo téma. V tomto příkladu vyberte frontu Service Bus.
   
      ![Vybrat Service Bus frontu](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Zadejte potřebné informace pro vybraný Trigger. Chcete-li přidat k akci další dostupné vlastnosti, otevřete seznam **Přidat nový parametr** a vyberte požadované vlastnosti.

   Pro tento příklad triggeru vyberte interval cyklického dotazování a četnost kontroly fronty.

   ![Nastavení intervalu dotazování](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Další informace o dostupných triggerech a vlastnostech najdete na [referenční stránce](/connectors/servicebus/)konektoru.

1. Pokračujte v sestavování aplikace logiky přidáním akcí, které chcete.

   Můžete například přidat akci, která odešle e-mail, když přijde nová zpráva. Když aktivační událost zkontroluje vaši frontu a najde novou zprávu, vaše aplikace logiky spustí vybrané akce pro nalezenou zprávu.

## <a name="add-service-bus-action"></a>Přidat Service Bus akci

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky.

1. V kroku, kam chcete přidat akci, vyberte **Nový krok**.

   Nebo pokud chcete přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a vyberte **přidat akci**.

1. V části **zvolit akci**zadejte do vyhledávacího pole "Azure Service Bus" jako filtr. V seznamu akce vyberte akci, kterou chcete. 

   V tomto příkladu vyberte akci **Odeslat zprávu** .

   ![Vybrat Service Bus akci](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Pokud se vaše akce připojuje k vašemu Service Bus oboru názvů poprvé, postupujte podle těchto kroků, když vás návrhář aplikace logiky vyzve k zadání informací o připojení.

   1. Zadejte název připojení a vyberte svůj obor názvů Service Bus.

      ![Vytvoření připojení Service Bus, část 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Chcete-li místo toho zadat připojovací řetězec ručně, vyberte možnost **ručně zadat informace o připojení**. Pokud nemáte připojovací řetězec, přečtěte si, [Jak najít připojovací řetězec](#permissions-connection-string).

   1. Vyberte zásady Service Bus a vyberte **vytvořit**.

      ![Vytvoření připojení Service Bus, 2. část](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Vyberte požadovanou entitu zasílání zpráv, například frontu nebo téma. V tomto příkladu vyberte frontu Service Bus.

      ![Vybrat Service Bus frontu](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Zadejte potřebné podrobnosti pro vybranou akci. Chcete-li přidat k akci další dostupné vlastnosti, otevřete seznam **Přidat nový parametr** a vyberte požadované vlastnosti.

   Vyberte například vlastnosti **obsah** a **typ obsahu** , abyste je přidali do akce. Pak zadejte obsah zprávy, kterou chcete odeslat.

   ![Zadání obsahu zprávy a podrobností](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Další informace o dostupných akcích a jejich vlastnostech najdete na [referenční stránce](/connectors/servicebus/)konektoru.

1. Pokračujte v sestavování aplikace logiky přidáním všech dalších akcí, které chcete.

   Můžete například přidat akci, která odešle e-mail s potvrzením, že zpráva byla odeslána.

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Konektor Service Bus může současně ušetřit až 1 500 jedinečných relací ze sběrnice Service Bus do mezipaměti konektoru. Pokud počet relací překročí tento limit, staré relace budou odebrány z mezipaměti. Další technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI (dříve Swagger) konektoru, najdete na [referenční stránce](/connectors/servicebus/)konektoru.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

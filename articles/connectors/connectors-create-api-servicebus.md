---
title: Zprávy Exchange s Azure Service Bus
description: Vytváření automatizovaných úloh a pracovních postupů, které odesílají a přijímají zprávy pomocí Azure Service Bus v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 10/22/2020
tags: connectors
ms.openlocfilehash: b6276ff940d8b156a671cb5386ce53ede30dd879
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019701"
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

* Účet a předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Obor názvů Service Bus a entita zasílání zpráv, jako je například fronta. Tyto položky a aplikace logiky potřebují použít stejné předplatné Azure. Pokud tyto položky nemáte, přečtěte si, jak [vytvořit obor názvů Service Bus a frontu](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde se používá Service Bus obor názvů a entita zasílání zpráv Vaše aplikace logiky a služba Service Bus musí používat stejné předplatné Azure. Pokud chcete pracovní postup spustit pomocí triggeru Service Bus, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete ve svém pracovním postupu použít akci Service Bus, spusťte aplikaci logiky s jinou triggerem, například [triggerem opakování](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Kontrolovat oprávnění

Potvrďte, že vaše aplikace logiky má oprávnění pro přístup k vašemu oboru názvů Service Bus.

1. V [Azure Portal](https://portal.azure.com)se přihlaste pomocí svého účtu Azure.

1. Přejít na *obor názvů* Service Bus. Na stránce obor názvů v části **Nastavení** vyberte **zásady sdíleného přístupu**. V části **deklarace identity** ověřte, že máte oprávnění ke **správě** tohoto oboru názvů.

   ![Správa oprávnění pro Service Bus obor názvů](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Získejte připojovací řetězec pro obor názvů Service Bus. Tento řetězec budete potřebovat, když zadáte informace o připojení do aplikace logiky.

   1. V podokně **zásady sdíleného přístupu** vyberte **RootManageSharedAccessKey**.

   1. Vedle primárního připojovacího řetězce vyberte tlačítko Kopírovat. Uložte připojovací řetězec pro pozdější použití.

      ![Zkopírování připojovacího řetězce oboru názvů Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Pokud chcete ověřit, jestli je připojovací řetězec přidružený k vašemu oboru názvů Service Bus nebo entitě zasílání zpráv, jako je například fronta, vyhledejte v připojovacím řetězci `EntityPath` parametr. Pokud tento parametr vyhledáte, připojovací řetězec je pro konkrétní entitu a není správným řetězcem pro použití s vaší aplikací logiky.

## <a name="add-service-bus-trigger"></a>Přidat aktivační událost Service Bus

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. Do vyhledávacího pole portálu zadejte `azure service bus` . Ze seznamu triggery, který se zobrazí, vyberte aktivační událost, kterou chcete.

   Chcete-li například aktivovat aplikaci logiky při odeslání nové položky do fronty Service Bus, vyberte možnost **při přijetí zprávy v aktivační události fronty (automatické dokončení)** .

   ![Vybrat aktivační událost Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Tady jsou některé předpoklady pro použití triggeru Service Bus:

   * Všechny triggery Service Bus jsou triggery *s dlouhým dotazem* . Tento popis znamená, že když se Trigger aktivuje, Trigger zpracuje všechny zprávy a potom počká 30 sekund, než se další zprávy zobrazí v předplatném fronty nebo tématu. Pokud se během 30 sekund nezobrazí žádné zprávy, spuštění triggeru se přeskočí. V opačném případě bude aktivační událost dál číst zprávy, dokud není předplatné fronty nebo tématu prázdné. Dotaz na další Trigger vychází z intervalu opakování zadaného ve vlastnostech triggeru.

   * Některé triggery, například **když přijde jedna nebo více zpráv do aktivační události Queue (AutoComplete)** , můžou vracet jednu nebo více zpráv. Když se tyto triggery aktivují, vrátí se mezi sebou a počtem zpráv, které jsou zadané ve vlastnosti **maximální počet zpráv** triggeru.

     > [!NOTE]
     > Trigger automatického dokončování automaticky dokončí zprávu, ale dokončování probíhá pouze při dalším volání Service Bus. Toto chování může ovlivnit návrh aplikace logiky. Neměňte například souběžnost na automatickém dokončení triggeru, protože tato změna může mít za následek duplicitní zprávy, pokud vaše aplikace logiky vstoupí do omezeného stavu. Změna řízení souběžnosti vytváří tyto podmínky: omezené triggery jsou přeskočeny `WorkflowRunInProgress` kódem, operace dokončení nebude provedena a další spuštění triggeru nastane po intervalu dotazování. Je nutné nastavit dobu trvání zámku služby Service Bus na hodnotu, která je delší než interval cyklického dotazování. Bez ohledu na toto nastavení ale nemusí být zpráva dokončena, pokud vaše aplikace logiky zůstane v dalším intervalu dotazování v omezeném stavu.

   * Pokud [zapnete nastavení souběžnosti](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) pro aktivační událost Service Bus, výchozí hodnota `maximumWaitingRuns` vlastnosti je 10. Na základě nastavení doby trvání zámku Service Bus entit a doby běhu vaší instance aplikace logiky může být tato výchozí hodnota příliš velká a může způsobit výjimku "zámek". Pro vyhledání optimální hodnoty pro váš scénář spusťte testování s hodnotou 1 nebo 2 pro `maximumWaitingRuns` vlastnost. Pokud chcete změnit maximální hodnotu čekání na spuštění, přečtěte si téma [Změna limitu čekání na spuštění](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs).

1. Pokud se Trigger připojuje k vašemu Service Bus oboru názvů poprvé, postupujte podle těchto kroků, když vás návrhář aplikace logiky vyzve k zadání informací o připojení.

   1. Zadejte název připojení a vyberte svůj obor názvů Service Bus.

      ![Snímek obrazovky, který ukazuje zadání názvu připojení a výběr Service Bus oboru názvů](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Chcete-li místo toho zadat připojovací řetězec ručně, vyberte možnost **ručně zadat informace o připojení**. Pokud nemáte připojovací řetězec, přečtěte si, [Jak najít připojovací řetězec](#permissions-connection-string).

   1. Vyberte zásady Service Bus a vyberte **vytvořit**.

      ![Snímek obrazovky zobrazující výběr zásady Service Bus](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Vyberte požadovanou entitu zasílání zpráv, například frontu nebo téma. V tomto příkladu vyberte frontu Service Bus.
   
      ![Snímek obrazovky zobrazující výběr Service Bus fronty](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Zadejte potřebné informace pro vybraný Trigger. Chcete-li přidat k akci další dostupné vlastnosti, otevřete seznam **Přidat nový parametr** a vyberte požadované vlastnosti.

   Pro tento příklad triggeru vyberte interval cyklického dotazování a četnost kontroly fronty.

   ![Snímek obrazovky zobrazující nastavení intervalu dotazování v aktivační události Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Další informace o dostupných triggerech a vlastnostech najdete na [referenční stránce](/connectors/servicebus/)konektoru.

1. Pokračujte v sestavování aplikace logiky přidáním akcí, které chcete.

   Můžete například přidat akci, která odešle e-mail, když přijde nová zpráva. Když aktivační událost zkontroluje vaši frontu a najde novou zprávu, vaše aplikace logiky spustí vybrané akce pro nalezenou zprávu.

## <a name="add-service-bus-action"></a>Přidat Service Bus akci

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V kroku, kam chcete přidat akci, vyberte **Nový krok**.

   Nebo pokud chcete přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a vyberte **přidat akci**.

1. V části **zvolit akci** zadejte do vyhledávacího pole `azure service bus` . V seznamu akce, který se zobrazí, vyberte akci, kterou chcete. 

   V tomto příkladu vyberte akci **Odeslat zprávu** .

   ![Snímek obrazovky zobrazující výběr akce Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Pokud se vaše akce připojuje k vašemu Service Bus oboru názvů poprvé, postupujte podle těchto kroků, když vás návrhář aplikace logiky vyzve k zadání informací o připojení.

   1. Zadejte název připojení a vyberte svůj obor názvů Service Bus.

      ![Snímek obrazovky, který ukazuje zadání názvu připojení a výběr Service Bus oboru názvů](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Chcete-li místo toho zadat připojovací řetězec ručně, vyberte možnost **ručně zadat informace o připojení**. Pokud nemáte připojovací řetězec, přečtěte si, [Jak najít připojovací řetězec](#permissions-connection-string).

   1. Vyberte zásady Service Bus a vyberte **vytvořit**.

      ![Snímek obrazovky zobrazující výběr zásady Service Bus a výběr tlačítka vytvořit](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Vyberte požadovanou entitu zasílání zpráv, například frontu nebo téma. V tomto příkladu vyberte frontu Service Bus.

      ![Snímek obrazovky, který zobrazuje výběr fronty Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Zadejte potřebné podrobnosti pro vybranou akci. Chcete-li přidat k akci další dostupné vlastnosti, otevřete seznam **Přidat nový parametr** a vyberte požadované vlastnosti.

   Vyberte například vlastnosti **obsah** a **typ obsahu** , abyste je přidali do akce. Pak zadejte obsah zprávy, kterou chcete odeslat.

   ![Snímek obrazovky, který ukazuje zadání typu obsahu zprávy a podrobností](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Další informace o dostupných akcích a jejich vlastnostech najdete na [referenční stránce](/connectors/servicebus/)konektoru.

1. Pokračujte v sestavování aplikace logiky přidáním všech dalších akcí, které chcete.

   Můžete například přidat akci, která odešle e-mail s potvrzením, že zpráva byla odeslána.

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>Odeslání korelačních zpráv v daném pořadí

Pokud potřebujete odesílat související zprávy v určitém pořadí, můžete použít [ *sekvenční vzor convoy*](/azure/architecture/patterns/sequential-convoy) pomocí [konektoru Azure Service Bus](../connectors/connectors-create-api-servicebus.md). Korelační zprávy mají vlastnost, která definuje vztah mezi těmito zprávami, jako je ID [relace](../service-bus-messaging/message-sessions.md) v Service Bus.

Když vytvoříte aplikaci logiky, můžete vybrat **korelujované doručení v pořadí pomocí šablony relace služby Service Bus** , která implementuje sekvenční vzor convoy. Další informace najdete v tématu [odeslání souvisejících zpráv v daném pořadí](../logic-apps/send-related-messages-sequential-convoy.md).

## <a name="delays-in-updates-to-your-logic-app-taking-effect"></a>Prodlevy v aktualizacích, které se projeví v aplikaci logiky

Pokud je interval dotazování triggeru Service Bus malý, například 10 sekund, aktualizace vaší aplikace logiky se nemusí projevit až po dobu 10 minut. Pokud chcete tento problém obejít, můžete před aktualizací aplikace logiky dočasně zvýšit interval dotazování na větší hodnotu, jako je například 30 sekund nebo 1 minuta. Po provedení aktualizace můžete interval dotazování obnovit na původní hodnotu. 

<a name="connector-reference"></a>

## <a name="connector-reference"></a>Referenční informace ke konektorům

V rámci služby Service Bus může konektor Service Bus v čase ukládat do mezipaměti konektoru až 1 500 jedinečných relací, a to za [Service Bus entitu zasílání zpráv, jako je například předplatné nebo téma](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). Pokud počet relací překročí tento limit, staré relace budou odebrány z mezipaměti. Další informace najdete v tématu [relace zpráv](../service-bus-messaging/message-sessions.md).

Další technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu Swagger tohoto konektoru, najdete na [referenční stránce konektoru](/connectors/servicebus/). Další informace o Azure Service Bus zasílání zpráv najdete v tématu [co je Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

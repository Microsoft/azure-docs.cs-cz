---
title: Výměna zpráv pomocí služby Azure Service Bus
description: Vytváření automatizovaných úloh a pracovních postupů, které odesílají a přijímají zprávy pomocí služby Azure Service Bus v Aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261615"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Výměna zpráv v cloudu pomocí Azure Logic Apps a Azure Service Bus

Pomocí [aplikací Azure Logic Apps](../logic-apps/logic-apps-overview.md) a konektoru Azure Service [Bus](../service-bus-messaging/service-bus-messaging-overview.md) můžete vytvářet automatizované úlohy a pracovní postupy, které přenášejí data, jako jsou prodejní a nákupní objednávky, deníky a přesuny inventáře napříč aplikacemi pro vaši organizaci. Konektor nejen monitoruje, odesílá a spravuje zprávy, ale také provádí akce s frontami, relacemi, tématy, předplatnými a tak dále, například:

* Sledujte, kdy zprávy dorazí (automaticky dokončují) nebo jsou přijaty (peek-lock) ve frontách, tématech a předplatných tématech.
* Posílat zprávy.
* Vytvořit a odstranit odběry témat.
* Správa zpráv ve frontách a odběry témat, například získat, získat odložené, úplné, odložit, opustit a nedoručené.
* Obnovte zámky zpráv a relací ve frontách a předplatných témat.
* Zavřete relace ve frontách a tématech.

Můžete použít aktivační události, které získají odpovědi ze služby Service Bus a zpřístupní výstup dalším akcím ve vašich aplikacích logiky. Můžete také mít jiné akce použít výstup z akce Service Bus. Pokud s Service Bus a logic apps tečune, přečtěte si [co je Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) a Co jsou aplikace Azure Logic [Apps](../logic-apps/logic-apps-overview.md)?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Obor názvů service bus a transakce zasílání zpráv, například fronta. Tyto položky a aplikace logiky je potřeba používat stejné předplatné Azure. Pokud tyto položky nemáte, přečtěte si, jak [vytvořit obor názvů service bus a frontu](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde používáte obor názvů Service Bus a entitu zasílání zpráv. Vaše aplikace logiky a service bus je potřeba použít stejné předplatné Azure. Chcete-li spustit pracovní postup pomocí aktivační události service bus, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li v pracovním postupu použít akci služby Service Bus, spusťte aplikaci logiky s jinou aktivační událostí, například [aktivační událostí Opakování](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Kontrola oprávnění

Zkontrolujte, zda má vaše aplikace logiky oprávnění pro přístup k oboru názvů Service Bus.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Přejděte do *oboru názvů*Service Bus . Na stránce oboru názvů vyberte v části **Nastavení** **zásady sdíleného přístupu**. V části **Deklarace identity**zkontrolujte, zda máte **oprávnění Spravovat** pro tento obor názvů.

   ![Správa oprávnění pro obor názvů Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Získejte připojovací řetězec pro obor názvů Service Bus. Tento řetězec potřebujete při zadání informací o připojení v aplikaci logiky.

   1. V podokně **Zásady sdíleného přístupu** vyberte **příkaz RootManageSharedAccessKey**.
   
   1. Vedle primárního připojovacího řetězce vyberte tlačítko kopírovat. Uložte připojovací řetězec pro pozdější použití.

      ![Kopírovat připojovací řetězec oboru názvů služby Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Chcete-li ověřit, zda je připojovací řetězec přidružen k oboru názvů Service Bus nebo `EntityPath`  k entitě zasílání zpráv, například k frontě, vyhledejte v připojovacím řetězci parametr. Pokud najdete tento parametr, připojovací řetězec je pro konkrétní entitu a není správný řetězec pro použití s aplikací logiky.

## <a name="add-service-bus-trigger"></a>Přidat aktivační událost služby Service Bus

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete prázdnou aplikaci logiky v Návrháři aplikací logiky.

1. Do vyhledávacího pole zadejte jako filtr "azure service bus". Ze seznamu aktivačních událostí vyberte požadovanou aktivační událost.

   Chcete-li například aktivovat aplikaci logiky při odeslání nové položky do fronty služby Service Bus, vyberte aktivační **událost Při přijetí zprávy ve frontě (automatické dokončování).**

   ![Vybrat aktivační událost služby Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Všechny aktivační události service bus jsou aktivační události *s dlouhým dotazováním.* Tento popis znamená, že při spuštění aktivační události aktivační událost zpracuje všechny zprávy a pak čeká 30 sekund na další zprávy se zobrazí ve frontě nebo téma odběr. Pokud se do 30 sekund nezobrazí žádné zprávy, bude spuštění aktivační události přeskočeno. V opačném případě aktivační událost pokračuje čtení zpráv, dokud fronty nebo téma odběr je prázdný. Další dotazování aktivačních událostí je založeno na intervalu opakování zadaném ve vlastnostech aktivační události.

   Některé aktivační události, například **Když jedna nebo více zpráv dorazí do fronty (automatické dokončování)** aktivační událost, můžete vrátit jednu nebo více zpráv. Když tyto aktivační události požáru, vrátí mezi jeden a počet zpráv, které jsou určeny aktivační události **Maximální počet zpráv** vlastnost.

1. Pokud se aktivační událost připojuje k oboru názvů Service Bus poprvé, postupujte takto, když vás Návrhář aplikace logiky vyzve k zadání informací o připojení.

   1. Zadejte název připojení a vyberte obor názvů Service Bus.

      ![Vytvořit připojení sběrnice, část 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Chcete-li místo toho ručně zadat připojovací řetězec, vyberte **ručně zadat informace o připojení**. Pokud připojovací řetězec nemáte, přečtěte si, [jak najít připojovací řetězec](#permissions-connection-string).

   1. Vyberte zásady služby Service Bus a vyberte **vytvořit**.

      ![Vytvořit připojení sběrnice, část 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Vyberte požadovanou entitu zasílání zpráv, například frontu nebo téma. V tomto příkladu vyberte frontu služby Service Bus.
   
      ![Vybrat frontu služby Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Poskytněte potřebné informace pro vybranou aktivační událost. Chcete-li do akce přidat další dostupné vlastnosti, otevřete seznam **Přidat nový parametr** a vyberte požadované vlastnosti.

   Pro aktivační událost tohoto příkladu vyberte interval dotazování a frekvenci pro kontrolu fronty.

   ![Nastavit interval dotazování](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Další informace o dostupných aktivačních událostech a vlastnostech naleznete na [referenční stránce konektoru](/connectors/servicebus/).

1. Pokračujte v vytváření aplikace logiky přidáním požadovaných akcí.

   Můžete například přidat akci, která odešle e-mail při příchodu nové zprávy. Když aktivační událost zkontroluje frontu a najde novou zprávu, aplikace logiky spustí vybrané akce pro nalezenou zprávu.

## <a name="add-service-bus-action"></a>Přidat akci Služby service bus

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Pod krokem, kam chcete přidat akci, vyberte **Nový krok**.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. Vyberte znaménko plus (**+**) a vyberte Přidat **akci**.

1. V **části Zvolte akci**zadejte do vyhledávacího pole jako filtr "azure service bus". V seznamu akcí vyberte požadovanou akci. 

   V tomto příkladu vyberte akci **Odeslat zprávu.**

   ![Vybrat akci Služby pro službu](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Pokud se vaše akce připojuje k oboru názvů Service Bus poprvé, postupujte takto, když vás Návrhář aplikace logiky vyzve k zadání informací o připojení.

   1. Zadejte název připojení a vyberte obor názvů Service Bus.

      ![Vytvořit připojení sběrnice, část 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Chcete-li místo toho ručně zadat připojovací řetězec, vyberte **ručně zadat informace o připojení**. Pokud připojovací řetězec nemáte, přečtěte si, [jak najít připojovací řetězec](#permissions-connection-string).

   1. Vyberte zásady služby Service Bus a vyberte **vytvořit**.

      ![Vytvořit připojení sběrnice, část 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Vyberte požadovanou entitu zasílání zpráv, například frontu nebo téma. V tomto příkladu vyberte frontu služby Service Bus.

      ![Vybrat frontu služby Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Uveďte potřebné podrobnosti pro vybranou akci. Chcete-li do akce přidat další dostupné vlastnosti, otevřete seznam **Přidat nový parametr** a vyberte požadované vlastnosti.

   Vyberte například vlastnosti **Obsah** a **Typ obsahu,** abyste je přidali do akce. Potom zadejte obsah zprávy, kterou chcete odeslat.

   ![Poskytnutí obsahu a podrobností zprávy](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Další informace o dostupných akcích a jejich vlastnostech naleznete na [referenční stránce konektoru](/connectors/servicebus/).

1. Pokračujte v vytváření aplikace logiky přidáním dalších akcí, které chcete.

   Můžete například přidat akci, která odešle e-mail s potvrzením, že byla odeslána vaše zpráva.

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Konektor service bus můžete uložit až 1 500 jedinečných relací najednou ze sběrnice do mezipaměti konektoru. Pokud počet relací překročí tento limit, staré relace jsou odebrány z mezipaměti. Další informace naleznete v tématu [Relace zpráv](../service-bus-messaging/message-sessions.md).

Další technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu OpenAPI (dříve Swagger) konektoru, zkontrolujte [referenční stránku konektoru](/connectors/servicebus/). Další informace o zasílání zpráv služby Azure Service Bus najdete v tématu [Co je Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)?

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)

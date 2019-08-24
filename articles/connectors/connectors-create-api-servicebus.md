---
title: Posílání a přijímání zpráv pomocí Azure Service Bus-Azure Logic Apps | Microsoft Docs
description: Nastavení podnikového cloudového zasílání zpráv pomocí Azure Service Bus v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 944bac44c1fc6504dfe1a93df5760ccf4ee46fa0
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982215"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Výměna zpráv v cloudu s Azure Service Bus a Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Azure Service Bus můžete vytvářet automatizované úlohy a pracovní postupy, které přenášejí data, jako jsou prodejní a nákupní objednávky, deníky nebo přesuny zásob, napříč aplikacemi vaší organizace. Konektor nejen monitoruje, odesílá a spravuje zprávy, ale také provádí akce s frontami, relacemi, tématy, předplatnými a tak dále, například:

* Monitorování, když přicházejí zprávy (automatické dokončování) nebo jsou přijímány (prohlížet zámky) ve frontách, tématech a předplatných tématu. 
* Odesílat zprávy.
* Vytvořte a odstraňte odběry témat.
* Spravujte zprávy ve frontách a předplatných tématu, například získat, získat odložené, úplné, odložit, opustit a nedoručené písmeno.
* Obnovte zámky u zpráv a relací ve frontách a předplatných tématu.
* Zavřete relace ve frontách a tématech.

Můžete použít triggery, které získávají odpovědi z Service Bus a zpřístupnit výstup ostatním akcím ve vašich aplikacích logiky. Můžete také použít jiné akce výstup z Service Bus akce. Pokud Service Bus a Logic Apps začínáte, přečtěte si téma [co je Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) a [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Obor názvů Service Bus a entita zasílání zpráv, jako je například fronta. Pokud tyto položky nemáte, přečtěte si, jak [vytvořit obor názvů Service Bus a frontu](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Tyto položky musí existovat ve stejném předplatném Azure jako aplikace logiky, které tyto položky používají.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete použít Service Bus. Vaše aplikace logiky musí existovat ve stejném předplatném Azure jako vaše služba Service Bus. Pokud chcete začít s triggerem Service Bus, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci Service Bus, spusťte aplikaci logiky s jinou triggerem, například triggerem **opakování** .

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Kontrolovat oprávnění

Potvrďte, že vaše aplikace logiky má oprávnění pro přístup k vašemu oboru názvů Service Bus. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Přejít na *obor názvů*Service Bus. Na stránce obor názvů v části **Nastavení**vyberte **zásady sdíleného přístupu**. V části **deklarace identity**ověřte, že máte oprávnění ke **správě** tohoto oboru názvů.

   ![Správa oprávnění pro Service Bus obor názvů](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Získejte připojovací řetězec pro obor názvů Service Bus. Tento řetězec budete potřebovat při zadávání informací o připojení do aplikace logiky.

   1. Vyberte **RootManageSharedAccessKey**. 
   
   1. Vedle primárního připojovacího řetězce klikněte na tlačítko Kopírovat. Uložte připojovací řetězec pro pozdější použití.

      ![Zkopírování připojovacího řetězce oboru názvů Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Pokud chcete ověřit, jestli je připojovací řetězec přidružený k vašemu oboru názvů Service Bus nebo entitě zasílání zpráv, jako je například fronta, vyhledejte v `EntityPath`připojovacím řetězci  parametr. Pokud tento parametr vyhledáte, připojovací řetězec je pro konkrétní entitu a není správným řetězcem pro použití s vaší aplikací logiky.

## <a name="add-trigger-or-action"></a>Přidat aktivační událost nebo akci

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Pokud chcete přidat *Trigger* do prázdné aplikace logiky, do vyhledávacího pole zadejte "Azure Service Bus" jako filtr. V seznamu triggery vyberte aktivační událost, kterou chcete. 

   Chcete-li například aktivovat aplikaci logiky při odeslání nové položky do fronty Service Bus, vyberte tuto aktivační událost: **Při přijetí zprávy ve frontě (automatické dokončování)**

   ![Vybrat aktivační událost Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Některé triggery můžou vracet jednu nebo několik zpráv, například Trigger, **když přijde jedna nebo víc zpráv do fronty (automatické dokončování)** . Když se tyto triggery aktivují, vrátí se mezi sebou a počet zpráv určených vlastností **maximální počet zpráv** triggeru.

   *Všechny triggery Service Bus jsou triggery s dlouhým dotazem*, což znamená, že když se Trigger aktivuje, Trigger zpracuje všechny zprávy a potom počká 30 sekund, než se další zprávy zobrazí v předplatném fronty nebo tématu. 
   Pokud se během 30 sekund nezobrazí žádné zprávy, spuštění triggeru se přeskočí. 
   V opačném případě bude aktivační událost dál číst zprávy, dokud není předplatné fronty nebo tématu prázdné. Dotaz na další Trigger vychází z intervalu opakování zadaného ve vlastnostech triggeru.

1. Pokud chcete přidat *akci* do existující aplikace logiky, použijte následující postup: 

   1. V posledním kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. 

      Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
      Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

   1. Do vyhledávacího pole zadejte jako filtr "Azure Service Bus". 
   V seznamu akce vyberte akci, kterou chcete. 
 
      Vyberte například tuto akci: **Odeslat zprávu**

      ![Vybrat Service Bus akci](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Pokud připojujete aplikaci logiky k vašemu Service Bus oboru názvů poprvé, návrhář aplikace logiky vás nyní vyzve k zadání informací o připojení. 

   1. Zadejte název připojení a vyberte svůj obor názvů Service Bus.

      ![Vytvoření připojení Service Bus, část 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Chcete-li místo toho zadat připojovací řetězec ručně, vyberte možnost **ručně zadat informace o připojení**. 
      Pokud nemáte připojovací řetězec, přečtěte si, [Jak najít připojovací řetězec](#permissions-connection-string).

   1. Teď vyberte zásady Service Bus a pak zvolte **vytvořit**.

      ![Vytvoření připojení Service Bus, 2. část](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. V tomto příkladu vyberte požadovanou entitu zasílání zpráv, například frontu nebo téma. V tomto příkladu vyberte frontu Service Bus. 
   
   ![Vybrat Service Bus frontu](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Zadejte potřebné údaje pro aktivační událost nebo akci. V tomto příkladu postupujte podle příslušných kroků triggeru nebo akce: 

   * **Pro aktivační událost Sample**: Nastavte interval dotazování a četnost kontroly fronty.

     ![Nastavení intervalu dotazování](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Až budete hotovi, pokračujte v vytváření pracovního postupu aplikace logiky tím, že přidáte požadované akce. Můžete například přidat akci, která odešle e-mail, když přijde nová zpráva.
     Když aktivační událost zkontroluje vaši frontu a najde novou zprávu, vaše aplikace logiky spustí vybrané akce pro nalezenou zprávu.

   * **Pro akci vzor**: Zadejte obsah zprávy a další podrobnosti. 

     ![Zadání obsahu zprávy a podrobností](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Až budete hotovi, pokračujte v vytváření pracovního postupu aplikace logiky přidáním dalších akcí, které chcete. Můžete například přidat akci, která odešle e-mail s potvrzením odeslání zprávy.

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Konektor Service Bus může současně ušetřit až 1 500 jedinečných relací ze sběrnice Service Bus do mezipaměti konektoru. Pokud počet relací překročí tento limit, staré relace budou odebrány z mezipaměti. Další technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI (dříve Swagger) konektoru, najdete na [referenční stránce](/connectors/servicebus/)konektoru.

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

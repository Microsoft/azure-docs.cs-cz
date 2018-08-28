---
title: Odesílání a příjem zpráv pomocí služby Azure Service Bus – Azure Logic Apps | Dokumentace Microsoftu
description: Nastavení cloudu Podnikové zasílání zpráv pomocí služby Azure Service Bus v Azure Logic Apps
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
ms.openlocfilehash: 813df5b4ef37ad1264df48863aa8f0ed5a4d4789
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048770"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Výměna zpráv v cloudu pomocí služby Azure Service Bus a Azure Logic Apps

S Azure Logic Apps a konektor služby Azure Service Bus můžete vytvořit automatizovaných úloh a pracovních postupů, které přenosu dat, jako je sales a nákupních objednávek, denících a pohybů plb typu inventáře napříč aplikacemi pro vaši organizaci. Konektor nejen monitoruje, odešle a spravuje zprávy, ale také provede akce s frontami, relace, témata, odběry a tak dále, například:

* Monitorování při doručení (automatické dokončení) nebo jsou přijaté nahlédnutím (peek-lock) do fronty, témata a odběry témat zprávy. 
* Odesílání zpráv.
* Vytvářet a odstraňovat odběry tématu.
* Správa zpráv do front a odběrů tématu, například získat, získat odloženo, dokončení, odložit, zrušení a onta nedoručených zpráv.
* Obnovte uzamčení zprávy a relace do front a odběrů tématu.
* Zavřít relace ve frontách a tématech.

Můžete použít aktivační události, které odpovědi ze služby Service Bus a zpřístupnit výstup dalších akcí ve svých aplikacích logiky. Také můžete mít další akce pomocí výstupu z akce služby Service Bus. Pokud jste služby Service Bus a Logic Apps teprve začínáte, přečtěte si [co je Azure Service Bus?](../service-bus-messaging/service-bus-messaging-overview.md) a [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Obor názvů služby Service Bus a entity pro zasílání zpráv, jako jsou fronty. Pokud nemáte k dispozici tyto položky, zjistěte, jak [vytvoření oboru názvů služby Service Bus a fronty](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Tyto položky musí existovat ve stejném předplatném Azure jako logic apps, které používají tyto položky.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete použít Service Bus. Aplikace logiky musí existovat ve stejném předplatném Azure jako vaše služby Service bus. Začít s triggerem Service Bus [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akce služby Service Bus, spusťte svou aplikaci logiky s další aktivační události, například, **opakování** aktivační události.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Zkontrolujte oprávnění

Ověřte, jestli aplikace logiky má oprávnění pro přístup k oboru názvů služby Service Bus. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Přejděte na Service Bus *obor názvů*. Na stránce obor názvů v rámci **nastavení**vyberte **zásady sdíleného přístupu**. V části **deklarace identity**, zkontrolujte, jestli máte **spravovat** oprávnění pro tento obor názvů

   ![Spravovat oprávnění pro váš obor názvů služby Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Získání připojovacího řetězce pro váš obor názvů služby Service Bus. Tento řetězec budete potřebovat při zadání informací o připojení v aplikaci logiky.

   1. Vyberte **RootManageSharedAccessKey**. 
   
   1. Vedle primární připojovací řetězec klikněte na tlačítko Kopírovat. Uložte připojovací řetězec pro pozdější použití.

      ![Zkopírujte připojovací řetězec pro obor názvů Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Potvrďte, zda je přidružené k oboru názvů služby Service Bus nebo entity pro zasílání zpráv, jako jsou fronty, aby váš připojovací řetězec hledání připojovací řetězec pro `EntityPath` parametru. Pokud tento parametr najít, připojovací řetězec je pro konkrétní entitu a není správný řetězec, který má používat s aplikací logiky.

## <a name="add-trigger-or-action"></a>Přidání triggeru nebo akce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a otevřete svou aplikaci logiky v návrháři aplikace logiky, není již otevřete.

1. Chcete-li přidat *aktivační událost* do prázdné aplikace logiky, do vyhledávacího pole zadejte "Azure Service Bus" jako filtr. V seznamu triggerů vyberte trigger, který chcete. 

   Například při vytvoření nové položky se odešlou do fronty služby Service Bus, spustit aplikaci logiky, vyberte tento trigger: **při doručení zprávy do fronty (automatické dokončení)**

   ![Výběr triggeru služby Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Některé aktivačních událostí může vracet instanci jednoho nebo zprávy, například aktivační událost, **při doručení jedné či více zpráv do fronty (automatické dokončení)**. Když tyto triggery aktivují, vrátí rozsahu od 1 do počtu zpráv zadaného má aktivační procedura **maximální počet zpráv** vlastnost.

   *Všechny aktivační události služby Service Bus jsou triggery s dlouhým intervalem dotazování*, což znamená, že když se trigger aktivuje, aktivační událost zpracovávat všechny zprávy a poté počká 30 sekund pro další zprávy zobrazit v předplatném fronty nebo tématu. 
   Pokud během 30 sekund, nezobrazí se žádné zprávy, spuštění aktivační události je přeskočeno. 
   V opačném případě se aktivační událost pokračuje v čtení zpráv do fronty nebo tématu předplatné je prázdné. Další cyklického dotazování aktivační události je založená na opakování intervalu zadaném ve vlastnosti aktivační události.

1. Chcete-li přidat *akce* do existující aplikace logiky, postupujte podle těchto kroků: 

   1. V posledním kroku, ve které chcete přidat akci, zvolte **nový krok**. 

      Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. 
      Vyberte znaménko plus (**+**), který se zobrazí a pak vyberte **přidat akci**.

   1. Do vyhledávacího pole zadejte jako filtr "Azure Service Bus". 
   V seznamu akcí vyberte požadovanou akci. 
 
      Například vyberte tuto akci: **odeslat zprávu**

      ![Výběr akce služby Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Pokud vaše aplikace logiky se připojujete poprvé do svého oboru názvů služby Service Bus, Návrhář aplikace logiky teď vás vyzve k zadání informací o připojení. 

   1. Zadejte název připojení a zvolte svůj obor názvů služby Service Bus.

      ![Vytvoření připojení k Service Bus, část 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Chcete-li ručně místo toho zadejte připojovací řetězec, zvolte **ručně zadat informace o připojení**. 
      Pokud nemáte k dispozici připojovací řetězec, přečtěte si [jak zjistit připojovací řetězec](#permissions-connection-string).

   1. Teď vyberte zásady služby Service Bus a pak zvolte **vytvořit**.

      ![Vytvoření připojení k Service Bus, část 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. V tomto příkladu vyberte entity zasílání zpráv, které potřebujete, jako je například fronta nebo téma. V tomto příkladu vyberte fronty služby Service Bus. 
   
   ![Vyberte frontu služby Service Bus](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Zadejte potřebné podrobnosti o triggeru nebo akce. V tomto příkladu postupujte podle příslušných kroků u triggeru nebo akce: 

   * **Pro trigger ukázka**: nastavte interval cyklického dotazování a frekvenci kontroly fronty.

     ![Nastavit interval dotazování](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Jakmile budete hotovi, pokračujte v sestavování pracovního postupu aplikace logiky tak, že přidáte akce, které chcete. Můžete například přidat akci, která odešle e-mail, když přijde nová zpráva.
     Když trigger zkontroluje fronty a zjistí novou zprávu, aplikace logiky spouští vaše vybrané akce pro nalezenou zprávu.

   * **Ukázka akce**: Zadejte obsah zprávy a další podrobnosti. 

     ![Zadejte obsah zprávy a podrobnosti](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Jakmile budete hotovi, pokračujte v sestavování pracovního postupu aplikace logiky tak, že přidáte další požadované akce. Můžete například přidat akci, která odešle e-mail s potvrzením, že zpráva byla odeslána.

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o omezení, akce a triggery, které jsou popsány pomocí konektoru OpenAPI (dříve Swagger) popis, přečtěte si tento konektor [referenční stránce](/connectors/servicebus/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
---
title: Dávkové zpracování EDI zpráv jako skupiny nebo kolekce - Azure Logic Apps | Microsoft Docs
description: Odesílání zpráv EDI pro dávkové zpracování v aplikacích logiky
keywords: zpracování dávky, batch, zakódovat batch
author: divswa
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 6340d1fc409b64c5e681f9f69e4e2d0895ede61f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299286"
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Odesílání X12 zpráv ve službě batch na obchodních partnerů

V obchodní na obchodní scénáře (B2B) často partnery výměna zpráv v skupiny nebo dávek. Odesílat zprávy v skupiny nebo dávek obchodním partnerům, můžete vytvořit dávce s více položek a potom pomocí X12 akce batch ke zpracování položek, jako dávku.


Dávkování pro X12 zprávy, jako ostatní zprávy používá batch aktivační události a akce. Také pro X12, dávky prochází X12 kódovat krok před přechodem k partnera nebo jiného umístění. Další informace o aktivační události batch a akce najdete v tématu [dávkové zpracování zpráv](logic-apps-batch-process-send-receive-messages.md).

Toto téma ukazuje, jak může zpracovat X12 zprávy jako dávku provedením těchto úkolů:
* [Vytvoření aplikace logiky, která přijímá položky a vytvoří dávce](#receiver). Tato aplikace logiky "příjemce" provede následující akce:
 
   * Určuje kritéria batch název a verze pro splnění před uvolněním položky jako dávku.

   * Zpracuje nebo kóduje položek v dávce pomocí zadané X12 smlouvy nebo partnera identit.

* [Vytvoření aplikace logiky, která odesílá položky do dávky](#sender). Tato aplikace logiky "sender" Určuje, kde k odesílání položek pro dávkové zpracování, které musí být existující aplikace logiky příjemce.


## <a name="prerequisites"></a>Požadavky

Postup popsaný v tomto příkladu, budete potřebovat tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Jinak si můžete [zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* [Integrace účet](logic-apps-enterprise-integration-create-integration-account.md) který již má definovaný a přidružené k předplatnému Azure

* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , kterou jste definovali ve vašem účtu integrace. Ujistěte se, že každého partnera, používá X12 (Standardní poskytovatel alfa kód) kvalifikátor ve vlastnostech jeho jako obchodní identitu.

* [X12 smlouvy](logic-apps-enterprise-integration-x12.md) , již je definována v účtu integrace

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Vytvoření logiku aplikace, která přijímá X12 zpráv a vytváří dávky

Předtím, než mohou zasílat zprávy do dávky, je nutné nejprve vytvořit aplikace logiky "příjemce" s **Batch** aktivační události. Tímto způsobem, tato aplikace logiky příjemce můžete vybrat, když vytvoříte aplikaci logiky odesílatele. Pro příjemce, zadejte název dávkového, vydání kritéria, X12 smlouvy a další nastavení. 


1. V [portál Azure](https://portal.azure.com), vytvoření aplikace logiky s tímto názvem: "BatchX12Messages".

2. V návrháři aplikace logiky, přidejte **Batch** aktivační události, které spustí pracovní postup aplikace logiky. Do vyhledávacího pole zadejte "batch" jako filtr. Vyberte této aktivační události: **Batch – zprávy dávkových**

   ![Přidat aktivační událost Batch](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. Zadejte název pro dávku a zadejte kritéria pro uvolnění batch, například:

   * **Název služby batch**: název sloužící k identifikaci dávky, která je "TestBatch" v tomto příkladu.

   * **Verze kritéria**: verze kritéria batch, které může být založen na počet zpráv, plán nebo obojí.
   
     ![Zadejte podrobnosti dávky aktivační události](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Počet zpráv**: počet zpráv pro uložení jako dávku před uvolněním pro zpracování, což je "5" v tomto příkladu.

     ![Zadejte podrobnosti dávky aktivační události](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Plán**: plánování verze batch pro zpracování, což je "každých 10 minut" v tomto příkladu.

     ![Zadejte podrobnosti dávky aktivační události](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. Přidejte další akci, která kóduje skupinových nebo dávek zprávy a vytvoří X12 zprávu zpracovat v dávce. 

   a. Zvolte **+ nový krok** > **přidat akci**.

   b. Do vyhledávacího pole zadejte "X 12 batch" jako filtr a vyberte akci pro **X12-kódování Batch**. Jako X12 kódování konektoru, je více varianty pro dávku kódování akce. Můžete si vybrat jednu z nich.

   ![Vyberte X12 Batch kódování akce](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. Nastavte vlastnosti pro akci, kterou jste právě přidali.

   * V **název X12 smlouvy** smlouvu vyberte z rozevíracího seznamu. Pokud je seznam prázdný, ujistěte se, které jste vytvořili připojení k vašemu účtu integrace.

   * V **BatchName** vyberte **název dávkového** pole ze seznamu dynamického obsahu.
   
   * V **%{PartitionName/** vyberte **název oddílu** pole ze seznamu dynamického obsahu.

   * V **položky** vyberte **zpracovat v dávce položky** ze seznamu dynamického obsahu.

   ![Podrobnosti dávky kódovat akce](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. Pro účely testování přidat akci HTTP pro odesílání dávkové zprávy do [požadavku Koš služby](https://requestbin.fullcontact.com/). 

   1. Jako filtr do vyhledávacího pole zadejte "HTTP". Vyberte tuto akci: **HTTP - HTTP**
    
      ![Vyberte akci HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. Z **metoda** seznamu, vyberte **POST**. Pro **Uri** pole, generovat identifikátor URI pro vaši žádost o Koš a zadejte tento identifikátor URI. V **textu** pole, když se otevře dynamického seznamu, vyberte **textu** pole v části **Batch kódování podle názvu smlouvy** části. Pokud nevidíte **textu**, zvolte **Další informace naleznete v** vedle **Batch kódování podle názvu smlouvy**.

      ![Zadejte podrobnosti akce HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  Vytvoření aplikace logiky příjemce, uložte svou aplikaci logiky.

    ![Uložení aplikace logiky](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Oddíl může obsahovat maximálně 5 000 zprávy nebo 80 MB. Pokud je splněna buď podmínka, může být dávky vydání, i v případě, že není splněna podmínka uživatelem definované.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Vytvoření aplikace logiky, která odešle X12 zpráv do dávky

Teď vytvořte jeden nebo více aplikace logiky, které odesílají položky do dávky definované aplikace logiky příjemce. Pro odesílatele je třeba zadat aplikaci logiky příjemce a název batch, obsah zprávy a další nastavení. Volitelně můžete zadat klíč oddílu jedinečný k rozdělení dávky do podmnožin shromažďovat položky s tímto klíčem.

Musíte vědět, kam má posílat položek, zatímco aplikace logiky příjemce nepotřebujete znát odesílatelé odesílatele logiku aplikace.


1. Vytvořit jinou aplikaci logiky s tímto názvem: "X12MessageSender". Přidání této aktivační události do aplikace logiky: **požadavku nebo odpovědi - požadavku** 
   
   ![Přidat aktivační událost požadavku](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. Přidejte nový krok pro odesílání zpráv do dávky.

   1. Zvolte **+ nový krok** > **přidat akci**.

   2. Do vyhledávacího pole zadejte "batch" jako filtr. 

3. Vyberte tuto akci: **odesílání zpráv do dávky – zvolte Logic Apps pracovního postupu se aktivační událostí batch**

   ![Vyberte "Odesílání zpráv do dávky"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. Nyní vyberte svou aplikaci logiky "BatchX12Messages", který jste dříve vytvořili, které nyní se zobrazí jako akce.

   ![Vyberte aplikaci logiky "batch příjemce"](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > V seznamu jsou také uvedeny všechny ostatní aplikace logiky, které mají batch aktivační události.

5. Nastavte vlastnosti dávky.

   * **Název služby batch**: název dávkového definované aplikace logiky příjemce, který je v tomto příkladu "TestBatch" a byl ověřen za běhu.

     > [!IMPORTANT]
     > Ujistěte se, že neměnit název batch, které se musí shodovat s názvem dávky, která je zadána aplikace logiky příjemce.
     > Změna názvu batch způsobí, že odesílatel aplikace logiky k selhání.

   * **Obsah zprávy**: zpráva obsah, který chcete poslat do dávky
   
   ![Umožňuje nastavit vlastnosti dávky](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. Uložte svou aplikaci logiky. Aplikace logiky odesílatele nyní vypadá podobně jako tento příklad:

   ![Uložte svou aplikaci logiky odesílatele](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Testování aplikace logiky

Chcete-li otestovat řešení dávkování, post X12 zprávy do aplikace logiky odesílatele z [Postman](https://www.getpostman.com/postman) nebo podobného nástroje. Brzy, měli byste začít získávání X12 zprávy buď jako dávku pěti položek nebo každých 10 minut, vaše žádost o koši – všechny se stejným klíčem oddílu.

## <a name="next-steps"></a>Další postup

* [Zpracování zpráv jako balíků](logic-apps-batch-process-send-receive-messages.md) 
* [Sestavení bez serveru aplikace v sadě Visual Studio s funkcemi a Azure Logic Apps](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Zpracování výjimek a protokolování chyb pro logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

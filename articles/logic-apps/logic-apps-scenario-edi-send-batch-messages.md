---
title: Dávkové zpracování zpráv EDI jako skupiny nebo kolekce – Azure Logic Apps | Dokumentace Microsoftu
description: Odesílání zpráv EDI pro dávkové zpracování ve službě logic apps
keywords: zpracování služby batch, batch, kódování batch
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
ms.openlocfilehash: fb15688968cb29039fc669ed6b8685ba64df9e81
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432129"
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Odeslat X12 zpráv ve službě batch obchodními partnery

V scénáře B2B (B2B) často partneři výměnu zpráv v skupiny nebo v dávkách. Odesílání zpráv do skupiny nebo dávky obchodní partneři, můžete vytvořit dávky s více položkami a pak pomocí X12 akce služby batch ke zpracování těchto položek v dávce.


Zprávy, stejně jako ostatní zprávy do dávek pro X12 používá s dávkovými triggery a akce. Také pro X12, dávka, prochází x X12 kódovat krok před přechodem na partnera nebo jiného umístění. Další informace o s dávkovými triggery a akce, naleznete v tématu [zprávy dávkového zpracování](logic-apps-batch-process-send-receive-messages.md).

Toto téma ukazuje, jak můžete zpracovávat X12 zpráv v dávce pomocí provádí tyto úlohy:
* [Vytvoření aplikace logiky, která přijímá položky a vytvoří dávky](#receiver). Tato aplikace logiky "příjemce" provede následující akce:
 
   * Určuje kritéria, podle před uvolněním položek v dávce název a verzi služby batch.

   * Zpracovává nebo kóduje položky ve službě batch pomocí zadaného X12 identitami smlouvy nebo partnera.

* [Vytvoření aplikace logiky, která odesílá položky do dávky](#sender). Tato aplikace logiky "sender" Určuje, kam má odesílat položky pro dávkové zpracování, které musí být existující aplikaci logiky příjemce.


## <a name="prerequisites"></a>Požadavky

V tomto příkladu, budete potřebovat tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Jinak si můžete [zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* [Účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) , který již má definovaný a spojené s předplatným Azure

* Alespoň dva [partnery](logic-apps-enterprise-integration-partners.md) , kterou jste definovali v účtu integrace. Ujistěte se, že každý partnera používá X12 (standardní kód dopravce alfa) kvalifikátor ve vlastnostech partnerské organizace jako obchodní identity.

* [X12 smlouvy](logic-apps-enterprise-integration-x12.md) , která je již definována v účtu integrace

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Vytvoření logiky aplikace, která přijímá X12 zpráv a vytvoří dávky

Před odesláním zprávy služby batch, je nutné nejprve vytvořit s využitím aplikace logiky "příjemce" **Batch** aktivační události. Tímto způsobem, tato aplikace logiky příjemce můžete vybrat, když vytvoříte aplikaci logiky odesílatele. Pro příjemce, zadejte název služby batch, kritéria, X12 uvolnění smlouvy a další nastavení. 


1. V [webu Azure portal](https://portal.azure.com), vytvořit aplikaci logiky s tímto názvem: "BatchX12Messages".

1. V návrháři pro Logic Apps, přidejte **Batch** triggeru, který spouští pracovního postupu aplikace logiky. Do vyhledávacího pole zadejte jako filtr "batch". Vyberte tento trigger: **služby Batch – zprávy dávkových**

   ![Přidání triggeru Batch](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

1. Zadejte název služby batch a zadejte kritéria uvolnění dávky, například:

   * **Název dávky**: název používaný k identifikaci služby batch, což je "TestBatch" v tomto příkladu.

   * **Kritéria uvolnění**: kritéria uvolnění dávky, která může být založen na počet zpráv a plán.
   
     ![Zadejte podrobnosti o triggeru Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Počet zpráv**: počet zpráv k uložení v dávce před uvolněním pro zpracování, což je "5" v tomto příkladu.

     ![Zadejte podrobnosti o triggeru Batch](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Plán**: plán vydané verze služby batch pro zpracování, což je "každých 10 minut" v tomto příkladu.

     ![Zadejte podrobnosti o triggeru Batch](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


1. Přidejte další akci, která kóduje skupinových nebo dávek zpráv a vytvoří x X12 dávce zprávy. 

   a. Zvolte **+ nový krok** > **přidat akci**.

   b. Do vyhledávacího pole zadejte "X 12 batch" jako filtr a vyberte akci pro **X12 – kódování Batch**. X12, jako je kódování konektor, existuje několik variant pro list kódováním akce. Můžete si vybrat jednu z nich.

   ![Vyberte X12 kódování Batch akce](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
1. Nastavte vlastnosti pro akci, kterou jste právě přidali.

   * V **název X12 smlouvy** smlouvu vyberte z rozevíracího seznamu. Pokud je seznam prázdný, ujistěte se, že jste vytvořili připojení k vašemu účtu integrace.

   * V **BatchName** vyberte **název dávky** pole ze seznamu dynamického obsahu.
   
   * V **%{PartitionName/** vyberte **název oddílu** pole ze seznamu dynamického obsahu.

   * V **položky** vyberte **dávce položky** ze seznamu dynamického obsahu.

   ![Podrobnosti akce kódovat služby batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

1. Pro účely testování, přidání akce HTTP pro odeslání dávkových zprávy do [Bin žádost o služby](https://requestbin.fullcontact.com/). 

   1. Při filtrování do pole hledání zadejte "HTTP". Vyberte tuto akci: **HTTP - HTTP**
    
      ![Výběr akce HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   1. Z **metoda** seznamu vyberte **příspěvek**. Pro **Uri** pole, umožňuje vygenerovat URI pro vaši žádost o bin a zadejte tento identifikátor URI. V **tělo** pole, když se otevře seznam dynamického vyberte **tělo** pole **kódování Batch podle názvu smlouvy** oddílu. Pokud nevidíte **tělo**, zvolte **zobrazit další** vedle **kódování Batch podle názvu smlouvy**.

      ![Zadejte podrobnosti akce HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

1.  Teď, když jste vytvořili aplikaci logiky příjemce, uložte svou aplikaci logiky.

    ![Uložení aplikace logiky](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Oddíl má limit 5 000 zpráv nebo 80 MB. Pokud je splněna některá podmínka, služby batch může být uvolněny, i v případě, že není splněna podmínka definovaný uživatelem.

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Vytvoření aplikace logiky, která odesílá X12 zprávy do dávky

Teď vytvořte jednu nebo víc aplikací logiky, které odesílání položek do služby batch určené aplikace logiky příjemce. Pro odesílatele je třeba zadat příjemce aplikace logiky a název dávky, obsah zprávy a další nastavení. Volitelně můžete zadat jedinečného klíče oddílu pro rozdělení dávku do podmnožiny ke shromažďování položky s tímto klíčem.

Odesílatel aplikace logiky potřebují vědět, kam má odesílat položek, zatímco aplikace logiky příjemce nemusíte vědět nic o odesílatelů.


1. Vytvořit jinou aplikaci logiky s tímto názvem: "X12MessageSender". Přidejte tento trigger aplikace logiky: **požadavku / odpovědi – požadavek** 
   
   ![Přidání triggeru požadavku](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

1. Přidáte nový krok pro odesílání zpráv do dávky.

   1. Zvolte **+ nový krok** > **přidat akci**.

   1. Do vyhledávacího pole zadejte jako filtr "batch". 

1. Vyberte tuto akci: **odesílat zprávy do služby batch – zvolte pracovní postup Logic Apps s dávkovými triggery**

   ![Vyberte "Odesílat zprávy do služby batch"](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

1. Teď vyberte "BatchX12Messages" aplikace logiky, kterou jste vytvořili, který se teď zobrazí jako akci.

   ![Vyberte aplikaci logiky "batch příjemce"](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > Tento seznam obsahuje také všechny ostatní aplikace logic apps s dávkovými triggery.

1. Nastavení vlastností služby batch.

   * **Název dávky**: název dávky určené příjemce aplikace logiky, která je v tomto příkladu "TestBatch" a ověření za běhu.

     > [!IMPORTANT]
     > Ujistěte se, že nezměníte název služby batch, který musí odpovídat názvu služby batch, která je určená aplikace logiky příjemce.
     > Změna názvu služby batch způsobí, že odesílatel aplikace logiky k selhání.

   * **Obsah zprávy**: obsah zprávy, které chcete odeslat do služby batch
   
   ![Nastavení vlastností služby batch](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

1. Uložte svou aplikaci logiky. Aplikace logiky odesílatele teď vypadá podobně jako v tomto příkladu:

   ![Uložení aplikace logiky odesílatele](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Test aplikace logiky

Chcete-li otestovat dávkování řešení, post X12 zprávy do aplikace logiky odesílatele z [Postman](https://www.getpostman.com/postman) nebo podobný nástroj. Brzy, měli byste začít získávání X12 zprávy, buď v dávce pět položek nebo každých 10 minut, v přihrádce vaši žádost - všechny se stejným klíčem oddílu.

## <a name="next-steps"></a>Další postup

* [Zpracování zpráv jako dávek](logic-apps-batch-process-send-receive-messages.md) 
* [Sestavení aplikace bez serveru v sadě Visual Studio s Azure Logic Apps a Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Zpracování výjimek a protokolování chyb pro logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

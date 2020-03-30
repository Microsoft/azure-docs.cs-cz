---
title: Dávkové zpracování ZPRÁV EDI jako skupiny
description: Odesílání a přijímání zpráv EDI jako dávek, skupin nebo kolekcí pomocí dávkového zpracování v Aplikacích Logika Azure
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666665"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Výměna zpráv EDI jako dávek nebo skupin mezi obchodními partnery v Azure Logic Apps

Ve scénářích business to business (B2B) si partneři často vyměňují zprávy ve skupinách nebo *dávkách*. Když vytváříte řešení dávkování s Logic Apps, můžete odesílat zprávy obchodním partnerům a zpracovávat tyto zprávy společně v dávkách. Tento článek ukazuje, jak můžete dávkově zpracovat zprávy EDI pomocí X12 jako příklad, vytvořením aplikace logiky "dávkového odesílatele" a aplikace logiky "dávkového příjemce". 

Dávkování zpráv X12 funguje podobně jako dávkování jiných zpráv; použijete dávkovou aktivační událost, která shromažďuje zprávy do dávky, a dávkovou akci, která odesílá zprávy do dávky. Také X12 dávkování obsahuje X12 kódování krok před zprávy jít do obchodního partnera nebo jiné místo určení. Další informace o aktivační události a akci dávky naleznete v [tématu Dávkové procesní zprávy](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

V tomto článku vytvoříte řešení dávkování vytvořením dvou aplikací logiky v rámci stejného předplatného Azure, oblasti Azure a podle tohoto konkrétního pořadí:

* Aplikace logiky ["dávkový příjemce",](#receiver) která přijímá a shromažďuje zprávy do dávky, dokud není splněna zadaná kritéria pro uvolnění a zpracování těchto zpráv. V tomto scénáři příjemce dávky také kóduje zprávy v dávce pomocí zadané smlouvy X12 nebo identity partnera.

  Ujistěte se, že jste nejprve vytvořili dávkový přijímač, abyste mohli později vybrat cíl dávky při vytváření odesílatele dávky.

* [Aplikace logiky "batch sender",](#sender) která odesílá zprávy dříve vytvořenému dávkovému příjemci. 

Ujistěte se, že váš dávkový příjemce a dávkový odesílatel sdílejí stejné předplatné Azure *a* oblast Azure. Pokud tomu tak není, nelze vybrat dávkový příjemce při vytváření dávkového odesílatele, protože nejsou navzájem viditelné.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto příkladu, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Nebo [si zaregistrujte předplatné s průběžným platbou](https://azure.microsoft.com/pricing/purchase-options/).

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Existující [účet integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) který je přidružený k vašemu předplatnému Azure a je propojený s vašimi aplikacemi logiky

* Nejméně dva stávající [partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) ve vašem účtu integrace. Každý partner musí používat kvalifikátor X12 (Standardní alfa kód dopravce) jako obchodní identitu ve vlastnostech partnera.

* Stávající [smlouva X12](../logic-apps/logic-apps-enterprise-integration-x12.md) ve vašem integračním účtu

* Pokud chcete použít Visual Studio místo portálu Azure, ujistěte se, že jste [nastavili Visual Studio pro práci s Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Vytvořit dávkový přijímač X12

Před odesláním zpráv do dávky musí tato dávka nejprve existovat jako cíl, kam tyto zprávy odesíláte. Takže nejprve musíte vytvořit aplikaci logiky "dávkový přijímač", která začíná aktivační událostí **Batch.** Tímto způsobem při vytváření aplikace logiky "dávkového odesílatele" můžete vybrat aplikaci logiky dávkového příjemce. Dávkový příjemce pokračuje ve shromažďování zpráv, dokud nejsou splněna zadaná kritéria pro uvolnění a zpracování těchto zpráv. Zatímco dávkové příjemce nemusí vědět nic o dávkových odesílatelích, odesilatelové listy musí znát cíl, kam odesílají zprávy. 

Pro tento dávkový přijímač určíte dávkový režim, název, kritéria vydání, smlouvu X12 a další nastavení. 

1. Na [webu Azure Portal](https://portal.azure.com) nebo Visual Studiu vytvořte aplikaci logiky s tímto názvem: BatchX12Messages.

2. [Propojte aplikaci logiky s účtem pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. V Návrháři aplikací logiky přidejte aktivační událost **Batch,** která spustí pracovní postup aplikace logiky. Do vyhledávacího pole zadejte jako filtr "dávka". Vybrat tuto aktivační událost: **Dávkové zprávy**

   ![Aktivační událost Přidat dávku](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Nastavte vlastnosti dávkového přijímače: 

   | Vlastnost | Hodnota | Poznámky | 
   |----------|-------|-------|
   | **Dávkový režim** | Přiřazený |  |  
   | **Název dávky** | TestBatch | K dispozici pouze **v režimu inline** dávky | 
   | **Kritéria vydání** | Počet zpráv na základě, Plán založený | K dispozici pouze **v režimu inline** dávky | 
   | **Počet zpráv** | 10 | K dispozici pouze s **kritérii pro** vydání podle počtu zpráv | 
   | **Interval** | 10 | K dispozici pouze s kritérii pro vydání **založenými na plánu** | 
   | **Frequency** | minute | K dispozici pouze s kritérii pro vydání **založenými na plánu** | 
   ||| 

   ![Poskytnout podrobnosti aktivační události dávky](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Tento příklad nenastavuje oddíl pro dávku, takže každá dávka používá stejný klíč oddílu. Další informace o oddílech naleznete v [tématu Dávkové zpracování zpráv](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Nyní přidejte akci, která kóduje každou dávku: 

   1. Pod aktivační událostí dávky zvolte **Nový krok**.

   2. Do vyhledávacího pole zadejte jako filtr "Dávka X12" a vyberte tuto akci (libovolnou verzi): **Dávkové kódování <*verze*> - X12** 

      ![Vybrat akci kódování dávky X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Pokud jste se dříve nepřipojili ke svému účtu integrace, vytvořte připojení nyní. Zadejte název připojení, vyberte požadovaný účet integrace a pak zvolte **Vytvořit**.

      ![Vytvoření spojení mezi dávkovou kodérem a účtem integrace](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Nastavte tyto vlastnosti pro akci dávkového kodéru:

      | Vlastnost | Popis |
      |----------|-------------|
      | **Název smlouvy X12** | Otevřete seznam a vyberte existující smlouvu. <p>Pokud je seznam prázdný, ujistěte se, že [propojíte aplikaci logiky s účtem integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) který má požadovanou smlouvu. | 
      | **Název dávky** | Klikněte do tohoto pole a po zobrazení seznamu dynamického obsahu vyberte token **Název dávky.** | 
      | **Název oddílu** | Klikněte do tohoto pole a po zobrazení seznamu dynamického obsahu vyberte token **Názvu oddílu.** | 
      | **Items** | Zavřete pole podrobností o položce a klikněte do tohoto pole. Po zobrazení seznamu dynamického obsahu vyberte token **dávkových položek.** | 
      ||| 

      ![Podrobnosti akce Dávkové kódování](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Pro pole **Položky:**

      ![Dávkové kódování položek akcí](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Uložte svou aplikaci logiky. 

7. Pokud používáte Visual Studio, ujistěte se, že [nasadíte aplikaci logiky dávkového přijímače do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). V opačném případě nelze vybrat dávkový přijímač při vytváření dávkového odesílatele.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li zajistit, aby dávkový přijímač fungoval očekávaným způsobem, můžete přidat akci HTTP pro účely testování a odeslat dávkovou zprávu [službě Přihrádka požadavků](https://requestbin.com/). 

1. V části Akce kódování X12 zvolte **Nový krok**. 

2. Do vyhledávacího pole zadejte jako filtr "http". Vyberte tuto akci: **HTTP - HTTP**
    
   ![Vybrat akci HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Nastavte vlastnosti akce HTTP:

   | Vlastnost | Popis | 
   |----------|-------------|
   | **Metoda** | V tomto seznamu vyberte **MOŽNOST POST**. | 
   | **Uri** | Vygenerujte identifikátor URI pro přihrádku požadavků a zadejte tento identifikátor URI do tohoto pole. | 
   | **Text** | Klikněte do tohoto pole a po otevření seznamu dynamického obsahu vyberte token **Tělo,** který se zobrazí v části **Dávka kódovat podle názvu smlouvy**. <p>Pokud token **Body** nevidíte, vedle **položky Batch kódovat podle názvu smlouvy**vyberte **Zobrazit další**. | 
   ||| 

   ![Poskytnutí podrobností akce PROTOKOLU HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Uložte svou aplikaci logiky. 

   Aplikace logiky dávkového příjemce vypadá takto: 

   ![Uložení aplikace logiky dávkového přijímače](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Vytvořit odesinebo žesérie X12

Teď vytvořte jednu nebo více aplikací logiky, které odesílají zprávy do aplikace logiky dávkového přijímače. V každém odesílate dávku zadáte dávkovou aplikaci logiky příjemce a název dávky, obsah zprávy a všechna další nastavení. Volitelně můžete poskytnout jedinečný klíč oddílu rozdělit dávky do podmnožiny shromažďovat zprávy s tímto klíčem. 

* Ujistěte se, že jste již [vytvořili dávkový přijímač,](#receiver) takže při vytváření dávkového odesílatele můžete jako cílovou dávku vybrat existující dávkový příjemce. Zatímco dávkové příjemce nemusí vědět nic o dávkových odesílatelích, odesilatelům dávek musí vědět, kam mají odesílat zprávy. 

* Ujistěte se, že váš dávkový příjemce a odesio je sdílí stejnou oblast Azure *a* předplatné Azure. Pokud tomu tak není, nelze vybrat dávkový příjemce při vytváření dávkového odesílatele, protože nejsou navzájem viditelné.

1. Vytvořit jinou aplikaci logiky s tímto názvem: "SendX12MessagesToBatch" 

2. Do vyhledávacího pole zadejte jako filtr "při požadavku http". Vyberte tuto aktivační událost: **Při přijetí požadavku HTTP** 
   
   ![Přidání aktivační události Požadavku](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Přidejte akci pro odesílání zpráv do dávky.

   1. V části akce požadavku HTTP zvolte **Nový krok**.

   2. Do vyhledávacího pole zadejte jako filtr "dávka". 
   Vyberte seznam **Akce** a pak vyberte tuto akci: **Zvolte pracovní postup Logic Apps s dávkovou aktivační událostí – Odeslat zprávy do dávky.**

      ![Vyberte možnost Vybrat pracovní postup logic apps s dávkovou aktivační událostí.](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Nyní vyberte aplikaci logiky BatchX12Messages, kterou jste dříve vytvořili.

      ![Vyberte aplikaci logiky "dávkový přijímač"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Vyberte tuto akci: **Batch_messages - <*dávkový přijímač* > **

      ![Vybrat akci "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Nastavte vlastnosti odesílatele dávky.

   | Vlastnost | Popis | 
   |----------|-------------| 
   | **Název dávky** | Název dávky definovaný aplikací logiky příjemce, která je "TestBatch" v tomto příkladu <p>**Důležité:** Název dávky získá ověřena za běhu a musí odpovídat názvu určeného aplikací logiky příjemce. Změna názvu dávky způsobí selhání odesílatele dávky. | 
   | **Obsah zprávy** | Obsah zprávy, kterou chcete odeslat, což je token **Body** v tomto příkladu | 
   ||| 
   
   ![Nastavení vlastností dávky](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Uložte svou aplikaci logiky. 

   Aplikace logiky dávkového odesílatele vypadá takto:

   ![Uložení aplikace logiky dávkového odesílatele](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testování aplikací logiky

Chcete-li otestovat dávkové řešení, zadejte zprávy X12 do aplikace logiky dávkového odesílatele od [Postman](https://www.getpostman.com/postman) nebo podobného nástroje. Brzy začnete dostávat zprávy X12 v přihrádce požadavků, a to buď každých 10 minut, nebo v dávkách po 10, všechny se stejným klíčem oddílu.

## <a name="next-steps"></a>Další kroky

* [Zpracovat zprávy jako dávky](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 

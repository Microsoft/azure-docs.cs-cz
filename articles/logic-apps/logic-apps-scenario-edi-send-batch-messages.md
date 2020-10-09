---
title: Dávkové zpracování zpráv EDI jako skupiny
description: Posílání a přijímání zpráv EDI jako dávek, skupin nebo kolekcí pomocí dávkového zpracování v Azure Logic Apps
services: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 6fc0833f70e3e9cd98100f193b52e5a1bfa4d651
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75666665"
---
# <a name="exchange-edi-messages-as-batches-or-groups-between-trading-partners-in-azure-logic-apps"></a>Zprávy EDI Exchange jako dávky nebo skupiny mezi obchodními partnery v Azure Logic Apps

Ve scénářích B2B (Business to Business) partneři často vyměňují zprávy ve skupinách nebo *dávkách*. Při sestavování řešení Batch pomocí Logic Apps můžete posílat zprávy obchodním partnerům a zpracovávat tyto zprávy v dávkách. V tomto článku se dozvíte, jak můžete dávkové zpracování zpráv EDI pomocí X12 vytvořit jako příklad vytvořením aplikace logiky "Batch Sender" a aplikace logiky Batch Receiver. 

Dávkování zpráv X12 funguje jako dávkování jiných zpráv; použijete Trigger dávky, který shromáždí zprávy do dávky a akci dávky, která odesílá zprávy do dávky. X12 Batch zahrnuje také krok kódování X12 předtím, než zprávy přejdou na obchodního partnera nebo na jiný cíl. Další informace o triggeru a akci dávky najdete v tématu [dávkové zpracování zpráv](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

V tomto článku sestavíte řešení pro dávkování tak, že vytvoříte dvě aplikace logiky v rámci stejného předplatného Azure, oblasti Azure a použijete toto konkrétní pořadí:

* Aplikace logiky pro [přijímače "Batch"](#receiver) , která přijímá a shromažďuje zprávy do dávky, dokud nebudou zadaná kritéria splněna pro uvolnění a zpracování těchto zpráv. V tomto scénáři příjemce dávky také zakóduje zprávy v dávce pomocí zadaných smluv X12 nebo identit partnerů.

  Ujistěte se, že jste nejdřív vytvořili přijímače Batch, abyste později mohli při vytváření odesílatele Batch vybrat cíl dávky.

* Aplikace logiky ["Batch Sender"](#sender) , která posílá zprávy dříve vytvořenému příjemci dávky. 

Ujistěte se, že přijímač Batch a odesilatel dávky sdílejí stejné předplatné Azure *a* oblast Azure. Pokud ne, nemůžete při vytváření odesílatele Batch vybrat přijímače Batch, protože nejsou navzájem viditelné.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto příkladu, budete potřebovat tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Případně si [můžete zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Existující [účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který je přidružený k vašemu předplatnému Azure a je propojený s vašimi Logic Apps

* Alespoň dva stávající [partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) v účtu pro integraci. Každý partner musí použít kvalifikátor X12 (Standard Carrier Alpha Code) jako obchodní identitu ve vlastnostech partnera.

* Existující [smlouva X12](../logic-apps/logic-apps-enterprise-integration-x12.md) v účtu integrace

* Chcete-li použít sadu Visual Studio místo Azure Portal, ujistěte se, že jste [nastavili sadu Visual Studio pro práci s Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Vytvořit přijímač X12 Batch

Než budete moci odeslat zprávy do dávky, tato dávková operace musí nejprve existovat jako cíl, kam tyto zprávy odešlete. Nejprve je třeba vytvořit aplikaci logiky "Batch Receiver", která začíná triggerem **dávky** . Tímto způsobem můžete při vytváření aplikace logiky pro dávkové příjemce vybrat aplikaci Logic Receiver Logic. Přijímač dávky pokračuje ve shromažďování zpráv, dokud nebudou zadaná kritéria splněna pro uvolnění a zpracování těchto zpráv. I když přijímače dávek nemusí nic informovat o modulech pro odesílání dávek, musí odesílatelé dávky znát cíl, kam zprávy odesílají. 

Pro tohoto přijímače Batch zadáte režim dávky, název, kritéria pro vydání, smlouvu X12 a další nastavení. 

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu vytvořte aplikaci logiky s tímto názvem: "BatchX12Messages"

2. [Propojte svou aplikaci logiky s vaším účtem pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. V Návrháři Logic Apps přidejte Trigger **dávky** , který spustí pracovní postup vaší aplikace logiky. Do vyhledávacího pole zadejte jako filtr "Batch". Vyberte tuto aktivační událost: **zprávy Batch**

   ![Přidat aktivační událost dávky](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Nastavte vlastnosti přijímače Batch: 

   | Vlastnost | Hodnota | Poznámky | 
   |----------|-------|-------|
   | **Dávkový režim** | Přiřazený |  |  
   | **Název dávky** | TestBatch | K dispozici pouze s **vloženým** režimem dávky | 
   | **Kritéria uvolnění** | Založené na počtu zpráv, podle plánu | K dispozici pouze s **vloženým** režimem dávky | 
   | **Počet zpráv** | 10 | Dostupné jenom pro kritéria uvolnění **na základě počtu zpráv** | 
   | **Interval** | 10 | K dispozici pouze s kritérii vydání **podle plánu** | 
   | **Frekvence** | minute | K dispozici pouze s kritérii vydání **podle plánu** | 
   ||| 

   ![Zadat podrobnosti triggeru dávky](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > Tento příklad nenastavuje oddíl pro dávku, takže každá dávka používá stejný klíč oddílu. Další informace o oddílech najdete v tématu [dávkové zpracování zpráv](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Teď přidejte akci, která zakóduje každou dávku: 

   1. V aktivační události dávky vyberte **Nový krok**.

   2. Do vyhledávacího pole zadejte jako filtr "X12 Batch" a vyberte tuto akci (jakoukoli verzi): **Batch encode <*verze*>-X12** 

      ![Vybrat akci dávkového kódování X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Pokud jste se předtím nepřipojili k účtu pro integraci, vytvořte připojení nyní. Zadejte název připojení, vyberte účet pro integraci, který chcete, a pak zvolte **vytvořit**.

      ![Vytvoření propojení mezi službou Batch Encoder a integračním účtem](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Nastavte tyto vlastnosti pro akci kodéru služby Batch:

      | Vlastnost | Popis |
      |----------|-------------|
      | **Název smlouvy X12** | Otevřete seznam a vyberte svou stávající smlouvu. <p>Pokud je seznam prázdný, ujistěte se, že jste provedli [propojení aplikace logiky s účtem pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) s požadovanou smlouvou. | 
      | **Batch** | Klikněte do tohoto pole a po zobrazení seznamu dynamický obsah vyberte token pro **název dávky** . | 
      | **PartitionName** | Klikněte do tohoto pole a po zobrazení seznamu dynamický obsah vyberte možnost token **názvu oddílu** . | 
      | **Položky** | Zavřete pole podrobnosti položky a potom klikněte do tohoto pole. Až se zobrazí seznam dynamického obsahu, vyberte token **dávkových položek** . | 
      ||| 

      ![Podrobnosti o akci dávkového kódování](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Pro pole **Items (položky** ):

      ![Dávkové kódování položek akcí](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Uložte aplikaci logiky. 

7. Pokud používáte Visual Studio, ujistěte se, že jste [nasadili aplikaci logiky přijímače z Batch do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). V opačném případě nelze při vytváření odesílatele Batch vybrat přijímače Batch.

### <a name="test-your-logic-app"></a>Testování aplikace logiky

Chcete-li se ujistit, že přijímač Batch funguje podle očekávání, můžete přidat akci HTTP pro účely testování a odeslat dávkovou zprávu do [služby Request bin](https://requestbin.com/). 

1. V akci kódování X12 vyberte **Nový krok**. 

2. Do vyhledávacího pole zadejte "http" jako filtr. Vyberte tuto akci: **http-http**
    
   ![Vybrat akci HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Nastavte vlastnosti pro akci HTTP:

   | Vlastnost | Popis | 
   |----------|-------------|
   | **Metoda** | Z tohoto seznamu vyberte **post**. | 
   | **Identifikátor URI** | Vygenerujte identifikátor URI pro vaši žádost a potom do tohoto pole zadejte tento identifikátor URI. | 
   | **Text** | Klikněte do tohoto pole a po otevření seznamu dynamický obsah vyberte token **textu** , který se zobrazí v části. **Služba Batch zakóduje podle názvu smlouvy**. <p>Pokud se token **textu** nezobrazuje vedle **dávkového kódování podle názvu smlouvy**, vyberte **Zobrazit další**. | 
   ||| 

   ![Zadat podrobnosti akce HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Uložte aplikaci logiky. 

   Aplikace logiky přijímače v dávce vypadá jako v tomto příkladu: 

   ![Uložení aplikace logiky pro přijímače Batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Vytvořit odesílatele X12 Batch

Nyní vytvořte jednu nebo více aplikací logiky, které odesílají zprávy do aplikace Logic přijímače Batch. V každém odesilateli dávky zadáte aplikaci logiky přijímače Batch a název dávky, obsah zprávy a všechna ostatní nastavení. Volitelně můžete zadat jedinečný klíč oddílu pro rozdělení dávky na podmnožiny a shromažďovat zprávy s tímto klíčem. 

* Ujistěte se, že jste už [vytvořili přijímač Batch](#receiver) , takže když vytvoříte odesílatele služby Batch, můžete jako cílovou dávku vybrat stávajícího příjemce dávky. I když přijímače Batch nepotřebují žádné informace o odesílajících dávkách, musí odesílatelé dávky zjistit, kam se mají posílat zprávy. 

* Ujistěte se, že přijímač Batch a odesilatel dávky sdílejí stejnou oblast Azure *a* předplatné Azure. Pokud ne, nemůžete při vytváření odesílatele Batch vybrat přijímače Batch, protože nejsou navzájem viditelné.

1. Vytvořte další aplikaci logiky s tímto názvem: "SendX12MessagesToBatch" 

2. Do vyhledávacího pole zadejte "při požadavku HTTP" jako filtr. Vyberte tuto aktivační událost **, když se přijme požadavek HTTP** . 
   
   ![Přidat aktivační událost žádosti](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Přidejte akci pro odeslání zprávy do dávky.

   1. V části akce žádosti HTTP vyberte **Nový krok**.

   2. Do vyhledávacího pole zadejte jako filtr "Batch". 
   Vyberte seznam **Akce** a potom vyberte tuto akci: **zvolte pracovní postup Logic Apps s triggerem Batch – odesílání zpráv do dávky**

      ![Vyberte "zvolit pracovní postup Logic Apps s triggerem dávky"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Teď vyberte svou aplikaci logiky "BatchX12Messages", kterou jste předtím vytvořili.

      ![Výběr aplikace logiky pro přijímače v dávce](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Vyberte tuto akci: **Batch_messages-<*vašeho-Batch-Receiver* > **

      ![Vybrat akci Batch_messages](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Nastavte vlastnosti odesilatele dávky.

   | Vlastnost | Popis | 
   |----------|-------------| 
   | **Název dávky** | Název dávky definovaný aplikací přijímač Logic, který je v tomto příkladu "TestBatch". <p>**Důležité**: název dávky se ověří za běhu a musí odpovídat názvu určenému aplikací příjemce Logic. Změnou názvu dávky dojde k selhání dávkového odesilatele. | 
   | **Obsah zprávy** | Obsah zprávy, kterou chcete odeslat, což je v tomto příkladu token **textu** | 
   ||| 
   
   ![Nastavení vlastností dávky](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Uložte aplikaci logiky. 

   Vaše aplikace logiky pro odesílatele Batch vypadá jako v tomto příkladu:

   ![Uložení aplikace logiky pro odesílatele služby Batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testování aplikací logiky

K otestování řešení pro dávkování odešlete zprávy X12 do aplikace logiky služby Batch pro aplikaci logicer [z odesílací](https://www.getpostman.com/postman) nebo podobného nástroje. Brzy začnete získávat zprávy X12 ve své přihrádce požadavků, a to buď každých 10 minut, nebo v dávkách po 10, a to pomocí stejného klíče oddílu.

## <a name="next-steps"></a>Další kroky

* [Zpracování zpráv jako dávek](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 

---
title: Dávkové zpracování zpráv EDI jako skupiny nebo kolekce – Azure Logic Apps | Dokumentace Microsoftu
description: Odesílání zpráv EDI pro dávkové zpracování ve službě logic apps
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
manager: jeconnoc
ms.topic: article
ms.date: 08/19/2018
ms.reviewer: estfan, LADocs
ms.openlocfilehash: 77965e20e7d42d12b34bcb2f7cc6c8680ba34b3a
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42059113"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Odesílání zpráv EDI v dávkách obchodními partnery pomocí Azure Logic Apps

Ve scénářích B2B (B2B), partneři často výměna zpráv ve skupinách nebo *dávky*. Při sestavování dávkování řešení s Logic Apps umožňuje odesílání zpráv do obchodními partnery a zpracovat tyto zprávy společně v dávkách. Tento článek popisuje, jak vám může dávkové zpracování zpráv EDI, pomocí X12 například tak, že vytvoříte aplikaci logiky "batch sender" a "batch příjemce" aplikace logiky. 

Dávkování X12 zpráv funguje jako dávkové zpracování další zprávy. pomocí aktivační služby batch, která shromažďuje zprávy do dávky a batch akci, která odesílá zprávy do služby batch. Navíc X12 dávkování zahrnuje x X12 kódování krok před zprávy obchodního partnera nebo jiného umístění. Další informace o s dávkovými triggery a akce, naleznete v tématu [zprávy dávkového zpracování](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

V tomto článku vytvoříte dávkování řešení tak, že vytvoříte dvě aplikace logiky v rámci jednoho předplatného Azure, oblasti Azure a následující této konkrétní pořadí:

* A ["batch příjemce"](#receiver) aplikace logiky, která přijímá a shromažďuje zprávy do dávky, dokud nebude splněna zadaná kritéria pro uvolnění a zpracování těchto zpráv. V tomto scénáři příjemce batch také kóduje zprávy v dávce s použitím zadaného X12 identitami smlouvy nebo partnera.

  Ujistěte se, že nejprve vytvoříte příjemce služby batch, můžete později vyberte cíl služby batch při vytváření služby batch odesílatele.

* A ["batch sender"](#sender) aplikace logiky, která odesílá zprávy do dříve vytvořeného batch příjemce. 

Ujistěte se, že vaše batch příjemce a odesílatele batch sdílet stejné předplatné Azure *a* oblasti Azure. Pokud ne, nemůžete vybrat příjemce batch při vytváření odesílatele služby batch, protože nejsou navzájem viditelné.

## <a name="prerequisites"></a>Požadavky

V tomto příkladu, budete potřebovat tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Nebo, [zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Existující [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který má přidružené k vašemu předplatnému Azure a je propojena s aplikací logiky

* Alespoň dva existující [partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) v účtu integrace. Každý partnera musí používat X12 (standardní kód dopravce alfa) kvalifikátor obchodní identita ve vlastnostech partnerské organizace.

* Existující [X12 smlouvy](../logic-apps/logic-apps-enterprise-integration-x12.md) v účtu integrace

* Pokud chcete použít Visual Studio, nikoli na webu Azure portal, ujistěte se, že jste [instalace sady Visual Studio pro práci s Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>Vytvoření X12 příjemce služby batch

Před odesláním zprávy do dávky této služby batch musí nejprve existovat jako cílové umístění, kam poslat zprávy. Proto napřed musíte vytvořit aplikaci logiky "batch příjemce", který začíná znakem **Batch** aktivační události. Tímto způsobem, při vytváření aplikace logiky "batch sender", můžete vybrat aplikace logiky příjemce služby batch. Příjemce batch pokračuje, shromažďovat zprávy, dokud nebude splněna zadaná kritéria pro uvolnění a zpracování těchto zpráv. Zatímco batch příjemci nemusíte vědět nic o dávkové odesílatelů, musíte znát batch odesílatelů cíle, které odesílají zprávy. 

Pro tento příjemce služby batch, zadejte v dávkovém režimu, název, kritéria uvolnění X12 smlouvy a další nastavení. 

1. V [webu Azure portal](https://portal.azure.com) nebo Visual Studio, vytvořit aplikaci logiky s tímto názvem: "BatchX12Messages"

2. [Propojení aplikace logiky ke svému účtu integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. V návrháři pro Logic Apps, přidejte **Batch** triggeru, který spouští pracovního postupu aplikace logiky. Do vyhledávacího pole zadejte jako filtr "batch". Vyberte tento trigger: **dávkové zprávy**

   ![Přidání triggeru Batch](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. Nastavte služby batch příjemce vlastnosti: 

   | Vlastnost | Hodnota | Poznámky | 
   |----------|-------|-------|
   | **Režim dávky** | Vložené |  |  
   | **Název dávky** | TestBatch | K dispozici pouze s **vložené** režimu služby batch | 
   | **Kritéria uvolnění** | Na základě plánu podle počtu zpráv, | K dispozici pouze s **vložené** režimu služby batch | 
   | **Počet zpráv** | 10 | K dispozici pouze s **zprávy podle počtu** kritéria uvolnění | 
   | **Interval** | 10 | K dispozici pouze s **podle plánu** kritéria uvolnění | 
   | **Frekvence** | minuta | K dispozici pouze s **podle plánu** kritéria uvolnění | 
   ||| 

   ![Zadejte podrobnosti o triggeru batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > V tomto příkladu není nastavení oddílu pro službu batch, aby každá dávka používá stejný klíč oddílu. Další informace o oddílech najdete v tématu [zprávy dávkového zpracování](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender).

5. Teď přidejte akci, která kóduje jednotlivých dávek: 

   1. Pod triggerem dávky, zvolte **nový krok**.

   2. Do vyhledávacího pole zadejte "X 12 batch" jako filtr a vyberte tuto akci (libovolná verze): **kódování Batch <*verze*>-X12** 

      ![Vyberte X12 kódování Batch akce](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. Pokud dříve nebyla připojit k účtu pro integraci, vytvořte teď připojení. Zadejte název připojení, vyberte integrační účet a klikněte na tlačítko **vytvořit**.

      ![Vytvořte připojení mezi kodér a integrace dávku](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. Nastavte tyto vlastnosti pro akci kodér batch:

      | Vlastnost | Popis |
      |----------|-------------|
      | **Název X12 smlouvy** | Otevřete seznam a vyberte stávající smlouvy. <p>Pokud je seznam prázdný, ujistěte se, že jste [propojit účet integrace aplikace logiky](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) , který má smlouvu chcete. | 
      | **BatchName** | Klikněte do tohoto pole a jakmile se objeví v seznamu dynamického obsahu vyberte **název dávky** token. | 
      | **%{PartitionName/** | Klikněte do tohoto pole a jakmile se objeví v seznamu dynamického obsahu vyberte **název oddílu** token. | 
      | **Položky** | Zavřete okno Podrobnosti položky a klikněte do tohoto pole. Jakmile se objeví v seznamu dynamického obsahu vyberte **dávce položky** token. | 
      ||| 

      ![Podrobnosti akce kódovat služby batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      Pro **položky** pole:

      ![Batch kódovat akce položky](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. Uložte svou aplikaci logiky. 

7. Pokud používáte Visual Studio, ujistěte se, že jste [nasazení vaší aplikace logiky příjemce batch do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). V opačném případě nemůžete vybrat příjemce batch při vytváření služby batch odesílatele.

### <a name="test-your-logic-app"></a>Otestujte aplikaci logiky

K Ujistěte se, že vaše batch příjemce funguje podle očekávání, můžete přidat akce HTTP pro účely testování a odesílat zprávu dávkové [Bin žádost o služby](https://requestbin.fullcontact.com/). 

1. V části X12 kódování akce, zvolte **nový krok**. 

2. Do vyhledávacího pole zadejte jako filtr "http". Vyberte tuto akci: **HTTP - HTTP**
    
   ![Výběr akce HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. Nastavení vlastností pro akce HTTP:

   | Vlastnost | Popis | 
   |----------|-------------|
   | **– Metoda** | Z tohoto seznamu, vyberte **příspěvek**. | 
   | **Identifikátor URI** | Umožňuje vygenerovat URI pro vaši žádost o bin a do tohoto pole zadejte tento identifikátor URI. | 
   | **Text** | Klikněte do tohoto pole a po otevření seznamu dynamického obsahu vyberte **tělo** token, který se zobrazí v části, **kódování Batch podle názvu smlouvy**. <p>Pokud nevidíte **tělo** token vedle **kódování Batch podle názvu smlouvy**vyberte **zobrazit další**. | 
   ||| 

   ![Zadejte podrobnosti akce HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. Uložte svou aplikaci logiky. 

   Vaše aplikace logiky příjemce batch bude vypadat jako v tomto příkladu: 

   ![Uložení aplikace logiky příjemce služby batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>Vytvoření X12 odesílatele služby batch

Teď vytvořte jeden nebo více aplikací logiky, které odesílání zpráv do aplikace logiky příjemce služby batch. V každé dávky odesílatele určíte aplikace logiky příjemce služby batch a služby batch název, obsah zprávy a další nastavení. Volitelně můžete zadat jedinečného klíče oddílu pro rozdělení dávku do podmnožiny shromažďovat zprávy pomocí tohoto klíče. 

* Ujistěte se, že jste již [vytvořili přijímač batch](#receiver) tak při vytváření vaší služby batch odesílatele, můžete vybrat existující příjemce batch jako cíl služby batch. Zatímco batch příjemci nemusíte vědět nic o dávkové odesílatelů, odesílatelů batch musíte vědět, kam má odesílat zprávy. 

* Ujistěte se, že vaše batch příjemce a odesílatele batch sdílet stejné oblasti Azure *a* předplatného Azure. Pokud ne, nemůžete vybrat příjemce batch při vytváření odesílatele služby batch, protože nejsou navzájem viditelné.

1. Vytvořit jinou aplikaci logiky s tímto názvem: "SendX12MessagesToBatch" 

2. Do vyhledávacího pole zadejte jako filtr "při požadavku http". Vyberte tento trigger: **přijetí požadavku HTTP je při** 
   
   ![Přidání triggeru požadavku](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. Přidání akce pro odesílání zpráv do dávky.

   1. V akci požadavku HTTP, zvolte **nový krok**.

   2. Do vyhledávacího pole zadejte jako filtr "batch". 
   Vyberte **akce** seznamu a pak vyberte tuto akci: **zvolte pracovní postup Logic Apps s dávkovými triggery – odeslání zprávy do služby batch**

      ![Vyberte "Zvolte pracovní postup Logic Apps s dávkovými triggery"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. Teď vyberte svou aplikaci logiky "BatchX12Messages", který jste předtím vytvořili.

      ![Vyberte aplikaci logiky "batch příjemce"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. Vyberte tuto akci: **Batch_messages - <*your příjemce služby batch*>**

      ![Vyberte akci "Batch_messages"](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. Vlastnosti odesílatele nastavte služby batch.

   | Vlastnost | Popis | 
   |----------|-------------| 
   | **Název dávky** | Název dávky definované aplikací logiky příjemce, který je v tomto příkladu "TestBatch" <p>**Důležité**: název dávky ověří za běhu a název určený příjemce aplikace logiky se musí shodovat. Změna názvu služby batch způsobí, že odesílatel služby batch k selhání. | 
   | **Obsah zprávy** | Obsah pro zprávu chcete odeslat, který je **tělo** tokenu v tomto příkladu | 
   ||| 
   
   ![Nastavení vlastností služby batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. Uložte svou aplikaci logiky. 

   Vaše aplikace logiky odesílatele batch bude vypadat jako v tomto příkladu:

   ![Uložení aplikace logiky odesílatele služby batch](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Test aplikace logiky

Chcete-li otestovat dávkování řešení, post X12 zprávy do aplikace logiky odesílatele batch z [Postman](https://www.getpostman.com/postman) nebo něco podobného. Brzy začít získávat X12 zprávy v požadavku bin, každých 10 minut, nebo v dávkách po 10 všechny se stejným klíčem oddílu.

## <a name="next-steps"></a>Další postup

* [Zpracování zpráv jako dávek](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 

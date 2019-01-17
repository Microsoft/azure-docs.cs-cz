---
title: Dávkové zpracování zpráv jako skupiny nebo kolekce – Azure Logic Apps | Dokumentace Microsoftu
description: Odesílání a příjem zpráv jako dávek v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, LADocs
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: c33b1d46ecf710f050fc998ce27f6448337c6b78
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352508"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Odesílání, příjem a dávkové zpracování zpráv v Azure Logic Apps

K odesílání a zpracování zpráv určitým způsobem společně jako skupiny, můžete vytvořit dávkování řešení, která shromažďuje zprávy do *batch* až do splnění zadaná kritéria pro uvolnění a zpracování dávkové zprávy. Dávkování lze snížit, jak často aplikace logiky zpracovává zprávy. Tento článek popisuje, jak vytvořit dávkování řešení tak, že vytvoříte dvě aplikace logiky v rámci jednoho předplatného Azure, oblast Azure a tuto konkrétní pořadí: 

* ["Batch příjemce"](#batch-receiver) aplikace logiky, která přijímá a shromažďuje zprávy do dávky, dokud nebude splněna zadaná kritéria pro uvolnění a zpracování těchto zpráv.

  Ujistěte se, že nejprve vytvoříte příjemce služby batch, můžete později vyberte cíl služby batch při vytváření služby batch odesílatele.

* Jeden nebo více ["batch sender"](#batch-sender) aplikace logiky, která zasílají zprávy do dříve vytvořeného batch příjemce. 

   Můžete také zadat jedinečný klíč, jako je číslo zákazníka, který *oddíly* nebo cíl batch rozdělí logické podmnožiny na základě tohoto klíče. Tímto způsobem ji příjemce může shromažďovat všechny položky se stejným klíčem a zpracovat je společně.

Ujistěte se, že vaše batch příjemce a odesílatele batch sdílet stejné předplatné Azure *a* oblasti Azure. Pokud ne, nemůžete vybrat příjemce batch při vytváření odesílatele služby batch, protože nejsou navzájem viditelné.

## <a name="prerequisites"></a>Požadavky

V tomto příkladu, budete potřebovat tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Nebo, [zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* E-mailový účet s žádným [poskytovatele e-mailu podporuje Azure Logic Apps](../connectors/apis-list.md)

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Pokud chcete použít Visual Studio, nikoli na webu Azure portal, ujistěte se, že jste [instalace sady Visual Studio pro práci s Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Vytvoření příjemce služby batch

Před odesláním zprávy do dávky této služby batch musí nejprve existovat jako cílové umístění, kam poslat zprávy. Proto napřed musíte vytvořit aplikaci logiky "batch příjemce", který začíná znakem **Batch** aktivační události. Tímto způsobem, při vytváření aplikace logiky "batch sender", můžete vybrat aplikace logiky příjemce služby batch. Příjemce batch pokračuje, shromažďovat zprávy, dokud nebude splněna zadaná kritéria pro uvolnění a zpracování těchto zpráv. Zatímco batch příjemci nemusíte vědět nic o dávkové odesílatelů, musíte znát batch odesílatelů cíle, které odesílají zprávy. 

1. V [webu Azure portal](https://portal.azure.com) nebo Visual Studio, vytvořit aplikaci logiky s tímto názvem: "BatchReceiver" 

2. V návrháři pro Logic Apps, přidejte **Batch** triggeru, který spouští pracovního postupu aplikace logiky. Do vyhledávacího pole zadejte jako filtr "batch". Vyberte tento trigger: **Zprávy služby batch**

   ![Přidání triggeru "Dávkové zprávy"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Tyto vlastnosti nastavte pro příjemce batch: 

   | Vlastnost | Popis | 
   |----------|-------------|
   | **Režim dávky** | - **Vložené**: Při definování kritéria uvolnění uvnitř triggeru batch <br>- **Účet pro integraci**: Pro definování více konfigurací kritéria uvolnění prostřednictvím [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Pomocí účtu pro integraci díky čemuž můžete udržovat tyto konfigurace vše na jednom místě, nikoli v samostatných logic apps. | 
   | **Název dávky** | Název služby batch, je "TestBatch" v tomto příkladu, který se týká pouze **vložené** režimu služby batch |  
   | **Kritéria uvolnění** | Platí jenom pro **vložené** dávkové režimu a vybere kritéria pro splnění před zpracováním jednotlivých dávek: <p>- **Zprávy podle počtu**: Verze služby batch na základě počtu zpráv shromažďovaných služby batch. <br>- **Velikost na základě**: Verze služby batch podle celková velikost v bajtech pro všechny zprávy shromážděné této služby batch. <br>- **Plán**: Verze služby batch na základě plánu opakování, který určuje interval a frekvenci. V části Upřesnit možnosti můžete také vyberte časové pásmo a zadejte datum a čas. <br>- **Vybrat vše**: Použijte všechna zadaná kritéria. | 
   | **Počet zpráv** | Počet zpráv ve službě batch, například shromažďování 10 zpráv. Omezení služby batch je 8 000 zpráv. | 
   | **Velikost dávky** | Celková velikost v bajtech pro shromažďování ve službě batch, například 10 MB. Omezení velikosti služby batch je 80 MB. | 
   | **Plán** | Interval a frekvenci mezi verzemi služby batch, například 10 minut. Minimální opakování je 60 sekund nebo 1 minuta. Zlomcích minut se efektivně zaokrouhluje nahoru na 1 minutu. Chcete-li zadat časové pásmo nebo počáteční datum a čas, zvolte **zobrazit pokročilé možnosti**. | 
   ||| 

   > [!NOTE]
   > 
   > Při změně kritéria uvolnění, zatímco aktivační událost má stále dávce ale neodeslané zprávy, aktivační události na základě kritérií aktualizovanou verzi pro zpracování neodeslané zprávy. 

   Tento příklad ukazuje všechna kritéria, ale pro účely vlastního testování, zkuste pouze jedno kritérium:

   ![Zadejte podrobnosti o triggeru Batch](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

4. Nyní přidejte jednu nebo více akcí, které zpracovávají jednotlivých dávek. 

   V tomto příkladu přidejte akci, která odešle e-mail, když se aktivuje trigger dávky. 
   Trigger spouští a odešle e-mail při dávky je 10 zpráv, dosáhne 10 MB nebo po 10 minutách předat.

   1. Pod triggerem dávky, zvolte **nový krok**.

   2. Při filtrování do pole hledání zadejte „odeslat e-mail“.
   Podle vašeho poskytovatele e-mailu, vyberte konektor e-mailu.

      Pokud máte osobní účet, jako například @outlook.com nebo @hotmail.com, vyberte konektor Outlook.com. 
      Pokud máte účet Gmail vyberte konektor Gmail. 
      Tento příklad používá Office 365 Outlook. 

   3. Vyberte tuto akci: **Odeslání e-mailu – <*poskytovatele e-mailu*>**

      Příklad:

      ![Vyberte akci "Odeslat e-mail" pro vašeho poskytovatele e-mailu](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

5. Pokud budete vyzváni, přihlaste se k e-mailovému účtu. 

6. Nastavte vlastnosti pro akci, kterou jste přidali.

   * Do pole **Komu** zadejte e-mailovou adresu příjemce. 
   Pro účely testování můžete použít svou vlastní e-mailovou adresu.

   * V **subjektu** pole, když se zobrazí v seznamu dynamického obsahu, vyberte **název oddílu** pole.

     ![Ze seznamu dynamického obsahu vyberte "Název oddílu"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Později v odesílatele služby batch můžete zadat jedinečného klíče oddílu, který rozdělí batch cílové logické podmnožiny, kde můžete odesílat zprávy. 
     Každá sada obsahuje jedinečné číslo, které generuje aplikace logiky odesílatele služby batch. 
     Tato funkce umožňuje používat v jedné dávce s více podmnožiny, definovat každý dílčí názvem, který zadáte.

     > [!IMPORTANT]
     > Oddíl má limit 5 000 zpráv nebo 80 MB. Pokud je splněna některá podmínka, vydá Logic Apps služby batch, i v případě, že vaše definované verze podmínka splněna není.

   * V **tělo** pole, když se zobrazí v seznamu dynamického obsahu, vyberte **Id zprávy** pole. 

     Návrhář pro Logic Apps automaticky přidá smyčku "For each" kolem akce Odeslat e-mail, protože tato akce zpracovává výstup z předchozí akce jako kolekci, nikoli dávky. 

     !["Text" Vyberte "Id zprávy"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

7.  Uložte svou aplikaci logiky. Právě jste vytvořili příjemce služby batch.

    ![Uložení aplikace logiky](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

8. Pokud používáte Visual Studio, ujistěte se, že jste [nasazení vaší aplikace logiky příjemce batch do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). V opačném případě nemůžete vybrat příjemce batch při vytváření služby batch odesílatele.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Vytvoření odesílatele služby batch

Teď vytvořte jeden nebo více aplikací logiky odesílatele batch, které odesílání zpráv do aplikace logiky příjemce služby batch. V každé dávky odesílatele je třeba zadat příjemce služby batch a služby batch název, obsah zprávy a další nastavení. Volitelně můžete zadat jedinečného klíče oddílu pro rozdělení dávku do logických podmnožiny ke shromažďování zpráv s tímto klíčem. 

* Ujistěte se, že jste již [vytvořili přijímač batch](#batch-receiver) tak při vytváření vaší služby batch odesílatele, můžete vybrat existující příjemce batch jako cíl služby batch. Zatímco batch příjemci nemusíte vědět nic o dávkové odesílatelů, odesílatelů batch musíte vědět, kam má odesílat zprávy. 

* Ujistěte se, že vaše batch příjemce a odesílatele batch sdílet stejné oblasti Azure *a* předplatného Azure. Pokud ne, nemůžete vybrat příjemce batch při vytváření odesílatele služby batch, protože nejsou navzájem viditelné.

1. Vytvořte jinou aplikaci logiky s tímto názvem: "BatchSender"

   1. Do vyhledávacího pole zadejte jako filtr "opakování". 
   Vyberte tento trigger: **Opakování – plán**

      ![Přidání triggeru "Plán – opakování"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   2. Nastavte četnost a interval spouštění odesílatele aplikace logiky každou minutu.

      ![Nastavte frekvencí a intervalem pro trigger opakování](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

2. Přidání nové akce pro odesílání zpráv do dávky.

   1. V části trigger opakování, zvolte **nový krok**.

   2. Do vyhledávacího pole zadejte jako filtr "batch". 
   Vyberte **akce** seznamu a pak vyberte tuto akci: **Zvolte pracovní postup Logic Apps s dávkovými triggery – odeslání zprávy do služby batch**

      ![Vyberte "Zvolte pracovní postup Logic Apps s dávkovými triggery"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   3. Vyberte svou aplikaci logiky příjemce služby batch, který jste předtím vytvořili.

      ![Vyberte aplikaci logiky "batch příjemce"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!NOTE]
      > Tento seznam obsahuje také všechny ostatní aplikace logic apps s dávkovými triggery. 
      > 
      > Pokud používáte Visual Studio a nevidíte všechny příjemce služby batch můžete vybrat, zkontrolujte, že jste nasadili přijímač batch do Azure. Pokud jste tak dosud, zjistěte, jak [nasazení vaší aplikace logiky příjemce batch do Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). 

   4. Vyberte tuto akci: **Batch_messages - <*your příjemce služby batch*>**

      ![Vyberte tuto akci: "Batch_messages – < your-logic-app >"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

3. Nastavte vlastnosti odesílatele služby batch:

   | Vlastnost | Popis | 
   |----------|-------------| 
   | **Název dávky** | Název dávky definované aplikací logiky příjemce, který je v tomto příkladu "TestBatch" <p>**Důležité**: Název dávky ověří za běhu a musí odpovídat názvu určené aplikace logiky příjemce. Změna názvu služby batch způsobí, že odesílatel služby batch k selhání. | 
   | **Obsah zprávy** | Obsah zprávy, kterou chcete odeslat | 
   ||| 

   V tomto příkladu přidejte tento výraz, který vloží aktuální datum a čas do obsahu zprávy, která odesíláte do služby batch:

   1. Klikněte do **obsahu zprávy** pole. 

   2. Jakmile se zobrazí v seznamu dynamického obsahu, zvolte **výraz**. 

   3. Zadejte výraz `utcnow()`a klikněte na tlačítko **OK**. 

      ![V "Obsah zprávy" zvolte možnost "Výraz", zadejte "utcnow()" a klikněte na tlačítko "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

4. Nyní nastavte oddílu pro dávku. V akci "BatchReceiver" zvolte **zobrazit pokročilé možnosti** a nastavte tyto vlastnosti:

   | Vlastnost | Popis | 
   |----------|-------------| 
   | **Název oddílu** | Volitelné jedinečného klíče oddílu pro cílem dělení batch do logických podmnožin a shromažďovat zprávy na základě tohoto klíče | 
   | **Id zprávy** | Volitelnou zprávu identifikátor, který je generovaný globálně jedinečný identifikátor (GUID), když je prázdný | 
   ||| 

   V tomto příkladu v **název oddílu** přidejte výraz, který generuje náhodné číslo rozsahu od 1 do 5. Nechte **Id zprávy** prázdné.
   
   1. Klikněte do **název oddílu** pole tak, aby zobrazil seznam dynamického obsahu. 

   2. V seznamu dynamického obsahu vyberte **Výraz**.
   
   3. Zadejte výraz `rand(1,6)`a klikněte na tlačítko **OK**.

      ![Nastavení oddílu pro cílové služby batch](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      To **rand** funkce generuje číslo v rozsahu od 1 do 5. 
      Tuto dávku se tak rozdělení do pěti číslované oddíly, které dynamicky nastaví tento výraz.

5. Uložte svou aplikaci logiky. Aplikace logiky odesílatele teď vypadá podobně jako v tomto příkladu:

   ![Uložení aplikace logiky odesílatele](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Test aplikace logiky

K otestování řešení dávkování, ponechte aplikace logiky spuštěná na pár minut. Brzy začít dostávat e-maily ve skupinách po 5, vše se stejným klíčem oddílu.

Svou aplikaci logiky odesílatele batch spustí každou minutu, generuje náhodné číslo rozsahu od 1 do 5 a používá toto generované číslo jako klíč oddílu pro službu batch cílové, kam se mají odesílat zprávy. Pokaždé, když batch má pět položek se stejným klíčem oddílu, vaše aplikace logiky příjemce batch aktivuje a odešle e-mail pro každou zprávu.

> [!IMPORTANT]
> Po dokončení testování, ujistěte se, že zakážete BatchSender aplikaci logiky zastavit odesílání zpráv a předcházeli přetížení sítí doručené pošty.

## <a name="next-steps"></a>Další postup

* [Dávky a odesílání zpráv EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Vytváření definic aplikací logiky s použitím souboru JSON](../logic-apps/logic-apps-author-definitions.md)
* [Sestavení aplikace bez serveru v sadě Visual Studio s Azure Logic Apps a Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Zpracování výjimek a protokolování chyb pro logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

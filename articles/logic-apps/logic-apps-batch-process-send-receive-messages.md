---
title: Zpracování zpráv služby batch jako skupiny nebo kolekce - Azure Logic Apps | Microsoft Docs
description: Odesílat a přijímat zprávy pro dávkové zpracování v aplikacích logiky
keywords: dávkové zpracování balíku
author: jonfancey
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
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: 2815ce7fe0e10aadb60eaa77b58e5395fb5c98d8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298011"
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>Odesílání, příjem a zpracování zpráv v aplikacích logiky služby batch

Zpracování zpráv společně ve skupinách, můžete odeslat datových položek, nebo zprávy, na *batch*a pak tyto položky zpracovávat jako dávku. Tento přístup je užitečné, když chcete zkontrolujte, zda jsou seskupené v určitým způsobem datové položky a společném zpracování. 

Můžete vytvořit logiku aplikace, které zobrazí položky jako dávce pomocí **Batch** aktivační události. Potom můžete vytvořit logiku aplikace, které položky poslat dávce pomocí **Batch** akce.

Toto téma ukazuje, jak můžete vytvořit dávkování řešení provedením těchto úkolů: 

* [Vytvoření aplikace logiky, která přijímá a shromažďuje položky jako dávku](#batch-receiver). Tato aplikace logiky "batch příjemce", určuje kritéria batch název a verze pro splnění předtím, než aplikaci logiky příjemce uvolní a zpracovává položky. 

* [Vytvoření aplikace logiky, která odesílá položky do dávky](#batch-sender). Tato aplikace logiky "batch sender" Určuje, kde k odeslání položek, které musí být existující aplikace logiky příjemce batch. Můžete také zadat jedinečný klíč, jako je číslo zákazníka, "oddílu" nebo rozdělte cílový batch do podmnožin na základě tohoto klíče. Tímto způsobem, všechny položky s tímto klíčem sběru a zpracování společně. 

## <a name="requirements"></a>Požadavky

Postup popsaný v tomto příkladu, budete potřebovat tyto položky:

* Předplatné Azure. Pokud předplatné nemáte, můžete [začít s bezplatným účtem Azure](https://azure.microsoft.com/free/). Jinak si můžete [zaregistrovat předplatné s průběžnými platbami](https://azure.microsoft.com/pricing/purchase-options/).

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* E-mailový účet s žádným [e-mailu zprostředkovatele nepodporuje Azure Logic Apps](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>Vytvoření aplikace logiky, které přijímají zprávy jako dávky

Předtím, než mohou zasílat zprávy do dávky, je nutné nejprve vytvořit aplikace logiky "batch příjemce" s **Batch** aktivační události. Tímto způsobem, tato aplikace logiky příjemce můžete vybrat, když vytvoříte aplikaci logiky odesílatele. Pro příjemce je třeba zadat název batch, verze kritéria a další nastavení. 

Aplikace logiky odesílatele potřebovat věděli, kde k odeslání položek, zatímco aplikace logiky příjemce nepotřebujete znát odesílatelé.

1. V [portál Azure](https://portal.azure.com), vytvoření aplikace logiky s tímto názvem: "BatchReceiver" 

2. V návrháři aplikace logiky, přidejte **Batch** aktivační události, které spustí pracovní postup aplikace logiky. Do vyhledávacího pole zadejte "batch" jako filtr. Vyberte této aktivační události: **Batch – zprávy dávkových**

   ![Přidat aktivační událost Batch](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Zadejte název pro dávku a zadejte kritéria pro uvolnění batch, například:

   * **Název služby batch**: název sloužící k identifikaci dávky, která je "TestBatch" v tomto příkladu.
   * **Verze kritéria**: verze kritéria batch, které může být založen na počet zpráv, plán nebo obojí.
   
     ![Zadejte podrobnosti dávky aktivační události](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Počet zpráv**: počet zpráv pro uložení jako dávku před uvolněním pro zpracování, což je "5" v tomto příkladu.

     ![Zadejte podrobnosti dávky aktivační události](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Plán**: plánování verze batch pro zpracování, což je "každých 5 minut" v tomto příkladu.

     ![Zadejte podrobnosti dávky aktivační události](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. Přidáte akci, která se pošle e-mailu, když se aktivuje batch aktivační událost. Pokaždé, když dávky má pět položek nebo jeho za posledních 5 minut, aplikaci logiky odešle e-mail.

   1. V části aktivační událost batch, zvolte **+ nový krok** > **přidat akci**.

   2. Do vyhledávacího pole zadejte jako filtr „e-mail“.
   Založená na zprostředkovateli e-mailu, vyberte konektor e-mailu.
   
      Například pokud máte pracovní nebo školní účet, vyberte konektor Office 365 Outlook. 
      Pokud máte účet Gmail, vyberte konektor z Gmailu.

   3. Vyberte tuto akci pro vaše konektor: **{*poskytovatele e-mailu*}-odeslat e-mail**

      Příklad:

      ![Vyberte možnost odeslat e-mail panel. pro váš poskytovatel e-mailu](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. Pokud jste nevytvořili dříve připojení pro poskytovatele e-mailu, zadejte svoje přihlašovací údaje e-mailu pro ověřování při zobrazení výzvy. Další informace o [ověřování přihlašovacích údajů e-mailu](../logic-apps/quickstart-create-first-logic-app-workflow.md).

6. Nastavte vlastnosti pro akci, kterou jste právě přidali.

   * Do pole **Komu** zadejte e-mailovou adresu příjemce. 
   Pro účely testování můžete použít svou vlastní e-mailovou adresu.

   * V **subjektu** pole, když **dynamický obsah** se zobrazí seznam, vyberte **název oddílu** pole.

     ![Ze seznamu "Dynamický obsah" Vyberte "Název oddílu"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     V další části můžete zadat klíč jedinečný oddílu, který rozděluje batch cíl do logické sad, kde můžete odesílat zprávy. 
     Každá sada má jedinečné číslo, které je generován aplikace logiky odesílatele. 
     Tato funkce umožňuje používat jeden batch s více podmnožin a definovat každý podmnožina s názvem, který zadáte.

   * V **textu** pole, když **dynamický obsah** se zobrazí seznam, vyberte **Id zprávy** pole.

     !["Text" Vyberte možnost "Id zprávy"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     Vstup pro odeslání e-mailu akce je pole, a proto návrháře automaticky přidá **pro každou** cykly kolem **e-mailovou zprávu** akce. 
     Tato smyčka provádí vnitřní akce na každou položku v dávce. 
     Aby se aktivační událostí batch nastavena na pět položek, můžete získat čas pět e-maily aktivační událost je aktivována.

7.  Vytvoření aplikace logiky příjemce batch, uložte svou aplikaci logiky.

    ![Uložení aplikace logiky](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Oddíl může obsahovat maximálně 5 000 zprávy nebo 80 MB. Pokud je splněna buď podmínka, může být dávky vydání, i v případě, že není splněna podmínka uživatelem definované.


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>Vytvoření aplikace logiky, které odesílají zprávy do dávky

Teď vytvořte jeden nebo více aplikace logiky, které odesílají položky do dávky definované aplikace logiky příjemce. Pro odesílatele je třeba zadat aplikaci logiky příjemce a název batch, obsah zprávy a další nastavení. Volitelně můžete zadat klíč oddílu jedinečný k rozdělení dávky do podmnožin shromažďovat položky s tímto klíčem.

Aplikace logiky odesílatele potřebovat věděli, kde k odeslání položek, zatímco aplikace logiky příjemce nepotřebujete znát odesílatelé.

1. Vytvořit jinou aplikaci logiky s tímto názvem: "BatchSender"

   1. Do vyhledávacího pole zadejte "recurrence" jako filtr. 
   Vyberte tento trigger: **Plán – Opakování**

      ![Přidat aktivační události "Plán-Recurrence"](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. Nastavení frekvence a intervalu spuštění odesílatel aplikace logiky každou minutu.

      ![Nastavení frekvence a intervalu opakování aktivační události](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. Přidejte nový krok pro odesílání zpráv do dávky.

   1. V části aktivační událost opakování, zvolte **+ nový krok** > **přidat akci**.

   2. Do vyhledávacího pole zadejte "batch" jako filtr. 

   3. Vyberte tuto akci: **odesílání zpráv do dávky – zvolte Logic Apps pracovního postupu se aktivační událostí batch**

      ![Vyberte "Odesílání zpráv do dávky"](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. Nyní vyberte svou aplikaci logiky "BatchReceiver", který jste dříve vytvořili, které nyní se zobrazí jako akce.

      ![Vyberte aplikaci logiky "batch příjemce"](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > V seznamu jsou také uvedeny všechny ostatní aplikace logiky, které mají batch aktivační události.

3. Nastavte vlastnosti dávky.

   * **Název služby batch**: název dávkového definované aplikace logiky příjemce, který je v tomto příkladu "TestBatch" a byl ověřen za běhu.

     > [!IMPORTANT]
     > Ujistěte se, že neměnit název batch, které se musí shodovat s názvem dávky, která je zadána aplikace logiky příjemce.
     > Změna názvu batch způsobí, že odesílatel aplikace logiky k selhání.

   * **Obsah zprávy**: zpráva obsah, který chcete odeslat. 
   V tomto příkladu přidejte tento výraz, který se vloží do obsah zprávy, který odešlete do dávky k aktuálnímu datu a času:

     1. Když **dynamický obsah** seznamu se zobrazí, zvolte **výraz**. 
     2. Zadejte výraz **utcnow()** a zvolte **OK**. 

        ![V "Zpráva obsah" Vyberte "Výraz". Zadejte "utcnow()".](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. Nyní nastavte oddíl pro dávku. V akci "BatchReceiver", zvolte **zobrazit rozšířené možnosti**.

   * **Název oddílu**: volitelné oddílu jedinečný klíč pro použití pro dělení batch cíl. V tomto příkladu přidáte výraz, který generuje náhodné číslo v intervalu jeden a pět.
   
     1. Když **dynamický obsah** seznamu se zobrazí, zvolte **výraz**.
     2. Zadejte tento výraz: **rand(1,6)**

        ![Nastavení pro dávku cílový oddíl](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        To **rand –** funkce generuje v rozmezí jednu až pět. 
        Proto jsou dělení tuto dávku do pěti číslem oddílů, které dynamicky nastaví tento výraz.

   * **Id zprávy**: identifikátor volitelné zpráv a je vytvářenému identifikátoru GUID, pokud je prázdný. 
   V tomto příkladu ponechte toto pole prázdné.

5. Uložte svou aplikaci logiky. Aplikace logiky odesílatele nyní vypadá podobně jako tento příklad:

   ![Uložte svou aplikaci logiky odesílatele](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>Testování aplikace logiky

Chcete-li otestovat dávkování řešení, ponechejte aplikace logiky spuštěná pro několik minut. Později, brzy spuštění získání e-mailů v 5, všechny se stejným klíčem oddílu skupiny.

Aplikace logiky BatchSender spouští každou minutu, vygeneruje náhodné číslo v intervalu jeden a 5 a používá toto generované číslo jako klíč oddílu pro dávku cíl, které jsou odesílány zprávy. Pokaždé, když dávky má pět položek se stejným klíčem oddílu aplikace logiky BatchReceiver aktivuje a odešle e-mailu pro každou zprávu.

> [!IMPORTANT]
> Po dokončení testování, ujistěte se, že zakážete aplikace logiky BatchSender zastavit odesílání zpráv a předcházeli přetížení vaší doručené poště.

## <a name="next-steps"></a>Další postup

* [Sestavení na definice aplikace logiky pomocí JSON](../logic-apps/logic-apps-author-definitions.md)
* [Sestavení bez serveru aplikace v sadě Visual Studio s funkcemi a Azure Logic Apps](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Zpracování výjimek a protokolování chyb pro logic apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

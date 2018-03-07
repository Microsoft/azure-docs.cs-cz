---
title: "Vytvoření pracovních postupů schvalování pro zpracování požadavků na seznam adresátů – Azure Logic Apps | Microsoft Docs"
description: "V tomto kurzu se dozvíte, jak vytvořit automatizované pracovní postupy schvalování pro zpracování přihlašování do seznamu adresátů pomocí Azure Logic Apps."
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 4765a38689d5476331c593b89e54f94d4bd310c3
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="manage-mailing-list-requests-with-a-logic-app"></a>Správa požadavků na seznam adresátů s využitím aplikace logiky

Služba Azure Logic Apps pomáhá automatizovat pracovní postupy a integrovat data napříč službami Azure, službami Microsoftu a dalšími aplikacemi SaaS (software jako služba) a místními systémy. V tomto kurzu se dozvíte, jak vytvořit [aplikaci logiky](../logic-apps/logic-apps-overview.md), která zpracovává žádosti o přihlášení do seznamu adresátů spravovaného službou [MailChimp](https://mailchimp.com/).
Tato aplikace logiky monitoruje tyto žádosti v e-mailovém účtu, odesílá je ke schválení a přidává schválené členy do seznamu adresátů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prázdné aplikace logiky
> * Přidání triggeru, který v e-mailech monitoruje žádosti o přihlášení k odběru
> * Přidání akce, která odesílá e-maily ke schválení nebo zamítnutí těchto žádostí
> * Přidání podmínky, která kontroluje odpověď na žádost o schválení
> * Přidání akce, která přidává schválené členy do seznamu adresátů
> * Přidání podmínky, která kontroluje úspěšné přidání těchto členů do seznamu
> * Přidání akce, která odesílá e-maily s potvrzením, jestli se tito členové úspěšně přidali do seznamu

Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Hotová aplikace logiky na základní úrovni](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Pokud ještě nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a> před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Účet MailChimp. Vytvořte seznam test-members-ML, do kterého vaše aplikace logiky může přidávat e-mailové adresy schválených členů. Pokud účet nemáte, [zaregistrujte si bezplatný účet](https://login.mailchimp.com/signup/) a zjistěte, [jak vytvořit seznam](https://us17.admin.mailchimp.com/lists/#). 

* E-mailový účet Office 365 Outlook nebo Outlook.com s podporou pracovních postupů schvalování. V tomto článku se používá Office 365 Outlook. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V hlavní nabídce Azure zvolte **Vytvořit prostředek** > **Podniková integrace** > **Aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky podle následujícího obrázku a popisu. Až budete hotovi, zvolte **Připnout na řídicí panel** > **Vytvořit**.

   ![Zadání informací o aplikaci logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název** | LA-MailingList | Název vaší aplikace logiky | 
   | **Předplatné** | <*název_vašeho_předplatného_Azure*> | Název vašeho předplatného Azure | 
   | **Skupina prostředků** | LA-MailingList-RG | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) sloužící k uspořádání souvisejících prostředků | 
   | **Umístění** | Východní USA 2 | Oblast, kam se mají ukládat informace o vaší aplikaci logiky | 
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. | 
   |||| 

3. Jakmile se vaše aplikace v Azure nasadí, otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a šablonami pro běžně používané vzory aplikací logiky. V části **Šablony** zvolte **Prázdná aplikace logiky**.

   ![Výběr šablony prázdné aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

Teď přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který naslouchá příchozím e-mailům s žádostmi o přihlášení k odběru.
Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Přidání triggeru pro monitorování e-mailů

1. V návrháři zadejte do vyhledávacího pole „při přijetí e-mailu“. Vyberte trigger pro vašeho poskytovatele e-mailu: **<*váš_poskytovatel_e-mailu*> – Při přijetí nového e-mailu**
   
   ![Výběr triggeru pro konkrétního poskytovatele e-mailu: „Při přijetí nového e-mailu“](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook.
   * Pro osobní účty Microsoft vyberte Outlook.com.

2. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se ke svému e-mailovému účtu, aby služba Logic Apps mohla vytvořit připojení k vašemu e-mailovému účtu.

3. Teď zadejte kritéria, která trigger bude kontrolovat ve všech nových e-mailech.

   1. Určete složku, interval a frekvenci kontroly e-mailů.

      ![Určení složky, intervalu a frekvence kontroly e-mailů](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Nastavení | Hodnota | Popis | 
      | ------- | ----- | ----------- | 
      | **Složka** | Doručená pošta | E-mailová složka, která se má monitorovat | 
      | **Interval** | 1 | Počet intervalů, po které se má čekat mezi kontrolami | 
      | **Frekvence** | Hodina | Jednota času pro každý interval mezi kontrolami  | 
      |  |  |  | 

   2. Zvolte **Zobrazit pokročilé možnosti**. Do pole **Filtr předmětu** zadejte tento text, který má trigger hledat v předmětu e-mailu: ```subscribe-test-members-ML```

      ![Nastavení pokročilých možností](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte na jeho záhlaví.

   ![Tvar sbalení pro skrytí podrobností](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

   Vaše aplikace logiky je teď v provozu, ale kromě kontroly příchozích e-mailů nic nedělá. 
   Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="send-approval-email"></a>Odeslání schvalovacího e-mailu

Teď, když máte trigger, přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), která odešle e-mail ke schválení nebo zamítnutí žádosti. 

1. Pod triggerem zvolte **+ Nový krok** > **Přidat akci**. Vyhledejte „schválení“ a vyberte tuto akci: **<*váš_poskytovatel_e-mailu*> – Odeslat schvalovací e-mail**

   ![Výběr akce <váš_poskytovatel_e-mailu> – Odeslat schvalovací e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Zadejte pro tuto akci informace zobrazené a popsané níže: 

   ![Nastavení schvalovacího e-mailu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Komu** | <*e-mailová_adresa_schvalovatele*> | E-mailová adresa schvalovatele. Pro účely testování můžete použít svou vlastní adresu. | 
   | **Možnosti uživatele** | Approve (Schválit), Reject (Zamítnout) | Možné odpovědi, které může schvalovatel zvolit. Ve výchozím nastavení může schvalovatel jako odpověď zvolit Approve (Schválit) nebo Reject (Zamítnout). | 
   | **Předmět** | Schválit žádost o členství v seznamu test-members-ML | Popisný předmět e-mailu | 
   |  |  |  | 

   Prozatím ignorujte seznam dynamického obsahu, případně seznam vložených parametrů, který se zobrazí po kliknutí do konkrétních textových polí. 
   Tento seznam umožňuje výběr parametrů z předchozích akcí, které můžete ve svém pracovním postupu použít jako vstupy. 
   Šířka vašeho prohlížeče určuje, který seznam se zobrazí. 
 
4. Uložte svou aplikaci logiky.

Dále přidejte podmínku, která kontroluje zvolenou odpověď schvalovatele.

## <a name="check-approval-response"></a>Kontrola odpovědi na žádost o schválení

1. Pod akcí **Odeslat schvalovací e-mail** zvolte **+ Nový krok** > **Přidat podmínku**.

   Zobrazí se obrazec podmínky a všechny dostupné parametry, které můžete do svého pracovního postupu zahrnout jako vstupy. 

2. Přejmenujte podmínku tak, aby její popis lépe vystihoval účel.

   1. V záhlaví podmínky zvolte tlačítko **s třemi tečkami** (**…**) > **Přejmenovat**.

      Příklad s úzkým zobrazením prohlížeče:

      ![Přejmenování podmínky](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Pokud je prohlížeč v širokém zobrazení a seznam dynamického obsahu blokuje přístup k tlačítku se třemi tečkami, zavřete seznam tak, že v podmínce zvolíte **Přidat dynamický obsah**.

   2. Přejmenujte podmínku s použitím tohoto popisu: ```If request approved```

3. Vytvořte podmínku, která kontroluje, jestli schvalovatel vybral možnost **Approve** (Schválit):

   1. Uvnitř podmínky klikněte do pole **Zvolit hodnotu**, které se nachází na levé straně (v širokém zobrazení prohlížeče) nebo nahoře (v úzkém zobrazení prohlížeče).
   Ze seznamu parametrů nebo dynamického obsahu vyberte v části **Odeslat schvalovací e-mail** pole **SelectedOption** (Vybraná možnost).

      Například pokud pracujete v širokém zobrazení, bude vaše podmínka vypadat jako v tomto příkladu:

      ![Výběr možnosti SelectedOption (Vybraná možnost) v části Odeslat schvalovací e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. V poli operátoru porovnání vyberte tento operátor: **se rovná**

   3. V poli **Zvolit hodnotu** na pravé straně (v širokém zobrazení) nebo dole (v úzkém zobrazení), zadejte tuto hodnotu: ```Approve```

      Jakmile budete hotovi, vaše podmínka bude vypadat jako v tomto příkladu:

      ![Dokončená podmínka](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Uložte svou aplikaci logiky.

Dále určete akci, kterou vaše aplikace logiky provede po schválení žádosti schvalovatelem. 

## <a name="add-member-to-mailchimp-list"></a>Přidání člena do seznamu MailChimpu

Teď přidejte akci, která přidá schváleného člena do vašeho seznamu adresátů.

1. Ve větvi podmínky **Pokud je true** zvolte **Přidat akci**.
Vyhledejte „mailchimp“ a vyberte tuto akci: **MailChimp – Přidat člena do seznamu**

   ![Výběr akce MailChimp – Přidat člena do seznamu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. Pokud se zobrazí výzva k přihlášení k vašemu účtu MailChimp, přihlaste se pomocí svých přihlašovacích údajů.

4. Zadejte pro tuto akci zde zobrazené a popsané informace:

   ![Zadání informací pro akci Přidat člena do seznamu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **ID seznamu** | test-members-ML | Název seznamu adresátů MailChimpu | 
   | **Stav** | subscribed (přihlášeno k odběru) | Stav odběru pro nového člena. Další informace najdete v tématu <a href="https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/" target="_blank">Správa odběratelů pomocí rozhraní API MailChimp</a>. | 
   | **E-mailová adresa** | <*e-mailová_adresa_nového_člena*> | Ze seznamu parametrů nebo dynamického obsahu vyberte v části **Při přijetí nového e-mailu** pole **Od**. Tím se předá e-mailová adresa nového člena. 
   |  |  |  | 

5. Uložte svou aplikaci logiky.

Dále přidejte podmínku, abyste mohli kontrolovat, jestli se nový člen úspěšně připojil k vašemu seznamu adresátů. Tím se zajistí, že vás aplikace logiky bude upozorňovat na úspěch nebo selhání této operace.

## <a name="check-for-success-or-failure"></a>Kontrola úspěchu nebo selhání

1. Ve větvi **Pokud je true** pod akcí **Přidat člena do seznamu** zvolte **Další...** > **Přidat podmínku**.

2. Přejmenujte podmínku s použitím tohoto popisu: ```If add member succeeded```

3. Vytvořte podmínku, která bude kontrolovat, jestli bylo připojení schváleného člena k vašemu seznamu adresátů úspěšné nebo neúspěšné:

   1. Uvnitř podmínky klikněte do pole **Zvolit hodnotu**, které se nachází na levé straně (v širokém zobrazení prohlížeče) nebo nahoře (v úzkém zobrazení prohlížeče).
   Ze seznamu parametrů nebo dynamického obsahu vyberte v části **Přidat člena do seznamu** pole **Status** (Stav).

      Například pokud pracujete v širokém zobrazení, bude vaše podmínka vypadat jako v tomto příkladu:

      ![Výběr možnosti Status (Stav) v části Přidat člena do seznamu](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. V poli operátoru porovnání vyberte tento operátor: **se rovná**

   3. V poli **Zvolit hodnotu** na pravé straně (v širokém zobrazení) nebo dole (v úzkém zobrazení), zadejte tuto hodnotu: ```subscribed```

   Jakmile budete hotovi, vaše podmínka bude vypadat jako v tomto příkladu:

   ![Dokončená podmínka](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Dále nastavte e-maily, které se odešlou po úspěšném nebo neúspěšném připojení schváleného člena k vašemu seznamu adresátů.

## <a name="send-email-if-member-added"></a>Odeslání e-mailu v případě přidání člena

1. Ve větvi **Pokud je true** pro podmínku **Pokud přidání člena proběhlo úspěšně** zvolte **Přidat akci**.

   ![Výběr možnosti Přidat akci ve větvi podmínky Pokud je true](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. Vyhledejte „outlook odeslat e-mail“ a vyberte tuto akci: **<*váš_poskytovatel_e-mailu*> – Odeslat e-mail**

   ![Přidání akce Odeslat e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Přejmenujte akci s tímto popisem: ```Send email on success```

4. Zadejte pro tuto akci informace zobrazené a popsané níže:

   ![Zadání informací pro e-mail s informací o úspěchu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Komu** | <*vaše_e-mailová_adresa*> | E-mailová adresa, na kterou se má odeslat e-mail s informací o úspěchu. Pro účely testování můžete použít svou vlastní e-mailovou adresu. | 
   | **Předmět** | <*předmět_e-mailu_s_informací_o_úspěchu*> | Předmět e-mailu s informací o úspěchu. Pro účely tohoto kurzu zadejte následující text a vyberte uvedené pole ze seznamu parametrů nebo dynamického obsahu v části **Přidat člena do seznamu**: <p>Úspěch! Člen byl přidán do seznamu test-members-ML: **E-mailová adresa** | 
   | **Text** | <*text_e-mailu_s_informací_o_úspěchu*> | Obsah textu e-mailu s informací o úspěchu. Pro účely tohoto kurzu zadejte následující text a vyberte uvedená pole ze seznamu parametrů nebo dynamického obsahu v části **Přidat člena do seznamu**:  <p>Nový člen se připojil do seznamu test-members-ML: **E-mailová adresa**</br>Stav přihlášení člena: **Stav** | 
   | | | | 

5. Uložte svou aplikaci logiky.

## <a name="send-email-if-member-not-added"></a>Odeslání e-mailu v případě nepřidání člena

1. Ve větvi **Pokud je false** pro podmínku **Pokud přidání člena proběhlo úspěšně** zvolte **Přidat akci**.

   ![Výběr možnosti Přidat akci ve větvi podmínky Pokud je false](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. Vyhledejte „outlook odeslat e-mail“ a vyberte tuto akci: **<*váš_poskytovatel_e-mailu*> – Odeslat e-mail**

   ![Přidání akce Odeslat e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Přejmenujte akci s tímto popisem: ```Send email on failure```

4. Zadejte pro tuto akci zde zobrazené a popsané informace:

   ![Zadání informací o e-mailu s informací o neúspěchu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Komu** | <*vaše_e-mailová_adresa*> | E-mailová adresa, na kterou se má odeslat e-mail s informací o neúspěchu. Pro účely testování můžete použít svou vlastní e-mailovou adresu. | 
   | **Předmět** | <*předmět_e-mailu_s_informací_o_neúspěchu*> | Předmět e-mailu s informací o neúspěchu. Pro účely tohoto kurzu zadejte následující text a vyberte uvedené pole ze seznamu parametrů nebo dynamického obsahu v části **Přidat člena do seznamu**: <p>Chyba, člen nebyl přidán do seznamu test-members-ML: **E-mailová adresa** | 
   | **Text** | <*text_e-mailu_s_informací_o_neúspěchu*> | Obsah textu e-mailu s informací o neúspěchu. Pro účely tohoto kurzu zadejte tento text: <p>Člen možná již existuje. Zkontrolujte svůj účet MailChimp. | 
   | | | | 

5. Uložte svou aplikaci logiky. 

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

 ![Hotová aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

1. Odešlete sami sobě e-mail s žádostí o připojení k vašemu seznamu adresátů.
Počkejte, až se žádost zobrazí ve vaší doručené poště.

3. Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře zvolte **Spustit**. 

   Pokud váš e-mail obsahuje předmět, který odpovídá filtru předmětu triggeru, aplikace logiky vám odešle e-mail ke schválení žádosti o odběr.

4. Ve schvalovacím e-mailu zvolte **Approve** (Schválit).

5. Pokud emailová adresa odběratele ve vašem seznamu adresátů neexistuje, aplikace logiky přidá e-mailovou adresu tohoto člověka a odešle vám podobný e-mail jako v tomto příkladu:

   ![E-mail s informací o úspěchu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Pokud vaše aplikace logiky nemůže odběratele přidat, obdržíte podobný e-mail jako v tomto příkladu:

   ![E-mail s informací o neúspěchu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. 
   Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. 
   Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Blahopřejeme, právě jste vytvořili a spustili aplikaci logiky, která integruje informace napříč Azure, službami Microsoftu a dalšími aplikacemi SaaS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky, a všechny související prostředky. V hlavní nabídce Azure přejděte na **Skupiny prostředků** a vyberte skupinu prostředků pro vaši aplikaci logiky. Zvolte **Odstranit skupinu prostředků**. Pro ověření zadejte název skupiny prostředků a zvolte **Odstranit**.

![Přehled > Odstranit skupinu prostředků](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která spravuje schvalování požadavků na seznam adresátů. Teď můžete zjistit, jak vytvořit aplikaci logiky, která zpracovává a ukládá e-mailové přílohy díky integraci služeb Azure, mimo jiné služeb Azure Storage a Azure Functions.

> [!div class="nextstepaction"]
> [Zpracování e-mailových příloh](../logic-apps/tutorial-process-email-attachments-workflow.md)
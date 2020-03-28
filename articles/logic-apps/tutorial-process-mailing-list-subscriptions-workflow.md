---
title: Vytváření automatizovaných pracovních postupů založených na schválení
description: Kurz – vytvoření automatizovaného pracovního postupu založeného na schválení, který zpracovává předplatná seznamů adresátů pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: 7d7f573e5b18e6e0e63d3275aecefe408a9143fb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75456613"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Kurz: Vytváření automatických pracovních postupů založených na schválení pomocí aplikací Azure Logic Apps

Tento kurz ukazuje, jak vytvořit [aplikaci logiky,](../logic-apps/logic-apps-overview.md) která automatizuje pracovní postup založený na schválení. Konkrétně tato aplikace logiky zpracovává požadavky na odběr pro seznam adresátů, který je spravován službou [MailChimp.](https://mailchimp.com/) Tato aplikace logiky monitoruje tyto žádosti v e-mailovém účtu, odesílá je ke schválení a přidává schválené členy do seznamu adresátů.

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

![Přehled dokončené aplikace logiky na vysoké úrovni](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné, [zaregistrujte si bezplatný účet Azure,](https://azure.microsoft.com/free/) než začnete.

* Účet MailChimp, který obsahuje seznam s názvem "test-members-ML", kde vaše aplikace logiky můžete přidat e-mailové adresy pro schválené členy. Pokud nemáte účet, [zaregistrujte si bezplatný účet](https://login.mailchimp.com/signup/)a přečtěte si, [jak vytvořit seznam MailChimp](https://us17.admin.mailchimp.com/lists/#).

* E-mailový účet v Office 365 Outlook nebo Outlook.com, který podporuje pracovní postupy schvalování. V tomto článku se používá Office 365 Outlook. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V hlavní nabídce Azure vyberte > Vytvořit > **aplikaci logiky****integrace** **prostředků**.

   ![Vytvoření nového prostředku aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky podle následujícího obrázku a popisu. Až to budete mít, vyberte **Vytvořit**.

   ![Poskytnutí informací o aplikaci logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | LA-MailingList | Název aplikace logiky, který může obsahovat pouze písmena, čísla, pomlčky`-`( ), podtržítka (`_`), závorky`(`( , `)`) a tečky (`.`). Tento příklad používá "LA-MailingList". |
   | **Předplatné** | <*název předplatného Azure*> | Název předplatného Azure |
   | **Skupina prostředků** | LA-MailingList-RG | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md), který se používá k uspořádání souvisejících prostředků. Tento příklad používá "LA-MailingList-RG". |
   | **Umístění** | USA – západ | TOblast, kde chcete uložit informace o aplikaci logiky. Tento příklad používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Po nasazení aplikace Azure na panelu nástrojů Azure vyberte **Oznámení** > **Přejít na prostředek** pro nasazenou aplikaci logiky.

   ![Přejděte na nový prostředek aplikace logiky.](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Nebo můžete najít a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Návrhář aplikací logiky se otevře a zobrazí stránku s úvodním videem a běžně používanými aktivačními událostmi a vzory aplikací logiky. V části **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Vybrat prázdnou šablonu aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Teď přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který naslouchá příchozím e-mailům s žádostmi o přihlášení k odběru. Každá aplikace logiky musí začínat aktivační událostí, která se aktivuje, když dojde k určité události nebo když nová data splňují určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Přidání triggeru pro monitorování e-mailů

1. V Návrháři aplikace logiky zadejte `when email arrives` do vyhledávacího pole jako filtr. V seznamu **Aktivační události** vyberte aktivační událost When a new **email arrives** pro vašeho poskytovatele e-mailu.

   Tento příklad používá aktivační událost Outlooku Office 365:

   ![Vyberte aktivační událost "Když přijde nový e-mail" pro poskytovatele e-mailu.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook.
   * Pro osobní účty Microsoft vyberte Outlook.com.

1. Pokud se zobrazí výzva, přihlaste se ke svému e-mailovému účtu pomocí přihlašovacích údajů, aby aplikace Logic Apps mohla vytvořit připojení k vašemu e-mailovému účtu.

1. V aktivační události zadejte kritéria pro kontrolu všech nových e-mailů.

   1. Určete složku, interval a frekvenci kontroly e-mailů.

      ![Určení složky, intervalu a frekvence kontroly e-mailů](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Vlastnost | Hodnota | Popis |
      |----------|-------|-------------|
      | **Složka** | `Inbox` | E-mailová složka, která se má monitorovat |
      | **Interval** | `1` | Počet intervalů, po které se má čekat mezi kontrolami |
      | **Frequency** | `Hour` | Jednotka času pro opakování |
      ||||

   1. Nyní přidejte další vlastnost do aktivační události, abyste mohli filtrovat na řádku předmětu e-mailu. Otevřete **seznam Přidat nový parametr**a vyberte vlastnost Filtr **předmětu.**

      ![Přidání vlastnosti Filtr předmětu pro aktivaci](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Další informace o vlastnostech této aktivační události najdete v [tématu odkaz na konektor aplikace Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) nebo [odkaz na konektor Outlook.com](https://docs.microsoft.com/connectors/outlook/).

   1. Po zobrazení vlastnosti v aktivační události zadejte tento text:`subscribe-test-members-ML`

      ![Zadat text pro vlastnost Filtr předmětu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte na jeho záhlaví.

   ![Tvar sbalení pro skrytí podrobností](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď v provozu, ale kromě kontroly příchozích e-mailů nic nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="send-approval-email"></a>Odeslání schvalovacího e-mailu

Teď, když máte trigger, přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), která odešle e-mail ke schválení nebo zamítnutí žádosti.

1. Pod aktivační událostí vyberte **Nový krok**. 

1. V části **Zvolte akci**zadejte `approval` do vyhledávacího pole jako filtr. V seznamu akcí vyberte akci **Odeslat e-mail schválení** pro poskytovatele e-mailu. 

   Tento příklad používá akci Outlook office 365:

   ![Výběr akce "Odeslat schvalovací e-mail"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Poskytněte informace o této akci, jak je popsáno: 

   ![Odeslat vlastnosti e-mailu schválení](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Akce** | <*vaše e-mailová adresa*> | E-mailová adresa schvalovatele. Pro účely testování můžete použít svou vlastní adresu. Tento příklad používásophia.owen@fabrikam.comfiktivní " " e-mailovou adresu. |
   | **Subjekt** | `Approve member request for test-members-ML` | Popisný předmět e-mailu |
   | **Možnosti uživatele** | `Approve, Reject` | Možnosti odpovědi, které může schvalovatel vybrat. Ve výchozím nastavení může schvalovatel jako odpověď vybrat buď "Schválit" nebo "Odmítnout". |
   ||||

   Prozatím ignorujte seznam dynamického obsahu, který se zobrazí po klepnutí do určitých polí pro úpravy. Tento seznam umožňuje vybrat dostupný výstup z předchozích akcí, které můžete použít jako vstupy v pracovním postupu.

   Další informace o vlastnostech této akce naleznete v [odkazu na konektor aplikace Office 365 Outlook](https://docs.microsoft.com/connectors/office365/) nebo v odkazu na Outlook.com [konektor](https://docs.microsoft.com/connectors/outlook/).
 
1. Uložte svou aplikaci logiky.

Dále přidejte podmínku pro kontrolu vybrané odpovědi schvalovatele.

## <a name="check-approval-response"></a>Kontrola odpovědi na žádost o schválení

1. V části Akce **Odeslat schválení e-mailu** vyberte **Nový krok**".

1. V části **Zvolit akci**vyberte **Předdefinované**. Do vyhledávacího pole `condition` zadejte jako filtr. Ze seznamu akcí vyberte akci **Podmínka.**

   ![Vyhledání a výběr akce Podmínka](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Přejmenujte podmínku tak, aby její popis lépe vystihoval účel.

   1. Na záhlaví podmínky vyberte **tlačítko elipsy** (**...**) > **Přejmenovat**.

      ![Přejmenovat popis podmínky](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Přejmenujte podmínku s použitím tohoto popisu: `If request approved`

1. Vytvořte podmínku, která zkontroluje, zda schvalovatel vybraný **schválit**.

   1. V podmínce klikněte do pole **Zvolte hodnotu** na levé straně podmínky.

   1. Ze seznamu dynamického obsahu, který se zobrazí, vyberte v části **Odeslat schvalovací e-mail**vlastnost **SelectedOption.**

      ![Ze seznamu dynamického obsahu vyberte "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. V prostředním poli porovnání vyberte **operátor.**

   1. Do pole **Zvolte hodnotu** na pravé straně podmínky zadejte tento text:`Approve`

      Po dokončení bude podmínka vypadat takto:

      ![Dokončená podmínka pro schválený příklad](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Uložte svou aplikaci logiky.

Dále určete akci, kterou vaše aplikace logiky provede po schválení žádosti schvalovatelem. 

## <a name="add-member-to-mailchimp-list"></a>Přidání člena do seznamu MailChimpu

Nyní přidejte akci, která přidá schváleného člena do seznamu adresátů.

1. V větvi podmínka je **pravda,** vyberte **Přidat akci**.

1. V části **Zvolte akci**zadejte `mailchimp` jako filtr a vyberte akci **Přidat člena do seznamu.**

   ![Vyberte akci "Přidat člena do seznamu"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Pokud se zobrazí výzva k přístupu ke svému účtu MailChimp, přihlaste se pomocí přihlašovacích údajů MailChimp.

1. Uveďte informace o této akci, jak je znázorněno a popsáno zde:

   ![Zadání informací pro akci Přidat člena do seznamu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **ID seznamu** | Ano | `test-members-ML` | Název vašeho mailchimp mailing listu. Tento příklad používá "test-members-ML". |
   | **Stav** | Ano | `subscribed` | Vyberte stav předplatného pro nového člena. Tento příklad používá "objednané". <p>Další informace najdete v tématu [Správa odběratelů pomocí rozhraní API MailChimp](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **E-mailová adresa** | Ano | <*e-mailová adresa nového člena*> | Ze seznamu dynamického obsahu vyberte **V** části **Když přijde nová pošta**, která projde e-mailovou adresou nového člena. |
   ||||

   Další informace o vlastnostech této akce naleznete v [odkazu na konektor MailChimp](https://docs.microsoft.com/connectors/mailchimp/).

1. Uložte svou aplikaci logiky.

Dále přidejte podmínku, abyste mohli kontrolovat, jestli se nový člen úspěšně připojil k vašemu seznamu adresátů. Tím se zajistí, že vás aplikace logiky bude upozorňovat na úspěch nebo selhání této operace.

## <a name="check-for-success-or-failure"></a>Kontrola úspěchu nebo selhání

1. Ve větvi **If true** vyberte v části Přidat členy **do seznamu** **položku Přidat akci**.

1. V části **Zvolit akci**vyberte **Předdefinované**. Do vyhledávacího pole `condition` zadejte jako filtr. V seznamu akcí vyberte **Možnost Podmínka**.

1. Přejmenujte podmínku s použitím tohoto popisu: `If add member succeeded`

1. Vytvořte podmínku, která bude kontrolovat, jestli bylo připojení schváleného člena k vašemu seznamu adresátů úspěšné nebo neúspěšné:

   1. V podmínce klikněte do pole **Zvolte hodnotu,** které je na levé straně podmínky. V seznamu dynamického obsahu vyberte v části **Přidat do seznamu člen**vlastnost **Stav.**

      Například váš stav vypadá jako v tomto příkladu:

      ![Výběr možnosti Status (Stav) v části Přidat člena do seznamu](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. V prostředním poli porovnání vyberte **operátor.**

   1. Do pole **Zvolte hodnotu** na pravé straně podmínky zadejte tento text:`subscribed`

      Po dokončení bude podmínka vypadat takto:

      ![Dokončená podmínka pro příklad přihlášení k odběru](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Dále nastavte e-maily, které se odešlou po úspěšném nebo neúspěšném připojení schváleného člena k vašemu seznamu adresátů.

## <a name="send-email-if-member-added"></a>Odeslání e-mailu v případě přidání člena

1. V části **Pokud přidat člen byl úspěšný,** vyberte ve větvi If **true** **položku Přidat akci**.

   ![Ve větvi "Pokud je true" vyberte "Přidat akci"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. V části **Zvolte akci**zadejte `outlook send email` do vyhledávacího pole jako filtr a vyberte akci **Odeslat e-mail.**

   ![Přidání akce Odeslat e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Přejmenujte akci s tímto popisem: `Send email on success`

1. Zadejte pro tuto akci informace zobrazené a popsané níže:

   ![Zadání informací pro e-mail s informací o úspěchu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Akce** | Ano | <*vaše e-mailová adresa*> | E-mailová adresa, na kterou se má odeslat e-mail s informací o úspěchu. Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   | **Subjekt** | Ano | <*předmět-pro-úspěch-e-mail*> | Předmět e-mailu s informací o úspěchu. Pro účely tohoto kurzu zadejte tento text: <p>`Success! Member added to "test-members-ML": ` <p>V seznamu dynamického obsahu vyberte v části **Přidat do seznamu člen**vlastnost **E-mailová adresa.** |
   | **Text** | Ano | <*body-for-success-email*> | Obsah textu e-mailu s informací o úspěchu. Pro účely tohoto kurzu zadejte tento text: <p>`New member has joined "test-members-ML":` <p>V seznamu dynamického obsahu vyberte vlastnost **E-mailová adresa.** <p>Na dalším řádku zadejte tento text:`Member opt-in status: ` <p> V seznamu dynamického obsahu vyberte v části **Přidat do seznamu člen**vlastnost **Stav.** |
   |||||

1. Uložte svou aplikaci logiky.

## <a name="send-email-if-member-not-added"></a>Odeslání e-mailu v případě nepřidání člena

1. V části **Pokud přidat člen byl úspěšný,** vyberte ve větvi If **false** **položku Přidat akci**.

   ![Ve větvi "Pokud false" vyberte "Přidat akci"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. V části **Zvolte akci**zadejte `outlook send email` do vyhledávacího pole jako filtr a vyberte akci **Odeslat e-mail.**

   ![Přidání akce Odeslat e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Přejmenujte akci s tímto popisem: `Send email on failure`

1. Uveďte informace o této akci, jak je znázorněno a popsáno zde:

   ![Zadání informací o e-mailu s informací o neúspěchu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Akce** | Ano | <*vaše e-mailová adresa*> | E-mailová adresa, na kterou se má odeslat e-mail s informací o neúspěchu. Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   | **Subjekt** | Ano | <*předmět-pro-selhání-e-mail*> | Předmět e-mailu s informací o neúspěchu. Pro účely tohoto kurzu zadejte tento text: <p>`Failed, member not added to "test-members-ML": ` <p>V seznamu dynamického obsahu vyberte v části **Přidat do seznamu člen**vlastnost **E-mailová adresa.** |
   | **Text** | Ano | <*body-for-failure-email*> | Obsah textu e-mailu s informací o neúspěchu. Pro účely tohoto kurzu zadejte tento text: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Uložte svou aplikaci logiky. 

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Příklad dokončeného pracovního postupu aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

1. Odešlete sami sobě e-mail s žádostí o připojení k vašemu seznamu adresátů. Počkejte, až se žádost zobrazí ve vaší doručené poště.

1. Chcete-li aplikaci logiky spustit ručně, vyberte na panelu nástrojů návrháře **možnost Spustit**. 

   Pokud váš e-mail obsahuje předmět, který odpovídá filtru předmětu triggeru, aplikace logiky vám odešle e-mail ke schválení žádosti o odběr.

1. V e-mailu schválení vyberte **Schválit**.

1. Pokud emailová adresa odběratele ve vašem seznamu adresátů neexistuje, aplikace logiky přidá e-mailovou adresu tohoto člověka a odešle vám podobný e-mail jako v tomto příkladu:

   ![Příklad e-mailu - úspěšné předplatné](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Pokud vaše aplikace logiky nemůže odběratele přidat, obdržíte podobný e-mail jako v tomto příkladu:

   ![Příklad e-mailu – neúspěšné předplatné](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Blahopřejeme, právě jste vytvořili a spustili aplikaci logiky, která integruje informace napříč Azure, službami Microsoftu a dalšími aplikacemi SaaS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete ukázkovou aplikaci logiky, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky. 

1. V hlavní nabídce Azure přejděte na **Skupiny prostředků** a vyberte skupinu prostředků pro vaši aplikaci logiky.

1. V nabídce skupiny prostředků vyberte **Přehled** > **odstranění skupiny prostředků**. 

   ![Přehled > Odstranit skupinu prostředků](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Jako potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která spravuje schvalování požadavků na seznam adresátů. Teď můžete zjistit, jak vytvořit aplikaci logiky, která zpracovává a ukládá e-mailové přílohy díky integraci služeb Azure, mimo jiné služeb Azure Storage a Azure Functions.

> [!div class="nextstepaction"]
> [Zpracování e-mailových příloh](../logic-apps/tutorial-process-email-attachments-workflow.md)

---
title: Vytváření automatizovaných pracovních postupů založených na schválení
description: Kurz – vytvoření automatizovaného pracovního postupu založeného na schválení, který zpracovává odběry seznamu adresátů pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/20/2019
ms.openlocfilehash: c802fafa92ace2260002f7156b0df9841af8338c
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90029575"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Kurz: vytváření pracovních postupů pro automatizované schvalování pomocí Azure Logic Apps

V tomto kurzu se dozvíte, jak vytvořit [aplikaci logiky](../logic-apps/logic-apps-overview.md) , která automatizuje pracovní postup založený na schválení. Konkrétně tato aplikace logiky zpracovává žádosti o odběr pro seznam adresátů spravovaný službou [MailChimp](https://mailchimp.com/) . Tato aplikace logiky monitoruje tyto žádosti v e-mailovém účtu, odesílá je ke schválení a přidává schválené členy do seznamu adresátů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
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

* Předplatné Azure. Pokud předplatné nemáte, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* Účet MailChimp, který obsahuje seznam s názvem test-Members-ML, kde aplikace logiky může přidat e-mailové adresy pro schválené členy. Pokud účet nemáte, [Zaregistrujte si bezplatný účet](https://login.mailchimp.com/signup/)a podívejte se, [jak vytvořit seznam MailChimp](https://us17.admin.mailchimp.com/lists/#).

* E-mailový účet v Outlooku pro Microsoft 365 nebo Outlook.com, který podporuje schvalovací pracovní postupy. V tomto článku se používá Office 365 Outlook. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. V hlavní nabídce Azure vyberte **vytvořit prostředek**  >  **Integration**  >  **Logic App**.

   ![Vytvoření nového prostředku aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky podle následujícího obrázku a popisu. Po dokončení vyberte **Vytvořit**.

   ![Zadání informací o vaší aplikaci logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | LA-MailingList | Název vaší aplikace logiky, který může obsahovat jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ), kulaté závorky ( `(` , `)` ) a tečky ( `.` ). V tomto příkladu se používá "LA-MailingList". |
   | **Předplatné** | <*Vaše předplatné – Azure-Subscription-Name*> | Název vašeho předplatného Azure |
   | **Skupina prostředků** | LA-MailingList-RG | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md), která se používá k uspořádání souvisejících prostředků. V tomto příkladu se používá "LA-MailingList-RG". |
   | **Umístění** | USA – západ | TNelze načíst oblast, kam se mají ukládat informace o aplikaci logiky V tomto příkladu se používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Až Azure nasadí vaši aplikaci, vyberte na panelu nástrojů Azure možnost **oznámení**  >  **Přejít na prostředek** pro vaši nasazenou aplikaci logiky.

   ![Přejít na nový prostředek aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/go-to-logic-app-resource.png)

   Nebo můžete vyhledat a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Otevře se Návrhář Logic Apps a zobrazí se stránka s úvodním videem a běžně používanými triggery a vzorci aplikace logiky. V oblasti **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Vybrat šablonu prázdná aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Teď přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který naslouchá příchozím e-mailům s žádostmi o přihlášení k odběru. Každá aplikace logiky musí začít triggerem, který se aktivuje, když dojde ke konkrétní události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Přidání triggeru pro monitorování e-mailů

1. V návrháři aplikace logiky zadejte do vyhledávacího pole `when email arrives` jako filtr. V seznamu **triggery** vyberte, kdy se má aktivovat **nový e-mail** pro poskytovatele e-mailu.

   V tomto příkladu se používá aktivační událost sady Office 365 Outlook:

   ![Vyberte, kdy se má aktivovat nový e-mail pro poskytovatele e-mailu.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook.
   * Pro osobní účty Microsoft vyberte Outlook.com.

1. Pokud se zobrazí výzva, přihlaste se k e-mailovému účtu pomocí svých přihlašovacích údajů, aby Logic Apps mohli vytvořit připojení k e-mailovému účtu.

1. V aktivační události zadejte kritéria pro kontrolu všech nových e-mailů.

   1. Určete složku, interval a frekvenci kontroly e-mailů.

      ![Určení složky, intervalu a frekvence kontroly e-mailů](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Vlastnost | Hodnota | Popis |
      |----------|-------|-------------|
      | **Složka** | `Inbox` | E-mailová složka, která se má monitorovat |
      | **Interval** | `1` | Počet intervalů, po které se má čekat mezi kontrolami |
      | **Frekvence** | `Hour` | Jednotka času pro opakování |
      ||||

   1. Nyní do triggeru přidejte další vlastnost, aby bylo možné filtrovat podle řádku předmětu e-mailu. Otevřete **seznam přidat nový parametr**a vyberte vlastnost **Filtr předmětu** .

      ![Přidat vlastnost filtr předmětu k triggeru](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Další informace o vlastnostech této triggeru najdete v referenčních informacích ke [konektoru Office 365 Outlook](/connectors/office365/) nebo [konektoru Outlook.com](/connectors/outlook/).

   1. Po zobrazení vlastnosti v triggeru zadejte tento text: `subscribe-test-members-ML`

      ![Zadat text pro vlastnost "filtr předmětu"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte na jeho záhlaví.

   ![Tvar sbalení pro skrytí podrobností](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď v provozu, ale kromě kontroly příchozích e-mailů nic nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="send-approval-email"></a>Odeslání schvalovacího e-mailu

Teď, když máte trigger, přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), která odešle e-mail ke schválení nebo zamítnutí žádosti.

1. V aktivační události vyberte **Nový krok**. 

1. V části **zvolit akci**zadejte do vyhledávacího pole `approval` jako filtr. V seznamu akce vyberte akci **Odeslat e-mailovou** zprávu o schválení pro poskytovatele e-mailů. 

   V tomto příkladu se používá akce Office 365 Outlook:

   ![Výběr akce Odeslat e-mail se schválením](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Zadejte informace o této akci, jak je popsáno níže: 

   ![Odeslat vlastnosti e-mailu schválení](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Záměr** | <*vaše e-mailová adresa*> | E-mailová adresa schvalovatele. Pro účely testování můžete použít svou vlastní adresu. V tomto příkladu se používá fiktivní sophia.owen@fabrikam.com e-mailová adresa. |
   | **Předmět** | `Approve member request for test-members-ML` | Popisný předmět e-mailu |
   | **Možnosti uživatele** | `Approve, Reject` | Možnosti odpovědi, které může schvalovatel vybrat. Ve výchozím nastavení může schvalovatel jako odpověď vybrat buď "schválit" nebo "odmítnout". |
   ||||

   Prozatím ignorujte seznam dynamického obsahu, který se zobrazí po kliknutí do specifických textových polí. Tento seznam vám umožní vybrat dostupný výstup z předchozích akcí, které můžete použít jako vstupy v pracovním postupu.

   Další informace o vlastnostech této akce najdete v referenčních informacích ke [konektoru Office 365 Outlook](/connectors/office365/) nebo [konektoru Outlook.com](/connectors/outlook/).
 
1. Uložte aplikaci logiky.

Dále přidejte podmínku pro kontrolu vybrané odpovědi schvalovatele.

## <a name="check-approval-response"></a>Kontrola odpovědi na žádost o schválení

1. V akci **Odeslat e-mail pro schválení** vyberte **Nový krok**.

1. V části **zvolit akci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `condition` jako filtr. V seznamu akce vyberte akci **Podmínka** .

   ![Vyhledejte a vyberte akci podmínka.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Přejmenujte podmínku tak, aby její popis lépe vystihoval účel.

   1. V záhlaví podmínky vyberte tlačítko se **třemi tečkami** (..**.**) > **Přejmenovat**.

      ![Popis podmínky přejmenování](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

   1. Přejmenujte podmínku s použitím tohoto popisu: `If request approved`

1. Vytvořte podmínku, která kontroluje, zda schvalovatel zvolil **schválení**.

   1. V podmínce klikněte do pole **zvolit hodnotu** na levé straně podmínky.

   1. V seznamu dynamický obsah, který se zobrazí, v části **Odeslat schvalovací e-mail**vyberte vlastnost **SelectedOption** .

      ![V seznamu dynamický obsah vyberte "SelectedOption".](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. V poli prostřední porovnání vyberte operátor **je rovno** .

   1. V poli **zvolit hodnotu** na pravé straně podmínky zadejte tento text: `Approve`

      Jakmile budete hotovi, bude podmínka vypadat jako v tomto příkladu:

      ![Podmínka dokončení pro schválený příklad](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Uložte aplikaci logiky.

Dále určete akci, kterou vaše aplikace logiky provede po schválení žádosti schvalovatelem. 

## <a name="add-member-to-mailchimp-list"></a>Přidání člena do seznamu MailChimpu

Nyní přidejte akci, která přidá schváleného člena do seznamu adresátů.

1. V větvi podmínka **Pokud je true** vyberte **přidat akci**.

1. V části **zvolit akci**zadejte `mailchimp` jako filtr a vyberte akci **Přidat člena do seznamu** .

   ![Výběr akce Přidat člena do seznamu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Pokud se zobrazí výzva k zadání přístupu ke svému účtu MailChimp, přihlaste se pomocí přihlašovacích údajů MailChimp.

1. Zadejte informace o této akci, jak je znázorněno zde:

   ![Zadání informací pro akci Přidat člena do seznamu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **ID seznamu** | Yes | `test-members-ML` | Název seznamu adresátů MailChimp. V tomto příkladu se používá "test-Members-ML". |
   | **Stav** | Yes | `subscribed` | Vyberte stav předplatného pro nového člena. V tomto příkladu se používá "odebírané". <p>Další informace najdete v tématu [Správa odběratelů pomocí rozhraní API MailChimp](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   | **E-mailová adresa** | Yes | <*New-member-e-mailová adresa*> | V seznamu dynamického **obsahu vyberte v** části **při přijetí nového e-mailu**, který předá e-mailovou adresu nového člena. |
   ||||

   Další informace o vlastnostech této akce najdete v referenčních informacích ke [konektoru MailChimp](/connectors/mailchimp/).

1. Uložte aplikaci logiky.

Dále přidejte podmínku, abyste mohli kontrolovat, jestli se nový člen úspěšně připojil k vašemu seznamu adresátů. Tím se zajistí, že vás aplikace logiky bude upozorňovat na úspěch nebo selhání této operace.

## <a name="check-for-success-or-failure"></a>Kontrola úspěchu nebo selhání

1. Ve větvi **Pokud je true** v akci **Přidat člena do seznamu** vyberte **přidat akci**.

1. V části **zvolit akci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `condition` jako filtr. V seznamu akce vyberte **Podmínka**.

1. Přejmenujte podmínku s použitím tohoto popisu: `If add member succeeded`

1. Vytvořte podmínku, která bude kontrolovat, jestli bylo připojení schváleného člena k vašemu seznamu adresátů úspěšné nebo neúspěšné:

   1. V podmínce klikněte do pole **zvolit hodnotu** , které je na levé straně podmínky. V seznamu dynamického obsahu vyberte v části **Přidat člena do seznamu**vlastnost **stav** .

      Například vaše podmínka vypadá jako v tomto příkladu:

      ![Výběr možnosti Status (Stav) v části Přidat člena do seznamu](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. V poli prostřední porovnání vyberte operátor **je rovno** .

   1. V poli **zvolit hodnotu** na pravé straně podmínky zadejte tento text: `subscribed`

      Jakmile budete hotovi, bude podmínka vypadat jako v tomto příkladu:

      ![Dokončená podmínka pro příklad odběru](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Dále nastavte e-maily, které se odešlou po úspěšném nebo neúspěšném připojení schváleného člena k vašemu seznamu adresátů.

## <a name="send-email-if-member-added"></a>Odeslání e-mailu v případě přidání člena

1. V části Pokud je **splněna** podmínka **Přidání člena** , ve větvi Pokud je true vyberte **přidat akci**.

   ![Ve větvi Pokud je true vyberte přidat akci.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. V části **zvolit akci**zadejte do vyhledávacího pole `outlook send email` jako filtr a vyberte akci **Odeslat e-mail** .

   ![Přidat akci "Odeslat e-mail"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Přejmenujte akci s tímto popisem: `Send email on success`

1. Zadejte pro tuto akci informace zobrazené a popsané níže:

   ![Zadání informací pro e-mail s informací o úspěchu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Záměr** | Yes | <*vaše e-mailová adresa*> | E-mailová adresa, na kterou se má odeslat e-mail s informací o úspěchu. Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   | **Předmět** | Yes | <*Předmět pro úspěch – e-mail*> | Předmět e-mailu s informací o úspěchu. Pro účely tohoto kurzu zadejte tento text: <p>`Success! Member added to "test-members-ML": ` <p>V seznamu dynamického obsahu v části **Přidat člena do seznamu**vyberte vlastnost **e-mailová adresa** . |
   | **Text** | Yes | <*text pro úspěch – e-mail*> | Obsah textu e-mailu s informací o úspěchu. Pro účely tohoto kurzu zadejte tento text: <p>`New member has joined "test-members-ML":` <p>V seznamu dynamický obsah vyberte vlastnost **e-mailová adresa** . <p>Do dalšího řádku zadejte tento text: `Member opt-in status: ` <p> V seznamu dynamického obsahu vyberte v části **Přidat člena do seznamu**vlastnost **stav** . |
   |||||

1. Uložte aplikaci logiky.

## <a name="send-email-if-member-not-added"></a>Odeslání e-mailu v případě nepřidání člena

1. V podmínce **úspěšné přidání člena** , ve větvi **Pokud je false** vyberte **přidat akci**.

   ![Ve větvi Pokud je false vyberte přidat akci.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. V části **zvolit akci**zadejte do vyhledávacího pole `outlook send email` jako filtr a vyberte akci **Odeslat e-mail** .

   ![Přidání akce Odeslat e-mail](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Přejmenujte akci s tímto popisem: `Send email on failure`

1. Zadejte informace o této akci, jak je znázorněno zde:

   ![Zadání informací o e-mailu s informací o neúspěchu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Záměr** | Yes | <*vaše e-mailová adresa*> | E-mailová adresa, na kterou se má odeslat e-mail s informací o neúspěchu. Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   | **Předmět** | Yes | <*Předmět pro selhání – e-mail*> | Předmět e-mailu s informací o neúspěchu. Pro účely tohoto kurzu zadejte tento text: <p>`Failed, member not added to "test-members-ML": ` <p>V seznamu dynamického obsahu v části **Přidat člena do seznamu**vyberte vlastnost **e-mailová adresa** . |
   | **Text** | Yes | <*tělo – chyba – e-mail*> | Obsah textu e-mailu s informací o neúspěchu. Pro účely tohoto kurzu zadejte tento text: <p>`Member might already exist. Check your MailChimp account.` |
   |||||

1. Uložte aplikaci logiky. 

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Ukázka dokončeného pracovního postupu aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

1. Odešlete sami sobě e-mail s žádostí o připojení k vašemu seznamu adresátů. Počkejte, až se žádost zobrazí ve vaší doručené poště.

1. Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře vyberte **Spustit**. 

   Pokud váš e-mail obsahuje předmět, který odpovídá filtru předmětu triggeru, aplikace logiky vám odešle e-mail ke schválení žádosti o odběr.

1. V e-mailu pro schválení vyberte **schválit**.

1. Pokud emailová adresa odběratele ve vašem seznamu adresátů neexistuje, aplikace logiky přidá e-mailovou adresu tohoto člověka a odešle vám podobný e-mail jako v tomto příkladu:

   ![Příklad e-mailu – úspěšné předplatné](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Pokud vaše aplikace logiky nemůže odběratele přidat, obdržíte podobný e-mail jako v tomto příkladu:

   ![Příklad e-mailu – neúspěšné předplatné](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

   Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Blahopřejeme, právě jste vytvořili a spustili aplikaci logiky, která integruje informace napříč Azure, službami Microsoftu a dalšími aplikacemi SaaS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už ukázkovou aplikaci logiky nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky. 

1. V hlavní nabídce Azure přejděte na **Skupiny prostředků** a vyberte skupinu prostředků pro vaši aplikaci logiky.

1. V nabídce skupina prostředků vyberte **Přehled**  >  **Odstranit skupinu prostředků**. 

   ![Přehled > Odstranit skupinu prostředků](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. Jako potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která spravuje schvalování požadavků na seznam adresátů. Teď můžete zjistit, jak vytvořit aplikaci logiky, která zpracovává a ukládá e-mailové přílohy díky integraci služeb Azure, mimo jiné služeb Azure Storage a Azure Functions.

> [!div class="nextstepaction"]
> [Zpracování e-mailových příloh](../logic-apps/tutorial-process-email-attachments-workflow.md)

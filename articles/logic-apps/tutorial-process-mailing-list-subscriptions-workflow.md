---
title: Vytváření automatizovaných pracovních postupů založených na schválení
description: Kurz – vytvoření automatizovaného pracovního postupu založeného na schválení, který zpracovává odběry seznamu adresátů pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: c6186e6e9f60e852f77943834bcd1ae3d526491d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777270"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Kurz: vytváření pracovních postupů pro automatizované schvalování pomocí Azure Logic Apps

V tomto kurzu se dozvíte, jak sestavit ukázkovou [aplikaci logiky](../logic-apps/logic-apps-overview.md) , která automatizuje pracovní postup založený na schválení. Konkrétně Tato ukázková aplikace logiky zpracovává žádosti o odběr pro seznam adresátů spravovaný službou [MailChimp](https://mailchimp.com/) . Tato aplikace logiky obsahuje různé kroky, které začnou monitorovat e-mailový účet pro žádosti, odesílá tyto požadavky ke schválení, kontroluje, jestli požadavek získá schválení, přidá schválené členy do seznamu adresátů a potvrdí, jestli se do seznamu přidaly noví členové.

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

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Účet MailChimp, kde jste dříve vytvořili seznam s názvem test-Members-ML, kde aplikace logiky může přidat e-mailové adresy pro schválené členy. Pokud účet nemáte, [Zaregistrujte si bezplatný účet](https://login.mailchimp.com/signup/)a podívejte se, [jak vytvořit seznam MailChimp](https://us17.admin.mailchimp.com/lists/#).

* E-mailový účet od poskytovatele e-mailu, který podporuje Logic Apps, jako je například Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](/connectors/). V tomto rychlém startu se pro Office 365 Outlook používá pracovní nebo školní účet. Pokud používáte jiný e-mailový účet, obecné kroky zůstanou stejné, ale vaše uživatelské rozhraní se může mírně lišit.

* E-mailový účet v Office 365 Outlooku nebo Outlook.com, který podporuje schvalovací pracovní postupy. Tento kurz používá Office 365 Outlook. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

* Pokud vaše aplikace logiky potřebuje komunikovat přes bránu firewall, která omezuje provoz na konkrétní IP adresy, musí brána firewall povolit přístup *pro* [příchozí](logic-apps-limits-and-config.md#inbound) i [odchozí](logic-apps-limits-and-config.md#outbound) IP adresy, které používá služba Logic Apps nebo modul runtime v oblasti Azure, ve které vaše aplikace logiky existuje. Pokud vaše aplikace logiky používá i [spravované konektory](../connectors/managed.md), jako je například konektor Office 365 Outlook nebo konektor SQL, nebo používá [vlastní konektory](/connectors/custom-connectors/), musí brána firewall také umožňovat přístup pro *všechny* [odchozí IP adresy spravovaného konektoru](logic-apps-limits-and-config.md#outbound) v oblasti Azure vaší aplikace logiky.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure. Na domovské stránce Azure vyberte **vytvořit prostředek**.

1. V nabídce Azure Marketplace vyberte aplikace **Integration**  >  **Logic**.

   ![Snímek obrazovky, který zobrazuje Azure Marketplace nabídku s vybranou možnost integrace a aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. V podokně **Aplikace logiky** zadejte zde popsané informace o aplikaci logiky, kterou chcete vytvořit.

   ![Snímek obrazovky, který zobrazuje podokno vytvoření aplikace logiky a informace, které se mají poskytnout pro novou aplikaci logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Předplatné** | <*Azure – předplatné – název*> | Název vašeho předplatného Azure. Tento příklad používá `Pay-As-You-Go`. |
   | **Skupina prostředků** | LA-MailingList-RG | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md), která se používá k uspořádání souvisejících prostředků. Tento příklad vytvoří novou skupinu prostředků s názvem `LA-MailingList-RG` . |
   | **Název** | LA-MailingList | Název vaší aplikace logiky, který může obsahovat jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ), kulaté závorky ( `(` , `)` ) a tečky ( `.` ). Tento příklad používá `LA-MailingList`. |
   | **Umístění** | USA – západ | Oblast, kam se mají ukládat informace o aplikaci logiky Tento příklad používá `West US`. |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**. Až Azure ověří informace o vaší aplikaci logiky, vyberte **vytvořit**.

1. Až Azure nasadí vaši aplikaci, vyberte **Přejít k prostředku**.

   Azure otevře podokno pro výběr šablony Logic Apps, ve kterém se zobrazí úvodní video, běžně používané triggery a vzory šablon aplikací logiky.

1. Posuňte se dolů v částech video a běžné triggery do části **šablony** a vyberte **prázdná aplikace logiky**.

   ![Snímek obrazovky, který zobrazuje podokno pro výběr šablony Logic Apps s vybranou volbou prázdná aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Dále přidejte [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) Outlooku, který naslouchá příchozím e-mailům s požadavky na odběr. Každá aplikace logiky musí začínat triggerem, který se aktivuje, když dojde ke konkrétní události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Přidání triggeru pro monitorování e-mailů

1. Do vyhledávacího pole návrháře Logic Apps zadejte `when email arrives` a **po přijetí nového e-mailu** vyberte aktivační událost s názvem.

   * U pracovních nebo školních účtů Azure vyberte **Office 365 Outlook**.
   * U osobních účtů Microsoft, vyberte **Outlook.com**.

   Tento příklad pokračuje tím, že vyberete Office 365 Outlook.

   ![Snímek obrazovky, který zobrazuje vyhledávací pole návrháře Logic Apps, které obsahuje hledaný termín "když e-mail přijde", a zobrazí se vybraná aktivační událost když nový e-mail přijde.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Pokud ještě nemáte připojení, přihlaste se a po zobrazení výzvy ověřte přístup k e-mailovému účtu.

   Azure Logic Apps vytvoří připojení k vašemu e-mailovému účtu.

1. V aktivační události zadejte kritéria pro kontrolu nového e-mailu.

   1. Zadejte složku pro kontrolu e-mailů a nechte ostatní vlastnosti nastavené na výchozí hodnoty.

      ![Snímek obrazovky, který zobrazuje návrháře s akcí "při přijetí nového e-mailu" a "složka" nastavenou na "Doručená pošta".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Přidejte vlastnost **filtru předmětu** triggeru, abyste mohli filtrovat e-maily na základě řádku předmětu. Otevřete seznam **Přidat nový parametr** a vyberte **Filtr předmětu**.

      ![Snímek obrazovky, který zobrazuje otevřený seznam nový parametr s vybraným "filtrem předmětu".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Další informace o vlastnostech této triggeru najdete v referenčních informacích ke [konektoru Office 365 Outlook](/connectors/office365/) nebo [konektoru Outlook.com](/connectors/outlook/).

   1. Po zobrazení vlastnosti v triggeru zadejte tento text: `subscribe-test-members-ML`

      ![Snímek obrazovky zobrazující vlastnost "filtr předmětu" s textem "odběr-test-Members-ML".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Chcete-li nyní skrýt podrobnosti triggeru, sbalte tvar kliknutím dovnitř záhlaví obrazce.

   ![Snímek obrazovky, který zobrazuje sbalený obrazec triggeru](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď v provozu, ale kromě kontroly příchozích e-mailů nic nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="send-approval-email"></a>Odeslání schvalovacího e-mailu

Teď, když máte trigger, přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), která odešle e-mail ke schválení nebo zamítnutí žádosti.

1. V Návrháři Logic Apps v části **při přijetí nového e-mailu** na triggeru vyberte **Nový krok**.

1. V části **Zvolit operaci** zadejte do vyhledávacího pole `send approval` a vyberte akci s názvem **Odeslat schvalovací e-mail**.

   ![Snímek obrazovky, na kterém se zobrazuje seznam zvolit operaci filtrovaný podle schválení a akce Odeslat e-mail schválení](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Nyní zadejte hodnoty pro zadané vlastnosti zobrazené a popsané zde. ponechte všechny ostatní na jejich výchozích hodnotách. Další informace o těchto vlastnostech najdete v článku referenční informace ke [konektoru Office 365 Outlook](/connectors/office365/) nebo [konektor Outlook.com](/connectors/outlook/).

   ![Snímek obrazovky se zobrazením vlastností e-mail pro odeslání schválení](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Do** | <*schválení-e-mailová adresa*> | E-mailová adresa schvalovatele. Pro účely testování můžete použít svou vlastní adresu. V tomto příkladu se používá fiktivní `sophiaowen@fabrikam.com` e-mailová adresa. |
   | **Předmět** | `Approve member request for test-members-ML` | Popisný předmět e-mailu |
   | **Možnosti uživatele** | `Approve, Reject` | Ujistěte se, že tato vlastnost určuje možnosti odpovědi, které schvalovatel může vybrat, které se ve výchozím nastavení **schvalují** nebo **zamítnou** . |
   ||||

   > [!NOTE]
   > Po kliknutí do některých textových polí se zobrazí seznam dynamického obsahu, který můžete pro nyní ignorovat. Tento seznam obsahuje výstupy z předchozích akcí, které jsou k dispozici pro výběr jako vstupy pro následné akce v pracovním postupu.
 
1. Uložte aplikaci logiky.

Dále přidejte podmínku, která zkontroluje odpověď vybraného schvalovatele.

## <a name="check-approval-response"></a>Kontrola odpovědi na žádost o schválení

1. V akci **Odeslat e-mail pro schválení** vyberte **Nový krok**.

1. V části **Zvolit operaci** vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `condition` a vyberte akci s názvem **Podmínka**.

   ![Snímek obrazovky, který zobrazuje vyhledávací pole "zvolit operaci" s vybraným "předdefinovaným" a "podmínkou" jako hledaný termín, zatímco se zobrazuje akce "podmínka".](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. V záhlaví **podmínky** vyberte tlačítko se **třemi tečkami** (**...**) a pak vyberte **Přejmenovat**. Přejmenujte podmínku s použitím tohoto popisu: `If request approved`

   ![Snímek obrazovky, který zobrazuje tlačítko se třemi tečkami vybraným se zobrazeným seznamem nastavení a vybraným příkazem pro přejmenování](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Vytvořte podmínku, která kontroluje, zda schvalovatel zvolil **schválení**.

   1. Na levé straně podmínky klikněte do pole **zvolit hodnotu** .

   1. V seznamu dynamický obsah, který se zobrazí, v části **Odeslat schvalovací e-mail** vyberte vlastnost **SelectedOption** .

      ![Snímek obrazovky, který zobrazuje seznam dynamického obsahu v části "odeslání e-mailu pro schválení", se zobrazuje výstup "SelectedOption".](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. V poli prostřední porovnání vyberte operátor **je rovno** .

   1. Na pravé straně podmínky v poli **zvolit hodnotu** zadejte text `Approve` .

      Jakmile budete hotovi, bude podmínka vypadat jako v tomto příkladu:

      ![Snímek obrazovky, který ukazuje dokončenou podmínku pro příklad schválené žádosti](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Uložte aplikaci logiky.

Dále určete akci, kterou vaše aplikace logiky provede po schválení žádosti schvalovatelem. 

## <a name="add-member-to-mailchimp-list"></a>Přidání člena do seznamu MailChimpu

Nyní přidejte akci, která přidá schváleného člena do seznamu adresátů.

1. V poli **pravdivá** větev podmínky vyberte **přidat akci**.

1. V poli pro hledání **operace zvolte** možnost **vše**. Do vyhledávacího pole zadejte `mailchimp` a vyberte akci s názvem **Přidat člena do seznamu**.

   ![Snímek obrazovky, který zobrazuje pole "zvolit operaci" s hledaným termínem "MailChimp" a vybranou akcí přidat člena do seznamu.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Pokud ještě nemáte připojení k účtu MailChimp, budete vyzváni k přihlášení.

1. V akci **Přidat člena do seznamu** zadejte informace, jak je znázorněno a popsáno zde:

   ![Snímek obrazovky, který zobrazuje informace o akci přidat člena do seznamu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **ID seznamu** | Yes | <*adresát – seznam názvů*> | Vyberte název seznamu adresátů MailChimp. Tento příklad používá `test-members-ML`. |
   | **E-mailová adresa** | Yes | <*New-member-e-mailová adresa*> | V seznamu dynamický obsah, který se otevře, vyberte v části **při přijetí nového e-mailu** možnost **z**, která je výstupem triggeru, a určete e-mailovou adresu nového člena. |
   | **Stav** | Yes | <*member-Subscription-status*> | Vyberte stav předplatného, který chcete nastavit pro nového člena. Tento příklad vybere `subscribed` . <p>Další informace najdete v tématu [Správa odběratelů pomocí rozhraní API MailChimp](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   Další informace o vlastnostech akce **Přidat člena do seznamu** naleznete v tématu reference ke [konektoru MailChimp](/connectors/mailchimp/).

1. Uložte aplikaci logiky.

Dále přidejte podmínku, abyste mohli kontrolovat, jestli se nový člen úspěšně připojil k vašemu seznamu adresátů. Vaše aplikace logiky tak může upozorňovat na to, jestli tato operace proběhla úspěšně, nebo selhala.

## <a name="check-for-success-or-failure"></a>Kontrola úspěchu nebo selhání

1. Ve větvi **true** vyberte v akci **Přidat člena do seznamu** možnost **přidat akci**.

1. V části **Zvolit operaci** vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `condition` a vyberte akci s názvem **Podmínka**.

1. Přejmenujte podmínku s použitím tohoto popisu: `If add member succeeded`

1. Vytvořte podmínku, která bude kontrolovat, jestli bylo připojení schváleného člena k vašemu seznamu adresátů úspěšné nebo neúspěšné:

   1. Na levé straně podmínky klikněte do pole **zvolit hodnotu** . V seznamu dynamický obsah, který se zobrazí, vyberte v části **Přidat člena do seznamu** vlastnost **stav** .

      Například vaše podmínka vypadá jako v tomto příkladu:

      ![Snímek obrazovky zobrazující levou stranu podmínky "výběr hodnoty", která je zadána jako "stav".](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. V poli prostřední porovnání vyberte operátor **je rovno** .

   1. Na pravé straně podmínky v poli **zvolit hodnotu** zadejte tento text: `subscribed`

      Jakmile budete hotovi, bude podmínka vypadat jako v tomto příkladu:

      ![Snímek obrazovky, který ukazuje dokončenou podmínku pro kontrolu úspěšného nebo neúspěšného předplatného.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Dále nastavte e-maily, které se mají odeslat, když schválený člen buď proběhne úspěšně, nebo selže v připojení k vašemu seznamu adresátů.

## <a name="send-email-if-member-added"></a>Odeslání e-mailu v případě přidání člena

1. V podmínce **úspěšné přidání člena** , ve větvi **true** vyberte **přidat akci**.

   ![Snímek obrazovky, který zobrazuje větev "Pokud byl úspěšně přidán člen" "true" s vybranou možnost přidat akci.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Do vyhledávacího pole **Zvolte operaci** zadejte `outlook send email` a vyberte akci s názvem **Odeslat e-mail**.

   ![Snímek obrazovky, který zobrazuje vyhledávací pole "zvolit operaci" s zadáním "Outlook send email" a akci "Odeslat e-mail", která je vybrána pro oznámení.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Přejmenujte akci s tímto popisem: `Send email on success`

1. V akci **Odeslat e-mail po úspěšném** zadání zadejte informace, jak je znázorněno a popsáno zde:

   ![Snímek obrazovky zobrazující akci odeslat e-mail při úspěchu a informace, které jsou k dispozici pro e-mail s informací o úspěchu](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Text** | Yes | <*úspěch – e-mail – tělo*> | Obsah textu e-mailu s informací o úspěchu. Pro tento kurz použijte následující postup: <p>1. Zadejte tento text s koncovým mezerou: `New member has joined "test-members-ML":` <p>2. ze seznamu dynamického obsahu, který se zobrazí, vyberte vlastnost **e-mailová adresa** . <p>**Poznámka**: Pokud se tato vlastnost nezobrazuje, vedle záhlaví oddílu **Přidat člena do seznamu** vyberte **Zobrazit další**. <p>3. na dalším řádku zadejte tento text s koncovým místem: `Member opt-in status: ` <p>4. ze seznamu dynamického obsahu vyberte v části **Přidat člena do seznamu** vlastnost **stav** . |
   | **Předmět** | Yes | <*úspěch – e-mail – předmět*> | Předmět e-mailu s informací o úspěchu. Pro tento kurz použijte následující postup: <p>1. Zadejte tento text s koncovým mezerou: `Success! Member added to "test-members-ML": ` <p>2. ze seznamu dynamického obsahu vyberte v části **Přidat člena do seznamu** vlastnost **e-mailové adresy** . |
   | **Do** | Yes | <*vaše e-mailová adresa*> | E-mailová adresa, na kterou se má odeslat e-mail s informací o úspěchu. Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   |||||

1. Uložte aplikaci logiky.

## <a name="send-email-if-member-not-added"></a>Odeslání e-mailu v případě nepřidání člena

1. V podmínce **úspěšné přidání člena** , ve větvi **false** vyberte **přidat akci**.

   ![Snímek obrazovky zobrazující větev "Pokud se člen úspěšně přidal" "NEPRAVDA", pokud má vybranou možnost přidat akci](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Do vyhledávacího pole **Zvolte operaci** zadejte `outlook send email` a vyberte akci s názvem **Odeslat e-mail**.

   ![Snímek obrazovky, který zobrazuje vyhledávací pole "zvolit operaci" s uvedeným zadáním "Outlook odeslat e-mail" a je vybrána akce Odeslat e-mail.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Přejmenujte akci s tímto popisem: `Send email on failure`

1. Zadejte informace o této akci, jak je znázorněno zde:

   ![Snímek obrazovky zobrazující akci "Odeslat e-mail při selhání" a informace, které jsou k dispozici pro e-mail o selhání](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Text** | Yes | <*tělo – chyba – e-mail*> | Obsah textu e-mailu s informací o neúspěchu. Pro účely tohoto kurzu zadejte tento text: <p>`Member might already exist. Check your MailChimp account.` |
   | **Předmět** | Yes | <*Předmět pro selhání – e-mail*> | Předmět e-mailu s informací o neúspěchu. Pro tento kurz použijte následující postup: <p>1. Zadejte tento text s koncovým mezerou: `Failed, member not added to "test-members-ML": ` <p>2. ze seznamu dynamického obsahu vyberte v části **Přidat člena do seznamu** vlastnost **e-mailové adresy** . |
   | **Do** | Yes | <*vaše e-mailová adresa*> | E-mailová adresa, na kterou se má odeslat e-mail s informací o neúspěchu. Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   |||||

1. Uložte aplikaci logiky. 

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Snímek obrazovky zobrazující příklad dokončeného pracovního postupu aplikace logiky](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

1. Odešlete sami sobě e-mail s žádostí o připojení k vašemu seznamu adresátů. Počkejte, až se žádost zobrazí ve vaší doručené poště.

1. Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře vyberte **Spustit**. 

   Pokud váš e-mail obsahuje předmět, který odpovídá filtru předmětu triggeru, aplikace logiky vám odešle e-mail ke schválení žádosti o odběr.

1. V přijatém e-mailu pro schválení vyberte **schválit**.

1. Pokud emailová adresa odběratele ve vašem seznamu adresátů neexistuje, aplikace logiky přidá e-mailovou adresu tohoto člověka a odešle vám podobný e-mail jako v tomto příkladu:

   ![Snímek obrazovky, který zobrazuje příklad e-mailu pro úspěšné předplatné.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Pokud vaše aplikace logiky nemůže odběratele přidat, obdržíte podobný e-mail jako v tomto příkladu:

   ![Snímek obrazovky, který zobrazuje příklad e-mailu pro neúspěšné předplatné.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Blahopřejeme, právě jste vytvořili a spustili aplikaci logiky, která integruje informace napříč Azure, službami Microsoftu a dalšími aplikacemi SaaS.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vaše aplikace logiky pokračuje běžet, dokud aplikaci nezakážete nebo neodstraníte. Pokud už ukázkovou aplikaci logiky nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. Do vyhledávacího pole Azure Portal zadejte název skupiny prostředků, kterou jste vytvořili. Z výsledků v části **skupiny prostředků** vyberte skupinu prostředků.

   V tomto příkladu se vytvořila skupina prostředků s názvem `LA-MailingList-RG` .

   ![Snímek obrazovky, který zobrazuje pole Azure Search s uvedením "La-Mailinglist-RG" a * * LA-MailingList-RG * * Selected.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Pokud se na domovské stránce Azure zobrazuje skupina prostředků v části **nedávné prostředky**, můžete vybrat skupinu z domovské stránky.

1. V nabídce skupina prostředků ověřte, že je vybraná možnost **Přehled** . Na panelu nástrojů v podokně **Přehled** vyberte **Odstranit skupinu prostředků**.

   ![Snímek obrazovky, který zobrazuje podokno "Přehled" skupiny prostředků a na panelu nástrojů, je vybrána možnost odstranit skupinu prostředků.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. V podokně potvrzení, které se zobrazí, zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která zpracovává schválení pro žádosti o seznam adresátů. Teď můžete zjistit, jak vytvořit aplikaci logiky, která zpracovává a ukládá e-mailové přílohy díky integraci služeb Azure, mimo jiné služeb Azure Storage a Azure Functions.

> [!div class="nextstepaction"]
> [Zpracování e-mailových příloh](../logic-apps/tutorial-process-email-attachments-workflow.md)

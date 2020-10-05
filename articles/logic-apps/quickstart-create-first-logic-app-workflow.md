---
title: Vytvořit pracovní postup automatizované integrace
description: Rychlý Start – sestavení prvního automatizovaného pracovního postupu pomocí Azure Logic Apps pro řešení Integration System a Enterprise Application Integration (EAI)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/23/2020
ms.openlocfilehash: c40bec80d9f61cf46221cbfe7dde80f3a7b46f6f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89658298"
---
# <a name="quickstart-create-your-first-automated-integration-workflow-by-using-azure-logic-apps---azure-portal"></a>Rychlý Start: vytvoření prvního pracovního postupu automatizované integrace pomocí Azure Logic Apps-Azure Portal

V tomto rychlém startu se seznámíte se základními obecnými koncepty, jak vytvořit první pracovní postup pomocí [Azure Logic Apps](logic-apps-overview.md), jako je vytvoření prázdné aplikace logiky, přidání triggeru a akce a testování aplikace logiky. V tomto rychlém startu vytvoříte aplikaci logiky, která pravidelně kontroluje informační kanál RSS webu pro nové položky. Pokud se najdou nové položky, aplikace logiky za každou z nich odešle e-mail. Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Koncepční obrázek znázorňující ukázkovou pracovní postup aplikace logiky vysoké úrovně.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Pro tento scénář potřebujete předplatné Azure, nebo si [Zaregistrujte bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), e-mailový účet ze služby, kterou podporuje Azure Logic Apps, jako je například Office 365 Outlook, Outlook.com nebo Gmail. Další podporované e-mailové služby najdete [v seznamu konektorů](/connectors/). V tomto příkladu aplikace logiky používá pracovní nebo školní účet. Pokud používáte jinou e-mailovou službu, je celkový obecný postup stejný, ale vaše uživatelské rozhraní se může mírně lišit.

> [!IMPORTANT]
> Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. Pokud máte účet příjemce Gmail, můžete tento konektor použít jenom pro konkrétní služby schválené v Google, nebo můžete [vytvořit klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do vyhledávacího pole Azure Portal zadejte `logic apps` a vyberte **Logic Apps**.

   ![Snímek obrazovky, který zobrazuje Azure Portal vyhledávací pole s "Logic Apps" jako hledaný termín a "Logic Apps" jako vybraný výsledek hledání.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na stránce **Logic Apps** vyberte **Přidat**.

   ![Snímek obrazovky se seznamem Logic Apps a vybraným tlačítkem Přidat](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. V podokně **Aplikace logiky** zadejte podrobnosti o vaší aplikaci logiky, jak je znázorněno níže.

   ![Snímek obrazovky s podoknem vytváření aplikací logiky s podrobnostmi pro novou aplikaci logiky](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*Logic-App-Name*> | Název vaší aplikace logiky, který musí být jedinečný napříč oblastmi a může obsahovat jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ), kulaté závorky ( `(` , `)` ) a tečky ( `.` ). Tento příklad používá "moji-First-Logic-App". |
   | **Předplatné** | <*Azure – předplatné – název*> | Název vašeho předplatného Azure |
   | **Skupina prostředků** | <*Azure-Resource-Group-Name*> | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md), který musí být v různých oblastech jedinečný a slouží k uspořádání souvisejících prostředků. V tomto příkladu se používá "My-First-LA-RG". |
   | **Umístění** | <*Oblast Azure*> | Oblast, kam se mají ukládat informace o aplikaci logiky V tomto příkladu se používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Až budete připraveni, vyberte **zkontrolovat + vytvořit**. Potvrďte podrobnosti, které jste zadali, a vyberte **vytvořit**.

1. Až Azure úspěšně nasadí vaši aplikaci, vyberte **Přejít k prostředku**.

   ![Snímek obrazovky zobrazující stránku nasazení prostředků a vybrané tlačítko pro možnost přejít k prostředku](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Nebo můžete vyhledat a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a běžně používanými triggery. V oblasti **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Snímek obrazovky znázorňující galerii šablon návrháře Logic Apps a vybranou šablonu "prázdná aplikace logiky".](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Dále přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když se v informačním kanálu RSS objeví nová položka. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Azure Logic Apps vytvoří instanci aplikace logiky, která spustí a spustí váš pracovní postup.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Přidat aktivační událost RSS

1. V **Návrháři aplikace logiky**pod vyhledávacím polem vyberte **vše**.

1. Chcete-li najít konektor RSS, zadejte do vyhledávacího pole `rss` . V seznamu triggery vyberte aktivační událost RSS, **když se publikuje položka informačního kanálu**.

   ![Snímek obrazovky znázorňující Logic Apps designeru pomocí "RSS" ve vyhledávacím poli a vybrané aktivační události "při publikování položky informačního kanálu".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Zadejte informace pro aktivační událost, jak je popsáno v tomto kroku:

   ![Snímek obrazovky znázorňující Logic Apps návrháře s nastavením triggeru RSS, včetně adresy URL RSS, frekvence a intervalu.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Adresa URL informačního kanálu RSS** | <*RSS – informační kanál – adresa URL*> | Odkaz na informační kanál RSS, který chcete monitorovat. V tomto příkladu se používá informační kanál RSS deníku na zdi `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` , ale pokud chcete, můžete použít vlastní adresu URL informačního kanálu RSS. |
   | **Interval** | 1 | Počet intervalů, po které se má čekat mezi kontrolami |
   | **Frekvence** | Minuta | Jednota času pro každý interval mezi kontrolami |
   ||||

   Interval a frekvence společně definují plán pro trigger vaší aplikace logiky. Tato aplikace logiky kontroluje kanál každou minutu.

1. Chcete-li nyní sbalit podrobnosti triggeru, klikněte do záhlaví triggeru.

   ![Snímek obrazovky znázorňující Logic Apps návrháře se sbaleným obrazcem aplikace logiky](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď v provozu, ale kromě kontroly informačního kanálu nic nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="add-the-send-email-action"></a>Přidat akci "Odeslat e-mail"

Teď přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts) , která odešle e-mail, když se v informačním kanálu RSS objeví nová položka.

1. Pod položkou aktivační událost **při publikování položky informačního kanálu** vyberte **Nový krok**.

   ![Snímek obrazovky znázorňující návrháře Logic Apps s novým krokem](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. V části **Zvolte akci** a vyhledávací pole vyberte **vše**.

1. Do vyhledávacího pole zadejte, aby `send an email` bylo možné najít konektory, které tuto akci nabízejí. Pokud chcete filtrovat seznam akcí pro konkrétní aplikaci nebo službu, můžete tuto aplikaci nebo službu vybrat jako první.

   Pokud například používáte pracovní nebo školní účet Microsoft a chcete použít Office 365 Outlook, vyberte **office 365 Outlook**. Nebo pokud používáte osobní účet Microsoft, můžete vybrat Outlook.com. Tento příklad pokračuje v Office 365 Outlooku:

   ![Snímek obrazovky znázorňující návrháře Logic Apps a vybraný konektor Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Teď můžete snadněji najít a vybrat akci, kterou chcete použít, například `send an email` :

   ![Snímek obrazovky znázorňující návrháře Logic Apps a seznam s filtrovanými akcemi](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Pokud váš vybraný e-mailový konektor vás vyzve k ověření vaší identity, dokončete tento krok, abyste vytvořili propojení mezi vaší aplikací logiky a vaší e-mailovou službou.

   > [!NOTE]
   > V tomto konkrétním příkladu ručně ověříte svou identitu. Konektory, které vyžadují ověřování, se však liší v typech ověřování, které podporují. Máte také možnost nastavit způsob, jakým chcete zpracovávat ověřování. Například při použití šablon Azure Resource Manager pro nasazení můžete parametrizovat a zdokonalit zabezpečení u vstupů, které chcete často nebo snadno měnit, například informace o připojení. Další informace najdete v těchto tématech:
   >
   > * [Parametry šablony pro nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autorizovat připojení OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Ověření přístupu ke spravovaným identitám](../logic-apps/create-managed-service-identity.md)
   > * [Ověřování připojení pro nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. V akci **Odeslat e-mail** zadejte informace, které chcete zahrnout do e-mailu.

   1. Do pole **Komu** zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít svou e-mailovou adresu.

      Prozatím ignorujte seznam **Přidat dynamický obsah**, který se zobrazí. Po kliknutí do některých textových polí se zobrazí tento seznam a zobrazí všechny dostupné výstupy z předchozího kroku, které můžete použít jako vstupy pro aktuální akci.

   1. Do pole **Předmět** zadejte tento text s mezerou na konci: `New RSS item: `

      ![Snímek obrazovky znázorňující Logic Apps návrháře pomocí akce "Odeslat e-mail" a kurzor v poli vlastnosti Subject.](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. V seznamu **Přidat dynamický obsah** vyberte **Název informačního kanálu**, který je výstupem z triggeru "při publikování položky informačního kanálu", který umožňuje použít název položky RSS k dispozici.

      ![Snímek obrazovky znázorňující Logic Apps návrháře pomocí akce "Odeslat e-mail" a kurzoru v poli vlastnosti Subject s otevřeným dynamickým obsahem a vybraným výstupem "název informačního kanálu".](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Pokud se v seznamu dynamického obsahu nezobrazí žádné výstupy z triggeru "když je položka informačního kanálu publikovaná", klikněte vedle záhlaví akce na **Zobrazit další**.
      > 
      > ![Snímek obrazovky zobrazující Logic Apps návrháře s otevřeným seznamem dynamického obsahu a možnost Zobrazit další pro aktivační událost je vybraná.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Jakmile budete hotovi, předmět e-mailu bude vypadat jako v tomto příkladu:

      ![Snímek obrazovky znázorňující Logic Apps návrháře pomocí akce Odeslat e-mail a příklad předmětu e-mailu s zahrnutou vlastností "titul informačního kanálu".](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Pokud se v návrháři zobrazí smyčka For each, pak jste vybrali token obsahující pole, například token **categories-Item**. Pro tyto typy tokenů návrhář automaticky přidá tuto smyčku okolo akce, která obsahuje referenci na příslušný token. Aplikace logiky tak provede stejnou akci pro každou položku pole. Pokud chcete smyčku odebrat, vyberte na záhlaví smyčky **tři tečky** (**...**) a pak vyberte **Odstranit**.

   1. Do pole **Text** zadejte tento text a pro text e-mailu vyberte tyto tokeny. Pokud chcete do textového pole přidat prázdné řádky, stiskněte Shift + Enter.

      ![Snímek obrazovky znázorňující Logic Apps návrháře pomocí akce "Odeslat e-mail" a vybrané vlastnosti v poli "tělo".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

      | Vlastnost | Popis |
      |----------|-------------|
      | **Název informačního kanálu** | Název položky |
      | **Datum publikování informačního kanálu** | Datum a čas publikování položky |
      | **Odkaz na primární informační kanál** | Adresa URL položky |
      |||

1. Uložte aplikaci logiky.

Dále svou aplikaci logiky otestujte.

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře vyberte **Spustit**. Nebo počkejte, až aplikace logiky zkontroluje informační kanál RSS podle zadaného plánu (každou minutu).

![Snímek obrazovky znázorňující návrháře Logic Apps s vybraným tlačítkem spustit na panelu nástrojů návrháře](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Pokud informační kanál RSS obsahuje nové položky, aplikace logiky za každou novou položku odešle e-mail. Jinak vaše aplikace logiky s další kontrolou počká na další interval. Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanými e-maily.

Například tady je ukázka e-mailu, který tato aplikace logiky odešle.

![Snímek obrazovky s ukázkovým e-mailem přijatým při zobrazení nové položky informačního kanálu RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technicky, když aktivační událost zkontroluje informační kanál RSS a najde nové položky, Trigger se aktivuje a modul Azure Logic Apps vytvoří instanci pracovního postupu aplikace logiky, která spouští akce v pracovním postupu. Pokud trigger nenajde nové položky, neaktivuje se a přeskočí vytvoření instance pracovního postupu.

Gratulujeme, právě jste úspěšně vytvořili a spustili svou první aplikaci logiky pomocí Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tuto ukázku už nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. Do pole Azure Search zadejte `resource groups` a pak vyberte **skupiny prostředků**.

   ![Snímek obrazovky zobrazující Azure Portal vyhledávací pole s hledaným termínem "skupiny prostředků".](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Vyhledejte a vyberte skupinu prostředků vaší aplikace logiky. V podokně **Přehled** vyberte **Odstranit skupinu prostředků**.

   ![Snímek obrazovky zobrazující Azure Portal s vybranou skupinou prostředků a tlačítkem pro "odstranění skupiny prostředků".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Jakmile se zobrazí podokno potvrzení, zadejte název skupiny prostředků a vyberte **Odstranit**.

   ![Snímek obrazovky zobrazující Azure Portal s podoknem potvrzení a zadaným názvem skupiny prostředků, který se má odstranit](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili svou první aplikaci logiky, která podle zadaného plánu (každou minutu) kontroluje aktualizace informačního kanálu RSS a při nalezení aktualizace provede akci. Další informace najdete v tomto kurzu, ve kterém se vytváří pokročilejší pracovní postupy založené na plánu:

> [!div class="nextstepaction"]
> [Kontrola provozu s využitím aplikace logiky založené na plánu](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

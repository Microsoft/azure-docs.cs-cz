---
title: Rychlý Start – vytvoření prvního Logic Apps pracovního postupu – Azure Portal
description: Pomocí tohoto průvodce rychlým startem si sestavíte první automatizovaný pracovní postup Logic Apps v Azure Portal. Seznamte se se základy řešení integrace systému a podnikových aplikací (EAI) v Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/30/2020
ms.openlocfilehash: d90b9e38158d951990fffc21a43317c688da12c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052038"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>Rychlý Start: vytvoření prvního pracovního postupu Logic Apps – Azure Portal

V tomto rychlém startu se dozvíte, jak pomocí [Azure Portal](https://portal.azure.com)vytvořit první pracovní postup v [Azure Logic Apps](logic-apps-overview.md) . Tato úvodní příručka také vysvětluje základní koncepty služby Logic Apps, včetně postupu vytvoření nové aplikace logiky, přidání triggeru a akce do aplikace logiky a otestování aplikace logiky. Pomocí tohoto rychlého startu sestavíte ukázkovou aplikaci logiky, která pravidelně kontroluje informační kanál RSS a pošle e-mailové oznámení pro nové položky. Následující snímek obrazovky ukazuje pracovní postup na nejvyšší úrovni této ukázkové aplikace logiky:

![Snímek obrazovky návrháře Logic Apps, ve kterém se zobrazuje ukázková aplikace logiky, kde Trigger je informační kanál RSS a akce posílá e-mail.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

Pokud se chcete dozvědět, jak vytvořit a spravovat svou první aplikaci logiky prostřednictvím jiných rozhraní a aplikací, přečtěte si tyto další Logic Apps rychlý Start: 

* [Vytváření a Správa aplikací logiky pomocí rozhraní Azure Command-Line (Azure CLI)](quickstart-logic-apps-azure-cli.md)
* [Vytváření a Správa aplikací logiky v Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
* [Vytváření a Správa aplikací logiky v aplikaci Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>Předpoklady

* Účet a předplatné Azure. Pokud ho ještě nemáte, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* E-mailový účet ze služby, kterou podporuje Logic Apps (například Office 365 Outlook nebo Outlook.com). Další podporované poskytovatele e-mailu najdete [v seznamu konektorů](/connectors/).

    > [!IMPORTANT]
    > Pokud používáte [konektor Gmail](/connectors/gmail/), mějte na paměti, že tento konektor může používat jenom účet G Suite bez omezení Logic Apps. Pokud máte účet služby Gmail pro příjemce, můžete tento konektor použít jenom s konkrétními službami, které jsou schválené pro Google, pokud [nevytvoříte klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Pokud vaše aplikace logiky potřebuje komunikovat přes bránu firewall, která omezuje provoz na konkrétní IP adresy, musí brána firewall povolit přístup *pro* [příchozí](logic-apps-limits-and-config.md#inbound) i [odchozí](logic-apps-limits-and-config.md#outbound) IP adresy, které používá služba Logic Apps nebo modul runtime v oblasti Azure, ve které vaše aplikace logiky existuje. Pokud vaše aplikace logiky používá i [spravované konektory](../connectors/apis-list.md#managed-api-connectors), jako je například konektor Office 365 Outlook nebo konektor SQL, nebo používá [vlastní konektory](/connectors/custom-connectors/), musí brána firewall také umožňovat přístup pro *všechny* [odchozí IP adresy spravovaného konektoru](logic-apps-limits-and-config.md#outbound) v oblasti Azure vaší aplikace logiky.

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Do vyhledávacího pole Azure Portal zadejte `logic apps` a vyberte **Logic Apps**.

   ![Snímek obrazovky Azure Portal, který zobrazuje vyhledávací pole s "Logic Apps" jako hledaný termín a "Logic Apps" jako vybraný výsledek hledání.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na stránce **Logic Apps** vyberte **Přidat**.

   ![Obrazovka stránky Logic Apps služby v Azure Portal, která zobrazuje seznam Logic Apps a vybrané tlačítko Přidat.](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. V podokně **Aplikace logiky** zadejte základní podrobnosti a nastavení vaší aplikace logiky. Vytvořte novou [skupinu prostředků](../azure-resource-manager/management/overview.md#terminology) pro účely této ukázkové aplikace logiky.

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*Logic-App-Name*> | Název vaší aplikace logiky, který musí být jedinečný napříč oblastmi. Název může obsahovat jenom písmena, číslice, spojovníky ( `-` ), podtržítka ( `_` ), kulaté závorky ( `(` , `)` ) a tečky ( `.` ). Tento příklad používá "moji-First-Logic-App". |
   | **Předplatné** | <*Azure – předplatné – název*> | Název vašeho předplatného Azure. |
   | **Skupina prostředků** | <*Azure-Resource-Group-Name*> | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md#terminology) , ve které vytváříte aplikaci logiky Název skupiny prostředků musí být v různých oblastech jedinečný. V tomto příkladu se používá "My-First-LA-RG". |
   | **Umístění** | <*Oblast Azure*> | Oblast Azure, kam se mají ukládat informace o aplikaci logiky V tomto příkladu se používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Nastavení pro protokolování diagnostiky, které je ve výchozím nastavení **vypnuté** . V tomto příkladu ponechejte nastavení **vypnuto** . |
   ||||

   ![Obrazovka stránky pro vytváření Logic Apps, která zobrazuje podokno s podrobnostmi pro novou aplikaci logiky](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. Až budete připraveni, vyberte **zkontrolovat + vytvořit**. Potvrďte podrobnosti, které jste zadali, a vyberte **vytvořit**.

1. Až Azure úspěšně nasadí vaši aplikaci, vyberte **Přejít k prostředku**. Nebo můžete vyhledat a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   ![Snímek obrazovky se stránkou nasazení prostředků, která zobrazuje vybrané tlačítko "přejít k prostředku".](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a běžně používanými triggery. V oblasti **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Snímek obrazovky návrháře Logic Apps, zobrazení galerie šablon a vybrané šablony, "prázdná aplikace logiky".](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Dále [přidejte Trigger do aplikace logiky](#add-rss-trigger).

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Přidat aktivační událost RSS

Každá aplikace logiky musí začínat [triggerem](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když Trigger najde nové položky, aktivuje se a modul Logic Apps vytvoří instanci aplikace logiky, která spustí a spustí váš pracovní postup. Pokud aktivační událost nenalezne nové položky, aktivační událost se neaktivuje, nevytvoří instanci nebo v této kontrole spustí pracovní postup.

V tomto příkladu pro rychlý Start: po [Vytvoření aplikace logiky](#create-your-logic-app)přidáte Trigger, který kontroluje nové položky v informačním kanálu RSS, a aktivuje se, když jsou k dispozici nové položky. Můžete také vytvářet aplikace logiky s různými typy triggerů, například v kurzu [vytváření pracovních postupů automatického schvalování](tutorial-process-mailing-list-subscriptions-workflow.md).

1. V **Návrháři aplikace logiky** pod vyhledávacím polem vyberte **vše**.

1. Chcete-li najít konektor RSS, zadejte do vyhledávacího pole `rss` . V seznamu **triggery** vyberte aktivační událost RSS, **když se publikuje položka informačního kanálu**.

   ![Snímek obrazovky znázorňující Logic Apps designeru pomocí "RSS" ve vyhledávacím poli a vybrané aktivační události RSS "při publikování položky informačního kanálu".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Zadejte adresu URL informačního kanálu RSS pro aktivační událost. Pak definujte plán triggeru nastavením intervalu a frekvence.

   | Vlastnost | Hodnota | Popis |
   | -------- | ----- | ----------- |
   | **Adresa URL informačního kanálu RSS** | <*RSS – informační kanál – adresa URL*> | Adresa URL informačního kanálu RSS, který chcete monitorovat. V tomto příkladu se používá informační kanál RSS deníku na zdi na adrese `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` . Pro účely tohoto příkladu ale můžete použít jakýkoli informační kanál RSS, který nevyžaduje autorizaci protokolem HTTP. Vyberte informační kanál RSS, který se často publikuje, takže můžete aplikaci logiky otestovat snadno později. |
   | **Interval** | 1 | Počet intervalů, které se mají čekat mezi kontrolami kanálu RSS. V tomto příkladu se používá 1 minutové intervaly. |
   | **Frekvence** | Minuta | Jednotka času pro každý interval mezi kontrolami kanálu RSS. V tomto příkladu se používá 1 minutové intervaly. |
   ||||

   ![Snímek obrazovky znázorňující Logic Apps návrháře s nastavením triggeru RSS, včetně adresy URL RSS, frekvence a intervalu.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. Kliknutím na záhlaví okna nyní můžete sbalit podrobnosti triggeru.

   ![Snímek obrazovky znázorňující Logic Apps návrháře se sbaleným obrazcem aplikace logiky](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Uložte aplikaci logiky tak, že na panelu nástrojů návrháře vyberete **Uložit** .

Vaše aplikace logiky je teď živá, ale neprovádí žádnou jinou než kontrolu informačního kanálu RSS. V dalším kroku [přidejte akci](#add-email-action) , která určuje, co se stane, když se Trigger aktivuje.

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>Přidat akci "Odeslat e-mail"

Po [Přidání triggeru pro aplikaci logiky](#add-rss-trigger)musíte přidat [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts) , která určí odpověď v případě, že aplikace logiky zkontroluje informační kanál RSS a zobrazí se nová položka. Můžete také vytvářet aplikace logiky s mnohem složitějšími akcemi, jako je například v kurzu [zpracování e-mailů pomocí Logic Apps, Azure functions a Azure Storage](./tutorial-process-email-attachments-workflow.md).

> [!NOTE]
> V tomto příkladu se jako e-mailová služba používá Office 365 Outlook. Pokud používáte jinou podporovanou e-mailovou službu ve vaší aplikaci logiky, uživatelské rozhraní může vypadat jinak. Základní koncepty pro připojení k jiné e-mailové službě zůstávají ale stejné.

1. Pod položkou aktivační událost **při publikování položky informačního kanálu** vyberte **Nový krok**.

   ![Snímek obrazovky návrháře Logic Apps zobrazující pracovní postup s vybraným tlačítkem a "nový krok".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. V části **Zvolte akci** a vyhledávací pole vyberte **vše**.

1. Do vyhledávacího pole zadejte, aby `send an email` bylo možné najít konektory, které tuto akci nabízejí. Pokud chcete filtrovat seznam akcí pro konkrétní aplikaci nebo službu, můžete tuto aplikaci nebo službu vybrat jako první.

   Pokud například používáte pracovní nebo školní účet Microsoft a chcete použít Office 365 Outlook, vyberte **office 365 Outlook**. Nebo pokud používáte osobní účet Microsoft, můžete vybrat Outlook.com. Tento příklad pokračuje v Office 365 Outlooku:

   ![Snímek obrazovky návrháře Logic Apps zobrazující krok akce s vybraným e-mailovým konektorem "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Teď můžete snadněji najít a vybrat akci, kterou chcete použít, například `send an email` :

   ![Snímek obrazovky návrháře Logic Apps zobrazující seznam filtrovaných akcí pro e-mailový konektor "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Pokud váš vybraný e-mailový konektor vás vyzve k ověření vaší identity, dokončete tento krok nyní. Aby tento příklad fungoval, musíte vytvořit připojení mezi vaší aplikací logiky a vaší e-mailovou službou. 

    > [!NOTE]
    > V tomto příkladu se zobrazuje ruční ověřování konektoru Office 365 Outlook. Jiné konektory však mohou podporovat různé typy ověřování.
    > Ověřování pro aplikace logiky můžete také zpracovávat různými způsoby v závislosti na vašem případu použití. Například pokud pro nasazení použijete Azure Resource Manager šablony, můžete parametrizovat, aby se zlepšilo zabezpečení vstupů, které se často mění, například v podrobnostech o připojení. Další informace najdete v těchto tématech:
   > * [Parametry šablony pro nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autorizovat připojení OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Ověření přístupu ke spravovaným identitám](../logic-apps/create-managed-service-identity.md)
   > * [Ověřování připojení pro nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. V akci **Odeslat e-mail** určete, jaké informace se mají zahrnout do e-mailových oznámení.

   1. Do pole **Komu** zadejte e-mailovou adresu příjemce. V tomto příkladu použijte svou e-mailovou adresu.

        > [!NOTE]
        > Seznam **Přidat dynamický obsah** se zobrazí po kliknutí dovnitř pole **do** a některých dalších vstupních polí v Návrháři Logic Apps. Tento příklad používá dynamický obsah v pozdějším kroku. Seznam **Přidat dynamický obsah** zobrazuje všechny dostupné výstupy z předchozího kroku, které můžete použít jako vstupy pro aktuální akci.

   1. Do pole **Předmět** zadejte předmět e-mailového oznámení. V tomto příkladu zadejte následující text s koncovým prázdným místem: `New RSS item: `

      ![Snímek obrazovky návrháře Logic Apps zobrazující akci "Odeslat e-mail" a kurzor do pole vlastnosti Subject.](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. V seznamu **Přidat dynamický obsah** vyberte **Název informačního kanálu**, který je výstupem triggeru, **když se publikuje položka informačního kanálu**. Vaše e-mailové oznámení pomocí tohoto výstupu Získá název položky RSS.

      ![Snímek obrazovky návrháře Logic Apps zobrazující akci "Odeslat e-mail" a kurzor v poli vlastnosti Subject s otevřeným dynamickým obsahem a vybraným výstupem "název informačního kanálu".](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Pokud v seznamu dynamického obsahu nezmizí žádné výstupy z aktivační události **při publikování položky informačního kanálu** , vyberte vedle záhlaví akce možnost **Zobrazit další**.
      > 
      > ![Snímek obrazovky návrháře Logic Apps zobrazující otevřený dynamický seznam obsahu a možnost Zobrazit další pro aktivační událost je vybraná.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Jakmile budete hotovi, předmět e-mailu bude vypadat jako v tomto příkladu:

      ![Snímek obrazovky návrháře Logic Apps, ve kterém se zobrazuje akce Odeslat e-mail a příklad předmětu e-mailu s zahrnutou vlastností "název informačního kanálu".](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Pokud se v Návrháři objeví smyčka "pro každou", pak jste vybrali token pro pole; Příklad: **kategorie-Item** token. Pro tyto typy tokenů návrhář automaticky přidá tuto smyčku okolo akce, která obsahuje referenci na příslušný token. Aplikace logiky tak provede stejnou akci pro každou položku pole. Pokud chcete smyčku odebrat, vyberte na záhlaví smyčky **tři tečky** (**...**) a pak vyberte **Odstranit**.

   1. Do pole **text** zadejte obsah pro text e-mailu. V tomto příkladu obsahuje obsah tři vlastnosti s popisným textem pro každý z nich: `Title:` , vlastnost **title informačního** kanálu `Date published:` , **informační kanál publikovaný ve** vlastnosti a `Link:` , vlastnost **odkazu na primární informační kanál** . Pokud chcete do textového pole přidat prázdné řádky, stiskněte Shift + Enter.

      | Vlastnost | Popis |
      |----------|-------------|
      | **Název informačního kanálu** | Název položky |
      | **Datum publikování informačního kanálu** | Datum a čas publikování položky |
      | **Odkaz na primární informační kanál** | Adresa URL položky |
      |||

      ![Snímek obrazovky návrháře Logic Apps, ve kterém se zobrazuje akce Odeslat e-mail a vybrané vlastnosti v poli "tělo".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. Uložte aplikaci logiky. V nabídce návrháře vyberte **Save (Uložit** ).

Potom [otestujte, jestli vaše aplikace logiky funguje](#test-logic-app).

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Po vytvoření ukázkové aplikace logiky potvrďte, že je váš pracovní postup správně nakonfigurovaný. Můžete počkat, až aplikace logiky zkontroluje váš informační kanál RSS podle zadaného plánu. Nebo můžete aplikaci logiky spustit ručně výběrem možnosti **Spustit** na panelu nástrojů návrháře Logic Apps, jak je znázorněno na následujícím snímku obrazovky. 

Pokud informační kanál RSS obsahuje nové položky, aplikace logiky za každou novou položku odešle e-mail. V opačném případě vaše aplikace logiky počká, až příště znovu zkontroluje informační kanál RSS. 

![Snímek obrazovky návrháře Logic Apps, na kterém je zobrazeno tlačítko spustit na panelu nástrojů návrháře](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Následující snímek obrazovky ukazuje ukázkové e-mailové oznámení z této ukázkové aplikace logiky. E-mail obsahuje podrobné informace o každé položce informačního kanálu RSS vybrané v návrháři a také popisný text přidaný pro každý z nich.

![Snímek obrazovky aplikace Outlook zobrazující ukázkovou e-mailovou zprávu přijatou při zobrazení nové položky informačního kanálu RSS s názvem položky, datum publikování a propojení.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Pokud nepřijímáte e-maily s oznámeními z aplikace logiky podle očekávání:

* Pro případ, že se zpráva nesprávně vyfiltroval, ověřte, jestli je ve složce Nevyžádaná pošta e-mailový účet.
* Ujistěte se, že kanál RSS, který používáte, publikoval položky od poslední naplánované nebo ruční kontroly.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení testování této ukázkové aplikace logiky vyčistěte aplikaci logiky a všechny související prostředky odstraněním skupiny prostředků, kterou jste pro tento příklad vytvořili.

> [!NOTE]
> Při [odstranění aplikace logiky](manage-logic-apps-with-azure-portal.md#delete-logic-apps)se nevytvoří žádná nová spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

1. Do pole Azure Search zadejte `resource groups` a pak vyberte **skupiny prostředků**.

   ![Snímek obrazovky zobrazující Azure Portal vyhledávací pole s hledaným termínem "skupiny prostředků".](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Vyhledejte a vyberte skupinu prostředků vaší aplikace logiky. V podokně **Přehled** vyberte **Odstranit skupinu prostředků**.

   ![Snímek obrazovky zobrazující Azure Portal s vybranou skupinou prostředků a tlačítkem pro "odstranění skupiny prostředků".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Jakmile se zobrazí podokno potvrzení, zadejte název skupiny prostředků a vyberte **Odstranit**.

   ![Snímek obrazovky zobrazující Azure Portal s podoknem potvrzení a zadaným názvem skupiny prostředků, který se má odstranit](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili svou první aplikaci logiky v Azure Portal ke kontrole informačního kanálu RSS podle plánu a odeslání e-mailového oznámení o každé nové položce informačního kanálu. 

Další informace o tom, jak vytvořit pokročilejší pracovní postupy na základě plánu v Logic Apps, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Kontrola provozu s využitím aplikace logiky založené na plánu](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
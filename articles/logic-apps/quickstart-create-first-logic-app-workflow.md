---
title: Rychlý Start – vytvoření a automatizace prvního pracovního postupu pomocí Azure Logic Apps
description: Vytvořte svou první aplikaci logiky, která automatizuje úlohy, procesy a pracovní postupy pomocí Azure Logic Apps. Vytvářejte aplikace Logic Apps pro systémovou integraci a řešení pro integraci podnikových aplikací (EAI) pro vaše systémy & cloudové služby.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.openlocfilehash: 2f39678e9fa0ed665863a94767240e2e37eace39
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906077"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Rychlý start: Vytvoření prvního automatizovaného pracovního postupu pomocí Azure Logic Apps-Azure Portal

V tomto rychlém startu se seznámíte se sestavením prvního automatizovaného pracovního postupu pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md). V tomto článku vytvoříte aplikaci logiky, která pravidelně kontrolujte nové položky v informačním kanálu RSS webu. Pokud se najdou nové položky, aplikace logiky za každou z nich odešle e-mail. Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Přehled – příklad aplikace logiky](./media/quickstart-create-first-logic-app-workflow/overview.png)

Abyste mohli postupovat podle tohoto rychlého startu, potřebujete e-mailový účet od poskytovatele podporovaného v Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](https://docs.microsoft.com/connectors/). Tato aplikace logiky používá účet Office 365 Outlook. Pokud použijete jiný e-mailový účet, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit.

Zároveň pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V hlavní nabídce Azure vyberte **vytvořit prostředek** > **Integration** > **Logic App**.

   ![Vytvoření aplikace logiky](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

1. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky, jak je vidět zde. Až budete hotovi, vyberte **vytvořit**.

   ![Zadání podrobností o aplikaci logiky](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Vlastnost | Value | Popis |
   |----------|-------|-------------|
   | **Název** | <*Logic-App-Name*> | Název aplikace logiky, který může obsahovat `-`jenom písmena, číslice nebo znaky `)`, `(` `_`,,, `.` . Tento příklad používá "moji-First-Logic-App". |
   | **Předplatné** | <*název_předplatného_Azure*> | Název vašeho předplatného Azure |
   | **Skupina prostředků** | <*Azure-resource-group-name*> | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) , která slouží k uspořádání souvisejících prostředků. V tomto příkladu se používá "My-First-LA-RG". |
   | **Location** | <*Oblast Azure*> | Oblast, kam se mají ukládat informace o aplikaci logiky V tomto příkladu se používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Až Azure nasadí vaši aplikaci, vyberte na panelu nástrojů Azure možnost **oznámení** > **Přejít na prostředek** pro vaši nasazenou aplikaci logiky.

   ![Přejít k prostředku](./media/quickstart-create-first-logic-app-workflow/go-to-logic-app.png)

   Nebo můžete vyhledat a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a běžně používanými triggery. V části **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Vybrat šablonu prázdná aplikace logiky](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Dále přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když se v informačním kanálu RSS objeví nová položka. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Kontrola informačního kanálu RSS pomocí triggeru

1. V návrháři aplikace logiky pod vyhledávacím polem vyberte **vše**.

1. Do vyhledávacího pole zadejte "RSS". V seznamu triggery vyberte tuto aktivační událost: **Při publikování položky informačního kanálu – RSS**

   ![Vyberte aktivační událost: RSS – při publikování položky informačního kanálu](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

1. Zadejte tyto informace pro aktivační událost, jak je znázorněno na obrázku a popisuje:

   ![Nastavení triggeru s informačním kanálem RSS, frekvencí a intervalem](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Vlastnost | Value | Popis |
   |----------|-------|-------------|
   | **Adresa URL informačního kanálu RSS** | ```http://feeds.reuters.com/reuters/topNews``` | Odkaz na informační kanál RSS, který chcete monitorovat |
   | **Interval** | 1 | Počet intervalů, po které se má čekat mezi kontrolami |
   | **Frekvence** | Minuta | Jednota času pro každý interval mezi kontrolami  |
   ||||

   Interval a frekvence společně definují plán pro trigger vaší aplikace logiky. Tato aplikace logiky kontroluje kanál každou minutu.

1. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte na záhlaví triggeru.

   ![Tvar sbalení pro skrytí podrobností](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď v provozu, ale kromě kontroly informačního kanálu nic nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="send-email-with-an-action"></a>Odeslání e-mailu pomocí akce

Nyní přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), která odešle e-mail, když se v informačním kanálu RSS objeví nová položka.

1. Pod položkou aktivační událost **při publikování položky informačního kanálu** vyberte **Nový krok**.

   ![Přidat akci](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

1. V části **Zvolte akci** a vyhledávací pole vyberte **vše**.

1. Do vyhledávacího pole zadejte "Odeslat e-mail". V seznamu akcí vyberte akci Odeslat e-mail pro požadovaného poskytovatele e-mailu.

   ![Vyberte tuto akci: "Office 365 Outlook – odeslat e-mail"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Pokud chcete v seznamu akcí vyfiltrovat konkrétní aplikaci nebo službu, můžete nejprve tuto aplikaci nebo službu vybrat:

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook.
   * Pro osobní účty Microsoft vyberte Outlook.com.

1. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se ke svému e-mailovému účtu a Logic Apps vytvoří připojení k vašemu e-mailovému účtu.

1. V akci **Odeslat e-mail** určete data, která má e-mail zahrnovat.

   1. Do pole **Komu** zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít svou vlastní e-mailovou adresu.

      Prozatím ignorujte seznam **Přidat dynamický obsah**, který se zobrazí. Tento seznam se zobrazí po kliknutí do některých textových polí a zobrazí všechny dostupné parametry z předchozího kroku, které můžete do svého pracovního postupu zahrnout jako vstupy.

   1. Do pole **Předmět** zadejte tento text s mezerou na konci: ```New RSS item:```

      ![Zadání předmětu e-mailu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. V seznamu **Přidat dynamický obsah** vyberte **Název informačního kanálu** a zahrňte název položky RSS.

      ![Seznam dynamického obsahu – Název informačního kanálu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Jakmile budete hotovi, předmět e-mailu bude vypadat jako v tomto příkladu:

      ![Přidaný název informačního kanálu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Pokud se v návrháři zobrazí smyčka For each, pak jste vybrali token obsahující pole, například token **categories-Item**. Pro tyto typy tokenů návrhář automaticky přidá tuto smyčku okolo akce, která obsahuje referenci na příslušný token. Aplikace logiky tak provede stejnou akci pro každou položku pole. Pokud chcete smyčku odebrat, vyberte na záhlaví smyčky **tři tečky** ( **...** ) a pak vyberte **Odstranit**.

   1. Do pole **Text** zadejte tento text a pro text e-mailu vyberte tyto tokeny. Pokud chcete do textového pole přidat prázdné řádky, stiskněte Shift + Enter.

      ![Přidání obsahu e-mailu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Vlastnost | Popis |
      |----------|-------------|
      | **Název informačního kanálu** | Název položky |
      | **Datum publikování informačního kanálu** | Datum a čas publikování položky |
      | **Odkaz na primární informační kanál** | Adresa URL položky |
      |||

1. Uložte svou aplikaci logiky.

Dále svou aplikaci logiky otestujte.

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře vyberte **Spustit**. Nebo počkejte, až aplikace logiky zkontroluje informační kanál RSS podle zadaného plánu (každou minutu). Pokud informační kanál RSS obsahuje nové položky, aplikace logiky za každou novou položku odešle e-mail. Jinak vaše aplikace logiky s další kontrolou počká na další interval. Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanými e-maily.

Například tady je ukázka e-mailu, který tato aplikace logiky odešle.

![E-mail odeslaný za novou položku informačního kanálu RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technicky vzato, když trigger zkontroluje informační kanál RSS a najde nové položky, aktivuje se a modul Logic Apps vytvoří instanci pracovního postupu vaší aplikace logiky, která provede akce v tomto pracovním postupu. Pokud trigger nenajde nové položky, neaktivuje se a přeskočí vytvoření instance pracovního postupu.

Gratulujeme, právě jste úspěšně vytvořili a spustili svou první aplikaci logiky pomocí Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tuto ukázku už nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. V hlavní nabídce Azure vyberte **skupiny prostředků**a pak vyberte skupinu prostředků vaší aplikace logiky. V podokně **Přehled** vyberte **Odstranit skupinu prostředků**.

   ![Skupiny prostředků > Přehled > Odstranit skupinu prostředků](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Jako potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.

   ![Potvrdit odstranění](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

## <a name="get-support"></a>Získat podporu

Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/home?forum=azurelogicapps).

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili svou první aplikaci logiky, která podle zadaného plánu (každou minutu) kontroluje aktualizace informačního kanálu RSS a při nalezení aktualizace provede akci. Další informace najdete v tomto kurzu, ve kterém se vytváří pokročilejší pracovní postupy založené na plánu:

> [!div class="nextstepaction"]
> [Kontrola provozu s využitím aplikace logiky založené na plánu](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

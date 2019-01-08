---
title: Rychlý start – vytvoření a automatizace prvního pracovního postupu – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvořte svou první aplikaci logiky, který automatizuje úlohy, procesy a pracovní postupy s Azure Logic Apps. Vytváření aplikací logiky pro řešení systémové integrace a integrace podnikových aplikací (EAI) určená vašim systémům a cloudovým službám
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: cc0f452cf9dc27354124437acc8d9a7c99fa80f4
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065184"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Rychlý start: Vytvoření prvního automatizovaného pracovního postupu pomocí Azure Logic Apps – Azure portal

V tomto rychlém startu se seznámíte se sestavením prvního automatizovaného pracovního postupu pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md). V tomto článku vytvoříte aplikaci logiky, která pravidelně kontrolujte nové položky v informačním kanálu RSS webu. Pokud se najdou nové položky, aplikace logiky za každou z nich odešle e-mail. Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Přehled – příklad aplikace logiky](./media/quickstart-create-first-logic-app-workflow/overview.png)

Abyste mohli postupovat podle tohoto rychlého startu, potřebujete e-mailový účet od poskytovatele podporovaného v Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](https://docs.microsoft.com/connectors/). Tato aplikace logiky používá účet Office 365 Outlook. Pokud použijete jiný e-mailový účet, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit. 

Zároveň pokud ještě nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky 

1. V hlavní nabídce Azure zvolte **Vytvořit prostředek** > **Integrace** > **Aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

3. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky, jak je vidět zde. Až budete hotovi, zvolte **vytvořit**.

   ![Zadání podrobností o aplikaci logiky](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------| 
   | **Název** | MyFirstLogicApp | Název vaší aplikace logiky | 
   | **Předplatné** | <*název_vašeho_předplatného_Azure*> | Název vašeho předplatného Azure | 
   | **Skupina prostředků** | My-First-LA-RG | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) sloužící k uspořádání souvisejících prostředků | 
   | **Umístění** | Západní USA | Oblast, do které se uloží informace o vaší aplikaci logiky | 
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. | 
   |||| 

3. Jakmile Azure nasadí vaši aplikaci, otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a běžně používanými triggery. V části **Šablony** zvolte **Prázdná aplikace logiky**.

   ![Výběr šablony prázdné aplikace logiky](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Dále přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když se v informačním kanálu RSS objeví nová položka. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Kontrola informačního kanálu RSS pomocí triggeru

1. V návrháři zadejte do vyhledávacího pole „rss“. Vyberte tento trigger: **RSS – při publikování položky informačního kanálu**

   ![Výběr triggeru: "RSS – při publikování položky informačního kanálu"](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

2. Zadejte pro trigger informace zobrazené a popsané níže: 

   ![Nastavení triggeru s informačním kanálem RSS, frekvencí a intervalem](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------| 
   | **Adresa URL informačního kanálu RSS** | ```http://feeds.reuters.com/reuters/topNews``` | Odkaz na informační kanál RSS, který chcete monitorovat | 
   | **Interval** | 1 | Počet intervalů, po které se má čekat mezi kontrolami | 
   | **Frekvence** | Minuta | Jednota času pro každý interval mezi kontrolami  | 
   |||| 

   Interval a frekvence společně definují plán pro trigger vaší aplikace logiky. 
   Tato aplikace logiky kontroluje kanál každou minutu.

3. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte na záhlaví triggeru.

   ![Tvar sbalení pro skrytí podrobností](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

4. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**. 

Vaše aplikace logiky je teď v provozu, ale kromě kontroly informačního kanálu nic nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="send-email-with-an-action"></a>Odeslání e-mailu pomocí akce

Nyní přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts), která odešle e-mail, když se v informačním kanálu RSS objeví nová položka. 

1. V části **při publikování položky informačního kanálu** aktivovat, vyberte **nový krok**.

   ![Přidání akce](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

2. V části **zvolte akci**, do vyhledávacího pole zadejte "Odeslat e-mail". Pod vyhledávacím polem vyberte **všechny**. V seznamu akcí vyberte akci Odeslat e-mail pro požadovaného poskytovatele e-mailu. 

   ![Vyberte tuto akci: "Office 365 Outlook – odeslat e-mail"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Pokud chcete v seznamu akcí vyfiltrovat konkrétní aplikaci nebo službu, můžete nejprve tuto aplikaci nebo službu vybrat:

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook. 
   * Pro osobní účty Microsoft vyberte Outlook.com.

3. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se ke svému e-mailovému účtu a Logic Apps vytvoří připojení k vašemu e-mailovému účtu.

4. V akci **Odeslat e-mail** určete data, která má e-mail zahrnovat. 

   1. Do pole **Komu** zadejte e-mailovou adresu příjemce. 
   Pro účely testování můžete použít svou vlastní e-mailovou adresu.

      Prozatím ignorujte seznam **Přidat dynamický obsah**, který se zobrazí. 
      Tento seznam se zobrazí po kliknutí do některých textových polí a zobrazí všechny dostupné parametry z předchozího kroku, které můžete do svého pracovního postupu zahrnout jako vstupy. 

   2. Do pole **Předmět** zadejte tento text s mezerou na konci: ```New RSS item: ```

      ![Zadání předmětu e-mailu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)
 
   3. V seznamu **Přidat dynamický obsah** vyberte **Název informačního kanálu** a zahrňte název položky RSS.

      ![Seznam dynamického obsahu – Název informačního kanálu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Jakmile budete hotovi, předmět e-mailu bude vypadat jako v tomto příkladu:

      ![Přidaný název informačního kanálu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Pokud se v návrháři zobrazí smyčka For each, pak jste vybrali token obsahující pole, například token **categories-Item**. 
      Pro tyto typy tokenů návrhář automaticky přidá tuto smyčku okolo akce, která obsahuje referenci na příslušný token. 
      Aplikace logiky tak provede stejnou akci pro každou položku pole. 
      Pokud chcete smyčku odebrat, zvolte **tři tečky** (**...**) v záhlaví smyčky a pak zvolte **Odstranit**.

   4. Do pole **Text** zadejte tento text a pro text e-mailu vyberte tyto tokeny. 
   Pokud chcete do textového pole přidat prázdné řádky, stiskněte Shift + Enter. 

      ![Přidání obsahu e-mailu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Vlastnost | Popis | 
      |----------|-------------| 
      | **Název informačního kanálu** | Název položky | 
      | **Datum publikování informačního kanálu** | Datum a čas publikování položky | 
      | **Odkaz na primární informační kanál** | Adresa URL položky | 
      ||| 
   
5. Uložte svou aplikaci logiky.

Dále svou aplikaci logiky otestujte.

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře zvolte **Spustit**. Nebo počkejte, až aplikace logiky zkontroluje informační kanál RSS podle zadaného plánu (každou minutu). Pokud informační kanál RSS obsahuje nové položky, aplikace logiky za každou novou položku odešle e-mail. Jinak vaše aplikace logiky s další kontrolou počká na další interval. 

Například tady je ukázka e-mailu, který tato aplikace logiky odešle. Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanými e-maily.

![E-mail odeslaný za novou položku informačního kanálu RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technicky vzato, když trigger zkontroluje informační kanál RSS a najde nové položky, aktivuje se a modul Logic Apps vytvoří instanci pracovního postupu vaší aplikace logiky, která provede akce v tomto pracovním postupu.
Pokud trigger nenajde nové položky, neaktivuje se a přeskočí vytvoření instance pracovního postupu.

Blahopřejeme, právě jste pomocí webu Azure Portal úspěšně sestavili a spustili svou první aplikaci logiky.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tuto ukázku už nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky. 

1. V hlavní nabídce Azure přejděte na **Skupiny prostředků** a vyberte skupinu prostředků vaší aplikace logiky. Na stránce **Přehled** zvolte **Odstranit skupinu prostředků**. 

   ![Skupiny prostředků > Přehled > Odstranit skupinu prostředků](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

2. Pro ověření zadejte název skupiny prostředků a zvolte **Odstranit**.

   ![Potvrzení odstranění](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili svou první aplikaci logiky, která podle zadaného plánu (každou minutu) kontroluje aktualizace informačního kanálu RSS a při nalezení aktualizace provede akci. Další informace najdete v tomto kurzu, ve kterém se vytváří pokročilejší pracovní postupy založené na plánu:

> [!div class="nextstepaction"]
> [Kontrola provozu s využitím aplikace logiky založené na plánu](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

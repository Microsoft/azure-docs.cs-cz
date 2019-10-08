---
title: Rychlý Start – vytvoření a automatizace prvního pracovního postupu pomocí Azure Logic Apps
description: Vytvořte svou první aplikaci logiky, která automatizuje úlohy, procesy a pracovní postupy pomocí Azure Logic Apps. Vytvářejte aplikace Logic Apps pro systémovou integraci a řešení pro integraci podnikových aplikací (EAI) pro vaše systémy & cloudové služby.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.openlocfilehash: c85b3e3ced661eb36d9cb1eb0ae443a5ab21a913
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029528"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Rychlý Start: vytvoření prvního automatizovaného pracovního postupu pomocí Azure Logic Apps-Azure Portal

Tento rychlý Start představuje způsob vytvoření prvního automatizovaného pracovního postupu pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md). V tomto článku vytvoříte aplikaci logiky, která pravidelně kontroluje informační kanál RSS webu pro nové položky. Pokud existují nové položky, aplikace logiky pošle e-mail pro každou položku. Jakmile budete hotovi, vaše aplikace logiky bude vypadat jako tento pracovní postup na vysoké úrovni:

![Ukázkový pracovní postup aplikace logiky vysoké úrovně](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

Abyste mohli postupovat podle tohoto rychlého startu, potřebujete e-mailový účet od poskytovatele, který podporuje Logic Apps, jako je například Office 365 Outlook, Outlook.com nebo Gmail. Pro jiné poskytovatele [Zkontrolujte seznam konektorů](https://docs.microsoft.com/connectors/). Tato aplikace logiky používá účet Office 365 Outlook. Pokud použijete jiný e-mailový účet, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit.

I když ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlaste se k Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svých přihlašovacích údajů k účtu Azure.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V hlavní nabídce Azure vyberte **vytvořit prostředek** > **integrace** **Aplikace logiky** > .

   ![Vytvoření aplikace logiky – Azure Portal](./media/quickstart-create-first-logic-app-workflow/create-new-logic-app.png)

1. V části **vytvořit aplikaci logiky**zadejte podrobnosti o vaší aplikaci logiky, jak je znázorněno zde. Až budete hotovi, vyberte **vytvořit**.

   ![Zadání podrobností pro novou aplikaci logiky](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Jméno** | <*Logic-App-name*> | Název aplikace logiky, který může obsahovat jenom písmena, číslice, spojovníky (`-`), podtržítka (`_`), kulaté závorky (`(`, `)`) a tečky (`.`). Tento příklad používá "moji-First-Logic-App". |
   | **Formě** | <*Azure-Subscription-name*> | Název vašeho předplatného Azure |
   | **Skupina prostředků** | <*Azure-Resource-Group-name*> | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) , která slouží k uspořádání souvisejících prostředků. V tomto příkladu se používá "My-First-LA-RG". |
   | **Poloha** | <*Azure-region*> | Oblast, kam se mají ukládat informace o aplikaci logiky V tomto příkladu se používá "Západní USA". |
   | **Log Analytics** | Off | Nechejte nastavení **vypnuto** pro protokolování diagnostiky. |
   ||||

1. Jakmile Azure nasadí vaši aplikaci, vyberte na panelu nástrojů Azure **oznámení** > **Přejít k prostředku** pro vaši nasazenou aplikaci logiky.

   ![Přejít na nově vytvořený prostředek aplikace logiky](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Nebo můžete vyhledat a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Otevře se Návrhář Logic Apps a zobrazí se stránka s úvodním videem a běžně používanými triggery. V části **šablony**vyberte **prázdná aplikace logiky**.

   ![Vybrat prázdnou šablonu pro aplikaci logiky](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Dále přidejte [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) , který se aktivuje při zobrazení nové položky informačního kanálu RSS. Každá aplikace logiky musí začínat triggerem, který se aktivuje, když dojde ke konkrétní události nebo když je splněná konkrétní podmínka. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky, která spustí a spustí váš pracovní postup.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Kontrolovat informační kanál RSS pomocí triggeru

1. V návrháři aplikace logiky pod vyhledávacím polem vyberte **vše**.

1. Do vyhledávacího pole zadejte "RSS". V seznamu triggery vyberte tuto aktivační událost: **když je publikovaná položka informačního kanálu – RSS**

   ![Vyberte, kdy se má aktivovat položka informačního kanálu.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Zadejte tyto informace pro aktivační událost, jak je znázorněno na obrázku a popisuje:

   ![Nastavení triggeru s informačním kanálem RSS, frekvencí a intervalem](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Adresa URL informačního kanálu RSS** | `http://feeds.reuters.com/reuters/topNews` | Odkaz na informační kanál RSS, který chcete monitorovat |
   | **Doba** | první | Počet intervalů, které se mají čekat mezi kontrolami |
   | **Opakování** | Minuta | Jednotka času pro každý interval mezi kontrolami  |
   ||||

   Interval a frekvence společně definují plán pro Trigger vaší aplikace logiky. Tato aplikace logiky zkontroluje informační kanál každou minutu.

1. Chcete-li nyní skrýt podrobnosti triggeru, klikněte do záhlaví triggeru.

   ![Sbalením obrazce aplikace logiky skryjete podrobnosti.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď živá, ale neprovádí žádnou jinou než kontrolu informačního kanálu RSS. Přidejte tedy akci, která odpoví, když se Trigger aktivuje.

## <a name="send-email-with-an-action"></a>Odeslání e-mailu pomocí akce

Teď přidejte [akci](../logic-apps/logic-apps-overview.md#logic-app-concepts) , která odešle e-mail, když se v informačním kanálu RSS objeví nová položka.

1. Pod položkou aktivační událost **při publikování položky informačního kanálu** vyberte **Nový krok**.

   ![V části Trigger vyberte nový krok.](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. V části **Zvolte akci** a vyhledávací pole vyberte **vše**.

1. Do vyhledávacího pole zadejte "Odeslat e-mail". V seznamu akce vyberte pro poskytovatele e-mailu akci "Odeslat e-mail".

   ![Výběr akce Odeslat e-mail pro Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Pokud chcete filtrovat seznam akcí pro konkrétní aplikaci nebo službu, můžete tuto aplikaci nebo službu vybrat jako první:

   * V případě pracovních nebo školních účtů Azure vyberte Office 365 Outlook.
   * Pro osobní účty Microsoft vyberte Outlook.com.

1. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se k e-mailovému účtu, aby Logic Apps vytvořila připojení k vašemu e-mailovému účtu.

1. V akci **Odeslat e-mail** zadejte data, která chcete zahrnout do e-mailu.

   1. Do pole **Komu** zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít vlastní e-mailovou adresu.

      Prozatím ignorujte seznam **Přidat dynamický obsah** , který se zobrazí. Po kliknutí do některých textových polí se zobrazí tento seznam a zobrazí všechny dostupné parametry z předchozího kroku, které můžete do pracovního postupu zahrnout jako vstupy.

   1. Do pole **Předmět** zadejte tento text s koncovým prázdným místem: ```New RSS item:```

      ![Do vlastnosti subject (předmět) zadejte předmět e-mailu.](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. V seznamu **Přidat dynamický obsah** vyberte **Název informačního kanálu** , aby zahrnoval název položky RSS.

      ![V seznamu dynamický obsah vyberte vlastnost název informačního kanálu.](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Až budete hotovi, bude předmět e-mailu vypadat jako v tomto příkladu:

      ![Příklad předmětu dokončeného e-mailu pro přidání nadpisu informačního kanálu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Pokud se v Návrháři objeví smyčka "pro každou", pak jste vybrali token pro pole, například token **Items** . Pro tyto typy tokenů návrhář automaticky přidá tuto smyčku kolem akce, která na tento token odkazuje. Aplikace logiky tak provede stejnou akci pro každou položku pole. Pokud chcete smyčku odebrat, vyberte na záhlaví smyčky **tři tečky** ( **...** ) a pak vyberte **Odstranit**.

   1. **Do pole text** zadejte tento text a vyberte tyto tokeny pro tělo e-mailu. Pokud chcete do textového pole přidat prázdné řádky, stiskněte SHIFT + ENTER.

      ![Vyberte vlastnosti pro obsah e-mailu.](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Vlastnost | Popis |
      |----------|-------------|
      | **Název informačního kanálu** | Název položky |
      | **Datum publikování informačního kanálu** | Datum a čas publikování položky |
      | **Odkaz na primární informační kanál** | Adresa URL položky |
      |||

1. Uložte aplikaci logiky.

V dalším kroku otestujte aplikaci logiky.

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře vyberte **Spustit**. Nebo počkejte, než aplikace logiky zkontroluje informační kanál RSS podle zadaného plánu (každou minutu). Pokud informační kanál RSS obsahuje nové položky, aplikace logiky pošle e-mail pro každou novou položku. V opačném případě vaše aplikace logiky počká do dalšího intervalu před opětovnou kontrolou. Pokud neobdržíte žádné e-maily, podívejte se do složky Nevyžádaná pošta.

Tady je příklad e-mailu, který tato aplikace logiky posílá.

![Ukázka e-mailu odeslaného při zobrazení nové položky informačního kanálu RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technicky, když aktivační událost zkontroluje informační kanál RSS a najde nové položky, Trigger se aktivuje a modul Logic Apps vytvoří instanci pracovního postupu aplikace logiky, která spouští akce v pracovním postupu. Pokud aktivační událost nenalezne nové položky, aktivační událost se neaktivuje a "přeskočí" vytváření instancí pracovního postupu.

Gratulujeme, právě jste úspěšně vytvořili a spustili svou první aplikaci logiky pomocí Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už tuto ukázku nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. V hlavní nabídce Azure vyberte **skupiny prostředků**a pak vyberte skupinu prostředků vaší aplikace logiky. V podokně **Přehled** vyberte **Odstranit skupinu prostředků**.

   ![Najít, vybrat a odstranit skupinu prostředků](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Jako potvrzení zadejte název skupiny prostředků a vyberte **Odstranit**.

   ![Odstranění potvrďte tak, že vyberete odstranit.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Při odstranění aplikace logiky se nevytvoří žádná nová spuštění. Všechny probíhající a probíhající běhy se zruší. Pokud máte tisíce spuštění, může dokončení zrušení trvat poměrně dlouho.

## <a name="get-support"></a>Získat podporu

Otázky najdete na [fóru Azure Logic Apps](https://social.msdn.microsoft.com/Forums/home?forum=azurelogicapps).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili svou první aplikaci logiky, která kontroluje aktualizace informačního kanálu RSS podle zadaného plánu (každou minutu) a provede akci (pošle e-mail), když existují aktualizace. Pokud se chcete dozvědět víc, pokračujte v tomto kurzu, který vytváří pokročilejší pracovní postupy na základě plánu:

> [!div class="nextstepaction"]
> [Ověřte provoz pomocí naplánované aplikace logiky na základě plánu.](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

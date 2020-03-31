---
title: Vytvoření prvního automatizovaného pracovního postupu
description: Úvodní příručka – vytvoření prvního automatizovaného pracovního postupu pomocí řešení Azure Logic Apps pro systémovou integraci a integraci podnikových aplikací (EAI)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: c2a26f2f40e0312fbfa0962e69ddfd0cfdabad5f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191281"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Úvodní příručka: Vytvoření prvního pracovního postupu pomocí Azure Logic Apps – portál Azure

Tento rychlý start představuje základní obecné koncepty, které stojí za tím, jak vytvořit svůj první pracovní postup pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md), jako je například vytvoření prázdné aplikace logiky, přidání aktivační události a akce a testování aplikace logiky. V tomto rychlém startu vytvoříte aplikaci logiky, která pravidelně kontroluje web rss kanál pro nové položky. Pokud se najdou nové položky, aplikace logiky za každou z nich odešle e-mail. Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Pracovní postup ukázkové aplikace logiky na vysoké úrovni](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

V tomto scénáři potřebujete e-mailový účet ze služby, která je podporovaná Azure Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Další podporované e-mailové služby [naleznete v seznamu konektorů zde](https://docs.microsoft.com/connectors/). V tomto příkladu aplikace logiky používá Office 365 Outlook. Pokud používáte jinou e-mailovou službu, celkové obecné kroky jsou stejné, ale vaše uživatelské rozhraní se může mírně lišit.

Zároveň pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. Na domovské stránce Azure ve vyhledávacím poli najděte a vyberte **Logic Apps**.

   ![Vyhledání a výběr "Logic Apps"](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na stránce **Logic Apps** vyberte **Přidat**.

   ![Přidání nové aplikace logiky](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. V podokně **aplikace logiky** zadejte podrobnosti o aplikaci logiky, jak je znázorněno níže. Až budete hotovi, vyberte **Vytvořit**.

   ![Zadejte podrobnosti o nové aplikaci logiky](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Název** | <*název aplikace logika*> | Název aplikace logiky, který může obsahovat pouze`-`písmena,`_`čísla, pomlčky`(`( `)`), podtržítka ( ), závorky ( , ) a tečky (`.`). Tento příklad používá "My-First-Logic-App". |
   | **Předplatné** | <*Název předplatného Azure*> | Název předplatného Azure |
   | **Skupina prostředků** | <*Název skupiny Azure-resource-group*> | Název [skupiny prostředků Azure,](../azure-resource-manager/management/overview.md) která slouží k uspořádání souvisejících prostředků. Tento příklad používá "My-First-LA-RG". |
   | **Umístění** | <*Oblast Azure*> | Oblast, kde chcete uložit informace o aplikaci logiky. Tento příklad používá "Západní USA". |
   | **Log Analytics** | Vypnuto | Pokud chcete zapnout protokolování diagnostiky, ponechte nastavení **Vypnuto**. |
   ||||

1. Po nasazení aplikace Azure na panelu nástrojů Azure vyberte **Oznámení** > **Přejít na prostředek** pro nasazenou aplikaci logiky.

   ![Přejít na nově vytvořený prostředek aplikace logiky](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Nebo můžete najít a vybrat aplikaci logiky zadáním názvu do vyhledávacího pole.

   Otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a běžně používanými triggery. V části **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Výběr prázdné šablony pro aplikaci logiky](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Dále přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když se v informačním kanálu RSS objeví nová položka. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se spustí aktivační událost, modul Azure Logic Apps vytvoří instanci aplikace logiky, která spustí a spustí váš pracovní postup.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Přidání aktivační události RSS

1. V **Návrháři aplikace logiky**vyberte pod vyhledávacím polem **možnost Vše**.

1. Do vyhledávacího pole `rss` zadejte a vyhledejte konektor RSS. Ze seznamu aktivačních událostí vyberte aktivační událost **Při publikování položky informačního kanálu.**

   ![Vyberte aktivační událost "Při publikování položky informačního kanálu"](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Zadejte tyto informace pro aktivační událost, jak je znázorněno a popsáno zde:

   ![Nastavení triggeru s informačním kanálem RSS, frekvencí a intervalem](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Adresa URL informačního kanálu RSS** | `http://feeds.reuters.com/reuters/topNews` | Odkaz na informační kanál RSS, který chcete monitorovat |
   | **Interval** | 1 | Počet intervalů, po které se má čekat mezi kontrolami |
   | **Frequency** | Minuta | Jednota času pro každý interval mezi kontrolami  |
   ||||

   Interval a frekvence společně definují plán pro trigger vaší aplikace logiky. Tato aplikace logiky kontroluje kanál každou minutu.

1. Chcete-li prozatím sbalit podrobnosti aktivační události, klepněte do záhlaví aktivační události.

   ![Sbalení obrazce aplikace logiky pro skrytí podrobností](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

Vaše aplikace logiky je teď v provozu, ale kromě kontroly informačního kanálu nic nedělá. Přidejte tedy akci, která bude reagovat na aktivaci triggeru.

## <a name="add-the-send-email-action"></a>Přidání akce Odeslat e-mail

Nyní přidejte [akci,](../logic-apps/logic-apps-overview.md#logic-app-concepts) která odešle e-mail, když se v informačním kanálu RSS zobrazí nová položka.

1. V části **Při publikování položky informačního kanálu** vyberte Nový **krok**.

   ![V části aktivační událost vyberte "Nový krok"](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. V části **Zvolte akci** a vyhledávací pole vyberte **Vše**.

1. Do vyhledávacího pole `send an email` zadejte, chcete-li najít konektory, které tuto akci nabízejí. V seznamu akcí vyberte akci Odeslat e-mail pro e-mailovou službu, kterou chcete použít. Tento příklad používá konektor Outlook office 365, který má akci **Odeslat e-mail.**

   ![Výběr akce Odeslat e-mail pro Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Pokud chcete v seznamu akcí vyfiltrovat konkrétní aplikaci nebo službu, můžete nejprve tuto aplikaci nebo službu vybrat:

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook.
   * Pro osobní účty Microsoft vyberte Outlook.com.

1. Pokud vybraný e-mailový konektor vyzve k ověření vaší identity, dokončete tento krok nyní a vytvořte připojení mezi aplikací logiky a e-mailovou službou.

   > [!NOTE]
   > V tomto konkrétním příkladu ručně ověřit identitu. Konektory, které vyžadují ověřování se však liší v typy ověřování, které podporují. Máte také možnosti nastavit způsob, jakým chcete zpracovat ověřování. Například při použití šablony Azure Resource Manager pro nasazení, můžete parametrizovat a zlepšit zabezpečení na vstupy, které chcete změnit často nebo snadno, jako jsou informace o připojení. Další informace najdete v těchto tématech:
   >
   > * [Parametry šablony pro nasazení](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autorizace připojení OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Ověření přístupu pomocí spravovaných identit](../logic-apps/create-managed-service-identity.md)
   > * [Ověření připojení pro nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. V akci **Odeslat e-mail** určete data, která má e-mail zahrnovat.

   1. Do pole **Komu** zadejte e-mailovou adresu příjemce. Pro účely testování můžete použít svou e-mailovou adresu.

      Prozatím ignorujte seznam **Přidat dynamický obsah**, který se zobrazí. Tento seznam se zobrazí po kliknutí do některých textových polí a zobrazí všechny dostupné parametry z předchozího kroku, které můžete do svého pracovního postupu zahrnout jako vstupy.

   1. Do pole **Předmět** zadejte tento text s mezerou na konci: `New RSS item: `

      ![Do vlastnosti Předmět zadejte předmět e-mailu.](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. V seznamu **Přidat dynamický obsah** vyberte **Název informačního kanálu** a zahrňte název položky RSS.

      ![V seznamu dynamického obsahu vyberte vlastnost "Název informačního kanálu".](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Jakmile budete hotovi, předmět e-mailu bude vypadat jako v tomto příkladu:

      ![Příklad dokončeného předmětu e-mailu pro přidanou název informačního kanálu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Pokud se v návrháři zobrazí smyčka For each, pak jste vybrali token obsahující pole, například token **categories-Item**. Pro tyto typy tokenů návrhář automaticky přidá tuto smyčku okolo akce, která obsahuje referenci na příslušný token. Aplikace logiky tak provede stejnou akci pro každou položku pole. Chcete-li smyčku odebrat, vyberte **tři tečky** (**...**) v záhlaví smyčky a pak vyberte **Odstranit**.

   1. Do pole **Text** zadejte tento text a pro text e-mailu vyberte tyto tokeny. Pokud chcete do textového pole přidat prázdné řádky, stiskněte Shift + Enter.

      ![Výběr vlastností pro obsah textu e-mailu](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Vlastnost | Popis |
      |----------|-------------|
      | **Název informačního kanálu** | Název položky |
      | **Datum publikování informačního kanálu** | Datum a čas publikování položky |
      | **Odkaz na primární informační kanál** | Adresa URL položky |
      |||

1. Uložte svou aplikaci logiky.

Dále svou aplikaci logiky otestujte.

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

Chcete-li aplikaci logiky spustit ručně, vyberte na panelu nástrojů návrháře **možnost Spustit**. Nebo počkejte, až aplikace logiky zkontroluje informační kanál RSS podle zadaného plánu (každou minutu). Pokud informační kanál RSS obsahuje nové položky, aplikace logiky za každou novou položku odešle e-mail. Jinak vaše aplikace logiky s další kontrolou počká na další interval. Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanými e-maily.

Například tady je ukázka e-mailu, který tato aplikace logiky odešle.

![Ukázkový e-mail odeslaný při zobrazení nové položky informačního kanálu RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technicky když aktivační událost zkontroluje informační kanál RSS a najde nové položky, aktivační událost se aktivuje a modul Azure Logic Apps vytvoří instanci pracovního postupu aplikace logiky, která spustí akce v pracovním postupu. Pokud trigger nenajde nové položky, neaktivuje se a přeskočí vytvoření instance pracovního postupu.

Gratulujeme, teď jste úspěšně vytvořili a spusťte svou první aplikaci logiky s portálem Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tuto ukázku už nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. V hlavní nabídce Azure vyberte **skupiny prostředků**a pak vyberte skupinu prostředků aplikace logiky. V podokně **Přehled** vyberte **Odstranit skupinu prostředků**.

   ![Najít, vybrat a odstranit skupinu prostředků](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Po zobrazení potvrzovacího podokna zadejte název skupiny prostředků a vyberte **Odstranit**.

   ![Chcete-li potvrdit odstranění, vyberte možnost Odstranit.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili svou první aplikaci logiky, která podle zadaného plánu (každou minutu) kontroluje aktualizace informačního kanálu RSS a při nalezení aktualizace provede akci. Další informace najdete v tomto kurzu, ve kterém se vytváří pokročilejší pracovní postupy založené na plánu:

> [!div class="nextstepaction"]
> [Kontrola provozu s využitím aplikace logiky založené na plánu](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

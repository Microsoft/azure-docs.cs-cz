---
title: Automatizace úloh s více službami Azure
description: Kurz – vytvoření automatizovaných pracovních postupů pro zpracování e-mailů pomocí Azure Logic Apps, Azure Storage a Azure Functions
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 02/27/2020
ms.openlocfilehash: 95cc13a79f39888a5be10e423bda4c7cd7c84cb3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054784"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>Kurz: automatizace úloh pro zpracování e-mailů pomocí Azure Logic Apps, Azure Functions a Azure Storage

Služba Azure Logic Apps pomáhá automatizovat pracovní postupy a integrovat data napříč službami Azure, službami Microsoftu a dalšími aplikacemi SaaS (software jako služba) a místními systémy. Tento kurz ukazuje postupy při vytváření [aplikace logiky](../logic-apps/logic-apps-overview.md), která bude zpracovávat příchozí e-maily včetně případných příloh. Tato aplikace logiky analyzuje obsah e-mailu, ukládá obsah do služby Azure Storage a odesílá oznámení pro kontrolu obsahu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení [úložiště Azure](../storage/common/storage-introduction.md) a Průzkumníka služby Storage tak, aby kontrolovaly uložené e-maily a přílohy
> * Vytvoření [funkce Azure](../azure-functions/functions-overview.md), která z e-mailů odebere kódování HTML (Tento kurz zahrnuje kód, který můžete pro tuto funkci využít.)
> * Vytvoření prázdné aplikace logiky
> * Přidání triggeru, který monitoruje přílohy v e-mailech
> * Přidání podmínky, která kontroluje, jestli má e-mail přílohy
> * Přidání akce, která volá funkci Azure, pokud má e-mail přílohy
> * Přidání akce, která vytvoří v úložišti objekty blob pro e-maily a přílohy
> * Přidání akce, která rozešle e-mailová oznámení

Jakmile budete hotovi, vaše aplikace logiky bude na základní úrovni vypadat jako tento pracovní postup:

![Hotová aplikace logiky na základní úrovni](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* E-mailový účet od poskytovatele podporovaného v Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](/connectors/).

  Tato aplikace logiky používá pracovní nebo školní účet. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

  > [!IMPORTANT]
  > Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. Pokud máte účet příjemce Gmail, můžete tento konektor použít jenom pro konkrétní služby schválené v Google, nebo můžete [vytvořit klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Zdarma stáhnout a nainstalovat [Průzkumníka služby Microsoft Azure Storage](https://storageexplorer.com/). Tento nástroj vám umožní zkontrolovat, jestli máte kontejner úložiště správně nastavený.

* Pokud vaše aplikace logiky potřebuje komunikovat přes bránu firewall, která omezuje provoz na konkrétní IP adresy, musí brána firewall povolit přístup *pro* [příchozí](logic-apps-limits-and-config.md#inbound) i [odchozí](logic-apps-limits-and-config.md#outbound) IP adresy, které používá služba Logic Apps nebo modul runtime v oblasti Azure, ve které vaše aplikace logiky existuje. Pokud vaše aplikace logiky používá i [spravované konektory](../connectors/apis-list.md#managed-api-connectors), jako je například konektor Office 365 Outlook nebo konektor SQL, nebo používá [vlastní konektory](/connectors/custom-connectors/), musí brána firewall také umožňovat přístup pro *všechny* [odchozí IP adresy spravovaného konektoru](logic-apps-limits-and-config.md#outbound) v oblasti Azure vaší aplikace logiky.

## <a name="set-up-storage-to-save-attachments"></a>Nastavení úložiště pro ukládání příloh

Příchozí e-maily a přílohy můžete ukládat jako objekty blob v [kontejneru úložiště Azure](../storage/common/storage-introduction.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Než budete moct vytvořit kontejner úložiště, vytvořte pomocí těchto nastavení na kartě **základy** v Azure Portal [účet úložiště](../storage/common/storage-account-create.md) :

   | Nastavení | Hodnota | Popis |
   |---------|-------|-------------|
   | **Předplatné** | <*Azure – předplatné – název*> | Název vašeho předplatného Azure |  
   | **Skupina prostředků** | <*Azure-Resource-Group*> | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md), který slouží k uspořádání a správě souvisejících prostředků V tomto příkladu se používá "LA-tutorial-RG". <p>**Poznámka:** Skupina prostředků existuje v konkrétní oblasti. Položky z tohoto kurzu nemusí být k dispozici ve všech oblastech, snažte se nicméně používat stejnou oblast, kdykoli je to možné. |
   | **Název účtu úložiště** | <*Azure-Storage-Account-Name*> | Název účtu úložiště, který musí mít 3-24 znaků a může obsahovat jenom malá písmena a číslice. V tomto příkladu se používá "attachmentstorageacct". |
   | **Umístění** | <*Oblast Azure*> | Oblast, kam se mají ukládat informace o vašem účtu úložiště V tomto příkladu se používá "Západní USA". |
   | **Výkon** | Standard | Toto nastavení specifikuje podporované datové typy a média pro ukládání dat. Další informace najdete v tématu [Typy účtů úložiště](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Druh účtu** | Obecné účely | [Typ účtu úložiště](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **Replikace** | Místně redundantní úložiště (LRS) | Toto nastavení určuje, jak se budou kopírovat, ukládat, spravovat a synchronizovat data. Viz [místně redundantní úložiště (LRS): redundance dat s nízkými náklady pro Azure Storage](../storage/common/storage-redundancy.md). |
   | **Úroveň přístupu (výchozí)** | Ponechte aktuální nastavení. |
   ||||

   Na kartě **Upřesnit** vyberte toto nastavení:

   | Nastavení | Hodnota | Popis |
   |---------|-------|-------------|
   | **Vyžádání bezpečného přenosu** | Zakázáno | Toto nastavení určuje zabezpečení vyžadované u požadavků směřujících od připojení. Další informace najdete v tématu [Vyžádání bezpečného přenosu](../storage/common/storage-require-secure-transfer.md). |
   ||||

   K vytvoření účtu úložiště můžete použít taky prostředí [Azure PowerShell](../storage/common/storage-account-create.md?tabs=powershell) nebo [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli).

1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

1. Až Azure nasadí váš účet úložiště, Najděte svůj účet úložiště a získejte přístupový klíč účtu úložiště:

   1. V nabídce účtu úložiště v části **Nastavení** vyberte **Přístupové klíče**.

   1. Zkopírujte název účtu úložiště a **klíč1** a uložte tyto hodnoty někam do trezoru.

      ![Zkopírování a uložení názvu a klíče účtu úložiště](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   K získání přístupového klíče účtu úložiště můžete použít taky prostředí [Azure PowerShell](/powershell/module/az.storage/get-azstorageaccountkey) nebo [Azure CLI](/cli/azure/storage/account/keys.md#az-storage-account-keys-list).

1. Vytvořte kontejner úložiště objektů blob pro přílohy e-mailů.

   1. V nabídce účtu úložiště vyberte **Přehled**. V podokně Přehled vyberte **kontejnery**.

      ![Přidání kontejneru úložiště objektů blob](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. Po otevření stránky **Kontejnery** vyberte na panelu nástrojů **Kontejner**.

   1. V části **Nový kontejner** zadejte `attachments` jako název kontejneru. V části **úroveň veřejného přístupu** vyberte **kontejner (anonymní přístup pro čtení pro kontejnery a objekty BLOB)**  >  **OK**.

      Jakmile budete hotovi, objeví se kontejner úložiště ve vašem účtu úložiště tady na portálu Azure Portal:

      ![Hotový kontejner úložiště](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Pokud chcete vytvořit kontejner úložiště, můžete použít taky [Azure PowerShell](/powershell/module/az.storage/new-azstoragecontainer) nebo [Azure CLI](/cli/azure/storage/container#az-storage-container-create).

Dalším krokem je propojení Průzkumníka služby Storage s vaším účtem.

## <a name="set-up-storage-explorer"></a>Nastavení Průzkumníka služby Storage

Teď se svým účtem úložiště propojte Průzkumníka služby Storage, abyste si mohli ověřit, že aplikace logiky do kontejneru úložiště správně ukládá přílohy jako objekty blob.

1. Spusťte Průzkumník služby Microsoft Azure Storage.

   Průzkumník služby Storage zobrazí výzvu k připojení k účtu úložiště.

1. V podokně **připojit k Azure Storage** vyberte **použít název a klíč účtu úložiště**  >  **Další**.

   ![Průzkumník služby Storage – připojení k účtu úložiště](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Pokud se nezobrazí žádné výzvy, na panelu nástrojů Průzkumník služby Storage vyberte **Přidat účet**.

1. Do **pole Zobrazovaný název** zadejte popisný název připojení. Do pole **Název účtu** zadejte název svého účtu úložiště. V části **klíč účtu** zadejte přístupový klíč, který jste předtím uložili, a vyberte **Další**.

1. Potvrďte informace o připojení a pak vyberte **připojit**.

   Průzkumník služby Storage vytvoří připojení a v okně Průzkumníka v části **místní & připojené**  >  **účty úložiště** se zobrazí váš účet úložiště.

1. Pokud chcete najít kontejner úložiště objektů blob, v části **účty úložiště** rozbalte svůj účet úložiště, který je **attachmentstorageacct** tady, a rozbalte **kontejnery objektů BLOB** , kde najdete kontejner **příloh** , například:

   ![Průzkumník služby Storage – vyhledání kontejneru úložiště](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

V dalším kroku vytvořte [funkci Azure](../azure-functions/functions-overview.md), která z příchozích e-mailů odebere kódování HTML.

## <a name="create-function-to-clean-html"></a>Vytvoření funkce k vyčištění kódu HTML

Teď pomocí připraveného fragmentu kódu a následujícího postupu vytvořte funkci Azure, která z každého příchozího e-mailu odebere kódování HTML. Obsah e-mailů se tak vyčistí a jeho zpracování bude jednodušší. Tuto funkci pak můžete volat z aplikace logiky.

1. Než funkci vytvoříte, nejdřív [vytvořte aplikaci funkcí](../azure-functions/functions-create-function-app-portal.md) s těmito nastaveními:

   | Nastavení | Hodnota | Popis |
   | ------- | ----- | ----------- |
   | **Název aplikace** | <*Function-App-Name*> | Název vaší aplikace Function App, který musí být globálně jedinečný v rámci Azure. Tento příklad už používá "CleanTextFunctionApp", takže zadejte jiný název, jako je například "MyCleanTextFunctionApp-<*your-name*>". |
   | **Předplatné** | <*Vaše předplatné – Azure-Subscription-Name*> | Stejné předplatné Azure, jaké jste používali dříve |
   | **Skupina prostředků** | LA-Tutorial-RG | Stejná skupina prostředků Azure, jakou jste používali dříve |
   | **OS** | <*váš operační systém*> | Vyberte operační systém, který podporuje váš oblíbený programovací jazyk funkcí. V tomto příkladu vyberte možnost **Windows**. |
   | **Plán hostování** | Plán Consumption | Toto nastavení určuje, jak se při běhu aplikace funkcí mají přidělovat a škálovat prostředky, například výpočetní výkon. Podívejte se na [porovnání plánů hostování](../azure-functions/functions-scale.md). |
   | **Umístění** | USA – západ | Stejná oblast, jakou jste používali dříve |
   | **Zásobník modulu runtime** | Upřednostňovaný jazyk | Vyberte modul runtime, který podporuje váš oblíbený programovací jazyk funkcí. Vyberte **.NET** pro funkce jazyka C# a F #. |
   | **Storage** | cleantextfunctionstorageacct | Vytvořte pro svou aplikaci funkcí účet úložiště. Použijte při tom jenom malá písmena a číslice. <p>**Poznámka:** Tento účet úložiště obsahuje vaše aplikace Function App a pro přílohy e-mailů se liší od dříve vytvořeného účtu úložiště. |
   | **Application Insights** | Zakázat | Zapne monitorování aplikací pomocí [Application Insights](../azure-monitor/app/app-insights-overview.md), ale pro tento kurz vyberte **Zakázat**  >  **použít**. |
   ||||

   Pokud se vaše aplikace Function po nasazení automaticky neotevře, v poli hledání [Azure Portal](https://portal.azure.com) vyhledejte a vyberte **Function App**. V části **Function App** vyberte svou aplikaci Function App.

   ![Výběr aplikace funkcí](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Azure vaši aplikaci funkcí automaticky otevře, jak je vidět tady:

   ![Vytvořená aplikace funkcí](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Pokud chcete vytvořit aplikaci Function App, můžete použít také [Azure CLI](../azure-functions/create-first-function-cli-csharp.md)nebo [šablony powershellu a správce prostředků](../azure-resource-manager/templates/deploy-powershell.md).

1. V seznamu **aplikace Function** App rozbalte aplikaci Function App, pokud ještě není rozbalená. V části aplikace Function App vyberte Functions ( **funkce**). Na panelu nástrojů funkcí zvolte **Nová funkce**.

   ![Vytvoření nové funkce](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. V části **Zvolte šablonu níže nebo se v rychlém** startu vyberte šablonu **triggeru http** .

   ![Vybrat šablonu triggeru HTTP](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure vytvoří funkci pomocí šablony specifické pro konkrétní jazyk pro funkci aktivovanou protokolem HTTP.

1. V okně **Nová funkce** v poli **Název** zadejte `RemoveHTMLFunction`. Možnost zachovat **úroveň autorizace** je nastavena na hodnotu **funkce** a vyberte **vytvořit**.

   ![Pojmenování funkce](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. Jakmile se otevře editor, nahraďte kód šablony tímto vzorkem kódu, který odebere kódování HTML a vrátí výsledky volající funkci:

   ```csharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. Jakmile budete mít hotovo, vyberte **Uložit**. Chcete-li otestovat funkci, klikněte na pravé straně editoru na ikonu šipky ( **<** ), vyberte možnost **test**.

   ![Otevření testovacího podokna](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. V podokně **test** v části **text požadavku** zadejte tento řádek a vyberte **Spustit**.

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![Otestování funkce](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   V okně **Výstup** se zobrazí výsledek funkce:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Když zkontrolujete, že funkce pracuje správně, vytvořte aplikaci logiky. Tento kurz vysvětluje postup vytvoření funkce, která z e-mailů odebere kódování HTML. Služba Logic Apps ale poskytuje také konektor pro převod **HTML na text**.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. Do vyhledávacího pole Azure nejvyšší úrovně zadejte `logic apps` a vyberte **Logic Apps**.

   ![Vyhledejte a vyberte "Logic Apps"](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. V podokně **Logic Apps** vyberte **Přidat**.

   ![Přidat novou aplikaci logiky](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. V podokně **Aplikace logiky** zadejte podrobnosti o vaší aplikaci logiky, jak je znázorněno zde. Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

   ![Zadání informací o aplikaci logiky](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Nastavení | Hodnota | Popis |
   | ------- | ----- | ----------- |
   | **Předplatné** | <*Vaše předplatné – Azure-Subscription-Name*> | Stejné předplatné Azure, jaké jste používali dříve |
   | **Skupina prostředků** | LA-Tutorial-RG | Stejná skupina prostředků Azure, jakou jste používali dříve |
   | **Název aplikace logiky** | LA-ProcessAttachment | Název vaší aplikace logiky |
   | **Vybrat umístění** | USA – západ | Stejná oblast, jakou jste používali dříve |
   | **Log Analytics** | Vypnuto | Pro tento kurz vyberte nastavení **vypnuto** . |
   ||||

1. Až Azure nasadí vaši aplikaci, vyberte na panelu nástrojů Azure ikonu oznámení a vyberte **Přejít k prostředku**.

   ![V seznamu oznámení Azure vyberte Přejít k prostředku.](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Po otevření návrháře Logic Apps a zobrazení stránky s úvodním videem a šablonami pro běžné vzory aplikací logiky. V oblasti **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Vybrat šablonu prázdná aplikace logiky](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Teď přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který naslouchá příchozím e-mailům s přílohami. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Monitorování příchozích e-mailů

1. Do návrháře v poli hledání zadejte `when new email arrives` jako filtr. Vyberte pro svého poskytovatele e-mailu tento trigger: **Při přijetí nového e-mailu – <*váš_poskytovatel_e-mailu*>**.

   Příklad:

   ![Výběr triggeru pro konkrétního poskytovatele e-mailu: „Při přijetí nového e-mailu“](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook.

   * Pro osobní účty Microsoft vyberte Outlook.com.

1. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se ke svému e-mailovému účtu, aby se s ním v Logic Apps mohlo vytvořit propojení.

1. Teď zadejte kritéria, která trigger použije k filtrování nových e-mailů.

   1. Zadejte níže uvedená nastavení pro kontrolu e-mailů.

      ![Určení složky, intervalu a frekvence kontroly e-mailů](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Složka** | Doručená pošta | E-mailová složka, která se má kontrolovat |
      | **Má přílohu** | Yes | Načte jen e-maily s přílohami. <p>**Poznámka:** Trigger neodebere z vašeho účtu žádné e-maily. Kontroluje jenom nové zprávy a zpracovává jenom e-maily odpovídající filtru pro předmět. |
      | **Zahrnout přílohy** | Yes | Místo samotné kontroly příloh je načtěte jako vstup do pracovního postupu. |
      | **Interval** | 1 | Počet intervalů, po které se má čekat mezi kontrolami |
      | **Frekvence** | Minuta | Jednota času pro každý interval mezi kontrolami |
      ||||

   1. V seznamu **Přidat nový parametr** vyberte **Filtr předmětu**.

   1. Po zobrazení pole **Filtr předmětu** v akci zadejte předmět, jak je uvedeno zde:

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Filtr předmětu** | `Business Analyst 2 #423501` | Text, který se má najít v předmětu e-mailu |
      ||||

1. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte do jeho záhlaví.

   ![Tvar sbalení pro skrytí podrobností](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Vaše aplikace logiky je teď v provozu, ale kromě kontroly e-mailů nic dalšího nedělá. Teď přidejte podmínku, která určí kritéria pro pokračování pracovního postupu.

## <a name="check-for-attachments"></a>Kontrola příloh

Teď přidejte podmínku, která vybere jenom e-maily s přílohami.

1. V aktivační události vyberte **Nový krok**.

   ![Nový krok](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. V části **zvolit akci** zadejte do vyhledávacího pole `condition` . Vyberte tuto akci: **Podmínka**

   ![Vybrat podmínku](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Přejmenujte podmínku tak, aby její popis lépe vystihoval účel. V záhlaví podmínky vyberte tlačítko se třemi tečkami (..**.**) > **Přejmenovat**.

      ![Přejmenování podmínky](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Přejmenujte podmínku s použitím tohoto popisu: `If email has attachments and key subject phrase`

1. Vytvořte podmínku, která kontroluje, jestli mají e-maily přílohy.

   1. Na prvním řádku pod **A** klikněte do levého pole. Zobrazí se seznam dynamického obsahu. Vyberte v něm vlastnost **Má přílohu**.

      ![Snímek obrazovky, který zobrazuje vlastnost "a" pro podmínku a výběr vlastnosti "má přílohy".](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. V prostředním poli ponechte operátor **rovná se**.

   1. Do pravého pole zadejte hodnotu **true** , která má být porovnána s hodnotou vlastnosti s **přílohou** z triggeru.

      ![Vytvoření podmínky](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Pokud se obě hodnoty shodují, má e-mail aspoň jednu přílohu, podmínka je splněna a pracovní postup pokračuje.

   V základní definici aplikace logiky, kterou si můžete zobrazit v okně editoru kódu, vypadá tato podmínka jako v tomto příkladu:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. Uložte aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="test-your-condition"></a>Otestování podmínky

Teď otestujte, jestli podmínka správně funguje:

1. Pokud vaše aplikace logiky ještě není spuštěná, vyberte na panelu nástrojů návrháře možnost **Spustit** .

   Tímto krokem ručně spustíte aplikaci logiky a nemusíte čekat na uplynutí zadaného intervalu. Dokud ale do schránky nedorazí testovací e-mail, nic se nestane.

1. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět e-mailu obsahuje text, který jste zadali ve **filtru předmětu** triggeru: `Business Analyst 2 #423501`

   * E-mail obsahuje jednu přílohu. Prozatím jednoduše vytvořte prázdný textový soubor a připojte ho k e-mailu.

   Jakmile e-mail dorazí, zkontroluje v něm aplikace logiky přílohy a zadaný text předmětu. Pokud bude podmínka splněna, trigger se aktivuje a zajistí, aby modul Logic Apps vytvořil instanci aplikace logiky a zahájil pracovní postup.

1. Pokud chcete ověřit, že Trigger vyvolal a jestli aplikace logiky proběhla úspěšně, vyberte v nabídce aplikace logiky možnost **Přehled**.

   ![Kontrola historie triggeru a spuštění](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Pokud se aplikace logiky i přes úspěšnou aktivaci triggeru neaktivovala nebo nespustila, podívejte se do článku [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

V dalším kroku definujte akce, které mají proběhnout ve větvi **Pokud je true**. Pokud chcete e-mail uložit spolu s přílohami, odeberte z jeho těla veškeré kódování HTML a potom v kontejneru úložiště vytvořte objekty blob pro e-maily a přílohy.

> [!NOTE]
> Ve větvi **Pokud je false**, když e-mail nemá přílohy, se od aplikace logiky nevyžadují žádné kroky.
> Jako bonusové cvičení můžete po dokončení tohoto kurzu přidat do větve **Pokud je false** případné akce, které byste chtěli provést.

## <a name="call-removehtmlfunction"></a>Volání funkce RemoveHTMLFunction

Tento krok přidá do aplikace logiky funkci Azure, kterou jste předtím vytvořili, a předá obsah e-mailu z triggeru e-mailu do vaší funkce.

1. V nabídce aplikace logiky zvolte **Návrhář aplikace logiky**. Ve větvi **Pokud je true** vyberte **přidat akci**.

   ![Přidání akce ve větvi „Pokud je true“](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. Ve vyhledávacím poli Najděte Azure functions a vyberte tuto akci: **Zvolte funkci Azure Functions-Azure Functions**

   ![Výběr akce v části „Zvolte funkci Azure“](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. Vyberte dříve vytvořenou aplikaci Function App, která je `CleanTextFunctionApp` v tomto příkladu:

   ![Výběr aplikace funkcí Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. Teď vyberte funkci: **RemoveHTMLFunction**

   ![Výběr funkce Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. Přejmenujte obrazec funkce s použitím tohoto popisu: `Call RemoveHTMLFunction to clean email body`

1. Teď zadejte vstup, který má funkce zpracovávat.

   1. V části **Tělo požadavku** zadejte následující text s koncovou mezerou:

      `{ "emailBody":`

      Při práci na tomto vstupu v následujících krocích se bude zobrazovat chyba upozorňující na neplatný formát JSON, dokud nebude vstup správně naformátovaný jako JSON. Pokud jste tuto funkci už testovali, použil se u zadávaného vstupu formát JSON (JavaScript Object Notation). V těle požadavku je proto potřeba použít stejný formát.

      Když navíc umístíte kurzor do pole **Tělo požadavku**, zobrazí se seznam dynamického obsahu, ve kterém můžete vybrat hodnoty vlastností dostupné z předchozích akcí.

   1. V seznamu dynamického obsahu v části **Při přijetí nového e-mailu** vyberte vlastnost **Text**. Za tuto vlastnost nezapomeňte přidat uzavírací složenou závorku: `}`

      ![Určení těla požadavku k předání do funkce](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   Až budete hotoví, vstup vaší funkce bude vypadat jako v tomto příkladu:

   ![Dokončené tělo požadavku k předání do vaší funkce](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. Uložte aplikaci logiky.

V dalším kroku přidejte akci, která vytvoří v kontejneru úložiště objekt blob, kterého uložíte tělo e-mailu.

## <a name="create-blob-for-email-body"></a>Vytvoření objektu blob pro tělo e-mailu

1. V bloku **if true** a v rámci funkce Azure vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte `create blob` jako filtr a vyberte tuto akci: **vytvořit objekt BLOB**

   ![Přidání akce pro vytvoření objektu blob pro tělo e-mailu](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. Vytvořte propojení svého účtu úložiště s nastaveními uvedenými na následujícím obrázku. Po dokončení vyberte **Vytvořit**.

   ![Vytvoření propojení s účtem úložiště](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Nastavení | Hodnota | Popis |
   | ------- | ----- | ----------- |
   | **Název připojení** | AttachmentStorageConnection | Popisný název propojení |
   | **Účet úložiště** | attachmentstorageacct | Název účtu úložiště, který jste dříve vytvořili pro ukládání příloh |
   ||||

1. Přejmenujte akci **Vytvořit objekt blob** s použitím tohoto popisu: `Create blob for email body`

1. U akce **Vytvořit objekt blob** zadejte informace a vyberte pole k vytvoření objektu blob podle následujícího příkladu:

   ![Zadání informací o objektu blob pro tělo e-mailu](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Nastavení | Hodnota | Popis |
   | ------- | ----- | ----------- |
   | **Cesta ke složce** | /přílohy | Název kontejneru, který jste dříve vytvořili, a cesta k němu. V tomto příkladu klikněte na ikonu složky a vyberte kontejner „/attachments“. |
   | **Název objektu blob** | Pole **Od** | V tomto příkladu použijte jako název objektu blob jméno odesílatele. Klikněte do tohoto pole, aby se zobrazil seznam dynamického obsahu, a u akce **Při přijetí nového e-mailu** vyberte pole **Od**. |
   | **Obsah objektu blob** | Pole **Obsah** | Pro účely tohoto příkladu se jako obsah objektu blob používá tělo e-mailu bez kódování HTML. Klikněte do tohoto pole, aby se zobrazil seznam dynamického obsahu, a u akce **Call RemoveHTMLFunction to clean email body** zvolte **Text**. |
   ||||

   Po dokončení bude akce vypadat jako v tomto příkladu:

   ![Snímek obrazovky, který ukazuje příklad dokončené akce vytvořit objekt BLOB.](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. Uložte aplikaci logiky.

### <a name="check-attachment-handling"></a>Kontrola zpracování příloh

Teď otestujte, jestli aplikace logiky zpracovává e-maily tak, jak jste zamýšleli:

1. Pokud vaše aplikace logiky ještě není spuštěná, vyberte na panelu nástrojů návrháře možnost **Spustit** .

1. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět e-mailu obsahuje text, který jste zadali ve **filtru předmětu** triggeru: `Business Analyst 2 #423501`

   * E-mail obsahuje aspoň jednu přílohu. Prozatím stačí vytvořit jeden prázdný textový soubor a připojit ho k e-mailu.

   * Váš e-mail obsahuje nějaký testovací obsah v těle, například: `Testing my logic app`

   Pokud se aplikace logiky i přes úspěšnou aktivaci triggeru neaktivovala nebo nespustila, podívejte se do článku [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

1. Zkontrolujte, jestli aplikace logiky uložila e-mail do správného kontejneru úložiště.

   1. V Průzkumník služby Storage rozbalte **místní & připojené**  >  attachmentstorageacct **účty úložiště**–  >  přílohy kontejnerů objektů BLOB **(Key)**  >    >  .

   1. Vyhledejte testovací e-mail v kontejneru **přílohy**.

      V této chvíli se v kontejneru zobrazuje jenom e-mail, protože aplikace logiky zatím nezpracovává přílohy.

      ![Vyhledání uloženého e-mailu v Průzkumníkovi služby Storage](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. Až budete hotovi, e-mail z Průzkumníka služby Storage odstraňte.

1. Pokud chcete otestovat také větev **Pokud je false**, která momentálně nic nedělá, pošlete sami sobě e-mail nesplňující kritéria funkce.

Teď přidejte smyčku, která zajistí zpracování veškerých příloh e-mailu.

## <a name="process-attachments"></a>Zpracování příloh

Pokud chcete zpracovat všechny přílohy e-mailu, přidejte do pracovního postupu aplikace logiky smyčku **For each**.

1. V obrazci **vytvořit objekt BLOB pro tělo e-mailu** vyberte **přidat akci**.

   ![Přidání smyčky „pro každý“](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. V části **zvolit akci** zadejte do vyhledávacího pole `for each` jako filtr a vyberte tuto akci: **pro každý**

   ![Vyberte for each.](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Přejmenujte smyčku s použitím tohoto popisu: `For each email attachment`

1. Teď zadejte data, která má smyčka zpracovávat. Klikněte do pole **Vybrat výstup z předchozího postupu**, aby se zobrazil seznam dynamického obsahu, a potom zvolte **Přílohy**.

   ![Výběr možnosti „Přílohy“](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Pole **Přílohy** předává pole hodnot obsahující všechny přílohy zahrnuté v e-mailu. Smyčka typu **pro každý** opakuje akce pro každou položku, která se pomocí pole předá.

1. Uložte aplikaci logiky.

V dalším kroku přidejte akci, která uloží každou přílohu jako objekt blob do kontejneru úložiště **přílohy**.

## <a name="create-blob-for-each-attachment"></a>Vytvoření objektu blob pro každou přílohu

1. V části **pro každý emailový cyklus přílohy** vyberte **přidat akci** , abyste mohli zadat úlohu, která se má provést u každé nalezené přílohy.

   ![Přidání akce do smyčky](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. Do vyhledávacího pole zadejte `create blob` jako filtr a pak vyberte tuto akci: **vytvořit objekt BLOB**

   ![Přidání akce pro vytvoření objektu blob](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. Přejmenujte akci **Vytvořit objekt blob 2** s použitím tohoto popisu: `Create blob for each email attachment`

1. U akce **Create blob for each email attachment** zadejte informace a vyberte vlastnosti k vytvoření jednotlivých objektů blob podle následujícího příkladu:

   ![Zadání informací o objektech blob](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Nastavení | Hodnota | Popis |
   | ------- | ----- | ----------- |
   | **Cesta ke složce** | /přílohy | Název kontejneru, který jste dříve vytvořili, a cesta k němu. V tomto příkladu klikněte na ikonu složky a vyberte kontejner „/attachments“. |
   | **Název objektu blob** | Pole **Název** | V tomto příkladu se jako název objektu blob používá název přílohy. Klikněte do tohoto pole, aby se zobrazil seznam dynamického obsahu, a u akce **Při přijetí nového e-mailu** vyberte pole **Název**. |
   | **Obsah objektu blob** | Pole **Obsah** | Pro účely tohoto příkladu se jako obsah objektu blob používá pole **Obsah**. Klikněte do tohoto pole, aby se zobrazil seznam dynamického obsahu, a u akce **Při přijetí nového e-mailu** vyberte pole **Obsah**. |
   ||||

   Po dokončení bude akce vypadat jako v tomto příkladu:

   ![Dokončená akce „Vytvořit objekt blob“](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. Uložte aplikaci logiky.

### <a name="check-attachment-handling"></a>Kontrola zpracování příloh

Teď otestujte, jestli aplikace logiky zpracovává e-přílohy tak, jak jste zamýšleli:

1. Pokud vaše aplikace logiky ještě není spuštěná, vyberte na panelu nástrojů návrháře možnost **Spustit** .

1. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět e-mailu obsahuje text, který jste zadali ve vlastnosti **filtru předmětu** triggeru: `Business Analyst 2 #423501`

   * E-mail obsahuje aspoň dvě přílohy. Prozatím jednoduše vytvořte dva prázdné textové soubory a připojte je k e-mailu.

   Pokud se aplikace logiky i přes úspěšnou aktivaci triggeru neaktivovala nebo nespustila, podívejte se do článku [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

1. Zkontrolujte, jestli aplikace logiky uložila e-mail a přílohy do správného kontejneru úložiště.

   1. V Průzkumník služby Storage rozbalte **místní & připojené**  >  attachmentstorageacct **účty úložiště**–  >  přílohy kontejnerů objektů BLOB **(Key)**  >    >  .

   1. Zkontrolujte, jestli se do kontejneru **přílohy** uložil e-mail i přílohy.

      ![Vyhledání uloženého e-mailu a příloh](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. Až budete hotovi, e-mail a přílohy z Průzkumníka služby Storage odstraňte.

V dalším kroku přidejte akci, která zajistí, aby aplikace logiky odeslala e-mail ke kontrole příloh.

## <a name="send-email-notifications"></a>Odeslání e-mailových oznámení

1. Ve větvi **Pokud je true** v části **pro každou e-mailovou smyčku pro každý e-mail** vyberte **přidat akci**.

   ![Přidání akce do smyčky „pro každý“](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. Do vyhledávacího pole zadejte `send email` jako filtr a potom pro vašeho poskytovatele e-mailu vyberte akci "Odeslat e-mail".

   Pokud chcete v seznamu akcí vyfiltrovat konkrétní službu, můžete nejdřív vybrat konektor.

   ![Výběr akce „odeslat e-mail“ pro vašeho poskytovatele e-mailu](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook.

   * Pro osobní účty Microsoft vyberte Outlook.com.

1. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se ke svému e-mailovému účtu a Logic Apps vytvoří připojení k vašemu e-mailovému účtu.

1. Přejmenujte akci **Odeslat e-mail** s použitím tohoto popisu: `Send email for review`

1. Zadejte potřebné informace pro tuto akci a vyberte pole, která chcete do e-mailu zahrnout, jak je vidět v následujícím příkladu. Pokud chcete do textového pole přidat prázdné řádky, stiskněte Shift + Enter.

   ![Odeslání e-mailového oznámení](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Pokud nemůžete najít očekávané pole v seznamu dynamického obsahu, vyberte **Zobrazit více** vedle **při přijetí nového e-mailu**.

   | Nastavení | Hodnota | Poznámky |
   | ------- | ----- | ----- |
   | **Do** | <*příjemce-e-mailová adresa*> | Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   | **Předmět**  | ```ASAP - Review applicant for position:```**Subjekt** | Předmět e-mailu, který chcete zahrnout. Klikněte do tohoto pole, zadejte příklad textu a v seznamu dynamického obsahu u položky **Při přijetí nového e-mailu** vyberte pole **Předmět**. |
   | **Text** | ```Please review new applicant:``` <p>```Applicant name:```**Od** <p>```Application file location:``` **Cesta** <p>```Application email content:``` **Text** | Obsah textu e-mailu. Klikněte do tohoto pole, zadejte příklad textu a v seznamu dynamického obsahu vyberte tato pole: <p>- Pole **Od** v části **Při přijetí nového e-mailu** </br>- Pole **Cesta** v části **Vytvořit objekt blob pro tělo e-mailu** </br>- Pole **Text** v části **Volat funkci RemoveHTMLFunction k vymazání textu e-mailu** |
   ||||

   > [!NOTE]
   > Pokud vyberete pole obsahující pole hodnot, například **Obsah**, což je pole obsahující přílohy, návrhář kolem akce odkazující na toto pole automaticky přidá smyčku For each.
   > Aplikace logiky tak může provést příslušnou akci pro každou položku pole.
   > Pokud chcete smyčku odebrat, odeberte pole pro pole, přesuňte odkazovou akci na vnější smyčku, v záhlaví smyčky vyberte tři tečky (**...**) a vyberte **Odstranit**.

1. Uložte aplikaci logiky.

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Hotová aplikace logiky](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

1. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět e-mailu obsahuje text, který jste zadali ve vlastnosti **filtru předmětu** triggeru: `Business Analyst 2 #423501`

   * E-mail obsahuje jednu nebo více příloh. Můžete znovu použít prázdný textový soubor z předchozího testu. Pokud chcete vyzkoušet realističtější scénář, připojte soubor s životopisem.

   * Tělo e-mailu obsahuje následující text, který můžete zkopírovat a vložit:

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. Spusťte svou aplikaci logiky. Pokud všechno proběhne úspěšně, z aplikace logiky vám přijde e-mail, který bude vypadat přibližně takto:

   ![E-mailové oznámení odeslané aplikací logiky](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Blahopřejeme, právě jste vytvořili a spustili aplikaci logiky, která automatizuje úlohy v různých službách Azure a volá vlastní kód.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tuto ukázku už nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. Do pole Azure Search nejvyšší úrovně zadejte `resources groups` a vyberte **skupiny prostředků**.

   ![Vyhledejte a vyberte "skupiny prostředků".](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. V seznamu **skupiny prostředků** vyberte skupinu prostředků tohoto kurzu. 

   ![Vyhledání skupiny prostředků pro kurz](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. V podokně **Přehled** vyberte **Odstranit skupinu prostředků**.

   ![Odstranění skupiny prostředků aplikace logiky](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. Jakmile se zobrazí podokno potvrzení, zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která zpracovává a ukládá e-mailové přílohy díky integraci služeb Azure, mimo jiné služeb Azure Storage a Azure Functions. Teď doporučujeme blíže se seznámit s dalšími konektory, které se dají používat k vytváření aplikací logiky.

> [!div class="nextstepaction"]
> [Další informace o konektorech pro Logic Apps](../connectors/apis-list.md)
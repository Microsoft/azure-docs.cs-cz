---
title: Automatizace úloh s více službami Azure
description: Kurz – vytváření automatizovaných pracovních postupů pro zpracování e-mailů pomocí Azure Logic Apps, Azure Storage a Azure Functions
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/27/2020
ms.openlocfilehash: 4adcda6030ed59cb6cc2285eb1c1eea0f768662c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77662668"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>Kurz: Automatizace úloh pro zpracování e-mailů pomocí Azure Logic Apps, Azure Functions a Azure Storage

Služba Azure Logic Apps pomáhá automatizovat pracovní postupy a integrovat data napříč službami Azure, službami Microsoftu a dalšími aplikacemi SaaS (software jako služba) a místními systémy. Tento kurz ukazuje postupy při vytváření [aplikace logiky](../logic-apps/logic-apps-overview.md), která bude zpracovávat příchozí e-maily včetně případných příloh. Tato aplikace logiky analyzuje obsah e-mailu, uloží obsah do úložiště Azure a odešle oznámení pro kontrolu tohoto obsahu.

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

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* E-mailový účet od poskytovatele podporovaného v Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](https://docs.microsoft.com/connectors/).

  Tato aplikace logiky používá účet Office 365 Outlook. Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

* Zdarma stáhnout a nainstalovat [Průzkumníka služby Microsoft Azure Storage](https://storageexplorer.com/). Tento nástroj vám umožní zkontrolovat, jestli máte kontejner úložiště správně nastavený.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

## <a name="set-up-storage-to-save-attachments"></a>Nastavení úložiště pro ukládání příloh

Příchozí e-maily a přílohy můžete ukládat jako objekty blob v [kontejneru úložiště Azure](../storage/common/storage-introduction.md).

1. Než budete moci vytvořit kontejner úložiště, [vytvořte si účet úložiště](../storage/common/storage-account-create.md) s těmito nastaveními na kartě **Základy** na webu Azure Portal:

   | Nastavení | Hodnota | Popis |
   |---------|-------|-------------|
   | **Předplatné** | <*Název předplatného Azure*> | Název vašeho předplatného Azure |  
   | **Skupina prostředků** | <*Skupina prostředků Azure*> | Název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md), který slouží k uspořádání a správě souvisejících prostředků Tento příklad používá "LA-Tutorial-RG". <p>**Poznámka:** Skupina prostředků existuje v konkrétní oblasti. Položky z tohoto kurzu nemusí být k dispozici ve všech oblastech, snažte se nicméně používat stejnou oblast, kdykoli je to možné. |
   | **Název účtu úložiště** | <*Název účtu Azure-storage*> | Název účtu úložiště, který musí mít 3-24 znaků a může obsahovat pouze malá písmena a čísla. Tento příklad používá "attachmentstorageacct". |
   | **Umístění** | <*Oblast Azure*> | Oblast, kde chcete ukládat informace o vašem účtu úložiště. Tento příklad používá "Západní USA". |
   | **Výkon** | Standard | Toto nastavení specifikuje podporované datové typy a média pro ukládání dat. Další informace najdete v tématu [Typy účtů úložiště](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Druh účtu** | Obecné účely | [Typ účtu úložiště](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **Replikace** | Místně redundantní úložiště (LRS) | Toto nastavení určuje, jak se budou kopírovat, ukládat, spravovat a synchronizovat data. Viz [Místně redundantní úložiště (LRS): Nízkonákladová redundance dat pro Azure Storage](../storage/common/storage-redundancy-lrs.md). |
   | **Úroveň přístupu (výchozí)** | Zachovat aktuální nastavení. |
   ||||

   Na kartě **Upřesnit** vyberte toto nastavení:

   | Nastavení | Hodnota | Popis |
   |---------|-------|-------------|
   | **Vyžádání bezpečného přenosu** | Zakázáno | Toto nastavení určuje zabezpečení vyžadované u požadavků směřujících od připojení. Další informace najdete v tématu [Vyžádání bezpečného přenosu](../storage/common/storage-require-secure-transfer.md). |
   ||||

   K vytvoření účtu úložiště můžete použít taky prostředí [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) nebo [Azure CLI](../storage/common/storage-quickstart-create-storage-account-cli.md).

1. Až budete hotovi, vyberte **Zkontrolovat + vytvořit**.

1. Až Azure nasadí váš účet úložiště, najděte svůj účet úložiště a získejte přístupový klíč účtu úložiště:

   1. V nabídce účtu úložiště v části **Nastavení** vyberte **Přístupové klíče**.

   1. Zkopírujte název účtu úložiště a **klíč1**a uložte tyto hodnoty na bezpečném místě.

      ![Zkopírování a uložení názvu a klíče účtu úložiště](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   K získání přístupového klíče účtu úložiště můžete použít taky prostředí [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) nebo [Azure CLI](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az-storage-account-keys-list).

1. Vytvořte kontejner úložiště objektů blob pro přílohy e-mailů.

   1. V nabídce účtu úložiště vyberte **Přehled**. V podokně Přehled vyberte **Kontejnery**.

      ![Přidání kontejneru úložiště objektů blob](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. Po otevření stránky **Kontejnery** vyberte na panelu nástrojů **Kontejner**.

   1. V části Nový `attachments` **kontejner**zadejte jako název kontejneru. V části **Úroveň veřejného přístupu**vyberte **kontejner (anonymní přístup pro čtení pro kontejnery a objekty BLOB)** > **OK**.

      Jakmile budete hotovi, objeví se kontejner úložiště ve vašem účtu úložiště tady na portálu Azure Portal:

      ![Hotový kontejner úložiště](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Pokud chcete vytvořit kontejner úložiště, můžete taky použít [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontainer) nebo [Azure CLI](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create).

Dalším krokem je propojení Průzkumníka služby Storage s vaším účtem.

## <a name="set-up-storage-explorer"></a>Nastavení Průzkumníka služby Storage

Teď se svým účtem úložiště propojte Průzkumníka služby Storage, abyste si mohli ověřit, že aplikace logiky do kontejneru úložiště správně ukládá přílohy jako objekty blob.

1. Spusťte Průzkumníka úložiště Microsoft Azure.

   Průzkumník služby Storage zobrazí výzvu k připojení k účtu úložiště.

1. V podokně **Připojit k úložišti Azure** vyberte Použít název účtu úložiště a **klíč** > **Další**.

   ![Průzkumník služby Storage – připojení k účtu úložiště](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Pokud se na panelu nástrojů Průzkumník a ukládání nezobrazí žádná výzva, vyberte **přidat účet**.

1. V části **Zobrazovaný název**zadejte popisný název připojení. Do pole **Název účtu** zadejte název svého účtu úložiště. V části **Klíč účet**zadejte přístupový klíč, který jste dříve uložili, a vyberte **Další**.

1. Potvrďte informace o připojení a pak vyberte **Připojit**.

   Průzkumník úložiště vytvoří připojení a zobrazí váš účet úložiště v okně Průzkumníka v části **Místní & připojené** > **účty úložiště**.

1. Pokud chcete najít kontejner úložiště objektů blob, rozbalte v části **Účty úložiště**účet úložiště, což je **storageacct přílohy,** a rozbalte **kontejnery objektů blob,** kde najdete kontejner **příloh,** například:

   ![Průzkumník služby Storage – vyhledání kontejneru úložiště](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

V dalším kroku vytvořte [funkci Azure](../azure-functions/functions-overview.md), která z příchozích e-mailů odebere kódování HTML.

## <a name="create-function-to-clean-html"></a>Vytvoření funkce k vyčištění kódu HTML

Teď pomocí připraveného fragmentu kódu a následujícího postupu vytvořte funkci Azure, která z každého příchozího e-mailu odebere kódování HTML. Obsah e-mailů se tak vyčistí a jeho zpracování bude jednodušší. Tuto funkci pak můžete volat z aplikace logiky.

1. Než funkci vytvoříte, nejdřív [vytvořte aplikaci funkcí](../azure-functions/functions-create-function-app-portal.md) s těmito nastaveními:

   | Nastavení | Hodnota | Popis |
   | ------- | ----- | ----------- |
   | **Název aplikace** | <*název aplikace funkce*> | Název aplikace funkce, který musí být globálně jedinečný v celém Azure. Tento příklad již používá "CleanTextFunctionApp", proto zadejte jiný název, například "MyCleanTextFunctionApp-<*vaše jméno*>" |
   | **Předplatné** | <*název předplatného Azure*> | Stejné předplatné Azure, jaké jste používali dříve |
   | **Skupina prostředků** | LA-Tutorial-RG | Stejná skupina prostředků Azure, jakou jste používali dříve |
   | **OS** | <*váš operační systém*> | Vyberte operační systém, který podporuje váš oblíbený programovací jazyk funkcí. V tomto příkladu vyberte **systém Windows**. |
   | **Plán hostování** | Plán Consumption | Toto nastavení určuje, jak se při běhu aplikace funkcí mají přidělovat a škálovat prostředky, například výpočetní výkon. Podívejte se na [porovnání plánů hostování](../azure-functions/functions-scale.md). |
   | **Umístění** | USA – západ | Stejná oblast, jakou jste používali dříve |
   | **Zásobník runtime** | Upřednostňovaný jazyk | Vyberte runtime, který podporuje váš oblíbený programovací jazyk funkcí. Vyberte **.NET** pro funkce C# a F#. |
   | **Úložiště** | cleantextfunctionstorageacct | Vytvořte pro svou aplikaci funkcí účet úložiště. Použijte při tom jenom malá písmena a číslice. <p>**Poznámka:** Tento účet úložiště obsahuje aplikace pro funkce a liší se od dříve vytvořeného účtu úložiště pro e-mailové přílohy. |
   | **Application Insights** | Zakázat | Zapne monitorování aplikací pomocí [application insights](../azure-monitor/app/app-insights-overview.md), ale v tomto kurzu vyberte **Zakázat** > **použít**. |
   ||||

   Pokud se vaše aplikace funkcí po nasazení automaticky neotevře, v vyhledávacím poli [na webu Azure Portal](https://portal.azure.com) najděte a vyberte Aplikace **funkcí**. V části **Function App**vyberte aplikaci funkce.

   ![Výběr aplikace funkcí](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Azure vaši aplikaci funkcí automaticky otevře, jak je vidět tady:

   ![Vytvořená aplikace funkcí](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Chcete-li vytvořit aplikaci funkcí, můžete také použít [Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md)nebo [šablony PowerShell a Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

1. V seznamu **Aplikace funkcí** rozbalte aplikaci funkcí, pokud již není rozšířena. V aplikaci funkce vyberte **Funkce**. Na panelu nástrojů funkcí zvolte **Nová funkce**.

   ![Vytvoření nové funkce](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. V části **Vyberte šablonu níže nebo přejděte na rychlý start**vyberte šablonu **aktivační události HTTP.**

   ![Vybrat šablonu aktivační události HTTP](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure vytvoří funkci pomocí šablony specifické pro jazyk pro funkci aktivovanou protokolem HTTP.

1. V okně **Nová funkce** v poli **Název** zadejte `RemoveHTMLFunction`. Zachovat **úroveň autorizace** nastavenou na **funkci**a vybrat **možnost Vytvořit**.

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

1. Jakmile budete mít hotovo, vyberte **Uložit**. Chcete-li otestovat svou funkci, na pravém okraji**<** editoru pod ikonou šipky ( ) vyberte **možnost Testovat**.

   ![Otevření testovacího podokna](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. V podokně **Test** v části **Text požadavku**zadejte tento řádek a vyberte **Spustit**.

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![Otestování funkce](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   V okně **Výstup** se zobrazí výsledek funkce:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Když zkontrolujete, že funkce pracuje správně, vytvořte aplikaci logiky. Tento kurz vysvětluje postup vytvoření funkce, která z e-mailů odebere kódování HTML. Služba Logic Apps ale poskytuje také konektor pro převod **HTML na text**.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. Do vyhledávacího pole Azure nejvyšší `logic apps`úrovně zadejte a vyberte **Logic Apps**.

   ![Vyhledání a výběr "Logic Apps"](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. V podokně **Aplikace logiky** vyberte **Přidat**.

   ![Přidání nové aplikace logiky](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. V podokně **aplikace logiky** zadejte podrobnosti o aplikaci logiky, jak je znázorněno zde. Až budete hotovi, vyberte **Zkontrolovat + vytvořit**.

   ![Zadání informací o aplikaci logiky](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Nastavení | Hodnota | Popis |
   | ------- | ----- | ----------- |
   | **Předplatné** | <*název předplatného Azure*> | Stejné předplatné Azure, jaké jste používali dříve |
   | **Skupina prostředků** | LA-Tutorial-RG | Stejná skupina prostředků Azure, jakou jste používali dříve |
   | **Název aplikace logiky** | LA-ProcessAttachment | Název vaší aplikace logiky |
   | **Vyberte umístění** | USA – západ | Stejná oblast, jakou jste používali dříve |
   | **Analýza protokolů** | Vypnuto | V tomto kurzu vyberte nastavení **Vypnuto.** |
   ||||

1. Po nasazení aplikace Azure vyberte na panelu nástrojů Azure ikonu oznámení a vyberte **Přejít na prostředek**.

   ![Ze seznamu oznámení Azure vyberte "Přejít na prostředek"](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Po otevření aplikace Logika Apps Designer a zobrazí stránku s úvodní video a šablony pro běžné vzory aplikace logiky. V části **Šablony** vyberte **Prázdná aplikace logiky**.

   ![Vybrat prázdnou šablonu aplikace logiky](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Teď přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který naslouchá příchozím e-mailům s přílohami. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Monitorování příchozích e-mailů

1. Na návrháři ve vyhledávacím `when new email arrives` poli zadejte jako filtr. Vyberte pro svého poskytovatele e-mailu tento trigger: **Při přijetí nového e-mailu – <*váš_poskytovatel_e-mailu*>**.

   Například:

   ![Výběr triggeru pro konkrétního poskytovatele e-mailu: „Při přijetí nového e-mailu“](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook.

   * Pro osobní účty Microsoft vyberte Outlook.com.

1. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se ke svému e-mailovému účtu, aby se s ním v Logic Apps mohlo vytvořit propojení.

1. Teď zadejte kritéria, která trigger použije k filtrování nových e-mailů.

   1. Zadejte níže popsaná nastavení pro kontrolu e-mailů.

      ![Určení složky, intervalu a frekvence kontroly e-mailů](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Složka** | Doručená pošta | E-mailová složka, která se má kontrolovat |
      | **Má přílohu** | Ano | Načte jen e-maily s přílohami. <p>**Poznámka:** Trigger neodebere z vašeho účtu žádné e-maily. Kontroluje jenom nové zprávy a zpracovává jenom e-maily odpovídající filtru pro předmět. |
      | **Zahrnout přílohy** | Ano | Místo samotné kontroly příloh je načtěte jako vstup do pracovního postupu. |
      | **Interval** | 1 | Počet intervalů, po které se má čekat mezi kontrolami |
      | **Frequency** | Minuta | Jednota času pro každý interval mezi kontrolami |
      ||||

   1. V seznamu **Přidat nový parametr** vyberte Filtr **předmětu**.

   1. Po zřeteli se v akci pole **Filtr předmětu,** zadejte předmět, jak je zde uvedeno:

      | Nastavení | Hodnota | Popis |
      | ------- | ----- | ----------- |
      | **Filtr předmětu** | `Business Analyst 2 #423501` | Text, který se má najít v předmětu e-mailu |
      ||||

1. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte do jeho záhlaví.

   ![Tvar sbalení pro skrytí podrobností](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

   Vaše aplikace logiky je teď v provozu, ale kromě kontroly e-mailů nic dalšího nedělá. Teď přidejte podmínku, která určí kritéria pro pokračování pracovního postupu.

## <a name="check-for-attachments"></a>Kontrola příloh

Teď přidejte podmínku, která vybere jenom e-maily s přílohami.

1. Pod aktivační událostí vyberte **Nový krok**.

   !["Nový krok"](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. V části **Zvolte akci**zadejte `condition`do vyhledávacího pole . Vybrat tuto akci: **Podmínka**

   ![Vyberte "Podmínka"](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Přejmenujte podmínku tak, aby její popis lépe vystihoval účel. Na záhlaví podmínky vyberte tlačítko elipsy (**...**) > **Přejmenovat**.

      ![Přejmenování podmínky](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Přejmenujte podmínku s použitím tohoto popisu: `If email has attachments and key subject phrase`

1. Vytvořte podmínku, která kontroluje, jestli mají e-maily přílohy.

   1. Na prvním řádku pod **A** klikněte mimo pole vlevo. Zobrazí se seznam dynamického obsahu. Vyberte v něm vlastnost **Má přílohu**.

      ![Vytvoření podmínky](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. V prostředním poli ponechte operátor **rovná se**.

   1. Do pravého pole zadejte **hodnotu true** jako hodnotu, která má být porovnána s hodnotou **vlastnosti Má příloha** z aktivační události.

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

1. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

### <a name="test-your-condition"></a>Otestování podmínky

Teď otestujte, jestli podmínka správně funguje:

1. Pokud vaše aplikace logiky ještě není spuštěná, vyberte **Spustit** na panelu nástrojů návrháře.

   Tímto krokem ručně spustíte aplikaci logiky a nemusíte čekat na uplynutí zadaného intervalu. Dokud ale do schránky nedorazí testovací e-mail, nic se nestane.

1. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět vašeho e-mailu má text, který jste zadali ve **filtru Předmět**aktivační události :`Business Analyst 2 #423501`

   * E-mail obsahuje jednu přílohu. Prozatím jednoduše vytvořte prázdný textový soubor a připojte ho k e-mailu.

   Jakmile e-mail dorazí, zkontroluje v něm aplikace logiky přílohy a zadaný text předmětu. Pokud bude podmínka splněna, trigger se aktivuje a zajistí, aby modul Logic Apps vytvořil instanci aplikace logiky a zahájil pracovní postup.

1. Chcete-li zkontrolovat, že aktivační událost aktivována a aplikace logiky úspěšně spuštěna, v nabídce aplikace logiky, vyberte **Přehled**.

   ![Kontrola historie triggeru a spuštění](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Pokud se aplikace logiky i přes úspěšnou aktivaci triggeru neaktivovala nebo nespustila, podívejte se do článku [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

V dalším kroku definujte akce, které mají proběhnout ve větvi **Pokud je true**. Pokud chcete e-mail uložit spolu s přílohami, odeberte z jeho těla veškeré kódování HTML a potom v kontejneru úložiště vytvořte objekty blob pro e-maily a přílohy.

> [!NOTE]
> Ve větvi **Pokud je false**, když e-mail nemá přílohy, se od aplikace logiky nevyžadují žádné kroky.
> Jako bonusové cvičení můžete po dokončení tohoto kurzu přidat do větve **Pokud je false** případné akce, které byste chtěli provést.

## <a name="call-removehtmlfunction"></a>Volání funkce RemoveHTMLFunction

Tento krok přidá do aplikace logiky funkci Azure, kterou jste předtím vytvořili, a předá obsah e-mailu z triggeru e-mailu do vaší funkce.

1. V nabídce aplikace logiky zvolte **Návrhář aplikace logiky**. Ve větvi **If true** vyberte **Přidat akci**.

   ![Přidání akce ve větvi „Pokud je true“](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. V poli hledání vyhledejte „azure functions“ a vyberte tuto akci: **Zvolte funkci Azure – Azure Functions**.

   ![Výběr akce v části „Zvolte funkci Azure“](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. Vyberte dříve vytvořenou aplikaci funkcí, která je `CleanTextFunctionApp` v tomto příkladu:

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

1. Uložte svou aplikaci logiky.

V dalším kroku přidejte akci, která vytvoří v kontejneru úložiště objekt blob, kterého uložíte tělo e-mailu.

## <a name="create-blob-for-email-body"></a>Vytvoření objektu blob pro tělo e-mailu

1. V bloku **If true** a v části Azure function vyberte Add **a action**.

1. Do vyhledávacího pole `create blob` zadejte jako filtr a vyberte tuto akci: **Vytvořit objekt blob.**

   ![Přidání akce pro vytvoření objektu blob pro tělo e-mailu](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. Vytvořte propojení svého účtu úložiště s nastaveními uvedenými na následujícím obrázku. Až to budete mít, vyberte **Vytvořit**.

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

   ![Dokončená akce „Vytvořit objekt blob“](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. Uložte svou aplikaci logiky.

### <a name="check-attachment-handling"></a>Kontrola zpracování příloh

Teď otestujte, jestli aplikace logiky zpracovává e-maily tak, jak jste zamýšleli:

1. Pokud vaše aplikace logiky ještě není spuštěná, vyberte **Spustit** na panelu nástrojů návrháře.

1. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět vašeho e-mailu má text, který jste zadali ve **filtru Předmět**aktivační události :`Business Analyst 2 #423501`

   * E-mail obsahuje aspoň jednu přílohu. Prozatím stačí vytvořit jeden prázdný textový soubor a připojit tento soubor k e-mailu.

   * Váš e-mail má nějaký testovací obsah v těle, například:`Testing my logic app`

   Pokud se aplikace logiky i přes úspěšnou aktivaci triggeru neaktivovala nebo nespustila, podívejte se do článku [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

1. Zkontrolujte, jestli aplikace logiky uložila e-mail do správného kontejneru úložiště.

   1. V Průzkumníku úložiště rozbalte**přílohy** >  **místní& připojené** > účty úložiště **(key)** > **kontejnery objektů blob** > **přílohy**.

   1. Vyhledejte testovací e-mail v kontejneru **přílohy**.

      V této chvíli se v kontejneru zobrazuje jenom e-mail, protože aplikace logiky zatím nezpracovává přílohy.

      ![Vyhledání uloženého e-mailu v Průzkumníkovi služby Storage](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. Až budete hotovi, e-mail z Průzkumníka služby Storage odstraňte.

1. Pokud chcete otestovat také větev **Pokud je false**, která momentálně nic nedělá, pošlete sami sobě e-mail nesplňující kritéria funkce.

Teď přidejte smyčku, která zajistí zpracování veškerých příloh e-mailu.

## <a name="process-attachments"></a>Zpracování příloh

Pokud chcete zpracovat všechny přílohy e-mailu, přidejte do pracovního postupu aplikace logiky smyčku **For each**.

1. V části Vytvořit objekt blob pro obrazec **tělo e-mailu** vyberte **Přidat akci**.

   ![Přidání smyčky „pro každý“](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. V části **Zvolte akci**zadejte `for each` do vyhledávacího pole jako filtr a vyberte tuto akci: **Pro každou akci**

   ![Vyberte "Pro každého"](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Přejmenujte smyčku s použitím tohoto popisu: `For each email attachment`

1. Teď zadejte data, která má smyčka zpracovávat. Klikněte do pole **Vybrat výstup z předchozího postupu**, aby se zobrazil seznam dynamického obsahu, a potom zvolte **Přílohy**.

   ![Výběr možnosti „Přílohy“](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Pole **Přílohy** předává pole hodnot obsahující všechny přílohy zahrnuté v e-mailu. Smyčka typu **pro každý** opakuje akce pro každou položku, která se pomocí pole předá.

1. Uložte svou aplikaci logiky.

V dalším kroku přidejte akci, která uloží každou přílohu jako objekt blob do kontejneru úložiště **přílohy**.

## <a name="create-blob-for-each-attachment"></a>Vytvoření objektu blob pro každou přílohu

1. Ve smyčce **Pro každou přílohu e-mailu** vyberte **Přidat akci,** abyste mohli určit úkol, který chcete provést u každé nalezené přílohy.

   ![Přidání akce do smyčky](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. Do vyhledávacího pole `create blob` zadejte jako filtr a vyberte tuto akci: **Vytvořit objekt blob.**

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

1. Uložte svou aplikaci logiky.

### <a name="check-attachment-handling"></a>Kontrola zpracování příloh

Teď otestujte, jestli aplikace logiky zpracovává e-přílohy tak, jak jste zamýšleli:

1. Pokud vaše aplikace logiky ještě není spuštěná, vyberte **Spustit** na panelu nástrojů návrháře.

1. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět vašeho e-mailu má text, který jste zadali ve vlastnosti **filtru Předmět** aktivační události:`Business Analyst 2 #423501`

   * E-mail obsahuje aspoň dvě přílohy. Prozatím jednoduše vytvořte dva prázdné textové soubory a připojte je k e-mailu.

   Pokud se aplikace logiky i přes úspěšnou aktivaci triggeru neaktivovala nebo nespustila, podívejte se do článku [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

1. Zkontrolujte, jestli aplikace logiky uložila e-mail a přílohy do správného kontejneru úložiště.

   1. V Průzkumníku úložiště rozbalte**přílohy** >  **místní& připojené** > účty úložiště **(key)** > **kontejnery objektů blob** > **přílohy**.

   1. Zkontrolujte, jestli se do kontejneru **přílohy** uložil e-mail i přílohy.

      ![Vyhledání uloženého e-mailu a příloh](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. Až budete hotovi, e-mail a přílohy z Průzkumníka služby Storage odstraňte.

V dalším kroku přidejte akci, která zajistí, aby aplikace logiky odeslala e-mail ke kontrole příloh.

## <a name="send-email-notifications"></a>Odeslání e-mailových oznámení

1. Ve větvi **Pokud je to pravda,** v části Pro každou **přílohu** **e-mailu** vyberte Přidat akci .

   ![Přidání akce do smyčky „pro každý“](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. Do vyhledávacího pole `send email` zadejte jako filtr a pak vyberte akci "Odeslat e-mail" pro poskytovatele e-mailu.

   Pokud chcete v seznamu akcí vyfiltrovat konkrétní službu, můžete nejdřív vybrat konektor.

   ![Výběr akce „odeslat e-mail“ pro vašeho poskytovatele e-mailu](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook.

   * Pro osobní účty Microsoft vyberte Outlook.com.

1. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se ke svému e-mailovému účtu a Logic Apps vytvoří připojení k vašemu e-mailovému účtu.

1. Přejmenujte akci **Odeslat e-mail** s použitím tohoto popisu: `Send email for review`

1. Zadejte potřebné informace pro tuto akci a vyberte pole, která chcete do e-mailu zahrnout, jak je vidět v následujícím příkladu. Pokud chcete do textového pole přidat prázdné řádky, stiskněte Shift + Enter.

   ![Odeslání e-mailového oznámení](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Pokud v seznamu dynamického obsahu nemůžete najít očekávané pole, vyberte **Zobrazit další** vedle **položky Když přijde nový e-mail**.

   | Nastavení | Hodnota | Poznámky |
   | ------- | ----- | ----- |
   | **Akce** | <*e-mailová adresa příjemce*> | Pro účely testování můžete použít svou vlastní e-mailovou adresu. |
   | **Subjekt**  | ```ASAP - Review applicant for position:``` **Subjekt** | Předmět e-mailu, který chcete zahrnout. Klikněte do tohoto pole, zadejte příklad textu a v seznamu dynamického obsahu u položky **Při přijetí nového e-mailu** vyberte pole **Předmět**. |
   | **Text** | ```Please review new applicant:``` <p>```Applicant name:``` **Od** <p>```Application file location:```**Cesta** <p>```Application email content:``` **Text** | Obsah textu e-mailu. Klikněte do tohoto pole, zadejte příklad textu a v seznamu dynamického obsahu vyberte tato pole: <p>- Pole **Od** v části **Při přijetí nového e-mailu** </br>- Pole **Cesta** v části **Vytvořit objekt blob pro tělo e-mailu** </br>- Pole **Text** v části **Volat funkci RemoveHTMLFunction k vymazání textu e-mailu** |
   ||||

   > [!NOTE]
   > Pokud vyberete pole obsahující pole hodnot, například **Obsah**, což je pole obsahující přílohy, návrhář kolem akce odkazující na toto pole automaticky přidá smyčku For each.
   > Aplikace logiky tak může provést příslušnou akci pro každou položku pole.
   > Chcete-li smyčku odebrat, odeberte pole pole, přesuňte akci odkazování mimo smyčku, vyberte tři tečky (**...**) v záhlaví smyčky a vyberte **Odstranit**.

1. Uložte svou aplikaci logiky.

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Hotová aplikace logiky](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

1. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět vašeho e-mailu má text, který jste zadali ve vlastnosti **filtru Předmět** aktivační události:`Business Analyst 2 #423501`

   * Váš e-mail obsahuje jednu nebo více příloh. Můžete znovu použít prázdný textový soubor z předchozího testu. Pokud chcete vyzkoušet realističtější scénář, připojte soubor s životopisem.

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

1. Do vyhledávacího pole Azure nejvyšší `resources groups`úrovně zadejte a vyberte **skupiny prostředků**.

   ![Vyhledání a výběr "Skupiny zdrojů"](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. Ze seznamu **Skupiny prostředků** vyberte skupinu prostředků pro tento kurz. 

   ![Vyhledání skupiny prostředků pro kurz](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. V podokně **Přehled** vyberte **Odstranit skupinu prostředků**.

   ![Odstranění skupiny prostředků aplikace logiky](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. Po zobrazení potvrzovacího podokna zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která zpracovává a ukládá e-mailové přílohy díky integraci služeb Azure, mimo jiné služeb Azure Storage a Azure Functions. Teď doporučujeme blíže se seznámit s dalšími konektory, které se dají používat k vytváření aplikací logiky.

> [!div class="nextstepaction"]
> [Další informace o konektorech pro Logic Apps](../connectors/apis-list.md)
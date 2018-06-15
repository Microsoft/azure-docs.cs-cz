---
title: Vytváření pracovních postupů pro zpracování e-mailů a příloh – Azure Logic Apps | Microsoft Docs
description: V tomto kurzu se dozvíte, jak s Azure Logic Apps, Azure Storage a Azure Functions vytvářet automatizované pracovní postupy pro zpracování e-mailů a příloh
author: ecfan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 3d6d66dca06c1f34a31155a27c32bbe3e48c8aa3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300629"
---
# <a name="process-emails-and-attachments-with-a-logic-app"></a>Zpracování e-mailů a příloh s využitím aplikace logiky

Služba Azure Logic Apps pomáhá automatizovat pracovní postupy a integrovat data napříč službami Azure, službami Microsoftu a dalšími aplikacemi SaaS (software jako služba) a místními systémy. Tento kurz ukazuje postupy při vytváření [aplikace logiky](../logic-apps/logic-apps-overview.md), která bude zpracovávat příchozí e-maily včetně případných příloh. Hotová aplikace zpracuje obsah e-mailů, uloží ho do úložiště Azure a odešle lidem oznámení, aby si obsah prohlédli. 

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

Pokud ještě nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a> před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

* E-mailový účet od poskytovatele podporovaného v Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](https://docs.microsoft.com/connectors/).

  Tato aplikace logiky používá účet Office 365 Outlook. 
  Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak.

* Zdarma stáhnout a nainstalovat <a href="http://storageexplorer.com/" target="_blank">Průzkumníka služby Microsoft Azure Storage</a>. Tento nástroj vám umožní zkontrolovat, jestli máte kontejner úložiště správně nastavený.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

## <a name="set-up-storage-to-save-attachments"></a>Nastavení úložiště pro ukládání příloh

Příchozí e-maily a přílohy můžete ukládat jako objekty blob v [kontejneru úložiště Azure](../storage/common/storage-introduction.md). 

1. Než vytvoříte kontejner úložiště, [vytvořte účet úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account). Použijte při tom tato nastavení:

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název** | attachmentstorageacct | Název účtu úložiště | 
   | **Model nasazení** | Resource Manager | [Model nasazení](../azure-resource-manager/resource-manager-deployment-model.md) pro správu nasazení prostředků | 
   | **Druh účtu** | Obecné účely | [Typ účtu úložiště](../storage/common/storage-introduction.md#types-of-storage-accounts) | 
   | **Výkon** | Standard | Toto nastavení specifikuje podporované datové typy a média pro ukládání dat. Další informace najdete v tématu [Typy účtů úložiště](../storage/common/storage-introduction.md#types-of-storage-accounts). | 
   | **Replikace** | Místně redundantní úložiště (LRS) | Toto nastavení určuje, jak se budou kopírovat, ukládat, spravovat a synchronizovat data. Další informace najdete v tématu [Replikace](../storage/common/storage-introduction.md#replication). | 
   | **Vyžádání bezpečného přenosu** | Zakázáno | Toto nastavení určuje zabezpečení vyžadované u požadavků směřujících od připojení. Další informace najdete v tématu [Vyžádání bezpečného přenosu](../storage/common/storage-require-secure-transfer.md). | 
   | **Předplatné** | <*název_vašeho_předplatného_Azure*> | Název vašeho předplatného Azure | 
   | **Skupina prostředků** | LA-Tutorial-RG | Název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md), který slouží k uspořádání a správě souvisejících prostředků <p>**Poznámka:** Skupina prostředků existuje v konkrétní oblasti. Položky z tohoto kurzu nemusí být k dispozici ve všech oblastech, snažte se nicméně používat stejnou oblast, kdykoli je to možné. | 
   | **Umístění** | Východní USA 2 | Oblast, kam se mají ukládat informace o vašem účtu úložiště | 
   | **Konfigurace virtuálních sítí** | Zakázáno | V tomto kurzu ponechejte nastavení **Zakázáno**. | 
   |||| 

   Můžete použít také [prostředí Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) nebo [rozhraní příkazového řádku Azure](../storage/common/storage-quickstart-create-storage-account-cli.md).
  
2. Jakmile se v Azure nasadí váš účet úložiště, je potřeba získat k němu přístupový klíč:

   1. V části **Nastavení** v nabídce účtu úložiště vyberte **Přístupové klíče**. 
   2. V části **Výchozí klíče** najděte klíč **key1** a název vašeho účtu úložiště.

      ![Zkopírování a uložení názvu a klíče účtu úložiště](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Můžete použít také [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az_storage_account_keys_list). 

3. Vytvořte kontejner úložiště pro přílohy e-mailů.
   
   1. V nabídce účtu úložiště v podokně **Přehled** vyberte v části **Služby** položku **Objekty blob** a potom zvolte **+ Kontejner**.

   2. Jako název kontejneru zadejte „přílohy“. V části **Úroveň veřejného přístupu** vyberte **Kontejner (anonymní přístup pro čtení ke kontejnerům a objektům blob)** a potvrďte kliknutím na **OK**.

   Můžete použít také [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create). 
   Jakmile budete hotovi, objeví se kontejner úložiště ve vašem účtu úložiště tady na portálu Azure Portal:

   ![Hotový kontejner úložiště](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

Dalším krokem je propojení Průzkumníka služby Storage s vaším účtem.

## <a name="set-up-storage-explorer"></a>Nastavení Průzkumníka služby Storage

Teď se svým účtem úložiště propojte Průzkumníka služby Storage. Budete tak moct ověřit, že aplikace logiky do kontejneru úložiště správně ukládá přílohy jako objekty blob.

1. Spusťte Průzkumníka služby Microsoft Azure Storage. Jakmile se v Průzkumníkovi zobrazí výzva k zadání připojení k úložišti Azure, zvolte **Použít název a klíč účtu úložiště** > **Další**.
Pokud se výzva nezobrazí, zvolte na panelu nástrojů Průzkumníka **Přidat účet**.

2. V části **Připojit s použitím názvu a klíče** zadejte název účtu a přístupový klíč úložiště, které jste si předtím uložili. Vyberte **Další** > **Připojit**.

3. Zkontrolujte, jestli se účet a kontejner úložiště v Průzkumníkovi služby Storage zobrazují správně:

   1. V podokně **Průzkumník** rozbalte nabídku **(Místní a připojené)** > 
   **Účty úložiště** > **attachmentstorageaccount** > 
   **Kontejnery objektů blob**.

   2. Ověřte, že se tady zobrazuje kontejner „přílohy“. 
   Příklad:

      ![Průzkumník služby Storage – ověření kontejneru úložiště](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

V dalším kroku vytvořte [funkci Azure](../azure-functions/functions-overview.md), která z příchozích e-mailů odebere kódování HTML.

## <a name="create-a-function-to-clean-html"></a>Vytvoření funkce k vyčištění kódování HTML

Teď pomocí připraveného fragmentu kódu a následujícího postupu vytvořte funkci Azure, která z každého příchozího e-mailu odebere kódování HTML. Obsah e-mailů se tak vyčistí a jeho zpracování bude jednodušší. Tuto funkci pak můžete volat z aplikace logiky.

1. Než funkci vytvoříte, nejdřív [vytvořte aplikaci funkcí](../azure-functions/functions-create-function-app-portal.md) s těmito nastaveními:

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název aplikace** | CleanTextFunctionApp | Globálně jedinečný a popisný název vaší aplikace funkcí | 
   | **Předplatné** | <*název_vašeho_předplatného_Azure*> | Stejné předplatné Azure, jaké jste používali dříve | 
   | **Skupina prostředků** | LA-Tutorial-RG | Stejná skupina prostředků Azure, jakou jste používali dříve | 
   | **Plán hostování** | Plán Consumption | Toto nastavení určuje, jak se při běhu aplikace funkcí mají přidělovat a škálovat prostředky, například výpočetní výkon. Podívejte se na [porovnání plánů hostování](../azure-functions/functions-scale.md). | 
   | **Umístění** | Východní USA 2 | Stejná oblast, jakou jste používali dříve | 
   | **Úložiště** | cleantextfunctionstorageacct | Vytvořte pro svou aplikaci funkcí účet úložiště. Použijte při tom jenom malá písmena a číslice. <p>**Poznámka:** Tento účet úložiště obsahuje vaše aplikace funkcí a je jiný, než účet úložiště pro přílohy e-mailů, který jste vytvořili předtím. | 
   | **Application Insights** | Vypnuto | Zapne monitorování aplikací pomocí [Application Insights](../application-insights/app-insights-overview.md). Pro účely tohoto kurzu ale ponechejte nastavení **Vypnuto**. | 
   |||| 

   Pokud se aplikace funkcí po nasazení automaticky nespustí, vyhledejte ji na portálu <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. V hlavní nabídce Azure zvolte **App Services**a vyberte svou aplikaci funkcí.

   ![Vytvořená aplikace funkcí](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Pokud se položka **App Services** v nabídce Azure nezobrazuje, přejděte místo toho na **Další služby**. Ve vyhledávacím poli najděte a vyberte **Aplikace funkcí**. Další informace najdete v článku o [vytváření funkcí](../azure-functions/functions-create-first-azure-function.md).

   Můžete také použít [rozhraní příkazového řádku Azure](../azure-functions/functions-create-first-azure-function-azure-cli.md) nebo [šablony prostředí PowerShell a Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md).

2. V části **Aplikace funkcí** rozbalte položku **CleanTextFunctionApp**a vyberte **Funkce**. Na panelu nástrojů funkcí zvolte **+ Nová funkce**.

   ![Vytvoření nové funkce](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. V části **Zvolte šablonu níže nebo přejděte na rychlý start** vyberte šablonu funkce **HttpTrigger – C#**.

   ![Výběr šablony funkce](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

4. V části **Pojmenujte svoji funkci** zadejte ```RemoveHTMLFunction```. V části **Trigger HTTP** > **Úroveň autorizace** ponechejte výchozí hodnotu **Funkce** a vyberte **Vytvořit**.

   ![Pojmenování funkce](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. Jakmile se otevře editor, nahraďte kód šablony následujícím kódem, který odebere kódování HTML a vrátí výsledky volající funkci:

   ``` CSharp
   using System.Net;
   using System.Text.RegularExpressions;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await req.Content.ReadAsStringAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return req.CreateResponse(HttpStatusCode.OK, new { updatedBody });

   }
   ```

6. Jakmile budete hotoví, vyberte **Uložit**. Pokud chcete funkci otestovat, zvolte na pravé straně editoru pod ikonou šipky (**<**) **Test**. 

   ![Otevření testovacího podokna](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. V podokně **Test** do části **Tělo požadavku** zadejte následující řádek a vyberte **Spustit**.

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![Otestování funkce](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   V okně **Výstup** se zobrazí tento výsledek funkce:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Když zkontrolujete, že funkce pracuje správně, vytvořte aplikaci logiky. Tento kurz vysvětluje postup vytvoření funkce, která z e-mailů odebere kódování HTML. Služba Logic Apps ale obsahuje také konektor **HTML na text**.

## <a name="create-your-logic-app"></a>Vytvoření aplikace logiky

1. V hlavní nabídce Azure zvolte **Vytvořit prostředek** > **Podniková integrace** > **Aplikace logiky**.

   ![Vytvoření aplikace logiky](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. V části **Vytvořit aplikaci logiky** zadejte podrobnosti o vaší aplikaci logiky podle následujícího obrázku a popisu. Až budete hotovi, zvolte **Připnout na řídicí panel** > **Vytvořit**.

   ![Zadání informací o aplikaci logiky](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název** | LA-ProcessAttachment | Název vaší aplikace logiky | 
   | **Předplatné** | <*název_vašeho_předplatného_Azure*> | Stejné předplatné Azure, jaké jste používali dříve | 
   | **Skupina prostředků** | LA-Tutorial-RG | Stejná skupina prostředků Azure, jakou jste používali dříve |
   | **Umístění** | Východní USA 2 | Stejná oblast, jakou jste používali dříve | 
   | **Log Analytics** | Vypnuto | V tomto kurzu ponechejte nastavení **Vypnuto**. | 
   |||| 

3. Jakmile se vaše aplikace v Azure nasadí, otevře se Návrhář pro Logic Apps se zobrazenou stránkou s úvodním videem a šablonami pro běžně používané vzory aplikací logiky. V části **Šablony** zvolte **Prázdná aplikace logiky**.

   ![Výběr šablony prázdné aplikace logiky](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Teď přidejte [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), který naslouchá příchozím e-mailům s přílohami. Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo když nová data splní určitou podmínku. Další informace najdete v článku [Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Monitorování příchozích e-mailů

1. V návrháři zadejte do vyhledávacího pole „při přijetí e-mailu“. Vyberte tento trigger pro svého poskytovatele e-mailu: **<*Váš-poskytovatel-e-mailu*> – při přijetí nového e-mailu**, například:

   ![Výběr triggeru pro konkrétního poskytovatele e-mailu: „Při přijetí nového e-mailu“](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook. 
   * Pro osobní účty Microsoft vyberte Outlook.com. 

2. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se ke svému e-mailovému účtu, aby se s ním v Logic Apps mohlo vytvořit propojení.

3. Teď zadejte kritéria, která trigger použije k filtrování nových e-mailů.

   1. Určete složku, interval a frekvenci kontroly e-mailů.

      ![Určení složky, intervalu a frekvence kontroly e-mailů](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Nastavení | Hodnota | Popis | 
      | ------- | ----- | ----------- | 
      | **Složka** | Doručená pošta | E-mailová složka, která se má kontrolovat | 
      | **Interval** | 1 | Počet intervalů, po které se má čekat mezi kontrolami | 
      | **Frekvence** | Minuta | Jednota času pro každý interval mezi kontrolami | 
      |  |  |  | 
  
   2. Zvolte **Zobrazit pokročilé možnosti** a zadejte tato nastavení:

      | Nastavení | Hodnota | Popis | 
      | ------- | ----- | ----------- | 
      | **Má přílohu** | Ano | Načte jen e-maily s přílohami. <p>**Poznámka:** Trigger neodebere z vašeho účtu žádné e-maily. Kontroluje jenom nové zprávy a zpracovává jenom e-maily odpovídající filtru pro předmět. | 
      | **Zahrnout přílohy** | Ano | Místo samotné kontroly příloh je načtěte jako vstup do pracovního postupu. | 
      | **Filtr předmětu** | ```Business Analyst 2 #423501``` | Text, který se má najít v předmětu e-mailu | 
      |  |  |  | 

4. Pokud chcete podrobnosti o triggeru prozatím skrýt, klikněte do jeho záhlaví.

   ![Tvar sbalení pro skrytí podrobností](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. Uložte svou aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

   Vaše aplikace logiky je teď v provozu, ale kromě kontroly e-mailů nic dalšího nedělá. 
   Teď přidejte podmínku, která určí kritéria pro pokračování pracovního postupu.

## <a name="check-for-attachments"></a>Kontrola příloh

1. U triggeru vyberte **+ Nový krok** > **Přidat podmínku**.

   Jakmile se zobrazí obrazec podmínky, zobrazí se ve výchozím nastavení buď seznam parametrů, nebo seznam dynamického obsahu se všemi parametry z předchozího kroku, které můžete použít jako vstupy pracovního postupu. 
   Šířka vašeho prohlížeče určuje, který seznam se zobrazí.

2. Přejmenujte podmínku tak, aby její popis lépe vystihoval účel.

   1. V záhlaví podmínky zvolte tlačítko **s třemi tečkami** (**…**) > **Přejmenovat**.

      Příklad s úzkým zobrazením prohlížeče:

      ![Přejmenování podmínky](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

      Pokud je prohlížeč v širokém zobrazení a seznam dynamického obsahu blokuje přístup k tlačítku se třemi tečkami, zavřete seznam tak, že v podmínce zvolíte **Přidat dynamický obsah**. 
      
      ![Zavření seznamu dynamického obsahu](./media/tutorial-process-email-attachments-workflow/close-dynamic-content-list.png)

   2. Přejmenujte podmínku s použitím tohoto popisu: ```If email has attachments and key subject phrase```

3. Popište podmínku zadáním výrazu. 

   1. Uvnitř obrazce podmínky zvolte **Upravit v rozšířeném režimu**.

      ![Úprava podmínky v rozšířeném režimu](./media/tutorial-process-email-attachments-workflow/edit-advanced-mode.png)

   2. Do textového pole zadejte tento výraz:

      ```@equals(triggerBody()?['HasAttachment'], bool('true'))```

      Tento výraz porovnává hodnotu vlastnosti **HasAttachment** z textu triggeru, což je v tomto kurzu e-mail, s booleovským objektem ```True```. 
      Pokud se obě hodnoty shodují, má e-mail aspoň jednu přílohu, podmínka je splněna a pracovní postup pokračuje.

      Vaše podmínka teď vypadá jako v tomto příkladu:

      ![Výraz podmínky](./media/tutorial-process-email-attachments-workflow/condition-expression.png)

   3. Zvolte **Upravit v základním režimu**. Výraz se teď vyřeší, jak je vidět na dalším obrázku:

      ![Vyřešený výraz](./media/tutorial-process-email-attachments-workflow/condition-expression-resolved.png)

      > [!NOTE]
      > Pokud chcete výraz sestavit ručně, musíte pracovat v základním režimu a mít otevřený seznam dynamického obsahu, abyste mohli využít tvůrce výrazů. V části **Výraz** můžete vybrat funkce. V části **Dynamický obsah** můžete vybrat pole parametrů, která se u těchto funkcí mají použít.
      > Ruční tvorbu výrazů si ukážeme dále v tomto kurzu.

4. Uložte svou aplikaci logiky.

### <a name="test-your-condition"></a>Otestování podmínky

Teď otestujte, jestli podmínka správně funguje:

1. Pokud aplikace logiky zatím neběží, na panelu nástrojů návrháře vyberte **Spustit**.

   Tímto krokem ručně spustíte aplikaci logiky a nemusíte čekat na uplynutí zadaného intervalu. 
   Dokud ale do schránky nedorazí testovací e-mail, nic se nestane. 

2. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět e-mailu obsahuje text, který jste v triggeru zadali ve **filtru předmětu**: ```Business Analyst 2 #423501```

   * E-mail obsahuje jednu přílohu. 
   Prozatím jednoduše vytvořte prázdný textový soubor a připojte ho k e-mailu.

   Jakmile e-mail dorazí, zkontroluje v něm aplikace logiky přílohy a zadaný text předmětu.
   Pokud bude podmínka splněna, trigger se aktivuje a zajistí, aby modul Logic Apps vytvořil instanci aplikace logiky a zahájil pracovní postup. 

3. Pokud se trigger aktivoval a aplikace logiky se úspěšně spustila, můžete zkontrolovat tak, že v nabídce aplikace logiky vyberete **Přehled**.

   ![Kontrola historie triggeru a spuštění](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Pokud se aplikace logiky i přes úspěšnou aktivaci triggeru neaktivovala nebo nespustila, podívejte se do článku [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

V dalším kroku definujte akce, které mají proběhnout ve větvi **Pokud je true**. Pokud chcete e-mail uložit spolu s přílohami, odeberte z jeho těla veškeré kódování HTML a potom v kontejneru úložiště vytvořte objekty blob pro e-maily a přílohy.

> [!NOTE]
> Ve větvi **Pokud je false**, když e-mail nemá přílohy, se od aplikace logiky nevyžadují žádné kroky. Jako bonusové cvičení můžete po dokončení tohoto kurzu přidat do větve **Pokud je false** případné akce, které byste chtěli provést.

## <a name="call-the-removehtmlfunction"></a>Volání funkce RemoveHTMLFunction

1. V nabídce aplikace logiky zvolte **Návrhář aplikace logiky**. Ve větvi **Pokud je true** vyberte **Přidat akci**.

2. Vyhledejte „funkce azure“ a vyberte tuto akci: **Azure Functions – Zvolte funkci Azure**

   ![Výběr akce pro „Azure Functions – Zvolte funkci Azure“](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. Vyberte aplikaci funkcí, kterou jste předtím vytvořili: **CleanTextFunctionApp**

   ![Výběr aplikace funkcí Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. Teď vyberte funkci: **RemoveHTMLFunction**

   ![Výběr funkce Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. Přejmenujte obrazec funkce s použitím tohoto popisu: ```Call RemoveHTMLFunction to clean email body``` 

6. V obrazci funkce zadejte vstup, který má funkce zpracovat. Zadejte text e-mailu, jak je vidět na obrázku:

   ![Určení, jaké tělo požadavku má funkce očekávat](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   1. V části **Tělo požadavku** zadejte následující text: 
   
      ```{ "emailBody": ``` 

      Zobrazí se chyba varující před neplatným identifikátorem JSON, která zmizí, jakmile tento záznam podle dalšího postupu dokončíte.
      Pokud jste tuto funkci už testovali, použil se u zadávaného vstupu formát JSON (JavaScript Object Notation). 
      V těle požadavku je proto nutné použít stejný formát. 

   2. Ze seznamu parametrů nebo dynamického obsahu vyberte v části **Při přijetí nového e-mailu** pole **Text**.
   Za pole **Text** doplňte uzavírací složenou závorku: ```}```

      ![Určení těla požadavku k předání do funkce](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing2.png)

      V definici aplikace logiky se tento záznam zobrazí v následujícím formátu:

      ```{ "emailBody": "@triggerBody()?['Body']" }```

7. Uložte svou aplikaci logiky.

V dalším kroku přidejte akci, která vytvoří v kontejneru úložiště objekt blob pro uložení těla e-mailu.

## <a name="create-blob-for-email-body"></a>Vytvoření objektu blob pro tělo e-mailu

1. V obrazci funkce Azure zvolte **Přidat akci**. 

2. V části **Vybrat akci** vyhledejte „blob“ a vyberte tuto akci: **Azure Blob Storage – Vytvořit objekt blob**

   ![Přidání akce pro vytvoření objektu blob pro tělo e-mailu](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. Pokud nemáte propojení s účtem úložiště Azure, vytvořte propojení svého účtu úložiště s nastaveními uvedenými na následujícím obrázku. Jakmile budete hotoví, vyberte **Vytvořit**.

   ![Vytvoření propojení s účtem úložiště](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Název připojení** | AttachmentStorageConnection | Popisný název propojení | 
   | **Účet úložiště** | attachmentstorageacct | Název účtu úložiště, který jste dříve vytvořili pro ukládání příloh | 
   |||| 

4. Přejmenujte akci **Vytvořit objekt blob** s použitím tohoto popisu: ```Create blob for email body```

5. U akce **Vytvořit objekt blob** zadejte informace a vyberte parametry k vytvoření objektu blob, jak je vidět tady:

   ![Zadání informací o objektu blob pro tělo e-mailu](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Cesta ke složce** | /přílohy | Název kontejneru, který jste dříve vytvořili, a cesta k němu. Můžete také procházet adresáře a vybrat požadovaný kontejner. | 
   | **Název objektu blob** | Pole **Od** | Jako název objektu blob se předá jméno odesílatele e-mailu. Ze seznamu parametrů nebo dynamického obsahu vyberte v části **Při přijetí nového e-mailu** pole **Od**. | 
   | **Obsah objektu blob** | Pole **Obsah** | Jako obsah objektu blob se předá tělo e-mailu bez kódování HTML. Ze seznamu parametrů nebo dynamického obsahu vyberte v části **Volat funkci RemoveHTMLFunction k vymazání textu e-mailu** pole **Text**. |
   |||| 

6. Uložte svou aplikaci logiky. 

### <a name="check-attachment-handling"></a>Kontrola zpracování příloh

Teď otestujte, jestli aplikace logiky zpracovává e-maily tak, jak jste zamýšleli:

1. Pokud aplikace logiky zatím neběží, na panelu nástrojů návrháře vyberte **Spustit**.

2. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět e-mailu obsahuje text, který jste v triggeru zadali ve **filtru předmětu**: ```Business Analyst 2 #423501```

   * E-mail obsahuje aspoň jednu přílohu. 
   Prozatím jednoduše vytvořte prázdný textový soubor a připojte ho k e-mailu.

   * E-mail má v těle nějaký zkušební obsah, třeba: 

     ```
     Testing my logic app
     ```

   Pokud se aplikace logiky i přes úspěšnou aktivaci triggeru neaktivovala nebo nespustila, podívejte se do článku [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

3. Zkontrolujte, jestli aplikace logiky uložila e-mail do správného kontejneru úložiště. 

   1. V Průzkumníkovi služby Storage rozbalte nabídku **(Místní a připojené)** > 
   **Účty úložiště** > **attachmentstorageacct (Externí)** > 
   **Kontejnery objektů blob** > **přílohy**.

   2. Vyhledejte testovací e-mail v kontejneru **přílohy**. 

      V této chvíli se v kontejneru zobrazuje jenom e-mail, protože aplikace logiky zatím nezpracovává přílohy.

      ![Vyhledání uloženého e-mailu v Průzkumníkovi služby Storage](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. Až budete hotovi, e-mail z Průzkumníka služby Storage odstraňte.

4. Pokud chcete otestovat také větev **Pokud je false**, která momentálně nic nedělá, pošlete sami sobě e-mail nesplňující kritéria funkce.

Teď přidejte smyčku, která zajistí zpracování veškerých příloh e-mailu.

## <a name="process-attachments"></a>Zpracování příloh

Tato aplikace logiky používá smyčku **pro každý**, která zajistí zpracování všech příloh e-mailu.

1. V obrazci **Vytvořit objekt blob pro tělo e-mailu** vyberte **… Další**a vyberte tento příkaz: **Přidat příkaz „pro každý“**.

   ![Přidání smyčky „pro každý“](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. Přejmenujte smyčku s použitím tohoto popisu: ```For each email attachment```

3. Teď zadejte data, která má smyčka zpracovávat. Klikněte do pole **Vybrat výstup z předchozího postupu**. V seznamu parametrů nebo dynamického obsahu vyberte **Přílohy**. 

   ![Výběr možnosti „Přílohy“](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Pole **Přílohy** předává pole hodnot obsahující všechny přílohy zahrnuté v e-mailu. 
   Smyčka typu **pro každý** opakuje akce pro každou položku, která se pomocí pole předá.

4. Uložte svou aplikaci logiky.

V dalším kroku přidejte akci, která uloží každou přílohu jako objekt blob do kontejneru úložiště **přílohy**.

## <a name="create-blobs-for-attachments"></a>Vytvoření objektů blob pro přílohy

1. Ve smyčce **pro každý** zvolte **Přidat akci** a zadejte úlohu, která se má provést u každé nalezené přílohy.

   ![Přidání akce do smyčky](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. V části **Vybrat akci** vyhledejte „blob“ a vyberte tuto akci: **Azure Blob Storage – Vytvořit objekt blob**

   ![Přidání akce pro vytvoření objektu blob](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. Přejmenujte akci **Vytvořit objekt blob 2** s použitím tohoto popisu: ```Create blob for each email attachment```

4. U akce **Vytvořit objekt blob pro každou přílohu e-mailu** zadejte informace a vyberte parametry k vytvoření jednotlivých objektů blob, jak je vidět tady:

   ![Zadání informací o objektech blob](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Nastavení | Hodnota | Popis | 
   | ------- | ----- | ----------- | 
   | **Cesta ke složce** | /přílohy | Název kontejneru, který jste dříve vytvořili, a cesta k němu. Můžete také procházet adresáře a vybrat požadovaný kontejner. | 
   | **Název objektu blob** | Pole **Název** | V seznamu parametrů nebo dynamického obsahu vyberte **Název** a zajistěte tak předání názvu přílohy pro název objektu blob. | 
   | **Obsah objektu blob** | Pole **Obsah** | V seznamu parametrů nebo dynamického obsahu vyberte **Obsah** a zajistěte tak předání obsahu přílohy pro obsah objektu blob. |
   |||| 

5. Uložte svou aplikaci logiky. 

### <a name="check-attachment-handling"></a>Kontrola zpracování příloh

Teď otestujte, jestli aplikace logiky zpracovává e-přílohy tak, jak jste zamýšleli:

1. Pokud aplikace logiky zatím neběží, na panelu nástrojů návrháře vyberte **Spustit**.

2. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět e-mailu obsahuje text, který jste v triggeru zadali ve **filtru předmětu**: ```Business Analyst 2 #423501```

   * E-mail obsahuje aspoň dvě přílohy. 
   Prozatím jednoduše vytvořte dva prázdné textové soubory a připojte je k e-mailu.

   Pokud se aplikace logiky i přes úspěšnou aktivaci triggeru neaktivovala nebo nespustila, podívejte se do článku [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

3. Zkontrolujte, jestli aplikace logiky uložila e-mail a přílohy do správného kontejneru úložiště. 

   1. V Průzkumníkovi služby Storage rozbalte nabídku **(Místní a připojené)** > 
   **Účty úložiště** > **attachmentstorageacct (Externí)** > 
   **Kontejnery objektů blob** > **přílohy**.

   2. Zkontrolujte, jestli se do kontejneru **přílohy** uložil e-mail i přílohy.

      ![Vyhledání uloženého e-mailu a příloh](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. Až budete hotovi, e-mail a přílohy z Průzkumníka služby Storage odstraňte.

V dalším kroku přidejte akci, která zajistí, aby aplikace logiky odeslala e-mail ke kontrole příloh.

## <a name="send-email-notifications"></a>Odeslání e-mailových oznámení

1. Ve větvi **Pokud je true** ve smyčce **Pro každou přílohu e-mailu** vyberte **Přidat akci**. 

   ![Přidání akce do smyčky „pro každý“](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. V části **Zvolit akci** vyhledejte „odeslat e-mail“ a pak vyberte akci „odeslat e-mail“ pro požadovaného poskytovatele e-mailu. Pokud chcete v seznamu akcí vyfiltrovat konkrétní službu, můžete nejprve vybrat konektor v části **Konektory**.

   ![Výběr akce „odeslat e-mail“ pro vašeho poskytovatele e-mailu](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Pro pracovní nebo školní účty Azure vyberte Office 365 Outlook. 
   * Pro osobní účty Microsoft vyberte Outlook.com. 

3. Pokud se zobrazí výzva k zadání přihlašovacích údajů, přihlaste se ke svému e-mailovému účtu a Logic Apps vytvoří připojení k vašemu e-mailovému účtu.

4. Přejmenujte akci **Odeslat e-mail** s použitím tohoto popisu: ```Send email for review```

5. Zadejte potřebné informace pro tuto akci a vyberte pole, která chcete do e-mailu zahrnout, jak je vidět tady. Pokud chcete do textového pole přidat prázdné řádky, stiskněte Shift + Enter.  

   Příklad se seznamem dynamického obsahu:

   ![Odeslání e-mailového oznámení](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Pokud v seznamu nemůžete najít očekávané pole, vyberte v seznamu dynamického obsahu nebo na konci seznamu parametrů vedle položky **Při přijetí nového e-mailu** možnost **Zobrazit více**.

   | Nastavení | Hodnota | Poznámky | 
   | ------- | ----- | ----- | 
   | **Komu** | <*recipient-email-address*> | Pro účely testování můžete použít svou vlastní e-mailovou adresu. | 
   | **Předmět**  | ```ASAP - Review applicant for position: ``` **Předmět** | Předmět e-mailu, který chcete zahrnout. Ze seznamu parametrů nebo dynamického obsahu vyberte v části **Při přijetí nového e-mailu** pole **Předmět**. | 
   | **Text** | ```Please review new applicant:``` <p>```Applicant name: ``` **Od** <p>```Application file location: ``` **Cesta** <p>```Application email content: ``` **Text** | Obsah hlavní části e-mailu. V seznamu parametrů nebo dynamického obsahu vyberte následující pole: <p>- Pole **Od** v části **Při přijetí nového e-mailu** </br>- Pole **Cesta** v části **Vytvořit objekt blob pro tělo e-mailu** </br>- Pole **Text** v části **Volat funkci RemoveHTMLFunction k vymazání textu e-mailu** | 
   |||| 

   Pokud vyberete pole obsahující pole hodnot, například **Obsah**, což je pole obsahující přílohy, návrhář kolem akce odkazující na toto pole automaticky přidá smyčku typu Pro každý. 
   Aplikace logiky tak může provést příslušnou akci pro každou položku pole. 
   Pokud chcete smyčku odebrat, odeberte pole pro dané pole hodnot, přesuňte odkazující akci mimo smyčku, vyberte v záhlaví smyčky symbol tří teček (**…**) a vyberte **Odstranit**.
     
6. Uložte svou aplikaci logiky. 

V dalším kroku otestujte aplikaci logiky, která teď vypadá podobně jako v tomto příkladu:

![Hotová aplikace logiky](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Spuštění aplikace logiky

1. Pošlete sami sobě e-mail splňující tato kritéria:

   * Předmět e-mailu obsahuje text, který jste v triggeru zadali ve **filtru předmětu**: ```Business Analyst 2 #423501```

   * E-mail obsahuje jednu nebo více příloh. 
   Můžete znovu použít prázdný textový soubor z předchozího testu. 
   Pokud chcete vyzkoušet realističtější scénář, připojte soubor s životopisem.

   * Tělo e-mailu obsahuje následující text, který můžete zkopírovat a vložit:

     ```
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

2. Spusťte svou aplikaci logiky. Pokud všechno proběhne úspěšně, z aplikace logiky vám přijde e-mail, který bude vypadat přibližně takto:

   ![E-mailové oznámení odeslané aplikací logiky](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanou poštou. 
   Váš filtr nevyžádané pošty může tento typ e-mailů přesměrovávat. 
   Pokud si nejste jisti správným spuštěním aplikace logiky, přečtěte si téma [Řešení potíží s aplikací logiky](../logic-apps/logic-apps-diagnosing-failures.md).

Blahopřejeme, právě jste vytvořili a spustili aplikaci logiky, která automatizuje úlohy v různých službách Azure a volá vlastní kód.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky, a všechny související prostředky. V hlavní nabídce Azure přejděte na **Skupiny prostředků** a vyberte skupinu prostředků pro vaši aplikaci logiky. Zvolte **Odstranit skupinu prostředků**. Pro ověření zadejte název skupiny prostředků a zvolte **Odstranit**.

![Odstranění skupiny prostředků aplikace logiky](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci logiky, která zpracovává a ukládá e-mailové přílohy díky integraci služeb Azure, mimo jiné služeb Azure Storage a Azure Functions. Teď doporučujeme blíže se seznámit s dalšími konektory, které se dají používat k vytváření aplikací logiky.

> [!div class="nextstepaction"]
> [Další informace o konektorech pro Logic Apps](../connectors/apis-list.md)

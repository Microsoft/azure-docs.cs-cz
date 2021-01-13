---
title: Přidat a spustit fragmenty kódu pomocí vloženého kódu
description: Naučte se vytvářet a spouštět fragmenty kódu pomocí akcí vloženého kódu pro automatizované úlohy a pracovní postupy, které vytvoříte pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 12/07/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3f88fa38d62778bc3c4c1e29571d1d0ae4eeb5ff
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179601"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Přidat a spustit fragmenty kódu pomocí vloženého kódu v Azure Logic Apps

Pokud chcete v aplikaci logiky spustit část kódu, můžete do pracovního postupu aplikace logiky přidat integrovanou akci vloženého kódu jako krok. Tato akce funguje nejlépe, když chcete spustit kód, který odpovídá tomuto scénáři:

* Běží v JavaScriptu. Další jazyky již brzy budou dostupné.

* Dokončeno po dobu 5 sekund nebo méně.

* Zpracovává data o velikosti až 50 MB.

* Nevyžaduje práci s [akcemi **proměnných**](../logic-apps/logic-apps-create-variables-store-values.md), které ještě nejsou podporované.

* Používá Node.js 8.11.1 verze. Další informace najdete v tématu [standardní předdefinované objekty](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

  > [!NOTE]
  > `require()`Funkce není podporována vloženou akcí kódu pro spuštění JavaScriptu.

Tato akce spustí fragment kódu a vrátí výstup z tohoto fragmentu kódu jako token s názvem `Result` . Pomocí tohoto tokenu můžete v pracovním postupu aplikace logiky použít následující akce. Pro jiné scénáře, kde chcete vytvořit funkci pro váš kód, zkuste vytvořit [a volat funkci prostřednictvím Azure Functions místo toho](../logic-apps/logic-apps-azure-functions.md) v aplikaci logiky.

V tomto článku se ukázková aplikace logiky aktivuje při přijetí nového e-mailu v pracovním nebo školním účtu. Fragment kódu extrahuje a vrátí všechny e-mailové adresy, které se zobrazí v těle e-mailu.

![Snímek obrazovky, který ukazuje ukázkovou aplikaci logiky](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, do které chcete přidat fragment kódu, včetně triggeru. Pokud nemáte aplikaci logiky, přečtěte si [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   V příkladu v tomto tématu se používá aktivační procedura Office 365 Outlook s názvem **při přijetí nového e-mailu**.

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který je propojený s vaší aplikací logiky.

  * Ujistěte se, že používáte účet pro integraci, který je vhodný pro váš případ použití nebo scénář.

    Například účty pro integraci na [volné](../logic-apps/logic-apps-pricing.md#integration-accounts) úrovni jsou určeny pouze pro průzkumné scénáře a úlohy, nikoli pro produkční scénáře, jsou omezeny využitím a propustností a nejsou podporovány smlouvou o úrovni služeb (SLA). Další úrovně se účtují náklady, ale zahrnují podporu smlouvy SLA, nabízí větší propustnost a má vyšší omezení. Přečtěte si další informace o [úrovních](../logic-apps/logic-apps-pricing.md#integration-accounts)účtu Integration, [cenách](https://azure.microsoft.com/pricing/details/logic-apps/)a [omezeních](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

   * Pokud nechcete používat účet pro integraci, můžete se pokusit použít [Azure Logic Apps Preview](logic-apps-overview-preview.md)a vytvořit aplikaci logiky z typu prostředku **Aplikace logiky (Preview)** .

     V Azure Logic Apps verze Preview se teď **vložený kód** nazývá **operace vloženého kódu** spolu s těmito dalšími rozdíly:

     * **Spustit kód JavaScriptu** se teď jmenuje **run in-line JavaScript**.

     * Pokud používáte macOS nebo Linux, vložené akce kódu nejsou aktuálně k dispozici, když používáte rozšíření Azure Logic Apps (Preview) v Visual Studio Code.

     * Vložené akce kódu mají [aktualizované limity](logic-apps-overview-preview.md#inline-code-limits).

     Můžete začít z obou možností:

     * Vytvořte aplikaci logiky z typu prostředku **Aplikace logiky (Preview)** [pomocí Azure Portal](create-stateful-stateless-workflows-azure-portal.md).

     * Vytvoření projektu pro aplikaci logiky [pomocí Visual Studio Code a rozšíření Azure Logic Apps (Preview)](create-stateful-stateless-workflows-visual-studio-code.md)

## <a name="add-inline-code"></a>Přidat vložený kód

1. Pokud jste to ještě neudělali, v [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V návrháři vyberte, kam chcete přidat akci vloženého kódu do pracovního postupu aplikace logiky.

   * Chcete-li přidat akci na konci pracovního postupu, vyberte možnost **Nový krok**.

   * Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku, která propojuje tyto kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a vyberte **přidat akci**.

   Tento příklad přidá akci vloženého kódu pod aktivační proceduru Office 365 Outlook.

   ![Přidání nového kroku do triggeru](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. V části **zvolit akci** zadejte do vyhledávacího pole `inline code` . V seznamu akce vyberte akci s názvem **Spustit kód JavaScriptu**.

   ![Vybrat akci spustit kód JavaScriptu](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Akce se zobrazí v návrháři a ve výchozím nastavení obsahuje ukázkový kód, včetně `return` příkazu.

   ![Akce vloženého kódu s výchozím ukázkovým kódem](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. V poli **kód** odstraňte ukázkový kód a zadejte kód. Napište kód, který byste umístili do metody, ale bez signatury metody.

   Pokud začnete psát rozpoznané klíčové slovo, zobrazí se seznam automatického dokončování, abyste mohli vybírat z dostupných klíčových slov, například:

   ![Seznam automatického dokončování klíčových slov](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Tento ukázkový fragment kódu nejprve vytvoří proměnnou, která ukládá *regulární výraz*, který určuje vzor odpovídající vstupnímu textu. Kód potom vytvoří proměnnou, která uloží data těla e-mailu z aktivační události.

   ![Vytvoření proměnných](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Chcete-li, aby se výsledky triggeru a předchozích akcí zjednodušily, zobrazí se seznam dynamického obsahu, když je kurzor uvnitř pole **kód** . V tomto příkladu se v seznamu zobrazí dostupné výsledky triggeru, včetně tokenu **textu** , který teď můžete vybrat.

   Po výběru tokenu **textu** se v vložené akci s kódem vyřeší token na `workflowContext` objekt, který odkazuje na hodnotu vlastnosti e-mailu `Body` :

   ![Vybrat výsledek](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   V poli **kód** váš fragment kódu může jako vstup použít objekt jen pro čtení `workflowContext` . Tento objekt obsahuje vlastnosti, které přidávají vašemu kódu přístup k výsledkům triggeru a předchozích akcím v pracovním postupu. Další informace najdete v části [referenční Trigger a výsledky akcí v kódu](#workflowcontext) dále v tomto tématu.

   > [!NOTE]
   > Pokud fragment kódu odkazuje na názvy akcí, které používají operátor tečka (.), je nutné přidat tyto názvy akcí do parametru [ **Actions**](#add-parameters). Tyto odkazy musí také uzavřít názvy akcí hranatými závorkami ([]) a uvozovkami, například:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Akce vloženého kódu nevyžaduje `return` příkaz, ale výsledky z `return` příkazu jsou k dispozici pro referenci v pozdějších akcích prostřednictvím **výsledného** tokenu. Například fragment kódu vrátí výsledek voláním `match()` funkce, která najde shody v těle e-mailu s regulárním výrazem. Akce **vytvořit** používá token **výsledku** k odkazování na výsledky z vložené akce kódu a vytvoří jeden výsledek.

   ![Hotová aplikace logiky](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Až budete hotovi, uložte aplikaci logiky.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Odkaz na Trigger a výsledky akce v kódu

`workflowContext`Objekt má tuto strukturu, která zahrnuje `actions` , `trigger` a `workflow` podvlastnosti:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Tato tabulka obsahuje další informace o těchto podvlastnostech:

| Vlastnost | Typ | Description |
|----------|------|-------|
| `actions` | Kolekce objektů | Objekty výsledků z akcí, které se spustí před spuštěním fragmentu kódu. Každý objekt má dvojici *klíč-hodnota* , kde klíč je název akce a hodnota je ekvivalentní volání [funkce Actions ()](../logic-apps/workflow-definition-language-functions-reference.md#actions) s `@actions('<action-name>')` . Název akce používá stejný název akce, který se používá v příslušné definici pracovního postupu, která nahradí mezery ("") v názvu akce podtržítkem (_). Tento objekt poskytuje přístup k hodnotám vlastností akce z aktuálního běhu instance pracovního postupu. |
| `trigger` | Objekt | Výsledný objekt z triggeru a ekvivalentní volání [funkce Trigger ()](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Tento objekt poskytuje přístup k aktivačním hodnotám vlastností z aktuálního běhu instance pracovního postupu. |
| `workflow` | Objekt | Objekt pracovního postupu a ekvivalentní volání [funkce workflow ()](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Tento objekt poskytuje přístup k hodnotám vlastností pracovního postupu, jako je název pracovního postupu, ID běhu a tak dále, z aktuálního spuštění instance pracovního postupu. |
|||

V tomto tématu `workflowContext` má objekt tyto vlastnosti, ke kterým může váš kód přistupovat:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Přidání parametrů

V některých případech může být nutné explicitně vyžadovat, aby akce vloženého kódu zahrnovala výsledky triggeru nebo konkrétní akce, které váš kód odkazuje jako závislosti, přidáním parametrů **Trigger** nebo **Actions** . Tato možnost je užitečná v případech, kdy se odkazované výsledky nenašly v době běhu.

> [!TIP]
> Pokud máte v úmyslu znovu použít svůj kód, přidejte odkazy na vlastnosti pomocí pole **kód** tak, aby váš kód zahrnoval odkazy na přeložené tokeny namísto přidání triggeru nebo akcí jako explicitní závislosti.

Předpokládejme například, že máte kód, který odkazuje na výsledek **SelectedOption** z akce **odeslání e-mailu o schválení** pro konektor Office 365 Outlook. V okamžiku vytvoření Logic Apps modul analyzuje váš kód, aby určil, zda jste se na výsledky triggeru nebo akce zakázali a aby tyto výsledky zahrnovaly automaticky. V době spuštění by se měla zobrazit chyba, že odkazovaný aktivační událost nebo výsledek akce není v zadaném objektu k dispozici `workflowContext` , můžete tuto aktivační událost nebo akci přidat jako explicitní závislost. V tomto příkladu přidáte parametr **Actions** a určíte, že akce vloženého kódu explicitně zahrne výsledek z akce **Odeslat e-mail o schválení** .

Chcete-li přidat tyto parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry:

   ![Přidání parametrů](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametr | Popis |
   |-----------|-------------|
   | **Akce** | Zahrnout výsledky z předchozích akcí Viz [Zahrnutí výsledků akcí](#action-results). |
   | **Trigger** | Zahrnout výsledky z triggeru Viz [Zahrnutí výsledků triggeru](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Zahrnout výsledky triggeru

Pokud vyberete **triggery**, zobrazí se dotaz, zda chcete zahrnout výsledky triggeru.

* V seznamu **Trigger** vyberte **Ano**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Zahrnout výsledky akce

Pokud vyberete možnost **Akce**, budete vyzváni k zadání akcí, které chcete přidat. Před zahájením přidávání akcí však potřebujete verzi názvu akce, která se zobrazí v základní definici pracovního postupu aplikace logiky.

* Tato funkce nepodporuje proměnné, smyčky a indexy iterace.

* Názvy v definici pracovního postupu vaší aplikace logiky používají podtržítko (_), ne mezeru.

* Pro názvy akcí, které používají operátor tečka (.), zahrňte tyto operátory, například:

  `My.Action.Name`

1. Na panelu nástrojů návrháře vyberte **zobrazení kódu** a vyhledejte v `actions` atributu název akce v atributu.

   Například `Send_approval_email_` je název JSON pro akci **odeslání e-mailu o schválení** .

   ![Najít název akce ve formátu JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Pokud se chcete vrátit do zobrazení návrháře, na panelu nástrojů zobrazení kódu vyberte **Návrhář**.

1. Chcete-li přidat první akci, v poli **Položka akce-1** zadejte název JSON akce.

   ![Zadat první akci](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Chcete-li přidat další akci, vyberte možnost **Přidat novou položku**.

## <a name="reference"></a>Referenční informace

Další informace o struktuře a syntaxi akce **Spustit kód JavaScriptu** v základní definici pracovního postupu aplikace logiky pomocí jazyka definice pracovního postupu najdete v [referenční části](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)této akce.

## <a name="next-steps"></a>Další kroky

Další informace o [konektorech pro Azure Logic Apps](../connectors/apis-list.md)

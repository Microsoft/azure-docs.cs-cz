---
title: Přidání a spuštění fragmenty kódu – Azure Logic Apps
description: Přidání a spuštění kódu pomocí vloženého kódu v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 9ef11eb2099ff617fb4da4b9a924dc3f0550f226
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160542"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Přidání a spuštění kódu pomocí vloženého kódu v Azure Logic Apps

Pokud chcete spustit kód ve svých aplikacích logiky, můžete přidat předdefinované **vloženého kódu** akce jako krok v pracovním postupu vaší aplikace logiky. Tato akce funguje nejlépe, když chcete spustit kód, který vyhovuje tento scénář:

* Spuštění v jazyce JavaScript. Připravujeme další jazyky.
* Dokončení běží na pět sekund nebo méně.
* Zpracovává data velikost až 50 MB.
* Node.js verze 8.11.1 používá. Další informace najdete v tématu [standardní předdefinované objekty](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

Tato akce spustí fragment kódu a vrátí její výstup z tohoto fragmentu jako token s názvem **výsledek**, který můžete použít v následných akcí ve vaší aplikaci logiky. Pro další scénáře, ve kterém chcete vytvořit funkci pro váš kód, zkuste [vytvořením a voláním funkce Azure](../logic-apps/logic-apps-azure-functions.md) ve vaší aplikaci logiky.

V tomto článku přijde triggery aplikace logiky příklad při přijetí nového účtu Office 365 Outlook. Fragment kódu extrahuje a vrátí všechny e-mailové adresy, které se zobrazí v e-mailu.

![Základní informace o ukázkovém](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, ve které chcete přidat fragment kódu, včetně aktivační události. Pokud nemáte aplikace logiky, přečtěte si téma [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Příklad aplikace logiky v tomto tématu se používá Tato aktivační událost Office 365 Outlook: **Při přijetí nového e-mailu**

* [Účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) připojený k vaší aplikaci logiky

## <a name="add-inline-code"></a>Přidání vloženého kódu

1. Pokud jste tak dosud neučinili, v [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

1. V návrháři, přidejte **vloženého kódu** akce na umístění, které chcete v pracovním postupu vaší aplikace logiky.

   * Chcete-li přidat akci na konci pracovního postupu, zvolte **nový krok**.

   * Přidání akce mezi stávající kroky, přesuňte ukazatel myši nad šipku, která se připojuje tyto kroky. Vyberte znaménko plus (**+**) a vyberte **přidat akci**.

   V tomto příkladu přidá **vloženého kódu** akce pod triggerem Office 365 Outlook.

   ![Přidat nový krok](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. V části **zvolte akci**, do vyhledávacího pole zadejte jako filtr "vloženého kódu". Ze seznamu akcí vyberte tuto akci: **Spuštění kódu jazyka JavaScript**

   ![Vyberte možnost "Spustit kód jazyka JavaScript."](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Akce se zobrazí v návrháři a obsahuje některé výchozí příklad kódu, včetně návratový příkaz.

   ![Vložený kód akce s výchozí ukázkový kód](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. V **kód** , odstraňte ukázkový kód a zadejte kód, který chcete spustit. Napište kód, který jste vložili uvnitř metody, ale bez definování podpisu metody. 

   Při zadávání rozpoznaný – klíčové slovo se objeví dokončování, takže můžete vybrat z dostupných klíčová slova, například:

   ![Dokončování – klíčové slovo](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Tento příklad fragment kódu nejprve vytvoří proměnnou, která ukládá *regulárního výrazu*, která určuje vzor, podle kterého se porovnává se vstupním textem. Kód poté vytvoří proměnnou, která ukládá data těla e-mailu z triggeru.

   ![Vytvoření proměnných](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Pro usnadnění výsledky z aktivační události a akce předchozí odkaz, zobrazí se seznam dynamického obsahu zatímco ukazatel myši nachází uvnitř **kód** pole. V seznamu se zobrazí dostupné výsledky z triggeru, včetně v tomto příkladu **tělo** token, který teď můžete vybrat.

   Po výběru **text** token, přeloží vložené akce kódu token, který má `workflowContext` , který odkazuje e-mailu `Body` hodnota vlastnosti:

   ![Vyberte výsledek](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   V **kód** pole fragment můžete použít jen pro čtení `workflowContext` objektu jako vstup. Tento objekt nemá podvlastnosti, které umožňují váš kód přístup k výsledky z aktivační události a předchozí akce ve svém pracovním postupu.
   Další informace najdete v této části dále v tomto tématu: [Aktivační událost a akce výsledky v kódu odkazovat](#workflowcontext).

   > [!NOTE]
   >
   > Pokud váš fragment kódu odkazuje na názvy akcí, které používají tečka (.), je nutné přidat tyto názvy akcí k [ **akce** parametr](#add-parameters). Tyto odkazy musí také uzavřít názvy akcí s hranaté závorky ([]) a uvozovky, například:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Nevyžaduje, aby akce vloženého kódu `return` příkazu, ale výsledky z `return` jsou k dispozici pro referenci v pozdějších akcích prostřednictvím **výsledek** token. 
   Například fragment kódu vrátí výsledek voláním `match()` funkce, které najde odpovídající v e-mailu s regulárním výrazem. **Compose** používá akce **výsledek** token k odkazu na výsledky z vložený kód akce a vytvoří jeden výsledek.

   ![Hotová aplikace logiky](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Jakmile budete hotovi, uložte svou aplikaci logiky.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Odkaz na trigger a akce výsledky v kódu

`workflowContext` Objekt má tuto strukturu, která zahrnuje `actions`, `trigger`, a `workflow` objektu třídy subproperties:

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

Tato tabulka obsahuje další informace o těchto objektu třídy subproperties:

| Vlastnost | Typ | Popis |
|----------|------|-------|
| `actions` | Kolekce objektů | Objekty výsledků z akcí, které spustit před spuštěním fragment kódu. Každý objekt má *klíč hodnota* pár, kde klíč je název akce a hodnota je ekvivalentní volání [actions() funkce](../logic-apps/workflow-definition-language-functions-reference.md#actions) s `@actions('<action-name>')`. Název akce používá stejný název akce, který se používá v základní definici pracovního postupu, který nahradí mezery ("") v názvu akce s podtržítka (_). Tento objekt umožňuje přístup k hodnoty vlastností akce z aktuální instance pracovního postupu spustit. |
| `trigger` | Object | Výsledný objekt z aktivační události a ekvivalentní volání [trigger() funkce](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Tento objekt umožňuje přístup k hodnoty vlastností aktivační události z aktuální instance pracovního postupu spustit. |
| `workflow` | Object | Objekt pracovního postupu a ekvivalentní volání [workflow() funkce](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Tento objekt umožňuje přístup k hodnoty vlastností pracovního postupu, jako je například název pracovního postupu, ID spuštění a tak dále, z aktuální instance pracovního postupu spustit. |
|||

V příkladu v tomto tématu `workflowContext` objekt má tyto vlastnosti, které váš kód může přistupovat:

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

V některých případech bude pravděpodobně nutné explicitně vyžadují, aby **vloženého kódu** akce obsahuje výsledky z aktivační události nebo konkrétní akce, které odkazuje kód jako závislosti tak, že přidáte **aktivační událost** nebo **Akce** parametry. Tato možnost je užitečná v případech, kde nejsou nalezeny odkazované výsledky v době běhu.

> [!TIP]
> Pokud máte v plánu pro opětovné použití kódu, přidejte odkazy na vlastnosti pomocí **kód** pole tak, aby váš kód obsahuje přeložit odkazy na token, místo přidávání triggeru nebo akce jako explicitní závislosti.

Předpokládejme například, že máte kód, který odkazuje **SelectedOption** výsledkem **odeslat schvalovací e-mail** akce pro konektor Office 365 Outlook. Při vytváření čas, modul Logic Apps provede analýzu vašeho kódu k určení, zda jste odkazovat všechny aktivační události nebo akce výsledky a obsahuje tyto výsledky automaticky. V době běhu, by se zobrazí chyba, že odkazované výsledek triggeru nebo akce není dostupná v zadaném `workflowContext` objektu, Tento aktivační události nebo akce můžete přidat jako explicitní závislosti. V tomto příkladu přidáte **akce** parametr a určit, že **vloženého kódu** akci explicitně zahrnout výsledek z **odeslat schvalovací e-mail** akce.

Chcete-li přidat tyto parametry, otevřete **přidat nový parametr** seznam a vyberte parametry chcete:

   ![Přidání parametrů](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametr | Popis |
   |-----------|-------------|
   | **Akce** | Zahrňte výsledky z předchozích akcí. Zobrazit [zahrnout výsledky akce](#action-results). |
   | **Trigger** | Zahrňte výsledky z triggeru. Zobrazit [výsledky aktivační události zahrnout](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Zahrnout výsledky v aktivační události

Pokud vyberete **triggery**, budete vyzváni, zda zahrnutí aktivační událost výsledků.

* Z **aktivační událost** seznamu vyberte **Ano**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Zahrnout výsledky v akci

Pokud vyberete **akce**, se zobrazí výzva k pro akce, které chcete přidat. Než začnete, přidání akce, ale potřebujete verzi názvu akce, které se zobrazí v základní definici pracovního postupu aplikace logiky.

* Tato funkce nepodporuje proměnné, smyčky a iterace indexy.

* Názvy v definici pracovního postupu aplikace logiky pomocí podtržítka (_), ne mezery.

* Pro názvy akcí, které používají tečka (.) patří například tyto operátory:

  `My.Action.Name`

1. Na panelu nástrojů návrháře zvolte **zobrazení kódu**a vyhledejte uvnitř `actions` atribut pro název akce.

   Například `Send_approval_email_` je název JSON **odeslat schvalovací e-mail** akce.

   ![Najít název akce ve formátu JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Chcete-li vrátit do zobrazení návrháře, na panelu nástrojů zobrazení kódu, zvolte **návrháře**.

1. Chcete-li přidat první akci v **akce položky - 1** zadejte název akce JSON.

   ![Zadejte první akci](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Chcete-li přidat další akci, zvolte **přidat novou položku**.

## <a name="reference"></a>Referenční informace

Další informace o **spuštění kódu jazyka JavaScript** tuto akci najdete v článku struktury a syntaxe v základní definici pracovního postupu aplikace logiky pomocí jazyka definice pracovního postupu akce [odkazovat na oddíl ](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>Další postup

Další informace o [konektory pro Azure Logic Apps](../connectors/apis-list.md)

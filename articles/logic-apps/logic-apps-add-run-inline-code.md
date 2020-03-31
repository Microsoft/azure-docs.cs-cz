---
title: Přidání a spuštění fragmentů kódu pomocí vstřikovaného kódu
description: Zjistěte, jak vytvářet a spouštět fragmenty kódu pomocí akcí vřádkového kódu pro automatizované úkoly a pracovní postupy, které vytvoříte pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453497"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Přidání a spuštění fragmentů kódu pomocí vkládacího kódu v aplikacích Azure Logic Apps

Pokud chcete spustit část kódu uvnitř aplikace logiky, můžete přidat integrovanou akci **Inline Code** jako krok v pracovním postupu aplikace logiky. Tato akce funguje nejlépe, pokud chcete spustit kód, který vyhovuje tomuto scénáři:

* Běží v JavaScriptu. Další jazyky již brzy.
* Dokončí spuštění za pět sekund nebo méně.
* Zpracovává data o velikosti až 50 MB.
* Nevyžaduje práci s akcemi Proměnné , které ještě nejsou [ **podporovány.** ](../logic-apps/logic-apps-create-variables-store-values.md)
* Používá node.js verze 8.11.1. Další informace naleznete [v tématu Standardní předdefinované objekty](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > Funkce `require()` není podporována akcí **Inline Code** pro spuštění JavaScriptu.

Tato akce spustí fragment kódu a vrátí výstup z tohoto fragmentu jako token s názvem **Result**, který můžete použít v následných akcích v aplikaci logiky. Pro další scénáře, kde chcete vytvořit funkci pro váš kód, zkuste [vytvořit a volat funkci Azure](../logic-apps/logic-apps-azure-functions.md) v aplikaci logiky.

V tomto článku se ukázková aplikace logiky aktivuje, když se nový e-mail dostane do účtu Office 365 Outlook. Fragment kódu extrahuje a vrací všechny e-mailové adresy, které se zobrazí v těle e-mailu.

![Ukázkový přehled](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky, kde chcete přidat fragment kódu, včetně aktivační události. Pokud nemáte aplikaci logiky, přečtěte si [úvodní příručku: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Ukázková aplikace logiky v tomto tématu používá tuto aktivační událost Outlooku Office 365: **Když přijde nový e-mail**

* [Účet integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) který je propojený s vaší aplikací logiky

  > [!NOTE]
  > Ujistěte se, že používáte účet integrace, který je vhodný pro váš případ použití nebo scénář. Například účty integrace [volné vrstvy](../logic-apps/logic-apps-pricing.md#integration-accounts) jsou určeny pouze pro průzkumné scénáře a úlohy, nikoli produkční scénáře, jsou omezené využití a propustnost a nejsou podporovány smlouvou o úrovni služeb (SLA). Ostatní úrovně vznikají náklady, ale zahrnují podporu SLA, nabízejí větší propustnost a mají vyšší limity. Přečtěte si další informace o [úrovních](../logic-apps/logic-apps-pricing.md#integration-accounts)účtů integrace , [cenách](https://azure.microsoft.com/pricing/details/logic-apps/)a [limitech](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="add-inline-code"></a>Přidání vřádkového kódu

1. Pokud jste tak ještě neučinili, otevřete na [webu Azure Portal](https://portal.azure.com)aplikaci logiky v Návrháři aplikací logiky.

1. V návrháři přidejte akci **Vřádkový kód** do požadovaného umístění v pracovním postupu aplikace logiky.

   * Chcete-li přidat akci na konec pracovního postupu, zvolte **Nový krok**.

   * Chcete-li přidat akci mezi existující kroky, přesuňte ukazatel myši na šipku, která tyto kroky spojuje. Zvolte znaménko plus (**+**) a vyberte Přidat **akci**.

   Tento příklad přidá akci **Vřádkový kód** pod aktivační událostí Outlook Office 365.

   ![Přidat nový krok](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. V části **Zvolte akci**zadejte do vyhledávacího pole jako filtr "vsazený kód". Ze seznamu akcí vyberte tuto akci: **Spustit kód JavaScriptu**

   ![Vyberte možnost Spustit kód JavaScriptu.](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Akce se zobrazí v návrháři a obsahuje některé výchozí ukázkový kód, včetně příkazu return.

   ![Akce Vřáditkód s výchozím ukázkovým kódem](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Do pole **Kód** odstraňte ukázkový kód a zadejte kód, který chcete spustit. Napište kód, který byste vložili do metody, ale bez definování podpisu metody. 

   Když zadáte rozpoznané klíčové slovo, zobrazí se seznam automatického dokončování, abyste mohli vybrat z dostupných klíčových slov, například:

   ![Seznam automatického dokončování klíčových slov](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Tento ukázkový fragment kódu nejprve vytvoří proměnnou, která ukládá *regulární výraz*, který určuje vzorek tak, aby odpovídal vstupnímu textu. Kód pak vytvoří proměnnou, která ukládá data těla e-mailu z aktivační události.

   ![Vytvoření proměnných](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Chcete-li usnadnit orientaci výsledků aktivační události a předchozích akcí, zobrazí se seznam dynamického obsahu, když je kurzor uvnitř pole **Kód.** V tomto příkladu seznam zobrazuje dostupné výsledky z aktivační události, včetně tokenu **Body,** který můžete nyní vybrat.

   Po **výběru** body token, akce vřádkový kód `workflowContext` překládá token na `Body` objekt, který odkazuje na hodnotu vlastnosti e-mailu:

   ![Vybrat výsledek](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   V poli **Kód** může fragment použít objekt jen `workflowContext` pro čtení jako vstup. Tento objekt má podvlastnosti, které poskytují váš kód přístup k výsledkům aktivační události a předchozí akce v pracovním postupu.
   Další informace naleznete v této části dále v tomto tématu: [Referenční aktivační událost a výsledky akce v kódu](#workflowcontext).

   > [!NOTE]
   >
   > Pokud váš fragment kódu odkazuje na názvy akcí, které používají operátor tečka (.), je nutné přidat tyto názvy akcí do [parametru **Akce** ](#add-parameters). Tyto odkazy musí také obsahovat názvy akcí s hranatými závorkami ([]) a uvozovkami, například:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Akce vslaného kódu nevyžaduje `return` příkaz, ale výsledky `return` z příkazu jsou k dispozici pro odkaz v pozdějších akcích prostřednictvím tokenu **Result.** 
   Například fragment kódu vrátí výsledek voláním `match()` funkce, která najde shody v těle e-mailu proti regulárnímu výrazu. Akce **Compose** používá token **Výsledek** k odkazování na výsledky z akce vsazený kód a vytvoří jeden výsledek.

   ![Hotová aplikace logiky](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Až budete hotovi, uložte aplikaci logiky.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Výsledky aktivační události a akce odkazu v kódu

Objekt `workflowContext` má tuto strukturu, `actions`která `trigger`zahrnuje `workflow` , a podvlastnosti:

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

| Vlastnost | Typ | Popis |
|----------|------|-------|
| `actions` | Kolekce objektů | Výsledek objekty z akcí, které jsou spuštěny před spuštěním fragmentu kódu. Každý objekt má dvojici *klíč-hodnota,* kde klíč je název akce a hodnota je ekvivalentní `@actions('<action-name>')`volání [akce() funkce](../logic-apps/workflow-definition-language-functions-reference.md#actions) s . Název akce používá stejný název akce, který se používá v základní definici pracovního postupu, která nahradí mezery (" ") v názvu akce podtržítky (_). Tento objekt poskytuje přístup k hodnotám vlastností akce z aktuální instance pracovního postupu. |
| `trigger` | Objekt | Výsledek objektu z aktivační události a ekvivalentní volání [funkce trigger().](../logic-apps/workflow-definition-language-functions-reference.md#trigger) Tento objekt poskytuje přístup k hodnotám vlastností aktivační události z aktuální instance pracovního postupu. |
| `workflow` | Objekt | Objekt pracovního postupu a ekvivalentní volání [funkce workflow().](../logic-apps/workflow-definition-language-functions-reference.md#workflow) Tento objekt poskytuje přístup k hodnotám vlastností pracovního postupu, jako je název pracovního postupu, ID spuštění a tak dále, z aktuální instance pracovního postupu. |
|||

V tomto tématu `workflowContext` příklad objekt má tyto vlastnosti, které váš kód přístup:

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

## <a name="add-parameters"></a>Přidat parametry

V některých případech může být nutné explicitně vyžadovat, aby akce **Inline Code** zahrnovala výsledky aktivační události nebo konkrétní akce, na které váš kód odkazuje jako na závislosti přidáním parametrů **Trigger** nebo **Actions.** Tato možnost je užitečná pro scénáře, kde se referenční výsledky nenacházejí za běhu.

> [!TIP]
> Pokud máte v plánu znovu použít kód, přidejte odkazy na vlastnosti pomocí pole **Kód** tak, aby váš kód obsahuje odkazy na přeložený token, spíše než přidávat aktivační událost nebo akce jako explicitní závislosti.

Předpokládejme například, že máte kód, který odkazuje na **výsledek SelectedOption** z akce **Odeslat schválení e-mailu** pro konektor Office 365 Outlook. V době vytvoření modul logic apps analyzuje váš kód k určení, zda jste odkazovali na všechny výsledky aktivační události nebo akce a zahrnuje tyto výsledky automaticky. Za běhu, pokud se zobrazí chyba, že odkazovaná aktivační událost nebo `workflowContext` výsledek akce není k dispozici v zadaném objektu, můžete přidat tuto aktivační událost nebo akci jako explicitní závislost. V tomto příkladu přidáte parametr **Actions** a určíte, že akce **Inline Code** explicitně zahrnuje výsledek z **akce Odeslat schválení e-mailu.**

Chcete-li přidat tyto parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry:

   ![Přidat parametry](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametr | Popis |
   |-----------|-------------|
   | **Akce** | Zahrnout výsledky z předchozích akcí. Viz [Zahrnout výsledky akcí](#action-results). |
   | **Trigger** | Zahrnout výsledky z aktivační události. Viz [Zahrnout výsledky aktivační události](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Zahrnout výsledky aktivační události

Pokud vyberete **aktivační události**, budete vyzváni, zda chcete zahrnout výsledky aktivační události.

* V seznamu **Aktivační událost** vyberte **Ano**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Zahrnout výsledky akce

Pokud vyberete **Akce**, budete vyzváni k zadání akcí, které chcete přidat. Než však začnete přidávat akce, potřebujete verzi názvu akce, která se zobrazí v definici pracovního postupu aplikace logiky.

* Tato funkce nepodporuje proměnné, smyčky a indexy iterace.

* Názvy v definici pracovního postupu aplikace logiky používají podtržítko (_), nikoli mezeru.

* Pro názvy akcí, které používají operátor tečka (.), zahrnují tyto operátory, například:

  `My.Action.Name`

1. Na panelu nástrojů návrháře zvolte Zobrazení `actions` **kódu**a vyhledejte uvnitř atributu název akce.

   Je například `Send_approval_email_` název JSON pro akci **Odeslat schválení e-mailu.**

   ![Najít název akce v JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Chcete-li se vrátit do zobrazení návrháře, zvolte na panelu nástrojů zobrazení kódu **návrháře**.

1. Chcete-li přidat první akci, zadejte do pole **Položka akce - 1** název JSON akce.

   ![Zadat první akci](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Chcete-li přidat další akci, zvolte **Přidat novou položku**.

## <a name="reference"></a>Odkaz

Další informace o struktuře a syntaxi akce **Spustit javascriptový kód** v definici pracovního postupu aplikace logiky pomocí jazyka definice pracovního postupu naleznete v [referenční části](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)této akce .

## <a name="next-steps"></a>Další kroky

Další informace o [konektorech pro aplikace Azure Logic Apps](../connectors/apis-list.md)

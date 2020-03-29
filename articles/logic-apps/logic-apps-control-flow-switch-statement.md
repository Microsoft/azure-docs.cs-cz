---
title: Přidání příkazů switch do pracovních postupů
description: Jak vytvořit příkazy přepínače, které řídí akce pracovního postupu na základě konkrétních hodnot v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 5c40feec2dca65e4bc9617a71a6d0a8e4c872a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793230"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Vytvoření příkazů přepínačů, které spouštějí akce pracovního postupu na základě konkrétních hodnot v Azure Logic Apps

Chcete-li spustit určité akce založené na hodnotách objektů, výrazů nebo tokenů, přidejte příkaz *switch.* Tato struktura vyhodnotí objekt, výraz nebo token, vybere případ, který odpovídá výsledku, a spustí určité akce pouze pro tento případ. Při spuštění příkazu switch by měl výsledek odpovídat pouze jeden případ.

Předpokládejme například, že chcete aplikaci logiky, která provede různé kroky na základě možnosti vybrané v e-mailu. V tomto příkladu aplikace logiky zkontroluje web rss informační kanál pro nový obsah. Když se v informačním kanálu RSS zobrazí nová položka, aplikace logiky odešle e-mail schvalovatelovi. Na základě toho, zda schvalovatel vybere "Schválit" nebo "Odmítnout", aplikace logiky postupují podle různých kroků.

> [!TIP]
> Stejně jako všechny programovací jazyky, příkazy přepínače podporují pouze operátory rovnosti. Pokud potřebujete další relační operátory, například "větší než", použijte [podmíněný příkaz](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Chcete-li zajistit deterministické provádění chování, případy musí obsahovat jedinečnou a statickou hodnotu namísto dynamické tokeny nebo výrazy.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Chcete-li postupovat podle příkladu v tomto článku, [vytvořte tuto ukázkovou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) s Outlook.com nebo účet Outlooku Office 365.

  1. Když přidáte akci pro odeslání e-mailu, vyhledání a výběr této akce místo toho: **Odeslat schvalovací e-mail**

     ![Vyberte možnost Odeslat schvalovací e-mail.](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Zadejte požadovaná pole, například e-mailovou adresu osoby, která dostane schvalovací e-mail. 
  V části **Možnosti uživatele**zadejte "Schválit, Odmítnout".

     ![Zadání podrobností o e-mailu](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Přidat příkaz switch

1. V tomto příkladu přidejte příkaz switch na konci ukázkového pracovního postupu. Po posledním kroku zvolte **Nový krok**.

   Pokud chcete přidat příkaz switch mezi kroky, přesuňte ukazatel myši na šipku, kam chcete přidat příkaz switch. Zvolte **znaménko plus** (**+**), které se zobrazí, a pak zvolte Přidat **akci**.

1. Do vyhledávacího pole zadejte jako filtr "switch". Vyberte tuto akci: **Přepnout – ovládat**

   ![Přidat přepínač](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Příkaz switch se zobrazí s jedním případem a výchozím případem. 
   Ve výchozím nastavení příkaz switch vyžaduje alespoň jeden případ plus výchozí případ. 

   ![Prázdný výchozí příkaz přepínače](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Klikněte do pole **Zapnuto,** aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte pole **SelectedOption,** jehož výstup určuje akci, kterou chcete provést. 

   ![Vyberte "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Chcete-li zpracovat případy, `Approve` kdy `Reject`schvalovatel vybere nebo , přidat další případ mezi **případ** a **výchozí**. 

   ![Přidat další případ](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Přidejte tyto akce do odpovídajících případů:

   | Případě # | **SelectedOption** | Akce |
   |--------|--------------------|--------|
   | Případ 1 | **Schválení** | Přidejte akci **Odeslat e-mail ovou** aplikaci Outlook pro odeslání podrobností o položce RSS pouze v případě, že schvalovatel vybraný **schválit**. |
   | Případ 2 | **Odmítnout** | Přidejte akci **Odeslat e-mail ovou** aplikaci Outlook, která upozorní ostatní schvalovatele, že položka RSS byla odmítnuta. |
   | Výchozí | Žádný | Žádná akce není nutná. V tomto příkladu je **výchozí** případ prázdný, protože **SelectedOption** má pouze dvě možnosti. |
   |||

   ![Příkaz dokončeného přepínače](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Uložte svou aplikaci logiky. 

   Chcete-li tento příklad otestovat ručně, zvolte **Spustit,** dokud aplikace logiky nenajde novou položku RSS a neodešle schvalovací e-mail. 
   Chcete-li sledovat výsledky, vyberte **možnost Schválit.**

## <a name="json-definition"></a>Definice JSON

Teď, když jste vytvořili aplikaci logiky pomocí příkazu switch, podívejme se na definici kódu vysoké úrovně za příkazem switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Popisek | Popis |
|-------|-------------|
| `"Switch"`         | Název příkazu switch, který můžete přejmenovat pro čitelnost |
| `"type": "Switch"` | Určuje, že akce je příkaz přepínače. |
| `"expression"`     | V tomto příkladu určuje vybranou možnost schvalovatele, která je vyhodnocena proti každému případu, jak je deklarováno dále v definici. |
| `"cases"` | Definuje libovolný počet případů. Pro každý `"Case_*"` případ je výchozí název pro tento případ, který můžete přejmenovat pro čitelnost |
| `"case"` | Určuje hodnotu případu, která musí být konstantní a jedinečná hodnota, kterou příkaz switch používá pro porovnání. Pokud žádné případy odpovídají výsledku výrazu `"default"` přepínače, jsou spuštěny akce v oddílu. | 
| | | 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete odeslat nebo hlasovat o funkcích nebo návrzích, navštivte [web pro zpětnou vazbu uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění a opakování kroků (smyček)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučení paralelních kroků (větví)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

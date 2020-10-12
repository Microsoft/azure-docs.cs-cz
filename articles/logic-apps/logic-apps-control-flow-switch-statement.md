---
title: Přidání příkazů Switch do pracovních postupů
description: Jak vytvořit příkazy Switch, které řídí akce pracovního postupu na základě konkrétních hodnot v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 62f147104cf9e0e1605b02a420cb6d20190361b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657459"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Vytvořte příkazy Switch, které spouštějí akce pracovního postupu na základě konkrétních hodnot v Azure Logic Apps

Chcete-li spustit konkrétní akce založené na hodnotách objektů, výrazů nebo tokenů, přidejte příkaz *Switch* . Tato struktura vyhodnocuje objekt, výraz nebo token, zvolí případ, který odpovídá výsledku, a spustí konkrétní akce pouze pro tento případ. Při spuštění příkazu switch by byl výsledek odpovídat pouze jednomu případu.

Předpokládejme například, že chcete, aby aplikace logiky, která provede různé kroky, byla založena na možnosti vybrané v e-mailu. V tomto příkladu aplikace logiky kontroluje nový obsah v informačním kanálu RSS webu. Když se v informačním kanálu RSS objeví nová položka, aplikace logiky pošle e-mail schvalovateli. V závislosti na tom, jestli schvalovatel vybere "schválit" nebo "zamítnout", aplikace logiky provede jiný postup.

> [!TIP]
> Podobně jako u všech programovacích jazyků podporují příkazy Switch jenom operátory rovnosti. Pokud potřebujete jiné relační operátory, například "větší než", použijte [podmíněný příkaz](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Aby bylo zajištěno deterministické chování při spuštění, musí být v případech jedinečné a statické hodnoty namísto dynamických tokenů nebo výrazů.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Pokud chcete postupovat podle příkladu v tomto článku, [vytvořte tuto ukázkovou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) s účtem Outlook.com nebo pracovním nebo školním účtem.

  1. Když přidáte akci pro odeslání e-mailu, najděte a vyberte tuto akci, ale **odešlete e-mail pro schválení** .

     ![Vyberte Odeslat e-mail pro schválení.](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Zadejte požadovaná pole, jako je e-mailová adresa osoby, která získá schvalovací e-mail. 
  V části **Možnosti uživatele**zadejte "schválit, odmítnout".

     ![Zadat podrobnosti e-mailu](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Přidat příkaz switch

1. V tomto příkladu přidejte příkaz switch na konci ukázkového pracovního postupu. Po posledním kroku vyberte **Nový krok**.

   Chcete-li přidat příkaz switch mezi kroky, přesuňte ukazatel myši na šipku, kam chcete přidat příkaz switch. Zvolte **symbol plus** ( **+** ), který se zobrazí, a pak zvolte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "Switch". Vyberte tuto akci: **ovládací prvek Switch**

   ![Přidat přepínač](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Příkaz switch se zobrazí s jedním případem a výchozím případem. 
   Ve výchozím nastavení příkaz switch vyžaduje aspoň jeden případ Plus výchozí případ. 

   ![Prázdný výchozí příkaz switch](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Klikněte do pole **zapnuto** , aby se zobrazil seznam dynamického obsahu. V tomto seznamu vyberte pole **SelectedOption** , jehož výstup Určuje akci, která má být provedena. 

   ![Vyberte "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Chcete-li zpracovat případy, kde schvalovatel vybere `Approve` nebo `Reject` , přidejte jiný případ mezi **případ** a **výchozí**. 

   ![Přidat další případ](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Přidejte tyto akce do odpovídajících případů:

   | Tom # | **SelectedOption** | Akce |
   |--------|--------------------|--------|
   | Případ 1 | **Schválení** | Přidání **e-mailové akce Odeslat e-mail** pro odeslání podrobností o položce RSS pouze v případě, že schvalovatel zvolil **schválení**. |
   | Případ 2 | **Odmítnout** | Přidání **e-mailové akce Odeslat e-mail** pro upozorňování dalších schvalovatelů, že položka RSS byla odmítnuta. |
   | Výchozí | Žádné | Žádná akce není nutná. V tomto příkladu je **výchozí** případ prázdný, protože **SelectedOption** má jenom dvě možnosti. |
   |||

   ![Dokončený příkaz switch](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Uložte aplikaci logiky. 

   Pokud chcete tento příklad otestovat ručně, klikněte na tlačítko **Spustit** , dokud aplikace logiky nenajde novou položku RSS a odešle e-mail pro schválení. 
   Vyberte **schválit** a sledujte výsledky.

## <a name="json-definition"></a>Definice JSON

Teď, když jste vytvořili aplikaci logiky pomocí příkazu switch, se podívejme na definici kódu na nejvyšší úrovni za příkazem Switch.

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

| Popisek | Description |
|-------|-------------|
| `"Switch"`         | Název příkazu switch, který lze přejmenovat pro čitelnost |
| `"type": "Switch"` | Určuje, že akce je příkaz switch. |
| `"expression"`     | V tomto příkladu určuje možnost schvalovatele, která se vyhodnocuje pro každý případ deklarovaný později v definici. |
| `"cases"` | Definuje libovolný počet případů. U každého případu `"Case_*"` je výchozím názvem pro tento případ, který můžete přejmenovat pro čitelnost. |
| `"case"` | Určuje hodnotu případu, která musí být konstantou a jedinečnou hodnotou, kterou příkaz switch používá pro porovnání. Pokud se ve výsledku výrazu Switch neshodují žádné případy, akce v `"default"` části se spustí. | 
| | | 

## <a name="get-support"></a>Získat podporu

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Chcete-li odeslat nebo hlasovat o funkcích nebo návrzích, navštivte [web Azure Logic Apps pro zpětnou vazbu uživatelů](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění a opakování kroků (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spustit nebo sloučit paralelní kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spuštění kroků na základě seskupeného stavu akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

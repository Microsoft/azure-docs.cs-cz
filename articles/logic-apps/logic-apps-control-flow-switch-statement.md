---
title: Přidání příkazů přepínače pracovní postupy – Azure Logic Apps | Dokumentace Microsoftu
description: Jak vytvořit příkazů přepínače, které řídí akce pracovního postupu podle konkrétní hodnoty v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 2a3f8ee5cba3110d392555fad78c1cb2513b5d4e
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232439"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Vytváření příkazů přepínače, na kterých běží akcí pracovního postupu podle konkrétní hodnoty v Azure Logic Apps

Chcete-li spustit konkrétní akce na základě hodnot objektů, výrazy nebo tokenů, přidejte *přepnout* příkazu. Tato struktura vyhodnocen jako objekt, výraz nebo token, zvolí případ srovná výsledek, který spouští konkrétní akce pouze pro tento případ. Při spuštění příkazu switch by měl odpovídat pouze jeden případ. výsledek.

Předpokládejme například, že chcete aplikaci logiky, která přebírá jiný postup podle zvolené v e-mailu. V tomto příkladu aplikace logiky kontroluje nový obsah informačního kanálu RSS webu. Když se v informačním kanálu RSS objeví nová položka, aplikace logiky odešle e-mail schvalovatele. Aplikace logiky založené na tom, jestli schvalovatel vybere "Schválení" nebo "Odmítnout", následuje jiný postup.

> [!TIP]
> Stejně jako všechny programovací jazyky příkazy přepínače podporují pouze operátory rovnosti. Pokud potřebujete další relační operátory, jako jsou "větší než", použijte [podmíněný příkaz](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> K zajištění provádění deterministické chování, případech musí obsahovat hodnotu jedinečný a statické namísto dynamického tokeny nebo výrazy.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* V našem příkladu v tomto článku [vytvořte Tato ukázková aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) pomocí účtu Outlook.com nebo Office 365 Outlook.

  1. Při přidání akce pro odeslání e-mailu vyhledejte a vyberte tuto akci místo toho: **pošle schvalovací e-mail**

     ![Vyberte "Odeslat schvalovací e-mail"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Zadejte požadované pole, jako jsou e-mailovou adresu pro osobu, která získá schvalovacího e-mailu. 
  V části **možnosti uživatele**, zadejte "Schválit, zamítnout".

     ![Zadejte podrobnosti o e-mailu](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Přidání příkazu switch.

1. V tomto příkladu přidejte příkaz switch na konci vaší ukázkového pracovního postupu. Po poslední krok, zvolte **nový krok**.

   Pokud chcete přidat příkaz switch mezi kroky, přesuňte ukazatel nad šipku, které chcete přidat příkazu switch. Zvolte **znaménko plus** (**+**), který se zobrazí, klikněte na tlačítko **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "přepněte". Vyberte tuto akci: **přepnout – ovládací prvek**

   ![Přidejte přepínač](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Příkaz přepínače se zobrazí s jeden případ a výchozí případ. 
   Ve výchozím nastavení příkazu switch vyžaduje aspoň jeden případ a výchozí případ. 

   ![Prázdný výchozí příkaz switch](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Klikněte do **na** pole tak, aby zobrazil seznam dynamického obsahu. V tomto seznamu, vyberte **SelectedOption** pole, jehož výstup určuje akce k provedení. 

   ![Vyberte "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Zpracování případů, které vybere schvalovatele `Approve` nebo `Reject`, přidat další možnost je mezi **případ** a **výchozí**. 

   ![Přidat další možnost je](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Přidejte do odpovídajících případech tyto akce:

   | # Případu | **SelectedOption** | Akce |
   |--------|--------------------|--------|
   | Případ 1 | **Schválení** | Přidat aplikace Outlook **odeslat e-mailu** akce pro odesílání podrobnosti položky RSS pouze v případě, že schvalovatel vybral **schválit**. |
   | Případ 2 | **Odmítnout** | Přidat aplikace Outlook **odeslat e-mailu** akce pro oznamování další schvalovatele, že položka RSS byla odmítnuta. |
   | Výchozí | Žádný | Není potřeba žádná akce. V tomto příkladu **výchozí** případ je prázdný protože **SelectedOption** má jenom dvě možnosti. |
   |||

   ![Příkaz switch dokončeno](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Uložte svou aplikaci logiky. 

   Chcete-li otestovat ručně v tomto příkladu, zvolte **spustit** až do aplikace logiky najde nové položky RSS a pošle schvalovací e-mail. 
   Vyberte **schválit** sledovat výsledky.

## <a name="json-definition"></a>Definice JSON

Teď, když jste vytvořili aplikaci logiky pomocí příkazu switch, Podívejme se na definici základní kód za příkazu switch.

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

| Štítek | Popis |
|-------|-------------|
| `"Switch"`         | Název příkazu switch, které je možné přejmenovat pro lepší čitelnost |
| `"type": "Switch"` | Určuje, že "action" je příkaz switch |
| `"expression"`     | V tomto příkladě určuje schvalovatele možnost, která se vyhodnotí pro každý případ, jak je deklarován v definici později |
| `"cases"` | Definuje libovolný počet případů. Pro každý případ `"Case_*"` je výchozí název pro tento případ, který je možné přejmenovat pro lepší čitelnost |
| `"case"` | Určuje hodnotu tento případ, který musí být jedinečný a konstantní hodnotu, která výrazu switch, který se používá pro porovnání. Pokud žádné případy odpovídat výsledkům výrazu přepínače, akce v `"default"` části jsou spuštěny. | 
| | | 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Odeslání návrhu nebo hlasování na návrhy nebo funkce, najdete v tématu [webu zpětné vazby uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění a opakujte kroky (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučit paralelními kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

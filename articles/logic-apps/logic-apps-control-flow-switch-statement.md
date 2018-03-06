---
title: "Přepínač příkazy – spustit kroky podle konkrétní hodnoty - Azure Logic Apps | Microsoft Docs"
description: "Spustit různé kroky na základě hodnot z objektů, výrazy nebo tokenů v aplikacích logiky"
services: logic-apps
keywords: "Switch – příkaz"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: e1f515189be8a5659af0f6c29b3fac0550abc9f9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="switch-statements-run-different-steps-based-on-specific-values-in-logic-apps"></a>Přepínač příkazy: spustit různé kroky, které jsou založené na konkrétní hodnoty v aplikace logiky

Pokud chcete provést různé kroky na základě hodnot objektu, výraz nebo token, použijte *přepínač* příkaz. Tato struktura vyhodnotí objektu, výraz nebo token, vybere případu, který odpovídá výsledek a kroky pro pouze v takovém případě se spustí. Po spuštění příkazu switch pouze jeden případ by měl odpovídat výsledek.

Předpokládejme například, že chcete aplikaci logiky, která přebírá různé kroky, které jsou založené na možnost vybrána v e-mailu. V tomto příkladu kontroluje aplikaci logiky na web RSS pro nový obsah. Když nové položky se zobrazí v informačního kanálu RSS, aplikaci logiky odešle e-mailu schvalovatele. Podle toho, jestli schvalovatel vybere "Schválit" nebo "Odmítnout", aplikaci logiky postupuje jiný.

> [!TIP]
> Podobně jako všechny programovacích jazyků příkazech switch podporují pouze operátory rovnosti. Pokud potřebujete další relační operátory, jako je například "větší než", použití [podmíněného příkaz](#conditions).
> Aby chování při spuštění deterministický, případech musí obsahovat jedinečný a statickou hodnotu namísto dynamického tokeny nebo výrazy.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* V našem příkladu v tomto článku [vytvoření této ukázkové aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) pomocí účtu, Outlook.com nebo Office 365 Outlook.

  1. Když přidáte akce k odeslání e-mailu, vyberte **e-mailovou zprávu schválení** místo.

     ![Vyberte "Odeslat e-mailu schválení"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. Zadejte požadovaná pole, jako je e-mailovou adresu pro uživatele, který získá e-mailu schválení. 
  V části **možností uživatele**, zadejte "Schválit, odmítnout".

     ![Zadejte podrobnosti o e-mailu](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>Přidejte příkaz switch

1. Na konci ukázkový pracovní postup, zvolte **+ nový krok** > **... Další** > **přidání přepínače případu**. 

   ![Přidejte příkaz switch](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Příkaz switch zobrazí s jeden případ a výchozí případu. 
   Příkaz switch vyžaduje alespoň jeden případ plus výchozí případu. 

   Pokud chcete přidat příkaz switch mezi kroky, přesuňte ukazatel přes šipku ve které chcete přidat příkaz switch. 
   Vyberte **znaménko plus** (**+**), který se zobrazuje, zvolte **přidání přepínače případu**.

4. V **na** vyberte **SelectedOption** pole, jejíž výstup určuje akce k provedení. 
   
   Můžete vybrat pole z **přidávat dynamický obsah** seznamu, který se zobrazí.

5. Zpracování případů, které vybere schvalovatel `Approve` nebo `Reject`, přidání jiného případu mezi **případ** a **výchozí**. 
   
6. Přidejte tyto akce v odpovídajících případech:

   | Case # | **SelectedOption** | Akce |
   |:------ |:-------------------|:------ |
   | Případ 1 | **Schválení** | Přidat možnost aplikace Outlook **e-mailovou zprávu** akce pro odesílání podrobnosti o položce RSS, jenom když vyberete schvalovatel **schválit**. |
   | Případ 2 | **Odmítnout** | Přidat možnost aplikace Outlook **e-mailovou zprávu** akce pro oznamování další schvalovatele, že položka RSS byla odmítnuta. |
   | Výchozí | \<None\> | Není potřebná žádná akce. V tomto příkladu **výchozí** případem je prázdný protože **SelectedOption** má jenom dvě možnosti. |
   |         |          |

   ![Switch – příkaz](./media/logic-apps-control-flow-switch-statement/switch.png)

7. Uložte svou aplikaci logiky. 

   Chcete-li otestovat ručně v tomto příkladu, zvolte **spustit** dokud aplikaci logiky najde nové položky RSS a odešle e-mail schválení. 
   Vyberte **schválit** chcete pozorovat výsledky.

## <a name="json-definition"></a>Definici JSON

Teď, když jste vytvořili pomocí příkazu switch aplikace logiky, podíváme se na definici podrobný kód za příkazem switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
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

| Štítek              | Popis |
| :----------------- | :---------- |
| `"Switch"`         | Název příkazu přepínače, které můžete přejmenovat čitelnější |
| `"type": "Switch"` | Určuje, že akce je příkaz switch |
| `"expression"`     | V tomto příkladu určuje jeho vybranou možnost, který se vyhodnotí pro každý případ deklarovaných později v definici |
| `"cases"` | Definuje libovolný počet případů. Pro každý případ `"Case_*"` je výchozí název pro tento případ, kterou lze přejmenovat čitelnější |
| `"case"` | Určuje hodnotu tento případ, který musí být jedinečný a konstantní hodnotu, která výrazu switch, který se používá pro porovnání. Je-li žádné případy odpovídat výraz výsledek akce v přepínač `"default"` části spouštějí.
|           |         |

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Odeslání nebo hlasovat o funkce nebo návrhy, najdete [web pro zasílání názorů uživatele Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění a opakujte kroky (smyčky)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučení paralelní kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (oborům)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
---
title: "Podmíněné příkazy – spustit kroky na základě podmínky - Azure Logic Apps | Microsoft Docs"
description: "Spusťte kroky v aplikaci logiky až po splnění podmínku. Vytvořte rozhodovací stromy, které spouštění pracovních postupů na základě zadaných podmínek."
services: logic-apps
keywords: "Podmíněné příkazy, rozhodovacích stromů"
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 486c1053f42ed3becc2c4b60accc993db7f24baa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="conditional-statements-run-steps-based-on-a-condition-in-logic-apps"></a>Podmíněné příkazy: spustit kroky na základě podmínky v aplikacích logiky

Pokud chcete provést kroky pouze po úspěšném je zadaná podmínka, použijte *podmíněného příkaz*. Tato struktura porovnávají data do svého pracovního postupu pro konkrétní hodnoty nebo pole. Potom můžete definovat různé kroky, které spustit závislosti na tom, zda data splňuje podmínku. Lze vnořit podmínky uvnitř sebe navzájem.

Předpokládejme například, že máte aplikaci logiky, která odešle příliš moc e-mailů, jakmile se zobrazí nové položky na webu informačního kanálu RSS. Můžete přidat podmíněného příkaz k odeslání e-mailu jenom v případě, že nová položka obsahuje konkrétní řetězec. 

> [!TIP]
> Chcete-li spustit různé kroky podle různých konkrétní hodnoty, použijte [ *příkaz switch* ](../logic-apps/logic-apps-control-flow-switch-statement.md) místo.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* V našem příkladu v tomto článku [vytvoření této ukázkové aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) pomocí účtu, Outlook.com nebo Office 365 Outlook.

## <a name="add-a-condition"></a>Přidat podmínku

1. V <a href="https://portal.azure.com" target="_blank">portál Azure</a>, otevřete aplikaci logiky v návrháři aplikace logiky.

2. V umístění, které chcete přidáte podmínku. 

   Chcete-li přidat podmínku mezi kroky, přesuňte ukazatel přes šipku, ve které chcete přidat podmínku. Vyberte **znaménko plus** (**+**), který se zobrazuje, zvolte **přidat podmínku**. Příklad:

   ![Přidejte podmínku mezi kroky](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   Pokud chcete přidat podmínku na konci pracovního postupu, v dolní části svou aplikaci logiky, vyberte **+ nový krok** > **přidat podmínku**.

3. V části **podmínku**, vytvořte vaše podmínku. 

   1. V dialogovém okně levém zadejte data nebo pole, které chcete porovnat.

      Z **přidávat dynamický obsah** seznamu můžete vybrat existující pole z aplikace logiky.

   2. V prostředním seznamu vyberte na provedení operace. 
   3. V dialogovém okně vpravo zadejte hodnota nebo pole jako kritériím.

   Příklad:

   ![Upravit podmínky v základní režimu](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode.png)

   Tady je Dokončená podmínka:

   ![Dokončení podmínky](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Chcete-li vytvořit podmínku pokročilejší nebo použití výrazů, zvolte **upravit v rozšířeném režimu**. Můžete použít výrazy, které jsou definované [jazyk definic workflowů](../logic-apps/logic-apps-workflow-definition-language.md).
   > 
   > Příklad:
   >
   > ![Upravit podmínky v kódu](./media/logic-apps-control-flow-conditional-statement/edit-condition-advanced-mode.png)

5. V části **Pokud Ano** a **ne v případě**, přidávat postupy založené na tom, zda je splněna podmínka. Příklad:

   ![Podmínky se Ano a žádné cesty](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Můžete přetáhnout existující akce do **Pokud Ano** a **ne v případě** cesty.

6. Uložte svou aplikaci logiky.

Tato aplikace logiky teď jenom pošle e-mailu, když nové položky v informačního kanálu RSS splňují vaše podmínky.

## <a name="json-definition"></a>Definici JSON

Teď, když jste vytvořili pomocí podmíněného příkazu aplikace logiky, podíváme se na definici podrobný kód za podmíněného příkaz.

``` json
"actions": {
  "myConditionName": {
    "type": "If",
    "expression": "@contains(triggerBody()?['summary'], 'Microsoft')",
    "actions": {
      "Send_an_email": {
        "inputs": { },
        "runAfter": {}
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Odeslání nebo hlasovat o funkcích a návrhy, najdete [web pro zasílání názorů uživatele Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě různých hodnot (příkazech switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakujte kroky (smyčky)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučení paralelní kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (oborům)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
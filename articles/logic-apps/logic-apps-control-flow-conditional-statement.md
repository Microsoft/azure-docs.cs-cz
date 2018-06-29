---
title: Podmíněné příkazy přidat do pracovních - Azure Logic Apps | Microsoft Docs
description: Vytvoření podmínek, které řídí akce v pracovních postupech v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: d4e69d33e07f484b4ccc5343786865230368c7ca
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096372"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Vytvoření podmíněné příkazy, které řídí akce pracovního postupu v Azure Logic Apps

Chcete-li spustit konkrétní akce v aplikaci logiky pouze po úspěšném je zadaná podmínka, přidejte *podmíněného příkaz*. Tato struktura porovnávají data do svého pracovního postupu pro konkrétní hodnoty nebo pole. Potom můžete definovat různé akce, které spustit závislosti na tom, zda data splňuje podmínku. Lze vnořit podmínky uvnitř sebe navzájem.

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

3. V části **podmínku**, sestavení vaší podmínky. 

   1. V dialogovém okně levém zadejte data nebo pole, které chcete porovnat.

      Po kliknutí na tlačítko uvnitř levé pole, seznamu dynamického obsahu se zobrazí, takže výstupy z předchozích kroků můžete vybrat v aplikaci logiky. 
      V tomto příkladu vyberte souhrn informačního kanálu RSS.

      ![Sestavení vaší podmínky](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   2. V prostředním seznamu vyberte na provedení operace. 
   V tomto příkladu vyberte "**obsahuje**". 

   3. V dialogovém okně vpravo zadejte hodnota nebo pole jako kritériím. 
   V tomto příkladu zadejte tento řetězec: **Microsoft**

   Tady je Dokončená podmínka:

   ![Dokončená podmínka](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

5. V části **v případě hodnoty true** a **-li pravda**, přidávat postupy založené na tom, zda je splněna podmínka. Příklad:

   ![Podmínka vyhodnocena jako s "je-li nastavena hodnota true" a "je-li hodnotu false" cesty](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Můžete přetáhnout existující akce do **v případě hodnoty true** a **-li pravda** cesty.

6. Uložte svou aplikaci logiky.

Tato aplikace logiky teď jenom pošle e-mailu, když nové položky v informačního kanálu RSS splňují vaše podmínky.

## <a name="json-definition"></a>Definici JSON

Teď, když jste vytvořili pomocí podmíněného příkazu aplikace logiky, podíváme se na definici podrobný kód za podmíněného příkaz.

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", "Microsoft"
          ]
        } 
      ]
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

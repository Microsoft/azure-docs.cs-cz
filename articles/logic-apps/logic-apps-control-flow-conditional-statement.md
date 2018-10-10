---
title: Přidat podmíněné příkazy pro pracovní postupy – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření podmínek, které řídí akce v pracovních postupech v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 462a21c760f7dec727148f2a41dec9f508e24dbc
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885234"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Vytvoření podmíněné příkazy, které řídí akce pracovního postupu ve službě Azure Logic Apps

Chcete-li spustit konkrétní akce ve vaší aplikaci logiky pouze po úspěšném zadaná podmínka, přidejte *podmíněný příkaz*. Tato struktura ovládací prvek porovnává data ve svém pracovním postupu proti konkrétní hodnoty nebo pole. Potom můžete zadat různé akce, které běží v závislosti na tom, zda data splní podmínku. Je možné vnořovat podmínkách v navzájem.

Předpokládejme například, že budete mít aplikaci logiky, která odesílá příliš moc e-mailů, když se v informačním kanálu RSS webu objeví nové položky. Můžete přidat podmíněný příkaz pro odeslání e-mailu pouze v případě, že nová položka obsahuje konkrétní řetězec. 

> [!TIP]
> Spustit různé kroky podle různých konkrétní hodnoty, použijte [ *switch – příkaz* ](../logic-apps/logic-apps-control-flow-switch-statement.md) místo.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* V našem příkladu v tomto článku [vytvořte Tato ukázková aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) pomocí účtu Outlook.com nebo Office 365 Outlook.

## <a name="add-condition"></a>Přidat podmínku

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, otevřete v návrháři aplikace logiky aplikace logiky.

1. V umístění, které chcete přidáte podmínku. 

   Přidání podmínky mezi kroky, přesuňte ukazatel nad šipku, ve které chcete přidat podmínku. Zvolte **znaménko plus** (**+**), který se zobrazí, klikněte na tlačítko **přidat akci**. Příklad:

   ![Přidání akce mezi kroky](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Pokud chcete přidat podmínku na konci pracovního postupu, v dolní části aplikace logiky zvolte **nový krok** > **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "podmínku". Vyberte tuto akci: **podmínky – ovládací prvek**

   ![Přidat podmínku](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. V **podmínku** pole, sestavování, vaše podmínka. 

   1. V levé pole zadejte data nebo pole, které chcete porovnat.

      Po kliknutí do levého pole se zobrazí seznam dynamického obsahu, tak ve vaší aplikaci logiky můžete vybrat výstupy z předchozích kroků. 
      V tomto příkladu vyberte souhrn informačního kanálu RSS.

      ![Sestavení podmínky](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. V prostředním poli vyberte operaci provést. 
   V tomto příkladu vyberte "**obsahuje**". 

   1. V pravé pole zadejte hodnotu nebo pole jako kritéria. 
   V tomto příkladu zadejte tento řetězec: **Microsoft**

   Tady je Dokončená podmínka:

   ![Dokončená podmínka](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Chcete-li přidat další řádek do vaše podmínky, zvolte **přidat** > **přidat řádek**. 
   Chcete-li přidat skupinu pomocí subconditions, zvolte **přidat** > **přidat skupinu**. 
   Seskupení existující řádků, zaškrtněte políčka pro řádky, klikněte na tlačítko se třemi tečkami (...) pro každý řádek a pak zvolte **Zkontrolujte skupiny**.

1. V části **při hodnotě true** a **případě hodnoty false**, přidat kroky nutné k provedení na základě na, jestli je splněná podmínka. Příklad:

   ![Podmínka s "při hodnotě true" a "Pokud je false" cesty](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Můžete přetáhnout do existující akce **při hodnotě true** a **případě hodnoty false** cesty.

1. Uložte svou aplikaci logiky.

Tato aplikace logiky teď odešle e-mailu jenom v případě, že splňují vaše podmínka nových položek v informačním kanálu RSS.

## <a name="json-definition"></a>Definice JSON

Tady je podrobný kód definice za podmíněný příkaz:

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
            "@triggerBody()?['summary']", 
            "Microsoft"
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
* Odeslání návrhu nebo hlasování o funkcích a návrhy, najdete v tématu [webu zpětné vazby uživatelů Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě různých hodnot (příkazů přepínače)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakujte kroky (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučit paralelními kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

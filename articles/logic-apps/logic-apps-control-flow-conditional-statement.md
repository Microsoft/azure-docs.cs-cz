---
title: Přidání podmíněných příkazů do pracovních postupů
description: Vytvoření podmínek, které řídí akce v pracovních postupech v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 19f42270e37c42cf56bbde04f73c01027a56ff63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657396"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Vytvořte podmíněné příkazy, které řídí akce pracovního postupu v Azure Logic Apps

Pokud chcete spustit konkrétní akce v aplikaci logiky až po předání zadané podmínky, přidejte *podmíněný příkaz*. Tato řídicí struktura porovnává data v pracovním postupu s konkrétními hodnotami nebo poli. Pak můžete zadat různé akce, které se spustí na základě toho, jestli data splňují podmínky. Podmínky můžete vnořovat mezi sebou.

Předpokládejme například, že máte aplikaci logiky, která posílá příliš mnoho e-mailů, když se nové položky zobrazí v informačním kanálu RSS webu. Můžete přidat podmíněný příkaz k odeslání e-mailu pouze v případě, že nová položka obsahuje konkrétní řetězec. 

> [!TIP]
> Pro spuštění různých kroků na základě různých specifických hodnot použijte místo toho [*příkaz switch*](../logic-apps/logic-apps-control-flow-switch-statement.md) .

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Pokud chcete postupovat podle příkladu v tomto článku, [vytvořte tuto ukázkovou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) s účtem Outlook.com nebo pracovním nebo školním účtem.

## <a name="add-condition"></a>Přidání podmínky

1. V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>otevřete aplikaci logiky v návrháři aplikace logiky.

1. Přidejte podmínku do umístění, které chcete. 

   Chcete-li přidat podmínku mezi kroky, přesuňte ukazatel myši na šipku, kam chcete podmínku přidat. Zvolte **symbol plus** ( **+** ), který se zobrazí, a pak zvolte **přidat akci**. Například:

   ![Přidat akci mezi kroky](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Pokud chcete přidat podmínku na konci pracovního postupu, v dolní části aplikace logiky vyberte  **Nový krok** > **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "podmínka". Vyberte tuto akci: **řízení podmínek**

   ![Přidání podmínky](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. V poli **Podmínka** si sestavte podmínku. 

   1. V levém poli zadejte data nebo pole, které chcete porovnat.

      Když kliknete dovnitř levého pole, zobrazí se seznam dynamického obsahu, abyste mohli vybírat výstupy z předchozích kroků v aplikaci logiky. 
      V tomto příkladu vyberte souhrn informačního kanálu RSS.

      ![Sestavení podmínky](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. V prostředním poli vyberte operaci, kterou chcete provést. 
   V tomto příkladu vyberte "**Contains**". 

   1. Do pravého pole zadejte hodnotu nebo pole jako kritéria. 
   V tomto příkladu zadejte tento řetězec: **Microsoft**

   Tady je kompletní podmínka:

   ![Dokončená podmínka](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Pokud chcete do podmínky přidat další řádek, vyberte **Přidat**  >  **Přidat řádek**. 
   Pokud chcete přidat skupinu s podstavy, vyberte **Přidat**  >  **Přidat skupinu**. 
   Pokud chcete seskupit existující řádky, zaškrtněte políčka pro tyto řádky, klikněte na tlačítko se třemi tečkami (...) pro libovolný řádek a pak zvolte **vytvořit skupinu**.

1. V části Pokud je nastaveno na **hodnotu true** a **Pokud má hodnotu false**, přidejte kroky k provedení na základě toho, zda je podmínka splněna. Například:

   ![Podmínka s cestami "if true" a "if false"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Existující akce lze přetáhnout do **hodnoty if true** a pokud se jedná o **falešnou** cestu.

1. Uložte aplikaci logiky.

Tato aplikace logiky teď odesílá poštu jenom v případě, že nové položky v informačním kanálu RSS splňují vaši podmínku.

## <a name="json-definition"></a>Definice JSON

Tady je definice kódu vysoké úrovně za podmíněným příkazem:

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

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Chcete-li odesílat nebo hlasovat o funkcích a návrzích, navštivte [web Azure Logic Apps pro zpětnou vazbu uživatelů](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě různých hodnot (příkazy Switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakování kroků (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spustit nebo sloučit paralelní kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spuštění kroků na základě seskupeného stavu akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

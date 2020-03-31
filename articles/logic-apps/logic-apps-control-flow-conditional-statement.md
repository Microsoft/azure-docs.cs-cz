---
title: Přidání podmíněných příkazů do pracovních postupů
description: Jak vytvořit podmínky, které řídí akce v pracovních postupech v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: fe79cf5af86e1f303e4735214b993d8db4488a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793248"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Vytváření podmíněných příkazů, které řídí akce pracovního postupu v Aplikacích Azure Logic Apps

Chcete-li spustit konkrétní akce v aplikaci logiky pouze po předání zadané podmínky, přidejte *podmíněný příkaz*. Tato řídicí struktura porovnává data v pracovním postupu s konkrétními hodnotami nebo poli. Potom můžete zadat různé akce, které běží na základě toho, zda data splňují podmínku. Můžete vnořit podmínky uvnitř sebe.

Předpokládejme například, že máte aplikaci logiky, která odesílá příliš mnoho e-mailů, když se nové položky zobrazí v informačním kanálu RSS webu. Podmíněný příkaz můžete přidat k odeslání e-mailu pouze v případě, že nová položka obsahuje určitý řetězec. 

> [!TIP]
> Chcete-li spustit různé kroky založené na různých konkrétních hodnotách, použijte místo toho [*příkaz switch.*](../logic-apps/logic-apps-control-flow-switch-statement.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Chcete-li postupovat podle příkladu v tomto článku, [vytvořte tuto ukázkovou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) s Outlook.com nebo účet Outlooku Office 365.

## <a name="add-condition"></a>Přidání podmínky

1. Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Přidejte podmínku do požadovaného umístění. 

   Chcete-li přidat podmínku mezi kroky, přesuňte ukazatel myši na šipku, kam chcete podmínku přidat. Zvolte **znaménko plus** (**+**), které se zobrazí, a pak zvolte Přidat **akci**. Například:

   ![Přidání akce mezi kroky](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Pokud chcete přidat podmínku na konec pracovního postupu, v dolní části aplikace logiky zvolte **Nový krok** > **Přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "condition". Vybrat tuto akci: **Podmínka – ovládací prvek**

   ![Přidání podmínky](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. V poli **Podmínka** vytvořte svůj stav. 

   1. V levém poli zadejte data nebo pole, které chcete porovnat.

      Když kliknete do levého pole, zobrazí se seznam dynamického obsahu, abyste mohli vybrat výstupy z předchozích kroků v aplikaci logiky. 
      V tomto příkladu vyberte souhrn informačního kanálu RSS.

      ![Sestavte si svůj stav](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Ve středním poli vyberte operaci, kterou chcete provést. 
   V tomto příkladu vyberte "**obsahuje**". 

   1. V pravém poli zadejte jako kritéria hodnotu nebo pole. 
   V tomto příkladu zadejte tento řetězec: **Microsoft**

   Zde je kompletní stav:

   ![Dokončená podmínka](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Pokud chcete do podmínky přidat další řádek, zvolte **Přidat** > **řádek**. 
   Chcete-li přidat skupinu s podpodmínkami, zvolte **Přidat** > **skupinu**. 
   Chcete-li seskupit existující řádky, zaškrtněte políčka pro tyto řádky, zvolte tlačítko elipsy (...) pro libovolný řádek a pak zvolte **Vytvořit skupinu**.

1. V části **Pokud true** a **Pokud false**, přidejte kroky k provedení na základě toho, zda je splněna podmínka. Například:

   ![Podmínka s cestami "Pokud true" a "Pokud false"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Existující akce můžete přetáhnout do **cest If true** a If **false.**

1. Uložte svou aplikaci logiky.

Tato aplikace logiky nyní odesílá poštu pouze v případě, že nové položky v informačním kanálu RSS splňují váš stav.

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

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete odeslat nebo hlasovat o funkcích a návrzích, navštivte [web pro zpětnou vazbu uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě různých hodnot (příkazy switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakování kroků (smyček)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění nebo sloučení paralelních kroků (větví)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

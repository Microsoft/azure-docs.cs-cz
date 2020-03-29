---
title: Vytvoření nebo spojení paralelních větví pro akce v pracovních postupech
description: Zjistěte, jak vytvořit nebo sloučit paralelní spuštěné větve pro nezávislé akce pracovního postupu v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: c0b1519992ba930382a1987aed185ef3c92eded4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453437"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Vytvoření paralelních větví nebo připojení k nim pro akce pracovního postupu v Aplikacích Azure Logic Apps

Ve výchozím nastavení se akce v pracovních postupech aplikace logiky spouštějí postupně. Chcete-li provádět nezávislé akce současně, můžete vytvořit [paralelní větve](#parallel-branches)a potom se připojit k [těmto větvím](#join-branches) později v toku. 

> [!TIP] 
> Pokud máte aktivační událost, která přijímá pole a chcete spustit pracovní postup pro každou položku pole, můžete *debatovat toto* pole s [vlastností **SplitOn** trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Přidat paralelní větev

Chcete-li spustit nezávislé kroky současně, můžete přidat paralelní větve vedle existujícího kroku. 

![Paralelní spuštění kroků](media/logic-apps-control-flow-branches/parallel.png)

Aplikace logiky čeká na dokončení všech větví před pokračováním pracovního postupu. Paralelní větve jsou spuštěny pouze v případě, že jejich `runAfter` hodnoty vlastností odpovídají stavu dokončeného nadřazeného kroku. Například oba `branchAction1` `branchAction2` a jsou nastaveny `parentAction` tak, `Succeeded` aby byly spuštěny pouze po dokončení se stavem.

> [!NOTE]
> Než začnete, aplikace logiky již musí mít krok, kde můžete přidat paralelní větve.

1. Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Přesuňte ukazatel myši nad šipku nad krokem, kam chcete přidat paralelní větve. Zvolte znaménko **plus** (**+**), které se zobrazí, a pak zvolte **Přidat paralelní větev**. 

   ![Přidat paralelní větev](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Ve vyhledávacím poli vyhledejte a vyberte požadovanou akci.

   ![Vyhledání a vyvýběr u požadované akce](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Vybraná akce se nyní zobrazí v paralelní větvi, například:

   ![Vyhledání a vyvýběr u požadované akce](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Nyní v každé paralelní větvi přidejte požadované kroky. Chcete-li do větve přidat další akci, přesuňte ukazatel pod akci, do které chcete přidat sekvenční akci. Zvolte zobrazilé znaménko **plus** (**+**) a pak vyberte **Přidat akci**.

   ![Přidání sekvenční akce do paralelní větve](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Ve vyhledávacím poli vyhledejte a vyberte požadovanou akci.

   ![Hledání a výběr sekvenční akce](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Vybraná akce se nyní zobrazí v aktuální větvi, například:

   ![Hledání a výběr sekvenční akce](media/logic-apps-control-flow-branches/added-sequential-action.png)

Chcete-li sloučit větve zpět dohromady, [spojte paralelní větve](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definice paralelní větve (JSON)

Pokud pracujete v zobrazení kódu, můžete místo toho definovat paralelní strukturu v definici JSON aplikace logiky, například:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Spojení paralelních větví

Chcete-li sloučit paralelní větve dohromady, stačí přidat krok v dolní části pod všemi větvemi. Tento krok se spustí po dokončení spuštění všech paralelních větví.

![Spojení paralelních větví](media/logic-apps-control-flow-branches/join.png)

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete aplikaci logiky v Návrháři aplikací logiky. 

1. Pod paralelními větvemi, ke kterým se chcete připojit, zvolte **Nový krok**. 

   ![Přidat krok pro připojení](media/logic-apps-control-flow-branches/add-join-step.png)

1. Ve vyhledávacím poli vyhledejte a vyberte požadovanou akci jako krok, který spojuje větve.

   ![Vyhledání a výběr akce, která spojuje paralelní větve](media/logic-apps-control-flow-branches/join-steps.png)

   Paralelní větve jsou nyní sloučeny.

   ![Spojené pobočky](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Definice spojení (JSON)

Pokud pracujete v zobrazení kódu, můžete definovat strukturu spojení v definici JSON aplikace logiky, například:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete odeslat nebo hlasovat o funkcích a návrzích, navštivte [web pro zpětnou vazbu uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění kroků na základě různých hodnot (příkazy switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakování kroků (smyček)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

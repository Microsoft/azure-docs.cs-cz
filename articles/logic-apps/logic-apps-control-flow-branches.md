---
title: Vytvořit nebo připojit paralelních větvích - Azure Logic Apps | Microsoft Docs
description: Jak vytvořit nebo připojit paralelních větvích pro pracovní postupy v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: cfowler
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 40af32b1618ee495cff82ed0a588ad620b759e96
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725543"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Vytvořit nebo připojit paralelních větvích pro akce pracovního postupu v Azure Logic Apps

Ve výchozím vaše akce v pracovních postupech logiku aplikace spouští sekvenčně. K provádění akcí nezávislé ve stejnou dobu, můžete vytvořit [paralelní větve](#parallel-branches)a potom [připojení těchto větví](#join-branches) novější v vaší toku. 

> [!TIP] 
> Pokud máte aktivační událost, která přijímá pole a chcete spustit pracovní postup pro každou položku pole, můžete *debatch* tohoto pole s [ **SplitOn** aktivovat vlastnost](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-a-parallel-branch"></a>Přidat paralelní větev

Ke spuštění nezávislé kroků ve stejnou dobu, můžete přidat paralelních větvích vedle stávající krok. 

![Paralelní spuštění kroky](media/logic-apps-control-flow-branches/parallel.png)

Aplikace logiky čeká všechny větve ukončíte před pokračováním pracovního postupu.
Paralelní větve spustit pouze tehdy, když jejich `runAfter` hodnoty vlastností shodují v kroku dokončení nadřazené stavu. Například pro obě `branchAction1` a `branchAction2` jsou nastaveny na spouštění pouze tehdy, když `parentAction` dokončení s `Succeded` stavu.

> [!NOTE]
> Než začnete, aplikace logiky musíte už mít krok kterém můžete přidat paralelních větvích.

1. V <a href="https://portal.azure.com" target="_blank">portál Azure</a>, otevřete aplikaci logiky v návrháři aplikace logiky.

2. Přesunutím ukazatele myši nad šipku výše v kroku, ve které chcete přidat paralelních větvích.

3. Vyberte **plus** přihlašovací (**+**), zvolte **přidat paralelní větev**a vyberte položku, kterou chcete přidat.

   ![Přidat paralelní větve](media/logic-apps-control-flow-branches/add-parallel-branch.png)

   Vybrané položky se teď zobrazí v paralelní větev.

4. Pro každou paralelní větev přidáte kroky, které chcete. Pokud chcete přidat sekvenční akce paralelní větev, přesuňte ukazatel myši v části Akce, ve které chcete přidat sekvenční akce. Vyberte **plus** (**+**) přihlášení a krok, který chcete přidat.

   ![Přidat sekvenční krok paralelní větve](media/logic-apps-control-flow-branches/add-sequential-action-parallel-branch.png)

5. Sloučit větví zpět společně [připojení paralelních větvích](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Paralelní větev definice (JSON)

Pokud pracujete v zobrazení kódu, můžete definovat strukturu paralelní v definici aplikace logiky JSON místo, například:

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
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Připojení k paralelních větvích

Sloučit paralelních větvích společně, stačí přidáte krok dole v části všechny větve. Tento krok se spouští po všech paralelních větvích dokončit, spuštěná.

![Připojení k paralelních větvích](media/logic-apps-control-flow-branches/join.png)

1. V [portál Azure](https://portal.azure.com), najít a otevřít aplikaci logiky v návrháři aplikace logiky. 

2. V části paralelní větve, které chcete připojit přidejte krok, který chcete provést.

   ![Přidejte krok, který připojí paralelních větvích](media/logic-apps-control-flow-branches/join-steps.png)

   Nyní jsou sloučeny paralelních větvích.

<a name="join-json"></a>

## <a name="join-definition-json"></a>Připojení k definici (JSON)

Pokud pracujete v zobrazení kódu, můžete definovat strukturu spojení v definici aplikace logiky JSON místo, například:

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
* Odeslání nebo hlasovat o funkcích a návrhy, najdete [web pro zasílání názorů uživatele Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spustit kroky na základě různých hodnot (příkazech switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakujte kroky (smyčky)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spustit kroky na základě stavu seskupené akce (oborům)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

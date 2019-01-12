---
title: Vytvořit nebo připojit k paralelních větvích – Azure Logic Apps | Dokumentace Microsoftu
description: Jak vytvořit nebo připojit paralelní větve pro pracovní postupy v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 2e1c155a371fa96e4f772f632a9585948b012e54
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232239"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Vytvořit nebo připojit k paralelní větve pro akce pracovního postupu ve službě Azure Logic Apps

Ve výchozím nastavení akcí v pracovních postupů aplikace logiky spouští sekvenčně. K provádění akcí nezávislé ve stejnou dobu, můžete vytvořit [paralelní větve](#parallel-branches)a potom [připojení těchto větví](#join-branches) dále v toku. 

> [!TIP] 
> Pokud máte trigger, který přijímá pole a chcete spustit pracovní postup pro každou položku pole, můžete si *debatch* toto pole se [ **vlastnost SplitOn** aktivovat vlastnost](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Přidat paralelní větev

Ke spuštění nezávislých kroků ve stejnou dobu, můžete přidat paralelních větvích vedle stávající krok. 

![Kroky při paralelním spuštění](media/logic-apps-control-flow-branches/parallel.png)

Počká, pro všechny větve na dokončení před pokračováním pracovní postup aplikace logiky. Paralelní větve spustit jenom v případě jejich `runAfter` hodnoty vlastností odpovídá stavu na dokončení nadřazené krok. Například obě `branchAction1` a `branchAction2` jsou nastavena pro spuštění pouze tehdy, když `parentAction` dokončí s `Succeeded` stav.

> [!NOTE]
> Než začnete, aplikace logiky musí už mít krok ve kterém můžete přidat paralelních větvích.

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, otevřete v návrháři aplikace logiky aplikace logiky.

1. Přesuňte ukazatel myši na šipku nad krok, ve které chcete přidat paralelních větvích. Zvolte **plus** znaménko (**+**), který se zobrazí a klikněte na tlačítko **přidat paralelní větev**. 

   ![Přidat paralelní větev](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Do vyhledávacího pole vyhledejte a vyberte požadovanou akci.

   ![Vyhledejte a vyberte požadovanou akci](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Vybranou akci nyní zobrazí v paralelní větvi, například:

   ![Vyhledejte a vyberte požadovanou akci](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Teď v jednotlivých paralelních větvích, přidejte kroky, které chcete. Přidání další akce na větev, přesuňte ukazatel v akci, ve které chcete přidat sekvenční akce. Zvolte **plus** (**+**) znak, který se zobrazí a pak vyberte **přidat akci**.

   ![Přidat paralelní větev sekvenční akci](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Do vyhledávacího pole vyhledejte a vyberte požadovanou akci.

   ![Vyhledejte a vyberte sekvenční akce](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Vybranou akci nyní se zobrazí v aktuální větvi, například:

   ![Vyhledejte a vyberte sekvenční akce](media/logic-apps-control-flow-branches/added-sequential-action.png)

Ke sloučení větví zpět společně [připojení paralelních větvích](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definice paralelní větev (JSON)

Pokud pracujete v zobrazení kódu, můžete definovat paralelní konstrukce v definici JSON vaší aplikace logiky místo, například:

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

## <a name="join-parallel-branches"></a>Připojte se k paralelních větvích

Pokud chcete sloučit paralelních větvích, stačí přidáte krok v dolní části v rámci všech větví. Tento krok se spustí po všech paralelních větvích dokončit systémem.

![Připojte se k paralelních větvích](media/logic-apps-control-flow-branches/join.png)

1. V [webu Azure portal](https://portal.azure.com), vyhledání a otevření aplikace logiky v návrháři aplikace logiky. 

1. V části paralelních větvích, které se chcete připojit, zvolte **nový krok**. 

   ![Přidat krok pro připojení](media/logic-apps-control-flow-branches/add-join-step.png)

1. Do vyhledávacího pole vyhledejte a vyberte akci, kterou chcete, aby jako krok, který spojuje větve.

   ![Vyhledejte a vyberte akci, která připojí paralelních větvích](media/logic-apps-control-flow-branches/join-steps.png)

   Paralelních větvích jsou nyní sloučeny.

   ![Připojené k doméně větví](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Připojte se k definici (JSON)

Pokud pracujete v zobrazení kódu, můžete definovat strukturu spojení v definici JSON vaší aplikace logiky místo, například:

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
* Odeslání návrhu nebo hlasování o funkcích a návrhy, najdete v tématu [webu zpětné vazby uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spustit kroky na základě různých hodnot (příkazů přepínače)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakujte kroky (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

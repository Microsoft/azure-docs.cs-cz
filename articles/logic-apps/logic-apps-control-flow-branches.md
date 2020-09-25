---
title: Vytvoření nebo připojení paralelních větví pro akce v pracovních postupech
description: Naučte se, jak vytvořit nebo sloučit paralelní běžící větve pro nezávislé akce pracovního postupu v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 3514ce966e1de9af1741de6b966964aca2599610
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269229"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Vytvoření nebo připojení paralelních větví pro akce pracovního postupu v Azure Logic Apps

Ve výchozím nastavení se vaše akce v pracovních postupech aplikace logiky spouštějí sekvenčně. Chcete-li provést nezávislé akce současně, můžete vytvořit [paralelní větve](#parallel-branches)a poté [tyto větve připojit](#join-branches) později v toku. 

> [!TIP] 
> Pokud máte aktivační událost, která přijímá pole a chcete pro každou položku pole Spustit pracovní postup, můžete toto pole v *dávce* [ **SplitOn** vlastností triggeru](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Přidat paralelní větev

Chcete-li spustit nezávislé kroky současně, můžete přidat paralelní větve vedle existujícího kroku. 

![Paralelní spuštění kroků](media/logic-apps-control-flow-branches/parallel.png)

Vaše aplikace logiky čeká na dokončení všech větví před pokračováním pracovního postupu. Paralelní větve se spouštějí jenom `runAfter` v případě, že hodnoty jejich vlastností odpovídají stavu dokončeného nadřazeného kroku. Například obojí `branchAction1` a `branchAction2` je nastaveno na spouštění pouze v případě, že je `parentAction` dokončeno se `Succeeded` stavem.

> [!NOTE]
> Než začnete, musí mít aplikace logiky již krok, kde můžete přidávat paralelní větve.

1. V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>otevřete aplikaci logiky v návrháři aplikace logiky.

1. Přesuňte ukazatel myši nad šipku nad krok, kam chcete přidat paralelní větve. Zvolte symbol **plus** ( **+** ), který se zobrazí, a pak zvolte **Přidat paralelní větev**. 

   ![Přidat paralelní větev](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Ve vyhledávacím poli vyhledejte a vyberte požadovanou akci.

   ![Snímek obrazovky zobrazující okno zvolit akci v návrháři aplikace logiky](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Vybraná akce se teď zobrazí ve větvi Parallel, například:

   ![Vyhledejte a vyberte akci, kterou chcete.](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Teď v každé paralelní větvi přidejte požadované kroky. Chcete-li přidat další akci do větve, přesuňte ukazatel pod akci, do které chcete přidat sekvenční akci. Vyberte symbol **plus** ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

   ![Přidat následnou akci do paralelní větve](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Ve vyhledávacím poli vyhledejte a vyberte požadovanou akci.

   ![Snímek obrazovky zobrazující okno zvolit akci a vyhledávací pole v návrháři aplikace logiky](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   Vybraná akce se teď zobrazí v rámci aktuální větve, například:

   ![Najít a vybrat sekvenční akci](media/logic-apps-control-flow-branches/added-sequential-action.png)

Ke sloučení větví dohromady [Připojte své paralelní větve](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definice paralelní větve (JSON)

Pokud pracujete v zobrazení kódu, můžete místo toho definovat paralelní strukturu v definici JSON vaší aplikace logiky, například:

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

## <a name="join-parallel-branches"></a>Spojit se s paralelními větvemi

Chcete-li sloučit paralelní větve dohromady, stačí přidat krok v dolní části všech větví. Tento krok se spustí po dokončení všech paralelních větví.

![Spojit se s paralelními větvemi](media/logic-apps-control-flow-branches/join.png)

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete aplikaci logiky v návrháři aplikace logiky. 

1. V části paralelní větve, ke kterým se chcete připojit, vyberte **Nový krok**. 

   ![Přidat krok pro připojení](media/logic-apps-control-flow-branches/add-join-step.png)

1. V poli hledání vyhledejte a vyberte akci, kterou chcete provést, jako krok, který spojuje větve.

   ![Najde a vybere akci, která se spojí s paralelními větvemi.](media/logic-apps-control-flow-branches/join-steps.png)

   Vaše paralelní větve se teď sloučí.

   ![Připojené větve](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Definice spojení (JSON)

Pokud pracujete v zobrazení kódu, můžete místo toho definovat strukturu spojení v definici JSON vaší aplikace logiky, například:

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

## <a name="get-support"></a>Získání podpory

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Chcete-li odesílat nebo hlasovat o funkcích a návrzích, navštivte [web Azure Logic Apps pro zpětnou vazbu uživatelů](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění kroků na základě různých hodnot (příkazy Switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění a opakování kroků (cykly)](../logic-apps/logic-apps-control-flow-loops.md)
* [Spuštění kroků na základě seskupeného stavu akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

---
title: Přidání smyček, které opakování akce nebo proces pole – Azure Logic Apps | Dokumentace Microsoftu
description: Postup vytvoření cyklů, které opakování akce pracovního postupu nebo zpracování pole v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
manager: jeconnoc
ms.date: 01/05/2019
ms.topic: article
ms.openlocfilehash: 7237a9a6a99b57401af40512a6d2e21a3fe49e53
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159481"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Vytvoření cyklů, které opakování akce pracovního postupu nebo zpracování pole v Azure Logic Apps

Ke zpracování pole v aplikaci logiky, můžete vytvořit [smyčka "Foreach"](#foreach-loop). Tato smyčka se opakuje, jednu nebo více akcí pro každou položku v poli. Omezení počtu položek pole, které "Foreach" smyčky může zpracovat, najdete v části [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

Akce opakování, dokud získá některá podmínka splněná nebo změny stavu, můžete vytvořit ["Do" smyčka](#until-loop). Aplikace logiky spouští všechny akce uvnitř smyčky a poté ověří stavu nebo stavu. Pokud je splněna podmínka, cyklus se ukončí. V opačném případě smyčka se opakuje. Omezení počtu "Do" smyčky v aplikaci logiky spustit, najdete v části [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Pokud máte trigger, který přijímá pole a chcete spustit pracovní postup pro každou položku pole, můžete si *debatch* toto pole se [ **vlastnost SplitOn** aktivovat vlastnost](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Smyčka "Foreach"

"Smyčka Foreach" se opakuje, jednu nebo více akcí pro každou položku pole a funguje pouze na pole. Iterací ve smyčce "Foreach" běžet paralelně. Však můžete spustit iterací jeden po druhém nastavením [sekvenční smyčka "Foreach"](#sequential-foreach-loop). 

Tady jsou některé aspekty při použití smyčky "Foreach":

* Ve smyčkách vnořené iterací vždy postupně, ne při paralelním spuštění. Pro spouštění operací pro položky ve smyčce vnořené paralelně, vytvořit a [volat aplikaci logiky podřízené](../logic-apps/logic-apps-http-endpoint.md).

* Předvídatelné výsledky z operací pro proměnné při každé iteraci smyčky získáte spuštěním těchto smyčky postupně. Například při souběžně běžících smyčky skončí, přírůstek, snížení a připojí k proměnné operace vrátíte předvídatelné výsledky. Ale při každé iteraci ve smyčce současně spuštěné tyto operace může vrátit neočekávané výsledky. 

* Použití opakování akce v "Foreach" [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
výraz, který se odkazuje a zpracovat každou položku v poli. Pokud chcete zadat data, která není v poli, pracovní postup aplikace logiky se nezdaří. 

Tento příklad aplikace logiky odešle denní souhrn pro informační kanál RSS webu. Aplikace používá "Foreach" smyčku, která odešle e-mail za každou novou položku.

1. [Vytvořte Tato ukázková aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) pomocí účtu Outlook.com nebo Office 365 Outlook.

2. Mezi RSS aktivovat a odeslat e-mailové akce, přidání smyčky "Foreach". 

   1. Přidání smyčky mezi kroky, přesuňte ukazatel nad šipku mezi tyto kroky. 
   Zvolte **znaménko plus** (**+**), který se zobrazí, vyberte **přidat akci**.

      ![Vyberte "Přidat akci"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Pod vyhledávacím polem vyberte **všechny**. Do vyhledávacího pole zadejte jako filtr "for each". Ze seznamu akcí vyberte tuto akci: **Pro každý – ovládací prvek**

      ![Přidání smyčky "pro každý"](media/logic-apps-control-flow-loops/select-for-each.png)

3. Nyní vytvořte smyčky. V části **vybrat výstup z předchozího postupu** po **Přidat dynamický obsah** seznamu se zobrazí, vyberte **odkazy na informační kanály** pole, které je výstup z RSS trigger. 

   ![Vyberte ze seznamu dynamického obsahu](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Můžete vybrat *pouze* pole výstup z předchozího kroku.

   Vybrané pole se teď zobrazí tady:

   ![Vyberte pole](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Chcete-li spustit akci pro každou položku pole, přetáhněte **odeslat e-mailu** akce do smyčky. 

   Aplikace logiky může vypadat přibližně jako v tomto příkladu:

   ![Přidání kroků do smyčky "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Uložte svou aplikaci logiky. Chcete-li ručním testováním aplikace logiky na panelu nástrojů návrháře zvolte **spustit**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definici smyčky "Foreach" (JSON)

Pokud pracujete v zobrazení kódu pro vaši aplikaci logiky, můžete definovat `Foreach` smyčky v definici JSON vaší aplikace logiky místo, například:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" smyčka: Sequential

Ve výchozím nastavení cykly ve smyčce "Foreach" běžet paralelně. Každý cyklus spouští sekvenčně, nastavte smyčky **sekvenční** možnost. "Foreach" smyčky musí jsou spouštěny postupně po mít člověk vnořené smyčky nebo proměnných uvnitř smyčky kde očekáváte, že předvídatelné výsledky. 

1. V horním pravém rohu smyčky, zvolte **symbol tří teček** (**...** ) > **Nastavení**.

   ![Smyčka "Foreach" tlačítko "..." > "Nastavení"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. V části **řízení souběžnosti**, zapnout **řízení souběžnosti** nastavení **na**. Přesunout **stupeň paralelismu** posuvníku **1**a zvolte **provádí**.

   ![Zapněte řízení souběžnosti](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Pokud pracujete s definice JSON vaší aplikace logiky, můžete použít `Sequential` možnost tak, že přidáte `operationOptions` parametru, například:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Do" smyčka
  
Akce opakování, dokud získá některá podmínka splněná nebo změny stavu, vložte tyto akce do smyčky "Až". Zde jsou uvedeny některé obvyklé scénáře, ve kterém můžete použít "Až" smyčka:

* Volání koncového bodu, dokud se nedostanete odpověď, kterou chcete.

* Vytvořte záznam v databázi. Počkejte, dokud konkrétní pole v tom, že záznam schválení. Pokračujte ve zpracování. 

Spouští v 8:00 hodin každý den, tato aplikace logiky příklad zvýší hodnotu proměnné dokud hodnota proměnné je rovno 10. Aplikace logiky pak odešle e-mailu s potvrzením aktuální hodnotu. 

> [!NOTE]
> Tyto kroky používají Office 365 Outlook, ale mohou využívat kteréhokoli zprostředkovatele e-mailu, který podporuje Logic Apps. 
> [Zkontrolujte na seznam konektorů zde](https://docs.microsoft.com/connectors/). Pokud používáte jiný e-mailový účet, zůstává obecný postup stejný, ale vaše uživatelské rozhraní může vypadat trochu jinak. 

1. Vytvoření prázdné aplikace logiky V návrháři aplikace logiky zvolte pod vyhledávacím polem **všechny**. Vyhledejte "opakování". Ze seznamu triggerů vyberte tento trigger: **Opakování – plán**

   ![Přidání triggeru "Plán – opakování"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Zadejte, když se trigger spustí nastavením interval, četnost a hodina dne. Chcete-li nastavit hodiny, zvolte **zobrazit pokročilé možnosti**.

   ![Nastavit plán opakování](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Vlastnost | Hodnota |
   | -------- | ----- |
   | **Interval** | 1 | 
   | **Frekvence** | Den |
   | **V těchto hodinách** | 8 |
   ||| 

1. Pod triggerem zvolte **nový krok**. Vyhledejte "proměnné" a vyberte tuto akci: **Inicializace proměnné – proměnné**

   ![Přidání akce "Inicializace proměnné - proměnné"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Nastavte vaši proměnnou s těmito hodnotami:

   ![Nastavení vlastností proměnné](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Vlastnost | Hodnota | Popis |
   | -------- | ----- | ----------- |
   | **Název** | Omezení | Název proměnné. | 
   | **Typ** | Integer | Vaše proměnná datový typ | 
   | **Hodnota** | 0 | Počáteční hodnota proměnné | 
   |||| 

1. V části **inicializovat proměnnou** akce, zvolte **nový krok**. 

1. Pod vyhledávacím polem vyberte **všechny**. Vyhledejte "do" a vyberte tuto akci: **Dokud – ovládací prvek**

   ![Přidání "smyčky do"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Vytvoření smyčky ukončovací podmínky tak, že vyberete **Limit** proměnné a **rovná** operátor. Zadejte **10** jako hodnotu porovnání.

   ![Sestavení ukončovací podmínky pro ukončení smyčky](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Uvnitř smyčka, zvolte **přidat akci**. 

1. Pod vyhledávacím polem vyberte **všechny**. Vyhledejte "proměnné" a vyberte tuto akci: **Zvýšení hodnoty proměnné – proměnné**

   ![Přidání akce pro zvyšování hodnoty proměnné](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Pro **název**, vyberte **Limit** proměnné. Pro **hodnota**, zadejte hodnotu "1". 

   !["Limit" přírůstkem 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Mimo a v rámci smyčky, zvolte **nový krok**. 

1. Pod vyhledávacím polem vyberte **všechny**. Najděte a přidejte akci, která se odešle e-mailu, například: 

   ![Přidejte akci, která odešle e-mailu](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Pokud budete vyzváni, přihlaste se k e-mailovému účtu.

1. Nastavení e-mailu vlastností akce. Přidat **Limit** proměnné subjektu. Tímto způsobem můžete ověřit, že se že aktuální hodnota proměnné splňuje zadanou podmínku, třeba:

    ![Nastavit vlastnosti e-mailů](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Vlastnost | Hodnota | Popis |
    | -------- | ----- | ----------- | 
    | **Komu** | *<email-address@domain>* | příjemce e-mailovou adresu. Pro účely testování použijte svou vlastní e-mailovou adresu. | 
    | **Předmět** | Aktuální hodnota pro "Limit" **Limit** | Zadejte předmět e-mailu. V tomto příkladu Ujistěte se, že složku zahrnujete **Limit** proměnné. | 
    | **Text** | <*obsah e-mailu*> | Zadejte obsah e-mailové zprávy, které chcete odeslat. V tomto příkladu zadejte jakýkoli text, který vám vyhovuje. | 
    |||| 

1. Uložte svou aplikaci logiky. Chcete-li ručním testováním aplikace logiky na panelu nástrojů návrháře zvolte **spustit**.

    Po svoji logiku spustí, dostanete e-mail s obsahem, který jste zadali:

    ![Přijatých e-mailů](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Zabránit nekonečná smyčka

"Až" smyčka má výchozí omezení, které zastavit provádění, pokud některá z těchto podmínek dojít:

| Vlastnost | Výchozí hodnota | Popis | 
| -------- | ------------- | ----------- | 
| **Počet** | 60 | Nejvyšší počet cyklů, které běžet, než opakování ve smyčce ukončeno. Výchozí hodnota je 60 cykly. | 
| **časový limit** | PT1H | Většina množství času spuštění smyčky před smyčku ukončí. Výchozí hodnota je jedna hodina a je zadaný ve formátu ISO 8601. <p>Hodnota časového limitu se vyhodnocuje pro každý cyklus smyčky. Pokud žádnou akci ve smyčce trvá déle než časový limit, aktuální cyklu nezastaví. Do dalšího cyklu však nespustí, protože limit podmínka splněna není. | 
|||| 

Chcete-li změnit tato výchozí omezení, zvolte **zobrazit pokročilé možnosti** ve tvaru akci opakovat.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Do" definice (JSON)

Pokud pracujete v zobrazení kódu pro vaši aplikaci logiky, můžete definovat `Until` smyčky v definici JSON vaší aplikace logiky místo, například:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

V tomto příkladu "Až" zavolá koncový bod HTTP, který se vytvoří prostředek smyčky. Cyklus se ukončí po návratu textu odpovědi HTTP s `Completed` stav. Pokud chcete zabránit nekonečné smyčky, smyčky také zastaví, pokud některá z těchto podmínek stát:

* Smyčky spustila 10krát podle `count` atribut. Výchozí hodnota je 60 x. 

* Smyčky běželo dvě hodiny, jak jsou určené `timeout` atribut ve formátu ISO 8601. Výchozí hodnota je jedna hodina.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* K odeslání návrhu nebo hlasování o funkcích a návrhy, [webu zpětné vazby uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spustit kroky na základě různých hodnot (příkazů přepínače)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění nebo sloučit paralelními kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

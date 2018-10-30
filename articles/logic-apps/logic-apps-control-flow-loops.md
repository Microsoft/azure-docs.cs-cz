---
title: Přidání smyček, které opakování akce nebo proces pole – Azure Logic Apps | Dokumentace Microsoftu
description: Postup vytvoření cyklů, které opakování akce pracovního postupu nebo zpracování pole v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 5ba5e5abef4ebdc58c44cbe7f5ba584efe8abfc7
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233102"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Vytvoření cyklů, které opakování akce pracovního postupu nebo zpracování pole v Azure Logic Apps

K iteraci v rámci pole ve vaší aplikaci logiky, můžete použít [smyčka "Foreach"](#foreach-loop) nebo [sekvenční smyčka "Foreach"](#sequential-foreach-loop). Iterace pro standardní smyčky "Foreach" běžet paralelně, zatímco iterací pro sekvenční smyčka "Foreach" spustit postupně po jednom. Maximální počet položek pole, které "Foreach" smyčky může zpracovat v běh aplikace logiky jeden, naleznete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Pokud máte trigger, který přijímá pole a chcete spustit pracovní postup pro každou položku pole, můžete si *debatch* toto pole se [ **vlastnost SplitOn** aktivovat vlastnost](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Chcete-li akce opakujte, dokud je podmínka splněna nebo u některých stavů, se změnila, použijte ["Do" smyčka](#until-loop). Aplikace logiky nejprve provede všechny akce uvnitř smyčky a potom zkontrolujte tuto podmínku jako poslední krok. Pokud je splněna podmínka, cyklus se ukončí. V opačném případě smyčka se opakuje. Maximální počet "Do" smyčky v aplikaci logiky jednoho spuštění, naleznete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Smyčka "Foreach"

Opakování akce pro každou položku v poli, použijte smyčku "Foreach" do pracovního postupu aplikace logiky. Může obsahovat více akcí ve smyčce "Foreach" a je možné vnořovat smyčky "Foreach" uvnitř sebe navzájem. Ve výchozím nastavení cykly v standardní smyčky "Foreach" běžet paralelně. Maximální počet paralelních cyklů tento "Foreach" smyčky poběží, najdete v části [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> Smyčka "Foreach" funguje pouze s polem a akcí ve smyčce použijte `@item()` odkaz ke zpracování každé položky v poli. Pokud chcete zadat data, která není v poli, pracovní postup aplikace logiky se nezdaří. 

Například tato aplikace logiky odešle vám denní souhrn z informačního kanálu RSS webu. Aplikace používá "Foreach" smyčku, která se odešle e-mailu pro každý nový položka nalezena.

1. [Vytvořte Tato ukázková aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) pomocí účtu Outlook.com nebo Office 365 Outlook.

2. Mezi RSS aktivovat a odeslat e-mailové akce, přidání smyčky "Foreach". 

   Přidání smyčky mezi kroky, přesuňte ukazatel nad šipku, ve které chcete přidat smyčky. 
   Zvolte **znaménko plus** (**+**), který se zobrazí, klikněte na tlačítko **přidat pro každou**.

   ![Přidání smyčky "Foreach" mezi kroky](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. Nyní vytvořte smyčky. V části **vybrat výstup z předchozího postupu** po **Přidat dynamický obsah** seznamu se zobrazí, vyberte **odkazy na informační kanály** pole, které je výstup z RSS trigger. 

   ![Vyberte ze seznamu dynamického obsahu](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Můžete vybrat *pouze* pole výstup z předchozího kroku.

   Vybrané pole se teď zobrazí tady:

   ![Vyberte pole](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Chcete-li provést akci pro každou položku pole, přetáhněte **odeslat e-mailu** akce do **pro každou** smyčky. 

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
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" smyčka: sekvenční

Ve výchozím nastavení spouští každý cyklus ve smyčce "Foreach" paralelně pro každou položku pole. Spustit každý cyklus postupně, nastavte **sekvenční** možnost v smyčku "Foreach".

1. V horním pravém rohu smyčky, zvolte **symbol tří teček** (**...** ) > **Nastavení**.

   ![Smyčka "Foreach" tlačítko "..." > "Nastavení"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Zapnout **sekvenční** nastavení, klikněte na tlačítko **provádí**.

   ![Zapnutí nastavení sekvenční smyčka "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Můžete také nastavit **operationOptions** parametr `Sequential` v definici JSON vaší aplikace logiky. Příklad:

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Do" smyčka
  
Akce opakování, dokud je podmínka splněna nebo u některých stavů, se změnila, použijte "Až" smyčka do pracovního postupu aplikace logiky. Tady jsou některé běžné případy použití, ve kterém můžete použít "Až" smyčka:

* Volejte koncový bod, dokud se nedostanete odpovědi, které chcete.
* Vytvořit záznam v databázi, počkejte, dokud konkrétní pole v tom, že záznam schválení a pokračovat ve zpracování. 

Například v 8:00 hodin každý den, tato aplikace logiky zvýší hodnotu proměnné dokud hodnota proměnné je rovno 10. Potom aplikace logiky odešle e-mailu s potvrzením aktuální hodnotu. Přestože tento příklad používá Office 365 Outlook, které mohou využívat kteréhokoli zprostředkovatele e-mailu podporovaného v Logic Apps ([podívejte na seznam konektorů zde](https://docs.microsoft.com/connectors/)). Pokud použijete jiný e-mailový účet, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit. 

1. Vytvoření prázdné aplikace logiky V návrháři aplikace logiky, vyhledejte "recurrence" a vyberte tento trigger: **plán – opakování** 

   ![Přidání triggeru "Plán – opakování"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Zadejte, když se trigger spustí nastavením interval, četnost a hodina dne. Chcete-li nastavit hodiny, zvolte **zobrazit pokročilé možnosti**.

   ![Přidání triggeru "Plán – opakování"](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Vlastnost | Hodnota |
   | -------- | ----- |
   | **Interval** | 1 | 
   | **Frekvence** | Den |
   | **V těchto hodinách** | 8 |
   ||| 

3. Pod triggerem zvolte **nový krok** > **přidat akci**. Vyhledejte "proměnné" a pak vyberte tuto akci: **proměnné – inicializovat proměnnou**

   ![Přidat "Proměnné – inicializovat proměnnou" akce](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Nastavte vaši proměnnou s těmito hodnotami:

   ![Nastavení vlastností proměnné](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Vlastnost | Hodnota | Popis |
   | -------- | ----- | ----------- |
   | **Název** | Omezení | Název proměnné. | 
   | **Typ** | Integer | Vaše proměnná datový typ | 
   | **Hodnota** | 0 | Počáteční hodnota proměnné | 
   |||| 

5. V části **inicializovat proměnnou** akce, zvolte **nový krok** > **Další**. Vyberte tuto smyčku: **přidat "provádět dokud"**

   ![Přidat cyklus "provádět dokud"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Vytvoření smyčky ukončovací podmínky tak, že vyberete **Limit** proměnné a **rovná** operátor. Zadejte **10** jako hodnotu porovnání.

   ![Sestavení ukončovací podmínky pro ukončení smyčky](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. Uvnitř smyčka, zvolte **přidat akci**. Vyhledejte "proměnné" a pak přidat tuto akci: **proměnné – zvýšení hodnoty proměnné**

   ![Přidání akce pro zvyšování hodnoty proměnné](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. Pro **název**, vyberte **Limit** proměnné. Pro **hodnota**, zadejte hodnotu "1". 

   !["Limit" přírůstkem 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. V části, ale mimo smyčku přidejte akci, která odešle e-mail. Pokud budete vyzváni, přihlaste se k e-mailovému účtu.

   ![Přidejte akci, která odešle e-mailu](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Nastavte vlastnosti v e-mailu. Přidat **Limit** proměnné subjektu. Tímto způsobem můžete ověřit, že se že aktuální hodnota proměnné splňuje zadanou podmínku, třeba:

    ![Nastavit vlastnosti e-mailů](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Vlastnost | Hodnota | Popis |
    | -------- | ----- | ----------- | 
    | **Komu** | *<email-address@domain>* | příjemce e-mailovou adresu. Pro účely testování použijte svou vlastní e-mailovou adresu. | 
    | **Předmět** | Aktuální hodnota pro "Limit" **Limit** | Zadejte předmět e-mailu. V tomto příkladu Ujistěte se, že složku zahrnujete **Limit** proměnné. | 
    | **Text** | <*obsah e-mailu*> | Zadejte obsah e-mailové zprávy, které chcete odeslat. V tomto příkladu zadejte jakýkoli text, který vám vyhovuje. | 
    |||| 

11. Uložte svou aplikaci logiky. Chcete-li ručním testováním aplikace logiky na panelu nástrojů návrháře zvolte **spustit**.

    Po svoji logiku spustí, dostanete e-mail s obsahem, který jste zadali:

    ![Přijatých e-mailů](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Zabránit nekonečná smyčka

"Až" smyčka má výchozí omezení, které zastavit provádění, pokud některá z těchto podmínek dojít:

| Vlastnost | Výchozí hodnota | Popis | 
| -------- | ------------- | ----------- | 
| **Počet** | 60 | Maximální počet cyklů, které běžet, než opakování ve smyčce ukončeno. Výchozí hodnota je 60 cykly. | 
| **časový limit** | PT1H | Maximální množství času spuštění smyčky před smyčku ukončí. Výchozí hodnota je jedna hodina a je zadaný ve formátu ISO 8601. <p>Hodnota časového limitu se vyhodnocuje pro každý cyklus smyčky. Pokud žádnou akci ve smyčce trvá déle než časový limit, nebude zastavit aktuální cyklu, ale do dalšího cyklu nespustí, protože limit podmínka splněna není. | 
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
        },
    }
},
```

Například Tato smyčka "Až" zavolá koncový bod HTTP, který se vytvoří prostředek a zastaví, když textu odpovědi HTTP, vrátí se stavem "Dokončeno". Pokud chcete zabránit nekonečné smyčky, smyčky také zastaví, pokud některá z těchto podmínek stát:

* Byl cyklus proveden 10krát podle `count` atribut. Výchozí hodnota je 60 x. 
* Smyčky se pokusil spustit dvě hodiny, jak jsou určené `timeout` atribut ve formátu ISO 8601. Výchozí hodnota je jedna hodina.
  
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
},
```

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* K odeslání návrhu nebo hlasování o funkcích a návrhy, [webu zpětné vazby uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* [Spustit kroky na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spustit kroky na základě různých hodnot (příkazů přepínače)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění nebo sloučit paralelními kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

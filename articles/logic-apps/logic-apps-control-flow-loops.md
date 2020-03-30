---
title: Přidání smyček pro opakování akcí
description: Vytváření smyček opakujících akce pracovních postupů nebo zpracovávajících pole v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270573"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Vytváření smyček opakujících akce pracovních postupů nebo zpracovávajících pole v Azure Logic Apps

Chcete-li zpracovat pole v aplikaci logiky, můžete vytvořit [smyčku "Foreach"](#foreach-loop). Tato smyčka opakuje jednu nebo více akcí u každé položky v poli. Omezení počtu položek pole, které mohou smyčky Foreach zpracovat, naleznete v tématu [Limity a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

Chcete-li opakovat akce, dokud není splněna podmínka nebo se nezmění stav, můžete vytvořit [smyčku "Do"](#until-loop). Aplikace logiky nejprve spustí všechny akce uvnitř smyčky a potom zkontroluje podmínku nebo stav. Pokud je podmínka splněna, smyčka se zastaví. V opačném případě se smyčka opakuje. Omezení počtu smyček "Do" v aplikaci logiky spustit, naleznete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Pokud máte aktivační událost, která přijímá pole a chcete spustit pracovní postup pro každou položku pole, můžete *debatovat toto* pole s [vlastností **SplitOn** trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Smyčka "Foreach"

"Foreach smyčky" opakuje jednu nebo více akcí na každou položku pole a funguje pouze na pole. Iterace ve smyčce "Foreach" spustit paralelně. Můžete však spustit iterace jeden po druhém nastavením [sekvenční smyčky "Foreach"](#sequential-foreach-loop). 

Zde jsou některé důležité informace při použití smyčky "Foreach":

* Ve vnořených smyčkách se iterace vždy spouštějí postupně, ne paralelně. Chcete-li spustit operace paralelně pro položky v vnořené smyčky, vytvořte a [zavolejte podřízenou aplikaci logiky](../logic-apps/logic-apps-http-endpoint.md).

* Chcete-li získat předvídatelné výsledky z operací na proměnné během každé opakování smyčky, spusťte tyto smyčky postupně. Například když souběžně spuštěná smyčka končí, přírůstek, snížení a připojení k operacím proměnné vrátí předvídatelné výsledky. Však během každé iteraci v souběžně spuštěné smyčky tyto operace může vrátit nepředvídatelné výsledky. 

* Akce ve smyčce "Foreach" používají[`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
výraz pro odkaz a zpracování každé položky v poli. Pokud zadáte data, která není v poli, pracovní postup aplikace logiky se nezdaří. 

Tento příklad aplikace logiky odešle denní souhrn pro web RSS feed. Aplikace používá smyčku "Foreach", která odešle e-mail pro každou novou položku.

1. [Vytvořte tuto ukázkovou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) s Outlook.com nebo účet Outlooku Office 365.

2. Mezi aktivační událost RSS a odeslat e-mailovou akci, přidejte "Foreach" smyčky. 

   1. Chcete-li přidat smyčku mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. 
   Vyberte **znaménko plus** (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

      ![Vyberte "Přidat akci"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Pod vyhledávacím polem zvolte **Vše**. Do vyhledávacího pole zadejte jako filtr "pro každého". Ze seznamu akcí vyberte tuto akci: **Pro každou – ovládací prvek**

      ![Přidat "Pro každý" smyčku](media/logic-apps-control-flow-loops/select-for-each.png)

3. Nyní sestavte smyčku. V části **Vybrat výstup z předchozích kroků** po zobrazení seznamu Přidat dynamický **obsah** vyberte pole **Odkazů informačního kanálu,** které je výstupem z aktivační události RSS. 

   ![Výběr ze seznamu dynamického obsahu](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Z předchozího kroku můžete vybrat *pouze* výstupy pole.

   Zde se zobrazí vybrané pole:

   ![Vybrat pole](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Chcete-li spustit akci pro každou položku pole, přetáhněte do smyčky akci **Odeslat e-mail.** 

   Aplikace logiky může vypadat podobně jako v tomto příkladu:

   ![Přidání kroků do smyčky "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Uložte svou aplikaci logiky. Chcete-li ručně otestovat aplikaci logiky, na panelu nástrojů návrháře zvolte **Spustit**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definice smyčky "Foreach" (JSON)

Pokud pracujete v zobrazení kódu pro aplikaci logiky, můžete místo toho definovat `Foreach` smyčku v definici JSON aplikace logiky, například:

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

## <a name="foreach-loop-sequential"></a>"Foreach" smyčka: Sekvenční

Ve výchozím nastavení cykly ve smyčce "Foreach" spustit paralelně. Chcete-li spustit každý cyklus postupně, nastavte možnost **sekvenční** smyčky. "Foreach" smyčky musí běžet postupně, pokud máte vnořené smyčky nebo proměnné uvnitř smyčky, kde očekáváte předvídatelné výsledky. 

1. V pravém horním rohu smyčky zvolte **elipsy** (**...**) > **Nastavení**.

   ![Ve smyčce "Foreach" zvolte "..." > "Nastavení"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. V části **Řízení souběžnosti**zapněte nastavení **Řízení souběžnosti** na **Zapnuto**. Posuňte jezdec **Stupeň paralelismu** na **1**a zvolte **Hotovo**.

   ![Zapnutí řízení souběžnosti](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Pokud pracujete s definicí JSON aplikace logiky, `Sequential` můžete tuto `operationOptions` možnost použít přidáním parametru, například:

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

## <a name="until-loop"></a>"Do" smyčky
  
Chcete-li spustit a opakovat akce, dokud není splněna podmínka nebo se změní stav, vložte tyto akce do smyčky "Do". Aplikace logiky nejprve spustí všechny akce uvnitř smyčky a potom zkontroluje podmínku nebo stav. Pokud je podmínka splněna, smyčka se zastaví. V opačném případě se smyčka opakuje.

Zde jsou některé běžné scénáře, kde můžete použít smyčku "Do":

* Zavolejte koncovému bodu, dokud nedostanete požadovanou odpověď.

* Vytvořte záznam v databázi. Počkejte, až bude schváleno určité pole v tomto záznamu. Pokračujte ve zpracování. 

Počínaje 8:00 am každý den, tento příklad aplikace logiky zintáží proměnnou, dokud hodnota proměnné se rovná 10. Aplikace logiky pak odešle e-mail, který potvrzuje aktuální hodnotu. 

> [!NOTE]
> Tyto kroky používají Office 365 Outlook, ale můžete použít libovolného poskytovatele e-mailu, který Logic Apps podporuje. 
> [Zkontrolujte seznam konektorů zde](https://docs.microsoft.com/connectors/). Pokud používáte jiný e-mailový účet, obecné kroky zůstanou stejné, ale vaše ui může vypadat trochu jinak. 

1. Vytvoření prázdné aplikace logiky V Návrháři aplikací logiky vyberte pod vyhledávacím polem **možnost Vše**. Vyhledejte "opakování". 
   Ze seznamu aktivačních událostí vyberte tuto aktivační událost: **Opakování – plán**

   ![Přidat aktivační událost "Opakování - plán"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Určete, kdy se aktivační událost aktivuje nastavením intervalu, frekvence a hodiny dne. Chcete-li nastavit hodinu, zvolte **Zobrazit upřesňující možnosti**.

   ![Nastavit plán opakování](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Vlastnost | Hodnota |
   | -------- | ----- |
   | **Interval** | 1 | 
   | **Frequency** | Den |
   | **V těchto hodinách** | 8 |
   ||| 

1. Pod aktivační událostí zvolte **Nový krok**. 
   Hledat "proměnné" a vybrat tuto akci: **Inicializovat proměnnou - Proměnné**

   ![Přidat akci "Inicializovat proměnnou - proměnné"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Nastavte proměnnou s těmito hodnotami:

   ![Nastavení vlastností proměnných](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Vlastnost | Hodnota | Popis |
   | -------- | ----- | ----------- |
   | **Název** | Omezení | Název proměnné | 
   | **Typ** | Integer | Datový typ proměnné | 
   | **Hodnotu** | 0 | Počáteční hodnota proměnné | 
   |||| 

1. V části **Akce Inicializovat proměnnou** zvolte **Nový krok**. 

1. Pod vyhledávacím polem zvolte **Vše**. Vyhledejte "do" a vyberte tuto akci: **Do – Řízení**

   ![Přidat "Do" smyčky](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Vytvořte podmínku ukončení smyčky výběrem proměnné **Limit** a **operátor je stejný.** 
   Jako porovnávací hodnotu zadejte **hodnotu** 10.

   ![Sestavení výstupní podmínky pro zastavení smyčky](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Uvnitř smyčky zvolte **Přidat akci**. 

1. Pod vyhledávacím polem zvolte **Vše**. Hledat "proměnné" a vybrat tuto akci: **Přírůstek proměnná - Proměnné**

   ![Přidat akci pro proměnnou zvýšení](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. V **části Název**vyberte proměnnou **Limit.** Do **pole Hodnota**zadejte "1". 

     ![Přírůstek "Limit" o 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Venku a pod smyčkou zvolte **Nový krok**. 

1. Pod vyhledávacím polem zvolte **Vše**. 
     Vyhledání a přidání akce, která odesílá e-mail, například: 

     ![Přidat akci, která odešle e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Pokud budete vyzváni, přihlaste se k e-mailovému účtu.

1. Nastavte vlastnosti akce e-mailu. Přidejte proměnnou **Limit** k předmětu. Tímto způsobem můžete potvrdit aktuální hodnotu proměnné, která splňuje zadanou podmínku, například:

      ![Nastavení vlastností e-mailu](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Vlastnost | Hodnota | Popis |
      | -------- | ----- | ----------- | 
      | **Akce** | *\<>domény\@e-mailové adresy* | E-mailová adresa příjemce. Pro testování použijte vlastní e-mailovou adresu. | 
      | **Subjekt** | Aktuální hodnota pro "Limit" je **Limit** | Zadejte předmět e-mailu. V tomto příkladu se ujistěte, že zahrnete proměnnou **Limit.** | 
      | **Text** | <*obsah e-mailu*> | Zadejte obsah e-mailové zprávy, který chcete odeslat. Do tohoto příkladu zadejte jakýkoli text, který se vám líbí. | 
      |||| 

1. Uložte svou aplikaci logiky. Chcete-li ručně otestovat aplikaci logiky, na panelu nástrojů návrháře zvolte **Spustit**.

      Po spuštění logiky dostanete e-mail s obsahem, který jste zadali:

      ![Přijatý e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Zabraňte nekonečným smyčkám

"Do" smyčka má výchozí limity, které zastaví provádění, pokud dojde k některé z těchto podmínek:

| Vlastnost | Výchozí hodnota | Popis | 
| -------- | ------------- | ----------- | 
| **Počet** | 60 | Nejvyšší počet smyček, které běží před ukončením smyčky. Výchozí hodnota je 60 cyklů. | 
| **Časový limit** | PT1H | Nejvíce množství času ke spuštění smyčky před ukončenísmyčky. Výchozí hodnota je jedna hodina a je určena ve formátu ISO 8601. <p>Hodnota časového oběhu je vyhodnocena pro každý cyklus smyčky. Pokud jakákoli akce ve smyčce trvá déle než limit časového limitu, aktuální cyklus se nezastaví. Další cyklus se však nespustí, protože není splněna podmínka limitu. | 
|||| 

Chcete-li tyto výchozí limity změnit, zvolte **Zobrazit rozšířené možnosti** v obrazci akce smyčky.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definice "Do" (JSON)

Pokud pracujete v zobrazení kódu pro aplikaci logiky, můžete místo toho definovat smyčku `Until` v definici JSON aplikace logiky, například:

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

Tento příklad "Do" smyčka volá koncový bod HTTP, který vytvoří prostředek. Smyčka se zastaví, když se `Completed` tělo odpovědi HTTP vrátí se stavem. Aby se zabránilo nekonečným smyčkám, smyčka se také zastaví, pokud dojde k některé z těchto podmínek:

* Smyčka byla spuštěna 10krát `count` podle atributu. Výchozí hodnota je 60krát. 

* Smyčka běžela dvě hodiny podle `timeout` atributu ve formátu ISO 8601. Výchozí hodnota je jedna hodina.
  
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
* Chcete-li odeslat nebo hlasovat o funkcích a návrzích, [web pro zpětnou vazbu uživatelů Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění kroků na základě různých hodnot (příkazy switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spuštění nebo sloučení paralelních kroků (větví)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spustit kroky na základě stavu seskupené akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

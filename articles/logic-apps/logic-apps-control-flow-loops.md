---
title: Přidat smyčky pro opakování akcí
description: Vytváření smyček opakujících akce pracovních postupů nebo zpracovávajících pole v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5bd637f4e4a786cd4cba0f70c4b2349e354469fd
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657478"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Vytváření smyček opakujících akce pracovních postupů nebo zpracovávajících pole v Azure Logic Apps

Chcete-li zpracovat pole ve vaší aplikaci logiky, můžete vytvořit [smyčku "foreach"](#foreach-loop). Tato smyčka opakuje jednu nebo více akcí u každé položky v poli. Omezení počtu položek pole, které můžou smyčky "foreach" zpracovat, najdete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

Chcete-li opakovat akce, dokud není splněna podmínka nebo změny stavu, můžete vytvořit [smyčku "do"](#until-loop). Vaše aplikace logiky nejdřív spustí všechny akce uvnitř smyčky a pak zkontroluje podmínku nebo stav. Pokud je podmínka splněna, smyčka se zastaví. V opačném případě se smyčka opakuje. Pro omezení počtu "až" smyček v běhu aplikace logiky si přečtěte část [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Pokud máte aktivační událost, která přijímá pole a chcete pro každou položku pole Spustit pracovní postup, můžete toto pole v *dávce* [ **SplitOn** vlastností triggeru](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Smyčka foreach

"Foreach Loop" opakuje jednu nebo více akcí u každé položky pole a funguje pouze pro pole. Zde jsou některé okolnosti použití smyček "foreach":

* Ve výchozím nastavení se iterace ve smyčce "foreach" spouští ve stejnou dobu, nebo paralelně. Toto chování se liší od [Automatizace napájení u **všech** cyklů](/power-automate/apply-to-each) , kde jsou iterace spouštěny po jednom, nebo sekvenčně. Můžete ale [nastavit sekvenční iterace smyčky "foreach"](#sequential-foreach-loop). Například pokud chcete pozastavit další iteraci ve smyčce "foreach" pomocí [akce zpoždění](../connectors/connectors-native-delay.md), je nutné nastavit smyčku tak, aby běžela sekvenčně.

  Výjimkou z výchozího chování jsou vnořené smyčky, kde jsou iterace vždy spouštěny sekvenčně, nikoli paralelně. K paralelnímu spuštění operací pro položky ve vnořené smyčce vytvořte a [zavolejte podřízenou aplikaci logiky](../logic-apps/logic-apps-http-endpoint.md).

* Chcete-li získat předvídatelné výsledky z operací na proměnné během každé iterace smyčky, spusťte cykly postupně. Například když cyklus končí souběžně, operace zvýšení, snížení a připojení k proměnným operaci vrátí předvídatelné výsledky. Během každé iterace v souběžně běžící smyčce ale můžou tyto operace vracet nepředvídatelné výsledky. 

* Akce ve smyčce foreach používají [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
výraz pro odkazování a zpracování každé položky v poli. Pokud zadáte data, která nejsou v poli, pracovní postup aplikace logiky se nezdařil. 

Tato ukázková aplikace logiky pošle denní souhrn informačního kanálu RSS webu. Aplikace používá smyčku foreach, která pro každou novou položku pošle e-mail.

1. [Tuto ukázkovou aplikaci logiky vytvoříte](../logic-apps/quickstart-create-first-logic-app-workflow.md) pomocí účtu Outlook.com nebo pracovního nebo školního účtu.

2. Mezi akcí aktivace a odeslání e-mailu RSS přidejte smyčku foreach. 

   1. Chcete-li přidat smyčku mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky. 
   Vyberte symbol **plus** ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

      ![Vyberte přidat akci.](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. V poli Hledat vyberte možnost **vše**. Do vyhledávacího pole zadejte jako filtr "for each". V seznamu akce vyberte tuto akci: **pro každý ovládací prvek** .

      ![Přidat smyčku For Each](media/logic-apps-control-flow-loops/select-for-each.png)

3. Nyní vytvořte smyčku. V části **vybrat výstup z předchozích kroků** po zobrazení seznamu **Přidat dynamický obsah** vyberte pole odkazy na **informační kanály** , které je výstupem z triggeru RSS. 

   ![Vybrat ze seznamu dynamického obsahu](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Z předchozího kroku můžete vybrat *jenom* výstupy polí.

   Vybrané pole se teď zobrazí tady:

   ![Vybrat pole](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Chcete-li spustit akci pro každou položku pole, přetáhněte akci **Odeslat e-mail** do smyčky. 

   Vaše aplikace logiky může vypadat přibližně jako v tomto příkladu:

   ![Přidání kroků do smyčky foreach](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Uložte aplikaci logiky. Pokud chcete aplikaci logiky otestovat ručně, na panelu nástrojů návrháře klikněte na **Spustit**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Foreach – definice smyčky (JSON)

Pokud pracujete v zobrazení kódu aplikace logiky, můžete `Foreach` místo toho definovat smyčku v definici JSON vaší aplikace logiky, například:

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

## <a name="foreach-loop-sequential"></a>Smyčka foreach: sekvenční

Ve výchozím nastavení se cyklická smyčka "foreach" spouští paralelně. Chcete-li každý cyklus spustit postupně, nastavte možnost **sekvenční** smyčky. Smyčky "foreach" musí běžet sekvenčně, pokud máte vnořené smyčky nebo proměnné uvnitř smyček, kde očekáváte předvídatelné výsledky. 

1. V pravém horním rohu smyčky vyberte **tři tečky** (**...**) > **Nastavení**.

   ![Na smyčce "foreach" vyberte možnost "..." > "nastavení".](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. V části **řízení souběžnosti**přepněte nastavení **řízení souběžnosti** na **zapnuto**. Přesuňte posuvník **stupeň paralelismus** na **1**a potom vyberte **Hotovo**.

   ![Zapnout řízení souběžnosti](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Pokud pracujete s definicí JSON vaší aplikace logiky, můžete použít `Sequential` možnost přidáním `operationOptions` parametru, například:

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

## <a name="until-loop"></a>Smyčka "do"
  
Chcete-li spustit a opakovat akce, dokud není splněna podmínka, nebo dojde ke změně stavu, vložte tyto akce do smyčky "do". Vaše aplikace logiky nejdřív spustí všechny akce uvnitř smyčky a pak zkontroluje podmínku nebo stav. Pokud je podmínka splněna, smyčka se zastaví. V opačném případě se smyčka opakuje.

Tady je několik běžných scénářů, kde můžete použít smyčku "do":

* Zavolejte koncový bod, dokud nezískáte odpověď, kterou požadujete.

* Vytvoří záznam v databázi. Počkejte na schválení konkrétního pole v záznamu. Pokračovat ve zpracování. 

Počínaje 8:00 ráno každý den Tato ukázková aplikace logiky zvýší proměnnou, dokud hodnota proměnné není rovna 10. Aplikace logiky pak pošle e-mail s potvrzením aktuální hodnoty. 

> [!NOTE]
> Tyto kroky používají Office 365 Outlook, ale můžete použít libovolného poskytovatele e-mailu, který Logic Apps podporuje. 
> [Tady se můžete podívat na seznam konektorů](/connectors/). Pokud použijete jiný e-mailový účet, obecné kroky zůstanou stejné, ale vaše uživatelské rozhraní může vypadat trochu jinak. 

1. Vytvoření prázdné aplikace logiky V návrháři aplikace logiky pod vyhledávacím polem vyberte **vše**. Vyhledejte "opakování". 
   V seznamu triggery vyberte tuto aktivační událost: **opakování – plán**

   ![Přidat aktivační událost opakování – plán](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Určete, kdy se Trigger aktivuje nastavením intervalu, frekvence a hodiny dne. Chcete-li nastavit hodinu, vyberte možnost **Zobrazit upřesňující možnosti**.

   ![Nastavit plán opakování](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Vlastnost | Hodnota |
   | -------- | ----- |
   | **Interval** | 1 | 
   | **Frekvence** | Den |
   | **V těchto hodinách** | 8 |
   ||| 

1. V části Trigger vyberte **Nový krok**. 
   Vyhledejte "proměnné" a vyberte tuto akci: **inicializovat proměnné** proměnné

   ![Přidat akci inicializovat proměnné proměnné](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Nastavte proměnnou pomocí těchto hodnot:

   ![Nastavit vlastnosti proměnné](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Vlastnost | Hodnota | Popis |
   | -------- | ----- | ----------- |
   | **Název** | Omezení | Název vaší proměnné | 
   | **Typ** | Integer | Datový typ proměnné | 
   | **Hodnota** | 0 | Počáteční hodnota vaší proměnné | 
   |||| 

1. V akci **inicializovat proměnnou** klikněte na možnost **Nový krok**. 

1. V poli Hledat vyberte možnost **vše**. Vyhledejte "do" a vyberte tuto akci: **do-řízení**

   ![Přidat smyčku "do"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Vytvořte podmínku ukončení smyčky výběrem proměnné **limit** a operátoru **is EQUAL** . 
   Jako hodnotu porovnání zadejte **10** .

   ![Podmínka ukončení sestavení pro zastavování smyčky](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Uvnitř smyčky vyberte **přidat akci**. 

1. V poli Hledat vyberte možnost **vše**. Vyhledejte "proměnné" a vyberte tuto akci: **zvýšit proměnnou proměnné**

   ![Přidat akci pro přírůstkovou proměnnou](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Jako **název**vyberte proměnnou **omezení** . Jako **hodnotu**zadejte "1". 

     ![Přírůstek "limit" o 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Mimo a pod smyčkou klikněte na možnost **Nový krok**. 

1. V poli Hledat vyberte možnost **vše**. 
     Vyhledejte a přidejte akci, která odešle e-mail, například: 

     ![Přidat akci, která odešle e-mail](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Pokud budete vyzváni, přihlaste se k e-mailovému účtu.

1. Nastavte vlastnosti akce e-mailu. Přidejte do předmětu **mezní** proměnnou. Tímto způsobem můžete potvrdit, že aktuální hodnota proměnné splňuje zadanou podmínku, například:

      ![Nastavení vlastností e-mailu](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Vlastnost | Hodnota | Popis |
      | -------- | ----- | ----------- | 
      | **Záměr** | *\<email-address\@domain>* | E-mailová adresa příjemce Pro účely testování použijte svoji vlastní e-mailovou adresu. | 
      | **Předmět** | Aktuální hodnota pro omezení je **limit** . | Zadejte předmět e-mailu. V tomto příkladu se ujistěte, že jste zahrnuli **mezní** proměnnou. | 
      | **Text** | <*e-mail – obsah*> | Zadejte obsah e-mailové zprávy, kterou chcete odeslat. V tomto příkladu zadejte libovolný text, který chcete. | 
      |||| 

1. Uložte aplikaci logiky. Pokud chcete aplikaci logiky otestovat ručně, na panelu nástrojů návrháře klikněte na **Spustit**.

      Po spuštění logiky dostanete e-mail s obsahem, který jste zadali:

      ![Přijatý e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Zabránit nekonečné smyčce

Smyčka "do" má výchozí omezení, která zastaví provádění, pokud nastane kterákoli z těchto podmínek:

| Vlastnost | Výchozí hodnota | Popis | 
| -------- | ------------- | ----------- | 
| **Výpočtu** | 60 | Nejvyšší počet cyklů, které jsou spuštěny před ukončením smyčky. Výchozí hodnota je 60 cyklů. | 
| **Prodlev** | PT1H | Maximální doba, po kterou se má spustit smyčka před ukončením smyčky. Výchozí hodnota je jedna hodina a je zadána ve formátu ISO 8601. <p>Hodnota časového limitu se vyhodnocuje pro každý cyklus smyčky. Pokud akce ve smyčce trvá déle, než je časový limit, aktuální cyklus se nezastaví. Další cyklus se ale nespustí, protože podmínka limitu není splněná. | 
|||| 

Chcete-li změnit tato výchozí omezení, vyberte možnost **Zobrazit upřesňující možnosti** v obrazci akce smyčky.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Do" definice (JSON)

Pokud pracujete v zobrazení kódu aplikace logiky, můžete `Until` místo toho definovat smyčku v definici JSON vaší aplikace logiky, například:

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

V tomto příkladu "dokud" smyčka volá koncový bod HTTP, který vytvoří prostředek. Smyčka se zastaví, když tělo odpovědi HTTP vrátí se `Completed` stavem. Aby nedocházelo k nekonečné smyčce, smyčka se také zastaví, pokud nastane kterákoli z těchto podmínek:

* Smyčka běžela desetkrát, jak je určeno `count` atributem. Výchozí hodnota je 60 časů. 

* Smyčka běžela po dobu dvou hodin, jak je určeno `timeout` atributem ve formátu ISO 8601. Výchozí hodnota je jedna hodina.
  
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

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Chcete-li odesílat nebo hlasovat o funkcích a návrzích, [Azure Logic Apps Web zpětné vazby uživatelů](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* [Spuštění kroků na základě podmínky (podmíněné příkazy)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Spuštění kroků na základě různých hodnot (příkazy Switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Spustit nebo sloučit paralelní kroky (větve)](../logic-apps/logic-apps-control-flow-branches.md)
* [Spuštění kroků na základě seskupeného stavu akce (obory)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

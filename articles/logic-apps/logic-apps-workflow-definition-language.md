---
title: Referenční dokumentace schématu pro jazyk definice pracovního postupu – Azure Logic Apps
description: Referenční příručka pro schéma jazyka definice pracovního postupu ve službě Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 05/13/2019
ms.openlocfilehash: 3b0ad33ea6348f24079b3c88f972437244c0bc93
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596761"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Referenční dokumentace schématu pro jazyk pro definování pracovních postupů v Azure Logic Apps

Když vytvoříte aplikaci logiky v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), aplikace logiky má základní definice pracovního postupu, který popisuje skutečné logiku, která ve vaší aplikaci logiky. Tuto definici pracovního postupu používá [JSON](https://www.json.org/) a řídí strukturu, která se ověří pomocí schématu rozhraní jazyka definice pracovního postupu. Tento odkaz obsahuje přehled o tuto strukturu a jak schéma definuje atributy v definici pracovního postupu.

## <a name="workflow-definition-structure"></a>Struktura definice pracovního postupu

Definice pracovního postupu, který je vždy obsahuje aktivační událost pro vytvoření instance vaší aplikace logiky a jednu nebo více akcí, které běží po aktivaci triggeru.

Tady je základní strukturu pro definici pracovního postupu:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Atribut | Požadováno | Popis |
|-----------|----------|-------------|
| `definition` | Ano | Počáteční element definice pracovního postupu |
| `$schema` | Pouze v případě, že externě odkazující na definici pracovního postupu | Umístění pro soubor schématu JSON, který popisuje verzi jazyka definice pracovního postupu, který najdete tady: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Ne | Definice pro jednu nebo více akcí pro spuštění v modulu runtime pracovního postupu. Další informace najdete v tématu [aktivační události a akce](#triggers-actions). <p><p>Maximální akce: 250 |
| `contentVersion` | Ne | Číslo verze definice pracovního postupu, který je ikona "1.0.0.0" ve výchozím nastavení. K identifikaci a ověřte správnou definici při nasazení pracovního postupu, zadejte hodnotu používat. |
| `outputs` | Ne | Definice pro výstupy, které vracejí z běhu pracovního postupu. Další informace najdete v tématu [výstupy](#outputs). <p><p>Maximální výstupy: 10 |
| `parameters` | Ne | Definice pro minimálně jeden parametr, který vkládá data do vašich pracovních postupů. Další informace najdete v tématu [parametry](#parameters). <p><p>Maximální parametry: 50 |
| `staticResults` | Ne | Definice pro jeden nebo více statických výsledky vrácené akcemi jako mock výstupy při statické výsledky jsou povolené na těchto akcí. V každé definici akce `runtimeConfiguration.staticResult.name` atribut odkazuje odpovídající definici uvnitř `staticResults`. Další informace najdete v tématu [statické výsledky](#static-results). |
| `triggers` | Ne | Definice pro jeden nebo více aktivačních událostí, které instanci pracovního postupu. Můžete definovat více než jeden trigger, ale pouze pomocí jazyka definice pracovního postupu, ne vizuálně pomocí návrháře pro Logic Apps. Další informace najdete v tématu [aktivační události a akce](#triggers-actions). <p><p>Maximální aktivační události: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Triggery a akce

V definici pracovního postupu `triggers` a `actions` oddíly definovat volání, ke kterým dochází při provádění pracovního postupu. Syntaxe a další informace o těchto částech najdete v tématu [triggerů pracovního postupu a akce](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="outputs"></a>

## <a name="outputs"></a>Výstupy

V `outputs` části, definují data, která vrací pracovního postupu po dokončení spuštění. Například můžete sledovat konkrétní stav nebo hodnota z každé spuštění, zadejte, že výstup pracovního postupu, vrátí tato data.

> [!NOTE]
> Když reagovat na příchozí požadavky z rozhraní REST API služby, nepoužívejte `outputs`. Místo toho použijte `Response` typ akce. Další informace najdete v tématu [triggerů pracovního postupu a akce](../logic-apps/logic-apps-workflow-actions-triggers.md).

Tady je obecnou strukturu pro definici výstupu:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Atribut | Požaduje se | Typ | Popis |
|-----------|----------|------|-------------|
| <*key-name*> | Ano | String | Název klíče pro výstup návratová hodnota |
| <*key-type*> | Ano | int, float, string, securestring, bool, array, objekt JSON | Typ výstupu vracené hodnoty |
| <*key-value*> | Ano | Stejné jako <*key-type*> | Návratová hodnota výstupu |
|||||

K získání výstup z běhu pracovního postupu, zkontrolujte historii spuštění aplikace logiky a podrobnosti na webu Azure Portal nebo pomocí [rozhraní REST API služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows). Můžete také předat výstup do externích systémů, například Power BI tak, že můžete vytvořit řídicí panely.

<a name="parameters"></a>

## <a name="parameters"></a>Parametry

V `parameters` části, definovat všechny parametry pracovních postupů, které používá vaše definice pracovního postupu při nasazení pro příjem vstupy. Deklarace parametru a hodnoty parametrů jsou požadovány v nasazení. Před použitím těchto parametrů v dalších částech pracovního postupu, ujistěte se, že je deklarovat všechny parametry v těchto oddílech. 

Tady je obecnou strukturu pro definici parametru:

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": {
      "key": {
        "name": "<key-value>"
      }
    }
  }
},
```

| Atribut | Požaduje se | Typ | Popis |
|-----------|----------|------|-------------|
| <*parameter-type*> | Ano | int, float, string, securestring, bool, array, objekt JSON, secureobject <p><p>**Poznámka:** Pro všechna hesla, klíče a tajné klíče, použijte `securestring` a `secureobject` typy, protože `GET` operace nevrací těchto typů. Další informace o zabezpečení parametry najdete v tématu [zabezpečení aplikací logiky](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | Typ parametru |
| <*default-parameter-values*> | Ano | Stejné jako `type` | Výchozí hodnota parametru, pokud není zadána žádná hodnota, když vytvoří instanci pracovního postupu |
| <*array-with-permitted-parameter-values*> | Ne | Array | Pole s hodnotami, které přijímají parametr |
| `metadata` | Ne | JSON – objekt | Další parametr podrobnosti, například název nebo čitelný popis pro svou aplikaci logiky nebo toku nebo dat doby návrhu použít Visual Studio nebo jinými nástroji |
||||

<a name="static-results"></a>

## <a name="static-results"></a>Statické výsledky

V `staticResults` atribut, definujte akce cvičné `outputs` a `status` , která vrací akce, pokud je zapnuté nastavení statické výsledek akce. V definici akce `runtimeConfiguration.staticResult.name` atribut odkazuje na název pro definici statické výsledek uvnitř `staticResults`. Přečtěte si, jak [testování aplikací logiky s využitím mock data nastavením statické výsledky](../logic-apps/test-logic-apps-mock-data-static-results.md).

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Atribut | Požaduje se | Typ | Popis |
|-----------|----------|------|-------------|
| <*statické výsledek definice názvu*> | Ano | String | Název pro definici statické výsledek, který může odkazovat na definici akce prostřednictvím `runtimeConfiguration.staticResult` objektu. Další informace najdete v tématu [nastavení konfigurace modulu Runtime](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Můžete použít libovolný jedinečný název, který chcete. Ve výchozím nastavení se připojí tento jedinečný název s číslem, které se zvyšuje podle potřeby. |
| <*output-attributes-and-values-returned*> | Ano | Různé | Požadavky pro tyto atributy se liší v závislosti na různých podmínek. Například, když `status` je `Succeeded`, `outputs` atribut obsahuje atributy a hodnoty vrácené jako mock výstupy akce. Pokud `status` je `Failed`, `outputs` obsahuje atribut `errors` atribut, který je pole obsahující jeden nebo více chyb `message` objekty, které mají informace o chybě. |
| <*hodnoty hlavičky*> | Ne | JSON | Hodnoty hlavičky vrácené akce |
| <*status-code-returned*> | Ano | String | Stavový kód vrácený akce |
| <*action-status*> | Ano | String | Stav akce, například `Succeeded` nebo `Failed` |
|||||

Například v této definici akce HTTP `runtimeConfiguration.staticResult.name` atribut odkazy `HTTP0` uvnitř `staticResults` atribut, ve kterém jsou definovány mock výstupy této akce. `runtimeConfiguration.staticResult.staticResultOptions` Atribut určuje, zda nastavení statické výsledek `Enabled` na akci HTTP.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

Akce HTTP vrátí výstupy v `HTTP0` definice uvnitř `staticResults`. V tomto příkladu, pro kód stavu je mock výstup `OK`. Pro hodnoty hlavičky mock výstup je `"Content-Type": "application/JSON"`. Stav akce mock výstup je `Succeeded`.

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Výrazy

Pomocí kódu JSON můžete mít literálové hodnoty, které existují v době návrhu, například:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Je také možné hodnoty, které neexistují až do spuštění. K reprezentaci tyto hodnoty, můžete použít *výrazy*, které jsou vyhodnocovány v době běhu. Výraz je sekvenci, která může obsahovat jednu nebo více [funkce](#functions), [operátory](#operators), proměnné, explicitní hodnoty nebo konstanty. V definici pracovního postupu můžete výraz kdekoli v řetězcové hodnotě JSON jsou výraz s znaku @ (\@). Při vyhodnocování výrazu, který reprezentuje hodnotu formátu JSON, je tak, že odeberete extrahován text výrazu \@ znak a vždy výsledky v jiné hodnoty JSON.

Například pro dřív definovanou `customerName` vlastností, můžete získat hodnotu vlastnosti pomocí [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) funkce ve výrazu a přiřaďte tuto hodnotu `accountName` vlastnost:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Interpolace řetězců* taky umožňuje použít více výrazů uvnitř řetězce, které jsou zabalené \@ znak a složené závorky ({}). Tady je syntaxe:

```json
@{ "<expression1>", "<expression2>" }
```

Výsledek je vždy řetězce, aby tato funkce podobně jako `concat()` funkce, například: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Pokud máte řetězcový literál, který začíná \@ znak, předpona \@ znak s jiným \@ znak jako řídicí znak: \@\@

Tyto příklady ukazují, jak jsou výrazy vyhodnocovány:

| Hodnota JSON | Výsledek |
|------------|--------|
| "Sophia Owen" | Vrátí tyto znaky: "Sophia Owen. |
| "pole [1]" | Vrátí tyto znaky: "pole [1]. |
| "\@\@" | Vrátit jako jeden znak řetězec tyto znaky: "\@. |
| " \@" | Vrátí tyto znaky jako řetězec dvou znaků: " \@. |
|||

Tyto příklady předpokládejme, že definujete "myBirthMonth" rovno "Od" a "myAge" rovno počtu 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Tyto příklady ukazují, jak se vyhodnocují těchto výrazů:

| Výraz JSON | Výsledek |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | Vraťte tento řetězec: "Od" |
| "\@{parameters('myBirthMonth')}" | Vraťte tento řetězec: "Od" |
| "\@parameters('myAge')" | Vrátí toto číslo: 42 |
| "\@{parameters('myAge')}" | Vrátí toto číslo jako řetězec: "42" |
| "Je můj věk \@{parameters('myAge')}" | Vraťte tento řetězec: "Můj věk je 42" |
| "\@concat ("Můj věk je:", string(parameters('myAge')))" | Vraťte tento řetězec: "Můj věk je 42" |
| "Je můj věk \@ \@{parameters('myAge')}" | Vraťte tento řetězec, který obsahuje výraz: "Je můj věk \@{parameters('myAge')}. |
|||

Při práci vizuálně v návrháři pro Logic Apps, můžete například vytvořit výrazy prostřednictvím Tvůrce výrazů:

![Návrhář pro Logic Apps > Tvůrce výrazů](./media/logic-apps-workflow-definition-language/expression-builder.png)

Jakmile budete hotovi, výraz se zobrazí pro odpovídající vlastnost v definici pracovního postupu, například, `searchQuery` vlastnost zde:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operátory

V [výrazy](#expressions) a [funkce](#functions), operátory provádět konkrétní úlohy, jako je například odkaz na vlastnost, nebo hodnotu v poli.

| Operátor | Úkol |
|----------|------|
| ' | Pomocí řetězcového literálu jako vstup nebo ve výrazech a funkce, zabalit řetězec pouze s jednoduchých uvozovek, například `'<myString>'`. Nepoužívejte dvojitých uvozovek (""), které jsou v konfliktu s formátování JSON kolem celý výraz. Příklad: <p>**Ano**: length('Hello') </br>**Ne**: length("Hello") <p>Při předání polí nebo čísla, není nutné obtékání interpunkce. Příklad: <p>**Ano**: délka ([1, 2, 3]) </br>**Ne**: délka ("[1, 2, 3]") |
| [] | Chcete-li odkazovat na konkrétní pozici (index) v poli hodnota, použijte hranaté závorky. Chcete-li například získat druhé položky v poli: <p>`myArray[1]` |
| . | K odkazování na vlastnost v objektu, použijte operátor tečky. Například, chcete-li získat `name` vlastnost `customer` objektu JSON: <p>`"@parameters('customer').name"` |
| ? | K odkazování na hodnotu null vlastnosti v objektu bez Chyba za běhu, použijte operátor otazníkem. Třeba pro zpracování null výstupy z aktivační události, můžete použít tento výraz: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funkce

Některé výrazy jejich hodnoty získat z akce modulu runtime, které nemusí ještě neexistuje, když se spustí vaše definice pracovního postupu. Odkaz nebo pracovat s těmito hodnotami ve výrazech, můžete použít [ *funkce* ](../logic-apps/workflow-definition-language-functions-reference.md) poskytující jazyka definice pracovního postupu.

## <a name="next-steps"></a>Další postup

* Další informace o [jazyka definice pracovního postupu akce a triggery](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Zjistěte, jak programově vytvářet a spravovat aplikace logiky s [rozhraní REST API služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows)

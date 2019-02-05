---
title: Referenční dokumentace schématu pro jazyk definice pracovního postupu – Azure Logic Apps | Dokumentace Microsoftu
description: Zapsat definice vlastní pracovní postup pro Azure Logic Apps s jazyka definice pracovního postupu
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 04/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 3c17ec2133e278b17475e4988e1e9766b1349ba4
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734636"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Referenční dokumentace schématu pro jazyk pro definování pracovních postupů v Azure Logic Apps

Když vytvoříte pracovní postup aplikace logiky s [Azure Logic Apps](../logic-apps/logic-apps-overview.md), základní definici pracovního postupu popisuje skutečné logiku, která pro vaši aplikaci logiky. Tento popis následující strukturu, která je definována a ověřit pomocí schématu rozhraní jazyka definice pracovního postupu, který používá [zápis JSON (JavaScript Object)](https://www.json.org/).

## <a name="workflow-definition-structure"></a>Struktura definice pracovního postupu

Definice pracovního postupu má aspoň jeden trigger, který vytvoří instanci aplikace logiky a jednu nebo více akcí, které spouští vaše aplikace logiky.

Tady je základní strukturu pro definici pracovního postupu:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```

| Element | Požaduje se | Popis |
|---------|----------|-------------|
| Definice | Ano | Počáteční element definice pracovního postupu |
| $schema | Pouze v případě, že externě odkazující na definici pracovního postupu | Umístění pro soubor schématu JSON, který popisuje verzi jazyka definice pracovního postupu, který najdete tady: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |
| contentVersion | Ne | Číslo verze definice pracovního postupu, který je ikona "1.0.0.0" ve výchozím nastavení. K identifikaci a ověřte správnou definici při nasazení pracovního postupu, zadejte hodnotu používat. |
| parameters | Ne | Definice pro minimálně jeden parametr, který vkládá data do vašich pracovních postupů <p><p>Maximální parametry: 50 |
| triggery | Ne | Definice pro jeden nebo více aktivačních událostí, které instanci pracovního postupu. Můžete definovat více než jeden trigger, ale pouze pomocí jazyka definice pracovního postupu, ne vizuálně pomocí návrháře pro Logic Apps. <p><p>Maximální aktivační události: 10 |
| Akce | Ne | Definice pro jednu nebo více akcí pro spuštění v modulu runtime pracovního postupu <p><p>Maximální akce: 250 |
| výstupy | Ne | Definice pro výstupy, které vracejí z běhu pracovního postupu <p><p>Maximální výstupy: 10 |
||||

## <a name="parameters"></a>Parametry

V `parameters` části, definovat všechny parametry pracovních postupů, které vaše aplikace logiky používá při nasazení pro příjem vstupy. Deklarace parametru a hodnoty parametrů jsou požadovány v nasazení. Před použitím těchto parametrů v dalších částech pracovního postupu, ujistěte se, že je deklarovat všechny parametry v těchto oddílech. 

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

| Element | Požaduje se | Typ | Popis |
|---------|----------|------|-------------|
| type | Ano | int, float, string, securestring, bool, pole, objekt JSON, secureobject <p><p>**Poznámka:** Pro všechna hesla, klíče a tajné klíče, použijte `securestring` a `secureobject` typy, protože `GET` operace nevrací těchto typů. | Typ parametru |
| Výchozí hodnota | Ne | Stejné jako `type` | Výchozí hodnota parametru, pokud není zadána žádná hodnota, když vytvoří instanci pracovního postupu |
| allowedValues | Ne | Stejné jako `type` | Pole s hodnotami, které přijímají parametr |
| zprostředkovatele identity | Ne | JSON – objekt | Další parametr podrobnosti, například název nebo čitelný popis pro svou aplikaci logiky nebo použít Visual Studio nebo jinými nástroji dat doby návrhu |
||||

## <a name="triggers-and-actions"></a>Aktivační události a akce

V definici pracovního postupu `triggers` a `actions` oddíly definovat volání, ke kterým dochází při provádění pracovního postupu. Syntaxe a další informace o těchto částech najdete v tématu [triggerů pracovního postupu a akce](../logic-apps/logic-apps-workflow-actions-triggers.md).

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

| Element | Požaduje se | Typ | Popis |
|---------|----------|------|-------------|
| <*key-name*> | Ano | String | Název klíče pro výstup návratová hodnota |
| type | Ano | int, float, string, securestring, bool, pole, objekt JSON | Typ výstupu vracené hodnoty |
| hodnota | Ano | Stejné jako `type` | Návratová hodnota výstupu |
|||||

K získání výstup z běhu pracovního postupu, přečtěte si podrobnosti na webu Azure Portal a historii spuštění aplikace logiky nebo použijte [rozhraní REST API služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows). Můžete také předat výstup do externích systémů, například Power BI tak, že můžete vytvořit řídicí panely.

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

## <a name="functions"></a>Functions

Některé výrazy jejich hodnoty získat z akce modulu runtime, které možná ještě neexistuje, při spuštění aplikace logiky pro spuštění. Odkaz nebo pracovat s těmito hodnotami ve výrazech, můžete použít [ *funkce* ](../logic-apps/workflow-definition-language-functions-reference.md) poskytující jazyka definice pracovního postupu.

## <a name="next-steps"></a>Další postup

* Další informace o [jazyka definice pracovního postupu akce a triggery](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Zjistěte, jak programově vytvářet a spravovat aplikace logiky s [rozhraní REST API služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows)

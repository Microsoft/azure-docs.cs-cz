---
title: Schéma jazyka pro definici pracovního postupu – Azure Logic Apps | Dokumentace Microsoftu
description: Zapsat definice vlastní pracovní postup pro Azure Logic Apps s jazyka definice pracovního postupu
services: logic-apps
author: ecfan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: babe21db6acc2f7154857b4eb0a02356e89a8ca7
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39060576"
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>Definice pracovního postupu Logic Apps se schématem jazyka definice pracovního postupu

Když vytvoříte pracovní postup aplikace logiky s [Azure Logic Apps](../logic-apps/logic-apps-overview.md), základní definici pracovního postupu popisuje skutečné logiku, která pro vaši aplikaci logiky. Tento popis následující strukturu, která je definována a ověřit pomocí schématu rozhraní jazyka definice pracovního postupu, který používá [zápis JSON (JavaScript Object)](https://www.json.org/) formátu. 
  
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
| contentversion – | Ne | Číslo verze definice pracovního postupu, který je ikona "1.0.0.0" ve výchozím nastavení. K identifikaci a ověřte správnou definici při nasazení pracovního postupu, zadejte hodnotu používat. | 
| parameters | Ne | Definice pro minimálně jeden parametr, který vkládá data do vašich pracovních postupů <p><p>Maximální parametry: 50 | 
| Aktivační události | Ne | Definice pro jeden nebo více aktivačních událostí, které instanci pracovního postupu. Můžete definovat více než jeden trigger, ale pouze pomocí jazyka definice pracovního postupu, ne vizuálně pomocí návrháře pro Logic Apps. <p><p>Maximální aktivační události: 10 | 
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
| type | Ano | int, float, string, securestring, bool, pole, objekt JSON, secureobject <p><p>**Poznámka:**: pro všechna hesla, klíče a tajné klíče, použijte `securestring` a `secureobject` typy, protože `GET` operace nevrací těchto typů. | Typ parametru |
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
| <*Název klíče*> | Ano | Řetězec | Název klíče pro výstup návratová hodnota |  
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
| "Je můj věk \@ \@{parameters('myAge')}" | Vraťte tento řetězec, který obsahuje výraz: "je můj věk \@{parameters('myAge')}. | 
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

Některé výrazy jejich hodnoty získat z akce modulu runtime, které možná ještě neexistuje, při spuštění aplikace logiky pro spuštění. Odkaz nebo pracovat s těmito hodnotami ve výrazech, můžete použít [ *funkce*](../logic-apps/workflow-definition-language-functions-reference.md). Například můžete použít matematické funkce pro výpočty, například [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) funkce, která vrací součet z celých čísel nebo float. Podrobné informace o jednotlivých funkcích naleznete v tématu [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).
Nebo, pokračujte ve čtení o funkcích a jejich obecné účely.

Tady je pár příkladu úkoly, které můžete provádět pomocí funkce: 

| Úkol | Syntaxe funkce | Výsledek | 
| ---- | --------------- | -------------- | 
| Vrátí řetězec ve formátu malá písmena. | toLower ('<*text*> ") <p>Příklad: toLower('Hello') | "hello" | 
| Vrátí globálně jedinečný identifikátor (GUID). | GUID() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

Tento příklad ukazuje, jak můžete získat hodnotu z `customerName` parametr a přiřaďte tuto hodnotu `accountName` vlastnost s použitím [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) funkce ve výrazu:

```json
"accountName": "@parameters('customerName')"
```

Zde jsou některé obecné způsoby, a tím, že můžete použít funkce ve výrazech:

| Úkol | Syntaxe funkce ve výrazu | 
| ---- | -------------------------------- | 
| Práce s položkou proveďte předáním této položky na funkci. | "\@<*functionName*> (<*položky*>)" | 
| 1. Získat *parameterName*na hodnotu s použitím ve vnořeném `parameters()` funkce. </br>2. Provedení práce s výsledkem předáním tuto hodnotu *functionName*. | "\@<*functionName*> (parametry ('<*parameterName*>")) " | 
| 1. Získat výsledek z vnořené vnitřní funkce *functionName*. </br>2. Výsledek předat vnější funkce *functionName2*. | "\@<*functionName2*> (<*functionName*> (<*položky*>))" | 
| 1. Získat výsledek z *functionName*. </br>2. Vzhledem k tomu, že výsledek je objekt s vlastností *propertyName*, získat hodnotu této vlastnosti. | "\@<*functionName*>(<*item*>). <*propertyName*>" | 
||| 

Například `concat()` funkce může trvat minimálně dva řetězcové hodnoty jako parametry. Tato funkce kombinuje tyto řetězce do jednoho řetězce. Můžete buď předat řetězcové literály, například "Sophia" a "Owen" tak, aby získat řetězec kombinované "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Nebo můžete získat řetězcové hodnoty parametrů. V tomto příkladu `parameters()` funkce v každém `concat()` parametr a `firstName` a `lastName` parametry. Potom předejte výsledného řetězce `concat()` fungovat, takže získáte kombinované řetězec, například "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

V obou případech oba příklady přiřadit výsledek, který má `customerName` vlastnost. 

Podrobné informace o jednotlivých funkcích naleznete v tématu [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).
Nebo, pokračujte ve čtení o funkce založené na jejich obecné účely.

<a name="string-functions"></a>

### <a name="string-functions"></a>Řetězcové funkce

Pro práci s řetězci, můžete použít tyto funkce řetězce a také některé [kolekce funkcí](#collection-functions). Řetězec funkce pracují pouze na řetězce. 

| String – funkce | Úkol | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Kombinací dvou nebo více řetězců a vrátit kombinované řetězec. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Zkontrolujte, jestli řetězec končí zadaným podřetězcem. | 
| [identifikátor GUID](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Vygeneruje globálně jedinečný identifikátor (GUID) jako řetězec. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Vrátí počáteční pozici pro dílčí řetězec. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Vrátí poslední pozice podřetězce. | 
| [nahradit](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Nahraďte podřetězce pomocí zadaného řetězce a vrátit řetězec aktualizované. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Vrátí pole, které obsahuje všechny znaky z řetězce a odděluje jednotlivé znaky s konkrétní oddělovací znak. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Zkontrolujte, jestli řetězec začíná na konkrétní dílčí řetězec. | 
| [dílčí řetězec](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Vrácení znaků z řetězce, počínaje od zadané pozice. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Vrátí řetězec ve formátu malá písmena. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Vrátí řetězec ve formátu velká písmena. | 
| [Trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Odebere úvodní a koncové mezery z řetězce a vrátit řetězec aktualizované. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Kolekce funkcí

Pro práci s kolekcemi, obecně pole, řetězce a v některých případech slovníky, můžete tyto funkce kolekce. 

| Kolekce – funkce | Úkol | 
| ------------------- | ---- | 
| [Obsahuje](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Zkontrolujte, zda kolekce obsahuje konkrétní položku. |
| [prázdný](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Zkontrolujte, zda kolekce je prázdná. | 
| [první](../logic-apps/workflow-definition-language-functions-reference.md#first) | Vrátí první položku z kolekce. | 
| [Průnik](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Vrátí kolekci, která má *pouze* společné položky mezi zadaným kolekcím. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Vrátí řetězec, který má *všechny* položek z pole, oddělené zadaný znak. | 
| [poslední](../logic-apps/workflow-definition-language-functions-reference.md#last) | Vrátí poslední položku z kolekce. | 
| [Délka](../logic-apps/workflow-definition-language-functions-reference.md#length) | Vrátí počet položek v řetězce nebo pole. | 
| [Přeskočit](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Odebrat položky z přední části kolekce a vrátí *všechny ostatní* položky. | 
| [Take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Vrátí položky ze začátku kolekce. | 
| [sjednocení](../logic-apps/workflow-definition-language-functions-reference.md#union) | Vrátí kolekci, která má *všechny* položky ze zadaných kolekcí. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Porovnání funkcí

K práci s podmínkami, porovnat hodnoty a výsledky výrazu nebo vyhodnotit různé druhy logiku, můžete tyto funkce porovnání. Úplný přehled o jednotlivých funkcích naleznete v tématu [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkce porovnání | Úkol | 
| ------------------- | ---- | 
| [a](../logic-apps/workflow-definition-language-functions-reference.md#and) | Zkontrolujte, zda jsou všechny výrazy hodnotu true. | 
| [rovná se](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Zkontrolujte, zda jsou ekvivalentní obě hodnoty. | 
| [větší](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Zkontrolujte, zda je první hodnota větší než druhá hodnota. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Zkontrolujte, zda je první hodnota větší než nebo rovna hodnotě druhá hodnota. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Zkontrolujte, zda je výraz hodnotu true nebo false. Na základě výsledku, vrátí zadanou hodnotu. | 
| [méně](../logic-apps/workflow-definition-language-functions-reference.md#less) | Zkontrolujte, zda je první hodnota menší než druhá hodnota. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Zkontrolujte, zda je první hodnota menší než druhá hodnota. | 
| [Not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Zkontrolujte, zda má výraz hodnotu false. | 
| [Nebo](../logic-apps/workflow-definition-language-functions-reference.md#or) | Zkontrolujte, zda má nejméně jeden výraz hodnotu true. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Převodní funkce

Chcete-li změnit typ nebo formát hodnoty, můžete tyto funkce pro převod. Můžete například změnit hodnotu z typu Boolean na celé číslo. Další způsob, jakým aplikace logiky zpracovává typy obsahu při převodu, najdete v článku [zpracování typů obsahu](../logic-apps/logic-apps-content-type.md). Úplný přehled o jednotlivých funkcích naleznete v tématu [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkce pro převod | Úkol | 
| ------------------- | ---- | 
| [Pole](../logic-apps/workflow-definition-language-functions-reference.md#array) | Vrátí pole z jedné zadané vstup. Více vstupů, naleznete v tématu [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Vrácení verze řetězce s kódováním base64. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Vrátí binární verze řetězce s kódováním base64. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Vrátí řetězec verze řetězce s kódováním base64. | 
| [Binární](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Vrátí binární verzi pro vstupní hodnotu. | 
| [BOOL](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Vrátí logickou verzi pro vstupní hodnotu. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Vrátíte pole z více vstupů. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Vrátí identifikátor URI dat pro vstupní hodnotu. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Vrátí binární verzi pro data identifikátoru URI. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Vrátí řetězec verzi pro data identifikátor URI. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Vrátí řetězec verze řetězce s kódováním base64. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Vrátí binární verzi pro data identifikátoru URI. | 
| [decodeuricomponent –](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Vrátí, nahradí řídicí znaky s verzemi dekódovaný řetězec. | 
| [encodeuricomponent –](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Vrátí řetězec, který nahradí adresu URL problematické znaky řídicími znaky. | 
| [plovoucí desetinnou čárkou](../logic-apps/workflow-definition-language-functions-reference.md#float) | Vrátí plovoucí číslo pro hodnotu vstupního bodu. | 
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Vrátí celé číslo verze řetězce. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Vrátí hodnotu typu JavaScript Object Notation (JSON) nebo objekt pro řetězec nebo kód XML. | 
| [řetězec](../logic-apps/workflow-definition-language-functions-reference.md#string) | Vrácení verze řetězce pro vstupní hodnotu. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Vrátí verzi kódovaný identifikátor URI pro vstupní hodnotu tak, že nahradíte adresu URL problematické znaky s řídicími znaky. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Vrátí binární verzi pro řetězec kódovaný identifikátor URI. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Vrácení verze řetězce pro řetězec kódovaný identifikátor URI. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Vrátí verzi XML pro řetězec. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Matematické funkce

Pro práci s celými čísly a float, můžete použít tyto matematické funkce. Úplný přehled o jednotlivých funkcích naleznete v tématu [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Matematické funkce | Úkol | 
| ------------- | ---- | 
| [Přidat](../logic-apps/workflow-definition-language-functions-reference.md#add) | Vrátí výsledek součtu daných dvou čísel. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Vrátí výsledek podílu dvou čísel. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Vrátí nejvyšší hodnota ze sady čísel nebo pole. | 
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Vrátí nejnižší hodnotu ze sady čísel nebo pole. | 
| [MOD](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Vrátí zbytek dělení dvou čísel. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Vrátí produkt od součin dvou čísel. | 
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Vrátí náhodné celé číslo od zadaného rozsahu. | 
| [rozsah](../logic-apps/workflow-definition-language-functions-reference.md#range) | Vrátí pole celé číslo, které začíná od zadané celé číslo. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Vrátí výsledek v daných druhé číslo z první číslo. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Funkce Date a time

Pro práci s daty a časy, můžete tyto funkce date a time.
Úplný přehled o jednotlivých funkcích naleznete v tématu [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Datum a čas – funkce | Úkol | 
| --------------------- | ---- | 
| [Přidat_dny](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Přidáte počet dní do časového razítka. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Přidáte počet hodin do časového razítka. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Počet minut, přidejte do časového razítka. | 
| [Přidat_sekundy](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Počet sekund, po přidání do časového razítka. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Přidáte počet časových jednotek do časového razítka. Viz také [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Převeďte časové razítko od koordinovaný světový čas (UTC) na časové pásmo cíle. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Převeďte časové razítko ze zdrojového časového pásma na časové pásmo cíle. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Převeďte časové razítko ze zdrojového časového pásma na koordinovaný univerzální čas (UTC). | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Vrátí den v komponentu měsíc z časové razítko. | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Vrátí den v týdnu komponenty z časové razítko. | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Vrátí den v komponentu rok z časové razítko. | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Vrátí datum od časové razítko. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Vrátí aktuální časové razítko plus zadané časové jednotky. Viz také [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Vrátí aktuální časové razítko minus zadané časové jednotky. Viz také [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Vrátí začátek dne pro časové razítko. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Vrátí začátek hodiny pro hodnotu časového razítka. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Vrátí začátek měsíce pro časové razítko. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odečte počet časových jednotek z časové razítko. Viz také [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [značky](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Vrátit `ticks` hodnota vlastnosti pro zadané časové razítko. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Vrátí aktuální časové razítko jako řetězec. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Funkce pracovních postupů

Tyto funkce pracovních postupů vám můžou pomoct:

* Získáte podrobnosti o instanci pracovního postupu za běhu. 
* Práce se vstupy pro vytvoření instance aplikace logiky.
* Odkazují na výstupy z aktivační události a akce.

Můžete například odkazují na výstupy z jedné akce a použít je v rámci novější akce. Úplný přehled o jednotlivých funkcích naleznete v tématu [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkce pracovního postupu | Úkol | 
| ----------------- | ---- | 
| [Akce](../logic-apps/workflow-definition-language-functions-reference.md#action) | Vrací výstup aktuální akci v modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON. Viz také [akce](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Vrácení akce `body` výstupu za běhu. Viz také [tělo](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Vrací výstup akce za běhu. Zobrazit [akce](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [Akce](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Vrácení výstupu akce modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON. Viz také [akce](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [Text](#body) | Vrácení akce `body` výstupu za běhu. Viz také [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Vytvoří pole s hodnotami, které odpovídají názvu klíče v *data formuláře* nebo *form-encoded.* výstupy akce. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Vrátí jednu hodnotu, která odpovídá názvu klíče v akce *data formuláře* nebo *form-encoded. výstup*. | 
| [Položka](../logic-apps/workflow-definition-language-functions-reference.md#item) | Když v opakující se akci nad polem, vrátí aktuální položku v poli během aktuální iteraci akce. | 
| [Položky](../logic-apps/workflow-definition-language-functions-reference.md#items) | Když uvnitř pro každý nebo proveďte až do smyčky, vrácení aktuální položku zadané smyčky.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Vrátí adresu "URL zpětného volání", která volá triggeru nebo akce. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Vrátí text pro určitou část ve výstupu akce, který má více částí. | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Vrátí hodnotu pro parametr, který je popsaný v definici aplikace logiky. | 
| [Aktivační událost](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Vrátíte výstupní trigger's za běhu, nebo z jiných dvojice název a hodnota JSON. Viz také [triggerOutputs](#triggerOutputs) a [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Vrátí trigger's `body` výstupu za běhu. Zobrazit [aktivační událost](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Vrátí jednu hodnotu, která odpovídá názvu klíče v *data formuláře* nebo *form-encoded.* aktivovat výstupy. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Vrátí text pro určitou část ve výstupu aktivační událost s více částmi. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Vytvořit pole, jejichž hodnoty odpovídají názvu klíče v *data formuláře* nebo *form-encoded.* aktivovat výstupy. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Vrátíte výstupní aktivační události v modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON. Zobrazit [aktivační událost](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [Proměnné](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Vrátí hodnotu zadané proměnné. | 
| [Pracovní postup](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Vrátí všechny podrobnosti samotného pracovního postupu za běhu. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>Funkce parsování identifikátorů URI

Práce s identifikátory URI (URI) a získat tyto identifikátory URI různé hodnoty, můžete tyto funkce parsování identifikátorů URI. Úplný přehled o jednotlivých funkcích naleznete v tématu [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkce parsování identifikátorů URI | Úkol | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Vrátit `host` hodnota pro identifikátor URI (URI). | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Vrátit `path` hodnota pro identifikátor URI (URI). | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Vrátit `path` a `query` hodnoty pro identifikátor URI (URI). | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Vrátit `port` hodnota pro identifikátor URI (URI). | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Vrátit `query` hodnota pro identifikátor URI (URI). | 
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Vrátit `scheme` hodnota pro identifikátor URI (URI). | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>Funkce JSON a XML

Pro práci s objekty JSON a z uzlů XML, můžete tyto funkce pro zpracování. Úplný přehled o jednotlivých funkcích naleznete v tématu [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkci pro zpracování | Úkol | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Přidání vlastnosti a její hodnotu nebo dvojice název hodnota do objektu JSON a vrácení aktualizovaného objektu. | 
| [sloučení](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Vrátí první hodnotu než null z jednoho nebo více parametrů. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Odebrání vlastnosti z objektu JSON a vrácení aktualizovaného objektu. | 
| [Metoda setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Nastavit hodnotu pro vlastnost objektu JSON a vrácení aktualizovaného objektu. | 
| [výraz XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Kontrolovat XML uzlů nebo hodnoty, které odpovídají výrazu XPath (XML Path Language) a vrátí odpovídající uzly nebo hodnoty. | 
||| 

## <a name="next-steps"></a>Další postup

* Další informace o [jazyka definice pracovního postupu akce a triggery](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Zjistěte, jak programově vytvářet a spravovat aplikace logiky s [rozhraní REST API služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows)

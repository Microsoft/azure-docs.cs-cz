---
title: Pracovní postup schema Definition Language - Azure Logic Apps | Microsoft Docs
description: Zápis definice vlastní pracovní postup pro Azure Logic Apps s jazykem definice pracovního postupu
services: logic-apps
author: ecfan
manager: cfowler
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
ms.openlocfilehash: efbfffec10b665ebab230375e774e476199c4ad5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="logic-apps-workflow-definitions-with-the-workflow-definition-language-schema"></a>Definice pracovního postupu aplikace logiky se schématem jazyk definic workflowů funkce

Když vytvoříte aplikaci logiky pracovního postupu s [Azure Logic Apps](../logic-apps/logic-apps-overview.md), základní definice pracovní postup popisuje skutečné logiku, která pro svou aplikaci logiky. Tento popis následuje strukturu, která má definovaný a ověřit schéma jazyk definic workflowů, které používá [JavaScript Object Notation (JSON)](https://www.json.org/) formátu. 
  
## <a name="workflow-definition-structure"></a>Struktura definice pracovního postupu

Definice pracovního postupu má nejméně jedna aktivační událost, která vytvoří instanci aplikace logiky, plus jednu nebo více akcí, které běží vaše aplikace logiky. 

Zde je podrobný strukturu pro definici pracovního postupu:  
  
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
| Definice | Ano | Počáteční element vaší definice pracovního postupu | 
| $schema | Jenom v případě, že externě odkazující na definici pracovního postupu | Umístění pro soubor schématu JSON, který popisuje verzi jazyka pro definici pracovního postupu, který najdete tady: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | Ne | Číslo verze vaší definice pracovního postupu, který je "1.0.0.0" ve výchozím nastavení. Pomáhají identifikovat a potvrďte správné definice při nasazování pracovního postupu, zadejte hodnotu, která se použije. | 
| parameters | Ne | Definice pro jeden nebo více parametrů, který vkládá data do vašeho pracovního postupu <p><p>Maximální parametry: 50 | 
| Aktivační události | Ne | Definice pro jeden nebo více aktivačních událostí, které vytvořit instanci pracovního postupu. Můžete definovat více než jedna aktivační událost, ale jenom s jazykem definice pracovního postupu, není vizuálně prostřednictvím návrháře logiku aplikace. <p><p>Maximální aktivační události: 10 | 
| Akce | Ne | Definice pro jednu nebo více akcí provést v modulu runtime pracovního postupu <p><p>Maximální akce: 250 | 
| výstupy | Ne | Definice pro výstupů, které vracejí z pracovního postupu spustit <p><p>Maximální výstupy: 10 |  
|||| 

## <a name="parameters"></a>Parametry

V `parameters` část, definovat všechny parametry pracovního postupu, které vaše aplikace logiky používá v nasazení pro přijetí vstupy. Deklarace parametrů a hodnoty parametrů jsou požadovány v nasazení. Než tyto parametry můžete použít v dalších částech pracovního postupu, ujistěte se, že deklarujete všech parametrů v těchto oddílech. 

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
| type | Ano | int, float, string, securestring, bool, pole, objekt JSON, secureobject <p><p>**Poznámka:**: pro všechny hesla, klíče a tajné klíče, použijte `securestring` a `secureobject` typy, protože `GET` operaci nevrací tyto typy. | Typ parametru |
| Výchozí hodnota | Ne | Stejné jako `type` | Výchozí hodnota parametru, když není zadaná žádná hodnota, když vytvoří instanci pracovního postupu | 
| allowedValues | Ne | Stejné jako `type` | Pole s hodnotami, které může přijmout parametr |  
| zprostředkovatele identity | Ne | Objekt JSON | Žádné další parametr podrobnosti, například název nebo popis čitelný pro vaše aplikace logiky, nebo data návrhu používá Visual Studio nebo jiných nástrojů |  
||||

## <a name="triggers-and-actions"></a>Aktivační události a akce  

V definici pracovního postupu `triggers` a `actions` části definovat volání, které dojít během provádění pracovní postup. Syntaxe a další informace o těchto částech najdete v tématu [pracovního postupu triggery a akce](../logic-apps/logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Výstupy 

V `outputs` část, definovat data, která pracovní postup může vrátit po dokončení spuštění. Například sledovat konkrétní stav nebo hodnota z každé spuštění, zadejte, že tato data vrátí výstup pracovního postupu. 

> [!NOTE]
> Když reagovat na příchozí požadavky z rozhraní API služby REST, nepoužívejte `outputs`. Místo toho použijte `Response` typ akce. Další informace najdete v tématu [pracovního postupu triggery a akce](../logic-apps/logic-apps-workflow-actions-triggers.md).

Tady je obecnou strukturu pro definici výstup: 

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
| type | Ano | int, float, string, securestring, bool, pole, objekt JSON | Typ výstupu návratová hodnota | 
| hodnota | Ano | Stejné jako `type` | Návratová hodnota výstupu |  
||||| 

Chcete-li získat výstup z pracovního postupu spustit, zkontrolujte historie spouštění aplikace logiky a podrobnosti na portálu Azure nebo použijte [pracovního postupu REST API](https://docs.microsoft.com/rest/api/logic/workflows). Můžete také předat výstup do externími systémy, například PowerBI tak, že můžete vytvořit řídicí panely. 

<a name="expressions"></a>

## <a name="expressions"></a>Výrazy

Formát JSON mohou být literálových hodnot, které existují v době návrhu, například:

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

Můžete taky nechat hodnoty, které neexistují až při spuštění. Představující tyto hodnoty, můžete použít *výrazy*, které jsou vyhodnocovány v době běhu. Výraz je posloupnost, která může obsahovat jednu nebo více [funkce](#functions), [operátory](#operators), proměnné, explicitní hodnoty nebo konstanty. Ve vaší definice pracovního postupu, lze použít výraz kdekoli v hodnotu řetězce formátu JSON pomocí prefixu výraz s znaku @ (@). Při vyhodnocování výrazu, který reprezentuje hodnotu formátu JSON, těle výrazu je extrahován odebráním @ znak a vždy má za následek jinou hodnotu JSON. 

Například pro dříve definovaný `customerName` vlastnost, můžete získat hodnotu vlastnosti pomocí [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) funkce ve výrazu a přiřaďte tuto hodnotu na `accountName` vlastnost:

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

*Řetězec interpolace* také umožňuje použití více výrazů uvnitř řetězců, které nejsou zabalené službou @ znak a složené závorky ({}). Tady je syntaxe:

```json
@{ "<expression1>", "<expression2>" }
```

Výsledkem je vždy řetězec, provedení podobná tato funkce `concat()` funkce, například: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Pokud máte řetězcový literál, který začíná @ – znak, předpony znak s jinou @ – znak jako řídicí znak @: @@

Tyto příklady ukazují, jak se vyhodnocují výrazů:

| Hodnota JSON | Výsledek |
|------------|--------| 
| "Sophia Owen" | Vrátí tyto znaky: 'Sophia Owen. |
| "array [1]" | Vrátí tyto znaky: 'pole [1]. |
| "@@" | Jako řetězec znaků jeden vrátit tyto znaky: ' @' |   
| " @" | Vrátí jako řetězec znaků dva tyto znaky: ' @' |
|||

Tyto příklady předpokládejme, že definujete "myBirthMonth" rovno "Leden" a "myAge" rovná počtu 42:  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

Tyto příklady ukazují, jak se vyhodnocují těchto výrazů:

| Výraz JSON | Výsledek |
|-----------------|--------| 
| "@parameters(myBirthMonth)" | Vraťte tento řetězec: "Leden" |  
| "@{parameters('myBirthMonth')}" | Vraťte tento řetězec: "Leden" |  
| "@parameters(myAge)" | Vrátí toto číslo: 42 |  
| "@{parameters('myAge')}" | Vrátí toto číslo jako řetězec: "42" |  
| "Je můj stáří @{parameters('myAge')}" | Vraťte tento řetězec: "Moje stáří je 42" |  
| "@concat(Moje stáří je, string(parameters('myAge')))" | Vraťte tento řetězec: "Moje stáří je 42" |  
| "Je můj stáří @@ {parameters('myAge')}" | Vraťte tento řetězec, který obsahuje výraz: "je můj stáří @{parameters('myAge')}. | 
||| 

Při práci vizuálně v návrháři aplikace logiky, můžete například vytvořit výrazy prostřednictvím Tvůrce výrazů: 

![Návrhář aplikace logiky > Tvůrce výrazů](./media/logic-apps-workflow-definition-language/expression-builder.png)

Když jste hotovi, výraz se zobrazí pro odpovídající vlastnost ve vaší definice pracovního postupu, například, `searchQuery` vlastnost tady:

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

V [výrazy](#expressions) a [funkce](#functions), operátory provádějí konkrétní úlohy, jako je například odkaz na vlastnost, nebo hodnota v poli. 

| Operátor | Úkol | 
|----------|------|
| ' | Řetězcový literál použít jako vstup nebo v výrazy a funkce, zabalení řetězec pouze s apostrofech, například `'<myString>'`. Nepoužívejte dvojitých uvozovek nahoře (""), který v konfliktu s JSON formátování kolem celý výraz. Příklad: <p>**Ano**: length('Hello') </br>**Ne**: length("Hello") <p>Pokud předáte pole nebo čísla, nepotřebujete zabalení interpunkce. Příklad: <p>**Ano**: délka ([1, 2, 3]) </br>**Ne**: délka ("[1, 2, 3]") | 
| [] | Chcete-li hodnotu na konkrétní pozici (index) v pole, použijte hranaté závorky. Chcete-li například získat druhý položky v poli: <p>`myArray[1]` | 
| . | Chcete-li vlastnost v objektu, použijte operátor tečky. Chcete-li například získat `name` vlastnost `customer` objekt JSON: <p>`"@parameters('customer').name"` | 
| ? | Chcete-li vlastnosti s hodnotou null v objektu bezchybně runtime, použijte operátor otazník. Například pro zpracování null výstupy z aktivační událost, můžete tento výraz: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>Functions

Některé výrazy získat své hodnoty z modulu runtime akce, které možná ještě neexistuje, když se spustí aplikace logiky. Pokud chcete odkazovat nebo pracovat s těmito hodnotami ve výrazech, můžete použít [ *funkce*](../logic-apps/workflow-definition-language-functions-reference.md). Například můžete použít matematické funkce pro výpočty, například [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) funkci, která vrátí součet z celých čísel nebo obtékaných objektů. Podrobné informace o každé funkci, najdete v článku [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).
Nebo pokračujte ve čtení o funkcích a jejich obecné účely.

Zde jsou po pár příklad úlohy, které můžete provádět pomocí funkce: 

| Úkol | Syntaxe funkce | Výsledek | 
| ---- | --------------- | -------------- | 
| Vrátí řetězec ve formátu malá písmena. | toLower ("<*text*>") <p>Příklad: toLower('Hello') | text "hello" | 
| Vrátí globálně jedinečný identifikátor (GUID). | GUID() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

Tento příklad ukazuje, jak můžete získat hodnotu z `customerName` parametr a přiřazení, která hodnotu `accountName` vlastnost pomocí [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) funkce ve výrazu:

```json
"accountName": "@parameters('customerName')"
```

Zde jsou některé obecné způsoby, můžete použít ve výrazech funkce:

| Úkol | Syntaxe funkce ve výrazu | 
| ---- | -------------------------------- | 
| Práci s položku pomocí předání této položky funkce. | "@<*%{FunctionName/*> (<*položky*>)" | 
| 1. Získat *parameterName*na hodnotu na základě vnořeného `parameters()` funkce. </br>2. Práci s výsledkem předáním tuto hodnotu na *%{FunctionName/*. | "@<*%{FunctionName/*> (parametry (" <*parameterName*> "))" | 
| 1. Získat výsledek z vnořené funkce vnitřní *%{FunctionName/*. </br>2. Výsledek předat vnější funkce *functionName2*. | "@<*functionName2*> (<*%{FunctionName/*> (<*položky*>))" | 
| 1. Získat výsledek z *%{FunctionName/*. </br>2. Vzhledem k tomu, že výsledkem je objekt s vlastností *propertyName*, získání hodnoty této vlastnosti. | "@<*%{FunctionName/*>(<*item*>). <*propertyName*>" | 
||| 

Například `concat()` funkce může trvat dvě nebo více řetězcové hodnoty jako parametry. Tato funkce kombinuje tyto řetězce do jednoho řetězce. Můžete buď předat v textové literály, například "Sophia" a "Owen", abyste měli kombinované řetězec "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Nebo můžete získat řetězcové hodnoty z parametrů. Tento příklad používá `parameters()` funkce v každé `concat()` parametr a `firstName` a `lastName` parametry. Pak předejte výsledné řetězce `concat()` fungovat tak, aby získat kombinovaný řetězec, například "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

V obou případech i příklady přiřadit výsledek, který má `customerName` vlastnost. 

Podrobné informace o každé funkci, najdete v článku [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).
Nebo pokračujte ve čtení o funkce založené na jejich obecné účely.

<a name="string-functions"></a>

### <a name="string-functions"></a>Řetězcové funkce

Chcete-li pracovat s řetězce, můžete tyto funkce pro řetězce a také některé [funkce kolekce](#collection-functions). Řetězcové funkce lze použít pouze u řetězců. 

| String – funkce | Úkol | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Kombinací dvou nebo více řetězců a vrátí součet řetězec. | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Zkontrolujte, zda řetězec končí určený dílčí řetězec. | 
| [Identifikátor GUID](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generování globálně jedinečný identifikátor (GUID) jako řetězec. | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Vrátí počáteční pozici pro dílčí řetězec. | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Vrátí koncovou pozici pro dílčí řetězec. | 
| [Nahradit](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Nahraďte je dílčí řetězec zadaný řetězec a vrátí aktualizované řetězec. | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Vrátí pole, které obsahuje všechny znaky v řetězci a odděluje jednotlivé znaky konkrétní oddělovací znak. | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Zkontrolujte, zda řetězec začíná konkrétní dílčí řetězec. | 
| [dílčí řetězec](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Vrácení znaků z řetězce od zadané pozici. | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Vrátí řetězec ve formátu malá písmena. | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Vrátí řetězec ve formátu velká písmena. | 
| [Uvolnění dočasné paměti](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Odebere úvodní a koncové mezery z hodnoty řetězce a vrátí aktualizované řetězec. | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>Kolekce funkcí

Pro práci s kolekcí, obecně pole, řetězce a v některých případech slovníky, můžete tyto funkce kolekce. 

| Kolekce – funkce | Úkol | 
| ------------------- | ---- | 
| [Obsahuje](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Zkontrolujte, zda kolekce obsahuje konkrétní položku. |
| [prázdný](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Zkontrolujte, zda kolekce je prázdný. | 
| [první](../logic-apps/workflow-definition-language-functions-reference.md#first) | Vrátí první položku z kolekce. | 
| [průnik](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Vrátí kolekci, která má *pouze* společné položky napříč k zadaným kolekcím. | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Vrátí řetězec, který má *všechny* položky z pole, oddělených zadaný znak. | 
| [poslední](../logic-apps/workflow-definition-language-functions-reference.md#last) | Vrátí poslední položky z kolekce. | 
| [Délka](../logic-apps/workflow-definition-language-functions-reference.md#length) | Vrátí počet položek v řetězec nebo pole. | 
| [Přeskočit](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Odebrání položek z před kolekce a vrátí *všechny ostatní* položky. | 
| [proveďte](../logic-apps/workflow-definition-language-functions-reference.md#take) | Vrátí položky z před kolekce. | 
| [sjednocení](../logic-apps/workflow-definition-language-functions-reference.md#union) | Vrátí kolekci, která má *všechny* položky ze zadaných kolekcí. | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>Porovnání funkcí

K práci s podmínkami, porovnat hodnoty a výsledky výraz nebo vyhodnocení různé druhy logiku, můžete tyto funkce porovnání. Úplné informace o každé funkci, najdete v článku [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Porovnání – funkce | Úkol | 
| ------------------- | ---- | 
| [A](../logic-apps/workflow-definition-language-functions-reference.md#and) | Zkontrolujte, zda jsou všechny výrazy hodnotu true. | 
| [Rovná se](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Zkontrolujte, zda jsou obě hodnoty ekvivalentní. | 
| [větší](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Zkontrolujte, zda je první hodnota je větší než druhá hodnota. | 
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Zkontrolujte, zda je první hodnota je větší než nebo rovna hodnotě druhá hodnota. | 
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Zkontrolujte, zda je výraz true nebo false. Na základě výsledku, vrátí zadanou hodnotu. | 
| [menší](../logic-apps/workflow-definition-language-functions-reference.md#less) | Zkontrolujte, zda je první hodnota menší než druhá hodnota. | 
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Zkontrolujte, zda je první hodnota menší než nebo rovna hodnotě druhá hodnota. | 
| [není](../logic-apps/workflow-definition-language-functions-reference.md#not) | Zkontrolujte, zda výraz je hodnota false. | 
| [Nebo](../logic-apps/workflow-definition-language-functions-reference.md#or) | Zkontrolujte, zda je nejméně jeden výraz hodnotu true. |
||| 

<a name="conversion-functions"></a>

### <a name="conversion-functions"></a>Převodní funkce

Chcete-li změnit typ nebo formát hodnotu, můžete tyto funkce pro převod. Můžete například změnit hodnotu z logickou hodnotu na celé číslo. Informace o způsobu, jakým aplikace logiky zpracovává typy obsahu při převodu, najdete v části [zpracování typy obsahu](../logic-apps/logic-apps-content-type.md). Úplné informace o každé funkci, najdete v článku [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkci pro převod | Úkol | 
| ------------------- | ---- | 
| [Pole](../logic-apps/workflow-definition-language-functions-reference.md#array) | Vrátí pole z jedné zadaný vstup. Více vstupů, najdete v části [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). | 
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Vrátí verzi kódováním base64 řetězce. | 
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Vrátí binární verze pro řetězec s kódováním base64. | 
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Vrátí řetězec verze pro řetězec s kódováním base64. | 
| [Binární](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Vrátí binární verze pro vstupní hodnotu. | 
| [BOOL](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Vrátí logickou verze pro vstupní hodnotu. | 
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Vrátí pole z více vstupů. | 
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Vrátí identifikátor URI dat. pro vstupní hodnotu. | 
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Vrátí binární verze pro data identifikátor URI. | 
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Vrátí řetězec verze pro data identifikátor URI. | 
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Vrátí řetězec verze pro řetězec s kódováním base64. | 
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Vrátí binární verze pro data identifikátor URI. | 
| [decodeuricomponent –](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Vrátí řetězec, nahradí řídicí znaky s dekódované verze. | 
| [encodeuricomponent –](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Vrátí řetězec, který nahradí znaky adresy URL unsafe řídicí znaky. | 
| [Plovoucí desetinná čárka](../logic-apps/workflow-definition-language-functions-reference.md#float) | Vrátí plovoucí číslo pro hodnotu vstupního bodu. | 
| [celá čísla](../logic-apps/workflow-definition-language-functions-reference.md#int) | Vrátí celé číslo verze pro řetězec. | 
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Vrátí hodnotu typu JavaScript Object Notation (JSON) nebo objekt pro řetězec nebo XML. | 
| [Řetězec](../logic-apps/workflow-definition-language-functions-reference.md#string) | Vrátí řetězec verze pro vstupní hodnotu. | 
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Vrátí verzi kódovaný identifikátor URI pro vstupní hodnotu nahrazením znaky adresy URL unsafe řídicí znaky. | 
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Vrátí binární verze pro řetězec kódovaný identifikátor URI. | 
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Vrátí řetězec verze pro řetězec kódovaný identifikátor URI. | 
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Vrátí verzi XML pro řetězec. | 
||| 

<a name="math-functions"></a>

### <a name="math-functions"></a>Matematické funkce

Chcete-li pracovat s celá čísla a obtékaných objektů, můžete tyto matematické funkce. Úplné informace o každé funkci, najdete v článku [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Matematické funkce | Úkol | 
| ------------- | ---- | 
| [Přidat](../logic-apps/workflow-definition-language-functions-reference.md#add) | Vrátí výsledek v přidávání dvou čísel. | 
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Vrátí výsledek dělení dvou čísel. | 
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Vrátí nejvyšší hodnotu ze sady čísla nebo pole. | 
| [Min.](../logic-apps/workflow-definition-language-functions-reference.md#min) | Vrátí nejnižší hodnotu ze sady čísla nebo pole. | 
| [MOD](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Vrátí zbytek dělení dvou čísel. | 
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Vrátí produktu z vynásobením dvou čísel. | 
| [rand –](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Vrátí náhodné číslo ze zadaného rozsahu. | 
| [rozsah](../logic-apps/workflow-definition-language-functions-reference.md#range) | Vrátí pole celé číslo, které začne ze zadané celé číslo. | 
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Vrátí výsledek z odečtením druhé číslo z první číslo. | 
||| 

<a name="date-time-functions"></a>

### <a name="date-and-time-functions"></a>Funkce data a času

Pro práci s daty a časy, můžete tyto funkce data a času.
Úplné informace o každé funkci, najdete v článku [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Datum nebo čas – funkce | Úkol | 
| --------------------- | ---- | 
| [Přidat_dny](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Přidejte počet dnů do časového razítka. | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Přidáte počet hodin časové razítko. | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Počet minut, přidejte do časového razítka. | 
| [Přidat_sekundy](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Počet sekund, přidejte do časového razítka. |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Počet jednotek přidejte do časového razítka. Viz také [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Převeďte časové razítko z koordinovaný univerzální čas (UTC) na časové pásmo cíl. | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Převeďte časovým razítkem ze zdroje časové pásmo na cílové časové pásmo. | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Převeďte časové razítko o časovém pásmu zdroj koordinovaný univerzální čas (UTC). | 
| [DayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Vrátí den komponentu měsíc z časového razítka. | 
| [DayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Vrátí den týdne součásti z časového razítka. | 
| [DayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Vrátí den komponentu rok z časového razítka. | 
| [FormatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Vrátí datum od časového razítka. | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Vrátí aktuální časové razítko plus jednotky zadané doby. Viz také [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Vrátí aktuální časové razítko minus jednotky zadané doby. Viz také [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Vrátí začátek dne pro časové razítko. | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Vrátí začátek hodiny pro hodnotu časového razítka. | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Vrátí začátek měsíce pro časové razítko. | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odečte počet časových jednotek z časového razítka. Viz také [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). | 
| [rysky](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Vrátí `ticks` hodnota vlastnosti pro zadané časové razítko. | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Vrátí aktuální časové razítko jako řetězec. | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>Funkce pracovního postupu

Vám můžou pomoct tyto funkce pracovního postupu:

* Získání podrobností o instanci pracovního postupu v době běhu. 
* Práce s vstupy pro vytvoření instance aplikace logiky.
* Výstupy odkazovat z triggery a akce.

Například můžete odkazovat výstupy z jednu akci a tato data použít v rámci novější akce. Úplné informace o každé funkci, najdete v článku [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkce pracovního postupu | Úkol | 
| ----------------- | ---- | 
| [Akce](../logic-apps/workflow-definition-language-functions-reference.md#action) | Vrátí aktuální akce Výstup v modulu runtime nebo hodnoty z jiných páry název hodnota JSON. Viz také [akce](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Vrátí akci `body` výstup za běhu. Viz také [textu](../logic-apps/workflow-definition-language-functions-reference.md#body). | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Vrátí výstup akce za běhu. V tématu [akce](../logic-apps/workflow-definition-language-functions-reference.md#actions). | 
| [Akce](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Vrátíte výstup akce v modulu runtime nebo hodnoty z jiných páry název hodnota JSON. Viz také [akce](../logic-apps/workflow-definition-language-functions-reference.md#action).  | 
| [Text](#body) | Vrátí akci `body` výstup za běhu. Viz také [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Vytvořit pole s hodnotami, které odpovídají názvu klíče v *data formuláře* nebo *formulářem kódované* výstupy akce. | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Vrátí jednu hodnotu, která odpovídá názvu klíče při akci *data formuláře* nebo *výsledek zakódovaný ve formátu*. | 
| [Položka](../logic-apps/workflow-definition-language-functions-reference.md#item) | Když uvnitř opakující se akce přes pole, vrátí aktuální položky v poli během aktuální opakování akce. | 
| [Položky](../logic-apps/workflow-definition-language-functions-reference.md#items) | Když uvnitř pro každou nebo proveďte až smyčky, vraťte se s aktuální položkou. Zadaný smyčky.| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Vrátí "Zpětného volání adresu" který volá aktivační události nebo akce. | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Vrátí pro určitou část textu v výstup akce, který má více částí. | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Vrátí hodnotu pro parametr, který je popsán v svou definici. aplikaci logiky. | 
| [Aktivační události](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Vrátí výstup aktivační události za běhu, nebo z jiných páry název hodnota JSON. Viz také [triggerOutputs](#triggerOutputs) a [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Vrátí aktivační události `body` výstup za běhu. V tématu [aktivační událost](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Vrátí jednu hodnotu odpovídající názvu klíče v *data formuláře* nebo *formulářem kódované* aktivovat výstupy. | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Vrátí text pro určitou část ve výstupu aktivační událost s více částmi. | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Vytvoření pole, jejichž hodnoty odpovídat názvu klíče v *data formuláře* nebo *formulářem kódované* aktivovat výstupy. | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Vrátíte výstup aktivační události v modulu runtime nebo hodnoty z jiných páry název hodnota JSON. V tématu [aktivační událost](../logic-apps/workflow-definition-language-functions-reference.md#trigger). | 
| [proměnné](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Vrátí hodnotu pro zadanou proměnnou. | 
| [pracovní postup](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Vrátí všechny podrobnosti o samotného pracovního postupu při běhu. | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>Identifikátor URI funkcí analýzy

Práce s identifikátory URI (URI) a získat různé hodnoty pro tyto identifikátory URI, můžete použít tyto URI funkcí analýzy. Úplné informace o každé funkci, najdete v článku [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Identifikátor URI analýza – funkce | Úkol | 
| -------------------- | ---- | 
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Vrátí `host` hodnota pro identifikátor URI (URI). | 
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Vrátí `path` hodnota pro identifikátor URI (URI). | 
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Vrátí `path` a `query` hodnoty pro identifikátor URI (URI). | 
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Vrátí `port` hodnota pro identifikátor URI (URI). | 
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Vrátí `query` hodnota pro identifikátor URI (URI). | 
| [UriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Vrátí `scheme` hodnota pro identifikátor URI (URI). | 
||| 

<a name="manipulation-functions"></a>

### <a name="json-and-xml-functions"></a>Funkce JSON a XML

Chcete-li pracovat s objekty JSON a z uzlů XML, můžete tyto funkce pro zpracování. Úplné informace o každé funkci, najdete v článku [abecedně řazená referenční dokumentace článku](../logic-apps/workflow-definition-language-functions-reference.md).

| Funkci pro zpracování | Úkol | 
| --------------------- | ---- | 
| [AddProperty –](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Přidejte vlastnost a její hodnota nebo dvojice název hodnota, na objekt JSON a vrátí objekt aktualizované. | 
| [sloučení](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Vrátí první hodnotu než null z jednoho nebo více parametrů. | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Odebrání vlastnosti z objektu JSON a vrátí objekt aktualizované. | 
| [SetProperty –](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Nastavit hodnotu pro vlastnost objekt JSON a vrátí objekt aktualizované. | 
| [výraz XPath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Zkontrolujte XML pro uzly nebo hodnoty, které odpovídají výraz XPath (XML Path Language) a vrátí odpovídající uzlů nebo hodnoty. | 
||| 

## <a name="next-steps"></a>Další postup

* Další informace o [jazyk definic workflowů akce a aktivační události](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Další informace o prostřednictvím kódu programu vytváření a Správa aplikací logiky pomocí [pracovního postupu REST API](https://docs.microsoft.com/rest/api/logic/workflows)

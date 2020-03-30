---
title: Odkaz na schéma jazyka definice pracovního postupu
description: Referenční příručka k schématu JSON a syntaxi pro jazyk definice pracovního postupu, který popisuje pracovní postupy v aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283859"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Referenční příručka schématu pro jazyk definice pracovního postupu v aplikacích Logika Azure

Když vytvoříte aplikaci logiky v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vaše aplikace logiky má základní definici pracovního postupu, která popisuje skutečnou logiku, která běží ve vaší aplikaci logiky. Definice pracovního postupu používá [JSON](https://www.json.org/) a řídí se strukturou, která je ověřena schématem jazyka definice pracovního postupu. Tento odkaz poskytuje přehled o této struktuře a o tom, jak schéma definuje atributy v definici pracovního postupu.

## <a name="workflow-definition-structure"></a>Struktura definice pracovního postupu

Definice pracovního postupu vždy obsahuje aktivační událost pro vytváření instancí aplikace logiky a jednu nebo více akcí, které se spustí po spuštění aktivační události.

Zde je struktura vysoké úrovně pro definici pracovního postupu:

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

| Atribut | Požaduje se | Popis |
|-----------|----------|-------------|
| `definition` | Ano | Počáteční prvek pro definici pracovního postupu |
| `$schema` | Pouze při externím odkazování na definici pracovního postupu | Umístění souboru schématu JSON, které popisuje verzi jazyka definice pracovního postupu, které naleznete zde: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Ne | Definice pro jednu nebo více akcí, které mají být provedeny za běhu pracovního postupu. Další informace naleznete v [tématu Aktivační události a akce](#triggers-actions). <p><p>Maximální počet akcí: 250 |
| `contentVersion` | Ne | Číslo verze definice pracovního postupu, která je ve výchozím nastavení "1.0.0.0". Chcete-li při nasazování pracovního postupu identifikovat a potvrdit správnou definici, zadejte hodnotu, která se má použít. |
| `outputs` | Ne | Definice pro výstupy vrátit z pracovního postupu spustit. Další informace naleznete v [tématu Výstupy](#outputs). <p><p>Maximální počet výstupů: 10 |
| `parameters` | Ne | Definice pro jeden nebo více parametrů, které předávají hodnoty, které se mají použít v době běhu aplikace logiky. Další informace naleznete v tématu [Parametry](#parameters). <p><p>Maximální počet parametrů: 50 |
| `staticResults` | Ne | Definice pro jeden nebo více statických výsledků vrácených akcemi jako falešné výstupy, pokud jsou u těchto akcí povoleny statické výsledky. V každé definici `runtimeConfiguration.staticResult.name` akce atribut odkazuje `staticResults`na odpovídající definici uvnitř . Další informace naleznete v [tématu Statické výsledky](#static-results). |
| `triggers` | Ne | Definice pro jednu nebo více aktivačních událostí, které instanci pracovního postupu. Můžete definovat více než jednu aktivační událost, ale pouze s jazykem definice pracovního postupu, nikoli vizuálně prostřednictvím Návrháře aplikací logiky. Další informace naleznete v [tématu Aktivační události a akce](#triggers-actions). <p><p>Maximální počet aktivačních událostí: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Aktivační události a akce

V definici pracovního `actions` postupu a oddíly a definují volání, `triggers` ke kterým dochází během provádění pracovního postupu. Syntaxe a další informace o těchto oddílech naleznete [v tématu Aktivační události a akce pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parametry

Životní cyklus nasazení má obvykle různá prostředí pro vývoj, testování, přípravu a produkční prostředí. Při nasazování aplikací logiky do různých prostředí pravděpodobně budete chtít použít různé hodnoty, například připojovací řetězce, na základě vašich potřeb nasazení. Nebo můžete mít hodnoty, které chcete znovu použít v celé aplikaci logiky bez hardcoding nebo které se často mění. V `parameters` části definice pracovního postupu můžete definovat nebo upravit parametry pro hodnoty, které vaše aplikace logiky používá za běhu. Tyto parametry je nutné nejprve definovat, než budete moci odkazovat na tyto parametry jinde v definici pracovního postupu.

Zde je obecná struktura pro definici parametru:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Atribut | Požaduje se | Typ | Popis |
|-----------|----------|------|-------------|
| <*název parametru*> | Ano | Řetězec | Název parametru, který chcete definovat |
| <*typ parametru*> | Ano | int, float, řetězec, bool, pole, objekt, securestring, secureobject <p><p>**Poznámka**: Pro všechna hesla, klíče a `securestring` `secureobject` tajné klíče `GET` použijte typy nebo, protože operace nevrátí tyto typy. Další informace o zabezpečení parametrů naleznete v [tématu Doporučení zabezpečení pro akce a vstupní parametry](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | Typ parametru |
| <*výchozí hodnota parametru*> | Ano | Stejné jako`type` | Výchozí hodnota parametru, která se má použít, pokud není zadána žádná hodnota při vytváření instancí pracovního postupu. Atribut `defaultValue` je vyžadován tak, aby Návrhář aplikace logiky můžete správně zobrazit parametr, ale můžete zadat prázdnou hodnotu. |
| <*pole s povolenými parametry*> | Ne | Pole | Pole s hodnotami, které může parametr přijmout |
| <*popis parametru*> | Ne | Objekt JSON | Jakékoli další podrobnosti o parametrech, například popis parametru |
||||

Dále vytvořte [šablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) pro definici pracovního postupu, definujte parametry šablony, které přijímají hodnoty, které chcete při nasazení, nahradit pevně zakódované hodnoty odkazy na parametry definice šablony nebo pracovního postupu podle potřeby a uložit hodnoty, které se mají použít při nasazení v samostatném [souboru parametrů](../azure-resource-manager/templates/parameter-files.md). Tímto způsobem můžete změnit tyto hodnoty snadněji prostřednictvím souboru parametrů bez nutnosti aktualizovat a znovu nasadit aplikaci logiky. Informace, které jsou citlivé nebo musí být zabezpečené, jako jsou uživatelská jména, hesla a tajné klíče, můžete tyto hodnoty uložit do úložiště klíčů Azure a nechat soubor parametrů načíst tyto hodnoty z trezoru klíčů. Další informace a příklady týkající se definování parametrů na úrovních definice šablony a pracovního postupu najdete v [tématu Přehled: Automatizace nasazení pro aplikace logiky pomocí šablon Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Statické výsledky

V `staticResults` atributu definujte mock `outputs` akce `status` a vrátí se, když je zapnuto nastavení statického výsledku akce. V definici akce `runtimeConfiguration.staticResult.name` atribut odkazuje na název definice statického `staticResults`výsledku uvnitř . Zjistěte, jak můžete [testovat aplikace logiky s falešnými daty nastavením statických výsledků](../logic-apps/test-logic-apps-mock-data-static-results.md).

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
| <*statická definice-název*> | Ano | Řetězec | Název definice statického výsledku, na který `runtimeConfiguration.staticResult` může definice akce odkazovat prostřednictvím objektu. Další informace naleznete v [tématu Runtime configuration settings](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Můžete použít libovolný jedinečný název, který chcete. Ve výchozím nastavení je tento jedinečný název připojen s číslem, které se podle potřeby zintáží. |
| <*výstupní atributy a vrácené hodnoty*> | Ano | Různé | Požadavky na tyto atributy se liší v závislosti na různých podmínkách. Například pokud `status` je `Succeeded`, `outputs` atribut obsahuje atributy a hodnoty vrácené jako mock výstupy akce. Pokud `status` je `Failed`, `outputs` atribut `errors` obsahuje atribut, což je pole `message` s jedním nebo více chybových objektů, které mají informace o chybě. |
| <*hodnoty záhlaví*> | Ne | JSON | Všechny hodnoty záhlaví vrácené akcí |
| <*vrácený stavový kód*> | Ano | Řetězec | Stavový kód vrácený akcí |
| <*stav akce*> | Ano | Řetězec | Stav akce, například `Succeeded` nebo`Failed` |
|||||

Například v této definici `runtimeConfiguration.staticResult.name` akce HTTP `HTTP0` atribut `staticResults` odkazuje uvnitř atributu, kde jsou definovány falešné výstupy pro akci. Atribut `runtimeConfiguration.staticResult.staticResultOptions` určuje, že nastavení statického výsledku je `Enabled` v akci HTTP.

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

Akce HTTP vrátí výstupy `HTTP0` v `staticResults`definici uvnitř . V tomto příkladu pro stavový kód `OK`je výstup mock . Pro hodnoty záhlaví je `"Content-Type": "application/JSON"`výstup mock . Pro stav akce mock výstup je `Succeeded`.

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

S JSON můžete mít literál hodnoty, které existují v době návrhu, například:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Můžete také mít hodnoty, které neexistují až do běhu. Chcete-li tyto hodnoty reprezentovat, můžete použít *výrazy*, které jsou vyhodnocovány za běhu. Výraz je posloupnost, která může obsahovat jednu nebo více [funkcí](#functions), [operátorů](#operators), proměnných, explicitních hodnot nebo konstant. V definici pracovního postupu můžete použít výraz kdekoli v řetězcové hodnotě JSON\@předponou výrazu pomocí znaku at ( ). Při vyhodnocování výrazu, který představuje hodnotu JSON, je tělo výrazu extrahováno odebráním znaku \@ a vždy vede k jiné hodnotě JSON.

Například pro dříve `customerName` definovanou vlastnost můžete získat hodnotu vlastnosti pomocí funkce [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) ve `accountName` výrazu a přiřadit tuto hodnotu vlastnosti:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Interpolace řetězců* také umožňuje používat více výrazů uvnitř řetězců, které jsou zabaleny znakem \@ a složenými závorkami ({}). Zde je syntaxe:

```json
@{ "<expression1>", "<expression2>" }
```

Výsledkem je vždy řetězec, takže tato `concat()` schopnost je podobná funkci, například: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Pokud máte literál řetězec, \@ který začíná znakem, předpona \@ znak s jiným \@ znakem jako řídicí znak:\@\@

Tyto příklady ukazují, jak jsou vyhodnocovány výrazy:

| Hodnota JSON | Výsledek |
|------------|--------|
| "Sophia Owen" | Vrátit tyto znaky: 'Sophia Owen' |
| "pole[1]" | Vrátit tyto znaky: 'array[1]' |
| "\@\@" | Vrátit tyto znaky jako řetězec s\@jedním znakem: ' |
| " \@" | Vrátit tyto znaky jako řetězec se \@dvěma znaky: ' |
|||

Pro tyto příklady předpokládejme, že definujete "myBirthMonth" rovna "leden" a "myAge" rovná číslo 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Tyto příklady ukazují, jak jsou vyhodnocovány následující výrazy:

| JSON výraz | Výsledek |
|-----------------|--------|
| "\@parametry('myBirthMonth')" | Vrátit tento řetězec: "Leden" |
| "\@{parameters('myBirthMonth')}" | Vrátit tento řetězec: "Leden" |
| "\@parametry('myAge')" | Vraťte toto číslo: 42 |
| "\@{parameters('myAge')}" | Vrátit toto číslo jako řetězec: "42" |
| "Můj věk \@je {parameters('myAge')}" | Vrátit tento řetězec: "Můj věk je 42" |
| "\@concat('Můj věk je ', string(parameters('myAge')))" | Vrátit tento řetězec: "Můj věk je 42" |
| "Můj věk \@ \@je {parameters('myAge')}" | Vrátí tento řetězec, který obsahuje výraz: \@"Můj věk je {parameters('myAge')}' |
|||

Při vizuální práci v Návrháři aplikací logiky můžete vytvářet výrazy prostřednictvím tvůrce výrazů, například:

![Tvůrce výrazů aplikace Pro aplikace >](./media/logic-apps-workflow-definition-language/expression-builder.png)

Po dokončení se výraz zobrazí pro odpovídající vlastnost v definici pracovního postupu, například `searchQuery` vlastnost zde:

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

<a name="outputs"></a>

## <a name="outputs"></a>Výstupy

V `outputs` části definujte data, která může pracovní postup vrátit po dokončení spuštění. Chcete-li například sledovat konkrétní stav nebo hodnotu z každého spuštění, určete, že výstup pracovního postupu vrátí tato data.

> [!NOTE]
> Při odpovídání na příchozí požadavky z rozhraní REST API `outputs`služby nepoužívejte . Místo toho `Response` použijte typ akce. Další informace naleznete [v tématu Aktivační události a akce pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md).

Zde je obecná struktura pro definici výstupu:

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
| <*název klíče*> | Ano | Řetězec | Název klíče pro výstupní vrácenou hodnotu |
| <*typ klíče*> | Ano | int, float, řetězec, securestring, bool, array, JSON objekt | Typ pro výstupní vrácenou hodnotu |
| <*klíč-hodnota*> | Ano | Stejné jako <*typ klíče*> | Výstupní vrácená hodnota |
|||||

Chcete-li získat výstup z pracovního postupu spustit, zkontrolujte historii spuštění aplikace logiky a podrobnosti na portálu Azure nebo použijte [rozhraní API PRO PRACOVNÍ POSTUP REST](https://docs.microsoft.com/rest/api/logic/workflows). Výstup můžete předat taky externím systémům, například Power BI, abyste mohli vytvářet řídicí panely.

<a name="operators"></a>

## <a name="operators"></a>Operátory

Ve [výrazech](#expressions) a [funkcích](#functions)provádějí operátory určité úkoly, například odkaz na vlastnost nebo hodnotu v poli.

| Operátor | Úkol |
|----------|------|
| ' | Chcete-li použít literál řetězce jako vstup nebo ve výrazech a funkcích, zabalte řetězec pouze s jednoduchými uvozovkami, `'<myString>'`například . Nepoužívejte dvojité uvozovky (""), které jsou v konfliktu s formátováním JSON kolem celého výrazu. Například: <p>**Ano:** délka ('Hello') </br>**Ne:** délka ("Hello") <p>Když předáte pole nebo čísla, nepotřebujete obtékání interpunkce. Například: <p>**Ano:** délka([1, 2, 3]) </br>**Č.:** délka("[1, 2, 3]") |
| [] | Chcete-li odkazovat na hodnotu na určité pozici (index) v poli, použijte hranaté závorky. Chcete-li například získat druhou položku v poli: <p>`myArray[1]` |
| . | Chcete-li odkazovat na vlastnost v objektu, použijte operátor tečka. Chcete-li například `name` získat `customer` vlastnost pro objekt JSON: <p>`"@parameters('customer').name"` |
| ? | Chcete-li odkazovat na vlastnosti null v objektu bez chyby za běhu, použijte operátor otazník. Chcete-li například zpracovat prázdné výstupy z aktivační události, můžete použít tento výraz: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funkce

Některé výrazy získat své hodnoty z akce za běhu, které nemusí ještě existovat, když se spustí definice pracovního postupu. Chcete-li odkazovat na tyto hodnoty nebo s nimi pracovat ve výrazech, můžete použít [*funkce,*](../logic-apps/workflow-definition-language-functions-reference.md) které poskytuje jazyk definice pracovního postupu.

## <a name="next-steps"></a>Další kroky

* Informace o [akcích a aktivačních událostech jazyka definice pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Informace o programovém vytváření a správě aplikací logiky pomocí [rozhraní API REST pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows)

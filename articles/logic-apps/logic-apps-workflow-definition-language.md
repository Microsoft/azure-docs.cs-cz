---
title: Referenční dokumentace schématu jazyka definice pracovního postupu
description: Referenční příručka ke schématu a syntaxi JSON pro jazyk definice pracovního postupu, který popisuje pracovní postupy v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 71929cd449f4a00b91cc6c8620b33b0e0c6d506c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87078146"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Referenční příručka schématu pro jazyk definice pracovního postupu v Azure Logic Apps

Když vytvoříte aplikaci logiky v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vaše aplikace logiky obsahuje základní definici pracovního postupu, která popisuje vlastní logiku, která se spouští ve vaší aplikaci logiky. Tato definice pracovního postupu používá [JSON](https://www.json.org/) a dodržuje strukturu, kterou ověřuje schéma jazyka definice pracovního postupu. Tento odkaz poskytuje přehled této struktury a způsob, jakým schéma definuje atributy v definici pracovního postupu.

## <a name="workflow-definition-structure"></a>Struktura definice pracovního postupu

Definice pracovního postupu vždy zahrnuje Trigger pro vytvoření instance aplikace logiky a jednu nebo více akcí, které se spustí po aktivaci triggeru.

Tady je struktura vysoké úrovně pro definici pracovního postupu:

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

| Atribut | Povinné | Popis |
|-----------|----------|-------------|
| `definition` | Ano | Počáteční element pro definici pracovního postupu |
| `$schema` | Pouze v případě, že odkaz odkazuje na externě na definici pracovního postupu | Umístění souboru schématu JSON, které popisuje jazykovou verzi definice pracovního postupu, které najdete tady: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | No | Definice pro jednu nebo více akcí, které mají být provedeny v modulu runtime pracovního postupu. Další informace najdete v tématu [triggery a akce](#triggers-actions). <p><p>Maximální počet akcí: 250 |
| `contentVersion` | No | Číslo verze definice pracovního postupu, které je ve výchozím nastavení "1.0.0.0". Chcete-li při nasazování pracovního postupu lépe identifikovat a potvrdit správnou definici, zadejte hodnotu, která se má použít. |
| `outputs` | No | Definice výstupů, které se mají vrátit z běhu pracovního postupu Další informace najdete v tématu [výstupy](#outputs). <p><p>Maximální počet výstupů: 10 |
| `parameters` | No | Definice jednoho nebo více parametrů, které předají hodnoty pro použití v modulu runtime vaší aplikace logiky Další informace najdete v tématu [parametry](#parameters). <p><p>Maximální počet parametrů: 50 |
| `staticResults` | No | Definice pro jednu nebo více statických výsledků vrácených akcemi jako výstupní výstupy, když jsou na těchto akcích povoleny statické výsledky. V každé definici akce `runtimeConfiguration.staticResult.name` atribut odkazuje na odpovídající definici v rámci `staticResults` . Další informace najdete v tématu [statické výsledky](#static-results). |
| `triggers` | No | Definice pro jednu nebo více triggerů, které vytváří instanci pracovního postupu. Můžete definovat více než jednu Trigger, ale pouze s jazykem definice pracovního postupu, nikoli vizuálně prostřednictvím návrháře Logic Apps. Další informace najdete v tématu [triggery a akce](#triggers-actions). <p><p>Maximální počet aktivačních událostí: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Aktivační události a akce

V definici pracovního postupu `triggers` `actions` definují oddíly a volání, ke kterým dochází během provádění pracovního postupu. Syntaxi a další informace o těchto oddílech najdete v tématu [triggery a akce pracovních postupů](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parametry

Životní cyklus nasazení má obvykle různá prostředí pro vývoj, testování, přípravu a produkci. Při nasazování Logic Apps do různých prostředí pravděpodobně budete chtít v závislosti na potřebách nasazení použít jiné hodnoty, například připojovací řetězce. Nebo můžete mít hodnoty, které chcete znovu použít v rámci aplikace logiky bez zakódujeme nebo změny často. V části definice pracovního postupu `parameters` můžete definovat nebo upravit parametry pro hodnoty, které vaše aplikace logiky používá za běhu. Než budete moci odkazovat na tyto parametry jinde v definici pracovního postupu, musíte tyto parametry definovat jako první.

Tady je obecná struktura definice parametru:

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

| Atribut | Požaduje se | Typ | Description |
|-----------|----------|------|-------------|
| <*název parametru*> | Ano | Řetězec | Název parametru, který chcete definovat |
| <*typ parametru*> | Yes | int, float, String, bool, Array, Object, SecureString, secureobject <p><p>**Poznámka**: pro všechna hesla, klíče a tajné klíče použijte `securestring` typy nebo, `secureobject` protože `GET` operace tyto typy nevrátí. Další informace o zabezpečení parametrů najdete v tématu [doporučení zabezpečení pro parametry akce a vstup](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | Typ parametru |
| <*výchozí parametr-hodnota*> | Yes | Stejné jako `type` | Výchozí hodnota parametru, která má být použita, pokud není zadána žádná hodnota při vytváření instance pracovního postupu. `defaultValue`Atribut je vyžadován, aby návrhář aplikace logiky mohl správně zobrazit parametr, ale můžete zadat prázdnou hodnotu. |
| <*pole s parametrem--------Parameter-Values*> | No | Pole | Pole s hodnotami, které může parametr přijmout |
| <*Parametr-Description*> | No | Objekt JSON | Jakékoli další podrobnosti o parametrech, jako je například popis parametru |
||||

Dále vytvořte [šablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) pro definici pracovního postupu, definujte parametry šablony, které přijmou hodnoty, které chcete v nasazení, nahraďte hodnoty pevně zakódované odkazy na šablonu nebo parametry definice pracovního postupu podle potřeby a uložte hodnoty, které chcete použít při nasazení v samostatném [souboru parametrů](../azure-resource-manager/templates/parameter-files.md). Tímto způsobem můžete tyto hodnoty snadno měnit pomocí souboru parametrů, aniž byste museli aktualizovat a znovu nasazovat aplikaci logiky. Pro informace, které jsou citlivé nebo musí být zabezpečené, jako jsou uživatelská jména, hesla a tajné klíče, můžete tyto hodnoty uložit v Azure Key Vault a nechat si soubory parametrů načíst tyto hodnoty z vašeho trezoru klíčů. Další informace a příklady definování parametrů na úrovních definice šablony a pracovního postupu najdete v tématu [Přehled: Automatizace nasazení pro Logic Apps pomocí šablon Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Statické výsledky

V `staticResults` atributu definujte prvek akce `outputs` a `status` , který akce vrátí, pokud je zapnuto nastavení statického výsledku akce. V definici akce `runtimeConfiguration.staticResult.name` atribut odkazuje na název statického výsledku definice v rámci `staticResults` . [Pomocí nastavení statických výsledků se dozvíte, jak testovat aplikace logiky pomocí](../logic-apps/test-logic-apps-mock-data-static-results.md)napodobných dat.

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

| Atribut | Požaduje se | Typ | Description |
|-----------|----------|------|-------------|
| <*statický výsledek – definice – název*> | Ano | Řetězec | Název statické definice výsledku, kterou může definice akce odkazovat prostřednictvím `runtimeConfiguration.staticResult` objektu. Další informace najdete v tématu [nastavení konfigurace modulu runtime](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Můžete použít libovolný jedinečný název, který chcete. Ve výchozím nastavení se tento jedinečný název připojí s číslem, což se zvyšuje podle potřeby. |
| <*výstupní atributy-a-vrácené hodnoty*> | Yes | Různé | Požadavky na tyto atributy se liší v závislosti na různých podmínkách. Například pokud `status` je `Succeeded` , `outputs` atribut obsahuje atributy a hodnoty vrácené jako výstupní výstupy akcí. Pokud `status` je `Failed` , `outputs` atribut obsahuje `errors` atribut, což je pole s jedním nebo více `message` objekty Error, které obsahují informace o chybě. |
| <*hodnoty hlaviček*> | Ne | JSON | Všechny hodnoty hlaviček vracené akcí |
| <*stav-kód – vráceno*> | Ano | Řetězec | Stavový kód vrácený akcí |
| <*akce – stav*> | Ano | Řetězec | Stav akce, například `Succeeded` nebo `Failed` |
|||||

Například v této definici akce HTTP `runtimeConfiguration.staticResult.name` jsou atributy odkazy v atributu, `HTTP0` `staticResults` kde jsou definovány výstupní výstupy pro akci. `runtimeConfiguration.staticResult.staticResultOptions`Atribut určuje, zda je nastavení statického výsledku `Enabled` v akci HTTP.

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

Akce HTTP vrátí výstupy v `HTTP0` definici uvnitř `staticResults` . V tomto příkladu pro stavový kód je výstupní výstup `OK` . V případě hodnot hlaviček je výstup výstupu `"Content-Type": "application/JSON"` . Pro stav akce je výstup výstupu `Succeeded` .

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

Pomocí formátu JSON můžete mít literálové hodnoty, které existují v době návrhu, například:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Můžete také mít hodnoty, které neexistují, až do doby běhu. Pro reprezentaci těchto hodnot můžete použít *výrazy*, které jsou vyhodnocovány v době běhu. Výraz je sekvence, která může obsahovat jednu nebo více [funkcí](#functions), [operátorů](#operators), [proměnných](./logic-apps-create-variables-store-values.md), explicitních hodnot nebo konstant. V definici pracovního postupu můžete použít výraz kdekoli v hodnotě řetězce JSON tím, že se výraz nastaví na znaménko ( \@ ). Při vyhodnocování výrazu, který představuje hodnotu JSON, je text výrazu extrahován odebráním \@ znaku a vždy vede k jiné hodnotě JSON.

Například pro dříve definovanou `customerName` vlastnost můžete získat hodnotu vlastnosti pomocí funkce [Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) ve výrazu a přiřadit tuto hodnotu k `accountName` vlastnosti:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Interpolace řetězců* také umožňuje použít více výrazů v řetězcích, které jsou zabaleny \@ znakem a složenou závorkou ( {} ). Tady je syntaxe:

```json
@{ "<expression1>", "<expression2>" }
```

Výsledkem je vždy řetězec, což znamená, že tato funkce bude podobná `concat()` funkci, například: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Pokud máte řetězcový literál, který začíná \@ znakem, \@ nahraďte znak jiným \@ znakem jako řídicí znak: \@\@

Tyto příklady ukazují, jak jsou výrazy vyhodnocovány:

| Hodnota JSON | Výsledek |
|------------|--------|
| "Sophia Owen" | Vrátí tyto znaky: ' Sophia Owen ' |
| Array [1] | Vrátí tyto znaky: Array [1]. |
| "\@\@" | Vrátí tyto znaky jako řetězec s jedním znakem: ' \@ ' |
| " \@" | Vrátí tyto znaky jako řetězec se dvěma znaky: ' \@ ' |
|||

Pro tyto příklady Předpokládejme, že definujete "myBirthMonth" rovnající se "lednu" a "myAge" rovnající se číslu 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Tyto příklady ukazují, jak jsou vyhodnocovány následující výrazy:

| Výraz JSON | Výsledek |
|-----------------|--------|
| " \@ Parameters (' myBirthMonth ')" | Vrátí tento řetězec: leden. |
| " \@ {Parameters (' myBirthMonth ')}" | Vrátí tento řetězec: leden. |
| " \@ Parameters (' myAge ')" | Vrátit toto číslo: 42 |
| " \@ {Parameters (' myAge ')}" | Vrátí toto číslo jako řetězec: "42" |
| "Má věk \@ {Parameters (' myAge ')}" | Vrátí tento řetězec: "moje věk je 42." |
| \@Concat (' má stáří je ', řetězec (parametry (' myAge '))) | Vrátí tento řetězec: "moje věk je 42." |
| "Má věk \@ \@ {Parameters (' myAge ')}" | Vrátí tento řetězec, který obsahuje výraz: "moje stáří je \@ {Parameters (' myAge ')}. |
|||

Při práci vizuálně v Návrháři Logic Apps můžete vytvářet výrazy prostřednictvím Tvůrce výrazů, například:

![Tvůrce výrazů > návrháře Logic Apps](./media/logic-apps-workflow-definition-language/expression-builder.png)

Až budete hotovi, výraz se zobrazí pro odpovídající vlastnost v definici pracovního postupu, například `searchQuery` vlastnost zde:

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

V `outputs` části definujte data, která může pracovní postup vracet po dokončení práce. Chcete-li například sledovat konkrétní stav nebo hodnotu z každého spuštění, určete, že výstup pracovního postupu vrací tato data.

> [!NOTE]
> Při reagování na příchozí žádosti ze REST API služby nepoužívejte `outputs` . Místo toho použijte `Response` typ akce. Další informace najdete v tématu [triggery a akce pracovních postupů](../logic-apps/logic-apps-workflow-actions-triggers.md).

Tady je obecná struktura definice výstupu:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Atribut | Požaduje se | Typ | Description |
|-----------|----------|------|-------------|
| <*název klíče*> | Ano | Řetězec | Název klíče pro návratovou hodnotu výstupu |
| <*typ klíče*> | Yes | int, float, String, SecureString, bool, Array, JSON objekt | Typ výstupní návratové hodnoty |
| <*hodnota klíče*> | Yes | Stejné jako <*typ klíče*> | Výstupní návratová hodnota |
|||||

Pokud chcete získat výstup z pracovního postupu, přečtěte si historii spuštění vaší aplikace logiky a podrobnosti v Azure Portal nebo použijte [pracovní postup REST API](/rest/api/logic/workflows). Výstup můžete také předat externím systémům, například Power BI, abyste mohli vytvářet řídicí panely.

<a name="operators"></a>

## <a name="operators"></a>Operátory

Ve [výrazech](#expressions) a [funkcích](#functions)operátory provádějí konkrétní úkoly, jako je například odkaz na vlastnost nebo hodnotu v poli.

| Operátor | Úkol |
|----------|------|
| ' | Chcete-li použít řetězcový literál jako vstup nebo ve výrazech a funkcích, zabalte řetězec pouze s jednoduchými uvozovkami, například `'<myString>'` . Nepoužívejte dvojité uvozovky (""), které jsou v konfliktu s formátováním JSON kolem celého výrazu. Například: <p>**Ano**: délka (' Hello ') </br>**Ne**: délka ("Hello") <p>Když předáte pole nebo čísla, nebudete potřebovat interpunkci zalomení. Například: <p>**Ano**: délka ([1; 2; 3]) </br>**Ne**: délka ("[1, 2, 3]") |
| [] | Pro odkazování na hodnotu na konkrétní pozici (index) v poli použijte hranaté závorky. Například pro získání druhé položky v poli: <p>`myArray[1]` |
| . | Chcete-li odkazovat na vlastnost v objektu, použijte operátor tečka. Například pro získání `name` vlastnosti pro `customer` objekt JSON: <p>`"@parameters('customer').name"` |
| ? | Chcete-li odkazovat na vlastnosti null v objektu bez běhové chyby, použijte operátor otazník. Chcete-li například zpracovat výstupy s hodnotou null z triggeru, můžete použít tento výraz: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

Některé výrazy získají jejich hodnoty z běhových akcí, které nemusí být k dispozici, když začne běžet definice pracovního postupu. Chcete-li odkazovat nebo pracovat s těmito hodnotami ve výrazech, můžete použít [*funkce*](../logic-apps/workflow-definition-language-functions-reference.md) , které poskytuje jazyk definice pracovního postupu.

## <a name="next-steps"></a>Další kroky

* Další informace o [akcích a triggerech jazyka definice pracovního postupu](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Přečtěte si, jak programově vytvářet a spravovat Logic Apps pomocí [pracovního postupu REST API](/rest/api/logic/workflows)

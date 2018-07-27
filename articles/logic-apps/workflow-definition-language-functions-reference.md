---
title: Funkce reference pro jazyk definice pracovního postupu – Azure Logic Apps | Dokumentace Microsoftu
description: Další informace o funkce pro vytváření aplikací logiky s jazyka definice pracovního postupu
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 04/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 46ccf9484b76ec5f24dba470a194b5b83c32f013
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263772"
---
# <a name="functions-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Functions – reference pro jazyk pro definování pracovních postupů v Azure Logic Apps

Tento článek popisuje funkce, které můžete použít při vytváření automatizovaných pracovních postupů pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Další informace o funkcích v definic aplikací logiky najdete v tématu [jazyka definice pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md#functions). 

> [!NOTE]
> Syntaxe pro parametr definice otazník (?), který se zobrazí po parametr znamená, že parametr je volitelný. Viz například [getFutureTime()](#getFutureTime).

<a name="action"></a>

## <a name="action"></a>akce

Vrátit *aktuální* výstupu akce modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON, které můžete přiřadit k výrazu. Ve výchozím nastavení tato funkce odkazuje na objekt celou akci, ale můžete volitelně zadat vlastnost, jejíž hodnotu chcete. Viz také [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Můžete použít `action()` funguje pouze v těchto umístěních: 

* `unsubscribe` Vlastnost pro akce webhooku, aby měli přístup k výsledku z původní `subscribe` žádosti
* `trackedProperties` Vlastnost pro akci.
* `do-until` Smyčky podmínku pro akci.

```
action()
action().outputs.body.<property> 
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Vlastnost*> | Ne | Řetězec | Název objektu akce vlastnosti jejíž hodnotu chcete: **název**, **startTime**, **endTime**, **vstupy**,  **Vypíše**, **stav**, **kód**, **trackingId**, a **clientTrackingId**. Na webu Azure Portal zjistíte kontrolou podrobnosti konkrétní spuštění historie tyto vlastnosti. Další informace najdete v tématu [rozhraní REST API – akce spuštění pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výstup akce*> | Řetězec | Výstup z aktuální akce nebo vlastností | 
|||| 

<a name="actionBody"></a>

## <a name="actionbody"></a>actionBody

Vrácení akce `body` výstupu za běhu. Zkrácený tvar vlastností `actions('<actionName>').outputs.body`. Zobrazit [body()](#body) a [actions()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Název této akce `body` výstup, který chcete, aby | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výstup textu akce*> | Řetězec | `body` Výstup z zadanou akci | 
|||| 

*Příklad*

Tento příklad načte `body` výstupu z akce Twitter `Get user`: 

```
actionBody('Get_user')
```

A vrátí výsledek:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

## <a name="actionoutputs"></a>actionOutputs

Vrací výstup akce za běhu. Zkrácený tvar vlastností `actions('<actionName>').outputs`. Zobrazit [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Výstup, který má název akce | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*Výstup*> | Řetězec | Výstup na zadanou akci | 
|||| 

*Příklad*

Tento příklad načte výstupu z akce Twitter `Get user`: 

```
actionOutputs('Get_user')
```

A vrátí výsledek:

```json
{ 
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

## <a name="actions"></a>Akce

Vrácení výstupu akce modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON, které můžete přiřadit k výrazu. Ve výchozím nastavení, funkce, odkazuje na objekt celou akci, ale můžete volitelně zadat vlastnost jehož požadovanou hodnotu. Zkrácený tvar vlastností verze najdete v tématu [actionBody()](#actionBody), [actionOutputs()](#actionOutputs), a [body()](#body). Aktuální akci, naleznete v tématu [action()](#action).

> [!NOTE] 
> Dříve, můžete použít `actions()` funkce nebo `conditions` element při určování, že akce spustili na základě výstupu z další akci. Ale chcete-li deklarovat explicitně závislosti mezi akcemi, musí teď používáte závislá akce `runAfter` vlastnost. Další informace o `runAfter` vlastnost, naleznete v tématu [zachytit a zpracovat selhání se vlastnost runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property> 
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Název objektu akce, jejichž výstup má  | 
| <*Vlastnost*> | Ne | Řetězec | Název objektu akce vlastnosti jejíž hodnotu chcete: **název**, **startTime**, **endTime**, **vstupy**,  **Vypíše**, **stav**, **kód**, **trackingId**, a **clientTrackingId**. Na webu Azure Portal zjistíte kontrolou podrobnosti konkrétní spuštění historie tyto vlastnosti. Další informace najdete v tématu [rozhraní REST API – akce spuštění pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výstup akce*> | Řetězec | Výstup z zadanou akci nebo vlastnost | 
|||| 

*Příklad*

Tento příklad načte `status` hodnota Twitter akce `Get user` za běhu: 

```
actions('Get_user').outputs.body.status 
```

A vrátí výsledek: `"Succeeded"`

<a name="add"></a>

## <a name="add"></a>přidat

Vrátí výsledek součtu daných dvou čísel.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*summand_1*>, <*summand_2*> | Ano | Celé číslo, Float, nebo smíšené | Chcete-li přidat čísla | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výsledek součtu*> | Integer nebo Float | Výsledkem přidání zadaných čísel | 
|||| 

*Příklad*

V tomto příkladu přidá zadaného čísla:

```
add(1, 1.5)
```

A vrátí výsledek: `2.5`

<a name="addDays"></a>

## <a name="adddays"></a>Přidat_dny

Přidáte počet dní do časového razítka.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*dny*> | Ano | Integer | Kladné nebo záporné číslo dnů pro přidání | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Časové razítko plus zadaný počet dnů  | 
|||| 

*Příklad 1*

V tomto příkladu přidá 10 dní do zadaného časového razítka:

```
addDays('2018-03-15T13:00:00Z', 10)
```

A vrátí výsledek: `"2018-03-25T00:00:0000000Z"`

*Příklad 2*

V tomto příkladu odečte pět dní od zadaného časového razítka:

```
addDays('2018-03-15T00:00:00Z', -5)
```

A vrátí výsledek: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

## <a name="addhours"></a>addHours

Přidáte počet hodin do časového razítka.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*hodiny*> | Ano | Integer | Kladné nebo záporné číslo hodin pro přidání | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Časové razítko plus zadaný počet hodin  | 
|||| 

*Příklad 1*

V tomto příkladu přidá 10 hodin do zadaného časového razítka:

```
addHours('2018-03-15T00:00:00Z', 10)
```

A vrátí výsledek: `"2018-03-15T10:00:0000000Z"`

*Příklad 2*

V tomto příkladu odečte pět hodin od zadaného časového razítka:

```
addHours('2018-03-15T15:00:00Z', -5)
```

A vrátí výsledek: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

## <a name="addminutes"></a>addMinutes

Počet minut, přidejte do časového razítka.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*minut*> | Ano | Integer | Kladné nebo záporné číslo minut pro přidání | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Časové razítko plus zadaný počet minut | 
|||| 

*Příklad 1*

V tomto příkladu přidá 10 minut do zadaného časového razítka:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

A vrátí výsledek: `"2018-03-15T00:20:00.0000000Z"`

*Příklad 2*

V tomto příkladu odečte za pět minut od zadaného časového razítka:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

A vrátí výsledek: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

## <a name="addproperty"></a>addProperty

Přidání vlastnosti a její hodnotu nebo dvojice název hodnota do objektu JSON a vrácení aktualizovaného objektu. Pokud objekt již existuje v době běhu, funkce vyvolá chybu.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*objekt*> | Ano | Objekt | Objekt JSON, ve které chcete přidat vlastnost | 
| <*Vlastnost*> | Ano | Řetězec | Název vlastnosti, která má přidat | 
| <*Hodnota*> | Ano | Všechny | Hodnota pro vlastnost |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktualizace objektu*> | Objekt | Aktualizovaný objekt JSON se zadanou vlastností | 
|||| 

*Příklad*

V tomto příkladu přidá `accountNumber` vlastnost `customerProfile` objektu, který je převést na JSON pomocí [JSON()](#json) funkce. Funkce přiřadí hodnotu, která je generován [guid()](#guid) funkci a vrátí aktualizovaného objektu:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

## <a name="addseconds"></a>Přidat_sekundy

Počet sekund, po přidání do časového razítka.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*sekundy*> | Ano | Integer | Kladné nebo záporné číslo sady sekund se má přidat | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Časové razítko plus zadaném počtu sekund  | 
|||| 

*Příklad 1*

V tomto příkladu přidá 10 sekund do zadaného časového razítka:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

A vrátí výsledek: `"2018-03-15T00:00:10.0000000Z"`

*Příklad 2*

V tomto příkladu odečte pět sekund do zadaného časového razítka:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

A vrátí výsledek: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

## <a name="addtotime"></a>addToTime

Přidáte počet časových jednotek do časového razítka. Viz také [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*interval*> | Ano | Integer | Počet jednotek určený čas přidání | 
| <*timeUnit*> | Ano | Řetězec | Jednotka času pro použití s *interval*: "Sekundu", "Minute", "Hour", "Day", "Týden", "Měsíc", "Rok" | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Časové razítko a za zadaný počet časových jednotek  | 
|||| 

*Příklad 1*

V tomto příkladu přidá jeden den do zadaného časového razítka:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day') 
```

A vrátí výsledek: `"2018-01-02T00:00:00:0000000Z"`

*Příklad 2*

V tomto příkladu přidá jeden den do zadaného časového razítka:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

A vrátí výsledek volitelné formátu "D": `"Tuesday, January 2, 2018"`

<a name="and"></a>

## <a name="and"></a>a

Zkontrolujte, zda jsou všechny výrazy hodnotu true. Vrátí hodnotu PRAVDA, pokud jsou splněny všechny výrazy nebo vrátit hodnotu false v případě nejméně jeden výraz hodnotu false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Expression1*>, <*expression2*>,... | Ano | Logická hodnota | Výrazy ke kontrole | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Vrátí hodnotu PRAVDA, pokud jsou splněny všechny výrazy. Vrátí hodnotu false, pokud se alespoň jeden výraz je nepravdivý. | 
|||| 

*Příklad 1*

Tyto příklady zkontrolujte, zda jsou splněny všechny zadané logické hodnoty:

```
and(true, true)
and(false, true)
and(false, false)
```

A vrátí tyto výsledky:

* První příklad: oba výrazy hodnotu true, proto vrátí `true`. 
* Druhý příklad: jeden výraz má hodnotu false, takže vrací `false`.
* Třetí příklad: oba výrazy jsou false, takže vrací `false`.

*Příklad 2*

Tyto příklady zkontrolujte, zda jsou splněny všechny zadaných výrazů:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

A vrátí tyto výsledky:

* První příklad: oba výrazy hodnotu true, proto vrátí `true`. 
* Druhý příklad: jeden výraz má hodnotu false, takže vrací `false`.
* Třetí příklad: oba výrazy jsou false, takže vrací `false`.

<a name="array"></a>

## <a name="array"></a>pole

Vrátí pole z jedné zadané vstup. Více vstupů, naleznete v tématu [createArray()](#createArray). 

```
array('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec pro vytvoření pole | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*hodnotu*>] | Pole | Pole obsahující jeden zadaný vstup | 
|||| 

*Příklad*

Tento příklad vytvoří pole z řetězce "hello":

```
array('hello')
```

A vrátí výsledek: `["hello"]`

<a name="base64"></a>

## <a name="base64"></a>ve formátu Base64

Vrácení verze řetězce s kódováním base64.

```
base64('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Vstupní řetězec | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*řetězec ve formátu Base64*> | Řetězec | Verze s kódováním base64 pro vstupní řetězec | 
|||| 

*Příklad*

Tento příklad převede na řetězec s kódováním base64 řetězec "hello":

```
base64('hello')
```

A vrátí výsledek: `"aGVsbG8="`

<a name="base64ToBinary"></a>

## <a name="base64tobinary"></a>base64ToBinary

Vrátí binární verze řetězce s kódováním base64.

```
base64ToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec s kódováním base64 pro převod | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární soubor pro base64 string*> | Řetězec | Binární verze řetězce s kódováním base64 | 
|||| 

*Příklad*

Tento příklad převede "aGVsbG8 =" řetězce zakódovaného řetězcem base64 binární řetězec:

```
base64ToBinary('aGVsbG8=')
```

A vrátí výsledek: 

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

## <a name="base64tostring"></a>base64ToString

Vrácení verze řetězce pro řetězec s kódováním base64, efektivně dekódování řetězec ve formátu base64. Tuto funkci použít spíše než [decodeBase64()](#decodeBase64). I když obě funkce fungovat stejným způsobem, `base64ToString()` je upřednostňována.

```
base64ToString('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec s kódováním base64 pro dekódování | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*dekódovat řetězec ve formátu base64*> | Řetězec | Řetězec verze řetězce s kódováním base64 | 
|||| 

*Příklad*

Tento příklad převede "aGVsbG8 =" řetězce zakódovaného řetězcem base64 jenom na řetězec:

```
base64ToString('aGVsbG8=')
```

A vrátí výsledek: `"hello"`

<a name="binary"></a>

## <a name="binary"></a>Binární 

Vrátí binární verze řetězce.

```
binary('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární soubor pro vstupní hodnota*> | Řetězec | Binární verze pro zadaný řetězec | 
|||| 

*Příklad*

Tento příklad převede řetězec "hello" na binární řetězec:

```
binary('hello')
```

A vrátí výsledek: 

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

## <a name="body"></a>hlavní část

Vrácení akce `body` výstupu za běhu. Zkrácený tvar vlastností `actions('<actionName>').outputs.body`. Zobrazit [actionBody()](#actionBody) a [actions()](#actions).

```
body('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Název této akce `body` výstup, který chcete, aby | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výstup textu akce*> | Řetězec | `body` Výstup z zadanou akci | 
|||| 

*Příklad*

Tento příklad načte `body` výstup z `Get user` akce na Twitteru: 

```
body('Get_user')
```

A vrátí výsledek: 

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

## <a name="bool"></a>BOOL

Vrátí verzi logickou hodnotu.

```
bool(<value>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Všechny | Hodnota k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Logická verze pro zadanou hodnotu | 
|||| 

*Příklad*

Tyto příklady převodu zadané hodnoty na logické hodnoty: 

```
bool(1)
bool(0)
```

A vrátí tyto výsledky: 

* První příklad: `true` 
* Druhý příklad: `false`

<a name="coalesce"></a>

## <a name="coalesce"></a>sloučení

Vrátí první hodnotu než null z jednoho nebo více parametrů. Prázdné řetězce, pole prázdné a prázdné objekty nejsou null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*object_1*>, <*object_2*>,... | Ano | Případně můžete kombinovat typy | Jednu nebo více položek, které chcete zkontrolovat hodnoty null | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*první bez null – položky*> | Všechny | První položka nebo hodnotu, která není null. Pokud všechny parametry mají hodnotu null, tato funkce vrátí hodnotu null. | 
|||| 

*Příklad*

Tyto příklady vrátí první hodnota jiná než null ze zadané hodnoty nebo hodnota null, pokud jsou všechny hodnoty null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

A vrátí tyto výsledky: 

* První příklad: `true` 
* Druhý příklad: `"hello"`
* Třetí příklad: `null`

<a name="concat"></a>

## <a name="concat"></a>concat

Kombinací dvou nebo více řetězců a vrátit kombinované řetězec. 

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text1*>, <*text2*>,... | Ano | Řetězec | Alespoň dva řetězce zkombinovat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*text1text2...*> | Řetězec | Řetězec vytvořený z kombinované vstupního řetězce | 
|||| 

*Příklad*

V tomto příkladu jsou k dispozici řetězce "Hello" a "World":

```
concat('Hello', 'World')
```

A vrátí výsledek: `"HelloWorld"`

<a name="contains"></a>

## <a name="contains"></a>obsahuje

Zkontrolujte, zda kolekce obsahuje konkrétní položku. Vrátí hodnotu PRAVDA, pokud byla položka nalezena, nebo vrátí hodnotu false, když nebyl nalezen. Tato funkce rozlišuje velká a malá písmena.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Konkrétně tato funkce funguje u těchto typů kolekce: 

* A *řetězec* k vyhledání *dílčí řetězec*
* *Pole* k vyhledání *hodnota*
* A *slovníku* k vyhledání *klíč*

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekce*> | Ano | Řetězec, pole nebo slovníku | Kolekce, kterou chcete zkontrolovat | 
| <*Hodnota*> | Ano | Řetězec, pole nebo slovníku, respektive | Položka k vyhledání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Vrátí hodnotu PRAVDA, pokud byla položka nalezena. Vrátí hodnotu false po nebyl nalezen. |
|||| 

*Příklad 1*

Tento příklad kontroluje řetězec "hello world" pro podřetězec "world" a vrací hodnotu true:

```
contains('hello world', 'world')
```

*Příklad 2*

Tento příklad kontroluje řetězec "hello world" pro podřetězec "universe" a vrátí hodnotu false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

## <a name="convertfromutc"></a>convertFromUtc

Převeďte časové razítko od koordinovaný světový čas (UTC) na časové pásmo cíle.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*destinationTimeZone*> | Ano | Řetězec | Název pro časové pásmo cíle. Další informace najdete v tématu [ID časového pásma](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Převést časové razítko*> | Řetězec | Časové razítko převést na časové pásmo cíle | 
|||| 

*Příklad 1*

Tento příklad převede časové razítko pro zadané časové pásmo: 

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

A vrátí výsledek: `"2018-01-01T00:00:00.0000000"`

*Příklad 2*

Tento příklad převede na zadané časové pásmo a formát časového razítka:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

A vrátí výsledek: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

## <a name="converttimezone"></a>convertTimeZone

Převeďte časové razítko ze zdrojového časového pásma na časové pásmo cíle.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*sourceTimeZone*> | Ano | Řetězec | Název zdrojového časového pásma. Další informace najdete v tématu [ID časového pásma](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*destinationTimeZone*> | Ano | Řetězec | Název pro časové pásmo cíle. Další informace najdete v tématu [ID časového pásma](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Převést časové razítko*> | Řetězec | Časové razítko převést na časové pásmo cíle | 
|||| 

*Příklad 1*

Tento příklad převede zdrojového časového pásma na cílové časové pásmo: 

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

A vrátí výsledek: `"2018-01-01T00:00:00.0000000"`

*Příklad 2*

Tento příklad převede časové pásmo pro zadané časové pásmo a formát:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

A vrátí výsledek: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

## <a name="converttoutc"></a>convertToUtc

Převeďte časové razítko ze zdrojového časového pásma na koordinovaný univerzální čas (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*sourceTimeZone*> | Ano | Řetězec | Název zdrojového časového pásma. Další informace najdete v tématu [ID časového pásma](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Převést časové razítko*> | Řetězec | Časové razítko převede na UTC | 
|||| 

*Příklad 1*

Tento příklad převede na UTC časové razítko: 

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

A vrátí výsledek: `"2018-01-01T08:00:00.0000000Z"`

*Příklad 2*

Tento příklad převede na UTC časové razítko:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

A vrátí výsledek: `"Monday, January 1, 2018"`

<a name="createArray"></a>

## <a name="createarray"></a>createArray

Vrátíte pole z více vstupů. Jeden vstupní pole, naleznete v tématu [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Objekt1*>, <*objekt2*>,... | Ano | Žádné, ne však obojí | Minimálně dvě položky k vytvoření pole | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*Objekt1*>, <*objekt2*>;...] | Pole | Pole vytvořené ze všech vstupních položek | 
|||| 

*Příklad*

Tento příklad vytvoří pole z těchto vstupy:

```
createArray('h', 'e', 'l', 'l', 'o')
```

A vrátí výsledek: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

## <a name="datauri"></a>Parametr

Vrátí identifikátor URI dat (URI) pro řetězec. 

```
dataUri('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*identifikátor uri dat*> | Řetězec | Identifikátor URI dat pro vstupní řetězec | 
|||| 

*Příklad*

Tento příklad vytvoří data identifikátor URI pro řetězec "hello":

```
dataUri('hello') 
```

A vrátí výsledek: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

## <a name="datauritobinary"></a>dataUriToBinary

Vrátí binární verzi pro identifikátor URI dat (URI). Tuto funkci použít spíše než [decodeDataUri()](#decodeDataUri). I když obě funkce fungovat stejným způsobem, `decodeDataUri()` je upřednostňována.

```
dataUriToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Identifikátor URI k převedení dat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární soubor pro data identifikátoru uri*> | Řetězec | Binární verze pro identifikátor URI dat. | 
|||| 

*Příklad*

Tento příklad vytvoří binární verze pro tento identifikátor URI dat:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí výsledek: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

## <a name="datauritostring"></a>dataUriToString

Vrácení verze řetězce pro identifikátor URI dat (URI).

```
dataUriToString('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Identifikátor URI k převedení dat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*řetězec pro data identifikátoru uri*> | Řetězec | Řetězec verze pro identifikátor URI dat. | 
|||| 

*Příklad*

Tento příklad vytvoří řetězec pro tento identifikátor URI dat:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí výsledek: `"hello"`

<a name="dayOfMonth"></a>

## <a name="dayofmonth"></a>dayOfMonth

Vrátí den v měsíci z časové razítko. 

```
dayOfMonth('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*den měsíce*> | Integer | Den v měsíci ze zadaného časového razítka | 
|||| 

*Příklad*

V tomto příkladu vrátí číslo za den v měsíci z tohoto časového razítka:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

A vrátí výsledek: `15`

<a name="dayOfWeek"></a>

## <a name="dayofweek"></a>dayOfWeek

Vrátí den v týdnu od časové razítko.  

```
dayOfWeek('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*den v týdnu*> | Integer | Den v týdnu ze zadaného časového razítka, kde 0 je neděle, pondělí je 1 a tak dále | 
|||| 

*Příklad*

V tomto příkladu vrátí číslo za den v týdnu z tohoto časového razítka:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

A vrátí výsledek: `3`

<a name="dayOfYear"></a>

## <a name="dayofyear"></a>dayOfYear

Vrátí den v roce od časové razítko. 

```
dayOfYear('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*den roku*> | Integer | Den v roce ze zadaného časového razítka | 
|||| 

*Příklad*

V tomto příkladu vrátí den v roce z tohoto časového razítka:

```
dayOfYear('2018-03-15T13:27:36Z')
```

A vrátí výsledek: `74`

<a name="decodeBase64"></a>

## <a name="decodebase64"></a>decodeBase64

Vrácení verze řetězce pro řetězec s kódováním base64, efektivně dekódování řetězec ve formátu base64. Zvažte použití [base64ToString()](#base64ToString) spíše než `decodeBase64()`. I když obě funkce fungovat stejným způsobem, `base64ToString()` je upřednostňována.

```
decodeBase64('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec s kódováním base64 pro dekódování | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*dekódovat řetězec ve formátu base64*> | Řetězec | Řetězec verze řetězce s kódováním base64 | 
|||| 

*Příklad*

Tento příklad vytvoří řetězec s kódováním base64 řetězce:

```
decodeBase64('aGVsbG8=')
```

A vrátí výsledek: `"hello"`

<a name="decodeDataUri"></a>

## <a name="decodedatauri"></a>decodeDataUri

Vrátí binární verzi pro identifikátor URI dat (URI). Zvažte použití [dataUriToBinary()](#dataUriToBinary), spíše než `decodeDataUri()`. I když obě funkce fungovat stejným způsobem, `dataUriToBinary()` je upřednostňována.

```
decodeDataUri('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Identifikátor URI řetězec pro dekódování dat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární soubor pro data identifikátoru uri*> | Řetězec | Binární verze pro řetězce identifikátoru URI dat. | 
|||| 

*Příklad*

V tomto příkladu vrací binární verze pro tento identifikátor URI dat:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí výsledek: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

## <a name="decodeuricomponent"></a>decodeuricomponent –

Vrátí, nahradí řídicí znaky s verzemi dekódovaný řetězec. 

```
decodeUriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec s řídicími znaky k dekódování | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*dekódovat. identifikátor uri*> | Řetězec | Aktualizované řetězec s dekódovaný řídicí znaky | 
|||| 

*Příklad*

V tomto příkladu řídicí znaky v tomto řetězci nahradí dekódovaný verze:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

A vrátí výsledek: `"https://contoso.com"`

<a name="div"></a>

## <a name="div"></a>div

Vrátí výsledek celočíselné dělení dvou čísel. Chcete-li získat zbytek výsledek, naleznete v tématu [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*podíl*> | Ano | Integer nebo Float | Číslo, které má dělit *dělitel* | 
| <*dělitel*> | Ano | Integer nebo Float | Číslo, které rozdělí *Delenec*, ale nemůže být 0 | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výsledek podílu*> | Integer | Výsledek celočíselné dělení první číslo v druhé číslo | 
|||| 

*Příklad*

Oba příklady vydělení prvního čísla druhé číslo:

```
div(10, 5)
div(11, 5)
```

A vraťte tento výsledek: `2`

<a name="encodeUriComponent"></a>

## <a name="encodeuricomponent"></a>encodeuricomponent –

Vrácení verze URI kódovaný identifikátor (URI) pro řetězec tak, že nahradíte adresu URL problematické znaky s řídicími znaky. Zvažte použití [uriComponent()](#uriComponent), spíše než `encodeUriComponent()`. I když obě funkce fungovat stejným způsobem, `uriComponent()` je upřednostňována.

```
encodeUriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec převést na formát kódování identifikátoru URI | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*kódovaný identifikátor uri*> | Řetězec | Řetězec kódovaný identifikátor URI s řídicími znaky | 
|||| 

*Příklad*

Tento příklad vytvoří verzi kódovaný identifikátor URI pro tento řetězec:

```
encodeUriComponent('https://contoso.com')
```

A vrátí výsledek: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

## <a name="empty"></a>prázdný

Zkontrolujte, zda kolekce je prázdná. Vrátí hodnotu PRAVDA, pokud kolekce je prázdná nebo vrátí hodnotu NEPRAVDA, pokud není prázdný.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekce*> | Ano | Řetězec, pole nebo objekt | Kolekce, kterou chcete zkontrolovat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Vrátí hodnotu PRAVDA, pokud kolekce je prázdná. Vrátí hodnotu NEPRAVDA, pokud není prázdný. | 
|||| 

*Příklad* 

Tyto příklady zkontrolujte, jestli zadaná kolekce jsou prázdné:

```
empty('')
empty('abc')
```

A vrátí tyto výsledky: 

* První příklad: předá prázdný řetězec, funkce vrátí `true`. 
* Druhý příklad: předává řetězec "abc" tak, aby funkce `false`. 

<a name="endswith"></a>

## <a name="endswith"></a>endsWith

Zkontrolujte, jestli řetězec končí konkrétní dílčí řetězec. Vrátí hodnotu PRAVDA, pokud se najde dílčí řetězec, nebo vrátí hodnotu false, když nebyl nalezen. Tato funkce není malá a velká písmena.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který má zkontrolovat | 
| <*Prohledávanýtext*> | Ano | Řetězec | Poslední dílčí řetězec k vyhledání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA  | Logická hodnota | Vrátí hodnotu PRAVDA, pokud se najde poslední podřetězec. Vrátí hodnotu false po nebyl nalezen. | 
|||| 

*Příklad 1* 

Tento příklad kontroluje, zda řetězec "hello world" končí řetězcem "world":

```
endsWith('hello world', 'world')
```

A vrátí výsledek: `true`

*Příklad 2*

Tento příklad kontroluje, zda řetězec "hello world" končí řetězcem "universe":

```
endsWith('hello world', 'universe')
```

A vrátí výsledek: `false`

<a name="equals"></a>

## <a name="equals"></a>rovná se

Zkontrolujte, zda jsou ekvivalentní hodnoty, výrazy nebo objekty. Vrátí hodnotu PRAVDA, pokud obě jsou ekvivalentní, nebo vrátí hodnotu false, když nejsou ekvivalentní.

```
equals('<object1>', '<object2>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Objekt1*>, <*objekt2*> | Ano | Různé | Hodnoty, výrazy nebo objekty k porovnání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Vrátí hodnotu PRAVDA, pokud obě jsou ekvivalentní. Vrátí hodnotu NEPRAVDA, pokud není ekvivalentní. | 
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda zadané vstupy jsou ekvivalentní. 

```
equals(true, 1)
equals('abc', 'abcd')
```

A vrátí tyto výsledky: 

* První příklad: jsou ekvivalentní, obě hodnoty, funkce vrátí `true`.
* Druhý příkladu: obě hodnoty nejsou ekvivalentní, tak, aby funkce `false`.

<a name="first"></a>

## <a name="first"></a>první

Vrátí první položku z řetězce nebo pole.

```
first('<collection>')
first([<collection>])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekce*> | Ano | Řetězec nebo pole | Kolekce kde najít první položka |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*první položku kolekce*> | Všechny | První položka v kolekci | 
|||| 

*Příklad*

Tyto příklady najdete v těchto kolekcích první položky:

```
first('hello')
first([0, 1, 2])
```

A vracet tyto výsledky: 

* První příklad: `"h"`
* Druhý příkladu: `0`

<a name="float"></a>

## <a name="float"></a>float

Převeďte řetězec verze pro číslo s plovoucí desetinnou čárkou na skutečný číslo s plovoucí desetinnou čárkou. Tato funkce slouží pouze v případě, že předávání vlastní parametry do aplikace, jako je například aplikace logiky.

```
float('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec, který má platné číslo s plovoucí desetinnou čárkou k převedení |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota float*> | plovoucí desetinnou čárkou | Číslo s plovoucí desetinnou čárkou pro zadaný řetězec | 
|||| 

*Příklad*

Tento příklad vytvoří řetězec verze pro toto číslo s plovoucí desetinnou čárkou:

```
float('10.333')
```

A vrátí výsledek: `10.333`

<a name="formatDateTime"></a>

## <a name="formatdatetime"></a>formatDateTime

Vrátí časové razítko v zadaném formátu.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*přeformátovali časové razítko*> | Řetězec | Aktualizované časové razítko v zadaném formátu. | 
|||| 

*Příklad*

Tento příklad převede na zadaný formát časového razítka:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

A vrátí výsledek: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

## <a name="formdatamultivalues"></a>formDataMultiValues

Vrátí pole hodnot, které odpovídají názvu klíče v akce *data formuláře* nebo *form-encoded.* výstup. 

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Akce, jejichž výstup má požadovanou hodnotu klíče | 
| <*Klíč*> | Ano | Řetězec | Název klíče, jejíž hodnotu chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*pole s klíčem hodnoty*>] | Pole | Pole obsahující všechny hodnoty, které odpovídají zadanému klíči | 
|||| 

*Příklad* 

Tento příklad vytvoří pole z hodnoty "Předmět" klíč v zadané akce form-data nebo form-encoded. výstup:  

```
formDataMultiValues('Send_an_email', 'Subject')
```

A vrátí text předmětu v poli, například: `["Hello world"]`

<a name="formDataValue"></a>

## <a name="formdatavalue"></a>formDataValue

Vrátí jednu hodnotu, která odpovídá názvu klíče v akce *data formuláře* nebo *form-encoded.* výstup. Pokud funkce najde více než jedna shoda, funkce vyvolá chybu.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Akce, jejichž výstup má požadovanou hodnotu klíče | 
| <*Klíč*> | Ano | Řetězec | Název klíče, jejíž hodnotu chcete |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*klíč hodnota*> | Řetězec | Hodnota v zadaný klíč  | 
|||| 

*Příklad* 

Tento příklad vytvoří řetězec z hodnoty "Předmět" klíč v zadané akce form-data nebo form-encoded. výstup:  

```
formDataValue('Send_an_email', 'Subject')
```

A vrací text předmětu jako řetězec, například: `"Hello world"`

<a name="getFutureTime"></a>

## <a name="getfuturetime"></a>getFutureTime

Vrátí aktuální časové razítko plus zadané časové jednotky.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*interval*> | Ano | Integer | Počet jednotek určený čas se má odečíst | 
| <*timeUnit*> | Ano | Řetězec | Jednotka času pro použití s *interval*: "Sekundu", "Minute", "Hour", "Day", "Týden", "Měsíc", "Rok" | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Aktuální časové razítko a za zadaný počet časových jednotek | 
|||| 

*Příklad 1*

Předpokládejme, že je aktuální časové razítko "2018-03-01T00:00:00.0000000Z". V tomto příkladu přidá pět dní do tohoto časového razítka:

```
getFutureTime(5, 'Day')
```

A vrátí výsledek: `"2018-03-06T00:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že je aktuální časové razítko "2018-03-01T00:00:00.0000000Z". Tento příklad přidá pět dní a převádí výsledek do formátu "D":

```
getFutureTime(5, 'Day', 'D')
```

A vrátí výsledek: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

## <a name="getpasttime"></a>getPastTime

Vrátí aktuální časové razítko minus zadané časové jednotky.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*interval*> | Ano | Integer | Počet jednotek určený čas se má odečíst | 
| <*timeUnit*> | Ano | Řetězec | Jednotka času pro použití s *interval*: "Sekundu", "Minute", "Hour", "Day", "Týden", "Měsíc", "Rok" | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Aktuální časové razítko minus zadaný počet časových jednotek | 
|||| 

*Příklad 1*

Předpokládejme, že je aktuální časové razítko "2018-02-01T00:00:00.0000000Z". V tomto příkladu odečte pět dní od tohoto časového razítka:

```
getPastTime(5, 'Day')
```

A vrátí výsledek: `"2018-01-27T00:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že je aktuální časové razítko "2018-02-01T00:00:00.0000000Z". V tomto příkladu odečte pět dní a převádí výsledek do formátu "D":

```
getPastTime(5, 'Day', 'D')
```

A vrátí výsledek: `"Saturday, January 27, 2018"`

<a name="greater"></a>

## <a name="greater"></a>větší

Zkontrolujte, zda je první hodnota větší než druhá hodnota. Vrátí hodnotu PRAVDA, pokud je první hodnota další, nebo vrátí hodnotu false, při nižší.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Celé číslo, Float nebo řetězec | První hodnota ke kontrole, jestli je větší než druhá hodnota | 
| <*Metoda compareTo*> | Ano | Celé číslo, Float nebo řetězec, respektive | Hodnota porovnání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Vrátí hodnotu PRAVDA, pokud je první hodnota větší než druhá hodnota. Vrátí hodnotu false, pokud první hodnota je větší nebo menší než druhá hodnota. | 
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda je první hodnota větší než druhá hodnota:

```
greater(10, 5)
greater('apple', 'banana')
```

A vracet tyto výsledky: 

* První příklad: `true`
* Druhý příklad: `false`

<a name="greaterOrEquals"></a>

## <a name="greaterorequals"></a>greaterOrEquals

Zkontrolujte, zda je první hodnota větší než nebo rovna hodnotě druhá hodnota.
Vrátí hodnotu PRAVDA, pokud je první hodnota větší nebo rovna nebo vrácení false v případě, první hodnota je menší.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Celé číslo, Float nebo řetězec | První hodnota ke kontrole, jestli větší než nebo rovna hodnotě druhá hodnota | 
| <*Metoda compareTo*> | Ano | Celé číslo, Float nebo řetězec, respektive | Hodnota porovnání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Vrátí hodnotu PRAVDA, pokud první hodnota je větší než nebo rovna hodnotě druhá hodnota. Vrátí hodnotu NEPRAVDA, pokud je první hodnota menší než druhá hodnota. | 
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda je první hodnota větší nebo rovna než druhá hodnota:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

A vracet tyto výsledky: 

* První příklad: `true`
* Druhý příklad: `false`

<a name="guid"></a>

## <a name="guid"></a>identifikátor GUID

Generování globálně jedinečný identifikátor (GUID) jako řetězec, například "c2ecc88d-88c8-4096-912c-d6f2e2b138ce": 

```
guid()
```

Také můžete zadat jiný formát pro identifikátor GUID než výchozí formát "D", což je 32 číslic oddělených pomlčkami.

```
guid('<format>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Formát*> | Ne | Řetězec | Jediný [specifikátor formátu](https://msdn.microsoft.com/library/97af8hh4) pro vrácený identifikátor GUID. Ve výchozím formátu je "D", ale můžete použít "N", "D", "B", "P" nebo "X". | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota identifikátoru GUID*> | Řetězec | Náhodně generované GUID | 
|||| 

*Příklad* 

Tento příklad vygeneruje stejný identifikátor GUID, ale jako 32 číslic oddělených pomlčkami a uzavřených v závorkách: 

```
guid('P')
```

A vrátí výsledek: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

## <a name="if"></a>If

Zkontrolujte, zda je výraz hodnotu true nebo false. Na základě výsledku, vrátí zadanou hodnotu.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Výraz*> | Ano | Logická hodnota | Výraz, který má zkontrolovat | 
| <*valueIfTrue*> | Ano | Všechny | Hodnota, která má vrácené v případě, kdy je výraz pravdivý | 
| <*valueIfFalse*> | Ano | Všechny | Hodnota, která má vrátit, pokud má výraz hodnotu false | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Zadaná – návratové hodnoty*> | Všechny | Zadaná hodnota, která vrací na základě toho, zda výraz hodnotu true nebo false | 
|||| 

*Příklad* 

V tomto příkladu vrátí `"yes"` vzhledem k tomu, že zadaný výraz vrátí hodnotu true. V opačném případě vrátí v příkladu `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

## <a name="indexof"></a>indexOf

Vrátí počáteční pozici nebo hodnotu indexu pro dílčí řetězec. Tato funkce není velká a malá písmena a indexy začínají číslem 0. 

```
indexOf('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který obsahuje dílčí řetězec k vyhledání | 
| <*Prohledávanýtext*> | Ano | Řetězec | Dílčí řetězec k vyhledání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota indexu*>| Integer | Počáteční pozice nebo index hodnotu pro zadaný dílčí řetězec. <p>Pokud není nalezen řetězec, vrátí číslo -1. </br>Pokud je řetězec prázdný, vrátí číslo 0. | 
|||| 

*Příklad* 

Tento příklad vyhledá počáteční hodnoty indexu pro podřetězec "world" v řetězci "hello world":

```
indexOf('hello world', 'world')
```

A vrátí výsledek: `6`

<a name="int"></a>

## <a name="int"></a>int

Vrátí celé číslo verze řetězce.

```
int('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*celé číslo výsledku*> | Integer | Celé číslo verze pro zadaný řetězec | 
|||| 

*Příklad* 

V tomto příkladu se vytvoří celé číslo verze pro řetězec "10":

```
int('10')
```

A vrátí výsledek: `10`

<a name="item"></a>

## <a name="item"></a>Položka

Když se použije v opakující se akci nad polem, vrátí aktuální položku v poli během aktuální iteraci akce. Můžete také získat hodnoty z vlastnosti této položky. 

```
item()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktuální položku pole*> | Všechny | Aktuální položku v poli pro aktuální iteraci akce | 
|||| 

*Příklad* 

Tento příklad načte `body` element z aktuální zprávu pro akci "Send_an_email" uvnitř smyčky for-each aktuální iterace:

```
item().body
```

<a name="items"></a>

## <a name="items"></a>položek

Vrátí aktuální položky v každém cyklu v smyčky for-each. Pomocí této funkce uvnitř smyčky for-each.

```
items('<loopName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Ano | Řetězec | Název smyčky for-each | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Položka*> | Všechny | Položku z aktuálního cyklu v zadané smyčky for-each | 
|||| 

*Příklad* 

Tento příklad načte aktuální položky ze zadané smyčky for-each:

```
items('myForEachLoopName')
```

<a name="json"></a>

## <a name="json"></a>JSON

Vrátí hodnotu typu JavaScript Object Notation (JSON) nebo objekt pro řetězec nebo kód XML.

```
json('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec nebo kód XML | Řetězec nebo kód XML, který chcete převést | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Výsledek JSON*> | Nativní typ formátu JSON nebo objekt | Nativní typ hodnoty JSON nebo objekt pro zadaný řetězec nebo kód XML. Pokud má řetězec hodnotu null, funkce vrátí prázdný objekt. | 
|||| 

*Příklad 1* 

Tento příklad převede tento řetězec na hodnotu JSON:

```
json('[1, 2, 3]')
```

A vrátí výsledek: `[1, 2, 3]`

*Příklad 2*

Tento příklad převede tento řetězec do formátu JSON: 

```
json('{"fullName": "Sophia Owen"}')
```

A vrátí výsledek:

```
{
  "fullName": "Sophia Owen"
}
```

*Příklad 3*

Tento příklad převede tento XML do formátu JSON: 

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

A vrátí výsledek:

```json
{ 
   "?xml": { "@version": "1.0" }, 
   "root": { 
      "person": [ { 
         "@id": "1", 
         "name": "Sophia Owen", 
         "occupation": "Engineer" 
       } ] 
   } 
}
```

<a name="intersection"></a>

## <a name="intersection"></a>Průnik

Vrátí kolekci, která má *pouze* společné položky mezi zadaným kolekcím. Zobrazit ve výsledku, musí být uvedena položka ve všech kolekcích předaném do této funkce. Pokud jeden nebo více položek se stejným názvem, zobrazí se ve výsledku poslední položka s tímto názvem.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*kolekce 2*>,... | Ano | Pole nebo objekt, ale ne obojí | Kolekce, ze kterého má *pouze* společné položky | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*společné položky*> | Pole nebo objekt, v uvedeném pořadí | Kolekce, která obsahuje pouze běžné položek v zadaných kolekcí | 
|||| 

*Příklad* 

Tento příklad vyhledá společné položky mezi tato pole:  

```
intersection([1, 2, 3], [101, 2, 1, 10], [6, 8, 1, 2])
```

A vrátí pole obsahující *pouze* tyto položky: `[1, 2]`

<a name="join"></a>

## <a name="join"></a>join

Oddělené řetězec, který má všechny položky z pole a každý znak návratu *oddělovač*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekce*> | Ano | Pole | Pole, které obsahuje položky, které chcete připojit |  
| <*Oddělovač*> | Ano | Řetězec | Oddělovač, který se zobrazí mezi každý znak ve výsledném řetězci | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*char1*><*oddělovač*><*char2*><*oddělovač*>... | Řetězec | Výsledný řetězec vytvořený ze všech položek v určeném poli |
|||| 

*Příklad* 

Tento příklad vytvoří řetězec ze všech položek v tomto poli s zadaný znak jako oddělovač:

```
join([a, b, c], '.')
```

A vrátí výsledek: `"a.b.c"`

<a name="last"></a>

## <a name="last"></a>poslední

Vrátí poslední položku z kolekce.

```
last('<collection>')
last([<collection>])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekce*> | Ano | Řetězec nebo pole | Kolekce kde najít poslední položky | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*poslední položku kolekce*> | Řetězec nebo pole, v uvedeném pořadí | Poslední položka v kolekci | 
|||| 

*Příklad* 

Tyto příklady najdete v těchto kolekcích poslední položky:

```
last('abcd')
last([0, 1, 2, 3])
```

A vrátí tyto výsledky: 

* První příklad: `"d"`
* Druhý příklad: `3`

<a name="lastindexof"></a>

## <a name="lastindexof"></a>lastIndexOf

Vrátí hodnotu koncové pozice nebo indexu pro dílčí řetězec. Tato funkce není velká a malá písmena a indexy začínají číslem 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který obsahuje dílčí řetězec k vyhledání | 
| <*Prohledávanýtext*> | Ano | Řetězec | Dílčí řetězec k vyhledání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota indexu ukončení*> | Integer | Koncová pozice nebo index Hodnota zadaným podřetězcem. <p>Pokud není nalezen řetězec, vrátí číslo -1. </br>Pokud je řetězec prázdný, vrátí číslo 0. | 
|||| 

*Příklad* 

Tento příklad vyhledá poslední hodnotu indexu pro podřetězec "world" v řetězci "hello world":

```
lastIndexOf('hello world', 'world')
```

A vrátí výsledek: `10`

<a name="length"></a>

## <a name="length"></a>Délka

Vrátí počet položek v kolekci.

```
length('<collection>')
length([<collection>])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekce*> | Ano | Řetězec nebo pole | Kolekce s položkami mají spočítat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Délka – počet*> | Integer | Počet položek v kolekci | 
|||| 

*Příklad*

Tyto příklady počet položek v těchto kolekcí: 

```
length('abcd')
length([0, 1, 2, 3])
```

A vraťte tento výsledek: `4`

<a name="less"></a>

## <a name="less"></a>méně

Zkontrolujte, zda je první hodnota menší než druhá hodnota.
Vrátí hodnotu PRAVDA, pokud je první hodnota menší nebo vrátit hodnotu false v případě více první hodnota.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Celé číslo, Float nebo řetězec | První hodnota ke kontrole, jestli menší než druhá hodnota | 
| <*Metoda compareTo*> | Ano | Celé číslo, Float nebo řetězec, respektive | Porovnání položky | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Vrátí hodnotu PRAVDA, pokud je první hodnota menší než druhá hodnota. Vrátí hodnotu false, pokud první hodnota je roven nebo větší než druhá hodnota. | 
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda je první hodnota menší než druhá hodnota.

```
less(5, 10)
less('banana', 'apple')
```

A vracet tyto výsledky: 

* První příklad: `true`
* Druhý příklad: `false`

<a name="lessOrEquals"></a>

## <a name="lessorequals"></a>lessOrEquals

Zkontrolujte, zda je první hodnota menší než druhá hodnota.
Vrátí hodnotu PRAVDA, pokud je první hodnota menší než nebo rovno nebo vrátit hodnotu false v případě více první hodnota.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Celé číslo, Float nebo řetězec | První hodnota ke kontrole, jestli menší než nebo rovno druhá hodnota | 
| <*Metoda compareTo*> | Ano | Celé číslo, Float nebo řetězec, respektive | Porovnání položky | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA  | Logická hodnota | Vrátí hodnotu PRAVDA, pokud je první hodnota menší než druhá hodnota. Vrátí hodnotu false, pokud první hodnota je větší než druhá hodnota. |  
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda je první hodnota menší než nebo rovné než druhá hodnota.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

A vracet tyto výsledky: 

* První příklad: `true`
* Druhý příklad: `false`

<a name="listCallbackUrl"></a>

## <a name="listcallbackurl"></a>listCallbackUrl

Vrátí adresu "URL zpětného volání", která volá triggeru nebo akce. Tato funkce funguje pouze s aktivačními událostmi a akce pro **HttpWebhook** a **ApiConnectionWebhook** typy konektoru, ale ne **ruční**,  **Opakování**, **HTTP**, a **APIConnection** typy. 

```
listCallbackUrl()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Adresa URL zpětného volání*> | Řetězec | Adresa URL zpětného volání triggeru nebo akce |  
|||| 

*Příklad*

Tento příklad ukazuje ukázkovou adresu URL zpětného volání, že tato funkce může vrátit:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

## <a name="max"></a>max

Vrátí nejvyšší hodnotu ze seznamu nebo pole s čísly, který je také zahrnuto na obou koncích. 

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Číslo1*>, <*číslo2*>,... | Ano | Integer nebo Float | Sadu čísel, ze kterého chcete nejvyšší hodnota | 
| [<*Číslo1*>, <*číslo2*>;...] | Ano | Array – Integer nebo Float | Pole čísla, ze kterého má nejvyšší hodnota | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Maximální hodnota*> | Integer nebo Float | Nejvyšší hodnota v zadaném poli nebo sadu čísel | 
|||| 

*Příklad* 

Tyto příklady získat nejvyšší hodnota ze sady čísel a pole:

```
max(1, 2, 3)
max([1, 2, 3])
```

A vraťte tento výsledek: `3`

<a name="min"></a>

## <a name="min"></a>min

Vrátí nejnižší hodnotu ze sady čísel nebo pole.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Číslo1*>, <*číslo2*>,... | Ano | Integer nebo Float | Sada čísla, ze kterého má nejnižší hodnota | 
| [<*Číslo1*>, <*číslo2*>;...] | Ano | Array – Integer nebo Float | Pole čísla, ze kterého má nejnižší hodnota | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*minimální hodnota*> | Integer nebo Float | Nejnižší hodnotu v zadané sadě čísel nebo určeného pole | 
|||| 

*Příklad* 

Tyto příklady získat nejnižší hodnotu v sadu čísel a pole:

```
min(1, 2, 3)
min([1, 2, 3])
```

A vraťte tento výsledek: `1`

<a name="mod"></a>

## <a name="mod"></a>MOD

Vrátí zbytek dělení dvou čísel. Pokud chcete získat výsledek celého čísla, naleznete v tématu [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*podíl*> | Ano | Integer nebo Float | Číslo, které má dělit *dělitel* | 
| <*dělitel*> | Ano | Integer nebo Float | Číslo, které rozdělí *Delenec*, ale nemůže být 0. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*modulo výsledek*> | Integer nebo Float | Zbytku z dělení první číslo v druhé číslo | 
|||| 

*Příklad* 

V tomto příkladu se rozdělí první číslo v druhé číslo:

```
mod(3, 2)
```

A vraťte tento výsledek: `1`

<a name="mul"></a>

## <a name="mul"></a>mul

Vrátí produkt od součin dvou čísel.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*multiplicand1*> | Ano | Integer nebo Float | Číslo, které má vynásobit *multiplicand2* | 
| <*multiplicand2*> | Ano | Integer nebo Float | Číslo, které násobky *multiplicand1* | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výsledek produktu*> | Integer nebo Float | Produkt vynásobení prvního čísla druhé číslo | 
|||| 

*Příklad* 

Tyto příklady více o první číslo v druhé číslo:

```
mul(1, 2)
mul(1.5, 2)
```

A vracet tyto výsledky:

* První příklad: `2`
* Druhý příklad `3`

<a name="multipartBody"></a>

## <a name="multipartbody"></a>multipartBody

Vrátí text pro určitou část ve výstupu akce, který má více částí.

```
multipartBody('<actionName>', <index>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Název akce, která má výstup s více částmi. | 
| <*Index*> | Ano | Integer | Hodnota indexu pro požadovanou část | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Text*> | Řetězec | Text pro zadanou část | 
|||| 

<a name="not"></a>

## <a name="not"></a>Not

Zkontrolujte, zda má výraz hodnotu false. Vrátí hodnotu PRAVDA, pokud má výraz hodnotu false nebo vrátí hodnotu false v případě hodnoty true.

```
not(<expression>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Výraz*> | Ano | Logická hodnota | Výraz, který má zkontrolovat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Vrátí hodnotu PRAVDA, pokud má výraz hodnotu false. Vrátí hodnotu false, pokud má výraz hodnotu true. |  
|||| 

*Příklad 1*

Tyto příklady zkontrolujte, zda jsou false zadaných výrazů: 

```
not(false)
not(true)
```

A vracet tyto výsledky:

* První příklad: výraz je hodnota false, tak, aby funkce `true`.
* Druhý příklad: výraz je hodnota true, tak, aby funkce `false`.

*Příklad 2*

Tyto příklady zkontrolujte, zda jsou false zadaných výrazů: 

```
not(equals(1, 2))
not(equals(1, 1))
```

A vracet tyto výsledky:

* První příklad: výraz je hodnota false, tak, aby funkce `true`.
* Druhý příklad: výraz je hodnota true, tak, aby funkce `false`.

<a name="or"></a>

## <a name="or"></a>nebo

Zkontrolujte, zda má nejméně jeden výraz hodnotu true. Vrátí hodnotu PRAVDA, pokud alespoň jeden výraz hodnotu true, nebo vrátí false, když jsou všechny false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Expression1*>, <*expression2*>,... | Ano | Logická hodnota | Výrazy ke kontrole | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA | Logická hodnota | Vrátí hodnotu PRAVDA, pokud alespoň jeden výraz hodnotu true. Vrátí hodnotu false, pokud jsou všechny výrazy hodnotu false. |  
|||| 

*Příklad 1*

Tyto příklady zkontrolujte, zda má nejméně jeden výraz hodnotu true:

```
or(true, false)
or(false, false)
```

A vracet tyto výsledky:

* První příklad: nejméně jeden výraz má hodnotu true, tak, aby funkce `true`.
* Druhý příklad: jsou oba výrazy hodnoty false, vrátí funkce hodnotu `false`.

*Příklad 2*

Tyto příklady zkontrolujte, zda má nejméně jeden výraz hodnotu true:

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

A vracet tyto výsledky:

* První příklad: nejméně jeden výraz má hodnotu true, tak, aby funkce `true`.
* Druhý příklad: jsou oba výrazy hodnoty false, vrátí funkce hodnotu `false`.

<a name="parameters"></a>

## <a name="parameters"></a>parameters

Vrátí hodnotu pro parametr, který je popsaný v definici aplikace logiky. 

```
parameters('<parameterName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název parametru*> | Ano | Řetězec | Název parametru, jehož hodnotu chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota parametru*> | Všechny | Hodnota zadaná parametru | 
|||| 

*Příklad* 

Předpokládejme, že tato hodnota JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Tento příklad načte hodnoty pro zadaný parametr:

```
parameters('fullName')
```

A vrátí výsledek: `"Sophia Owen"`

<a name="rand"></a>

## <a name="rand"></a>rand

Vrátí ze zadaného rozsahu, který je také zahrnuto pouze po uplynutí počáteční náhodné celé číslo.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota minValue*> | Ano | Integer | Nejnižší celé číslo v rozsahu | 
| <*Hodnota maxValue*> | Ano | Integer | Celé číslo, který následuje nejvyšší číslo v rozsahu, která vrací funkce | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*náhodný výsledek*> | Integer | Náhodné celé číslo vrácen ze zadaného rozsahu |  
|||| 

*Příklad*

Tento příklad načte náhodné celé číslo v určeném rozsahu, s výjimkou maximální hodnota: 

```
rand(1, 5)
```

A jako výsledek vrátí jednu z těchto čísel: `1`, `2`, `3`, nebo `4` 

<a name="range"></a>

## <a name="range"></a>rozsah

Vrátí pole celé číslo, které začíná od zadané celé číslo.

```
range(<startIndex>, <count>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Počáteční index*> | Ano | Integer | Celočíselná hodnota, která spustí pole jako první položka | 
| <*Počet*> | Ano | Integer | Počet celých čísel v poli | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*rozsah výsledek*>] | Pole | Pole s celými čísly počínaje zadaným indexem. |  
|||| 

*Příklad*

Tento příklad vytvoří celočíselné pole, která začíná od zadaného indexu a má zadaný počet celých čísel:

```
range(1, 4)
```

A vrátí výsledek: `[1, 2, 3, 4]`

<a name="replace"></a>

## <a name="replace"></a>nahradit

Nahraďte řetězec zadaný řetězec a vrátí výsledný řetězec. Tato funkce rozlišuje velká a malá písmena.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který obsahuje dílčí řetězec pro nahrazení | 
| <*oldText*> | Ano | Řetězec | Dílčí řetězec pro nahrazení | 
| <*newText*> | Ano | Řetězec | Řetězci pro nahrazení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace textu*> | Řetězec | Aktualizované řetězec po nahrazení dílčí řetězec <p>Není-li podřetězec nalezen, vraťte původního řetězce. | 
|||| 

*Příklad* 

Tento příklad vyhledá dílčí řetězec "starými" v "starý řetězec" a nahradí "starými" "New": 

```
replace('the old string', 'old', 'new')
```

A vrátí výsledek: `"the new string"`

<a name="removeProperty"></a>

## <a name="removeproperty"></a>removeProperty

Odebrání vlastnosti z objektu a vrácení aktualizovaného objektu.

```
removeProperty(<object>, '<property>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*objekt*> | Ano | Objekt | Objekt JSON z, ve které chcete odebrat vlastnost | 
| <*Vlastnost*> | Ano | Řetězec | Název vlastnosti, která má odebrat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktualizace objektu*> | Objekt | Aktualizovaný objekt JSON bez zadané vlastnosti | 
|||| 

*Příklad*

V tomto příkladu odebere `"accountLocation"` vlastnost z `"customerProfile"` objektu, který je převést na JSON pomocí [JSON()](#json) funkci a vrátí aktualizovaného objektu:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

## <a name="setproperty"></a>Metoda setProperty

Nastavit hodnotu pro vlastnost objektu a vrácení aktualizovaného objektu. Chcete-li přidat nové vlastnosti tuto funkci můžete použít nebo [addProperty()](#addProperty) funkce.

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*objekt*> | Ano | Objekt | Objekt JSON, jehož vlastnosti chcete nastavit | 
| <*Vlastnost*> | Ano | Řetězec | Název vlastnosti existujících nebo nových nastavení | 
| <*Hodnota*> | Ano | Všechny | Hodnota k nastavení pro zadanou vlastnost |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktualizace objektu*> | Objekt | Aktualizovaný objekt JSON jehož nastavenou | 
|||| 

*Příklad*

Tento příklad nastaví `"accountNumber"` vlastnost `"customerProfile"` objektu, který je převést na JSON pomocí [JSON()](#json) funkce. Funkce přiřadí hodnoty generované [guid()](#guid) funkci a vrátí aktualizovaného objektu JSON:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

## <a name="skip"></a>Přeskočit

Odebrat položky z přední části kolekce a vrátí *všechny ostatní* položky.

```
skip([<collection>], <count>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekce*> | Ano | Pole | Kolekce, jejichž položky, které chcete odebrat | 
| <*Počet*> | Ano | Integer | Kladné celé číslo pro počet položek, které chcete odebrat do přední | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*aktualizovat kolekci*>] | Pole | Aktualizované kolekce po odebrání zadaných položek | 
|||| 

*Příklad*

V tomto příkladu odebere jednu položku, číslo 0, z přední části zadaného pole: 

```
skip([0, 1, 2, 3], 1)
```

A vrátí toto pole Zbývající položky: `[1,2,3]`

<a name="split"></a>

## <a name="split"></a>split

Vrátit pole, která obsahuje všechny znaky z řetězce a má každý znak oddělené *oddělovač*.

```
split('<text>', '<separator>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který obsahuje znaky, které chcete rozdělit |  
| <*Oddělovač*> | Ano | Řetězec | Oddělovač, který se zobrazí mezi každý znak v výsledné pole | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*char1*><*oddělovač*><*char2*><*oddělovač*>...] | Pole | Výsledná pole vytvořené ze všech položek v zadaném řetězci |
|||| 

*Příklad* 

Tento příklad vytvoří pole ze zadaného řetězce, každý znak oddělíte čárkou jako oddělovačem:

```
split('abc', ',')
```

A vrátí výsledek: `[a, b, c]`

<a name="startOfDay"></a>

## <a name="startofday"></a>startOfDay

Vrátí začátek dne pro časové razítko. 

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Zadané časové razítko, ale začínající na nulté celé hodiny značky daného dne | 
|||| 

*Příklad* 

Tento příklad vyhledá začátek dne pro toto časové razítko:

```
startOfDay('2018-03-15T13:30:30Z')
```

A vrátí výsledek: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

## <a name="startofhour"></a>startOfHour

Vrátí začátek hodiny pro hodnotu časového razítka. 

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Zadané časové razítko, ale od značkou nula minut za hodinu | 
|||| 

*Příklad* 

Tento příklad vyhledá začátek hodiny pro hodnotu tohoto časového razítka:

```
startOfHour('2018-03-15T13:30:30Z')
```

A vrátí výsledek: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

## <a name="startofmonth"></a>startOfMonth

Vrátí začátek měsíce pro časové razítko. 

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Zadané časové razítko, ale od první den v měsíci v označení nulté celé hodiny | 
|||| 

*Příklad* 

V tomto příkladu vrátí začátek měsíce pro toto časové razítko:

```
startOfMonth('2018-03-15T13:30:30Z')
```

A vrátí výsledek: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

## <a name="startswith"></a>startsWith

Zkontrolujte, jestli řetězec začíná na konkrétní dílčí řetězec. Vrátí hodnotu PRAVDA, pokud se najde dílčí řetězec, nebo vrátí hodnotu false, když nebyl nalezen. Tato funkce není malá a velká písmena.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který má zkontrolovat | 
| <*Prohledávanýtext*> | Ano | Řetězec | Počáteční řetězec k vyhledání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| PRAVDA nebo NEPRAVDA  | Logická hodnota | Vrátí hodnotu PRAVDA, když se najde dílčí řetězec začíná. Vrátí hodnotu false po nebyl nalezen. | 
|||| 

*Příklad 1* 

Tento příklad kontroluje, zda začíná řetězcem "hello world" podřetězec "hello":

```
startsWith('hello world', 'hello')
```

A vrátí výsledek: `true`

*Příklad 2*

Tento příklad kontroluje, zda začíná řetězcem "hello world" podřetězec "Pozdrav":

```
startsWith('hello world', 'greetings')
```

A vrátí výsledek: `false`

<a name="string"></a>

## <a name="string"></a>řetězec

Vrácení verze řetězce pro hodnoty.

```
string(<value>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Všechny | Hodnota k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Řetězcová hodnota*> | Řetězec | Řetězec verze pro zadanou hodnotu | 
|||| 

*Příklad 1* 

Tento příklad vytvoří řetězec verze pro toto číslo:

```
string(10)
```

A vrátí výsledek: `"10"`

*Příklad 2*

Tento příklad vytvoří řetězec pro zadaný objekt JSON a používá znak zpětného lomítka (\\) jako řídicí znak pro značku dvojité uvozovky (").

```
string( { "name": "Sophie Owen" } )
```

A vrátí výsledek: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

## <a name="sub"></a>Sub

Vrátí výsledek v daných druhé číslo z první číslo.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*menšenec*> | Ano | Integer nebo Float | Číslo, ze kterého se má odečíst *subtrahend* | 
| <*menšitel*> | Ano | Integer nebo Float | Číslo, které se má odečíst od *minuend* | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výsledek*> | Integer nebo Float | Výsledek daných druhé číslo z první číslo | 
|||| 

*Příklad* 

V tomto příkladu odečte druhý číslo od první číslo:

```
sub(10.3, .3)
```

A vrátí výsledek: `10`

<a name="substring"></a>

## <a name="substring"></a>dílčí řetězec

Vrácení znaků z řetězce, počínaje od zadané pozice nebo index. Index hodnoty začíná číslem 0. 

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, jehož znaků | 
| <*Počáteční index*> | Ano | Integer | Kladné číslo pro počáteční pozice, nebo hodnotu indexu | 
| <*Délka*> | Ano | Integer | Kladný počet znaků, které chcete v dílčí řetězec | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výsledek dílčí řetězec*> | Řetězec | Dílčí řetězec s zadaný počet znaků, počínaje na pozici zadaného indexu ve zdrojovém řetězci | 
|||| 

*Příklad* 

Tento příklad vytvoří pět znaků podřetězce ze zadaného řetězce, počínaje hodnotu indexu 6:

```
substring('hello world', 6, 5)
```

A vrátí výsledek: `"world"`

<a name="subtractFromTime"></a>

## <a name="subtractfromtime"></a>subtractFromTime

Odečte počet časových jednotek z časové razítko. Viz také [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*interval*> | Ano | Integer | Počet jednotek určený čas se má odečíst | 
| <*timeUnit*> | Ano | Řetězec | Jednotka času pro použití s *interval*: "Sekundu", "Minute", "Hour", "Day", "Týden", "Měsíc", "Rok" | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizace časového razítka*> | Řetězec | Časové razítko minus zadaný počet časových jednotek | 
|||| 

*Příklad 1*

V tomto příkladu odečte jeden den od tohoto časového razítka:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day') 
```

A vrátí výsledek: `"2018-01-01T00:00:00:0000000Z"`

*Příklad 2*

V tomto příkladu odečte jeden den od tohoto časového razítka:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D') 
```

A vrátí výsledek volitelné formátu "D": `"Monday, January, 1, 2018"`

<a name="take"></a>

## <a name="take"></a>Take

Vrátí položky ze začátku kolekce. 

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Kolekce*> | Ano | Řetězec nebo pole | Kolekce, jejichž položky, které chcete | 
| <*Počet*> | Ano | Integer | Kladné celé číslo pro počet položek, které chcete z front | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*dílčí*> nebo [<*dílčí*>] | Řetězec nebo pole, v uvedeném pořadí | Řetězec nebo pole, který má zadaný počet položek přijatá od začátku původní kolekci | 
|||| 

*Příklad*

Tyto příklady získat zadaný počet položek od začátku těchto kolekcí:

```
take('abcde`, 3)
take([0, 1, 2, 3, 4], 3)
```

A vracet tyto výsledky:

* První příklad: `"abc"`
* Druhý příklad: `[0, 1, 2]`

<a name="ticks"></a>

## <a name="ticks"></a>značky

Vrátit `ticks` hodnota vlastnosti pro zadané časové razítko. A *značek* během intervalu 100 nanosekund.

```
ticks('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Časové razítko*> | Ano | Řetězec | Řetězec pro časové razítko | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Číslo značky*> | Integer | Počet taktů od zadaného časového razítka | 
|||| 

<a name="toLower"></a>

## <a name="tolower"></a>toLower

Vrátí řetězec ve formátu malá písmena. Pokud znak v řetězci nemá malá verzi, zůstane ve vráceném řetězci beze změny tohoto znaku.

```
toLower('<text>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který se vrátí ve formátu malá písmena | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*text malými písmeny*> | Řetězec | Původní řetězec ve formátu malá písmena | 
|||| 

*Příklad* 

Tento příklad převede tento řetězec na malá písmena: 

```
toLower('Hello World')
```

A vrátí výsledek: `"hello world"`

<a name="toUpper"></a>

## <a name="toupper"></a>toUpper

Vrátí řetězec ve formátu velká písmena. Pokud znak v řetězci nemá verzi s velkými písmeny, zůstane ve vráceném řetězci beze změny tohoto znaku.

```
toUpper('<text>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který se vrátí ve formátu velká písmena | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*text velkými písmeny psané*> | Řetězec | Původní řetězec ve formátu velká písmena | 
|||| 

*Příklad* 

Tento příklad převede tento řetězec na velká písmena:

```
toUpper('Hello World')
```

A vrátí výsledek: `"HELLO WORLD"`

<a name="trigger"></a>

## <a name="trigger"></a>Aktivační událost

Vrátíte výstupní aktivační události v modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON, které můžete přiřadit k výrazu. 

* Ve vstupech triggeru od tato funkce vrací výstup z předchozího zpracování. 

* Uvnitř aktivační podmínku tato funkce vrací výstup z aktuálního spuštění. 

Ve výchozím nastavení, funkce, odkazuje na objekt celý aktivační událost, ale můžete volitelně zadat vlastnost jehož požadovanou hodnotu. Kromě toho tato funkce má k dispozici Zkrácený tvar vlastností verzí naleznete v tématu [triggerOutputs()](#triggerOutputs) a [triggerBody()](#triggerBody). 

```
trigger()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výstupu triggeru*> | Řetězec | Výstup z aktivační události za běhu | 
|||| 

<a name="triggerBody"></a>

## <a name="triggerbody"></a>triggerBody

Vrátí trigger's `body` výstupu za běhu. Zkrácený tvar vlastností `trigger().outputs.body`. Zobrazit [trigger()](#trigger). 

```
triggerBody()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výstupu triggeru textu*> | Řetězec | `body` Výstup z triggeru | 
|||| 

<a name="triggerFormDataMultiValues"></a>

## <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Vrátí pole hodnot, které odpovídají názvu klíče v trigger's *data formuláře* nebo *form-encoded.* výstup. 

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Klíč*> | Ano | Řetězec | Název klíče, jejíž hodnotu chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*pole s klíčem hodnoty*>] | Pole | Pole obsahující všechny hodnoty, které odpovídají zadanému klíči | 
|||| 

*Příklad* 

Tento příklad vytvoří pole z "adresa URL kanálu" hodnotu klíče v RSS trigger form-data nebo form-encoded. výstup: 

```
triggerFormDataMultiValues('feedUrl')
```

A jako výsledek příkladu vrátí toto pole: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

## <a name="triggerformdatavalue"></a>triggerFormDataValue

Vrátí řetězec s jednu hodnotu, která odpovídá názvu klíče v trigger's *data formuláře* nebo *form-encoded.* výstup. Pokud funkce najde více než jedna shoda, funkce vyvolá chybu.

```
triggerFormDataValue('<key>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Klíč*> | Ano | Řetězec | Název klíče, jejíž hodnotu chcete |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*klíč hodnota*> | Řetězec | Hodnota v zadaný klíč | 
|||| 

*Příklad* 

Tento příklad vytvoří řetězec z "adresa URL kanálu" hodnotu klíče v RSS trigger form-data nebo form-encoded. výstup:

```
triggerFormDataValue('feedUrl')
```

A jako výsledek příkladu vrátí tento řetězec: `"http://feeds.reuters.com/reuters/topNews"` 

<a name="triggerMultipartBody"></a>

Vrátí text pro určitou část ve trigger's výstup, který má více částí. 

```
triggerMultipartBody(<index>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Index*> | Ano | Integer | Hodnota indexu pro požadovanou část |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Text*> | Řetězec | Text pro zadanou část ve výstupu aktivační událost s více částmi. | 
|||| 

<a name="triggerOutputs"></a>

## <a name="triggeroutputs"></a>triggerOutputs

Vrátíte výstupní aktivační události v modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON. Zkrácený tvar vlastností `trigger().outputs`. Zobrazit [trigger()](#trigger). 

```
triggerOutputs()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výstupu triggeru*> | Řetězec | Výstup z aktivační události za běhu  | 
|||| 

<a name="trim"></a>

## <a name="trim"></a>Trim

Odebere úvodní a koncové mezery z řetězce a vrátit řetězec aktualizované.

```
trim('<text>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který se má odebrat úvodní a koncové mezery | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*updatedText*> | Řetězec | Aktualizovanou verzi pro původní řetězec bez počáteční ani koncové prázdné znaky | 
|||| 

*Příklad* 

V tomto příkladu odebere úvodní a koncové prázdné znaky v řetězci "Hello World":  

```
trim(' Hello World  ')
```

A vrátí výsledek: `"Hello World"`

<a name="union"></a>

## <a name="union"></a>sjednocení

Vrátí kolekci, která má *všechny* položky ze zadaných kolekcí. Zobrazit ve výsledku se může zobrazit položky v každé kolekci této funkci byl předán. Pokud jeden nebo více položek se stejným názvem, zobrazí se ve výsledku poslední položka s tímto názvem. 

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*kolekce 2*>,...  | Ano | Pole nebo objekt, ale ne obojí | Kolekce, ze kterého má *všechny* položky | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*updatedCollection*> | Pole nebo objekt, v uvedeném pořadí | Kolekce se všechny položky ze zadaných kolekcí – žádné duplikáty | 
|||| 

*Příklad* 

Tento příklad načte *všechny* položky z těchto kolekcí: 

```
union([1, 2, 3], [1, 2, 10, 101])
```

A vrátí výsledek: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

## <a name="uricomponent"></a>uriComponent

Vrácení verze URI kódovaný identifikátor (URI) pro řetězec tak, že nahradíte adresu URL problematické znaky s řídicími znaky. Tuto funkci použít spíše než [encodeUriComponent()](#encodeUriComponent). I když obě funkce fungovat stejným způsobem, `uriComponent()` je upřednostňována.

```
uriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec převést na formát kódování identifikátoru URI | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*kódovaný identifikátor uri*> | Řetězec | Řetězec kódovaný identifikátor URI s řídicími znaky | 
|||| 

*Příklad*

Tento příklad vytvoří verzi kódovaný identifikátor URI pro tento řetězec:

```
uriComponent('https://contoso.com')
```

A vrátí výsledek: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

## <a name="uricomponenttobinary"></a>uriComponentToBinary

Vrátí binární verzi pro komponentu URI identifikátor URI.

```
uriComponentToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec kódovaný identifikátor URI k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární soubor pro kódovaný uri*> | Řetězec | Binární verze pro řetězec kódovaný identifikátor URI. Binární obsah, který je s kódováním base64 a je znázorněn `$content`. | 
|||| 

*Příklad*

Tento příklad vytvoří binární verze pro tento řetězec kódovaný identifikátor URI: 

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

A vrátí výsledek: 

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

## <a name="uricomponenttostring"></a>uriComponentToString

Vrátí řetězec verze pro identifikátor URI (URI) zakódovaného řetězce, efektivně dekódování řetězec kódovaný identifikátor URI.

```
uriComponentToString('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec kódovaný identifikátor URI k dekódování | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*dekódovat. identifikátor uri*> | Řetězec | Dekódovaný verze pro řetězec kódovaný identifikátor URI | 
|||| 

*Příklad*

Tento příklad vytvoří dekódovaný řetězec verze pro tento řetězec kódovaný identifikátor URI: 

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

A vrátí výsledek: `"https://contoso.com"` 

<a name="uriHost"></a>

## <a name="urihost"></a>uriHost

Vrátit `host` hodnota pro identifikátor URI (URI).

```
uriHost('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Identifikátor URI*> | Ano | Řetězec | Identifikátor URI jehož `host` hodnotu, kterou chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota hostitele*> | Řetězec | `host` Hodnotu pro zadaný identifikátor URI | 
|||| 

*Příklad*

Tento příklad vyhledá `host` hodnotu pro tento identifikátor URI: 

```
uriHost('https://www.localhost.com:8080')
```

A vrátí výsledek: `"www.localhost.com"`

<a name="uriPath"></a>

## <a name="uripath"></a>uriPath

Vrátit `path` hodnota pro identifikátor URI (URI). 

```
uriPath('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Identifikátor URI*> | Ano | Řetězec | Identifikátor URI jehož `path` hodnotu, kterou chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota cesty*> | Řetězec | `path` Hodnotu pro zadaný identifikátor URI. Pokud `path` nebude mít hodnotu, vrátí znak "/". | 
|||| 

*Příklad*

Tento příklad vyhledá `path` hodnotu pro tento identifikátor URI: 

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí výsledek: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

## <a name="uripathandquery"></a>uriPathAndQuery

Vrátit `path` a `query` hodnoty pro identifikátor URI (URI).

```
uriPathAndQuery('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Identifikátor URI*> | Ano | Řetězec | Identifikátor URI jehož `path` a `query` hodnoty požadujete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota cesty dotazu*> | Řetězec | `path` a `query` hodnot pro zadaný identifikátor URI. Pokud `path` nelze zadat hodnotu, vrátí znak "/". | 
|||| 

*Příklad*

Tento příklad vyhledá `path` a `query` hodnoty pro tento identifikátor URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí výsledek: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

## <a name="uriport"></a>uriPort

Vrátit `port` hodnota pro identifikátor URI (URI).

```
uriPort('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Identifikátor URI*> | Ano | Řetězec | Identifikátor URI jehož `port` hodnotu, kterou chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota portu*> | Integer | `port` Hodnotu pro zadaný identifikátor URI. Pokud `port` nelze zadat hodnotu, vrátí výchozí port pro protokol. | 
|||| 

*Příklad*

V tomto příkladu vrátí `port` hodnotu pro tento identifikátor URI:

```
uriPort('http://www.localhost:8080')
```

A vrátí výsledek: `8080`

<a name="uriQuery"></a>

## <a name="uriquery"></a>uriQuery

Vrátit `query` hodnota pro identifikátor URI (URI).

```
uriQuery('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Identifikátor URI*> | Ano | Řetězec | Identifikátor URI jehož `query` hodnotu, kterou chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota dotazu*> | Řetězec | `query` Hodnotu pro zadaný identifikátor URI | 
|||| 

*Příklad*

V tomto příkladu vrátí `query` hodnotu pro tento identifikátor URI: 

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí výsledek: `"?date=today"`

<a name="uriScheme"></a>

## <a name="urischeme"></a>uriScheme

Vrátit `scheme` hodnota pro identifikátor URI (URI).

```
uriScheme('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Identifikátor URI*> | Ano | Řetězec | Identifikátor URI jehož `scheme` hodnotu, kterou chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*schéma – hodnota*> | Řetězec | `scheme` Hodnotu pro zadaný identifikátor URI | 
|||| 

*Příklad*

V tomto příkladu vrátí `scheme` hodnotu pro tento identifikátor URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí výsledek: `"http"`

<a name="utcNow"></a>

## <a name="utcnow"></a>utcNow

Vrátí aktuální časové razítko. 

```
utcNow('<format>')
```

Volitelně můžete zadat jiný formát s <*formátu*> parametru. 


| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formátovací vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktuální časové razítko*> | Řetězec | Aktuální datum a čas | 
|||| 

*Příklad 1*

Předpokládejme, že dnes je 15. dubna 2018 v 1:00:00 PM. Tento příklad načte aktuální časové razítko: 

```
utcNow()
```

A vrátí výsledek: `"2018-04-15T13:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že dnes je 15. dubna 2018 v 1:00:00 PM. Tento příklad načte aktuální časové razítko volitelné formátu "D":

```
utcNow('D')
```

A vrátí výsledek: `"Sunday, April 15, 2018"`

<a name="variables"></a>

## <a name="variables"></a>Proměnné

Vrátí hodnotu zadané proměnné. 

```
variables('<variableName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*NázevProměnné*> | Ano | Řetězec | Název proměnné, jejíž hodnotu chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota proměnné*> | Všechny | Hodnotu zadanou proměnnou | 
|||| 

*Příklad*

Předpokládejme, že aktuální hodnota proměnné "numItems" je 20. V tomto příkladu získá celočíselnou hodnotou pro tuto proměnnou:

```
variables('numItems')
```

A vrátí výsledek: `20`

<a name="workflow"></a>

## <a name="workflow"></a>pracovní postup

Vrátí všechny podrobnosti samotného pracovního postupu za běhu. 

```
workflow().<property>
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Vlastnost*> | Ne | Řetězec | Název vlastnosti pracovního postupu, jejíž hodnotu chcete <p>Objekt pracovního postupu má tyto vlastnosti: **název**, **typ**, **id**, **umístění**, a **spustit**. **Spustit** hodnota vlastnosti je také objekt, který má tyto vlastnosti: **název**, **typ**, a **id**. | 
||||| 

*Příklad*

V tomto příkladu vrátí název aktuální běh pracovního postupu:

```
workflow().run.name
```

<a name="xml"></a>

## <a name="xml"></a>xml

Vrátí verzi XML pro řetězec, který obsahuje objekt JSON. 

```
xml('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec s objekt JSON, který chcete převést <p>Musí být pouze jeden kořenový vlastnost objektu JSON. <br>Použít znak zpětného lomítka (\\) jako řídicí znak pro dvojité uvozovky ("). | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*verze XML*> | Objekt | Kódovaný XML pro zadaný řetězec nebo objekt JSON | 
|||| 

*Příklad 1*

Tento příklad vytvoří verzi XML pro tento řetězec, který obsahuje objekt JSON: 

`xml( '{ \"name\": \"Sophia Owen\" }' )`

A vrátí výsledek XML: 

```xml
<name>Sophia Owen</name>
```

*Příklad 2*

Předpokládejme, že máte tohoto objektu JSON:

```json
{ 
  "person": { 
    "name": "Sophia Owen", 
    "city": "Seattle" 
  } 
}
```

Tento příklad vytvoří XML pro řetězec, který obsahuje tohoto objektu JSON:

`xml( '{ \"person\": { \"name\": \"Sophia Owen\", \"city\": \"Seattle\" } }' )`

A vrátí výsledek XML: 

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

## <a name="xpath"></a>výraz XPath

Kontrolovat XML uzlů nebo hodnoty, které odpovídají výrazu XPath (XML Path Language) a vrátí odpovídající uzly nebo hodnoty. Výraz XPath, nebo jen "XPath", pomůže vám tak, aby uzly nebo výpočetní hodnoty můžete vybrat v obsahu XML struktury dokumentu XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*XML*> | Ano | Všechny | Řetězec XML k vyhledání uzlů nebo hodnoty, které odpovídají hodnota výrazu XPath | 
| <*výraz XPath*> | Ano | Všechny | Výraz XPath, který je použit k vyhledání odpovídající uzly XML nebo hodnoty | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*uzel XML*> | XML | Pokud pouze jeden uzel odpovídá zadanému výrazu XPath uzel XML | 
| <*Hodnota*> | Všechny | Hodnota z při jenom jedna hodnota odpovídá zadanému výrazu XPath uzel XML | 
| [<*xml node1*>, <*xml node2*>;...] </br>-nebo- </br>[<*hodnota1*>, <*hodnota2*>;...] | Pole | Pole s uzly XML nebo hodnoty, které odpovídají zadanému výrazu XPath | 
|||| 

*Příklad 1*

Tento příklad vyhledá uzly, které odpovídají `<name></name>` uzel v zadaných argumentů a vrátí pole těmito hodnotami uzlu: 

`xpath(xml(parameters('items')), '/produce/item/name')`

Tady jsou argumenty:

* Řetězec "položky", který obsahuje tento kód XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  V příkladu se používá [parameters()](#parameters) funkce získat řetězec XML z argumentu "položky", ale musíte také tak řetězec převést na formát XML pomocí [xml()](#xml) funkce. 

* Výraz tento výraz XPath, který se předá jako řetězec:

  `"/produce/item/name"`

Tady je pole výsledku s uzly, které odpovídají `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Příklad 2*

Následující příklad 1, tento příklad vyhledá uzly, které odpovídají `<count></count>` uzlu a přidá tyto hodnoty uzlu s `sum()` funkce:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

A vrátí výsledek: `30`

*Příklad 3*

V tomto příkladu oba výrazy najít uzly, které odpovídají `<location></location>` uzlu, jsou zadané argumenty, které zahrnují XML s oborem názvů. Výrazy použít znak zpětného lomítka (\\) jako řídicí znak pro dvojité uvozovky (").

* *Výraz 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Výraz 2* 

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

Tady jsou argumenty:

* Tato XML, který obsahuje obor názvů dokumentu XML, `xmlns="http://contoso.com"`: 

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Buď XPath výrazu tady:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]`

Tady je výsledek uzlu, který odpovídá `<location></location` uzlu:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Příklad 4:*

Následující příklad 3, tento příklad vyhledá hodnota v `<location></location>` uzlu: 

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

A vrátí výsledek: `"Paris"`

## <a name="next-steps"></a>Další postup

Další informace o [jazyka definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md)

---
title: Funkce jazyka pro definici pracovního postupu - Azure Logic Apps | Microsoft Docs
description: Další informace o funkcích, které můžete použít v definicích pracovní postup aplikace logiky
services: logic-apps
author: ecfan
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/25/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 0155e35641a0407fe48c4da07400fa188152b0af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="workflow-definition-language-functions-reference-for-azure-logic-apps"></a>Referenční dokumentace funkcí Definition Language pracovního postupu pro Azure Logic Apps

Tento článek popisuje funkce, které můžete použít při vytváření pracovních postupů s [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Další informace o definicích aplikaci logiky najdete v tématu [jazyk definice pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> V syntaxi pro parametr definice otazník (?), který se zobrazí po parametr znamená parametr je volitelný. Například v tématu [getFutureTime()](#getFutureTime).

<a name="action"></a>

## <a name="action"></a>akce

Vrátí *aktuální* akce výstupu v modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON, které lze přiřadit výrazu. Ve výchozím nastavení tato funkce odkazuje na objekt celé akce, ale Volitelně můžete zadat vlastnost jehož hodnotu chcete. Viz také [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Můžete použít `action()` funkce jenom na těchto místech: 

* `unsubscribe` Vlastnost pro akce webhooku, takže přistupujete výsledek z původní `subscribe` požadavku
* `trackedProperties` Vlastnost pro akci.
* `do-until` Cykly podmínku pro akci.

```
action()
action().outputs.body.<property> 
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Vlastnost*> | Ne | Řetězec | Název pro objekt akce vlastnost jehož hodnotu chcete: **název**, **startTime**, **endTime**, **vstupy**,  **výstupy**, **stav**, **kód**, **trackingId**, a **clientTrackingId**. Na portálu Azure můžete zjistit tyto vlastnosti zobrazením podrobnosti konkrétní spuštění historie. Další informace najdete v tématu [REST API – akce spuštění pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výstup akce*> | Řetězec | Výstup z aktuální akce nebo vlastností | 
|||| 

<a name="actionBody"></a>

## <a name="actionbody"></a>actionBody

Vrátí akci `body` výstup za běhu. Sdružená vlastnost `actions('<actionName>').outputs.body`. V tématu [body()](#body) a [actions()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Název akce `body` výstupu, který chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výstup textu akce*> | Řetězec | `body` Výstup z zadanou akci | 
|||| 

*Příklad*

Tento příklad načte `body` výstup z akce Twitter `Get user`: 

```
actionBody('Get_user')
```

A vrátí výsledek tohoto:

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

Vrátí výstup akce za běhu. Sdružená vlastnost `actions('<actionName>').outputs`. V tématu [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Název akce výstupu, které mají | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*Výstup*> | Řetězec | Výstup z zadanou akci | 
|||| 

*Příklad*

Tento příklad načte výstup z akce Twitter `Get user`: 

```
actionOutputs('Get_user')
```

A vrátí výsledek tohoto:

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

Vrátíte výstup akce v modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON, které lze přiřadit výrazu. Ve výchozím nastavení, funkce odkazuje na objekt celé akce, ale Volitelně můžete zadat vlastnost jejichž požadovanou hodnotu. Sdružená vlastnost verze najdete v tématu [actionBody()](#actionBody), [actionOutputs()](#actionOutputs), a [body()](#body). Aktuální akci, naleznete v části [action()](#action).

> [!NOTE] 
> Dříve, můžete použít `actions()` funkce nebo `conditions` element při určení, jestli akce spustila na základě na výstup z jiné akce. Ale deklarovat explicitně závislosti mezi akce, musíte teď použít závislé akce `runAfter` vlastnost. Další informace o `runAfter` vlastnost, najdete v části [Catch a zpracování chyb s vlastností runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property> 
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Název objektu jejichž výstup má být akce  | 
| <*Vlastnost*> | Ne | Řetězec | Název pro objekt akce vlastnost jehož hodnotu chcete: **název**, **startTime**, **endTime**, **vstupy**,  **výstupy**, **stav**, **kód**, **trackingId**, a **clientTrackingId**. Na portálu Azure můžete zjistit tyto vlastnosti zobrazením podrobnosti konkrétní spuštění historie. Další informace najdete v tématu [REST API – akce spuštění pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výstup akce*> | Řetězec | Výstup z zadanou akci nebo vlastnost | 
|||| 

*Příklad*

Tento příklad načte `status` hodnotu vlastnosti z akce Twitter `Get user` za běhu: 

```
actions('Get_user').outputs.body.status 
```

A vrátí výsledek tohoto: `"Succeeded"`

<a name="add"></a>

## <a name="add"></a>přidat

Vrátí výsledek v přidávání dvou čísel.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*summand_1*>, <*summand_2*> | Ano | Celé číslo, Float, nebo smíšené | Chcete-li přidat čísla | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výsledek – součet*> | Celé číslo nebo číslo Float | Výsledkem přidání zadaných čísel | 
|||| 

*Příklad*

Tento příklad přidá zadaného čísla:

```
add(1, 1.5)
```

A vrátí výsledek tohoto: `2.5`

<a name="addDays"></a>

## <a name="adddays"></a>Přidat_dny

Přidejte počet dnů do časového razítka.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*počet dnů*> | Ano | Integer | Kladné a záporné počet dní, které chcete přidat | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Časové razítko přičtením zadaného počtu dnů  | 
|||| 

*Příklad 1*

Tento příklad přidá zadané časové razítko 10 dnů:

```
addDays('2018-03-15T13:00:00Z', 10)
```

A vrátí výsledek tohoto: `"2018-03-25T00:00:0000000Z"`

*Příklad 2*

Tento příklad odečítá pět dní od zadaného časového razítka:

```
addDays('2018-03-15T00:00:00Z', -5)
```

A vrátí výsledek tohoto: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

## <a name="addhours"></a>addHours

Přidáte počet hodin časové razítko.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Hodiny*> | Ano | Integer | Kladné a záporné počet hodin, které chcete přidat | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Časové razítko plus zadaného počtu hodin  | 
|||| 

*Příklad 1*

Tento příklad přidá 10 hodin zadané časové razítko:

```
addHours('2018-03-15T00:00:00Z', 10)
```

A vrátí výsledek tohoto: `"2018-03-15T10:00:0000000Z"`

*Příklad 2*

Tento příklad odečítá pět hodin ze zadaného časového razítka:

```
addHours('2018-03-15T15:00:00Z', -5)
```

A vrátí výsledek tohoto: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

## <a name="addminutes"></a>addMinutes

Počet minut, přidejte do časového razítka.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*minut*> | Ano | Integer | Kladné a záporné počet minut pro přidání | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Časové razítko plus zadaném počtu minut | 
|||| 

*Příklad 1*

Tento příklad přidá 10 minut na zadané časové razítko:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

A vrátí výsledek tohoto: `"2018-03-15T00:20:00.0000000Z"`

*Příklad 2*

Tento příklad odečítá od pěti minut od zadaného časového razítka:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

A vrátí výsledek tohoto: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

## <a name="addproperty"></a>AddProperty –

Přidejte vlastnost a její hodnota nebo dvojice název hodnota, na objekt JSON a vrátí objekt aktualizované. Pokud objekt již existuje v době běhu, funkce vyvolá chybu.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Objekt*> | Ano | Objekt | Objekt JSON, ve které chcete přidat vlastnost | 
| <*Vlastnost*> | Ano | Řetězec | Název vlastnosti, která má přidat | 
| <*Hodnota*> | Ano | Všechny | Hodnotu pro vlastnost |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktualizovat objekt*> | Objekt | Aktualizovaný objekt JSON s zadané vlastnosti | 
|||| 

*Příklad*

Tento příklad přidá `accountNumber` vlastnost, která má `customerProfile` objekt, který je převést na JSON se [JSON()](#json) funkce. Funkce přiřadí hodnotu, která je generován [guid()](#guid) fungovat a vrátí aktualizovaného objektu:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

## <a name="addseconds"></a>Přidat_sekundy

Počet sekund, přidejte do časového razítka.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Sekund*> | Ano | Integer | Kladné a záporné počet sekund, po přidání | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Časové razítko plus zadaném počtu sekund  | 
|||| 

*Příklad 1*

Tento příklad přidá 10 sekund zadaného časového razítka:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

A vrátí výsledek tohoto: `"2018-03-15T00:00:10.0000000Z"`

*Příklad 2*

Tento příklad odečítá od pěti sekund zadaného časového razítka:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

A vrátí výsledek tohoto: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

## <a name="addtotime"></a>addToTime

Počet jednotek přidejte do časového razítka. Viz také [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Interval*> | Ano | Integer | Počet jednotek zadaný čas přidání | 
| <*timeUnit*> | Ano | Řetězec | Jednotka času pro použití s *interval*: "Sekundu", "Minut", "Hodina", "Den", "Týden", "Měsíc", "Rok" | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Časové razítko s přičtením zadaného počtu časové jednotky  | 
|||| 

*Příklad 1*

Tento příklad přidá jeden den zadané časové razítko:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day') 
```

A vrátí výsledek tohoto: `"2018-01-02T00:00:00:0000000Z"`

*Příklad 2*

Tento příklad přidá jeden den zadané časové razítko:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

A vrátí výsledek ve formátu volitelné "D": `"Tuesday, January 2, 2018"`

<a name="and"></a>

## <a name="and"></a>a

Zkontrolujte, zda jsou všechny výrazy hodnotu true. Vrátí hodnotu PRAVDA, pokud jsou všechny výrazy true nebo vrátí hodnotu false v případě, že nejméně jeden výraz je hodnota false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Expression1*>, <*Výraz2*>,... | Ano | Logická hodnota | Výrazy ke kontrole | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| hodnotu true nebo false | Logická hodnota | Vrátí hodnotu PRAVDA, pokud jsou všechny výrazy true. Vrátí hodnotu false, pokud se nejméně jeden výraz je hodnota false. | 
|||| 

*Příklad 1*

Tyto příklady zkontrolujte, zda jsou splněny všechny zadané logické hodnoty:

```
and(true, true)
and(false, true)
and(false, false)
```

A vrátí tyto výsledky:

* Prvním příkladu: oba výrazy hodnotu true, takže vrátí `true`. 
* Druhý příklad: jeden výraz je hodnota false, takže vrátí `false`.
* Třetí příklad: oba výrazy jsou false, takže vrátí `false`.

*Příklad 2*

Tyto příklady zkontrolujte, zda jsou splněny všechny zadaných výrazů:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

A vrátí tyto výsledky:

* Prvním příkladu: oba výrazy hodnotu true, takže vrátí `true`. 
* Druhý příklad: jeden výraz je hodnota false, takže vrátí `false`.
* Třetí příklad: oba výrazy jsou false, takže vrátí `false`.

<a name="array"></a>

## <a name="array"></a>pole

Vrátí pole z jedné zadaný vstup. Více vstupů, najdete v části [createArray()](#createArray). 

```
array('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec pro vytváření pole | 
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

A vrátí výsledek tohoto: `["hello"]`

<a name="base64"></a>

## <a name="base64"></a>formátu Base64.

Vrátí verzi kódováním base64 řetězce.

```
base64('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Vstupní řetězec | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*řetězec formátu Base64.*> | Řetězec | Verze kódováním base64 pro vstupní řetězec | 
|||| 

*Příklad*

Tento příklad převede na řetězec s kódováním base64, pomocí řetězce "hello":

```
base64('hello')
```

A vrátí výsledek tohoto: `"aGVsbG8="`

<a name="base64ToBinary"></a>

## <a name="base64tobinary"></a>base64ToBinary

Vrátí binární verze pro řetězec s kódováním base64.

```
base64ToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec s kódováním base64, pomocí k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární pro base64 řetězec*> | Řetězec | Binární verze pro řetězec s kódováním base64 | 
|||| 

*Příklad*

Tento příklad převede "aGVsbG8 =" kódováním base64 řetězec binárního řetězce:

```
base64ToBinary('aGVsbG8=')
```

A vrátí výsledek tohoto: 

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

## <a name="base64tostring"></a>base64ToString

Vrátí řetězec verze pro řetězec s kódováním base64, efektivně dekódování řetězec base64. Tuto funkci můžete používat místo [decodeBase64()](#decodeBase64). I když obě funkce fungovat stejným způsobem, `base64ToString()` upřednostňuje.

```
base64ToString('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec s kódováním base64, pomocí funkce Decode | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*dekódovat řetězec base64*> | Řetězec | Řetězec verze pro řetězec s kódováním base64 | 
|||| 

*Příklad*

Tento příklad převede "aGVsbG8 =" kódováním base64 řetězec právě řetězec:

```
base64ToString('aGVsbG8=')
```

A vrátí výsledek tohoto: `"hello"`

<a name="binary"></a>

## <a name="binary"></a>Binární 

Vrátí binární verze pro řetězec.

```
binary('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární pro vstupní hodnota*> | Řetězec | Binární verze pro zadaný řetězec | 
|||| 

*Příklad*

Tento příklad převede text "hello" na binární řetězec:

```
binary('hello')
```

A vrátí výsledek tohoto: 

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

## <a name="body"></a>hlavní část

Vrátí akci `body` výstup za běhu. Sdružená vlastnost `actions('<actionName>').outputs.body`. V tématu [actionBody()](#actionBody) a [actions()](#actions).

```
body('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Název akce `body` výstupu, který chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | -----| ----------- | 
| <*výstup textu akce*> | Řetězec | `body` Výstup z zadanou akci | 
|||| 

*Příklad*

Tento příklad načte `body` výstup z `Get user` Twitter akce: 

```
body('Get_user')
```

A vrátí výsledek tohoto: 

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

Vrátí logickou verze pro hodnotu.

```
bool(<value>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Všechny | Hodnota k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false | Logická hodnota | Logická hodnota verze pro zadanou hodnotu | 
|||| 

*Příklad*

Tyto příklady Převést zadané hodnoty logické hodnoty: 

```
bool(1)
bool(0)
```

A vrátí tyto výsledky: 

* Prvním příkladu: `true` 
* Druhém příkladu: `false`

<a name="coalesce"></a>

## <a name="coalesce"></a>sloučení

Vrátí první hodnotu než null z jednoho nebo více parametrů. Prázdné řetězce, prázdné pole a prázdné objekty nejsou null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*object_1*>, <*object_2*>,... | Ano | Žádné, můžete kombinovat typy | Jednu nebo více položek, které chcete vyhledat hodnotu null | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*první bez--položku null*> | Všechny | První položka nebo hodnotu, která není null. Všechny parametry mají hodnotu null, funkce vrátí hodnotu null. | 
|||| 

*Příklad*

Tyto příklady vrátí první hodnotu než null ze zadané hodnoty, nebo hodnota null, když jsou všechny hodnoty null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

A vrátí tyto výsledky: 

* Prvním příkladu: `true` 
* Druhém příkladu: `"hello"`
* Třetí příklad: `null`

<a name="concat"></a>

## <a name="concat"></a>concat

Kombinací dvou nebo více řetězců a vrátí součet řetězec. 

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text1*>, <*text2*>,... | Ano | Řetězec | Alespoň dva řetězce kombinovat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*text1text2...*> | Řetězec | Řetězec vytvořený z kombinované vstupní řetězců | 
|||| 

*Příklad*

Tento příklad kombinuje řetězce "Hello" a "World":

```
concat('Hello', 'World')
```

A vrátí výsledek tohoto: `"HelloWorld"`

<a name="contains"></a>

## <a name="contains"></a>obsahuje

Zkontrolujte, zda kolekce obsahuje konkrétní položku. Vrátí hodnotu PRAVDA, pokud byla položka nalezena, nebo vrátí hodnotu false, pokud nebyl nalezen. Tato funkce je malá a velká písmena.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Konkrétně tato funkce funguje na tyto typy kolekcí: 

* A *řetězec* najít *dílčí řetězec*
* *Pole* najít *hodnota*
* A *slovník* najít *klíč*

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*kolekce*> | Ano | Řetězec, pole nebo slovník | Kolekce ke kontrole | 
| <*Hodnota*> | Ano | Řetězec, pole nebo adresář, v uvedeném pořadí | Položka k vyhledání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false | Logická hodnota | Vrátí hodnotu PRAVDA, pokud byla položka nalezena. Vrátí hodnotu false při nebyl nalezen. |
|||| 

*Příklad 1*

Tento příklad zkontroluje text "hello, world" pro "world" podřetězec a vrátí hodnotu true:

```
contains('hello world', 'world')
```

*Příklad 2*

Tento příklad zkontroluje text "hello, world" pro dílčí řetězec "universe" a vrátí hodnotu false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

## <a name="convertfromutc"></a>convertFromUtc

Převeďte časové razítko z koordinovaný univerzální čas (UTC) na časové pásmo cíl.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*destinationTimeZone*> | Ano | Řetězec | Název pro časové pásmo cíl. Další informace najdete v tématu [časové pásmo ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*převést na časové razítko*> | Řetězec | Časové razítko převést na časové pásmo cíl | 
|||| 

*Příklad 1*

Tento příklad převede časové razítko pro zadané časové pásmo: 

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

A vrátí výsledek tohoto: `"2018-01-01T00:00:00.0000000"`

*Příklad 2*

Tento příklad převede na zadané časové pásmo a formát časového razítka:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

A vrátí výsledek tohoto: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

## <a name="converttimezone"></a>convertTimeZone

Převeďte časovým razítkem ze zdroje časové pásmo na cílové časové pásmo.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*sourceTimeZone*> | Ano | Řetězec | Název pro časové pásmo zdroje. Další informace najdete v tématu [časové pásmo ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*destinationTimeZone*> | Ano | Řetězec | Název pro časové pásmo cíl. Další informace najdete v tématu [časové pásmo ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*převést na časové razítko*> | Řetězec | Časové razítko převést na časové pásmo cíl | 
|||| 

*Příklad 1*

Tento příklad převede časové pásmo zdrojového na cílový časové pásmo: 

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

A vrátí výsledek tohoto: `"2018-01-01T00:00:00.0000000"`

*Příklad 2*

Tento příklad převede časové pásmo pro zadané časové pásmo a formát:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

A vrátí výsledek tohoto: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

## <a name="converttoutc"></a>convertToUtc

Převeďte časové razítko o časovém pásmu zdroj koordinovaný univerzální čas (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*sourceTimeZone*> | Ano | Řetězec | Název pro časové pásmo zdroje. Další informace najdete v tématu [časové pásmo ID](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*převést na časové razítko*> | Řetězec | Časové razítko převedeny na UTC | 
|||| 

*Příklad 1*

Tento příklad převede na UTC časového razítka: 

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

A vrátí výsledek tohoto: `"2018-01-01T08:00:00.0000000Z"`

*Příklad 2*

Tento příklad převede na UTC časového razítka:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

A vrátí výsledek tohoto: `"Monday, January 1, 2018"`

<a name="createArray"></a>

## <a name="createarray"></a>createArray

Vrátí pole z více vstupů. Pro jeden vstupní pole, najdete v části [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*1*>, <*object2*>,... | Ano | Žádné, ale není ve smíšeném | Minimálně dvě položky vytvořte pole | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*1*>, <*object2*>,...] | Pole | Pole vytvořené z všechny vstupní položky | 
|||| 

*Příklad*

Tento příklad vytvoří pole z těchto vstupy:

```
createArray('h', 'e', 'l', 'l', 'o')
```

A vrátí výsledek tohoto: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

## <a name="datauri"></a>dataUri

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
| <*data-uri*> | Řetězec | Vstupní řetězec identifikátor URI dat. | 
|||| 

*Příklad*

Tento příklad vytvoří identifikátor URI dat. pro řetězec "hello":

```
dataUri('hello') 
```

A vrátí výsledek tohoto: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

## <a name="datauritobinary"></a>dataUriToBinary

Vrátí binární verze pro identifikátor URI dat (URI). Tuto funkci můžete používat místo [decodeDataUri()](#decodeDataUri). I když obě funkce fungovat stejným způsobem, `decodeDataUri()` upřednostňuje.

```
dataUriToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Identifikátor URI k převedení dat. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární pro data-uri*> | Řetězec | Binární verze pro identifikátor URI dat. | 
|||| 

*Příklad*

Tento příklad vytvoří binární verze pro tato data URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí výsledek tohoto: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

## <a name="datauritostring"></a>dataUriToString

Vrátí řetězec verze pro identifikátor URI dat (URI).

```
dataUriToString('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Identifikátor URI k převedení dat. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*řetězec pro data identifikátor uri*> | Řetězec | Řetězec verze pro identifikátor URI dat. | 
|||| 

*Příklad*

Tento příklad vytvoří řetězec pro tato data URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí výsledek tohoto: `"hello"`

<a name="dayOfMonth"></a>

## <a name="dayofmonth"></a>DayOfMonth

Vrátí den v měsíci od časového razítka. 

```
dayOfMonth('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*den měsíce*> | Integer | Den v měsíci ze zadaného časového razítka | 
|||| 

*Příklad*

Tento příklad vrátí číslo dne v měsíci z této časové razítko:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

A vrátí výsledek tohoto: `15`

<a name="dayOfWeek"></a>

## <a name="dayofweek"></a>DayOfWeek

Vrátí den v týdnu z časového razítka.  

```
dayOfWeek('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*den v týdnu*> | Integer | Zadaný den v týdnu z zadané časové razítko, které je neděle 0, pondělí je 1 a tak dále | 
|||| 

*Příklad*

Tento příklad vrátí číslo dne v týdnu z této časové razítko:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

A vrátí výsledek tohoto: `3`

<a name="dayOfYear"></a>

## <a name="dayofyear"></a>DayOfYear

Vrátí den v roce od časového razítka. 

```
dayOfYear('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*den v roce*> | Integer | Den v roce ze zadaného časového razítka | 
|||| 

*Příklad*

Tento příklad vrátí počet den v roce z této časové razítko:

```
dayOfYear('2018-03-15T13:27:36Z')
```

A vrátí výsledek tohoto: `74`

<a name="decodeBase64"></a>

## <a name="decodebase64"></a>decodeBase64

Vrátí řetězec verze pro řetězec s kódováním base64, efektivně dekódování řetězec base64. Zvažte použití [base64ToString()](#base64ToString) místo `decodeBase64()`. I když obě funkce fungovat stejným způsobem, `base64ToString()` upřednostňuje.

```
decodeBase64('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec s kódováním base64, pomocí funkce Decode | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*dekódovat řetězec base64*> | Řetězec | Řetězec verze pro řetězec s kódováním base64 | 
|||| 

*Příklad*

Tento příklad vytvoří řetězec s kódováním base64, pomocí řetězce:

```
decodeBase64('aGVsbG8=')
```

A vrátí výsledek tohoto: `"hello"`

<a name="decodeDataUri"></a>

## <a name="decodedatauri"></a>decodeDataUri

Vrátí binární verze pro identifikátor URI dat (URI). Zvažte použití [dataUriToBinary()](#dataUriToBinary), místo `decodeDataUri()`. I když obě funkce fungovat stejným způsobem, `dataUriToBinary()` upřednostňuje.

```
decodeDataUri('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec identifikátoru URI pro dekódování dat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární pro data-uri*> | Řetězec | Binární verze pro data řetězce URI | 
|||| 

*Příklad*

Tento příklad vrací binární verze pro tato data URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí výsledek tohoto: 

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

## <a name="decodeuricomponent"></a>decodeuricomponent –

Vrátí řetězec, nahradí řídicí znaky s dekódované verze. 

```
decodeUriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec s řídicími znaky dekódování | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*dekódovat uri*> | Řetězec | Aktualizované řetězec s dekódované řídicí znaky | 
|||| 

*Příklad*

Tento příklad řídicí znaky v tomto řetězci nahradí dekódované verze:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

A vrátí výsledek tohoto: `"https://contoso.com"`

<a name="div"></a>

## <a name="div"></a>div

Vrátí výsledek v celočíselné dělení dvou čísel. Výsledek zbývající najdete [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*dělenec*> | Ano | Celé číslo nebo číslo Float | Číslo, které má dělit *dělitel* | 
| <*dělitel*> | Ano | Celé číslo nebo číslo Float | Číslo, které rozdělí *dělenec*, ale nemůže být 0. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výsledek koeficient*> | Integer | Celé číslo výsledek dělení první číslo druhé číslo | 
|||| 

*Příklad*

Oba příklady dělení první číslo druhé číslo:

```
div(10, 5)
div(11, 5)
```

A vrátí výsledek tohoto: `2`

<a name="encodeUriComponent"></a>

## <a name="encodeuricomponent"></a>encodeuricomponent –

Vrácení verze identifikátor URI kódovaný URI pro řetězec nahrazením znaky adresy URL unsafe řídicí znaky. Zvažte použití [uriComponent()](#uriComponent), místo `encodeUriComponent()`. I když obě funkce fungovat stejným způsobem, `uriComponent()` upřednostňuje.

```
encodeUriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec převést na formátu s kódováním identifikátor URI | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*kódovaný identifikátor uri*> | Řetězec | Řetězec kódovaný identifikátor URI s řídicí znaky | 
|||| 

*Příklad*

Tento příklad vytvoří verzi kódovaný identifikátor URI pro tento řetězec:

```
encodeUriComponent('https://contoso.com')
```

A vrátí výsledek tohoto: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

## <a name="empty"></a>prázdný

Zkontrolujte, zda kolekce je prázdný. Vrátí hodnotu PRAVDA, pokud kolekce je prázdná nebo vrátí hodnotu NEPRAVDA, pokud není prázdný.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*kolekce*> | Ano | Řetězec, pole nebo objekt | Kolekce ke kontrole | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false | Logická hodnota | Vrátí hodnotu PRAVDA, pokud kolekce je prázdná. Vrátí hodnotu NEPRAVDA, pokud není prázdný. | 
|||| 

*Příklad* 

Tyto příklady zkontrolujte, zda jsou prázdné k zadaným kolekcím:

```
empty('')
empty('abc')
```

A vrátí tyto výsledky: 

* Prvním příkladu: předá řetězec prázdný, funkce vrátí hodnotu `true`. 
* Druhý příklad: předá řetězec "abc", proto funkce vrátí hodnotu `false`. 

<a name="endswith"></a>

## <a name="endswith"></a>endsWith

Zkontrolujte, zda řetězec končí konkrétní dílčí řetězec. Vrátí hodnotu PRAVDA, když se najde dílčí řetězec, nebo vrátí hodnotu false, pokud nebyl nalezen. Tato funkce není malá a velká písmena.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který má zkontrolovat | 
| <*Prohledávanýtext*> | Ano | Řetězec | Koncová dílčí řetězec k vyhledání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false  | Logická hodnota | Vrátí hodnotu PRAVDA, když se najde koncová dílčí řetězec. Vrátí hodnotu false při nebyl nalezen. | 
|||| 

*Příklad 1* 

Tento příklad zkontroluje, zda text "hello, world" končí textem "world" řetězec:

```
endsWith('hello world', 'world')
```

A vrátí výsledek tohoto: `true`

*Příklad 2*

Tento příklad zkontroluje, zda text "hello, world" končí textem "universe" řetězec:

```
endsWith('hello world', 'universe')
```

A vrátí výsledek tohoto: `false`

<a name="equals"></a>

## <a name="equals"></a>rovná se

Zkontrolujte, zda jsou hodnoty, výrazy nebo objekty ekvivalentní. Vrátí hodnotu PRAVDA, jak jsou ekvivalentní, nebo vrátí hodnotu false v případě, že není ekvivalentní.

```
equals('<object1>', '<object2>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*1*>, <*object2*> | Ano | Různé | Hodnoty, výrazy nebo objekty k porovnání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false | Logická hodnota | Vrátí hodnotu PRAVDA, pokud jsou obě ekvivalentní. Vrátí hodnotu NEPRAVDA, pokud není ekvivalentní. | 
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda jsou zadané vstupy ekvivalentní. 

```
equals(true, 1)
equals('abc', 'abcd')
```

A vrátí tyto výsledky: 

* Prvním příkladu: obě hodnoty jsou ekvivalentní, proto funkce vrátí hodnotu `true`.
* Druhý exmaple: obě hodnoty nejsou ekvivalentní, funkce vrátí hodnotu `false`.

<a name="first"></a>

## <a name="first"></a>první

Vrátí první položka z řetězec nebo pole.

```
first('<collection>')
first([<collection>])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*kolekce*> | Ano | Řetězec nebo pole | Kolekce kde najít první položka |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*první položku kolekce.*> | Všechny | První položka v kolekci | 
|||| 

*Příklad*

Tyto příklady najít první položky v těchto kolekcích:

```
first('hello')
first([0, 1, 2])
```

A tyto výsledky: 

* Prvním příkladu: `"h"`
* Druhý exmaple: `0`

<a name="float"></a>

## <a name="float"></a>Plovoucí desetinná čárka

Převeďte řetězec verze pro číslo s plovoucí desetinnou čárkou skutečné číslo s plovoucí desetinnou čárkou. Tuto funkci můžete použít jenom v případě, že předávání vlastních parametrů do aplikace, jako je například aplikace logiky.

```
float('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec, který se má převést platné číslo s plovoucí desetinnou čárkou |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*float – hodnota*> | Plovoucí desetinná čárka | Číslo s plovoucí desetinnou čárkou pro zadaný řetězec | 
|||| 

*Příklad*

Tento příklad vytvoří řetězec verze pro toto číslo s plovoucí desetinnou čárkou:

```
float('10.333')
```

A vrátí výsledek tohoto: `10.333`

<a name="formatDateTime"></a>

## <a name="formatdatetime"></a>FormatDateTime

Vrátí časovým razítkem v zadaném formátu.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*naformátována časové razítko*> | Řetězec | Aktualizované časové razítko v zadaném formátu. | 
|||| 

*Příklad*

Tento příklad převede na zadaný formát časového razítka:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

A vrátí výsledek tohoto: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

## <a name="formdatamultivalues"></a>formDataMultiValues

Vrátí pole s hodnotami, které odpovídají názvu klíče při akci *data formuláře* nebo *formulářem kódované* výstup. 

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Akce, jejíž výstup obsahuje požadovanou hodnotu klíče | 
| <*Klíč*> | Ano | Řetězec | Název klíče, jehož hodnotu chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*pole s klíč hodnoty*>] | Pole | Pole s všechny hodnoty, které odpovídají zadaný klíč | 
|||| 

*Příklad* 

Tento příklad vytvoří pole z hodnoty "Předmět" klíč data formuláře na zadanou akci nebo výsledek zakódovaný ve formátu:  

```
formDataMultiValues('Send_an_email', 'Subject')
```

A vrátí subjektu textové pole, například: `["Hello world"]`

<a name="formDataValue"></a>

## <a name="formdatavalue"></a>formDataValue

Vrátí jednu hodnotu, která odpovídá názvu klíče při akci *data formuláře* nebo *formulářem kódované* výstup. Pokud funkce najde více než jednu shodu, funkce vyvolá chybu.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název akce*> | Ano | Řetězec | Akce, jejíž výstup obsahuje požadovanou hodnotu klíče | 
| <*Klíč*> | Ano | Řetězec | Název klíče, jehož hodnotu chcete |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*klíč hodnota*> | Řetězec | Hodnota v zadaný klíč  | 
|||| 

*Příklad* 

Tento příklad vytvoří řetězec z hodnoty "Předmět" klíč data formuláře na zadanou akci nebo výsledek zakódovaný ve formátu:  

```
formDataValue('Send_an_email', 'Subject')
```

A vrátí text předmětu jako řetězec, například: `"Hello world"`

<a name="getFutureTime"></a>

## <a name="getfuturetime"></a>getFutureTime

Vrátí aktuální časové razítko plus jednotky zadané doby.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Interval*> | Ano | Integer | Počet jednotek zadaný čas má odečíst | 
| <*timeUnit*> | Ano | Řetězec | Jednotka času pro použití s *interval*: "Sekundu", "Minut", "Hodina", "Den", "Týden", "Měsíc", "Rok" | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Aktuální časové razítko s přičtením zadaného počtu časové jednotky | 
|||| 

*Příklad 1*

Předpokládejme, že je aktuální časové razítko "2018-03-01T00:00:00.0000000Z". Tento příklad přidá pět dní tohoto časového razítka:

```
getFutureTime(5, 'Day')
```

A vrátí výsledek tohoto: `"2018-03-06T00:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že je aktuální časové razítko "2018-03-01T00:00:00.0000000Z". Tento příklad přidá pět dní a převádí výsledek do formátu "D":

```
getFutureTime(5, 'Day', 'D')
```

A vrátí výsledek tohoto: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

## <a name="getpasttime"></a>getPastTime

Vrátí aktuální časové razítko minus jednotky zadané doby.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Interval*> | Ano | Integer | Počet jednotek zadaný čas má odečíst | 
| <*timeUnit*> | Ano | Řetězec | Jednotka času pro použití s *interval*: "Sekundu", "Minut", "Hodina", "Den", "Týden", "Měsíc", "Rok" | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Aktuální časové razítko minus zadaný počet časových jednotek | 
|||| 

*Příklad 1*

Předpokládejme, že je aktuální časové razítko "2018-02-01T00:00:00.0000000Z". Tento příklad odečítá pět dní od tohoto časového razítka:

```
getPastTime(5, 'Day')
```

A vrátí výsledek tohoto: `"2018-01-27T00:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že je aktuální časové razítko "2018-02-01T00:00:00.0000000Z". Tento příklad odečítá pět dní a převádí výsledek do formátu "D":

```
getPastTime(5, 'Day', 'D')
```

A vrátí výsledek tohoto: `"Saturday, January 27, 2018"`

<a name="greater"></a>

## <a name="greater"></a>větší

Zkontrolujte, zda je první hodnota je větší než druhá hodnota. Vrátí hodnotu PRAVDA, pokud je první hodnota další nebo vrátí hodnotu false, kdy menší.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Celé číslo, Float nebo řetězec | První hodnota ke kontrole, zda je větší než druhá hodnota | 
| <*CompareTo*> | Ano | Celé číslo, Float nebo řetězec, v uvedeném pořadí | Hodnotu porovnání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false | Logická hodnota | Vrátí hodnotu PRAVDA, pokud je první hodnota větší než druhá hodnota. Vrátí hodnotu false, pokud první hodnota je rovna nebo menší než druhá hodnota. | 
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda je první hodnota je větší než druhá hodnota:

```
greater(10, 5)
greater('apple', 'banana')
```

A tyto výsledky: 

* Prvním příkladu: `true`
* Druhém příkladu: `false`

<a name="greaterOrEquals"></a>

## <a name="greaterorequals"></a>greaterOrEquals

Zkontrolujte, zda je první hodnota je větší než nebo rovna hodnotě druhá hodnota.
Vrátí hodnotu PRAVDA, pokud je první hodnota větší nebo rovna nebo vrátí hodnotu false v případě, že první hodnota je menší.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Celé číslo, Float nebo řetězec | První hodnota ke kontrole, zda je větší než nebo rovna hodnotě druhá hodnota | 
| <*CompareTo*> | Ano | Celé číslo, Float nebo řetězec, v uvedeném pořadí | Hodnotu porovnání | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false | Logická hodnota | Vrátí hodnotu PRAVDA, pokud je první hodnota větší než nebo rovna hodnotě druhá hodnota. Vrátí hodnotu NEPRAVDA, pokud první hodnota je menší než druhá hodnota. | 
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda je první hodnota je větší nebo rovna než druhá hodnota:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

A tyto výsledky: 

* Prvním příkladu: `true`
* Druhém příkladu: `false`

<a name="guid"></a>

## <a name="guid"></a>Identifikátor GUID

Generovat globálně jedinečný identifikátor (GUID) jako řetězec, například "c2ecc88d-88c8-4096-912c-d6f2e2b138ce": 

```
guid()
```

Také můžete zadat jiný formát identifikátoru GUID než výchozí formát, "D", což je 32 znaků oddělených pomlčkami.

```
guid('<format>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Formát*> | Ne | Řetězec | Jediný [formátovací řetězec](https://msdn.microsoft.com/library/97af8hh4) vrácený identifikátoru GUID. Ve výchozím nastavení formát je "D", ale můžete použít "N", "D", "B", "P" nebo "X". | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota identifikátoru GUID*> | Řetězec | Náhodně generovaný identifikátor GUID | 
|||| 

*Příklad* 

Tento příklad vytvoří stejný identifikátor GUID, ale jako 32 znaků oddělených pomlčkami a uzavřený v závorkách: 

```
guid('P')
```

A vrátí výsledek tohoto: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

## <a name="if"></a>if

Zkontrolujte, zda je výraz true nebo false. Na základě výsledku, vrátí zadanou hodnotu.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*výraz*> | Ano | Logická hodnota | Výraz, který se kontrola | 
| <*valueIfTrue*> | Ano | Všechny | Hodnota, která má vrácené v případě, kdy je výraz pravdivý | 
| <*valueIfFalse*> | Ano | Všechny | Hodnota, která má vracet výraz je hodnota false | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Zadaná hodnota vrátit*> | Všechny | Zadaná hodnota, která vrátí závislosti na tom, zda výraz hodnotu true nebo false | 
|||| 

*Příklad* 

Tento příklad vrací `"yes"` vzhledem k tomu, že zadaný výraz vrací hodnotu true. Jinak se vrátí v příkladu `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

## <a name="indexof"></a>indexOf

Vrátí počáteční pozici nebo hodnotu indexu pro dílčí řetězec. Tato funkce není velká a malá písmena a indexy, které začínají číslem 0. 

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
| <*Hodnota indexu*>| Integer | Počáteční pozice nebo index hodnotu pro určený dílčí řetězec. <p>Pokud řetězec není nalezeno, vrátí číslo -1. </br>Pokud je řetězec prázdný, vrátí se číslo 0. | 
|||| 

*Příklad* 

Tento příklad vyhledá počáteční hodnoty indexu pro dílčí řetězec "world" v řetězci "hello, world":

```
indexOf('hello world', 'world')
```

A vrátí výsledek tohoto: `6`

<a name="int"></a>

## <a name="int"></a>celá čísla

Vrátí celé číslo verze pro řetězec.

```
int('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*celé číslo*> | Integer | Celé číslo verze pro zadaný řetězec | 
|||| 

*Příklad* 

Tento příklad vytvoří je celé číslo verze pro řetězec "10":

```
int('10')
```

A vrátí výsledek tohoto: `10`

<a name="item"></a>

## <a name="item"></a>Položka

Při použití uvnitř opakující se akce přes pole, vrátí aktuální položky v poli během aktuální opakování akce. Můžete také získat hodnoty z vlastnosti této položky. 

```
item()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktuální položku pole*> | Všechny | Aktuální položky v poli pro akce aktuální iterace | 
|||| 

*Příklad* 

Tento příklad načte `body` element z aktuální zprávu pro akci "Send_an_email" uvnitř aktuální iterace pro každou smyčku:

```
item().body
```

<a name="items"></a>

## <a name="items"></a>položek

Vrátí aktuální položky z jednotlivých cyklů ve smyčce pro každý. Pomocí této funkce uvnitř pro každou smyčku.

```
items('<loopName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Ano | Řetězec | Název pro každou smyčky | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Položka*> | Všechny | Položku z aktuální cyklu v zadané pro každou smyčku | 
|||| 

*Příklad* 

Tento příklad získá aktuální položku ze zadané pro každou smyčku:

```
items('myForEachLoopName')
```

<a name="json"></a>

## <a name="json"></a>JSON

Vrátí hodnotu typu JavaScript Object Notation (JSON) nebo objekt pro řetězec nebo XML.

```
json('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec nebo XML | Řetězec nebo XML, který má být převeden | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Výsledek formátu JSON*> | Nativní typu JSON nebo objektu | Hodnota JSON nativní typ nebo objekt pro zadaný řetězec nebo XML. Pokud má řetězec hodnotu null, funkce vrátí prázdný objekt. | 
|||| 

*Příklad 1* 

Tento příklad převede tento řetězec na hodnotu JSON:

```
json('[1, 2, 3]')
```

A vrátí výsledek tohoto: `[1, 2, 3]`

*Příklad 2*

Tento příklad převede tento řetězec do formátu JSON: 

```
json('{"fullName": "Sophia Owen"}')
```

A vrátí výsledek tohoto:

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

A vrátí výsledek tohoto:

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

## <a name="intersection"></a>průnik

Vrátí kolekci, která má *pouze* společné položky napříč k zadaným kolekcím. Než se objeví ve výsledku, musí být uvedena položky ve všech kolekcích této funkci byl předán. Pokud jeden nebo více položek se stejným názvem, zobrazí se ve výsledku poslední položky s tímto názvem.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>,... | Ano | Pole nebo objekt, ale ne obojí | Kolekce, ze kterého má *pouze* společné položky | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*běžné položky*> | Objekt, nebo pole v uvedeném pořadí | Kolekce, která má pouze společné položky napříč k zadaným kolekcím | 
|||| 

*Příklad* 

Tento příklad vyhledá společné položky mezi těmito poli:  

```
intersection([1, 2, 3], [101, 2, 1, 10], [6, 8, 1, 2])
```

A vrátí pole s *pouze* tyto položky: `[1, 2]`

<a name="join"></a>

## <a name="join"></a>join

Vrátí řetězec, který má všechny položky z pole a každý znak oddělených *oddělovač*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*kolekce*> | Ano | Pole | Pole, které má položky, které chcete připojit |  
| <*Oddělovač*> | Ano | Řetězec | Oddělovač, který se zobrazí mezi každý znak ve výsledném řetězci | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*char1*><*oddělovač*><*char2*><*oddělovač*>... | Řetězec | Výsledný řetězec vytvořený z všechny položky v zadaném poli |
|||| 

*Příklad* 

Tento příklad vytvoří řetězec z všechny položky v toto pole s zadaný znak jako oddělovač, který:

```
join([a, b, c], '.')
```

A vrátí výsledek tohoto: `"a.b.c"`

<a name="last"></a>

## <a name="last"></a>poslední

Vrátí poslední položky z kolekce.

```
last('<collection>')
last([<collection>])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*kolekce*> | Ano | Řetězec nebo pole | Kolekce kde najít poslední položky | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*poslední položku kolekce.*> | Řetězec nebo pole, v uvedeném pořadí | Poslední položky v kolekci | 
|||| 

*Příklad* 

Tyto příklady najít poslední položky v těchto kolekcích:

```
last('abcd')
last([0, 1, 2, 3])
```

A vrátí tyto výsledky: 

* Prvním příkladu: `"d"`
* Druhém příkladu: `3`

<a name="lastindexof"></a>

## <a name="lastindexof"></a>lastIndexOf

Vrátí hodnotu koncovou pozici nebo index pro dílčí řetězec. Tato funkce není velká a malá písmena a indexy, které začínají číslem 0.

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
| <*ukončování hodnotu indexu*> | Integer | Koncová pozice nebo index Hodnota určený dílčí řetězec. <p>Pokud řetězec není nalezeno, vrátí číslo -1. </br>Pokud je řetězec prázdný, vrátí se číslo 0. | 
|||| 

*Příklad* 

Tento příklad vyhledá koncovou hodnotu indexu pro dílčí řetězec "world" v řetězci "hello, world":

```
lastIndexOf('hello world', 'world')
```

A vrátí výsledek tohoto: `10`

<a name="length"></a>

## <a name="length"></a>Délka

Vrátí počet položek v kolekci.

```
length('<collection>')
length([<collection>])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*kolekce*> | Ano | Řetězec nebo pole | Kolekce s položkami počítat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Délka – počet*> | Integer | Počet položek v kolekci | 
|||| 

*Příklad*

Tyto příklady určený počet položek v těchto kolekcích: 

```
length('abcd')
length([0, 1, 2, 3])
```

A vrátí výsledek tohoto: `4`

<a name="less"></a>

## <a name="less"></a>menší

Zkontrolujte, zda je první hodnota menší než druhá hodnota.
Vrátí hodnotu PRAVDA, pokud je první hodnota menší nebo vrátí hodnotu false v případě, že první hodnota je další.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Celé číslo, Float nebo řetězec | První hodnota ke kontrole, jestli menší než druhá hodnota | 
| <*CompareTo*> | Ano | Celé číslo, Float nebo řetězec, v uvedeném pořadí | Porovnání položek | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false | Logická hodnota | Vrátí hodnotu PRAVDA, pokud první hodnota je menší než druhá hodnota. Vrátí hodnotu false, pokud první hodnota je rovna nebo větší než druhá hodnota. | 
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda je první hodnota menší než druhá hodnota.

```
less(5, 10)
less('banana', 'apple')
```

A tyto výsledky: 

* Prvním příkladu: `true`
* Druhém příkladu: `false`

<a name="lessOrEquals"></a>

## <a name="lessorequals"></a>lessOrEquals

Zkontrolujte, zda je první hodnota menší než nebo rovna hodnotě druhá hodnota.
Vrátí hodnotu PRAVDA, pokud je první hodnota menší než nebo rovna nebo vrátí hodnotu false v případě, že první hodnota je další.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Celé číslo, Float nebo řetězec | První hodnota ke kontrole, jestli menší než nebo rovno druhá hodnota | 
| <*CompareTo*> | Ano | Celé číslo, Float nebo řetězec, v uvedeném pořadí | Porovnání položek | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false  | Logická hodnota | Vrátí hodnotu PRAVDA, pokud je první hodnota menší než nebo rovna hodnotě druhá hodnota. Vrátí hodnotu false, pokud první hodnota je větší než druhá hodnota. |  
|||| 

*Příklad*

Tyto příklady zkontrolujte, zda se první hodnota je menší nebo rovna než druhá hodnota.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

A tyto výsledky: 

* Prvním příkladu: `true`
* Druhém příkladu: `false`

<a name="listCallbackUrl"></a>

## <a name="listcallbackurl"></a>listCallbackUrl

Vrátí "Zpětného volání adresu" který volá aktivační události nebo akce. Tato funkce pracuje pouze s triggery a akce pro **HttpWebhook** a **ApiConnectionWebhook** konektor typy, ale ne **ruční**,  **Opakování**, **HTTP**, a **APIConnection** typy. 

```
listCallbackUrl()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*zpětné volání – adresa URL*> | Řetězec | Adresu URL zpětné volání pro aktivační události nebo akci |  
|||| 

*Příklad*

Tento příklad ukazuje adresu URL ukázkových zpětného volání, že tato funkce může vrátit:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

## <a name="max"></a>max

Vrátí nejvyšší hodnotu ze seznamu nebo pole s čísla, která je na obou koncích (včetně). 

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Číslo1*>, <*číslo2*>,... | Ano | Celé číslo, Float nebo obojí | Sadu čísel, ze kterého chcete nejvyšší hodnotou | 
| [<*Číslo1*>, <*číslo2*>,...] | Ano | Pole - celé číslo, Float nebo obojí | Pole čísla, ze kterého se má nejvyšší hodnotou | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota maximálního počtu*> | Celé číslo nebo číslo Float | Nejvyšší hodnotu v zadané pole nebo sadu čísel | 
|||| 

*Příklad* 

Tyto příklady získat nejvyšší hodnotu ze sady čísel a pole:

```
max(1, 2, 3)
max([1, 2, 3])
```

A vrátí výsledek tohoto: `3`

<a name="min"></a>

## <a name="min"></a>min

Vrátí nejnižší hodnotu ze sady čísla nebo pole.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Číslo1*>, <*číslo2*>,... | Ano | Celé číslo, Float nebo obojí | Sada čísla, ze kterého se má nejnižší hodnotu | 
| [<*Číslo1*>, <*číslo2*>,...] | Ano | Pole - celé číslo, Float nebo obojí | Pole čísla, ze kterého se má nejnižší hodnotu | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*minimální hodnota*> | Celé číslo nebo číslo Float | Nejnižší hodnotu v zadané sadě čísla nebo zadané pole | 
|||| 

*Příklad* 

Tyto příklady získat nejnižší hodnotu v sadě čísla a pole:

```
min(1, 2, 3)
min([1, 2, 3])
```

A vrátí výsledek tohoto: `1`

<a name="mod"></a>

## <a name="mod"></a>MOD

Vrátí zbytek dělení dvou čísel. Pokud chcete získat celé číslo, najdete v části [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*dělenec*> | Ano | Celé číslo nebo číslo Float | Číslo, které má dělit *dělitel* | 
| <*dělitel*> | Ano | Celé číslo nebo číslo Float | Číslo, které rozdělí *dělenec*, ale nemůže být 0. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*modulo výsledek*> | Celé číslo nebo číslo Float | Zbývající dělení první číslo druhé číslo | 
|||| 

*Příklad* 

Tento příklad rozdělí prvního čísla druhé číslo:

```
mod(3, 2)
```

A vrátí výsledek tohoto: `1`

<a name="mul"></a>

## <a name="mul"></a>mul

Vrátí produktu z vynásobením dvou čísel.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*multiplicand1*> | Ano | Celé číslo nebo číslo Float | Číslo, které má vynásobit *multiplicand2* | 
| <*multiplicand2*> | Ano | Celé číslo nebo číslo Float | Číslo, násobky *multiplicand1* | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výsledek produktu*> | Celé číslo nebo číslo Float | Produkt ze vynásobením prvního čísla druhé číslo | 
|||| 

*Příklad* 

Tyto příklady více prvního čísla druhé číslo:

```
mul(1, 2)
mul(1.5, 2)
```

A tyto výsledky:

* Prvním příkladu: `2`
* Druhém příkladu `3`

<a name="multipartBody"></a>

## <a name="multipartbody"></a>multipartBody

Vrátí pro určitou část textu v výstup akce, který má více částí.

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
| <*Text*> | Řetězec | Text pro zadaný část | 
|||| 

<a name="not"></a>

## <a name="not"></a>není

Zkontrolujte, zda výraz je hodnota false. Vrátí hodnotu PRAVDA, pokud výraz je hodnota false, nebo hodnotu NEPRAVDA, pokud je hodnota true, vrátí.

```
not(<expression>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*výraz*> | Ano | Logická hodnota | Výraz, který se kontrola | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false | Logická hodnota | Vrátí hodnotu PRAVDA, pokud výraz je hodnota false. Vrátí hodnotu false, pokud výraz hodnotu true. |  
|||| 

*Příklad 1*

Tyto příklady zkontrolujte, zda jsou false zadaných výrazů: 

```
not(false)
not(true)
```

A tyto výsledky:

* Prvním příkladu: výraz je nastavena hodnota false, takže funkce vrátí hodnotu `true`.
* Druhý příklad: výraz je nastavena hodnota true, takže funkce vrátí hodnotu `false`.

*Příklad 2*

Tyto příklady zkontrolujte, zda jsou false zadaných výrazů: 

```
not(equals(1, 2))
not(equals(1, 1))
```

A tyto výsledky:

* Prvním příkladu: výraz je nastavena hodnota false, takže funkce vrátí hodnotu `true`.
* Druhý příklad: výraz je nastavena hodnota true, takže funkce vrátí hodnotu `false`.

<a name="or"></a>

## <a name="or"></a>nebo

Zkontrolujte, zda je nejméně jeden výraz hodnotu true. Vrátí hodnotu PRAVDA, pokud nejméně jeden výraz hodnotu true, nebo vrátí hodnotu false v případě, že jsou všechny false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Expression1*>, <*Výraz2*>,... | Ano | Logická hodnota | Výrazy ke kontrole | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false | Logická hodnota | Vrátí hodnotu PRAVDA, pokud nejméně jeden výraz hodnotu true. Vrátí hodnotu false, pokud jsou všechny výrazy hodnotu false. |  
|||| 

*Příklad 1*

Tyto příklady zkontrolujte, zda je nejméně jeden výraz hodnotu true:

```
or(true, false)
or(false, false)
```

A tyto výsledky:

* Prvním příkladu: nejméně jednoho výrazu je nastavena hodnota true, takže funkce vrátí hodnotu `true`.
* Druhý příklad: oba výrazy jsou, funkce vrátí hodnotu false, `false`.

*Příklad 2*

Tyto příklady zkontrolujte, zda je nejméně jeden výraz hodnotu true:

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

A tyto výsledky:

* Prvním příkladu: nejméně jednoho výrazu je nastavena hodnota true, takže funkce vrátí hodnotu `true`.
* Druhý příklad: oba výrazy jsou, funkce vrátí hodnotu false, `false`.

<a name="parameters"></a>

## <a name="parameters"></a>parameters

Vrátí hodnotu pro parametr, který je popsán v svou definici. aplikaci logiky. 

```
parameters('<parameterName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Název parametru*> | Ano | Řetězec | Název pro parametr, jehož hodnotu chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota parametru*> | Všechny | Hodnota zadaného parametru | 
|||| 

*Příklad* 

Předpokládejme, že máte tato hodnota JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Tento příklad získá hodnotu pro zadaný parametr:

```
parameters('fullName')
```

A vrátí výsledek tohoto: `"Sophia Owen"`

<a name="rand"></a>

## <a name="rand"></a>rand –

Vrátí náhodné číslo ze zadaného rozsahu, který je pouze na konci počáteční (včetně).

```
rand(<minValue>, <maxValue>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*MinValue*> | Ano | Integer | Nejnižší celé číslo v rozsahu | 
| <*MaxValue*> | Ano | Integer | Celé číslo, které následuje nejvyšší celé číslo v rozsahu, který může vrátit funkce | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*náhodné výsledek*> | Integer | Vrácená z zadaný rozsah náhodné celé číslo |  
|||| 

*Příklad*

Tento příklad načte náhodné celé číslo z zadaný rozsah, s výjimkou maximální hodnota: 

```
rand(1, 5)
```

A jako výsledek vrátí jednu z těchto čísla: `1`, `2`, `3`, nebo `4` 

<a name="range"></a>

## <a name="range"></a>rozsah

Vrátí pole celé číslo, které začne ze zadané celé číslo.

```
range(<startIndex>, <count>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Počáteční index*> | Ano | Integer | Celočíselná hodnota, která se spouští jako první položka pole | 
| <*Počet*> | Ano | Integer | Počet celých čísel v poli | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*rozsah výsledků*>] | Pole | Pole s celá čísla od zadaného indexu |  
|||| 

*Příklad*

Tento příklad vytvoří pole celé číslo, které se spustí z určeného indexu a má zadaný počet celá čísla:

```
range(1, 4)
```

A vrátí výsledek tohoto: `[1, 2, 3, 4]`

<a name="replace"></a>

## <a name="replace"></a>Nahradit

Nahraďte je dílčí řetězec zadaný řetězec a vrátí výsledný řetězec. Tato funkce je malá a velká písmena.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který obsahuje dílčí řetězec nahrazen | 
| <*oldText*> | Ano | Řetězec | Dílčí řetězec nahrazen | 
| <*newText*> | Ano | Řetězec | Náhradní řetězec | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat text*> | Řetězec | Aktualizované řetězec po nahrazení dílčí řetězec <p>Pokud není nalezen dílčí řetězec, vrátí původní řetězec. | 
|||| 

*Příklad* 

Tento příklad najde dílčí řetězec "staré" v "původní řetězec" a nahradí "staré" "New": 

```
replace('the old string', 'old', 'new')
```

A vrátí výsledek tohoto: `"the new string"`

<a name="removeProperty"></a>

## <a name="removeproperty"></a>removeProperty

Odeberte vlastnost z objektu a vrátí objekt aktualizované.

```
removeProperty(<object>, '<property>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Objekt*> | Ano | Objekt | Objekt JSON, ze které chcete odebrat vlastnost | 
| <*Vlastnost*> | Ano | Řetězec | Název vlastnosti odebrat | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktualizovat objekt*> | Objekt | Aktualizovaný objekt JSON bez zadané vlastnosti | 
|||| 

*Příklad*

V tomto příkladu odebere `"accountLocation"` vlastnost z `"customerProfile"` objekt, který je převést na JSON se [JSON()](#json) fungovat a vrátí aktualizovaného objektu:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

## <a name="setproperty"></a>SetProperty –

Nastavit hodnotu pro vlastnost objektu a vrátí objekt aktualizované. Chcete-li přidat novou vlastnost, můžete tuto funkci nebo [addProperty()](#addProperty) funkce.

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Objekt*> | Ano | Objekt | Objekt JSON, jehož vlastnosti chcete nastavit | 
| <*Vlastnost*> | Ano | Řetězec | Název vlastnosti existující nebo nové nastavení | 
| <*Hodnota*> | Ano | Všechny | Hodnota k nastavení pro zadanou vlastnost |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktualizovat objekt*> | Objekt | Aktualizovaný objekt JSON jejichž vlastnost nastavíte | 
|||| 

*Příklad*

Tento příklad nastaví `"accountNumber"` vlastnost na `"customerProfile"` objekt, který je převést na JSON se [JSON()](#json) funkce. Funkce přiřazuje hodnotu generované [guid()](#guid) fungovat a vrátí aktualizovaný objekt JSON:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

## <a name="skip"></a>Přeskočit

Odebrání položek z před kolekce a vrátí *všechny ostatní* položky.

```
skip([<collection>], <count>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*kolekce*> | Ano | Pole | Kolekce, jejichž položky, které chcete odebrat | 
| <*Počet*> | Ano | Integer | Kladné celé číslo pro počet položek, které chcete odebrat vpředu | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*aktualizovat kolekci*>] | Pole | Aktualizované kolekci po odebrání zadaných položek | 
|||| 

*Příklad*

V tomto příkladu odebere jednu položku, číslo 0, od před zadané pole: 

```
skip([0, 1, 2, 3], 1)
```

A vrátí toto pole Zbývající položky: `[1,2,3]`

<a name="split"></a>

## <a name="split"></a>split

Vrátí pole, které má všechny znaky v řetězci a každý znak oddělených *oddělovač*.

```
split('<text>', '<separator>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který obsahuje znaky rozdělit |  
| <*Oddělovač*> | Ano | Řetězec | Oddělovač, který se zobrazí mezi každý znak v poli výsledný | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*char1*><*oddělovače*><*char2*><*oddělovače*>...] | Pole | Výsledné pole vytvořené z všechny položky v zadaném řetězci |
|||| 

*Příklad* 

Tento příklad vytvoří pole ze zadaného řetězce, každý znak oddělíte čárkou jako oddělovače:

```
split('abc', ',')
```

A vrátí výsledek tohoto: `[a, b, c]`

<a name="startOfDay"></a>

## <a name="startofday"></a>startOfDay

Vrátí začátek dne pro časové razítko. 

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Zadané časové razítko, ale počínaje Označit nula hodin dne | 
|||| 

*Příklad* 

Tento příklad vyhledá začátek dne pro toto časové razítko:

```
startOfDay('2018-03-15T13:30:30Z')
```

A vrátí výsledek tohoto: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

## <a name="startofhour"></a>startOfHour

Vrátí začátek hodiny pro hodnotu časového razítka. 

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Zadané časové razítko, ale počínaje Označit nula minut za hodinu | 
|||| 

*Příklad* 

Tento příklad vyhledá začátek hodiny pro toto časové razítko:

```
startOfHour('2018-03-15T13:30:30Z')
```

A vrátí výsledek tohoto: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

## <a name="startofmonth"></a>startOfMonth

Vrátí začátek měsíce pro časové razítko. 

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Zadané časové razítko, ale od první den v měsíci na značce hodinu nula. | 
|||| 

*Příklad* 

Tento příklad vrátí začátek měsíce pro toto časové razítko:

```
startOfMonth('2018-03-15T13:30:30Z')
```

A vrátí výsledek tohoto: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

## <a name="startswith"></a>startsWith

Zkontrolujte, zda řetězec začíná konkrétní dílčí řetězec. Vrátí hodnotu PRAVDA, když se najde dílčí řetězec, nebo vrátí hodnotu false, pokud nebyl nalezen. Tato funkce není malá a velká písmena.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který má zkontrolovat | 
| <*Prohledávanýtext*> | Ano | Řetězec | Výchozí řetězec, který má najít | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| hodnotu true nebo false  | Logická hodnota | Vrátí hodnotu PRAVDA, když se najde počáteční dílčí řetězec. Vrátí hodnotu false při nebyl nalezen. | 
|||| 

*Příklad 1* 

Tento příklad zkontroluje, zda text "hello, world" začíná dílčí řetězec "hello":

```
startsWith('hello world', 'hello')
```

A vrátí výsledek tohoto: `true`

*Příklad 2*

Tento příklad zkontroluje, zda text "hello, world" začíná dílčí řetězec "Pozdrav":

```
startsWith('hello world', 'greetings')
```

A vrátí výsledek tohoto: `false`

<a name="string"></a>

## <a name="string"></a>řetězec

Vrátí řetězec verze pro hodnotu.

```
string(<value>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Všechny | Hodnota k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*hodnotu řetězce*> | Řetězec | Řetězec verze pro zadanou hodnotu | 
|||| 

*Příklad 1* 

Tento příklad vytvoří řetězec verze pro toto číslo:

```
string(10)
```

A vrátí výsledek tohoto: `"10"`

*Příklad 2*

Tento příklad vytvoří řetězec pro zadaný objekt JSON a používá znak zpětného lomítka (\\) jako řídicí znak pro značku dvojité uvozovky (").

```
string( { "name": "Sophie Owen" } )
```

A vrátí výsledek tohoto: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

## <a name="sub"></a>Sub –

Vrátí výsledek z odečtením druhé číslo z první číslo.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*minuend*> | Ano | Celé číslo nebo číslo Float | Číslo, ze kterého má odečíst *subtrahend* | 
| <*subtrahend*> | Ano | Celé číslo nebo číslo Float | Číslo, které má odečíst od *minuend* | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*výsledek*> | Celé číslo nebo číslo Float | Výsledek z odečtením druhé číslo z první číslo | 
|||| 

*Příklad* 

Tento příklad odečítá od druhé číslo z první číslo:

```
sub(10.3, .3)
```

A vrátí výsledek tohoto: `10`

<a name="substring"></a>

## <a name="substring"></a>dílčí řetězec

Vrácení znaků z řetězce začínajících zadané pozici nebo index. Počáteční hodnoty indexu s číslem 0. 

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
| <*substring výsledek*> | Řetězec | Substring s zadaný počet znaků, počínaje na pozici zadaného indexu v řetězci zdroje | 
|||| 

*Příklad* 

Tento příklad vytvoří pěti znacích dílčí řetězec z zadaný řetězec, od hodnotu indexu 6:

```
substring('hello world', 6, 5)
```

A vrátí výsledek tohoto: `"world"`

<a name="subtractFromTime"></a>

## <a name="subtractfromtime"></a>subtractFromTime

Odečte počet časových jednotek z časového razítka. Viz také [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko | 
| <*Interval*> | Ano | Integer | Počet jednotek zadaný čas má odečíst | 
| <*timeUnit*> | Ano | Řetězec | Jednotka času pro použití s *interval*: "Sekundu", "Minut", "Hodina", "Den", "Týden", "Měsíc", "Rok" | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Aktualizovat časové razítko*> | Řetězec | Časové razítko minus zadaný počet časových jednotek | 
|||| 

*Příklad 1*

Tento příklad odečítá jeden den z této časové razítko:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day') 
```

A vrátí výsledek tohoto: `"2018-01-01T00:00:00:0000000Z"`

*Příklad 2*

Tento příklad odečítá jeden den z této časové razítko:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D') 
```

A vrátí výsledek tohoto formátu volitelné "D": `"Monday, January, 1, 2018"`

<a name="take"></a>

## <a name="take"></a>proveďte

Vrátí položky z před kolekce. 

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*kolekce*> | Ano | Řetězec nebo pole | Kolekce, jejichž položky, které chcete | 
| <*Počet*> | Ano | Integer | Pro počet položek, které chcete z před kladné celé číslo | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*podmnožina*> nebo [<*podmnožina*>] | Řetězec nebo pole, v uvedeném pořadí | Řetězec nebo pole, které má zadaný počet položek, které jsou převzaty z před původní kolekci | 
|||| 

*Příklad*

Tyto příklady sám před těchto kolekcí zadaný počet položek:

```
take('abcde`, 3)
take([0, 1, 2, 3, 4], 3)
```

A tyto výsledky:

* Prvním příkladu: `"abc"`
* Druhém příkladu: `[0, 1, 2]`

<a name="ticks"></a>

## <a name="ticks"></a>rysky

Vrátí `ticks` hodnota vlastnosti pro zadané časové razítko. A *značek* je 100 nanosekund interval.

```
ticks('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*časové razítko*> | Ano | Řetězec | Řetězec pro časovým razítkem | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*rysky číslo*> | Integer | Počet značek od zadaného časového razítka | 
|||| 

<a name="toLower"></a>

## <a name="tolower"></a>toLower

Vrátí řetězec ve formátu malá písmena. Pokud znak v řetězci nemá malá verze, zůstane nezměněno pro vrácený řetězec tento znak.

```
toLower('<text>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který vrátí ve formátu malá písmena | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*malá nešifrovaným textem*> | Řetězec | Původní řetězec ve formátu malá písmena | 
|||| 

*Příklad* 

Tento příklad převede tento řetězec na malá písmena: 

```
toLower('Hello World')
```

A vrátí výsledek tohoto: `"hello world"`

<a name="toUpper"></a>

## <a name="toupper"></a>toUpper

Vrátí řetězec ve formátu velká písmena. Pokud znak v řetězci nemá verzi s velkými písmeny, zůstane nezměněno pro vrácený řetězec tento znak.

```
toUpper('<text>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který vrátí ve formátu velká písmena | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*velká písmena textu*> | Řetězec | Původní řetězec ve formátu velká písmena | 
|||| 

*Příklad* 

Tento příklad převede tento řetězec na velká písmena:

```
toUpper('Hello World')
```

A vrátí výsledek tohoto: `"HELLO WORLD"`

<a name="trigger"></a>

## <a name="trigger"></a>Aktivační události

Vrátíte výstup aktivační události v modulu runtime nebo hodnoty z jiných dvojice název a hodnota JSON, které lze přiřadit výrazu. 

* Uvnitř vstupy aktivační události vrátí tato funkce výstup z předchozí zpracování. 

* Uvnitř aktivační podmínka vrátí tato funkce výstup z aktuální provedení. 

Ve výchozím nastavení, funkce odkazuje na objekt celý aktivační událost, ale Volitelně můžete zadat vlastnost jejichž požadovanou hodnotu. Navíc tato funkce má sdružená verze, které jsou k dispozici, najdete v části [triggerOutputs()](#triggerOutputs) a [triggerBody()](#triggerBody). 

```
trigger()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktivační událost output*> | Řetězec | Výstup z aktivační událost za běhu | 
|||| 

<a name="triggerBody"></a>

## <a name="triggerbody"></a>triggerBody

Vrátí aktivační události `body` výstup za běhu. Sdružená vlastnost `trigger().outputs.body`. V tématu [trigger()](#trigger). 

```
triggerBody()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktivační událost text output*> | Řetězec | `body` Výstup z aktivační události | 
|||| 

<a name="triggerFormDataMultiValues"></a>

## <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Vrátí pole s hodnotami, které odpovídají názvu klíče do aktivační události *data formuláře* nebo *formulářem kódované* výstup. 

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Klíč*> | Ano | Řetězec | Název klíče, jehož hodnotu chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| [<*pole s klíč hodnoty*>] | Pole | Pole s všechny hodnoty, které odpovídají zadaný klíč | 
|||| 

*Příklad* 

Tento příklad vytvoří pole z hodnoty "adresa URL kanálu" klíče v aktivační procedury RSS data formuláře nebo výsledek zakódovaný ve formátu: 

```
triggerFormDataMultiValues('feedUrl')
```

A vrátí jako výsledek příklad toto pole: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

## <a name="triggerformdatavalue"></a>triggerFormDataValue

Vrátí řetězec s jednu hodnotu, která odpovídá názvu klíče do aktivační události *data formuláře* nebo *formulářem kódované* výstup. Pokud funkce najde více než jednu shodu, funkce vyvolá chybu.

```
triggerFormDataValue('<key>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Klíč*> | Ano | Řetězec | Název klíče, jehož hodnotu chcete |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*klíč hodnota*> | Řetězec | Hodnota v zadaný klíč | 
|||| 

*Příklad* 

Tento příklad vytvoří řetězec z hodnoty "adresa URL kanálu" klíče v aktivační procedury RSS data formuláře nebo výsledek zakódovaný ve formátu:

```
triggerFormDataValue('feedUrl')
```

A vrátí jako výsledek příklad tento řetězec: `"http://feeds.reuters.com/reuters/topNews"` 

<a name="triggerMultipartBody"></a>

Vrátí text pro určitou část ve výstupu aktivační událost, která má více částí. 

```
triggerMultipartBody(<index>)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Index*> | Ano | Integer | Hodnota indexu pro požadovanou část |
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Text*> | Řetězec | Text pro zadaný část ve výstupu aktivační událost s více částmi. | 
|||| 

<a name="triggerOutputs"></a>

## <a name="triggeroutputs"></a>triggerOutputs

Vrátíte výstup aktivační události v modulu runtime nebo hodnoty z jiných páry název hodnota JSON. Sdružená vlastnost `trigger().outputs`. V tématu [trigger()](#trigger). 

```
triggerOutputs()
```

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktivační událost output*> | Řetězec | Výstup z aktivační událost za běhu  | 
|||| 

<a name="trim"></a>

## <a name="trim"></a>Uvolnění dočasné paměti

Odebere úvodní a koncové mezery z hodnoty řetězce a vrátí aktualizované řetězec.

```
trim('<text>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Text*> | Ano | Řetězec | Řetězec, který se má odebrat úvodní a koncové mezery | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*updatedText*> | Řetězec | Aktualizovaná verze pro původní řetězec bez začátku nebo na konci prázdné znaky | 
|||| 

*Příklad* 

V tomto příkladu odebere úvodní a koncové mezery z řetězce "Hello World":  

```
trim(' Hello World  ')
```

A vrátí výsledek tohoto: `"Hello World"`

<a name="union"></a>

## <a name="union"></a>sjednocení

Vrátí kolekci, která má *všechny* položky ze zadaných kolekcí. Než se objeví ve výsledku, může vyskytovat položky v jakékoli kolekce této funkci byl předán. Pokud jeden nebo více položek se stejným názvem, zobrazí se ve výsledku poslední položky s tímto názvem. 

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*collection1*>, <*collection2*>,...  | Ano | Pole nebo objekt, ale ne obojí | Kolekce, ze kterého má *všechny* položky | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*updatedCollection*> | Objekt, nebo pole v uvedeném pořadí | Kolekce se všechny položky ze zadaných kolekcí - žádné duplikáty | 
|||| 

*Příklad* 

Tento příklad načte *všechny* položky z těchto kolekcí: 

```
union([1, 2, 3], [1, 2, 10, 101])
```

A vrátí výsledek tohoto: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

## <a name="uricomponent"></a>uriComponent

Vrácení verze identifikátor URI kódovaný URI pro řetězec nahrazením znaky adresy URL unsafe řídicí znaky. Tuto funkci můžete používat místo [encodeUriComponent()](#encodeUriComponent). I když obě funkce fungovat stejným způsobem, `uriComponent()` upřednostňuje.

```
uriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec převést na formátu s kódováním identifikátor URI | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*kódovaný identifikátor uri*> | Řetězec | Řetězec kódovaný identifikátor URI s řídicí znaky | 
|||| 

*Příklad*

Tento příklad vytvoří verzi kódovaný identifikátor URI pro tento řetězec:

```
uriComponent('https://contoso.com')
```

A vrátí výsledek tohoto: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

## <a name="uricomponenttobinary"></a>uriComponentToBinary

Vrátí binární verze pro komponentu URI identifikátor URI.

```
uriComponentToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec kódovaný identifikátor URI k převedení | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*binární pro kódovaný uri*> | Řetězec | Binární verze pro řetězec kódovaný identifikátor URI. Binární obsah, který je kódováním base64 a je znázorněn `$content`. | 
|||| 

*Příklad*

Tento příklad vytvoří binární verze pro tento řetězec kódovaný identifikátor URI: 

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

A vrátí výsledek tohoto: 

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

## <a name="uricomponenttostring"></a>uriComponentToString

Vrátí řetězec, řetězec kódovaný jazykem URI efektivně dekódování kódování řetězec verze pro identifikátor URI (URI).

```
uriComponentToString('<value>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Hodnota*> | Ano | Řetězec | Řetězec kódovaný jazykem URI k dekódování | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*dekódovat uri*> | Řetězec | Dekódované verze pro řetězec kódovaný identifikátor URI | 
|||| 

*Příklad*

Tento příklad vytvoří dekódované řetězec verze pro tento řetězec kódovaný identifikátor URI: 

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

A vrátí výsledek tohoto: `"https://contoso.com"` 

<a name="uriHost"></a>

## <a name="urihost"></a>uriHost

Vrátí `host` hodnota pro identifikátor URI (URI).

```
uriHost('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI jejichž `host` hodnotu, kterou chcete | 
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

A vrátí výsledek tohoto: `"www.localhost.com"`

<a name="uriPath"></a>

## <a name="uripath"></a>uriPath

Vrátí `path` hodnota pro identifikátor URI (URI). 

```
uriPath('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI jejichž `path` hodnotu, kterou chcete | 
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

A vrátí výsledek tohoto: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

## <a name="uripathandquery"></a>uriPathAndQuery

Vrátí `path` a `query` hodnoty pro identifikátor URI (URI).

```
uriPathAndQuery('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI jejichž `path` a `query` hodnoty, které mají | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota dotazu cesty*> | Řetězec | `path` a `query` hodnoty pro zadaný identifikátor URI. Pokud `path` není zadat hodnotu, vrátí znak "/". | 
|||| 

*Příklad*

Tento příklad vyhledá `path` a `query` hodnoty pro tento identifikátor URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí výsledek tohoto: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

## <a name="uriport"></a>uriPort

Vrátí `port` hodnota pro identifikátor URI (URI).

```
uriPort('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI jejichž `port` hodnotu, kterou chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota portu*> | Integer | `port` Hodnotu pro zadaný identifikátor URI. Pokud `port` není zadejte hodnotu, vrátí výchozí port pro protokol. | 
|||| 

*Příklad*

Tento příklad vrací `port` hodnotu pro tento identifikátor URI:

```
uriPort('http://www.localhost:8080')
```

A vrátí výsledek tohoto: `8080`

<a name="uriQuery"></a>

## <a name="uriquery"></a>uriQuery

Vrátí `query` hodnota pro identifikátor URI (URI).

```
uriQuery('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI jejichž `query` hodnotu, kterou chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota dotazu*> | Řetězec | `query` Hodnotu pro zadaný identifikátor URI | 
|||| 

*Příklad*

Tento příklad vrací `query` hodnotu pro tento identifikátor URI: 

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí výsledek tohoto: `"?date=today"`

<a name="uriScheme"></a>

## <a name="urischeme"></a>UriScheme

Vrátí `scheme` hodnota pro identifikátor URI (URI).

```
uriScheme('<uri>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI jejichž `scheme` hodnotu, kterou chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*schéma – hodnota*> | Řetězec | `scheme` Hodnotu pro zadaný identifikátor URI | 
|||| 

*Příklad*

Tento příklad vrací `scheme` hodnotu pro tento identifikátor URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí výsledek tohoto: `"http"`

<a name="utcNow"></a>

## <a name="utcnow"></a>utcNow

Vrátí aktuální časové razítko. 

```
utcNow('<format>')
```

Volitelně můžete zadat jiný formát se <*formát*> parametr. 


| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Formát*> | Ne | Řetězec | Buď [jeden specifikátor formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo [vlastní formát vzor](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (rrrr-MM-ddT:mm:ss:fffffffK), která splňuje [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*aktuální-časového razítka*> | Řetězec | Aktuální datum a čas | 
|||| 

*Příklad 1*

Předpokládejme, že dnes je 15. dubna 2018:00:00: 00. Tento příklad načte aktuální časové razítko: 

```
utcNow()
```

A vrátí výsledek tohoto: `"2018-04-15T13:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že dnes je 15. dubna 2018:00:00: 00. Tento příklad načte aktuální časové razítko volitelné formátu "D":

```
utcNow('D')
```

A vrátí výsledek tohoto: `"Sunday, April 15, 2018"`

<a name="variables"></a>

## <a name="variables"></a>proměnné

Vrátí hodnotu pro zadanou proměnnou. 

```
variables('<variableName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*NázevProměnné*> | Ano | Řetězec | Název proměnné, jehož hodnotu chcete | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Hodnota proměnné*> | Všechny | Hodnota pro zadané proměnné | 
|||| 

*Příklad*

Předpokládejme, že aktuální hodnota proměnné "numItems" je 20. Tento příklad načte celočíselnou hodnotu pro tuto proměnnou:

```
variables('numItems')
```

A vrátí výsledek tohoto: `20`

<a name="workflow"></a>

## <a name="workflow"></a>pracovní postup

Vrátí všechny podrobnosti o samotného pracovního postupu při běhu. 

```
workflow().<property>
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*Vlastnost*> | Ne | Řetězec | Název vlastnosti pracovního postupu, jehož hodnotu chcete <p>Objekt pracovního postupu má tyto vlastnosti: **název**, **typ**, **id**, **umístění**, a **spustit**. **Spustit** hodnota vlastnosti je také objekt, který má tyto vlastnosti: **název**, **typ**, a **id**. | 
||||| 

*Příklad*

Tento příklad vrací název pro aktuální spuštění pracovního postupu:

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
| <*Hodnota*> | Ano | Řetězec | Řetězec s převáděný objekt JSON <p>Objekt JSON, musí mít pouze jeden kořenový vlastnost. <br>Použít znak zpětného lomítka (\\) jako řídicí znak pro dvojité uvozovky ("). | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*verze XML*> | Objekt | Kódovaného XML pro zadaný řetězec nebo objekt JSON | 
|||| 

*Příklad 1*

Tento příklad vytvoří verze XML pro tento řetězec, který obsahuje objekt JSON: 

`xml( '{ \"name\": \"Sophia Owen\" }' )`

A vrátí výsledek tohoto kódu XML: 

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

A vrátí výsledek tohoto kódu XML: 

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

## <a name="xpath"></a>výraz XPath

Zkontrolujte XML pro uzly nebo hodnoty, které odpovídají výraz XPath (XML Path Language) a vrátí odpovídající uzlů nebo hodnoty. Výraz XPath, nebo jen "XPath", můžete přejít strukturu dokumentu XML tak, aby uzly nebo výpočetní hodnoty můžete vybrat obsah XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*XML*> | Ano | Všechny | Hledaný řetězec XML pro uzly nebo hodnot, které odpovídají hodnotou výraz XPath | 
| <*výraz XPath*> | Ano | Všechny | Výraz XPath použít k vyhledání odpovídající z uzlů XML nebo hodnoty | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*uzel XML*> | XML | Když jenom jeden uzel odpovídá zadaným výrazem XPath uzlu XML | 
| <*Hodnota*> | Všechny | Hodnota z uzlu XML při pouze jednu hodnotu odpovídá zadaný výraz XPath | 
| [<*xml Uzel1*>, <*xml Uzel2*>,...] </br>-nebo- </br>[<*value1*>, <*value2*>,...] | Pole | Pole s z uzlů XML nebo hodnoty, které odpovídají zadaným výrazem XPath | 
|||| 

*Příklad 1*

Tento příklad vyhledá uzlů, které odpovídají `<name></name>` uzlu jsou zadané argumenty a vrátí pole těmito hodnotami uzlu: 

`xpath(xml(parameters('items')), '/produce/item/name')`

Zde jsou argumenty:

* Řetězce "položky", který obsahuje tento kód XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  V příkladu se používá [parameters()](#parameters) k získání řetězec XML v argumentu "položky", ale musí také převést řetězec na formátu XML s použitím [xml()](#xml) funkce. 

* Tento výraz XPath výraz, který se předá jako řetězec:

  `"/produce/item/name"`

Tady je výsledek pole s uzly, které odpovídají `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Příklad 2*

Následující příklad 1, tento příklad vyhledá uzlů, které odpovídají `<count></count>` uzlu a přidá tyto hodnoty uzlu s `sum()` funkce:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

A vrátí výsledek tohoto: `30`

*Příklad 3*

V tomto příkladu oba výrazy najít uzlů, které odpovídají `<location></location>` v zadané argumenty, mezi které patří k oboru názvů XML uzlu. Výrazy použít znak zpětného lomítka (\\) jako řídicí znak pro dvojité uvozovky (").

* *Výraz 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Výraz 2* 

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

Zde jsou argumenty:

* Tento XML, který zahrnuje obor názvů XML dokumentu, `xmlns="http://contoso.com"`: 

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Buď výraz XPath tady:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]`

Tady je výsledek uzlu, který odpovídá `<location></location` uzlu:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Příklad 4*

Následující příklad 3, tento příklad vyhledá hodnotu v `<location></location>` uzlu: 

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

A vrátí výsledek tohoto: `"Paris"`

## <a name="next-steps"></a>Další postup

Další informace o [jazyk definic workflowů funkce](../logic-apps/logic-apps-workflow-definition-language.md)

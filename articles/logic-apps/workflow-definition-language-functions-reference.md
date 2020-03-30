---
title: Referenční příručka pro funkce ve výrazech
description: Referenční příručka k funkcím ve výrazech pro Azure Logic Apps a Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 87798c93bbc1098daea2f7258a3af3e26bb4bb93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283911"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Referenční příručka pro používání funkcí ve výrazech pro Azure Logic Apps a Power Automate

Pro definice pracovních postupů v [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a Power [Automate](https://docs.microsoft.com/flow/getting-started), některé [výrazy](../logic-apps/logic-apps-workflow-definition-language.md#expressions) získat své hodnoty z akce runtime, které nemusí ještě existovat při spuštění pracovního postupu. Chcete-li odkazovat na tyto hodnoty nebo zpracovat hodnoty v těchto výrazech, můžete použít *funkce* poskytované [jazykem definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Tato referenční stránka se vztahuje na Azure Logic Apps a Power Automate, ale zobrazí se v dokumentaci K tomu, aby se aplikace Azure Logic Apps. I když tato stránka odkazuje konkrétně na aplikace logiky, tyto funkce fungují pro toky i aplikace logiky. Další informace o funkcích a výrazech v Power Automate najdete v tématu [Použití výrazů v podmínkách](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Můžete například vypočítat hodnoty pomocí matematických funkcí, jako je [například funkce add(),](../logic-apps/workflow-definition-language-functions-reference.md#add)pokud chcete součet z celá čísla nebo plovoucí. Zde jsou další příklady úkolů, které můžete provádět s funkcemi:

| Úkol | Syntaxe funkce | Výsledek |
| ---- | --------------- | ------ |
| Vrátí řetězec ve formátu s malou písmena. | toLower('<*text*>') <p>Například: toLower('Hello') | "Dobrý den" |
| Vrátí globálně jedinečný identifikátor (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Chcete-li najít funkce [na základě jejich obecného účelu](#ordered-by-purpose), zkontrolujte následující tabulky. Nebo podrobné informace o jednotlivých funkcích naleznete v [abecedním seznamu](#alphabetical-list).

> [!NOTE]
> V syntaxi pro definice parametrů, otazník (?), který se zobrazí za parametr znamená, že parametr je volitelný.
> Například viz [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funkce ve výrazech

Chcete-li zobrazit, jak používat funkci ve výrazu, tento příklad `customerName` ukazuje, jak můžete `accountName` získat hodnotu z parametru a přiřadit tuto hodnotu vlastnosti pomocí funkce [parameters()](#parameters) ve výrazu:

```json
"accountName": "@parameters('customerName')"
```

Zde jsou některé další obecné způsoby, jak můžete používat funkce ve výrazech:

| Úkol | Syntaxe funkce ve výrazu |
| ---- | -------------------------------- |
| Proveďte práci s položkou předáním této položky funkci. | "\@<*functionName*> (> *položky* <)" |
| 1. Získejte hodnotu *parameterName*'s `parameters()` pomocí vnořené funkce. </br>2. Proveďte práci s výsledkem předáním této hodnoty *functionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. Získejte výsledek z vnořené vnitřní *functionFunctionName*. </br>2. Výsledek předajte do funkce vnější *funkceName2*. | "\@<*functionName2*>(<*functionName*>(<*položka*>))" |
| 1. Získejte výsledek z *functionName*. </br>2. Vzhledem k tomu, že výsledkem je objekt s property *propertyName*, získat hodnotu této vlastnosti. | "\@<*functionName*> (<*položka*>).<*propertyName*>" |
|||

`concat()` Funkce může například trvat dvě nebo více hodnot řetězce jako parametry. Tato funkce kombinuje tyto řetězce do jednoho řetězce.
Můžete buď předat v řetězci literály, například "Sophia" a "Owen", takže dostanete kombinovaný řetězec, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Nebo můžete získat hodnoty řetězce z parametrů. Tento příklad `parameters()` používá funkci `concat()` v `firstName` každém `lastName` parametru a parametry a. Potom předat výsledné řetězce funkce `concat()` tak, aby se vám kombinovaný řetězec, například "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

V obou případech oba příklady `customerName` přiřadit výsledek vlastnosti.

Zde jsou dostupné funkce seřazené podle jejich obecného účelu, nebo můžete procházet funkce na základě [abecedního pořadí](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funkce řetězců

Chcete-li pracovat s řetězci, můžete použít tyto funkce řetězce a také některé [funkce kolekce](#collection-functions).
Řetězcové funkce fungují pouze na řetězcích.

| Funkce řetězce | Úkol |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Zkombinujte dva nebo více řetězců a vraťte kombinovaný řetězec. |
| [Endswith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Zkontrolujte, zda řetězec končí zadaným podřetězcem. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Vrátit číslo jako řetězec na základě zadaného formátu |
| [Identifikátor guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generovat globálně jedinečný identifikátor (GUID) jako řetězec. |
| [Indexof](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Vraťte počáteční pozici pro podřetězec. |
| [Lastindexof](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Vrátí počáteční pozici pro poslední výskyt podřetězce. |
| [Nahradit](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Nahraďte podřetězec zadaným řetězcem a vraťte aktualizovaný řetězec. |
| [Rozdělit](../logic-apps/workflow-definition-language-functions-reference.md#split) | Vrátí pole, které obsahuje podřetězce oddělené čárkami, z většího řetězce založeného na zadaném znaku oddělovače v původním řetězci. |
| [Startswith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Zkontrolujte, zda řetězec začíná určitým podřetězcem. |
| [Podřetězec](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Vrátí znaky z řetězce, počínaje zadanou pozici. |
| [Tolower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Vrátí řetězec ve formátu s malou písmena. |
| [Toupper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Vrátí řetězec ve formátu velkých písmen. |
| [Trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Odeberte úvodní a koncové mezery z řetězce a vraťte aktualizovaný řetězec. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funkce sběru

Chcete-li pracovat s kolekcemi, obecně pole, řetězce a někdy slovníky, můžete použít tyto funkce kolekce.

| Funkce sběru | Úkol |
| ------------------- | ---- |
| [Obsahuje](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Zkontrolujte, zda kolekce má určitou položku. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Zkontrolujte, zda je kolekce prázdná. |
| [První](../logic-apps/workflow-definition-language-functions-reference.md#first) | Vrátí první položku z kolekce. |
| [Průsečíku](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Vrátí kolekci, která má *pouze* běžné položky v rámci zadané kolekce. |
| [Položky](../logic-apps/workflow-definition-language-functions-reference.md#item) | Když uvnitř opakující se akce přes pole, vrátí aktuální položku v poli během aktuální iterace akce. |
| [Připojit](../logic-apps/workflow-definition-language-functions-reference.md#join) | Vrátí řetězec, který má *všechny* položky z pole, oddělené zadaným znakem. |
| [Poslední](../logic-apps/workflow-definition-language-functions-reference.md#last) | Vrátí poslední položku z kolekce. |
| [Délka](../logic-apps/workflow-definition-language-functions-reference.md#length) | Vrátí počet položek v řetězci nebo matici. |
| [Přeskočit](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Odeberte položky z přední části kolekce a vraťte *všechny ostatní* položky. |
| [vzít](../logic-apps/workflow-definition-language-functions-reference.md#take) | Vrátit položky z přední části kolekce. |
| [Unie](../logic-apps/workflow-definition-language-functions-reference.md#union) | Vrátí kolekci, která má *všechny* položky ze zadaných kolekcí. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funkce logického porovnání

Chcete-li pracovat s podmínkami, porovnávat hodnoty a výsledky výrazů nebo vyhodnocovat různé druhy logiky, můžete použít tyto logické funkce porovnání.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce logického porovnání | Úkol |
| --------------------------- | ---- |
| [A](../logic-apps/workflow-definition-language-functions-reference.md#and) | Zkontrolujte, zda jsou všechny výrazy pravdivé. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Zkontrolujte, zda jsou obě hodnoty ekvivalentní. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Zkontrolujte, zda je první hodnota větší než druhá hodnota. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Zkontrolujte, zda je první hodnota větší nebo rovna druhé hodnotě. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Zkontrolujte, zda je výraz pravdivý nebo nepravdivý. Na základě výsledku vrátí zadanou hodnotu. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Zkontrolujte, zda je první hodnota menší než druhá hodnota. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Zkontrolujte, zda je první hodnota menší nebo rovna druhé hodnotě. |
| [Ne](../logic-apps/workflow-definition-language-functions-reference.md#not) | Zkontrolujte, zda je výraz false. |
| [Nebo](../logic-apps/workflow-definition-language-functions-reference.md#or) | Zkontrolujte, zda je pravdivý alespoň jeden výraz. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funkce převodu

Chcete-li změnit typ nebo formát hodnoty, můžete použít tyto funkce převodu.
Můžete například změnit hodnotu z logického na celé číslo.
Další informace o tom, jak aplikace Logic Apps zpracovávají typy obsahu během převodu, naleznete v [tématu Zpracování typů obsahu](../logic-apps/logic-apps-content-type.md).
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce převodu | Úkol |
| ------------------- | ---- |
| [Pole](../logic-apps/workflow-definition-language-functions-reference.md#array) | Vrátí pole z jednoho zadaného vstupu. Více vstupů naleznete v tématu [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Vraťte verzi kódu base64 pro řetězec. |
| [base64ToBinární](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Vrátí binární verzi pro řetězec kódovaný base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Vraťte verzi řetězce pro řetězec kódovaný base64. |
| [binární](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Vrátí binární verzi pro vstupní hodnotu. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Vraťte logickou verzi pro vstupní hodnotu. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Vrátí pole z více vstupů. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Vrátí identifikátor URI dat pro vstupní hodnotu. |
| [dataUriToBinární](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Vraťte binární verzi identifikátoru URI dat. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Vraťte verzi řetězce pro identifikátor URI dat. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Vraťte verzi řetězce pro řetězec kódovaný base64. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Vraťte binární verzi identifikátoru URI dat. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Vrátí řetězec, který nahradí řídicí znaky dekódované verze. |
| [kódUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Vrátí řetězec, který nahradí znaky url nebezpečné řídicími znaky. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Vrátí číslo s plovoucí desetinnou tácem pro vstupní hodnotu. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Vraťte verzi celého čísla pro řetězec. |
| [Json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Vraťte hodnotu nebo objekt typu Zápis objektu JavaScriptu (JSON) pro řetězec nebo XML. |
| [Řetězec](../logic-apps/workflow-definition-language-functions-reference.md#string) | Vraťte verzi řetězce pro vstupní hodnotu. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Vraťte verzi kódovky URI pro vstupní hodnotu nahrazením znaků nebezpečných adres URL řídicími znaky. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Vraťte binární verzi pro řetězec kódovaný identifikátorem URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Vraťte verzi řetězce pro řetězec kódovaný identifikátorem URI. |
| [Xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Vraťte verzi XML pro řetězec. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematické funkce

Chcete-li pracovat s celá čísla a plovoucí, můžete použít tyto matematické funkce.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Matematické funkce | Úkol |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Vrátit výsledek přidáním dvou čísel. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Vrátit výsledek z dělení dvou čísel. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Vrátí nejvyšší hodnotu ze sady čísel nebo pole. |
| [Min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Vrátí nejnižší hodnotu ze sady čísel nebo pole. |
| [Mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Vraťte zbytek z dělení dvou čísel. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Vraťte produkt z násobení dvou čísel. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Vrátí náhodné celé číslo ze zadaného rozsahu. |
| [Rozsah](../logic-apps/workflow-definition-language-functions-reference.md#range) | Vrátí celé pole, které začíná od zadaného celého čísla. |
| [Dílčí](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Vrátit výsledek od odečtením druhé číslo od prvního čísla. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funkce data a času

Chcete-li pracovat s daty a časy, můžete použít tyto funkce data a času.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce data nebo času | Úkol |
| --------------------- | ---- |
| [Adddays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Přidejte k časovému razítku počet dní. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Přidejte k časovému razítku několik hodin. |
| [Addminutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Přidejte k časovému razítku několik minut. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Přidejte k časovému razítku počet sekund. |
| [přidatToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Přidejte k časovému razítku počet časových jednotek. Viz také [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [převéstFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Převeďte časové razítko z časové souřadnice (UTC) na cílové časové pásmo. |
| [převéstTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Převeďte časové razítko ze zdrojového časového pásma na cílové časové pásmo. |
| [převéstToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Převést časové razítko ze zdrojového časového pásma na časovou souřadnou (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Vrátí součást den v měsíci z časového razítka. |
| [Dayofweek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Vrátí součást den v týdnu z časového razítka. |
| [Dayofyear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Vrátí součást den v roce z časového razítka. |
| [Formatdatetime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Vrátit datum z časového razítka. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Vrátí aktuální časové razítko plus zadané časové jednotky. Viz také [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Vrátí aktuální časové razítko mínus zadané časové jednotky. Viz také [odečístFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [začátekDne](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Vrátit začátek dne pro časové razítko. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Vraťte začátek hodiny pro časové razítko. |
| [začátekMěsíce](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Vrátit začátek měsíce pro časové razítko. |
| [odečístFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odečtěte počet časových jednotek od časového razítka. Viz také [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Klíšťata](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Vrátí `ticks` hodnotu vlastnosti pro zadané časové razítko. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Vrátí aktuální časové razítko jako řetězec. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funkce pracovního postupu

Tyto funkce pracovního postupu vám mohou pomoci:

* Získejte podrobnosti o instanci pracovního postupu za běhu.
* Pracujte se vstupy používanými pro vytváření instancí aplikací nebo toků logiky.
* Odkaz na výstupy z aktivačních událostí a akcí.

Můžete například odkazovat na výstupy z jedné akce a použít tato data v pozdější akci.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce pracovních postupů | Úkol |
| ----------------- | ---- |
| [Akce](../logic-apps/workflow-definition-language-functions-reference.md#action) | Vrátí výstup aktuální akce za běhu nebo hodnoty z jiných párů názvů a hodnot JSON. Viz také [akce](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Vrátit `body` výstup akce za běhu. Viz také [tělo](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionVýstupy](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Vrátit výstup akce za běhu. Viz [výstupy](../logic-apps/workflow-definition-language-functions-reference.md#outputs) a [akce](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Akce](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Vrátí výstup akce za běhu nebo hodnoty z jiných párů názvů a hodnot JSON. Viz také [akce](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [Tělo](#body) | Vrátit `body` výstup akce za běhu. Viz také [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Vytvořte pole s hodnotami, které odpovídají názvu klíče ve *výstupech* akcí *s daty* formuláře nebo formulářem. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Vrátí jednu hodnotu, která odpovídá názvu klíče ve *výstupu formuláře* nebo kódu formuláře akce nebo *ve formuláři*. |
| [Položky](../logic-apps/workflow-definition-language-functions-reference.md#item) | Když uvnitř opakující se akce přes pole, vrátí aktuální položku v poli během aktuální iterace akce. |
| [Položky](../logic-apps/workflow-definition-language-functions-reference.md#items) | Když uvnitř Foreach nebo Until smyčky, vrátí aktuální položku ze zadané smyčky.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Když uvnitř Until smyčky, vrátí hodnotu indexu pro aktuální iteraci. Tuto funkci můžete použít uvnitř vnořené Do smyčky. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Vraťte "adresu URL zpětného volání", která volá aktivační událost nebo akci. |
| [vícedílná tělo](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Vrátí tělo pro konkrétní část ve výstupu akce, která má více částí. |
| [Výstupy](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Vrátit výstup akce za běhu. |
| [Parametry](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Vrátí hodnotu parametru, který je popsán v definici pracovního postupu. |
| [Výsledek](../logic-apps/workflow-definition-language-functions-reference.md#result) | Vrátí vstupy a výstupy ze všech akcí uvnitř zadané `For_each`akce `Until`s `Scope`vymezenou matou, například , a . |
| [Aktivační událost](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Vrátí výstup aktivační události za běhu nebo z jiných párů názvů a hodnot JSON. Viz také [triggerOutputs](#triggerOutputs) a [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Vrátí `body` výstup aktivační události za běhu. Viz [aktivační událost](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Vrátí jednu hodnotu odpovídající názvu klíče ve výstupech aktivačních událostí *kódovaných* *formulářem* nebo formulářem. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Vrátí tělo pro určitou část ve vícepodajovém výstupu aktivační události. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Vytvořte pole, jehož hodnoty odpovídají názvu klíče ve výstupech aktivačních událostí *kódovaných* *formulářem* nebo formulářem. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Vrátí výstup aktivační události za běhu nebo hodnoty z jiných párů názvů a hodnot JSON. Viz [aktivační událost](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Proměnné](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Vrátí hodnotu pro zadanou proměnnou. |
| [pracovní postup](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Vrátí všechny podrobnosti o samotném pracovním postupu během běhu. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funkce analýzy identifikátoru URI

Chcete-li pracovat s jednotnými identifikátory prostředků (URI) a získat různé hodnoty vlastností pro tyto identifikátory URI, můžete použít tyto funkce analýzy identifikátorů URI.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce analýzy identifikátoru URI | Úkol |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Vrátí `host` hodnotu pro identifikátor jednotného prostředku (URI). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Vrátí `path` hodnotu pro identifikátor jednotného prostředku (URI). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Vrátí `path` hodnoty `query` a pro jednotný identifikátor prostředku (URI). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Vrátí `port` hodnotu pro identifikátor jednotného prostředku (URI). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Vrátí `query` hodnotu pro identifikátor jednotného prostředku (URI). |
| [uchvace](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Vrátí `scheme` hodnotu pro identifikátor jednotného prostředku (URI). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulační funkce: JSON & XML

Chcete-li pracovat s objekty JSON a uzly XML, můžete použít tyto funkce manipulace.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Manipulační funkce | Úkol |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Přidejte vlastnost a její hodnotu nebo dvojici název-hodnota do objektu JSON a vraťte aktualizovaný objekt. |
| [Coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Vrátí první hodnotu bez nuly z jednoho nebo více parametrů. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Odeberte vlastnost z objektu JSON a vraťte aktualizovaný objekt. |
| [Setproperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Nastavte hodnotu vlastnosti objektu JSON a vraťte aktualizovaný objekt. |
| [Xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Zkontrolujte, zda jazyk XML neodpovídá uzlům nebo hodnotám, které odpovídají výrazu XPath (JAZYK CESTY XML), a vraťte odpovídající uzly nebo hodnoty. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Všechny funkce - abecední seznam

V této části jsou uvedeny všechny dostupné funkce v abecedním pořadí.

<a name="action"></a>

### <a name="action"></a>action

Vrátí výstup *aktuální* akce za běhu nebo hodnoty z jiných párů názvů a hodnot JSON, které můžete přiřadit výrazu.
Ve výchozím nastavení tato funkce odkazuje na celý objekt akce, ale volitelně můžete určit vlastnost, jejíž hodnotu chcete.
Viz také [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Funkci můžete `action()` používat pouze na těchto místech:

* Vlastnost `unsubscribe` pro akci webhooku, takže máte přístup `subscribe` k výsledku z původního požadavku
* Vlastnost `trackedProperties` pro akci
* Podmínka `do-until` smyčky pro akci

```
action()
action().outputs.body.<property>
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Vlastnost*> | Ne | Řetězec | Název vlastnosti objektu akce, jejíž hodnotu chcete: **name**, **startTime**, **endTime**, **input ,** **outputs**, **status**, **code**, **trackingId**a **clientTrackingId**. Na webu Azure Portal můžete tyto vlastnosti najít kontrolou podrobnosti o konkrétní historii spuštění. Další informace naleznete v tématu [REST API - Akce spuštění pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | -----| ----------- |
| <*akční výstup*> | Řetězec | Výstup z aktuální akce nebo vlastnosti |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Vrátit `body` výstup akce za běhu.
Zkratka `actions('<actionName>').outputs.body`pro .
Viz [body()](#body) a [actions()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Actionname*> | Ano | Řetězec | Název `body` výstupu akce, který chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | -----| ----------- |
| <*akce-tělo-výstup*> | Řetězec | Výstup `body` ze zadané akce |
||||

*Příklad*

Tento příklad `body` získá výstup z `Get user`akce Twitter :

```
actionBody('Get_user')
```

A vrátí tento výsledek:

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

### <a name="actionoutputs"></a>actionVýstupy

Vrátit výstup akce za běhu.  a je zkratka pro `actions('<actionName>').outputs`. Viz [actions()](#actions). Funkce `actionOutputs()` překládá `outputs()` v Návrháři aplikace logiky, takže zvažte `actionOutputs()`použití [outputs()](#outputs), spíše než . Ačkoli obě funkce fungují `outputs()` stejným způsobem, je upřednostňována.

```
actionOutputs('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Actionname*> | Ano | Řetězec | Název výstupu akce, který chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | -----| ----------- |
| <*Výstup*> | Řetězec | Výstup ze zadané akce |
||||

*Příklad*

Tento příklad získá výstup z `Get user`akce Twitter :

```
actionOutputs('Get_user')
```

A vrátí tento výsledek:

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

### <a name="actions"></a>akce

Vrátí výstup akce za běhu nebo hodnoty z jiných párů názvů a hodnot JSON, které můžete přiřadit výrazu. Ve výchozím nastavení funkce odkazuje na celý objekt akce, ale volitelně můžete určit vlastnost, jejíž hodnotu chcete.
Pro zkrácené verze viz [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)a [body()](#body).
Aktuální akce naleznete v [tématu action()](#action).

> [!NOTE]
> Dříve bylo možné `actions()` použít funkci `conditions` nebo prvek při určování, že akce byla spuštěna na základě výstupu z jiné akce. Chcete-li však deklarovat explicitně závislosti mezi `runAfter` akcemi, musíte nyní použít vlastnost závislé akce.
> Další informace o `runAfter` vlastnosti, naleznete v [tématu Catch a zpracování selhání s runAfter vlastnost](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Actionname*> | Ano | Řetězec | Název objektu akce, jehož výstup chcete  |
| <*Vlastnost*> | Ne | Řetězec | Název vlastnosti objektu akce, jejíž hodnotu chcete: **name**, **startTime**, **endTime**, **input ,** **outputs**, **status**, **code**, **trackingId**a **clientTrackingId**. Na webu Azure Portal můžete tyto vlastnosti najít kontrolou podrobnosti o konkrétní historii spuštění. Další informace naleznete v tématu [REST API - Akce spuštění pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | -----| ----------- |
| <*akční výstup*> | Řetězec | Výstup ze zadané akce nebo vlastnosti |
||||

*Příklad*

Tento příklad `status` získá hodnotu vlastnosti z akce `Get user` Twitter za běhu:

```
actions('Get_user').outputs.body.status
```

A vrátí tento výsledek:`"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Vrátit výsledek přidáním dvou čísel.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, summand_2 *<*> | Ano | Celé číslo, Plovoucí nebo smíšené | Čísla, která chcete přidat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | -----| ----------- |
| <*výsledek-součet*> | Celé číslo nebo plovoucí | Výsledek přidání zadaných čísel |
||||

*Příklad*

Tento příklad přidá zadaná čísla:

```
add(1, 1.5)
```

A vrátí tento výsledek:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>Adddays

Přidejte k časovému razítku počet dní.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Dní*> | Ano | Integer | Kladný nebo záporný počet dní, které chcete přidat |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Časové razítko plus zadaný počet dní  |
||||

*Příklad 1*

Tento příklad přidá 10 dní k zadanému časovému razítku:

```
addDays('2018-03-15T13:00:00Z', 10)
```

A vrátí tento výsledek:`"2018-03-25T00:00:0000000Z"`

*Příklad 2*

Tento příklad odečte pět dní od zadaného časového razítka:

```
addDays('2018-03-15T00:00:00Z', -5)
```

A vrátí tento výsledek:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Přidejte k časovému razítku několik hodin.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Hodin*> | Ano | Integer | Kladný nebo záporný počet hodin, které chcete přidat |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Časové razítko plus zadaný počet hodin  |
||||

*Příklad 1*

Tento příklad přidá 10 hodin k zadanému časovému razítku:

```
addHours('2018-03-15T00:00:00Z', 10)
```

A vrátí tento výsledek:`"2018-03-15T10:00:0000000Z"`

*Příklad 2*

Tento příklad odečte pět hodin od zadaného časového razítka:

```
addHours('2018-03-15T15:00:00Z', -5)
```

A vrátí tento výsledek:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>Addminutes

Přidejte k časovému razítku několik minut.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Minut*> | Ano | Integer | Kladný nebo záporný počet minut, které chcete přidat |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Časové razítko plus zadaný počet minut |
||||

*Příklad 1*

Tento příklad přidá 10 minut k zadanému časovému razítku:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

A vrátí tento výsledek:`"2018-03-15T00:20:00.0000000Z"`

*Příklad 2*

Tento příklad odečte pět minut od zadaného časového razítka:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

A vrátí tento výsledek:`"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Přidejte vlastnost a její hodnotu nebo dvojici název-hodnota do objektu JSON a vraťte aktualizovaný objekt. Pokud vlastnost již existuje za běhu, funkce se nezdaří a vyvolá chybu.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ano | Objekt | Objekt JSON, kam chcete přidat vlastnost |
| <*Vlastnost*> | Ano | Řetězec | Název vlastnosti, kterou chcete přidat |
| <*Hodnotu*> | Ano | Všechny | Hodnota vlastnosti |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizovaný objekt*> | Objekt | Aktualizovaný objekt JSON se zadanou vlastností |
||||

Chcete-li přidat podřízenou vlastnost do existující vlastnosti, použijte tuto syntaxi:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ano | Objekt | Objekt JSON, kam chcete přidat vlastnost |
| <*nadřazená vlastnost*> | Ano | Řetězec | Název nadřazené vlastnosti, do které chcete přidat podřízenou vlastnost |
| <*dětský majetek*> | Ano | Řetězec | Název podřízené vlastnosti, kterou chcete přidat |
| <*Hodnotu*> | Ano | Všechny | Hodnota nastavená pro zadanou vlastnost |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizovaný objekt*> | Objekt | Aktualizovaný objekt JSON, jehož vlastnost nastavíte |
||||

*Příklad 1*

Tento příklad `middleName` přidá vlastnost k objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON().](#json) Objekt již obsahuje `firstName` `surName` vlastnosti a. Funkce přiřadí zadanou hodnotu nové vlastnosti a vrátí aktualizovaný objekt:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Zde je aktuální Objekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Zde je aktualizovaný objekt JSON:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Příklad 2*

Tento příklad `middleName` přidá podřízenou `customerName` vlastnost do existující vlastnosti v objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON().](#json) Funkce přiřadí zadanou hodnotu nové vlastnosti a vrátí aktualizovaný objekt:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Zde je aktuální Objekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Zde je aktualizovaný objekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Přidejte k časovému razítku počet sekund.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Sekund*> | Ano | Integer | Kladný nebo záporný počet sekund, který chcete přidat |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Časové razítko plus zadaný počet sekund  |
||||

*Příklad 1*

Tento příklad přidá 10 sekund k zadanému časovému razítku:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

A vrátí tento výsledek:`"2018-03-15T00:00:10.0000000Z"`

*Příklad 2*

Tento příklad odečte pět sekund od zadaného časového razítka:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

A vrátí tento výsledek:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>přidatToTime

Přidejte k časovému razítku počet časových jednotek.
Viz také [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Interval*> | Ano | Integer | Počet určených časových jednotek, které chcete přidat |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, který je třeba použít s *intervalem:*"Druhý", "Minute", "Hodina", "Den", "Týden", "Měsíc", "Rok" |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Časové razítko plus zadaný počet časových jednotek  |
||||

*Příklad 1*

Tento příklad přidá jeden den k zadanému časovému razítku:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

A vrátí tento výsledek:`"2018-01-02T00:00:00.0000000Z"`

*Příklad 2*

Tento příklad přidá jeden den k zadanému časovému razítku:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

A vrátí výsledek pomocí volitelného formátu "D":`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>a

Zkontrolujte, zda jsou všechny výrazy pravdivé.
Vrátit true, pokud jsou všechny výrazy true, nebo vrátit false, pokud alespoň jeden výraz je false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Ano | Logická hodnota | Výrazy, které chcete zkontrolovat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | -----| ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud jsou splněny všechny výrazy. Vrátit false, pokud alespoň jeden výraz je false. |
||||

*Příklad 1*

V těchto příkladech zkontrolujte, zda jsou zadané logické hodnoty pravdivé:

```
and(true, true)
and(false, true)
and(false, false)
```

A vrátí tyto výsledky:

* První příklad: Oba výrazy jsou `true`pravdivé, takže vrátí .
* Druhý příklad: Jeden výraz je `false`false, takže vrátí .
* Třetí příklad: Oba výrazy jsou `false`false, takže vrátí .

*Příklad 2*

V těchto příkladech zkontrolujte, zda jsou zadané výrazy pravdivé:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

A vrátí tyto výsledky:

* První příklad: Oba výrazy jsou `true`pravdivé, takže vrátí .
* Druhý příklad: Jeden výraz je `false`false, takže vrátí .
* Třetí příklad: Oba výrazy jsou `false`false, takže vrátí .

<a name="array"></a>

### <a name="array"></a>pole

Vrátí pole z jednoho zadaného vstupu.
Více vstupů naleznete v tématu [createArray()](#createArray).

```
array('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec pro vytvoření pole |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| [<*hodnota*>] | Pole | Pole, které obsahuje jeden zadaný vstup |
||||

*Příklad*

Tento příklad vytvoří pole z řetězce "hello":

```
array('hello')
```

A vrátí tento výsledek:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Vraťte verzi kódu base64 pro řetězec.

```
base64('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Vstupní řetězec |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*base64-řetězec*> | Řetězec | Verze kódovaná base64 pro vstupní řetězec |
||||

*Příklad*

Tento příklad převede řetězec "hello" na řetězec kódovaný base64:

```
base64('hello')
```

A vrátí tento výsledek:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinární

Vrátí binární verzi pro řetězec kódovaný base64.

```
base64ToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec kódovaný base64, který chcete převést |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*binární řetězec pro base64*> | Řetězec | Binární verze pro řetězec kódovaný base64 |
||||

*Příklad*

Tento příklad převede řetězec "aGVsbG8=" kódu base64 na binární řetězec:

```
base64ToBinary('aGVsbG8=')
```

A vrátí tento výsledek:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Vraťte verzi řetězce pro řetězec kódovaný base64, který účinně dekóduje řetězec base64.
Použijte tuto funkci spíše než [decodeBase64()](#decodeBase64).
Ačkoli obě funkce fungují `base64ToString()` stejným způsobem, je upřednostňována.

```
base64ToString('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec kódovaný base64 k dekódování |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*dekódovaný base64-string*> | Řetězec | Verze řetězce pro řetězec kódovaný base64 |
||||

*Příklad*

Tento příklad převádí řetězec "aGVsbG8=" kódu base64 na pouze řetězec:

```
base64ToString('aGVsbG8=')
```

A vrátí tento výsledek:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binární

Vraťte binární verzi pro řetězec.

```
binary('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec, který chcete převést |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*binární hodnota pro vstup*> | Řetězec | Binární verze pro zadaný řetězec |
||||

*Příklad*

Tento příklad převede řetězec "hello" na binární řetězec:

```
binary('hello')
```

A vrátí tento výsledek:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>text

Vrátit `body` výstup akce za běhu.
Zkratka `actions('<actionName>').outputs.body`pro .
Viz [actionBody()](#actionBody) a [actions()](#actions).

```
body('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Actionname*> | Ano | Řetězec | Název `body` výstupu akce, který chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | -----| ----------- |
| <*akce-tělo-výstup*> | Řetězec | Výstup `body` ze zadané akce |
||||

*Příklad*

Tento příklad `body` získá výstup `Get user` z akce Twitter:

```
body('Get_user')
```

A vrátí tento výsledek:

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

### <a name="bool"></a>bool

Vraťte logickou verzi pro hodnotu.

```
bool(<value>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Všechny | Hodnota, kterou chcete převést |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Logická verze pro zadanou hodnotu |
||||

*Příklad*

Tyto příklady převedou zadané hodnoty na logické hodnoty:

```
bool(1)
bool(0)
```

A vrátí tyto výsledky:

* První příklad:`true`
* Druhý příklad:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>Coalesce

Vrátí první hodnotu bez nuly z jednoho nebo více parametrů.
Prázdné řetězce, prázdná pole a prázdné objekty nejsou null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, *> <object_2,* ... | Ano | Jakékoliv, může míchat typy | Jedna nebo více položek ke kontrole hodnoty null |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*první-nenulová položka*> | Všechny | První položka nebo hodnota, která není null. Pokud jsou všechny parametry null, tato funkce vrátí hodnotu null. |
||||

*Příklad*

Tyto příklady vrátí první hodnotu bez hodnoty null ze zadaných hodnot nebo hodnotu null, pokud jsou všechny hodnoty null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

A vrátí tyto výsledky:

* První příklad:`true`
* Druhý příklad:`"hello"`
* Třetí příklad:`null`

<a name="concat"></a>

### <a name="concat"></a>concat

Zkombinujte dva nebo více řetězců a vraťte kombinovaný řetězec.

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Ano | Řetězec | Nejméně dva řetězce kombinovat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Řetězec | Řetězec vytvořený z kombinovaných vstupních řetězců |
||||

*Příklad*

Tento příklad kombinuje řetězce "Hello" a "World":

```
concat('Hello', 'World')
```

A vrátí tento výsledek:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>obsahuje

Zkontrolujte, zda kolekce má určitou položku.
Vrátit true při nalezení položky nebo vrátit false, pokud nebyl nalezen.
Tato funkce rozlišuje malá a velká písmena.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Konkrétně tato funkce funguje na těchto typech kolekce:

* *Řetězec* pro nalezení *podřetězce*
* *Pole* pro nalezení *hodnoty*
* *Slovník* pro nalezení *klíče*

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Kolekce*> | Ano | Řetězec, pole nebo slovník | Kolekce ke kontrole |
| <*Hodnotu*> | Ano | Řetězec, pole nebo slovník, resp. | Položka, kterou chcete najít |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátit true při nalezení položky. Vrátit false, pokud nebyl nalezen. |
||||

*Příklad 1*

Tento příklad zkontroluje řetězec "hello world" pro podřetězec "svět" a vrátí hodnotu true:

```
contains('hello world', 'world')
```

*Příklad 2*

Tento příklad zkontroluje řetězec "hello world" pro podřetězec "universe" a vrátí false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>převéstFromUtc

Převeďte časové razítko z časové souřadnice (UTC) na cílové časové pásmo.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Destinationtimezone*> | Ano | Řetězec | Název cílového časového pásma. Názvy časových pásem naleznete v [tématu Microsoft Time Zone Index Values](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale bude pravděpodobně muset odebrat všechny interpunkce z názvu časového pásma. |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*převedené časové razítko*> | Řetězec | Časové razítko převedené na cílové časové pásmo |
||||

*Příklad 1*

Tento příklad převede časové razítko na zadané časové pásmo:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

A vrátí tento výsledek:`"2018-01-01T00:00:00.0000000"`

*Příklad 2*

Tento příklad převede časové razítko na zadané časové pásmo a formát:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

A vrátí tento výsledek:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>převéstTimeZone

Převeďte časové razítko ze zdrojového časového pásma na cílové časové pásmo.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Sourcetimezone*> | Ano | Řetězec | Název zdrojového časového pásma. Názvy časových pásem naleznete v [tématu Microsoft Time Zone Index Values](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale bude pravděpodobně muset odebrat všechny interpunkce z názvu časového pásma. |
| <*Destinationtimezone*> | Ano | Řetězec | Název cílového časového pásma. Názvy časových pásem naleznete v [tématu Microsoft Time Zone Index Values](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale bude pravděpodobně muset odebrat všechny interpunkce z názvu časového pásma. |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*převedené časové razítko*> | Řetězec | Časové razítko převedené na cílové časové pásmo |
||||

*Příklad 1*

Tento příklad převede zdrojové časové pásmo na cílové časové pásmo:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

A vrátí tento výsledek:`"2018-01-01T00:00:00.0000000"`

*Příklad 2*

Tento příklad převede časové pásmo na zadané časové pásmo a formát:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

A vrátí tento výsledek:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>převéstToUtc

Převést časové razítko ze zdrojového časového pásma na časovou souřadnou (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Sourcetimezone*> | Ano | Řetězec | Název zdrojového časového pásma. Názvy časových pásem naleznete v [tématu Microsoft Time Zone Index Values](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale bude pravděpodobně muset odebrat všechny interpunkce z názvu časového pásma. |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*převedené časové razítko*> | Řetězec | Časové razítko převedené na Čas UTC |
||||

*Příklad 1*

Tento příklad převede časové razítko na ČasU:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

A vrátí tento výsledek:`"2018-01-01T08:00:00.0000000Z"`

*Příklad 2*

Tento příklad převede časové razítko na ČasU:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

A vrátí tento výsledek:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Vrátí pole z více vstupů.
U jednotlivých vstupních polí viz [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Ano | Žádné, ale ne smíšené | Alespoň dvě položky pro vytvoření pole |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Pole | Pole vytvořené ze všech vstupních položek |
||||

*Příklad*

Tento příklad vytvoří pole z těchto vstupů:

```
createArray('h', 'e', 'l', 'l', 'o')
```

A vrátí tento výsledek:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Vrátí identifikátor identifikátoru prostředku (URI) dat pro řetězec.

```
dataUri('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec, který chcete převést |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*data-uri*> | Řetězec | Identifikátor URI dat pro vstupní řetězec |
||||

*Příklad*

Tento příklad vytvoří identifikátor URI dat pro řetězec "hello":

```
dataUri('hello')
```

A vrátí tento výsledek:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinární

Vrátí binární verzi pro identifikátor jednotného prostředku dat (URI).
Použijte tuto funkci spíše než [decodeDataUri()](#decodeDataUri).
Ačkoli obě funkce fungují `dataUriBinary()` stejným způsobem, je upřednostňována.

```
dataUriToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Identifikátor URI dat pro převod |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*binární-pro-data-uri*> | Řetězec | Binární verze identifikátoru URI dat |
||||

*Příklad*

Tento příklad vytvoří binární verzi pro tento identifikátor URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí tento výsledek:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Vraťte verzi řetězce pro identifikátor jednotného prostředku (URI).

```
dataUriToString('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Identifikátor URI dat pro převod |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*řetězec pro data-uri*> | Řetězec | Verze řetězce pro identifikátor URI dat |
||||

*Příklad*

Tento příklad vytvoří řetězec pro tento identifikátor URI dat:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí tento výsledek:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Vrátit den v měsíci z časového razítka.

```
dayOfMonth('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*den v měsíci*> | Integer | Den v měsíci od zadaného časového razítka |
||||

*Příklad*

Tento příklad vrátí číslo pro den v měsíci od tohoto časového razítka:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

A vrátí tento výsledek:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>Dayofweek

Vrátit den v týdnu z časového razítka.

```
dayOfWeek('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*den v týdnu*> | Integer | Den v týdnu od zadaného časového razítka, kde neděle je 0, pondělí je 1 a tak dále |
||||

*Příklad*

Tento příklad vrátí číslo pro den v týdnu od tohoto časového razítka:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

A vrátí tento výsledek:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>Dayofyear

Vrátit den v roce z časového razítka.

```
dayOfYear('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*den v roce*> | Integer | Den v roce od zadaného časového razítka |
||||

*Příklad*

Tento příklad vrátí číslo dne v roce od tohoto časového razítka:

```
dayOfYear('2018-03-15T13:27:36Z')
```

A vrátí tento výsledek:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Vraťte verzi řetězce pro řetězec kódovaný base64, který účinně dekóduje řetězec base64.
Zvažte použití [base64ToString()](#base64ToString) spíše než `decodeBase64()`.
Ačkoli obě funkce fungují `base64ToString()` stejným způsobem, je upřednostňována.

```
decodeBase64('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec kódovaný base64 k dekódování |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*dekódovaný base64-string*> | Řetězec | Verze řetězce pro řetězec kódovaný base64 |
||||

*Příklad*

Tento příklad vytvoří řetězec pro řetězec kódovaný base64:

```
decodeBase64('aGVsbG8=')
```

A vrátí tento výsledek:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Vrátí binární verzi pro identifikátor jednotného prostředku dat (URI).
Zvažte použití [dataUriToBinary()](#dataUriToBinary), spíše než `decodeDataUri()`.
Ačkoli obě funkce fungují `dataUriToBinary()` stejným způsobem, je upřednostňována.

```
decodeDataUri('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec identifikátoru URI dat, který se má dekódovat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*binární-pro-data-uri*> | Řetězec | Binární verze řetězce identifikátoru URI dat |
||||

*Příklad*

Tento příklad vrátí binární verzi pro tento identifikátor URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí tento výsledek:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Vrátí řetězec, který nahradí řídicí znaky dekódované verze.

```
decodeUriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec s řídicími znaky k dekódování |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*dekódovaný-uri*> | Řetězec | Aktualizovaný řetězec s dekódované řídicí znaky |
||||

*Příklad*

Tento příklad nahradí řídicí znaky v tomto řetězci dekódované verze:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

A vrátí tento výsledek:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Vrátí výsledek celého čísla z dělení dvou čísel.
Chcete-li získat zbývající výsledek, viz [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Dividenda*> | Ano | Celé číslo nebo plovoucí | Číslo, které má *dělitel dělitelem* |
| <*Dělitel*> | Ano | Celé číslo nebo plovoucí | Číslo, které vydělí *dividendu*, ale nemůže být 0 |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*kvocient-výsledek*> | Integer | Výsledek celého čísla z vydělení prvního čísla druhým číslem |
||||

*Příklad*

Oba příklady vydělují první číslo druhým číslem:

```
div(10, 5)
div(11, 5)
```

A vrátit tento výsledek:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>kódUriComponent

Vrátí adresu uri kódované verze identifikátoru (URI) pro řetězec nahrazením znaků nebezpečných adres URL řídicími znaky.
Zvažte použití [uriComponent()](#uriComponent), spíše než `encodeUriComponent()`.
Ačkoli obě funkce fungují `uriComponent()` stejným způsobem, je upřednostňována.

```
encodeUriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec, který chcete převést do formátu kódovaného identifikátorem URI |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*kódovaný-uri*> | Řetězec | Řetězec kódovaný identifikátorem URI s řídicími znaky |
||||

*Příklad*

Tento příklad vytvoří verzi kódovku URI pro tento řetězec:

```
encodeUriComponent('https://contoso.com')
```

A vrátí tento výsledek:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Zkontrolujte, zda je kolekce prázdná.
Vrátit true při kolekci je prázdný nebo vrátit false, pokud není prázdný.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Kolekce*> | Ano | Řetězec, pole nebo objekt | Kolekce ke kontrole |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátit true, pokud je kolekce prázdná. Vrátit false, když není prázdný. |
||||

*Příklad*

Tyto příklady zkontrolují, zda jsou zadané kolekce prázdné:

```
empty('')
empty('abc')
```

A vrátí tyto výsledky:

* První příklad: Předá prázdný řetězec, `true`takže funkce vrátí .
* Druhý příklad: Předá řetězec "abc", `false`takže funkce vrátí .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Zkontrolujte, zda řetězec končí určitým podřetězcem.
Vrátí hodnotu true, pokud je podřetězec nalezen, nebo vrátí hodnotu false, pokud nebyl nalezen.
Tato funkce není rozlišována malá a velká písmena.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec ke kontrole |
| <*searchText*> | Ano | Řetězec | Koncový podřetězec, který chcete najít |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false  | Logická hodnota | Vrátí hodnotu true, když je nalezen koncový podřetězec. Vrátit false, pokud nebyl nalezen. |
||||

*Příklad 1*

Tento příklad zkontroluje, zda řetězec "hello world" končí řetězcem "svět":

```
endsWith('hello world', 'world')
```

A vrátí tento výsledek:`true`

*Příklad 2*

Tento příklad zkontroluje, zda řetězec "hello world" končí řetězcem "universe":

```
endsWith('hello world', 'universe')
```

A vrátí tento výsledek:`false`

<a name="equals"></a>

### <a name="equals"></a>equals

Zkontrolujte, zda jsou obě hodnoty, výrazy nebo objekty ekvivalentní.
Vrátit true, pokud jsou ekvivalentní, nebo vrátit false, pokud nejsou ekvivalentní.

```
equals('<object1>', '<object2>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*objekt2*> | Ano | Různých | Hodnoty, výrazy nebo objekty, které chcete porovnat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátit true, pokud jsou oba ekvivalentní. Vrátit false, pokud není ekvivalentní. |
||||

*Příklad*

Tyto příklady zkontrolují, zda zadané vstupy jsou ekvivalentní.

```
equals(true, 1)
equals('abc', 'abcd')
```

A vrátí tyto výsledky:

* První příklad: Obě hodnoty jsou ekvivalentní, takže funkce vrátí `true`.
* Druhý příklad: Obě hodnoty nejsou ekvivalentní, `false`takže funkce vrátí .

<a name="first"></a>

### <a name="first"></a>První

Vrátí první položku z řetězce nebo pole.

```
first('<collection>')
first([<collection>])
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Kolekce*> | Ano | Řetězec nebo pole | Kolekce, kde najít první položku |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*první položka sběru*> | Všechny | První položka v kolekci |
||||

*Příklad*

Tyto příklady najdou první položku v těchto kolekcích:

```
first('hello')
first(createArray(0, 1, 2))
```

A vrátit tyto výsledky:

* První příklad:`"h"`
* Druhý příklad:`0`

<a name="float"></a>

### <a name="float"></a>float

Převeďte verzi řetězce pro číslo s plovoucí desetinnou tácem na skutečné číslo s plovoucí desetinnou tácem.
Tuto funkci můžete použít pouze při předávání vlastní parametry do aplikace, například aplikace logiky nebo toku.

```
float('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec, který má platné číslo s plovoucí desetinnou tácek převést |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*float-hodnota*> | Plovoucí desetinná čárka | Číslo s plovoucí desetinnou tázkou pro zadaný řetězec |
||||

*Příklad*

Tento příklad vytvoří verzi řetězce pro toto číslo s plovoucí desetinnou táhovkou:

```
float('10.333')
```

A vrátí tento výsledek:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>Formatdatetime

Vrátí časové razítko v zadaném formátu.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*přeformátované časové razítko*> | Řetězec | Aktualizované časové razítko v určeném formátu |
||||

*Příklad*

Tento příklad převede časové razítko na zadaný formát:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

A vrátí tento výsledek:`"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Vrátí pole s hodnotami, které odpovídají názvu klíče ve *formulářovém nebo* *formulářově kódovaném* výstupu akce.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Actionname*> | Ano | Řetězec | Akce, jejíž výstup má požadovanou hodnotu klíče |
| <*Klíč*> | Ano | Řetězec | Název klíče, jehož hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| [<*> pole s hodnotami klíče]* | Pole | Pole se všemi hodnotami, které odpovídají zadanému klíči |
||||

*Příklad*

Tento příklad vytvoří pole z hodnoty klíče Předmět ve výstupu zadaných dat o formuláři nebo ve formuláři:

```
formDataMultiValues('Send_an_email', 'Subject')
```

A vrátí text předmětu v poli, například:`["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Vrátí jednu hodnotu, která odpovídá názvu klíče ve *formulářovém nebo* *formulářově kódovaném* výstupu akce.
Pokud funkce najde více než jednu shodu, funkce vyvolá chybu.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Actionname*> | Ano | Řetězec | Akce, jejíž výstup má požadovanou hodnotu klíče |
| <*Klíč*> | Ano | Řetězec | Název klíče, jehož hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*klíč-hodnota*> | Řetězec | Hodnota v zadaném klíči  |
||||

*Příklad*

Tento příklad vytvoří řetězec z hodnoty klíče Předmět ve výstupu zadaných dat o formuláři nebo ve formuláři:

```
formDataValue('Send_an_email', 'Subject')
```

A vrátí text předmětu jako řetězec, například:`"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Vrátí číslo jako řetězec, který je založen na zadaném formátu.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Číslo*> | Ano | Celé číslo nebo dvojité | Hodnota, kterou chcete formátovat. |
| <*Formát*> | Ano | Řetězec | Složený formátovací řetězec, který určuje formát, který chcete použít. Podporované číselné formátovací řetězce naleznete v tématu Standardní `number.ToString(<format>, <locale>)` [číselné formátovací řetězce](https://docs.microsoft.com/dotnet/standard/base-types/standard-numeric-format-strings), které podporují . |
| <*Národní prostředí*> | Ne | Řetězec | Národní prostředí, které chcete `number.ToString(<format>, <locale>)`použít, jak je podporováno programem . Pokud není zadán, výchozí `en-us`hodnota je . |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*formátované číslo*> | Řetězec | Zadané číslo jako řetězec ve zadaném formátu. Tuto vrácenou hodnotu `int` můžete `float`přetypovat na hodnotu nebo . |
||||

*Příklad 1*

Předpokládejme, že chcete `1234567890`číslo naformátovat . Tento příklad zformátuje toto číslo jako řetězec "1,234,567,890.00".

```
formatNumber(1234567890, '{0:0,0.00}', 'en-us')
```

*Příklad 2"

Předpokládejme, že chcete `1234567890`číslo naformátovat . Tento příklad zformátuje číslo na řetězec "1.234.567.890,00".

```
formatNumber(1234567890, '{0:0,0.00}', 'is-is')
```

*Příklad 3*

Předpokládejme, že chcete `17.35`číslo naformátovat . Tento příklad zformátuje číslo na řetězec "$17.35".

```
formatNumber(17.36, 'C2')
```

*Příklad 4*

Předpokládejme, že chcete `17.35`číslo naformátovat . Tento příklad zformátuje číslo na řetězec "17,35 kr".

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Vrátí aktuální časové razítko plus zadané časové jednotky.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Interval*> | Ano | Integer | Počet určených časových jednotek, které chcete přidat |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, který je třeba použít s *intervalem:*"Druhý", "Minute", "Hodina", "Den", "Týden", "Měsíc", "Rok" |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Aktuální časové razítko plus zadaný počet časových jednotek |
||||

*Příklad 1*

Předpokládejme, že aktuální časové razítko je "2018-03-01T00:00.00.0000000Z".
Tento příklad přidá k tomuto časovému razítku pět dní:

```
getFutureTime(5, 'Day')
```

A vrátí tento výsledek:`"2018-03-06T00:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že aktuální časové razítko je "2018-03-01T00:00.00.0000000Z".
Tento příklad přidá pět dní a převede výsledek do formátu "D":

```
getFutureTime(5, 'Day', 'D')
```

A vrátí tento výsledek:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Vrátí aktuální časové razítko mínus zadané časové jednotky.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Interval*> | Ano | Integer | Počet zadaných časových jednotek, které mají být odečteny |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, který je třeba použít s *intervalem:*"Druhý", "Minute", "Hodina", "Den", "Týden", "Měsíc", "Rok" |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Aktuální časové razítko mínus zadaný počet časových jednotek |
||||

*Příklad 1*

Předpokládejme, že aktuální časové razítko je "2018-02-01T00:00.00.0000000Z".
Tento příklad odečte pět dní od tohoto časového razítka:

```
getPastTime(5, 'Day')
```

A vrátí tento výsledek:`"2018-01-27T00:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že aktuální časové razítko je "2018-02-01T00:00.00.0000000Z".
Tento příklad odečte pět dní a převede výsledek na formát "D":

```
getPastTime(5, 'Day', 'D')
```

A vrátí tento výsledek:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Zkontrolujte, zda je první hodnota větší než druhá hodnota.
Vrátit true, když první hodnota je více, nebo vrátit false, pokud méně.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Celé číslo, plovoucí nebo řetězec | První hodnota pro kontrolu, zda větší než druhá hodnota |
| <*Compareto*> | Ano | Celé číslo, plovoucí nebo řetězec, resp. | Porovnávací hodnota |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud je první hodnota větší než druhá hodnota. Vrátí hodnotu false, pokud je první hodnota rovna nebo menší než druhá hodnota. |
||||

*Příklad*

Tyto příklady zkontrolují, zda je první hodnota větší než druhá hodnota:

```
greater(10, 5)
greater('apple', 'banana')
```

A vrátit tyto výsledky:

* První příklad:`true`
* Druhý příklad:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Zkontrolujte, zda je první hodnota větší nebo rovna druhé hodnotě.
Vrátí hodnotu true, pokud je první hodnota větší nebo rovna, nebo vraťte hodnotu false, pokud je první hodnota menší.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Celé číslo, plovoucí nebo řetězec | První hodnota pro kontrolu, zda je větší nebo rovna druhé hodnotě |
| <*Compareto*> | Ano | Celé číslo, plovoucí nebo řetězec, resp. | Porovnávací hodnota |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud je první hodnota větší nebo rovna druhé hodnotě. Vrátí hodnotu false, pokud je první hodnota menší než druhá hodnota. |
||||

*Příklad*

Tyto příklady zkontrolují, zda je první hodnota větší nebo rovna druhé hodnotě:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

A vrátit tyto výsledky:

* První příklad:`true`
* Druhý příklad:`false`

<a name="guid"></a>

### <a name="guid"></a>Identifikátor guid

Generovat globálně jedinečný identifikátor (GUID) jako řetězec, například "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Můžete také zadat jiný formát identifikátoru GUID, než je výchozí formát "D", což je 32 číslic oddělených spojovníky.

```
guid('<format>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Formát*> | Ne | Řetězec | Jeden [specifikátor formátu](https://msdn.microsoft.com/library/97af8hh4) pro vrácený identifikátor GUID. Ve výchozím nastavení je formát "D", ale můžete použít "N", "D", "B", "P" nebo "X". |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*Hodnota GUID*> | Řetězec | Náhodně generovaný identifikátor GUID |
||||

*Příklad*

Tento příklad generuje stejný identifikátor GUID, ale jako 32 číslic, oddělených spojovníky a uzavřených v závorcích:

```
guid('P')
```

A vrátí tento výsledek:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Zkontrolujte, zda je výraz pravdivý nebo nepravdivý.
Na základě výsledku vrátí zadanou hodnotu.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Výraz*> | Ano | Logická hodnota | Výraz, který chcete zkontrolovat |
| <*valueIfTrue*> | Ano | Všechny | Hodnota, která má být vrácena, pokud je výraz pravdivý |
| <*valueIfFalse*> | Ano | Všechny | Hodnota, která má být vrácena, pokud je výraz false |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*zadaná vrácená hodnota*> | Všechny | Zadaná hodnota, která vrací na základě toho, zda je výraz pravdivý nebo nepravdivý |
||||

*Příklad*

Tento příklad `"yes"` vrátí, protože zadaný výraz vrátí true.
V opačném případě `"no"`vrátí příklad :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>Indexof

Vrátí počáteční pozici nebo hodnotu indexu pro podřetězec.
Tato funkce nerozlišuje malá a velká písmena a indexy začínají číslem 0.

```
indexOf('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec, který má podřetězec najít |
| <*searchText*> | Ano | Řetězec | Podřetězec, který chcete najít |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*index-hodnota*>| Integer | Počáteční pozice nebo hodnota indexu pro zadaný podřetězec. <p>Pokud řetězec nebyl nalezen, vraťte číslo -1. |
||||

*Příklad*

Tento příklad vyhledá počáteční hodnotu indexu pro podřetězec "svět" v řetězci "hello world":

```
indexOf('hello world', 'world')
```

A vrátí tento výsledek:`6`

<a name="int"></a>

### <a name="int"></a>int

Vraťte verzi celého čísla pro řetězec.

```
int('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec, který chcete převést |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*celočíselný výsledek*> | Integer | Integer verze pro zadaný řetězec |
||||

*Příklad*

Tento příklad vytvoří celou verzi řetězce "10":

```
int('10')
```

A vrátí tento výsledek:`10`

<a name="item"></a>

### <a name="item"></a>položka

Při použití uvnitř opakující se akce přes pole, vrátit aktuální položku v poli během aktuální iterace akce.
Můžete také získat hodnoty z vlastností této položky.

```
item()
```

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*položka aktuálního pole*> | Všechny | Aktuální položka v poli pro aktuální iteraci akce |
||||

*Příklad*

Tento příklad `body` získá prvek z aktuální zprávy pro akci "Send_an_email" uvnitř aktuální iterace každé smyčky:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>Položky

Vrátí aktuální položku z každého cyklu ve smyčce for-each.
Použijte tuto funkci uvnitř smyčky pro každý.

```
items('<loopName>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*název smyčky*> | Ano | Řetězec | Název smyčky for-each |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*Položky*> | Všechny | Položka z aktuálního cyklu v zadané smyčce pro každou |
||||

*Příklad*

Tento příklad získá aktuální položku ze zadané smyčky pro každý:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Vrátí hodnotu indexu pro aktuální iteraci uvnitř Until smyčky. Tuto funkci můžete použít uvnitř vnořené Do smyčky. 

```
iterationIndexes('<loopName>')
```

| Parametr | Požaduje se | Typ | Popis | 
| --------- | -------- | ---- | ----------- | 
| <*název smyčky*> | Ano | Řetězec | Název smyčky Do | 
||||| 

| Návratová hodnota | Typ | Popis | 
| ------------ | ---- | ----------- | 
| <*Index*> | Integer | Hodnota indexu pro aktuální iteraci uvnitř zadané smyčky Until | 
|||| 

*Příklad* 

Tento příklad vytvoří proměnnou čítače a zvyšuje tuto proměnnou o jednu během každé iterace ve smyčce Until, dokud hodnota čítače nedosáhne pěti. Příklad také vytvoří proměnnou, která sleduje aktuální index pro každou iteraci. Ve smyčce Until se během každé iterace v příkladu nastoupí čítač a potom přiřadí hodnotu čítače aktuální hodnotě indexu a pak čítač znásobí. Kdykoli můžete určit aktuální číslo iterace načtením aktuální hodnoty indexu.

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5),
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Vraťte hodnotu nebo objekt typu Zápis objektu JavaScriptu (JSON) pro řetězec nebo XML.

```
json('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec nebo XML | Řetězec nebo XML, které chcete převést |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*Výsledek JSON*> | Nativní typ nebo objekt JSON | Hodnota nebo objekt nativního typu JSON pro zadaný řetězec nebo XML. Pokud je řetězec null, funkce vrátí prázdný objekt. |
||||

*Příklad 1*

Tento příklad převede tento řetězec na hodnotu JSON:

```
json('[1, 2, 3]')
```

A vrátí tento výsledek:`[1, 2, 3]`

*Příklad 2*

Tento příklad převede tento řetězec na JSON:

```
json('{"fullName": "Sophia Owen"}')
```

A vrátí tento výsledek:

```
{
  "fullName": "Sophia Owen"
}
```

*Příklad 3*

Tento příklad převede tento XML na JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

A vrátí tento výsledek:

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

### <a name="intersection"></a>Průsečíku

Vrátí kolekci, která má *pouze* běžné položky v rámci zadané kolekce.
Chcete-li zobrazit ve výsledku, musí se zobrazit položka ve všech kolekcích předaných této funkci.
Pokud má jedna nebo více položek stejný název, zobrazí se ve výsledku poslední položka s tímto názvem.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*kolekce1*>, <*kolekce2*>, ... | Ano | Pole nebo objekt, ale ne obojí | Kolekce, ze kterých chcete *pouze* běžné položky |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*běžné položky*> | Pole nebo objekt, resp. | Kolekce, která má pouze běžné položky v rámci zadané kolekce |
||||

*Příklad*

Tento příklad vyhledá běžné položky v těchto polích:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

A vrátí pole *pouze* s těmito položkami:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Vrátí řetězec, který má všechny položky z pole a má každý znak oddělený *oddělovač*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Kolekce*> | Ano | Pole | Pole, které má položky ke spojení |
| <*Oddělovač*> | Ano | Řetězec | Oddělovač, který se zobrazí mezi jednotlivými znaky ve výsledném řetězci |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*char1*><*oddělovač*><*char2*><*oddělovač*>... | Řetězec | Výsledný řetězec vytvořený ze všech položek v zadaném poli |
||||

*Příklad*

Tento příklad vytvoří řetězec ze všech položek v tomto poli se zadaným znakem jako oddělovač:

```
join(createArray('a', 'b', 'c'), '.')
```

A vrátí tento výsledek:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>poslední

Vrátí poslední položku z kolekce.

```
last('<collection>')
last([<collection>])
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Kolekce*> | Ano | Řetězec nebo pole | Kolekce, kde najít poslední položku |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*poslední položka kolekce*> | Řetězec nebo pole, resp. | Poslední položka v kolekci |
||||

*Příklad*

Tyto příklady naleznou poslední položku v těchto kolekcích:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

A vrátí tyto výsledky:

* První příklad:`"d"`
* Druhý příklad:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>Lastindexof

Vrátí počáteční pozici nebo hodnotu indexu pro poslední výskyt podřetězce.
Tato funkce nerozlišuje malá a velká písmena a indexy začínají číslem 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec, který má podřetězec najít |
| <*searchText*> | Ano | Řetězec | Podřetězec, který chcete najít |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*koncová hodnota indexu*> | Integer | Počáteční pozice nebo hodnota indexu pro poslední výskyt zadaného podřetězce. <p>Pokud řetězec nebyl nalezen, vraťte číslo -1. |
||||

*Příklad*

Tento příklad vyhledá počáteční hodnotu indexu pro poslední výskyt podřetězce "svět" v řetězci "hello world":

```
lastIndexOf('hello world', 'world')
```

A vrátí tento výsledek:`6`

<a name="length"></a>

### <a name="length"></a>length

Vrátí počet položek v kolekci.

```
length('<collection>')
length([<collection>])
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Kolekce*> | Ano | Řetězec nebo pole | Kolekce s položkami, které mají být počítány |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*délka nebo počet*> | Integer | Počet položek v kolekci |
||||

*Příklad*

Tyto příklady počítají počet položek v těchto kolekcích:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

A vrátit tento výsledek:`4`

<a name="less"></a>

### <a name="less"></a>less

Zkontrolujte, zda je první hodnota menší než druhá hodnota.
Vrátit true, pokud je první hodnota menší, nebo vrátit false, pokud je první hodnota více.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Celé číslo, plovoucí nebo řetězec | První hodnota pro kontrolu, zda je menší než druhá hodnota |
| <*Compareto*> | Ano | Celé číslo, plovoucí nebo řetězec, resp. | Položka porovnání |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud je první hodnota menší než druhá hodnota. Vrátí hodnotu false, pokud je první hodnota rovna nebo větší než druhá hodnota. |
||||

*Příklad*

Tyto příklady zkontrolují, zda je první hodnota menší než druhá hodnota.

```
less(5, 10)
less('banana', 'apple')
```

A vrátit tyto výsledky:

* První příklad:`true`
* Druhý příklad:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Zkontrolujte, zda je první hodnota menší nebo rovna druhé hodnotě.
Vrátit true, pokud je první hodnota menší než nebo rovna, nebo vrátit false, pokud je první hodnota více.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Celé číslo, plovoucí nebo řetězec | První hodnota pro kontrolu, zda je menší nebo rovna druhé hodnotě |
| <*Compareto*> | Ano | Celé číslo, plovoucí nebo řetězec, resp. | Položka porovnání |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false  | Logická hodnota | Vrátí hodnotu true, pokud je první hodnota menší nebo rovna druhé hodnotě. Vrátí false, pokud je první hodnota větší než druhá hodnota. |
||||

*Příklad*

Tyto příklady zkontrolují, zda je první hodnota menší nebo rovna jako druhá hodnota.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

A vrátit tyto výsledky:

* První příklad:`true`
* Druhý příklad:`false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Vraťte "adresu URL zpětného volání", která volá aktivační událost nebo akci.
Tato funkce funguje pouze s aktivačními událostmi a akcemi pro typy konektorů **HttpWebhook** a **ApiConnectionWebhook,** ale ne s typy **Ruční**, **Opakování**, **HTTP**a **APIConnection.**

```
listCallbackUrl()
```

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*adresa URL zpětného volání*> | Řetězec | Adresa URL zpětného volání pro aktivační událost nebo akci |
||||

*Příklad*

Tento příklad ukazuje ukázkovou adresu URL zpětného volání, kterou může tato funkce vrátit:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Vrátí nejvyšší hodnotu ze seznamu nebo pole s čísly, která je včetně na obou koncích.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*číslo1*>, <*číslo2*>, ... | Ano | Celé číslo, Plovoucí nebo obojí | Sada čísel, ze kterých chcete nejvyšší hodnotu |
| [<*číslo1*>, <*číslo2*>, ...] | Ano | Pole – celé číslo, plovoucí nebo obojí | Pole čísel, ze kterých chcete nejvyšší hodnotu |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*maximální hodnota*> | Celé číslo nebo plovoucí | Nejvyšší hodnota v zadaném poli nebo sadě čísel |
||||

*Příklad*

Tyto příklady získat nejvyšší hodnotu ze sady čísel a pole:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

A vrátit tento výsledek:`3`

<a name="min"></a>

### <a name="min"></a>min

Vrátí nejnižší hodnotu ze sady čísel nebo pole.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*číslo1*>, <*číslo2*>, ... | Ano | Celé číslo, Plovoucí nebo obojí | Sada čísel, ze kterých chcete nejnižší hodnotu |
| [<*číslo1*>, <*číslo2*>, ...] | Ano | Pole – celé číslo, plovoucí nebo obojí | Pole čísel, ze kterých chcete nejnižší hodnotu |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*min-hodnota*> | Celé číslo nebo plovoucí | Nejnižší hodnota v zadané sadě čísel nebo zadaného pole |
||||

*Příklad*

Tyto příklady získat nejnižší hodnotu v sadě čísel a pole:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

A vrátit tento výsledek:`1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Vraťte zbytek z dělení dvou čísel.
Chcete-li získat výsledek celé číslo, viz [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Dividenda*> | Ano | Celé číslo nebo plovoucí | Číslo, které má *dělitel dělitelem* |
| <*Dělitel*> | Ano | Celé číslo nebo plovoucí | Číslo, které rozdělí *dividendu*, ale nemůže být 0. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*modulo-výsledek*> | Celé číslo nebo plovoucí | Zbytek od vydělení prvního čísla druhým číslem |
||||

*Příklad*

Tento příklad vydělí první číslo druhým číslem:

```
mod(3, 2)
```

A vrátit tento výsledek:`1`

<a name="mul"></a>

### <a name="mul"></a>mul

Vraťte produkt z násobení dvou čísel.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Ano | Celé číslo nebo plovoucí | Číslo vynásobené *multiplicand2* |
| <*multiplicand2*> | Ano | Celé číslo nebo plovoucí | Číslo, které násobky *multiplicand1* |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*výsledek produktu*> | Celé číslo nebo plovoucí | Produkt z vynásobení prvního čísla druhým číslem |
||||

*Příklad*

Tyto příklady násobí první číslo druhým číslem:

```
mul(1, 2)
mul(1.5, 2)
```

A vrátit tyto výsledky:

* První příklad:`2`
* Druhý příklad`3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>vícedílná tělo

Vrátí tělo pro konkrétní část ve výstupu akce, která má více částí.

```
multipartBody('<actionName>', <index>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Actionname*> | Ano | Řetězec | Název akce, která má výstup s více částmi |
| <*Index*> | Ano | Integer | Hodnota indexu pro požadovanou součást |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*Tělo*> | Řetězec | Tělo pro zadaný díl |
||||

<a name="not"></a>

### <a name="not"></a>not

Zkontrolujte, zda je výraz false.
Vrátit true, pokud je výraz false, nebo vrátit false, pokud true.

```json
not(<expression>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Výraz*> | Ano | Logická hodnota | Výraz, který chcete zkontrolovat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud je výraz nepravdivý. Vrátit false, pokud je výraz pravdivý. |
||||

*Příklad 1*

Tyto příklady zkontrolují, zda zadané výrazy jsou false:

```json
not(false)
not(true)
```

A vrátit tyto výsledky:

* První příklad: Výraz je false, `true`takže funkce vrátí .
* Druhý příklad: Výraz je true, `false`takže funkce vrátí .

*Příklad 2*

Tyto příklady zkontrolují, zda zadané výrazy jsou false:

```json
not(equals(1, 2))
not(equals(1, 1))
```

A vrátit tyto výsledky:

* První příklad: Výraz je false, `true`takže funkce vrátí .
* Druhý příklad: Výraz je true, `false`takže funkce vrátí .

<a name="or"></a>

### <a name="or"></a>– nebo –

Zkontrolujte, zda je pravdivý alespoň jeden výraz.
Vrátit true, pokud alespoň jeden výraz je true, nebo vrátit false, pokud jsou všechny false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Ano | Logická hodnota | Výrazy, které chcete zkontrolovat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud je splněn alespoň jeden výraz. Vrátit false, pokud jsou všechny výrazy false. |
||||

*Příklad 1*

V těchto příkladech zkontrolujte, zda je splněn alespoň jeden výraz:

```json
or(true, false)
or(false, false)
```

A vrátit tyto výsledky:

* První příklad: Alespoň jeden výraz je true, takže funkce vrátí `true`.
* Druhý příklad: Oba výrazy jsou false, takže funkce vrátí `false`.

*Příklad 2*

V těchto příkladech zkontrolujte, zda je splněn alespoň jeden výraz:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

A vrátit tyto výsledky:

* První příklad: Alespoň jeden výraz je true, takže funkce vrátí `true`.
* Druhý příklad: Oba výrazy jsou false, takže funkce vrátí `false`.

<a name="outputs"></a>

### <a name="outputs"></a>Výstupy

Vrátí výstupy akce za běhu. Použijte tuto funkci, `actionOutputs()`nikoli , `outputs()` který řeší v Návrháři aplikace logiky. Ačkoli obě funkce fungují `outputs()` stejným způsobem, je upřednostňována.

```
outputs('<actionName>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Actionname*> | Ano | Řetězec | Název výstupu akce, který chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | -----| ----------- |
| <*Výstup*> | Řetězec | Výstup ze zadané akce |
||||

*Příklad*

Tento příklad získá výstup z `Get user`akce Twitter :

```
outputs('Get_user')
```

A vrátí tento výsledek:

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

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Vrátí hodnotu parametru, který je popsán v definici pracovního postupu.

```
parameters('<parameterName>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Parametername*> | Ano | Řetězec | Název parametru, jehož hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*hodnota parametru*> | Všechny | Hodnota pro zadaný parametr |
||||

*Příklad*

Předpokládejme, že máte tuto hodnotu JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Tento příklad získá hodnotu pro zadaný parametr:

```
parameters('fullName')
```

A vrátí tento výsledek:`"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Vrátí náhodné celé číslo ze zadaného rozsahu, který je včetně pouze na počátečním konci.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Minvalue*> | Ano | Integer | Nejnižší celé číslo v rozsahu |
| <*Maxvalue*> | Ano | Integer | Celé číslo, které následuje za nejvyšším celé číslo v rozsahu, který funkce může vrátit |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*náhodný výsledek*> | Integer | Náhodné celé číslo vrácené ze zadaného rozsahu |
||||

*Příklad*

Tento příklad získá náhodné celé číslo ze zadaného rozsahu, s výjimkou maximální hodnoty:

```
rand(1, 5)
```

A vrátí jedno z těchto `1`čísel `2` `3`jako výsledek: , , , nebo`4`

<a name="range"></a>

### <a name="range"></a>range

Vrátí celé pole, které začíná od zadaného celého čísla.

```
range(<startIndex>, <count>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Startindex*> | Ano | Integer | Celá hodnota, která spustí pole jako první položku |
| <*Počet*> | Ano | Integer | Počet celá čísla v poli |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| [<> *výsledků rozsahu]* | Pole | Pole s celámi počínaje zadaným indexem |
||||

*Příklad*

Tento příklad vytvoří celé pole, které začíná ze zadaného indexu a má zadaný počet celých čísel:

```
range(1, 4)
```

A vrátí tento výsledek:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Nahradit

Nahraďte podřetězec zadaným řetězcem a vraťte výsledný řetězec. Tato funkce rozlišuje malá a velká písmena.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec, který má podřetězec nahradit |
| <*starýText*> | Ano | Řetězec | Podřetězec, který chcete nahradit |
| <*novýtext*> | Ano | Řetězec | Náhradní řetězec |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizovaný text*> | Řetězec | Aktualizovaný řetězec po nahrazení podřetězce <p>Pokud podřetězec nebyl nalezen, vraťte původní řetězec. |
||||

*Příklad*

Tento příklad vyhledá podřetězec "old" ve starém řetězci a nahradí "old" "new":

```
replace('the old string', 'old', 'new')
```

A vrátí tento výsledek:`"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Odeberte vlastnost z objektu a vraťte aktualizovaný objekt. Pokud vlastnost, kterou se pokusíte odebrat, neexistuje, funkce vrátí původní objekt.

```
removeProperty(<object>, '<property>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ano | Objekt | Objekt JSON z místa, odkud chcete odebrat vlastnost |
| <*Vlastnost*> | Ano | Řetězec | Název vlastnosti, kterou chcete odebrat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizovaný objekt*> | Objekt | Aktualizovaný objekt JSON bez zadané vlastnosti |
||||

Chcete-li odebrat podřízenou vlastnost z existující vlastnosti, použijte tuto syntaxi:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ano | Objekt | Objekt JSON, jehož vlastnost chcete odebrat |
| <*nadřazená vlastnost*> | Ano | Řetězec | Název nadřazené vlastnosti s podřízenou vlastností, kterou chcete odebrat |
| <*dětský majetek*> | Ano | Řetězec | Název podřízené vlastnosti odebrat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizovaný objekt*> | Objekt | Aktualizovaný objekt JSON, jehož podřízená vlastnost, kterou jste odebrali |
||||

*Příklad 1*

Tento příklad odebere `middleName` vlastnost z objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON()](#json) a vrátí aktualizovaný objekt:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Zde je aktuální Objekt JSON:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Zde je aktualizovaný objekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Příklad 2*

Tento příklad odebere `middleName` podřízenou vlastnost z nadřazené `customerName` vlastnosti v objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON()](#json) a vrátí aktualizovaný objekt:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Zde je aktuální Objekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Zde je aktualizovaný objekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

Vrátí vstupy a výstupy ze všech akcí, které jsou uvnitř `For_each`zadané `Until`akce `Scope` s vymezeným oborem, například , nebo akce. Tato funkce je užitečné vrácení výsledků z neúspěšné akce, takže můžete diagnostikovat a zpracovávat výjimky. Další informace naleznete v [tématu Získání kontextu a výsledků pro chyby](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Ano | Řetězec | Název akce s vymezenou oborem, ze které chcete vrátit vstupy a výstupy ze všech vnitřních akcí |
||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*objekt pole*> | Objekt pole | Pole, které obsahuje pole vstupů a výstupů z každé akce, která se zobrazí uvnitř zadané akce s vymezeným oborem |
||||

*Příklad*

Tento příklad vrátí vstupy a výstupy z každé iterace `For_each` akce HTTP `result()` uvnitř, `Compose` která je uvnitř smyčky pomocí funkce v akci:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Zde je návod, jak může vypadat `outputs` vodné pole, kde vnější objekt obsahuje vstupy a výstupy z každé iterace akcí uvnitř `For_each` akce.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>Setproperty

Nastavte hodnotu vlastnosti objektu JSON a vraťte aktualizovaný objekt. Pokud vlastnost, kterou se pokusíte nastavit neexistuje, vlastnost získá přidána do objektu. Chcete-li přidat novou vlastnost, použijte funkci [addProperty().](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ano | Objekt | Objekt JSON, jehož vlastnost chcete nastavit |
| <*Vlastnost*> | Ano | Řetězec | Název existující nebo nové vlastnosti, kterou chcete nastavit |
| <*Hodnotu*> | Ano | Všechny | Hodnota nastavená pro zadanou vlastnost |
|||||

Chcete-li nastavit podřízenou vlastnost v `setProperty()` podřízeném objektu, použijte místo toho vnořené volání. Jinak funkce vrátí pouze podřízený objekt jako výstup.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Objekt*> | Ano | Objekt | Objekt JSON, jehož vlastnost chcete nastavit |
| <*nadřazená vlastnost*> | Ano | Řetězec | Název nadřazené vlastnosti s podřízenou vlastností, kterou chcete nastavit |
| <*dětský majetek*> | Ano | Řetězec | Název podřízené vlastnosti, která má být nastavena |
| <*Hodnotu*> | Ano | Všechny | Hodnota nastavená pro zadanou vlastnost |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizovaný objekt*> | Objekt | Aktualizovaný objekt JSON, jehož vlastnost nastavíte |
||||

*Příklad 1*

Tento příklad `surName` nastaví vlastnost v objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON().](#json) Funkce přiřadí zadané hodnotě vlastnosti a vrátí aktualizovaný objekt:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Zde je aktuální Objekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Zde je aktualizovaný objekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Příklad 2*

Tento příklad `surName` nastaví podřízenou vlastnost `customerName` nadřazené vlastnosti v objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON().](#json) Funkce přiřadí zadané hodnotě vlastnosti a vrátí aktualizovaný objekt:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Zde je aktuální Objekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Zde je aktualizovaný objekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>Přeskočit

Odeberte položky z přední části kolekce a vraťte *všechny ostatní* položky.

```
skip([<collection>], <count>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Kolekce*> | Ano | Pole | Kolekce, jejíž položky chcete odebrat |
| <*Počet*> | Ano | Integer | Kladné celé číslo pro počet položek, které mají být na přední straně odstraněny |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| [<*> aktualizované kolekce]* | Pole | Aktualizovaná kolekce po odebrání zadaných položek |
||||

*Příklad*

Tento příklad odebere jednu položku, číslo 0, z přední části zadaného pole:

```
skip(createArray(0, 1, 2, 3), 1)
```

A vrátí toto pole se zbývajícími položkami:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Vrátí pole, které obsahuje podřetězce oddělené čárkami, na základě zadaného znaku oddělovače v původním řetězci.

```
split('<text>', '<delimiter>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec, který se má rozdělit na podřetězce na základě zadaného oddělovače v původním řetězci |
| <*Oddělovač*> | Ano | Řetězec | Znak v původním řetězci, který se má použít jako oddělovač |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Pole | Pole, které obsahuje podřetězce z původního řetězce oddělené čárkami |
||||

*Příklad*

Tento příklad vytvoří pole s podřetězci ze zadaného řetězce na základě zadaného znaku jako oddělovač:

```
split('a_b_c', '_')
```

A vrátí toto pole jako výsledek:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>začátekDne

Vrátit začátek dne pro časové razítko.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Zadané časové razítko, ale počínaje značkou nula hodin pro den |
||||

*Příklad*

Tento příklad vyhledá začátek dne pro toto časové razítko:

```
startOfDay('2018-03-15T13:30:30Z')
```

A vrátí tento výsledek:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Vraťte začátek hodiny pro časové razítko.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Zadané časové razítko, ale počínaje značkou nula minut pro hodinu |
||||

*Příklad*

Tento příklad vyhledá začátek hodiny pro toto časové razítko:

```
startOfHour('2018-03-15T13:30:30Z')
```

A vrátí tento výsledek:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>začátekMěsíce

Vrátit začátek měsíce pro časové razítko.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Zadané časové razítko, ale počínaje prvním dnem v měsíci se značkou nula hodin |
||||

*Příklad*

Tento příklad vrátí začátek měsíce pro toto časové razítko:

```
startOfMonth('2018-03-15T13:30:30Z')
```

A vrátí tento výsledek:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Zkontrolujte, zda řetězec začíná určitým podřetězcem.
Vrátí hodnotu true, pokud je podřetězec nalezen, nebo vrátí hodnotu false, pokud nebyl nalezen.
Tato funkce není rozlišována malá a velká písmena.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec ke kontrole |
| <*searchText*> | Ano | Řetězec | Počáteční řetězec, který chcete najít |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| true nebo false  | Logická hodnota | Vrátí hodnotu true, když je nalezen počáteční podřetězec. Vrátit false, pokud nebyl nalezen. |
||||

*Příklad 1*

Tento příklad zkontroluje, zda řetězec "hello world" začíná podřetězcem "hello":

```
startsWith('hello world', 'hello')
```

A vrátí tento výsledek:`true`

*Příklad 2*

Tento příklad zkontroluje, zda řetězec "hello world" začíná podřetězcem "pozdravy":

```
startsWith('hello world', 'greetings')
```

A vrátí tento výsledek:`false`

<a name="string"></a>

### <a name="string"></a>řetězec

Vraťte verzi řetězce pro hodnotu.

```
string(<value>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Všechny | Hodnota, kterou chcete převést |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*hodnota řetězce*> | Řetězec | Verze řetězce pro zadanou hodnotu |
||||

*Příklad 1*

Tento příklad vytvoří verzi řetězce pro toto číslo:

```
string(10)
```

A vrátí tento výsledek:`"10"`

*Příklad 2*

Tento příklad vytvoří řetězec pro zadaný objekt JSON\\a použije znak zpětného lomítka ( ) jako řídicí znak pro dvojité uvozovky ().

```
string( { "name": "Sophie Owen" } )
```

A vrátí tento výsledek:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Dílčí

Vrátit výsledek od odečtením druhé číslo od prvního čísla.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Ano | Celé číslo nebo plovoucí | Číslo, od kterého odečíst *subtrahend* |
| <*subtrahend*> | Ano | Celé číslo nebo plovoucí | Číslo odečíst od *minuend* |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*Výsledek*> | Celé číslo nebo plovoucí | Výsledek odečtení druhého čísla od prvního čísla |
||||

*Příklad*

Tento příklad odečte druhé číslo od prvního čísla:

```
sub(10.3, .3)
```

A vrátí tento výsledek:`10`

<a name="substring"></a>

### <a name="substring"></a>Podřetězec

Vrátí znaky z řetězce, počínaje zadanou pozici nebo indexu.
Hodnoty indexu začínají číslem 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec, jehož znaky chcete |
| <*Startindex*> | Ano | Integer | Kladné číslo rovné nebo větší než 0, které chcete použít jako počáteční pozici nebo hodnotu indexu |
| <*Délka*> | Ano | Integer | Kladný počet znaků, které chcete v podřetězci |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*výsledek podřetězce*> | Řetězec | Podřetězec se zadaným počtem znaků začínající na zadané pozici indexu ve zdrojovém řetězci |
||||

*Příklad*

Tento příklad vytvoří podřetězec pěti znaků ze zadaného řetězce počínaje hodnotou indexu 6:

```
substring('hello world', 6, 5)
```

A vrátí tento výsledek:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>odečístFromTime

Odečtěte počet časových jednotek od časového razítka.
Viz také [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Interval*> | Ano | Integer | Počet zadaných časových jednotek, které mají být odečteny |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, který je třeba použít s *intervalem:*"Druhý", "Minute", "Hodina", "Den", "Týden", "Měsíc", "Rok" |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizované časové razítko*> | Řetězec | Časové razítko mínus zadaný počet časových jednotek |
||||

*Příklad 1*

Tento příklad odečte jeden den od tohoto časového razítka:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

A vrátí tento výsledek:`"2018-01-01T00:00:00:0000000Z"`

*Příklad 2*

Tento příklad odečte jeden den od tohoto časového razítka:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

A vrátí tento výsledek pomocí volitelného formátu "D":`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>vzít

Vrátit položky z přední části kolekce.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Kolekce*> | Ano | Řetězec nebo pole | Kolekce, jejíž položky chcete |
| <*Počet*> | Ano | Integer | Kladné celé číslo pro počet položek, které chcete z přední strany |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <> *> podmnožinu* nebo [<*podmnožinu]* | Řetězec nebo pole, resp. | Řetězec nebo pole, které má zadaný počet položek převzatých z přední části původní kolekce |
||||

*Příklad*

Tyto příklady získat zadaný počet položek z přední části těchto kolekcí:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

A vrátit tyto výsledky:

* První příklad:`"abc"`
* Druhý příklad:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Klíšťata

Vrátí `ticks` hodnotu vlastnosti pro zadané časové razítko.
*Klíště* je interval 100 nanosekund.

```
ticks('<timestamp>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Časové razítko*> | Ano | Řetězec | Řetězec pro časové razítko |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*číslo značek*> | Integer | Počet značek od zadaného časového razítka |
||||

<a name="toLower"></a>

### <a name="tolower"></a>Tolower

Vrátí řetězec ve formátu s malou písmena. Pokud znak v řetězci nemá malou verzi, zůstane tento znak beze změny ve vráceném řetězci.

```
toLower('<text>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec, který se má vrátit ve formátu s ložených písmeny |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*text s malá písmena*> | Řetězec | Původní řetězec ve formátu s ložených písmen |
||||

*Příklad*

Tento příklad převede tento řetězec na malá písmena:

```
toLower('Hello World')
```

A vrátí tento výsledek:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>Toupper

Vrátí řetězec ve formátu velkých písmen. Pokud znak v řetězci nemá verzi velkých písmen, zůstane tento znak ve vráceném řetězci beze změny.

```
toUpper('<text>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec, který se má vrátit ve formátu velkých písmen |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*velká písmena*> | Řetězec | Původní řetězec ve formátu velkých písmen |
||||

*Příklad*

Tento příklad převede tento řetězec na velká písmena:

```
toUpper('Hello World')
```

A vrátí tento výsledek:`"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Aktivační událost

Vrátí výstup aktivační události za běhu nebo hodnoty z jiných párů názvů a hodnot JSON, které můžete přiřadit výrazu.

* Uvnitř vstupy aktivační události tato funkce vrátí výstup z předchozího spuštění.

* Uvnitř stavu aktivační události tato funkce vrátí výstup z aktuálního spuštění.

Ve výchozím nastavení funkce odkazuje na celý objekt aktivační události, ale volitelně můžete určit vlastnost, jejíž hodnotu chcete.
Tato funkce má také k dispozici zkrácené verze, viz [triggerOutputs()](#triggerOutputs) a [triggerBody()](#triggerBody).

```
trigger()
```

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*spouštěcí výstup*> | Řetězec | Výstup z aktivační události za běhu |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Vrátí `body` výstup aktivační události za běhu.
Zkratka `trigger().outputs.body`pro .
Viz [trigger()](#trigger).

```
triggerBody()
```

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*spouštěcí tělo-výstup*> | Řetězec | Výstup `body` ze spouště |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Vrátí pole s hodnotami, které odpovídají názvu klíče ve *výstupu formuláře nebo* formuláře *kódovaném* aktivační událostí.

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Klíč*> | Ano | Řetězec | Název klíče, jehož hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| [<*> pole s hodnotami klíče]* | Pole | Pole se všemi hodnotami, které odpovídají zadanému klíči |
||||

*Příklad*

Tento příklad vytvoří pole z hodnoty klíče "feedUrl" ve výstupu formuláře rss aktivační události nebo výstupu kódovaného formuláře:

```
triggerFormDataMultiValues('feedUrl')
```

A vrátí toto pole jako příklad výsledku:`["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Vrátí řetězec s jedinou hodnotou, která odpovídá názvu klíče ve *formulářovém nebo* *formulářově kódovaném* výstupu aktivační události.
Pokud funkce najde více než jednu shodu, funkce vyvolá chybu.

```
triggerFormDataValue('<key>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Klíč*> | Ano | Řetězec | Název klíče, jehož hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*klíč-hodnota*> | Řetězec | Hodnota v zadaném klíči |
||||

*Příklad*

Tento příklad vytvoří řetězec z hodnoty klíče "feedUrl" ve výstupu formuláře rss aktivační události nebo výstupu kódovaného formuláře:

```
triggerFormDataValue('feedUrl')
```

A vrátí tento řetězec jako příklad výsledku:`"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Vrátí tělo pro konkrétní část ve výstupu aktivační události, která má více částí.

```
triggerMultipartBody(<index>)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Index*> | Ano | Integer | Hodnota indexu pro požadovanou součást |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*Tělo*> | Řetězec | Tělo určeného dílu ve vícetakpotém výstupu spouště |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Vrátí výstup aktivační události za běhu nebo hodnoty z jiných párů názvů a hodnot JSON.
Zkratka `trigger().outputs`pro .
Viz [trigger()](#trigger).

```
triggerOutputs()
```

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*spouštěcí výstup*> | Řetězec | Výstup z aktivační události za běhu  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trim

Odeberte úvodní a koncové mezery z řetězce a vraťte aktualizovaný řetězec.

```
trim('<text>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Text*> | Ano | Řetězec | Řetězec, který má úvodní a koncové mezery odebrat |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*updatedText*> | Řetězec | Aktualizovaná verze pro původní řetězec bez úvodních nebo koncových mezer |
||||

*Příklad*

Tento příklad odebere úvodní a koncové mezery z řetězce " Hello World ":

```
trim(' Hello World  ')
```

A vrátí tento výsledek:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>sjednocení

Vrátí kolekci, která má *všechny* položky ze zadaných kolekcí.
Chcete-li zobrazit ve výsledku, položka se může zobrazit v libovolné kolekci předány této funkci. Pokud má jedna nebo více položek stejný název, zobrazí se ve výsledku poslední položka s tímto názvem.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*kolekce1*>, <*kolekce2*>, ...  | Ano | Pole nebo objekt, ale ne obojí | Kolekce, odkud chcete *všechny* položky |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktualizovánoKolekce*> | Pole nebo objekt, resp. | Kolekce se všemi položkami ze zadaných kolekcí - žádné duplikáty |
||||

*Příklad*

Tento příklad získá *všechny* položky z těchto kolekcí:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

A vrátí tento výsledek:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Vrátí adresu uri kódované verze identifikátoru (URI) pro řetězec nahrazením znaků nebezpečných adres URL řídicími znaky.
Použijte tuto funkci spíše než [encodeUriComponent()](#encodeUriComponent).
Ačkoli obě funkce fungují `uriComponent()` stejným způsobem, je upřednostňována.

```
uriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec, který chcete převést do formátu kódovaného identifikátorem URI |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*kódovaný-uri*> | Řetězec | Řetězec kódovaný identifikátorem URI s řídicími znaky |
||||

*Příklad*

Tento příklad vytvoří verzi kódovku URI pro tento řetězec:

```
uriComponent('https://contoso.com')
```

A vrátí tento výsledek:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Vraťte binární verzi pro součást identifikátoru URI (uniformní ho dispozičním kódem).

```
uriComponentToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec kódovaný identifikátorem URI, který chcete převést |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*binární kódovaný identifikátor URI*> | Řetězec | Binární verze pro řetězec kódovaný identifikátorem URI. Binární obsah je base64 kódován `$content`a reprezentován . |
||||

*Příklad*

Tento příklad vytvoří binární verzi pro tento řetězec kódovaný identifikátorem URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

A vrátí tento výsledek:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Vraťte verzi řetězce pro kódovaný řetězec identifikátoru URI (uniformní identifikátor) a účinně dekódujte řetězec kódovaný identifikátorem URI.

```
uriComponentToString('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec kódovaný identifikátorem URI, který má být dekódován |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*dekódovaný-uri*> | Řetězec | Dekódovaná verze řetězce kódovaného identifikátorem URI |
||||

*Příklad*

Tento příklad vytvoří dekódovanou verzi řetězce pro tento řetězec kódovaný identifikátorem URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

A vrátí tento výsledek:`"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Vrátí `host` hodnotu pro identifikátor jednotného prostředku (URI).

```
uriHost('<uri>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ano | Řetězec | Identifikátor URI, jehož `host` požadovanou hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*hodnota hostitele*> | Řetězec | Hodnota `host` pro zadaný identifikátor URI |
||||

*Příklad*

Tento příklad `host` vyhledá hodnotu pro tento identifikátor URI:

```
uriHost('https://www.localhost.com:8080')
```

A vrátí tento výsledek:`"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Vrátí `path` hodnotu pro identifikátor jednotného prostředku (URI).

```
uriPath('<uri>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ano | Řetězec | Identifikátor URI, jehož `path` požadovanou hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*cesta-hodnota*> | Řetězec | Hodnota `path` pro zadaný identifikátor URI. Pokud `path` nemá hodnotu, vrátí znak "/". |
||||

*Příklad*

Tento příklad `path` vyhledá hodnotu pro tento identifikátor URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí tento výsledek:`"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Vrátí `path` hodnoty `query` a pro jednotný identifikátor prostředku (URI).

```
uriPathAndQuery('<uri>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ano | Řetězec | Identifikátor URI, jehož `path` a `query` hodnoty chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*path-query-value*> | Řetězec | Hodnoty `path` `query` a pro zadaný identifikátor URI. Pokud `path` nezadává hodnotu, vraťte znak "/". |
||||

*Příklad*

Tento příklad `path` vyhledá `query` hodnoty a pro tento identifikátor URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí tento výsledek:`"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Vrátí `port` hodnotu pro identifikátor jednotného prostředku (URI).

```
uriPort('<uri>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ano | Řetězec | Identifikátor URI, jehož `port` požadovanou hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*hodnota portu*> | Integer | Hodnota `port` pro zadaný identifikátor URI. Pokud `port` nezadává hodnotu, vraťte výchozí port protokolu. |
||||

*Příklad*

Tento příklad `port` vrátí hodnotu pro tento identifikátor URI:

```
uriPort('http://www.localhost:8080')
```

A vrátí tento výsledek:`8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Vrátí `query` hodnotu pro identifikátor jednotného prostředku (URI).

```
uriQuery('<uri>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ano | Řetězec | Identifikátor URI, jehož `query` požadovanou hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*hodnota dotazu*> | Řetězec | Hodnota `query` pro zadaný identifikátor URI |
||||

*Příklad*

Tento příklad `query` vrátí hodnotu pro tento identifikátor URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí tento výsledek:`"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uchvace

Vrátí `scheme` hodnotu pro identifikátor jednotného prostředku (URI).

```
uriScheme('<uri>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Uri*> | Ano | Řetězec | Identifikátor URI, jehož `scheme` požadovanou hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*hodnota schématu*> | Řetězec | Hodnota `scheme` pro zadaný identifikátor URI |
||||

*Příklad*

Tento příklad `scheme` vrátí hodnotu pro tento identifikátor URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí tento výsledek:`"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Vrátí aktuální časové razítko.

```
utcNow('<format>')
```

Volitelně můžete zadat jiný formát s parametrem <*formátu*>.


| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Formát*> | Ne | Řetězec | Specifikátor [jednoho formátu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) nebo vlastní vzor [formátu](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát časového razítka je ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), který splňuje [normu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a zachová informace o časovém pásmu. |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*aktuální časové razítko*> | Řetězec | Aktuální datum a čas |
||||

*Příklad 1*

Předpokládejme, že dnes je duben 15, 2018 v 1:00:00 PM.
Tento příklad získá aktuální časové razítko:

```
utcNow()
```

A vrátí tento výsledek:`"2018-04-15T13:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že dnes je duben 15, 2018 v 1:00:00 PM.
Tento příklad získá aktuální časové razítko pomocí volitelného formátu "D":

```
utcNow('D')
```

A vrátí tento výsledek:`"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Proměnné

Vrátí hodnotu pro zadanou proměnnou.

```
variables('<variableName>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Ano | Řetězec | Název proměnné, jejíž hodnotu chcete |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*proměnná-hodnota*> | Všechny | Hodnota pro zadanou proměnnou |
||||

*Příklad*

Předpokládejme, že aktuální hodnota proměnné "numItems" je 20.
Tento příklad získá hodnotu celé číslo pro tuto proměnnou:

```
variables('numItems')
```

A vrátí tento výsledek:`20`

<a name="workflow"></a>

### <a name="workflow"></a>pracovní postup

Vrátí všechny podrobnosti o samotném pracovním postupu během běhu.

```
workflow().<property>
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Vlastnost*> | Ne | Řetězec | Název vlastnosti pracovního postupu, jejíž hodnotu chcete <p>Objekt pracovního postupu má tyto vlastnosti: **název**, **typ**, **id**, **umístění**a **spuštění**. Hodnota vlastnosti **run** je také objekt, který má tyto vlastnosti: **name**, **type**a **id**. |
|||||

*Příklad*

Tento příklad vrátí název aktuálního spuštění pracovního postupu:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Vraťte verzi XML pro řetězec, který obsahuje objekt JSON.

```
xml('<value>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Hodnotu*> | Ano | Řetězec | Řetězec s objektem JSON převést <p>Objekt JSON musí mít pouze jednu kořenovou vlastnost, která nemůže být pole. <br>Použijte znak zpětného\\lomítka ( ) jako řídicí znak pro dvojité uvozovky ("). |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*xml-verze*> | Objekt | Kódovaný kód XML pro zadaný řetězec nebo objekt JSON |
||||

*Příklad 1*

Tento příklad vytvoří verzi XML pro tento řetězec, který obsahuje objekt JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

A vrátí tento výsledek XML:

```xml
<name>Sophia Owen</name>
```

*Příklad 2*

Předpokládejme, že máte tento objekt JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Tento příklad vytvoří XML pro řetězec, který obsahuje tento objekt JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

A vrátí tento výsledek XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Xpath

Zkontrolujte, zda jazyk XML neodpovídá uzlům nebo hodnotám, které odpovídají výrazu XPath (JAZYK CESTY XML), a vraťte odpovídající uzly nebo hodnoty. Výraz XPath nebo jen "XPath" vám pomůže procházet strukturu dokumentu XML, takže můžete vybrat uzly nebo vypočítat hodnoty v obsahu XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Požaduje se | Typ | Popis |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Ano | Všechny | Řetězec XML pro hledání uzlů nebo hodnot, které odpovídají hodnotě výrazu XPath |
| <*Xpath*> | Ano | Všechny | Výraz XPath používaný k vyhledání odpovídajících uzlů nebo hodnot XML |
|||||

| Návratová hodnota | Typ | Popis |
| ------------ | ---- | ----------- |
| <*uzel xml*> | XML | Uzel XML, pokud pouze jeden uzel odpovídá zadanému výrazu XPath |
| <*Hodnotu*> | Všechny | Hodnota z uzlu XML, pokud pouze jedna hodnota odpovídá zadanému výrazu XPath |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-nebo- </br>[<*value1*>, <*hodnota2*>, ...] | Pole | Pole s uzly XML nebo hodnotami, které odpovídají zadanému výrazu XPath |
||||

*Příklad 1*

Tento příklad vyhledá uzly, které odpovídají `<name></name>` uzlu v určených argumentech, a vrátí pole s těmito hodnotami uzlů:

`xpath(xml(parameters('items')), '/produce/item/name')`

Zde jsou argumenty:

* Řetězec "položky", který obsahuje tento XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  Příklad používá funkci [parameters()](#parameters) k získání řetězce XML z argumentu "items", ale musí také převést řetězec do formátu XML pomocí funkce [xml().](#xml)

* Tento výraz XPath, který je předán jako řetězec:

  `"/produce/item/name"`

Zde je pole výsledků s uzly, které odpovídají `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Příklad 2*

Následující v příkladu 1 tento příklad `<count></count>` vyhledá uzly, které odpovídají `sum()` uzlu a přidá tyto hodnoty uzlů s funkcí:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

A vrátí tento výsledek:`30`

*Příklad 3*

V tomto příkladu oba výrazy najít `<location></location>` uzly, které odpovídají uzlu, v zadané argumenty, které zahrnují XML s oborem názvů. 

> [!NOTE]
>
> Pokud pracujete v zobrazení kódu, uniknout dvojité uvozovky (")\\pomocí znaku zpětného lomítka ( ). 
> Například je třeba použít řídicí znaky při serializaci výrazu jako řetězec JSON. 
> Pokud však pracujete v návrháři aplikace logiky nebo editoru výrazů, nemusíte unikat dvojité uvozovky, protože znak zpětného lomítka je automaticky přidán do základní definice, například:
> 
> * Zobrazení kódu:`xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Editor výrazů:`xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
> 
> Následující příklady platí pro výrazy, které zadáte do editoru výrazů.

* *Výraz 1*

  `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

* *Výraz 2*

  `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Zde jsou argumenty:

* Tento kód XML, který zahrnuje `xmlns="http://contoso.com"`obor názvů dokumentu XML:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Zde výraz XPath:

  * `/*[name()="file"]/*[name()="location"]`

  * `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]`

Zde je výsledek uzlu, `<location></location>` který odpovídá uzlu:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Příklad 4*

Následující na příkladu 3, tento `<location></location>` příklad najde hodnotu v uzlu:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

A vrátí tento výsledek:`"Paris"`

## <a name="next-steps"></a>Další kroky

Informace o [definičním jazyce pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md)

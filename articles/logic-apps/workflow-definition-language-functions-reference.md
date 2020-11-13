---
title: Referenční příručka pro funkce ve výrazech
description: Referenční příručka k funkcím ve výrazech pro Azure Logic Apps a automatizaci
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: dff2488094bd9abde44b8d8a8d7a44d4f79a12e0
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592681"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Referenční příručka k používání funkcí ve výrazech pro Azure Logic Apps a automatizaci

Pro definice pracovních postupů v [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [automatizaci](/flow/getting-started)některé [výrazy](../logic-apps/logic-apps-workflow-definition-language.md#expressions) získají jejich hodnoty z běhových akcí, které nemusí být k dispozici při spuštění pracovního postupu. Chcete-li odkazovat na tyto hodnoty nebo zpracovat hodnoty v těchto výrazech, můžete použít *funkce* poskytované [jazykem definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Tato referenční stránka se týká Azure Logic Apps a automatizace, ale zobrazí se v dokumentaci k Azure Logic Apps. I když tato stránka odkazuje konkrétně na Logic Apps, tyto funkce fungují pro toky i aplikace logiky. Další informace o funkcích a výrazech v Power automatizuje najdete v tématu [použití výrazů v podmínkách](/flow/use-expressions-in-conditions).

Můžete například vypočítat hodnoty pomocí matematických funkcí, jako je například funkce [Add ()](../logic-apps/workflow-definition-language-functions-reference.md#add) , pokud chcete součet z celých čísel nebo floatů. Zde jsou uvedené Další ukázkové úlohy, které můžete provádět s funkcemi:

| Úkol | Syntaxe funkce | Výsledek |
| ---- | --------------- | ------ |
| Vrátí řetězec ve formátu s malými písmeny. | toLower (' < *text* > ') <p>Příklad: toLower (' Hello ') | Hello |
| Vrátí globálně jedinečný identifikátor (GUID). | GUID () |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Pokud chcete najít funkce [založené na jejich obecném účelu](#ordered-by-purpose), Projděte si následující tabulky. Podrobnější informace o jednotlivých funkcích naleznete v [abecedním seznamu](#alphabetical-list).

## <a name="functions-in-expressions"></a>Funkce ve výrazech

Chcete-li zobrazit způsob použití funkce ve výrazu, tento příklad ukazuje, jak lze získat hodnotu z `customerName` parametru a přiřadit tuto hodnotu k `accountName` vlastnosti pomocí funkce [Parameters ()](#parameters) ve výrazu:

```json
"accountName": "@parameters('customerName')"
```

Tady je několik dalších obecných způsobů, jak můžete ve výrazech používat funkce:

| Úkol | Syntaxe funkce ve výrazu |
| ---- | -------------------------------- |
| Chcete-li provést práci s položkou, předejte tuto položku funkci. | " \@ < *Function* > (< *položka* >)" |
| 1. k získání hodnoty *ParameterName* použijte vnořenou `parameters()` funkci. </br>2. Chcete-li provést práci s výsledkem, předejte tuto hodnotu do hodnoty *Function*. | " \@ < *Function* > (Parameters (' < *ParameterName* > '))" |
| 1. Získejte výsledek z vnořené *vnitřní funkce Function.* </br>2. předejte výsledek *functionName2* vnější funkce. | " \@ < *functionName2* > (< *funkce* > (< *položka* >))" |
| 1. Získejte výsledek z *funkce Function*. </br>2. vzhledem k tomu, že výsledkem je objekt s vlastností *PropertyName* , získá hodnotu této vlastnosti. | " \@ < *Function* > (< *položka* >). < *PropertyName* >" |
|||

Například `concat()` funkce může jako parametry převzít dvě nebo více řetězcových hodnot. Tato funkce kombinuje tyto řetězce do jednoho řetězce. Můžete buď předat řetězcové literály, například "Sophia" a "Owen", abyste získali kombinovaný řetězec "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Nebo můžete z parametrů získat řetězcové hodnoty. V tomto příkladu se používá `parameters()` funkce v jednotlivých `concat()` parametrech a parametrech `firstName` a `lastName` . Pak předáte výsledné řetězce do funkce, `concat()` abyste získali kombinovaný řetězec, například "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

V obou příkladech oba příklady přiřadí výsledek `customerName` Vlastnosti.

Zde jsou některé další poznámky týkající se funkcí ve výrazech:

* Parametry funkce jsou vyhodnocovány zleva doprava.

* V syntaxi pro definice parametrů se otazník (?), který se zobrazí po parametru, označuje, že je parametr volitelný. Například viz [getFutureTime ()](#getFutureTime).

Následující části slouží k uspořádání funkcí na základě jejich obecného účelu nebo můžete tyto funkce Procházet v [abecedním pořadí](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funkce řetězců

Chcete-li pracovat s řetězci, můžete použít tyto řetězcové funkce a také některé [funkce kolekce](#collection-functions). Funkce řetězce fungují pouze v řetězcích.

| Funkce String | Úkol |
| --------------- | ---- |
| [spojuje](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Kombinací dvou nebo více řetězců a vrácení kombinovaného řetězce. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Zkontroluje, zda řetězec končí zadaným podřetězcem. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Vrátí číslo jako řetězec na základě zadaného formátu. |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Vygeneruje globálně jedinečný identifikátor (GUID) jako řetězec. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Vrátí počáteční pozici podřetězce. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Vrátí počáteční pozici pro poslední výskyt podřetězce. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Vrátí počet položek v řetězci nebo poli. |
| [náhrady](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Nahraďte podřetězec zadaným řetězcem a vraťte aktualizovaný řetězec. |
| [rozdělení](../logic-apps/workflow-definition-language-functions-reference.md#split) | Vrátí pole, které obsahuje podřetězce oddělené čárkami, z většího řetězce na základě zadaného oddělovače v původním řetězci. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Kontroluje, zda řetězec začíná konkrétním podřetězcem. |
| [podřetězec](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Vrátí znaky z řetězce počínaje od zadané pozice. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Vrátí řetězec ve formátu s malými písmeny. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Vrátí řetězec ve formátu velkými písmeny. |
| [sklon](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Odstraňte úvodní a koncové mezery z řetězce a vraťte aktualizovaný řetězec. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funkce kolekcí

Chcete-li pracovat s kolekcemi, všeobecně se jedná o pole, řetězce a někdy slovníky, můžete použít tyto funkce kolekce.

| Funkce kolekce | Úkol |
| ------------------- | ---- |
| [zobrazí](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Kontroluje, zda kolekce obsahuje konkrétní položku. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Zkontroluje, jestli je kolekce prázdná. |
| [první](../logic-apps/workflow-definition-language-functions-reference.md#first) | Vrátí první položku z kolekce. |
| [průnik](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Vrátí kolekci, která má v zadaných kolekcích *jenom* společné položky. |
| [položkami](../logic-apps/workflow-definition-language-functions-reference.md#item) | V případě opakující se akce nad polem vrátí aktuální položku v poli během aktuální iterace akce. |
| [zúčastnit](../logic-apps/workflow-definition-language-functions-reference.md#join) | Vrátí řetězec, který obsahuje *všechny* položky z pole oddělené zadaným znakem. |
| [posledního](../logic-apps/workflow-definition-language-functions-reference.md#last) | Vrátí poslední položku z kolekce. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Vrátí počet položek v řetězci nebo poli. |
| [přímo](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Odebere položky z přední části kolekce a vrátí *všechny ostatní* položky. |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Vrátí položky od začátku kolekce. |
| [sjednocovací](../logic-apps/workflow-definition-language-functions-reference.md#union) | Vrátí kolekci, která obsahuje *všechny* položky ze zadaných kolekcí. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logické funkce porovnání

Chcete-li pracovat s podmínkami, porovnat hodnoty a výsledky výrazů nebo vyhodnotit různé druhy logiky, můžete použít tyto logické funkce porovnání. Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Použijete-li logické funkce nebo podmínky pro porovnání hodnot, hodnoty null budou převedeny na prázdné hodnoty řetězce ( `""` ). Chování podmínek se liší při porovnání s prázdným řetězcem namísto hodnoty null. Další informace naleznete v tématu [funkce String ()](#string). 

| Funkce logického porovnání | Úkol |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Ověřte, zda jsou všechny výrazy pravdivé. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Zkontroluje, jestli jsou obě hodnoty ekvivalentní. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Ověřte, zda je první hodnota větší než druhá hodnota. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Ověřte, zda je první hodnota větší než nebo rovna druhé hodnotě. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Zkontroluje, jestli je výraz pravdivý, nebo nepravdivý. Na základě výsledku vrátí zadanou hodnotu. |
| [tolik](../logic-apps/workflow-definition-language-functions-reference.md#less) | Ověřte, zda je první hodnota menší než druhá hodnota. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Ověřte, zda je první hodnota menší nebo rovna druhé hodnotě. |
| [mění](../logic-apps/workflow-definition-language-functions-reference.md#not) | Zkontroluje, jestli je výraz nepravdivý. |
| [nebo](../logic-apps/workflow-definition-language-functions-reference.md#or) | Ověřte, zda je alespoň jeden výraz pravdivý. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Převodní funkce

Chcete-li změnit typ nebo formát hodnoty, můžete použít tyto funkce pro převod. Můžete například změnit hodnotu z typu Boolean na celé číslo. Další informace o tom, jak Logic Apps zpracovává typy obsahu během převodu, najdete v tématu [zpracování typů obsahu](../logic-apps/logic-apps-content-type.md). Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Azure Logic Apps automaticky převádí hodnoty mezi některými datovými typy, což znamená, že tyto převody není nutné provádět ručně. Pokud to uděláte, může dojít k neočekávanému chování zobrazení, které nemá vliv na skutečné převody, a to jenom na to, jak se zobrazují. Další informace naleznete v tématu [implicitní převody datových typů](#implicit-data-conversions).

| Převodní funkce | Úkol |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Vrátí pole z jednoho zadaného vstupu. Více vstupů naleznete v tématu [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Vrátí verzi kódovanou pro řetězec ve formátu base64. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Vrátí binární verzi řetězce zakódovaného ve formátu base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Vrátí verzi řetězce pro řetězec kódovaný v kódování Base64. |
| [binární](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Vrátí binární verzi pro vstupní hodnotu. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Vrátí logickou verzi pro vstupní hodnotu. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Vrátí pole z více vstupů. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Vrátí identifikátor URI dat pro vstupní hodnotu. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Vrátí binární verzi pro identifikátor URI dat. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Vrátí verzi řetězce pro identifikátor URI dat. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Vrátí verzi řetězce pro řetězec kódovaný v kódování Base64. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Vrátí binární verzi pro identifikátor URI dat. |
| [decodeURIComponent –](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Vrátí řetězec, který nahradí řídicí znaky pomocí dekódování verzí. |
| [encodeURIComponent –](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Vrátí řetězec, který nahradí URL – nebezpečné znaky pomocí řídicích znaků. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Vrátí číslo s plovoucí desetinnou čárkou pro vstupní hodnotu. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Vrátí celočíselnou verzi řetězce. |
| [JSON](../logic-apps/workflow-definition-language-functions-reference.md#json) | Vrátí hodnotu nebo objekt typu JavaScript Object Notation (JSON) pro řetězec nebo XML. |
| [řetězec](../logic-apps/workflow-definition-language-functions-reference.md#string) | Vrátí verzi řetězce pro vstupní hodnotu. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Vrátí verzi kódovanou identifikátorem URI pro vstupní hodnotu nahrazením znaků, které nejsou bezpečné, pomocí řídicích znaků. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Vrátí binární verzi řetězce zakódovaného identifikátorem URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Vrátí verzi řetězce pro řetězec kódovaný pomocí identifikátoru URI. |
| [XML](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Vrátí verzi XML pro řetězec. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Implicitní převody datových typů

Azure Logic Apps automatické nebo implicitně převáděné mezi některými datovými typy, takže tyto typy nemusíte ručně převádět. Například pokud použijete neřetězcové hodnoty, kde jsou jako vstupy očekávány řetězce, Logic Apps automaticky převede hodnoty neobsahující řetězec na řetězce.

Předpokládejme například, že Trigger vrátí číselnou hodnotu jako výstup:

`triggerBody()?['123']`

Použijete-li tento číselný výstup, kde je očekáván vstup řetězce, například adresa URL, Logic Apps automaticky převede hodnotu na řetězec pomocí notace složených závorek ( `{}` ):

`@{triggerBody()?['123']}`

### <a name="base64-encoding-and-decoding"></a>Kódování a dekódování base64

Logic Apps automaticky nebo implicitně provádí kódování nebo dekódování Base64, takže nemusíte tyto operace provádět ručně pomocí odpovídajících výrazů:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Pokud přidáte tyto výrazy do aplikace logiky například ručně pomocí editoru výrazů, opustíte návrháře aplikace logiky a vrátíte se do návrháře, Návrhář zobrazí pouze hodnoty parametrů. Výrazy jsou zachovány v zobrazení kódu pouze v případě, že hodnoty parametrů neupravíte. V opačném případě Logic Apps odstraní výrazy ze zobrazení kódu a zachová pouze hodnoty parametru. Toto chování nemá vliv na kódování nebo dekódování, a to pouze na to, zda jsou výrazy zobrazeny.

<a name="math-functions"></a>

## <a name="math-functions"></a>Matematické funkce

Chcete-li pracovat s celými čísly a Floaty, můžete použít tyto matematické funkce.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce Math | Úkol |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Vrátí výsledek z přidání dvou čísel. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Vrátí výsledek z dělení dvou čísel. |
| [počet](../logic-apps/workflow-definition-language-functions-reference.md#max) | Vrátí nejvyšší hodnotu ze sady čísel nebo pole. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Vrátí nejnižší hodnotu ze sady čísel nebo pole. |
| [střední](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Vrátí zbytek po dělení dvou čísel. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Vrátí produkt pro vynásobení dvou čísel. |
| [funkcí](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Vrátí náhodné celé číslo ze zadaného rozsahu. |
| [oblasti](../logic-apps/workflow-definition-language-functions-reference.md#range) | Vrátí celočíselné pole, které začíná zadaným celým číslem. |
| [jednotk](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Vrátí výsledek odečtením druhého čísla od prvního čísla. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funkce data a času

Chcete-li pracovat s daty a časy, můžete použít tyto funkce data a času.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce data a času | Úkol |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Přidání počtu dní do časového razítka. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Přidejte do časového razítka několik hodin. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Přidejte do časového razítka počet minut. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Přidejte do časového razítka počet sekund. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Přidání počtu časových jednotek k časovému razítku. Viz také [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Převeďte časové razítko ze koordinovaného světového času (UTC) na cílové časové pásmo. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Převede časové razítko ze zdrojového časového pásma na cílové časové pásmo. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Převede časové razítko ze zdrojového časového pásma na koordinovaný světový čas (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Vrátí den v měsíci součásti z časového razítka. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Vrátí den v týdnu komponenty z časového razítka. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Vrátí den komponenty v roce z časového razítka. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Vrátí datum z časového razítka. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Vrátí aktuální časové razítko plus zadané časové jednotky. Viz také [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Vrátí aktuální časové razítko minus zadané časové jednotky. Viz také [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Vrátí začátek dne pro časové razítko. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Vrátí začátek hodiny pro časové razítko. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Vrátí začátek měsíce pro časové razítko. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odečte počet časových jednotek od časového razítka. Viz také [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Vrátí `ticks` hodnotu vlastnosti pro zadané časové razítko. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Vrátí aktuální časové razítko jako řetězec. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Pracovní postup – funkce

Tyto funkce pracovního postupu vám můžou pomáhat:

* Získat podrobnosti o instanci pracovního postupu v době běhu.
* Pracujte se vstupy používanými při vytváření instancí Logic Apps nebo toků.
* Odkazování na výstupy z triggerů a akcí.

Například můžete odkazovat na výstupy z jedné akce a používat tato data v pozdější akci.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce pracovních postupů | Úkol |
| ----------------- | ---- |
| [kroky](../logic-apps/workflow-definition-language-functions-reference.md#action) | Vrátí výstup aktuální akce za běhu nebo hodnoty z jiných párů názvu a hodnoty JSON. Viz také [Akce](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Vrátí `body` výstup akce za běhu. Viz také [tělo](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Vrátí výstup akce za běhu. Zobrazit [výstupy](../logic-apps/workflow-definition-language-functions-reference.md#outputs) a [Akce](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [činností](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Vrátí výstup akce za běhu nebo hodnoty z jiných párů názvu a hodnoty JSON. Viz také [Akce](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [těles](#body) | Vrátí `body` výstup akce za běhu. Viz také [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Vytvořte pole s hodnotami, které odpovídají názvu klíče v výstupech akce *form-data* nebo *Form-Encoded* . |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Vrátí jednu hodnotu, která odpovídá názvu klíče v rámci výstupu akce *form-data* nebo *Form-Encoded*. |
| [položkami](../logic-apps/workflow-definition-language-functions-reference.md#item) | V případě opakující se akce nad polem vrátí aktuální položku v poli během aktuální iterace akce. |
| [položek](../logic-apps/workflow-definition-language-functions-reference.md#items) | V případě, že uvnitř příkazu foreach nebo do smyčky, vrátí aktuální položku ze zadané smyčky.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | V případě, že dovnitř smyčky do, vrátí hodnotu indexu pro aktuální iteraci. Tuto funkci můžete použít uvnitř vnoření do smyčky. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Vrátí adresu URL zpětného volání, která volá Trigger nebo akci. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Vrátí text pro určitou část výstupu akce, která má více částí. |
| [činnosti](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Vrátí výstup akce za běhu. |
| [ukazatelů](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Vrátí hodnotu parametru, který je popsán v definici pracovního postupu. |
| [vyústit](../logic-apps/workflow-definition-language-functions-reference.md#result) | Vrátí vstupy a výstupy ze všech akcí uvnitř zadané akce s vymezeným oborem, jako například `For_each` , `Until` a `Scope` . |
| [signálu](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Vrátí výstup triggeru za běhu nebo jiné páry název-hodnota JSON. Viz také [triggerOutputs](#triggerOutputs) a [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Vrátí výstup triggeru `body` za běhu. Viz [Trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Vrátí jednu hodnotu odpovídající názvu klíče v výstupech triggerů *form-data* nebo *Form-Encoded* . |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Vrátí tělo pro určitou část výstupu triggeru. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Vytvoří pole, jehož hodnoty se shodují s názvem klíče v výstupech triggerů *form-data* nebo *Form-Encoded* . |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Vrátí výstup triggeru za běhu nebo hodnoty z jiných párů názvu a hodnoty JSON. Viz [Trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [proměnné](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Vrátí hodnotu pro zadanou proměnnou. |
| [pracovního postupu](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Vrátí všechny podrobnosti o pracovním postupu samotném během běhu. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funkce analýzy identifikátorů URI

Pro práci s identifikátory URI (Uniform Resource Identifier) a získání různých hodnot vlastností pro tyto identifikátory URI můžete použít tyto funkce analýzy identifikátoru URI.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce analýzy identifikátoru URI | Úkol |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Vrátí `host` hodnotu identifikátoru URI (Uniform Resource Identifier). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Vrátí `path` hodnotu identifikátoru URI (Uniform Resource Identifier). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Vrátí `path` hodnoty a `query` pro identifikátor URI (Uniform Resource Identifier). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Vrátí `port` hodnotu identifikátoru URI (Uniform Resource Identifier). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Vrátí `query` hodnotu identifikátoru URI (Uniform Resource Identifier). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Vrátí `scheme` hodnotu identifikátoru URI (Uniform Resource Identifier). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funkce manipulace: JSON & XML

Chcete-li pracovat s objekty JSON a uzly XML, můžete použít tyto funkce manipulace.
Úplný odkaz na jednotlivé funkce naleznete v [abecedním seznamu](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkce manipulace | Úkol |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Přidejte vlastnost a její hodnotu nebo dvojici název-hodnota do objektu JSON a vraťte aktualizovaný objekt. |
| [COALESCE](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Vrátí první hodnotu, která není null, z jednoho nebo více parametrů. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Odebere vlastnost z objektu JSON a vrátí aktualizovaný objekt. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Nastavte hodnotu vlastnosti objektu JSON a vraťte aktualizovaný objekt. |
| [cestou](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Zkontroluje XML pro uzly nebo hodnoty, které odpovídají výrazu XPath (XML Path Language), a vrátí odpovídající uzly nebo hodnoty. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Všechny funkce – Abecední seznam

V této části jsou uvedeny všechny dostupné funkce v abecedním pořadí.

<a name="action"></a>

### <a name="action"></a>akce

Vrátí výstup *aktuální* akce za běhu nebo hodnoty z jiných párů názvu a hodnoty JSON, které můžete přiřadit k výrazu.
Ve výchozím nastavení tato funkce odkazuje na celý objekt akce, ale můžete volitelně zadat vlastnost, jejíž hodnota chcete.
Viz také [Actions ()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Funkci lze použít `action()` pouze v těchto místech:

* `unsubscribe`Vlastnost pro akci Webhooku, abyste mohli získat přístup k výsledku z původní `subscribe` žádosti
* `trackedProperties`Vlastnost pro akci
* `do-until`Podmínka smyčky pro akci

```
action()
action().outputs.body.<property>
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*majetek*> | No | Řetězec | Název vlastnosti objektu akce, jejíž hodnota má být: **název** , **čas_spuštění** , **čas_ukončení** , **vstupy** , **výstupy** , **stav** , **kód** , **trackingId** a **clientTrackingId**. V Azure Portal můžete tyto vlastnosti najít tak, že zkontrolujete podrobnosti o konkrétní historii spuštění. Další informace najdete v tématu [REST API – akce běhu pracovního postupu](/rest/api/logic/workflowrunactions/get). |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | -----| ----------- |
| <*akce – výstup*> | Řetězec | Výstup z aktuální akce nebo vlastnosti |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Vrátí `body` výstup akce za běhu.
Zkrácený pro `actions('<actionName>').outputs.body` .
Viz [body ()](#body) a [Actions ()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ano | Řetězec | Název `body` výstupu akce, který chcete |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | -----| ----------- |
| <*Text akce – výstup*> | Řetězec | `body`Výstup ze zadané akce |
||||

*Příklad*

Tento příklad načte `body` výstup z akce Twitteru `Get user` :

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

### <a name="actionoutputs"></a>actionOutputs

Vrátí výstup akce za běhu.  a je zkrácený pro `actions('<actionName>').outputs` . Viz [Actions ()](#actions). `actionOutputs()`Funkce překládá na `outputs()` v návrháři aplikace logiky, proto zvažte použití [výstupů ()](#outputs)místo `actionOutputs()` . I když obě funkce fungují stejným způsobem, `outputs()` jsou upřednostňovány.

```
actionOutputs('<actionName>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ano | Řetězec | Název výstupu akce, který chcete |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | -----| ----------- |
| <*výkonem*> | Řetězec | Výstup ze zadané akce |
||||

*Příklad*

Tento příklad načte výstup z akce Twitteru `Get user` :

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

Vrátí výstup akce za běhu nebo hodnoty z jiných párů názvu a hodnoty JSON, které můžete přiřadit k výrazu. Ve výchozím nastavení funkce odkazuje na celý objekt akce, ale můžete volitelně zadat vlastnost, jejíž hodnota má být.
Pro zkrácených verzí viz [actionBody ()](#actionBody), [actionOutputs ()](#actionOutputs)a [body ()](#body).
Aktuální akci naleznete v tématu [Action ()](#action).

> [!TIP]
> `actions()`Funkce vrátí výstup jako řetězec. Pokud potřebujete pracovat s vrácenou hodnotou jako objekt JSON, musíte nejprve převést řetězcovou hodnotu. Můžete transformovat řetězcovou hodnotu na objekt JSON pomocí [Akce analyzovat JSON](logic-apps-perform-data-operations.md#parse-json-action).

> [!NOTE]
> Dřív jste mohli použít `actions()` funkci nebo `conditions` prvek při určení, že akce běžela na základě výstupu z jiné akce. Chcete-li však deklarovat explicitně závislosti mezi akcemi, je nyní nutné použít vlastnost závislé akce `runAfter` .
> Další informace o této `runAfter` vlastnosti naleznete v tématu [zachycení a zpracování selhání pomocí vlastnosti runafter šablonové](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ano | Řetězec | Název objektu akce, jehož výstup chcete  |
| <*majetek*> | No | Řetězec | Název vlastnosti objektu akce, jejíž hodnota má být: **název** , **čas_spuštění** , **čas_ukončení** , **vstupy** , **výstupy** , **stav** , **kód** , **trackingId** a **clientTrackingId**. V Azure Portal můžete tyto vlastnosti najít tak, že zkontrolujete podrobnosti o konkrétní historii spuštění. Další informace najdete v tématu [REST API – akce běhu pracovního postupu](/rest/api/logic/workflowrunactions/get). |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | -----| ----------- |
| <*akce – výstup*> | Řetězec | Výstup z určené akce nebo vlastnosti |
||||

*Příklad*

Tento příklad získá `status` hodnotu vlastnosti z akce Twitteru `Get user` za běhu:

```
actions('Get_user').outputs.body.status
```

A vrátí tento výsledek: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Vrátí výsledek z přidání dvou čísel.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*summand_1* > < *summand_2*> | Yes | Integer, float nebo mixed | Čísla, která mají být přidána |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | -----| ----------- |
| <*výsledek – součet*> | Integer nebo float | Výsledek přidání zadaných čísel |
||||

*Příklad*

V tomto příkladu se přidají zadaná čísla:

```
add(1, 1.5)
```

A vrátí tento výsledek: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Přidání počtu dní do časového razítka.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*denní*> | Yes | Integer | Kladný nebo záporný počet dní, které se mají přidat |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Časové razítko plus zadaný počet dní  |
||||

*Příklad 1*

Tento příklad přidá do zadaného časového razítka 10 dní:

```
addDays('2018-03-15T13:00:00Z', 10)
```

A vrátí tento výsledek: `"2018-03-25T00:00:0000000Z"`

*Příklad 2*

Tento příklad odečte pět dní od zadaného časového razítka:

```
addDays('2018-03-15T00:00:00Z', -5)
```

A vrátí tento výsledek: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Přidejte do časového razítka několik hodin.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*hodin*> | Yes | Integer | Kladný nebo záporný počet hodin, které se mají přidat |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Časové razítko plus zadaný počet hodin  |
||||

*Příklad 1*

Tento příklad přidá 10 hodin do zadaného časového razítka:

```
addHours('2018-03-15T00:00:00Z', 10)
```

A vrátí tento výsledek: `"2018-03-15T10:00:0000000Z"`

*Příklad 2*

Tento příklad odečte pět hodin od zadaného časového razítka:

```
addHours('2018-03-15T15:00:00Z', -5)
```

A vrátí tento výsledek: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Přidejte do časového razítka počet minut.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*minuty*> | Yes | Integer | Kladný nebo záporný počet minut, který se má přidat |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Časové razítko plus zadaný počet minut |
||||

*Příklad 1*

Tento příklad přidá do zadaného časového razítka 10 minut:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

A vrátí tento výsledek: `"2018-03-15T00:20:00.0000000Z"`

*Příklad 2*

Tento příklad odečte pět minut od zadaného časového razítka:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

A vrátí tento výsledek: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Přidejte vlastnost a její hodnotu nebo dvojici název-hodnota do objektu JSON a vraťte aktualizovaný objekt. Pokud vlastnost již v době běhu existuje, funkce se nezdařila a vyvolá chybu.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*předmětů*> | Yes | Objekt | Objekt JSON, do kterého chcete přidat vlastnost |
| <*majetek*> | Ano | Řetězec | Název vlastnosti, která se má přidat |
| <*osa*> | Yes | Libovolný | Hodnota vlastnosti |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – objekt*> | Objekt | Aktualizovaný objekt JSON se zadanou vlastností |
||||

Chcete-li přidat podřízenou vlastnost do existující vlastnosti, použijte tuto syntaxi:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*předmětů*> | Yes | Objekt | Objekt JSON, do kterého chcete přidat vlastnost |
| <*Nadřazená vlastnost*> | Ano | Řetězec | Název pro nadřazenou vlastnost, do které chcete přidat podřízenou vlastnost |
| <*podřízená vlastnost*> | Ano | Řetězec | Název podřízené vlastnosti, která se má přidat |
| <*osa*> | Yes | Libovolný | Hodnota, která se má nastavit pro zadanou vlastnost |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – objekt*> | Objekt | Aktualizovaný objekt JSON, jehož vlastnost nastavena |
||||

*Příklad 1*

Tento příklad přidá `middleName` vlastnost do objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON ()](#json) . Objekt již obsahuje `firstName` `surName` vlastnosti a. Funkce přiřadí zadanou hodnotu k nové vlastnosti a vrátí aktualizovaný objekt:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Tady je aktuální objekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Tady je aktualizovaný objekt JSON:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Příklad 2*

Tento příklad přidá `middleName` podřízenou vlastnost do existující `customerName` vlastnosti v objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON ()](#json) . Funkce přiřadí zadanou hodnotu k nové vlastnosti a vrátí aktualizovaný objekt:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Tady je aktuální objekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Tady je aktualizovaný objekt JSON:

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

Přidejte do časového razítka počet sekund.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*Second*> | Yes | Integer | Kladný nebo záporný počet sekund, které se mají přidat |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Časové razítko plus zadaný počet sekund  |
||||

*Příklad 1*

Tento příklad přidá 10 sekund do zadaného časového razítka:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

A vrátí tento výsledek: `"2018-03-15T00:00:10.0000000Z"`

*Příklad 2*

Tento příklad odečte pět sekund na zadané časové razítko:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

A vrátí tento výsledek: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Přidání počtu časových jednotek k časovému razítku.
Viz také [getFutureTime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*doba*> | Yes | Integer | Počet zadaných časových jednotek, které se mají přidat |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, která se má použít v *intervalu* : "druhé", "Minute", "hodina", "den", "týden", "měsíc", "rok" |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Časové razítko plus zadaný počet časových jednotek  |
||||

*Příklad 1*

Tento příklad přidá jeden den do zadaného časového razítka:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

A vrátí tento výsledek: `"2018-01-02T00:00:00.0000000Z"`

*Příklad 2*

Tento příklad přidá jeden den do zadaného časového razítka:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

A vrátí výsledek pomocí volitelného formátu "D": `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>a

Ověřte, zda jsou všechny výrazy pravdivé.
Vrátí hodnotu true, pokud mají všechny výrazy hodnotu true, nebo vrátí hodnotu false, pokud je alespoň jeden výraz nepravdivý.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*výraz1* >, < *Výraz2* >,... | Yes | Logická hodnota | Výrazy, které mají být zkontrolovány |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | -----| ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud jsou všechny výrazy pravdivé. Vrátí hodnotu false, pokud je alespoň jeden výraz nepravdivý. |
||||

*Příklad 1*

Tyto příklady kontrolují, jestli jsou všechny zadané logické hodnoty pravdivé:

```
and(true, true)
and(false, true)
and(false, false)
```

A vrátí tyto výsledky:

* První příklad: oba výrazy mají hodnotu true, takže se vrátí `true` .
* Druhý příklad: jeden výraz má hodnotu NEPRAVDA, takže se vrátí `false` .
* Třetí příklad: oba výrazy mají hodnotu false, takže se vrátí `false` .

*Příklad 2*

Tyto příklady kontrolují, jestli jsou všechny zadané výrazy pravdivé:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

A vrátí tyto výsledky:

* První příklad: oba výrazy mají hodnotu true, takže se vrátí `true` .
* Druhý příklad: jeden výraz má hodnotu NEPRAVDA, takže se vrátí `false` .
* Třetí příklad: oba výrazy mají hodnotu false, takže se vrátí `false` .

<a name="array"></a>

### <a name="array"></a>array

Vrátí pole z jednoho zadaného vstupu.
Více vstupů naleznete v tématu [createArray ()](#createArray).

```
array('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec pro vytvoření pole |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [< *value* >] | Pole | Pole, které obsahuje jeden zadaný vstup |
||||

*Příklad*

Tento příklad vytvoří pole z řetězce "Hello":

```
array('hello')
```

A vrátí tento výsledek: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Vrátí verzi kódovanou pro řetězec ve formátu base64.

> [!NOTE]
> Azure Logic Apps automaticky provádí kódování a dekódování Base64, což znamená, že není nutné provádět tyto převody ručně. Pokud to uděláte, může dojít k neočekávanému chování zobrazení, které nemá vliv na skutečné převody, a to jenom na to, jak se zobrazují. Další informace naleznete v tématu [implicitní převody datových typů](#implicit-data-conversions).

```
base64('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Vstupní řetězec |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*řetězec base64*> | Řetězec | Verze zakódovaná ve formátu base64 pro vstupní řetězec |
||||

*Příklad*

Tento příklad převede řetězec "Hello" na řetězec kódovaný v kódování Base64:

```
base64('hello')
```

A vrátí tento výsledek: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Vrátí binární verzi řetězce zakódovaného ve formátu base64.

> [!NOTE]
> Azure Logic Apps automaticky provádí kódování a dekódování Base64, což znamená, že není nutné provádět tyto převody ručně. Pokud to uděláte, může dojít k neočekávanému chování zobrazení, které nemá vliv na skutečné převody, a to jenom na to, jak se zobrazují. Další informace naleznete v tématu [implicitní převody datových typů](#implicit-data-conversions).

```
base64ToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec kódovaný v kódování Base64 k převedení |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*binární – řetězec pro kódování Base64*> | Řetězec | Binární verze řetězce zakódovaného ve formátu base64 |
||||

*Příklad*

V tomto příkladu se převede řetězec s kódováním base64 na binární řetězec typu "aGVsbG8 =".

```
base64ToBinary('aGVsbG8=')
```

A vrátí tento výsledek:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Vrátí verzi řetězce pro řetězec kódovaný v kódování Base64 a efektivně dekódování řetězce base64. Použijte tuto funkci místo [decodeBase64 ()](#decodeBase64), která je zastaralá.

> [!NOTE]
> Azure Logic Apps automaticky provádí kódování a dekódování Base64, což znamená, že není nutné provádět tyto převody ručně. Pokud to uděláte, může dojít k neočekávanému chování zobrazení, které nemá vliv na skutečné převody, a to jenom na to, jak se zobrazují. Další informace naleznete v tématu [implicitní převody datových typů](#implicit-data-conversions).

```
base64ToString('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec kódovaný v kódování Base64 k dekódování |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*dekódované – řetězec base64*> | Řetězec | Verze řetězce pro řetězec kódovaný v kódování Base64 |
||||

*Příklad*

Tento příklad převede řetězec kódovaný řetězcem Base64 "aGVsbG8 =" na pouze řetězec:

```
base64ToString('aGVsbG8=')
```

A vrátí tento výsledek: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binární

Vrátí binární verzi řetězce.

```
binary('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec, který se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*binární hodnota pro zadání hodnoty*> | Řetězec | Binární verze zadaného řetězce |
||||

*Příklad*

Tento příklad převede řetězec "Hello" na binární řetězec:

```
binary('hello')
```

A vrátí tento výsledek:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>text

Vrátí `body` výstup akce za běhu.
Zkrácený pro `actions('<actionName>').outputs.body` .
Viz [actionBody ()](#actionBody) a [Actions ()](#actions).

```
body('<actionName>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ano | Řetězec | Název `body` výstupu akce, který chcete |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | -----| ----------- |
| <*Text akce – výstup*> | Řetězec | `body`Výstup ze zadané akce |
||||

*Příklad*

Tento příklad načte `body` výstup z `Get user` Akce Twitteru:

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

Vrátí logickou verzi hodnoty.

```
bool(<value>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Yes | Libovolný | Hodnota, která má být převedena na logickou hodnotu. |
|||||

Pokud používáte `bool()` s objektem, hodnota objektu musí být řetězec nebo celé číslo, které lze převést na logickou hodnotu.

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| `true` nebo `false` | Logická hodnota | Logická verze zadané hodnoty. |
||||

*Výstupy*

Tyto příklady znázorňují různé podporované typy vstupu pro `bool()` :

| Vstupní hodnota | Typ | Vrácená hodnota |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | Integer | `true` |
| `bool(0)` | Integer    | `false` |
| `bool(-1)` | Integer | `true` |
| `bool('true')` | Řetězec | `true` |
| `bool('false')` | Řetězec | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>COALESCE

Vrátí první hodnotu, která není null, z jednoho nebo více parametrů.
Prázdné řetězce, prázdné pole a prázdné objekty nejsou null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*object_1* > < *object_2* >... | Yes | Libovolný, můžou kombinovat typy. | Jedna nebo více položek pro kontrolu hodnoty null |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*první položka, která není null,*> | Libovolný | První položka nebo hodnota, která není null. Pokud jsou všechny parametry null, tato funkce vrátí hodnotu null. |
||||

*Příklad*

Tyto příklady vrátí první hodnotu jinou než null ze zadaných hodnot nebo hodnotu null, pokud jsou všechny hodnoty null:

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

### <a name="concat"></a>concat

Kombinací dvou nebo více řetězců a vrácení kombinovaného řetězce.

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*text1* >, < *Text2* >,... | Ano | Řetězec | Aspoň dva řetězce, které se mají zkombinovat |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Řetězec | Řetězec vytvořený z kombinovaných vstupních řetězců |
||||

*Příklad*

V tomto příkladu jsou kombinovány řetězce "Hello" a "World":

```
concat('Hello', 'World')
```

A vrátí tento výsledek: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>obsahuje

Kontroluje, zda kolekce obsahuje konkrétní položku.
Vrátí hodnotu true, pokud se položka najde, nebo vrátí hodnotu false, pokud nebyla nalezena.
Tato funkce rozlišuje velká a malá písmena.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Konkrétně tato funkce funguje na těchto typech kolekcí:

* *Řetězec* pro vyhledání *podřetězce*
* *Pole* , ve kterém se má najít *hodnota*
* *Slovník* , ve kterém se má najít *klíč*

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Řetězec, pole nebo slovník | Kolekce, která má být zkontrolována |
| <*osa*> | Yes | Řetězec, pole nebo slovník, v uvedeném pořadí | Položka, která se má najít |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud se položka najde. Pokud se nenajde, vrátí se hodnota false. |
||||

*Příklad 1*

Tento příklad zkontroluje řetězec "Hello World" pro podřetězec "World" a vrátí hodnotu true:

```
contains('hello world', 'world')
```

*Příklad 2*

Tento příklad zkontroluje řetězec "Hello World" pro podřetězec "Universe" a vrátí hodnotu false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Převeďte časové razítko ze koordinovaného světového času (UTC) na cílové časové pásmo.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*destinationTimeZone*> | Ano | Řetězec | Název cílového časového pásma. Informace o názvech časových pásem najdete v tématu [hodnoty indexu časového pásma Microsoftu](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale možná budete muset odebrat všechna interpunkční znaménka z názvu časového pásma. |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*převedeno – časové razítko*> | Řetězec | Časové razítko převedené do cílového časového pásma |
||||

*Příklad 1*

Tento příklad převede časové razítko na zadané časové pásmo:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

A vrátí tento výsledek: `"2018-01-01T00:00:00.0000000"`

*Příklad 2*

Tento příklad převede časové razítko na zadané časové pásmo a formát:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

A vrátí tento výsledek: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Převede časové razítko ze zdrojového časového pásma na cílové časové pásmo.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*sourceTimeZone*> | Ano | Řetězec | Název zdrojového časového pásma. Informace o názvech časových pásem najdete v tématu [hodnoty indexu časového pásma Microsoftu](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale možná budete muset odebrat všechna interpunkční znaménka z názvu časového pásma. |
| <*destinationTimeZone*> | Ano | Řetězec | Název cílového časového pásma. Informace o názvech časových pásem najdete v tématu [hodnoty indexu časového pásma Microsoftu](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale možná budete muset odebrat všechna interpunkční znaménka z názvu časového pásma. |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*převedeno – časové razítko*> | Řetězec | Časové razítko převedené do cílového časového pásma |
||||

*Příklad 1*

Tento příklad převede zdrojové časové pásmo na cílové časové pásmo:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

A vrátí tento výsledek: `"2018-01-01T00:00:00.0000000"`

*Příklad 2*

Tento příklad převede časové pásmo na zadané časové pásmo a formát:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

A vrátí tento výsledek: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Převede časové razítko ze zdrojového časového pásma na koordinovaný světový čas (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*sourceTimeZone*> | Ano | Řetězec | Název zdrojového časového pásma. Informace o názvech časových pásem najdete v tématu [hodnoty indexu časového pásma Microsoftu](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale možná budete muset odebrat všechna interpunkční znaménka z názvu časového pásma. |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*převedeno – časové razítko*> | Řetězec | Časové razítko převedené na UTC |
||||

*Příklad 1*

Tento příklad převede časové razítko na UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

A vrátí tento výsledek: `"2018-01-01T08:00:00.0000000Z"`

*Příklad 2*

Tento příklad převede časové razítko na UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

A vrátí tento výsledek: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Vrátí pole z více vstupů.
Pro pole s jedním vstupem naleznete v tématu [Array ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*objekt1* > < *objekt2* >... | Yes | Libovolná, ale ne smíšená | Aspoň dvě položky pro vytvoření pole |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [< *objekt1* >, < *objekt2* >,...] | Pole | Pole vytvořené ze všech vstupních položek |
||||

*Příklad*

Tento příklad vytvoří pole z těchto vstupů:

```
createArray('h', 'e', 'l', 'l', 'o')
```

A vrátí tento výsledek: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Vrátí v řetězci datový identifikátor URI (Uniform Resource Identifier).

```
dataUri('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec, který se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*identifikátor URI dat*> | Řetězec | Identifikátor URI dat pro vstupní řetězec |
||||

*Příklad*

Tento příklad vytvoří identifikátor URI dat pro řetězec "Hello":

```
dataUri('hello')
```

A vrátí tento výsledek: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Vrátí binární verzi pro datový identifikátor URI (Uniform Resource Identifier).
Použijte tuto funkci místo [decodeDataUri ()](#decodeDataUri).
I když obě funkce fungují stejným způsobem, `dataUriBinary()` jsou upřednostňovány.

```
dataUriToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Identifikátor URI dat, který se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*binární data pro identifikátor URI*> | Řetězec | Binární verze identifikátoru URI dat |
||||

*Příklad*

Tento příklad vytvoří binární verzi pro tento identifikátor URI dat:

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

Vrátí verzi řetězce pro datový identifikátor URI (Uniform Resource Identifier).

```
dataUriToString('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Identifikátor URI dat, který se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*řetězec pro datový identifikátor URI*> | Řetězec | Verze řetězce pro identifikátor URI dat |
||||

*Příklad*

Tento příklad vytvoří řetězec pro tento identifikátor URI dat:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí tento výsledek: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Vrátí den v měsíci z časového razítka.

```
dayOfMonth('<timestamp>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*den měsíce*> | Integer | Den v měsíci ze zadaného časového razítka |
||||

*Příklad*

Tento příklad vrátí číslo dne v měsíci z tohoto časového razítka:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

A vrátí tento výsledek: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Vrátí den v týdnu z časového razítka.

```
dayOfWeek('<timestamp>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*den týdne*> | Integer | Den v týdnu ze zadaného časového razítka, kde neděle je 0, pondělí a tak dále |
||||

*Příklad*

Tento příklad vrátí číslo pro den v týdnu z tohoto časového razítka:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

A vrátí tento výsledek: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Vrátí den v roce z časového razítka.

```
dayOfYear('<timestamp>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*den roku*> | Integer | Den v roce ze zadaného časového razítka |
||||

*Příklad*

Tento příklad vrátí číslo dne v roce z tohoto časového razítka:

```
dayOfYear('2018-03-15T13:27:36Z')
```

A vrátí tento výsledek: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64 (zastaralé)

Tato funkce je zastaralá, proto použijte místo toho [base64ToString ()](#base64ToString) .

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Vrátí binární verzi pro datový identifikátor URI (Uniform Resource Identifier). Zvažte použití [dataUriToBinary ()](#dataUriToBinary)místo `decodeDataUri()` . I když obě funkce fungují stejným způsobem, `dataUriToBinary()` jsou upřednostňovány.

> [!NOTE]
> Azure Logic Apps automaticky provádí kódování a dekódování Base64, což znamená, že není nutné provádět tyto převody ručně. Pokud to uděláte, může dojít k neočekávanému chování zobrazení, které nemá vliv na skutečné převody, a to jenom na to, jak se zobrazují. Další informace naleznete v tématu [implicitní převody datových typů](#implicit-data-conversions).

```
decodeDataUri('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec identifikátoru URI dat k dekódování |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*binární data pro identifikátor URI*> | Řetězec | Binární verze řetězce identifikátoru URI dat |
||||

*Příklad*

Tento příklad vrátí binární verzi pro tento identifikátor URI dat:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

A vrátí tento výsledek:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeURIComponent –

Vrátí řetězec, který nahradí řídicí znaky pomocí dekódování verzí.

```
decodeUriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec s řídicími znaky k dekódování |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Dekódovatelné – identifikátor URI*> | Řetězec | Aktualizovaný řetězec s dekódovými řídicími znaky |
||||

*Příklad*

Tento příklad nahrazuje řídicí znaky v tomto řetězci pomocí Dekódovatelné verze:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

A vrátí tento výsledek: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Vrátí výsledek z dělení dvou čísel. Chcete-li získat zbývající výsledek, viz [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*podíl*> | Yes | Integer nebo float | Číslo, které se má rozdělit *dělitelem* |
| <*dělitel*> | Yes | Integer nebo float | Číslo, které vydělí *dividendy* , ale nemůže být 0 |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*podíl – výsledek*> | Integer nebo float | Výsledek dělení prvního čísla druhým číslem. Pokud má dělenec nebo dělitel typ float, výsledek má typ float. <p><p>**Poznámka** : Pokud chcete převést výsledek typu float na celé číslo, zkuste [vytvořit a zavolat funkci Azure Functions](../logic-apps/logic-apps-azure-functions.md) z vaší aplikace logiky. |
||||

*Příklad 1*

Oba příklady vrátí tuto hodnotu s typem Integer: `2`

```
div(10,5)
div(11,5)
```

*Příklad 2*

Oba příklady vrátí tuto hodnotu s typem float: `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeURIComponent –

Nahrazením znaků, které nejsou v adrese URL, pomocí řídicích znaků vrátíte verzi kódovanou podle identifikátoru URI (Uniform Resource Identifier) pro řetězec. Zvažte použití [uriComponent ()](#uriComponent)místo `encodeUriComponent()` . I když obě funkce fungují stejným způsobem, `uriComponent()` jsou upřednostňovány.

> [!NOTE]
> Azure Logic Apps automaticky provádí kódování a dekódování Base64, což znamená, že není nutné provádět tyto převody ručně. Pokud to uděláte, může dojít k neočekávanému chování zobrazení, které nemá vliv na skutečné převody, a to jenom na to, jak se zobrazují. Další informace naleznete v tématu [implicitní převody datových typů](#implicit-data-conversions).

```
encodeUriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec, který se má převést na formát kódovaný identifikátorem URI |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*kódovaný identifikátor URI*> | Řetězec | Řetězec kódovaný identifikátorem URI s řídicími znaky |
||||

*Příklad*

Tento příklad vytvoří pro tento řetězec verzi kódovanou pomocí identifikátoru URI:

```
encodeUriComponent('https://contoso.com')
```

A vrátí tento výsledek: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Zkontroluje, jestli je kolekce prázdná.
Vrátí hodnotu true, pokud je kolekce prázdná, nebo vrátí hodnotu false, pokud není prázdná.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Řetězec, pole nebo objekt | Kolekce, která má být zkontrolována |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu pravda, pokud je kolekce prázdná. Vrátí hodnotu false, pokud není prázdná. |
||||

*Příklad*

Tyto příklady kontrolují, jestli jsou zadané kolekce prázdné:

```
empty('')
empty('abc')
```

A vrátí tyto výsledky:

* První příklad: předává prázdný řetězec, takže funkce vrátí hodnotu `true` .
* Druhý příklad: předá řetězec "ABC", takže funkce vrátí `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Zkontroluje, jestli řetězec končí určitým podřetězcem.
Vrátí hodnotu true, pokud se podřetězec najde, nebo vrátí hodnotu false, pokud nebyla nalezena.
Tato funkce nerozlišuje velká a malá písmena.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který má být zkontrolován |
| <*Prohledávanýtext*> | Ano | Řetězec | Koncový dílčí řetězec, který se má najít |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false  | Logická hodnota | Vrátí hodnotu pravda, pokud je nalezen poslední podřetězec. Pokud se nenajde, vrátí se hodnota false. |
||||

*Příklad 1*

Tento příklad zkontroluje, jestli řetězec "Hello World" končí řetězcem "World":

```
endsWith('hello world', 'world')
```

A vrátí tento výsledek: `true`

*Příklad 2*

Tento příklad zkontroluje, jestli řetězec "Hello World" končí řetězcem "Universe":

```
endsWith('hello world', 'universe')
```

A vrátí tento výsledek: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Zkontroluje, jestli jsou hodnoty, výrazy nebo objekty ekvivalentní.
Vrátí hodnotu true, pokud jsou obě ekvivalentní, nebo vrátí hodnotu false, pokud nejsou ekvivalentní.

```
equals('<object1>', '<object2>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*objekt1* >, < *objekt2*> | Yes | Některé | Hodnoty, výrazy nebo objekty, které se mají porovnat |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud jsou obě ekvivalentní. Vrátí hodnotu false, pokud není ekvivalentní. |
||||

*Příklad*

Tyto příklady kontrolují, zda jsou zadané vstupy ekvivalentní.

```
equals(true, 1)
equals('abc', 'abcd')
```

A vrátí tyto výsledky:

* První příklad: obě hodnoty jsou ekvivalentní, takže funkce vrátí `true` .
* Druhý příklad: obě hodnoty nejsou ekvivalentní, takže funkce vrátí `false` .

<a name="first"></a>

### <a name="first"></a>první

Vrátí první položku z řetězce nebo pole.

```
first('<collection>')
first([<collection>])
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Řetězec nebo pole | Kolekce, ve které se má najít první položka |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*první kolekce – položka*> | Libovolný | První položka v kolekci |
||||

*Příklad*

Tyto příklady vyhledají první položku v těchto kolekcích:

```
first('hello')
first(createArray(0, 1, 2))
```

A vrátí tyto výsledky:

* První příklad: `"h"`
* Druhý příklad: `0`

<a name="float"></a>

### <a name="float"></a>float

Převede verzi řetězce pro číslo s plovoucí desetinnou čárkou na skutečné číslo s plovoucí desetinnou čárkou.
Tuto funkci můžete použít pouze při předávání vlastních parametrů aplikaci, například aplikace logiky nebo toku.

```
float('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec, který má platné číslo s plovoucí desetinnou čárkou, které se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*float-Value*> | Float | Číslo s plovoucí desetinnou čárkou pro zadaný řetězec |
||||

*Příklad*

Tento příklad vytvoří verzi řetězce pro toto číslo s plovoucí desetinnou čárkou:

```
float('10.333')
```

A vrátí tento výsledek: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Vrátí časové razítko v zadaném formátu.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*přeformátované – časové razítko*> | Řetězec | Aktualizované časové razítko v zadaném formátu |
||||

*Příklad*

Tento příklad převede časové razítko na určený formát:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

A vrátí tento výsledek: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Vrátí pole s hodnotami, které odpovídají názvu klíče ve výstupu akce *formuláře* nebo *formuláře s kódováním* .

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ano | Řetězec | Akce, jejíž výstup má požadovanou hodnotu klíče |
| <*zkrat*> | Ano | Řetězec | Název pro klíč, jehož hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [< *pole-s-klíč-hodnoty* >] | Pole | Pole se všemi hodnotami, které odpovídají zadanému klíči |
||||

*Příklad*

Tento příklad vytvoří pole z hodnoty klíče "Subject" v zadané formě data formuláře nebo formuláře s kódováním:

```
formDataMultiValues('Send_an_email', 'Subject')
```

A vrátí text předmětu v poli, například: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Vrátí jednu hodnotu, která odpovídá názvu klíče v rámci výstupu akce *form-data* nebo *Form-Encoded* .
Pokud funkce najde více než jednu shodu, funkce vyvolá chybu.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ano | Řetězec | Akce, jejíž výstup má požadovanou hodnotu klíče |
| <*zkrat*> | Ano | Řetězec | Název pro klíč, jehož hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*hodnota klíče*> | Řetězec | Hodnota v zadaném klíči  |
||||

*Příklad*

Tento příklad vytvoří řetězec z hodnoty klíče "Subject" v zadané formě data formuláře nebo formuláře s kódováním:

```
formDataValue('Send_an_email', 'Subject')
```

A vrátí text předmětu jako řetězec, například: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Vrátí číslo jako řetězec, který je založen na zadaném formátu.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*Automatické*> | Yes | Integer nebo Double | Hodnota, kterou chcete formátovat. |
| <*formátovat*> | Ano | Řetězec | Složený formátovací řetězec, který určuje formát, který chcete použít. Podporované řetězce číselného formátu naleznete v tématu [Standardní číselné formátovací řetězce](/dotnet/standard/base-types/standard-numeric-format-strings), které jsou podporovány nástrojem `number.ToString(<format>, <locale>)` . |
| <*jazyka*> | No | Řetězec | Národní prostředí, které se má použít jako podporované `number.ToString(<format>, <locale>)` . Pokud není zadaný, použije se výchozí hodnota `en-us` . |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*formátované číslo*> | Řetězec | Zadané číslo jako řetězec ve formátu, který jste zadali. Tuto vrácenou hodnotu můžete přetypovat na `int` nebo `float` . |
||||

*Příklad 1*

Předpokládejme, že chcete číslo naformátovat `1234567890` . Tento příklad formátuje číslo jako řetězec "1 234 567 890,00".

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

* Příklad 2 "

Předpokládejme, že chcete číslo naformátovat `1234567890` . V tomto příkladu se zformátuje číslo do řetězce "1.234.567.890, 00".

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*Příklad 3*

Předpokládejme, že chcete číslo naformátovat `17.35` . V tomto příkladu se zformátuje číslo do řetězce "$17,35".

```
formatNumber(17.36, 'C2')
```

*Příklad 4*

Předpokládejme, že chcete číslo naformátovat `17.35` . V tomto příkladu se zformátuje číslo do řetězce "17, 35 kr".

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Vrátí aktuální časové razítko plus zadané časové jednotky.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*doba*> | Yes | Integer | Počet zadaných časových jednotek, které se mají přidat |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, která se má použít v *intervalu* : "druhé", "Minute", "hodina", "den", "týden", "měsíc", "rok" |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Aktuální časové razítko plus zadaný počet časových jednotek |
||||

*Příklad 1*

Předpokládejme, že aktuální časové razítko je "2018-03-01T00:00:00.0000000 Z".
Tento příklad přidá pět dní do tohoto časového razítka:

```
getFutureTime(5, 'Day')
```

A vrátí tento výsledek: `"2018-03-06T00:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že aktuální časové razítko je "2018-03-01T00:00:00.0000000 Z".
Tento příklad přidá pět dnů a převede výsledek na formát "D":

```
getFutureTime(5, 'Day', 'D')
```

A vrátí tento výsledek: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Vrátí aktuální časové razítko minus zadané časové jednotky.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*doba*> | Yes | Integer | Počet zadaných časových jednotek k odečtení |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, která se má použít v *intervalu* : "druhé", "Minute", "hodina", "den", "týden", "měsíc", "rok" |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Aktuální časové razítko minus zadaný počet časových jednotek |
||||

*Příklad 1*

Předpokládejme, že aktuální časové razítko je "2018-02-01T00:00:00.0000000 Z".
Tento příklad odečte pět dní od tohoto časového razítka:

```
getPastTime(5, 'Day')
```

A vrátí tento výsledek: `"2018-01-27T00:00:00.0000000Z"`

*Příklad 2*

Předpokládejme, že aktuální časové razítko je "2018-02-01T00:00:00.0000000 Z".
Tento příklad odečte pět dní a převede výsledek na formát "D":

```
getPastTime(5, 'Day', 'D')
```

A vrátí tento výsledek: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Ověřte, zda je první hodnota větší než druhá hodnota.
Vrátí hodnotu true, pokud je první hodnota větší, nebo vrátí hodnotu false, pokud je menší.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Yes | Integer, float nebo String | První hodnota, která ověří, zda je větší než druhá hodnota |
| <*compareTo*> | Yes | Integer, float nebo String, v uvedeném pořadí | Hodnota porovnání |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud je první hodnota větší než druhá hodnota. Vrátí hodnotu false, pokud je první hodnota rovna nebo menší než druhá hodnota. |
||||

*Příklad*

Tyto příklady kontrolují, zda je první hodnota větší než druhá hodnota:

```
greater(10, 5)
greater('apple', 'banana')
```

A vrátí tyto výsledky:

* První příklad: `true`
* Druhý příklad: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Ověřte, zda je první hodnota větší než nebo rovna druhé hodnotě.
Vrátí hodnotu true, pokud je první hodnota větší nebo rovna, nebo vrátí hodnotu false, pokud je první hodnota menší.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Yes | Integer, float nebo String | První hodnota, která kontroluje, zda je hodnota větší nebo rovna druhé hodnotě |
| <*compareTo*> | Yes | Integer, float nebo String, v uvedeném pořadí | Hodnota porovnání |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud je první hodnota větší nebo rovna druhé hodnotě. Vrátí hodnotu false, pokud je první hodnota menší než druhá hodnota. |
||||

*Příklad*

Tyto příklady kontrolují, zda je první hodnota větší nebo rovna druhé hodnotě:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

A vrátí tyto výsledky:

* První příklad: `true`
* Druhý příklad: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Vygenerujte globálně jedinečný identifikátor (GUID) jako řetězec, například "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Můžete také zadat jiný formát pro jiný identifikátor GUID, než je výchozí formát "D", což je 32 číslic oddělených pomlčkami.

```
guid('<format>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*formátovat*> | No | Řetězec | Jeden [specifikátor formátu](/dotnet/api/system.guid.tostring?view=netcore-3.1#system_guid_tostring_system_string_) pro vrácený identifikátor GUID. Ve výchozím nastavení je formát "D", ale můžete použít "N", "D", "B", "P" nebo "X". |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Hodnota GUID*> | Řetězec | Náhodně generovaný identifikátor GUID |
||||

*Příklad*

Tento příklad generuje stejný identifikátor GUID, ale jako 32 číslic oddělených pomlčkami a uzavřený v závorkách:

```
guid('P')
```

A vrátí tento výsledek: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Zkontroluje, jestli je výraz pravdivý, nebo nepravdivý. Na základě výsledku vrátí zadanou hodnotu. Parametry jsou vyhodnocovány zleva doprava.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*vyjádření*> | Yes | Logická hodnota | Výraz, který se má kontrolovat |
| <*valueIfTrue*> | Yes | Libovolný | Hodnota, která se má vrátit, když je výraz pravdivý |
| <*valueIfFalse*> | Yes | Libovolný | Hodnota, která se má vrátit, pokud je výraz nepravdivý |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*zadané-návratové hodnoty*> | Libovolný | Zadaná hodnota, která se vrátí na základě toho, zda je výraz true nebo false |
||||

*Příklad*

Tento příklad vrátí, `"yes"` protože zadaný výraz vrátí hodnotu true.
V opačném případě vrátí příklad `"no"` :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Vrátí počáteční pozici nebo hodnotu indexu pro podřetězec.
Tato funkce nerozlišuje velká a malá písmena a indexy začínají číslem 0.

```
indexOf('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který obsahuje dílčí řetězec, který se má najít |
| <*Prohledávanýtext*> | Ano | Řetězec | Dílčí řetězec, který se má najít |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Hodnota indexu*>| Integer | Počáteční pozice nebo hodnota indexu pro zadaný dílčí řetězec. <p>Pokud řetězec nebyl nalezen, vrátí číslo-1. |
||||

*Příklad*

Tento příklad najde počáteční hodnotu indexu pro podřetězec "World" v řetězci "Hello World":

```
indexOf('hello world', 'world')
```

A vrátí tento výsledek: `6`

<a name="int"></a>

### <a name="int"></a>int

Vrátí celočíselnou verzi řetězce.

```
int('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec, který se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*celočíselný výsledek*> | Integer | Celočíselná verze zadaného řetězce |
||||

*Příklad*

Tento příklad vytvoří celočíselnou verzi pro řetězec "10":

```
int('10')
```

A vrátí tento výsledek: `10`

<a name="item"></a>

### <a name="item"></a>položka

Při použití v rámci opakující se akce nad polem vrátí aktuální položku v poli během aktuální iterace akce.
Můžete také získat hodnoty z vlastností dané položky.

```
item()
```

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*aktuální pole-položka*> | Libovolný | Aktuální položka v poli pro aktuální iteraci akce |
||||

*Příklad*

Tento příklad získá `body` prvek z aktuální zprávy pro akci "Send_an_email" v rámci aktuální iterace smyčky for-each:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>položek

Vrátí aktuální položku z každého cyklu smyčky for-each.
Tuto funkci použijte uvnitř smyčky for-each.

```
items('<loopName>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*opakovat*> | Ano | Řetězec | Název pro smyčku For-Each |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*položkami*> | Libovolný | Položka z aktuálního cyklu v zadaném pro – každou smyčku |
||||

*Příklad*

Tento příklad načte aktuální položku z určeného pro – každou smyčku:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Vrátí hodnotu indexu pro aktuální iteraci uvnitř smyčky do. Tuto funkci můžete použít uvnitř vnoření do smyčky. 

```
iterationIndexes('<loopName>')
```

| Parametr | Požaduje se | Typ | Description | 
| --------- | -------- | ---- | ----------- | 
| <*opakovat*> | Ano | Řetězec | Název pro smyčku do | 
||||| 

| Vrácená hodnota | Typ | Description | 
| ------------ | ---- | ----------- | 
| <*indexovacím*> | Integer | Hodnota indexu pro aktuální iteraci uvnitř zadaného do smyčky | 
|||| 

*Příklad* 

Tento příklad vytvoří proměnnou čítače a zvýší proměnnou o jednu během každé iterace ve smyčce do, dokud hodnota čítače nedosáhne pěti. V příkladu je také vytvořena proměnná, která sleduje aktuální index pro každou iteraci. V rámci smyčky do v rámci každé iterace příklad zvýší čítač a potom přiřadí hodnotu čítače k aktuální hodnotě indexu a poté zvýší čítač. Ve smyčce tento příklad odkazuje na aktuální index iterace pomocí `iterationIndexes` funkce:

`iterationIndexes('Until_Max_Increment')`

```json
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
      "Until_Max_Increment": {
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
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
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
         "expression": "@equals(variables('myCounter'), 5)",
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

Vrátí hodnotu nebo objekt typu JavaScript Object Notation (JSON) pro řetězec nebo XML.

```
json('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Yes | Řetězec nebo XML | Řetězec nebo XML, který se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Výsledek JSON*> | Nativní typ nebo objekt JSON | Hodnota nebo objekt nativního typu JSON pro zadaný řetězec nebo XML. Pokud je řetězec null, funkce vrátí prázdný objekt. |
||||

*Příklad 1*

Tento příklad převede tento řetězec na hodnotu JSON:

```
json('[1, 2, 3]')
```

A vrátí tento výsledek: `[1, 2, 3]`

*Příklad 2*

Tento příklad převede tento řetězec na formát JSON:

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

Tento příklad převede tento kód XML na formát JSON:

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

### <a name="intersection"></a>průnik

Vrátí kolekci, která má v zadaných kolekcích *jenom* společné položky.
Aby se položka zobrazila ve výsledku, musí se objevit ve všech kolekcích předaných této funkci.
Pokud má jedna nebo více položek stejný název, zobrazí se ve výsledku poslední položka se stejným názvem.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1* > < *Collection2* >... | Yes | Pole nebo objekt, ale ne obojí | Kolekce, ze kterých mají být *pouze* běžné položky |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*společné položky*> | Pole nebo objekt, v uvedeném pořadí | Kolekce, která má v zadaných kolekcích jenom společné položky |
||||

*Příklad*

Tento příklad najde společné položky v těchto polích:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

A vrátí pole *pouze* s následujícími položkami: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Vrátí řetězec, který má všechny položky z pole a má každý znak oddělený *oddělovačem*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Pole | Pole, ve kterém se mají položky připojit |
| <*oddělovač*> | Ano | Řetězec | Oddělovač, který se zobrazí mezi jednotlivými znaky ve výsledném řetězci |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*CHAR1* >< *oddělovač* >< *CHAR2* >< *oddělovač* >... | Řetězec | Výsledný řetězec vytvořený ze všech položek v zadaném poli |
||||

*Příklad*

Tento příklad vytvoří řetězec ze všech položek v tomto poli se zadaným znakem jako oddělovač:

```
join(createArray('a', 'b', 'c'), '.')
```

A vrátí tento výsledek: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>poslední

Vrátí poslední položku z kolekce.

```
last('<collection>')
last([<collection>])
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Řetězec nebo pole | Kolekce, ve které se má najít poslední položka |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Poslední kolekce – položka*> | Řetězec nebo pole, v uvedeném pořadí | Poslední položka v kolekci |
||||

*Příklad*

Tyto příklady vyhledají poslední položku v těchto kolekcích:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

A vrátí tyto výsledky:

* První příklad: `"d"`
* Druhý příklad: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Vrátí počáteční pozici nebo hodnotu indexu pro poslední výskyt podřetězce. Tato funkce nerozlišuje velká a malá písmena a indexy začínají číslem 0.

```json
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který obsahuje dílčí řetězec, který se má najít |
| <*Prohledávanýtext*> | Ano | Řetězec | Dílčí řetězec, který se má najít |
|||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*konec-index-hodnota*> | Integer | Počáteční pozice nebo hodnota indexu pro poslední výskyt zadaného podřetězce. |
|||

Je-li hodnota řetězce nebo podřetězce prázdná, dojde k následujícímu chování:

* Je-li hodnota řetězce prázdná, `-1` je vrácena:

* Pokud jsou hodnoty řetězců a podřetězců prázdné, `0` vrátí.

* Pokud je pouze hodnota podřetězce prázdná, je vrácena větší z následujících dvou hodnot:

  * `0`

  * Délka řetězce, mínus 1.

*Příklady*

Tento příklad najde počáteční hodnotu indexu pro poslední výskyt podřetězce podřetězce `world` v řetězci `hello world hello world` . Vrácený výsledek je `18` :

```json
lastIndexOf('hello world hello world', 'world')
```

V tomto příkladu chybí parametr SUBSTRING a vrátí hodnotu, `22` protože hodnota vstupního řetězce ( `23` ) minus 1 je větší než 0.

```json
lastIndexOf('hello world hello world', '')
```

<a name="length"></a>

### <a name="length"></a>length

Vrátí počet položek v kolekci.

```
length('<collection>')
length([<collection>])
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Řetězec nebo pole | Kolekce s položkami, které mají být počítány |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Délka nebo počet*> | Integer | Počet položek v kolekci |
||||

*Příklad*

Tyto příklady počítají počet položek v těchto kolekcích:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

A vrátí tento výsledek: `4`

<a name="less"></a>

### <a name="less"></a>less

Ověřte, zda je první hodnota menší než druhá hodnota.
Vrátí hodnotu true, pokud je první hodnota menší, nebo vrátí hodnotu false, pokud je první hodnota větší.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Yes | Integer, float nebo String | První hodnota, která ověří, zda je menší než druhá hodnota |
| <*compareTo*> | Yes | Integer, float nebo String, v uvedeném pořadí | Položka porovnání |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud je první hodnota menší než druhá hodnota. Vrátí hodnotu false, pokud je první hodnota rovna nebo větší než druhá hodnota. |
||||

*Příklad*

Tyto příklady kontrolují, zda je první hodnota menší než druhá hodnota.

```
less(5, 10)
less('banana', 'apple')
```

A vrátí tyto výsledky:

* První příklad: `true`
* Druhý příklad: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Ověřte, zda je první hodnota menší nebo rovna druhé hodnotě.
Vrátí hodnotu true, pokud je první hodnota menší nebo rovna, nebo vrátí hodnotu false, pokud je první hodnota větší.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Yes | Integer, float nebo String | První hodnota, která kontroluje, zda je menší nebo rovna druhé hodnotě |
| <*compareTo*> | Yes | Integer, float nebo String, v uvedeném pořadí | Položka porovnání |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false  | Logická hodnota | Vrátí hodnotu true, pokud je první hodnota menší nebo rovna druhé hodnotě. Vrátí hodnotu false, pokud je první hodnota větší než druhá hodnota. |
||||

*Příklad*

Tyto příklady kontrolují, zda je první hodnota menší nebo stejná jako druhá hodnota.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

A vrátí tyto výsledky:

* První příklad: `true`
* Druhý příklad: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Vrátí adresu URL zpětného volání, která volá Trigger nebo akci.
Tato funkce funguje pouze s triggery a akcemi pro typy konektorů **HttpWebhook** a **vstupech apiconnectionwebhook** , ale ne typy **manuální** , **Opakovat** , **http** a **vstupech apiconnection** .

```
listCallbackUrl()
```

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*zpětné volání – adresa URL*> | Řetězec | Adresa URL zpětného volání pro aktivační událost nebo akci |
||||

*Příklad*

Tento příklad ukazuje ukázkovou adresu URL zpětného volání, kterou může tato funkce vrátit:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Vrátí nejvyšší hodnotu ze seznamu nebo pole s čísly, která jsou zahrnuta na obou koncích.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*číslo1* >, < *číslo2* >,... | Yes | Integer, float nebo Both | Sada čísel, ze kterých má být nejvyšší hodnota |
| [< *číslo1* >, < *číslo2* >,...] | Yes | Array – celé číslo, float nebo obojí | Pole čísel, ze kterých má být nejvyšší hodnota |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Max – hodnota*> | Integer nebo float | Nejvyšší hodnota v zadaném poli nebo sadě čísel |
||||

*Příklad*

Tyto příklady získávají nejvyšší hodnotu ze sady čísel a pole:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

A vrátí tento výsledek: `3`

<a name="min"></a>

### <a name="min"></a>min

Vrátí nejnižší hodnotu ze sady čísel nebo pole.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*číslo1* >, < *číslo2* >,... | Yes | Integer, float nebo Both | Sada čísel, ze kterých má být nejnižší hodnota |
| [< *číslo1* >, < *číslo2* >,...] | Yes | Array – celé číslo, float nebo obojí | Pole čísel, ze kterých má být nejnižší hodnota |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*minimální hodnota*> | Integer nebo float | Nejnižší hodnota v zadané sadě čísel nebo zadaného pole |
||||

*Příklad*

Tyto příklady získají nejnižší hodnotu v sadě čísel a poli:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

A vrátí tento výsledek: `1`

<a name="mod"></a>

### <a name="mod"></a>střední

Vrátí zbytek po dělení dvou čísel.
Chcete-li získat celočíselný výsledek, viz [div ()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*podíl*> | Yes | Integer nebo float | Číslo, které se má rozdělit *dělitelem* |
| <*dělitel*> | Yes | Integer nebo float | Číslo, které vydělí *dividendy* , ale nemůže být 0. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*modulo – výsledek*> | Integer nebo float | Zbytek po dělení prvního čísla druhým číslem |
||||

*Příklad*

Tento příklad vydělí první číslo druhým číslem:

```
mod(3, 2)
```

A vrátí tento výsledek: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Vrátí produkt pro vynásobení dvou čísel.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Yes | Integer nebo float | Číslo, které se má vynásobit *multiplicand2* |
| <*multiplicand2*> | Yes | Integer nebo float | Číslo, které *multiplicand1* násobek |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*výsledek produktu*> | Integer nebo float | Součin pro násobení prvního čísla druhým číslem |
||||

*Příklad*

Tyto příklady vynásobí první číslo druhým číslem:

```
mul(1, 2)
mul(1.5, 2)
```

A vrátí tyto výsledky:

* První příklad: `2`
* Druhý příklad `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Vrátí text pro určitou část výstupu akce, která má více částí.

```
multipartBody('<actionName>', <index>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ano | Řetězec | Název akce, která má výstup s více částmi |
| <*indexovacím*> | Yes | Integer | Hodnota indexu pro část, kterou chcete |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*těles*> | Řetězec | Tělo pro určenou část |
||||

<a name="not"></a>

### <a name="not"></a>not

Zkontroluje, jestli je výraz nepravdivý.
Vrátí hodnotu true, pokud má výraz hodnotu false, nebo vrátí hodnotu false, pokud má hodnotu true.

```json
not(<expression>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*vyjádření*> | Yes | Logická hodnota | Výraz, který se má kontrolovat |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud je výraz nepravdivý. Vrátí hodnotu false, pokud má výraz hodnotu true. |
||||

*Příklad 1*

Tyto příklady kontrolují, zda jsou zadané výrazy nepravdivé:

```json
not(false)
not(true)
```

A vrátí tyto výsledky:

* První příklad: výraz má hodnotu false, takže funkce vrátí hodnotu `true` .
* Druhý příklad: výraz má hodnotu true, takže funkce vrátí hodnotu `false` .

*Příklad 2*

Tyto příklady kontrolují, zda jsou zadané výrazy nepravdivé:

```json
not(equals(1, 2))
not(equals(1, 1))
```

A vrátí tyto výsledky:

* První příklad: výraz má hodnotu false, takže funkce vrátí hodnotu `true` .
* Druhý příklad: výraz má hodnotu true, takže funkce vrátí hodnotu `false` .

<a name="or"></a>

### <a name="or"></a>nebo

Ověřte, zda je alespoň jeden výraz pravdivý.
Vrátí hodnotu true, pokud má alespoň jeden výraz hodnotu true, nebo vrátí hodnotu false, pokud všechny mají hodnotu false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*výraz1* >, < *Výraz2* >,... | Yes | Logická hodnota | Výrazy, které mají být zkontrolovány |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud má alespoň jeden výraz hodnotu true. Vrátí hodnotu false, pokud jsou všechny výrazy nepravdivé. |
||||

*Příklad 1*

Tyto příklady kontrolují, zda alespoň jeden výraz má hodnotu true:

```json
or(true, false)
or(false, false)
```

A vrátí tyto výsledky:

* První příklad: nejméně jeden výraz má hodnotu true, takže funkce vrátí hodnotu `true` .
* Druhý příklad: oba výrazy mají hodnotu false, takže funkce vrátí hodnotu `false` .

*Příklad 2*

Tyto příklady kontrolují, zda alespoň jeden výraz má hodnotu true:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

A vrátí tyto výsledky:

* První příklad: nejméně jeden výraz má hodnotu true, takže funkce vrátí hodnotu `true` .
* Druhý příklad: oba výrazy mají hodnotu false, takže funkce vrátí hodnotu `false` .

<a name="outputs"></a>

### <a name="outputs"></a>činnosti

Vrátí výstupy akce za běhu. Použijte tuto funkci, nikoli `actionOutputs()` , která se přeloží na `outputs()` v návrháři aplikace logiky. I když obě funkce fungují stejným způsobem, `outputs()` jsou upřednostňovány.

```
outputs('<actionName>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ano | Řetězec | Název výstupu akce, který chcete |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | -----| ----------- |
| <*výkonem*> | Řetězec | Výstup ze zadané akce |
||||

*Příklad*

Tento příklad načte výstup z akce Twitteru `Get user` :

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

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Ano | Řetězec | Název parametru, jehož hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*hodnota parametru*> | Libovolný | Hodnota pro zadaný parametr |
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

A vrátí tento výsledek: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Vrátí náhodné celé číslo ze zadaného rozsahu, který je zahrnut pouze na počátečním konci.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Yes | Integer | Nejnižší celé číslo v rozsahu |
| <*maxValue*> | Yes | Integer | Celé číslo, které následuje po nejvyšší celé číslo v rozsahu, který může funkce vracet |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*náhodný výsledek*> | Integer | Náhodné celé číslo vrácené ze zadaného rozsahu |
||||

*Příklad*

Tento příklad získá náhodné celé číslo ze zadaného rozsahu s výjimkou maximální hodnoty:

```
rand(1, 5)
```

A vrátí jeden z těchto čísel jako výsledek: `1` , `2` , `3` nebo `4`

<a name="range"></a>

### <a name="range"></a>range

Vrátí celočíselné pole, které začíná zadaným celým číslem.

```
range(<startIndex>, <count>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*Indexu*> | Yes | Integer | Celočíselná hodnota, která spouští pole jako první položku |
| <*výpočtu*> | Yes | Integer | Počet celých čísel v poli |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [< *Rozsah-> výsledků* ] | Pole | Pole s celými čísly počínaje zadaným indexem |
||||

*Příklad*

Tento příklad vytvoří celočíselné pole, které začíná od zadaného indexu a má zadaný počet celých čísel:

```
range(1, 4)
```

A vrátí tento výsledek: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>náhrady

Nahraďte podřetězec zadaným řetězcem a vraťte výsledný řetězec. Tato funkce rozlišuje velká a malá písmena.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který má podřetězec, který má být nahrazen |
| <*oldText*> | Ano | Řetězec | Dílčí řetězec, který má být nahrazen |
| <*newText*> | Ano | Řetězec | Řetězec pro nahrazení |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*aktualizovaný text*> | Řetězec | Aktualizovaný řetězec po nahrazení podřetězce <p>Pokud dílčí řetězec nebyl nalezen, vrátí původní řetězec. |
||||

*Příklad*

Tento příklad najde "starý" podřetězec v "starém řetězci" a nahradí "Old" novou:

```
replace('the old string', 'old', 'new')
```

A vrátí tento výsledek: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Odebere vlastnost z objektu a vrátí aktualizovaný objekt. Pokud vlastnost, kterou se pokoušíte odstranit, neexistuje, funkce vrátí původní objekt.

```
removeProperty(<object>, '<property>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*předmětů*> | Yes | Objekt | Objekt JSON, ze kterého chcete odebrat vlastnost |
| <*majetek*> | Ano | Řetězec | Název vlastnosti, která se má odebrat |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – objekt*> | Objekt | Aktualizovaný objekt JSON bez zadané vlastnosti |
||||

Chcete-li odebrat podřízenou vlastnost z existující vlastnosti, použijte tuto syntaxi:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*předmětů*> | Yes | Objekt | Objekt JSON, jehož vlastnost se má odebrat |
| <*Nadřazená vlastnost*> | Ano | Řetězec | Název nadřazené vlastnosti s podřízenou vlastností, kterou chcete odebrat |
| <*podřízená vlastnost*> | Ano | Řetězec | Název pro podřízenou vlastnost, která se má odebrat |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – objekt*> | Objekt | Aktualizovaný objekt JSON, jehož podřízená vlastnost byla odebrána |
||||

*Příklad 1*

Tento příklad odebere `middleName` vlastnost z objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON ()](#json) a vrátí aktualizovaný objekt:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Tady je aktuální objekt JSON:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Tady je aktualizovaný objekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Příklad 2*

Tento příklad odebere `middleName` podřízenou vlastnost z `customerName` nadřazené vlastnosti v objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON ()](#json) a vrátí aktualizovaný objekt:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Tady je aktuální objekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Tady je aktualizovaný objekt JSON:

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

Vrátí vstupy a výstupy ze všech akcí, které jsou uvnitř zadané akce s vymezeným oborem, jako je například `For_each` , `Until` nebo `Scope` akce. Tato funkce je užitečná pro vrácení výsledků z neúspěšné akce, abyste mohli diagnostikovat a zpracovávat výjimky. Další informace najdete v tématu [získání kontextu a výsledků pro selhání](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Ano | Řetězec | Název akce s vymezeným oborem, ze které se mají vrátit vstupy a výstupy ze všech vnitřních akcí |
||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Array – objekt*> | Array – objekt | Pole, které obsahuje pole vstupů a výstupů z každé akce, která se zobrazí v rámci zadané akce s oborem |
||||

*Příklad*

Tento příklad vrátí vstupy a výstupy z každé iterace akce HTTP uvnitř `For_each` smyčky pomocí `result()` funkce v `Compose` akci:

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

Zde je uvedeno, jak vrácený příklad pole může vypadat tam, kde vnější `outputs` objekt obsahuje vstupy a výstupy z každé iterace akcí uvnitř `For_each` akce.

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

### <a name="setproperty"></a>setProperty

Nastavte hodnotu vlastnosti objektu JSON a vraťte aktualizovaný objekt. Pokud vlastnost, kterou se pokoušíte nastavit, neexistuje, vlastnost se přidá do objektu. Chcete-li přidat novou vlastnost, použijte funkci [AddProperty ()](#addProperty) .

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*předmětů*> | Yes | Objekt | Objekt JSON, jehož vlastnost má být nastavena |
| <*majetek*> | Ano | Řetězec | Název existující nebo nové vlastnosti, která se má nastavit |
| <*osa*> | Yes | Libovolný | Hodnota, která se má nastavit pro zadanou vlastnost |
|||||

Chcete-li nastavit podřízenou vlastnost v podřízeném objektu, použijte `setProperty()` místo toho vnořené volání. V opačném případě funkce vrátí pouze podřízený objekt jako výstup.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*předmětů*> | Yes | Objekt | Objekt JSON, jehož vlastnost má být nastavena |
| <*Nadřazená vlastnost*> | Ano | Řetězec | Název nadřazené vlastnosti s podřízenou vlastností, kterou chcete nastavit |
| <*podřízená vlastnost*> | Ano | Řetězec | Název podřízené vlastnosti, která se má nastavit |
| <*osa*> | Yes | Libovolný | Hodnota, která se má nastavit pro zadanou vlastnost |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – objekt*> | Objekt | Aktualizovaný objekt JSON, jehož vlastnost nastavena |
||||

*Příklad 1*

Tento příklad nastaví `surName` vlastnost v objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON ()](#json) . Funkce přiřadí zadanou hodnotu k vlastnosti a vrátí aktualizovaný objekt:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Tady je aktuální objekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Tady je aktualizovaný objekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Příklad 2*

Tento příklad nastaví `surName` podřízenou vlastnost pro `customerName` nadřazenou vlastnost v objektu JSON, který je převeden z řetězce na JSON pomocí funkce [JSON ()](#json) . Funkce přiřadí zadanou hodnotu k vlastnosti a vrátí aktualizovaný objekt:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Tady je aktuální objekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Tady je aktualizovaný objekt JSON:

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

Odebere položky z přední části kolekce a vrátí *všechny ostatní* položky.

```
skip([<collection>], <count>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Pole | Kolekce, jejíž položky chcete odebrat |
| <*výpočtu*> | Yes | Integer | Kladné celé číslo pro počet položek, které se mají odebrat na začátku |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [< *aktualizováno> kolekce* ] | Pole | Aktualizovaná kolekce po odebrání zadaných položek |
||||

*Příklad*

Tento příklad odebere jednu položku, číslo 0, od začátku zadaného pole:

```
skip(createArray(0, 1, 2, 3), 1)
```

A vrátí toto pole se zbývajícími položkami: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Vrátí pole obsahující podřetězce oddělené čárkami na základě zadaného oddělovače v původním řetězci.

```
split('<text>', '<delimiter>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který se má oddělit k podřetězcům na základě zadaného oddělovače v původním řetězci |
| <*oddělovač*> | Ano | Řetězec | Znak v původním řetězci, který má být použit jako oddělovač |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [< *subřetězec1* >, < *subřetězec2* >,...] | Pole | Pole, které obsahuje podřetězce z původního řetězce oddělené čárkami |
||||

*Příklad*

Tento příklad vytvoří pole s podřetězci ze zadaného řetězce založeného na zadaném znaku jako oddělovač:

```
split('a_b_c', '_')
```

A vrátí toto pole jako výsledek: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Vrátí začátek dne pro časové razítko.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Zadané časové razítko, ale začíná nulovou časovou značkou dne |
||||

*Příklad*

Tento příklad najde začátek dne pro toto časové razítko:

```
startOfDay('2018-03-15T13:30:30Z')
```

A vrátí tento výsledek: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Vrátí začátek hodiny pro časové razítko.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Zadané časové razítko, ale začíná na označení 0 minuta za hodinu |
||||

*Příklad*

Tento příklad najde začátek hodiny pro toto časové razítko:

```
startOfHour('2018-03-15T13:30:30Z')
```

A vrátí tento výsledek: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Vrátí začátek měsíce pro časové razítko.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Zadané časové razítko, ale počínaje prvním dnem v měsíci s označením nulové hodiny |
||||

*Příklad 1*

Tento příklad vrátí začátek měsíce pro toto časové razítko:

```
startOfMonth('2018-03-15T13:30:30Z')
```

A vrátí tento výsledek: `"2018-03-01T00:00:00.0000000Z"`

*Příklad 2*

Tento příklad vrátí začátek měsíce v zadaném formátu pro toto časové razítko:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

A vrátí tento výsledek: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Kontroluje, zda řetězec začíná konkrétním podřetězcem.
Vrátí hodnotu true, pokud se podřetězec najde, nebo vrátí hodnotu false, pokud nebyla nalezena.
Tato funkce nerozlišuje velká a malá písmena.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který má být zkontrolován |
| <*Prohledávanýtext*> | Ano | Řetězec | Hledaný řetězec, který se má najít |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false  | Logická hodnota | Vrátí hodnotu pravda, pokud je nalezen počáteční podřetězec. Pokud se nenajde, vrátí se hodnota false. |
||||

*Příklad 1*

Tento příklad kontroluje, zda řetězec "Hello World" začíná řetězcem "Hello":

```
startsWith('hello world', 'hello')
```

A vrátí tento výsledek: `true`

*Příklad 2*

Tento příklad kontroluje, zda řetězec "Hello World" začíná řetězcem "pozdravy":

```
startsWith('hello world', 'greetings')
```

A vrátí tento výsledek: `false`

<a name="string"></a>

### <a name="string"></a>řetězec

Vrátí verzi řetězce pro hodnotu.

```
string(<value>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Yes | Libovolný | Hodnota, kterou chcete převést. Pokud je tato hodnota null nebo je vyhodnocena jako null, hodnota je převedena na prázdnou řetězcovou `""` hodnotu (). <p><p>Například pokud přiřadíte proměnnou řetězce k neexistující vlastnosti, ke které máte přístup pomocí `?` operátoru, hodnota null je převedena na prázdný řetězec. Porovnávání hodnoty null však není stejné jako porovnávání prázdného řetězce. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*hodnota řetězce*> | Řetězec | Verze řetězce pro zadanou hodnotu Pokud má parametr *Value* hodnotu null nebo je vyhodnocen jako null, tato hodnota se vrátí jako prázdná hodnota řetězce ( `""` ). |
||||





*Příklad 1*

Tento příklad vytvoří verzi řetězce pro toto číslo:

```
string(10)
```

A vrátí tento výsledek: `"10"`

*Příklad 2*

Tento příklad vytvoří řetězec pro zadaný objekt JSON a používá znak zpětného lomítka ( \\ ) jako řídicí znak pro dvojité uvozovky (").

```
string( { "name": "Sophie Owen" } )
```

A vrátí tento výsledek: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>jednotk

Vrátí výsledek odečtením druhého čísla od prvního čísla.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Yes | Integer nebo float | Číslo, od kterého se má odečíst *subtrahend* |
| <*subtrahend*> | Yes | Integer nebo float | Číslo, které se má odečíst od *minuend* |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*vyústit*> | Integer nebo float | Výsledek odečte druhé číslo od prvního čísla. |
||||

*Příklad*

Tento příklad odečte druhé číslo od prvního čísla:

```
sub(10.3, .3)
```

A vrátí tento výsledek: `10`

<a name="substring"></a>

### <a name="substring"></a>podřetězec

Vrátí znaky z řetězce počínaje zadaným umístěním nebo indexem. Hodnoty indexu začínají číslem 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, jehož znaky chcete |
| <*Indexu*> | Yes | Integer | Kladné číslo větší nebo rovno 0, které chcete použít jako počáteční pozici nebo hodnotu indexu. |
| <*časový*> | No | Integer | Kladný počet znaků, které chcete v podřetězci. |
|||||

> [!NOTE]
> Ujistěte se, že součet z přidání hodnot parametrů *startIndex* a *Length* je menší než délka řetězce, který je k dispozici pro parametr *text* .
> V opačném případě se zobrazí chyba, na rozdíl od podobných funkcí v jiných jazycích, kde výsledek je podřetězec z *startIndex* na konec řetězce. Parametr *Length* je nepovinný, a pokud není zadaný, funkce **substring ()** přebírá všechny znaky začínající od *startIndex* do konce řetězce.

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*podřetězec – výsledek*> | Řetězec | Podřetězec, který má zadaný počet znaků od zadané pozice indexu ve zdrojovém řetězci |
||||

*Příklad*

Tento příklad vytvoří podřetězec s pěti znaky ze zadaného řetězce počínaje hodnotou indexu 6:

```
substring('hello world', 6, 5)
```

A vrátí tento výsledek: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Odečte počet časových jednotek od časového razítka.
Viz také [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*doba*> | Yes | Integer | Počet zadaných časových jednotek k odečtení |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, která se má použít v *intervalu* : "druhé", "Minute", "hodina", "den", "týden", "měsíc", "rok" |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Časové razítko minus zadaný počet časových jednotek |
||||

*Příklad 1*

Tento příklad odečte jeden den od tohoto časového razítka:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

A vrátí tento výsledek: `"2018-01-01T00:00:00:0000000Z"`

*Příklad 2*

Tento příklad odečte jeden den od tohoto časového razítka:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

A vrátí tento výsledek pomocí volitelného formátu "D": `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Vrátí položky od začátku kolekce.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Řetězec nebo pole | Kolekce, jejíž položky chcete |
| <*výpočtu*> | Yes | Integer | Celé kladné číslo pro počet položek, které požadujete od začátku |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*podmnožina* > nebo [ *podmnožina* <>] | Řetězec nebo pole, v uvedeném pořadí | Řetězec nebo pole, které má zadaný počet položek od začátku původní kolekce |
||||

*Příklad*

Tyto příklady získají zadaný počet položek od začátku těchto kolekcí:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

A vrátí tyto výsledky:

* První příklad: `"abc"`
* Druhý příklad: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Ticks

Vrátí počet impulsů, které jsou 100, od 1. ledna 0001 12:00:00 půlnoci (nebo DateTime. Ticks v jazyce C#) až po zadané časové razítko. Další informace najdete v tomto tématu ve [vlastnosti DateTime. Ticks (System)](/dotnet/api/system.datetime.ticks?view=netframework-4.7.2#remarks).

```
ticks('<timestamp>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec pro časové razítko |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*takty – číslo*> | Integer | Počet tiků od zadaného časového razítka |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Vrátí řetězec ve formátu s malými písmeny. Pokud znak v řetězci nemá malá verze, tento znak zůstane ve vráceném řetězci beze změny.

```
toLower('<text>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který se má vrátit ve formátu malého písmene |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*malý text*> | Řetězec | Původní řetězec ve formátu malými písmeny |
||||

*Příklad*

Tento příklad převede tento řetězec na malá písmena:

```
toLower('Hello World')
```

A vrátí tento výsledek: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Vrátí řetězec ve formátu velkými písmeny. Pokud znak v řetězci nemá velké písmeno, tento znak zůstane ve vráceném řetězci beze změny.

```
toUpper('<text>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který se má vrátit ve formátu velkého písmene |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Velká písmena textu*> | Řetězec | Původní řetězec ve formátu velkých písmen |
||||

*Příklad*

Tento příklad převede tento řetězec na velká písmena:

```
toUpper('Hello World')
```

A vrátí tento výsledek: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>signálu

Vrátí výstup triggeru za běhu nebo hodnoty z jiných párů názvu a hodnoty JSON, které můžete přiřadit k výrazu.

* Ve vstupech triggeru Tato funkce vrátí výstup z předchozího spuštění.

* V rámci podmínky triggeru Tato funkce vrátí výstup z aktuálního spuštění.

Ve výchozím nastavení funkce odkazuje na celý objekt triggeru, ale můžete volitelně zadat vlastnost, jejíž hodnota má být.
Tato funkce má také k dispozici zkrácený počet verzí, viz [triggerOutputs ()](#triggerOutputs) a [triggerBody ()](#triggerBody).

```
trigger()
```

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*aktivační procedura – výstup*> | Řetězec | Výstup triggeru za běhu |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Vrátí výstup triggeru `body` za běhu.
Zkrácený pro `trigger().outputs.body` .
Viz [Trigger ()](#trigger).

```
triggerBody()
```

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*aktivační text – výstup*> | Řetězec | `body`Výstup triggeru |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Vrátí pole s hodnotami, které odpovídají názvu klíče ve výstupu triggeru *formuláře* *nebo formuláře.*

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*zkrat*> | Ano | Řetězec | Název pro klíč, jehož hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [< *pole-s-klíč-hodnoty* >] | Pole | Pole se všemi hodnotami, které odpovídají zadanému klíči |
||||

*Příklad*

Tento příklad vytvoří pole z hodnoty klíče "feedUrl" v aktivačním formuláři – data nebo ve výstupu s kódováním informačního formátu RSS:

```
triggerFormDataMultiValues('feedUrl')
```

A vrátí toto pole jako výsledek příkladu: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Vrátí řetězec s jednou hodnotou, která odpovídá názvu klíče ve výstupu triggeru *formuláře* nebo formuláře s *kódováním* .
Pokud funkce najde více než jednu shodu, funkce vyvolá chybu.

```
triggerFormDataValue('<key>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*zkrat*> | Ano | Řetězec | Název pro klíč, jehož hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*hodnota klíče*> | Řetězec | Hodnota v zadaném klíči |
||||

*Příklad*

Tento příklad vytvoří řetězec z hodnoty klíče "feedUrl" v aktivačním formuláři-data nebo ve výstupu s kódováním informačního formátu RSS:

```
triggerFormDataValue('feedUrl')
```

A vrátí tento řetězec jako příklad výsledku: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Vrátí tělo konkrétní části ve výstupu triggeru, který má více částí.

```
triggerMultipartBody(<index>)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*indexovacím*> | Yes | Integer | Hodnota indexu pro část, kterou chcete |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*těles*> | Řetězec | Tělo zadané části výstupu triggeru |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Vrátí výstup triggeru za běhu nebo hodnoty z jiných párů názvu a hodnoty JSON.
Zkrácený pro `trigger().outputs` .
Viz [Trigger ()](#trigger).

```
triggerOutputs()
```

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*aktivační procedura – výstup*> | Řetězec | Výstup triggeru za běhu  |
||||

<a name="trim"></a>

### <a name="trim"></a>sklon

Odstraňte úvodní a koncové mezery z řetězce a vraťte aktualizovaný řetězec.

```
trim('<text>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který má počáteční a koncovou mezeru, která se má odebrat |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*updatedText*> | Řetězec | Aktualizovaná verze původního řetězce bez počátečního nebo koncového znaku |
||||

*Příklad*

Tento příklad odstraní úvodní a koncové mezery z řetězce "Hello World":

```
trim(' Hello World  ')
```

A vrátí tento výsledek: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>sjednocení

Vrátí kolekci, která obsahuje *všechny* položky ze zadaných kolekcí.
Aby se zobrazila ve výsledku, může se položka objevit v jakékoli kolekci předané této funkci. Pokud má jedna nebo více položek stejný název, zobrazí se ve výsledku poslední položka se stejným názvem.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1* > < *Collection2* >...  | Yes | Pole nebo objekt, ale ne obojí | Kolekce, ze kterých chcete *všechny* položky |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*aktualizacecollection*> | Pole nebo objekt, v uvedeném pořadí | Kolekce se všemi položkami ze zadané kolekce – žádné duplicity |
||||

*Příklad*

Tento příklad načte *všechny* položky z těchto kolekcí:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

A vrátí tento výsledek: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Nahrazením znaků, které nejsou v adrese URL, pomocí řídicích znaků vrátíte verzi kódovanou podle identifikátoru URI (Uniform Resource Identifier) pro řetězec.
Použijte tuto funkci místo [encodeURIComponent – ()](#encodeUriComponent).
I když obě funkce fungují stejným způsobem, `uriComponent()` jsou upřednostňovány.

```
uriComponent('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec, který se má převést na formát kódovaný identifikátorem URI |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*kódovaný identifikátor URI*> | Řetězec | Řetězec kódovaný identifikátorem URI s řídicími znaky |
||||

*Příklad*

Tento příklad vytvoří pro tento řetězec verzi kódovanou pomocí identifikátoru URI:

```
uriComponent('https://contoso.com')
```

A vrátí tento výsledek: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Vrátí binární verzi součásti identifikátoru URI (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec kódovaný identifikátorem URI, který se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*binární-pro kódovaný identifikátor URI*> | Řetězec | Binární verze řetězce zakódovaného identifikátorem URI. Binární obsah je kódovaný v kódování Base64 a reprezentován `$content` . |
||||

*Příklad*

Tento příklad vytvoří binární verzi pro tento řetězec kódovaný pomocí identifikátoru URI:

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

Vrátí verzi řetězce pro řetězec kódovaný v identifikátoru URI (Uniform Resource Identifier), který efektivně Dekóduje řetězec kódovaný identifikátorem URI.

```
uriComponentToString('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec kódovaný identifikátorem URI k dekódování |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Dekódovatelné – identifikátor URI*> | Řetězec | Dekódovaná verze řetězce zakódovaného identifikátorem URI |
||||

*Příklad*

Tento příklad vytvoří dekódovanou verzi řetězce pro tento řetězec kódovaný pomocí identifikátoru URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

A vrátí tento výsledek: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Vrátí `host` hodnotu identifikátoru URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI, jehož `host` hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*hodnota hosta*> | Řetězec | `host`Hodnota pro zadaný identifikátor URI |
||||

*Příklad*

Tento příklad najde `host` hodnotu pro tento identifikátor URI:

```
uriHost('https://www.localhost.com:8080')
```

A vrátí tento výsledek: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Vrátí `path` hodnotu identifikátoru URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI, jehož `path` hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*hodnota Path*> | Řetězec | `path`Hodnota pro zadaný identifikátor URI. Pokud `path` hodnota nemá, vrátí znak "/". |
||||

*Příklad*

Tento příklad najde `path` hodnotu pro tento identifikátor URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí tento výsledek: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Vrátí `path` hodnoty a `query` pro identifikátor URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI `path` , jehož `query` hodnoty chcete |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*cesta-dotaz-hodnota*> | Řetězec | `path`Hodnoty a `query` pro zadaný identifikátor URI. Pokud `path` nezadáte hodnotu, vrátí znak "/". |
||||

*Příklad*

Tento příklad najde `path` hodnoty a `query` pro tento identifikátor URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí tento výsledek: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Vrátí `port` hodnotu identifikátoru URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI, jehož `port` hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Hodnota portu*> | Integer | `port`Hodnota pro zadaný identifikátor URI. Pokud `port` nezadáte hodnotu, vraťte výchozí port pro protokol. |
||||

*Příklad*

Tento příklad vrátí `port` hodnotu pro tento identifikátor URI:

```
uriPort('http://www.localhost:8080')
```

A vrátí tento výsledek: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Vrátí `query` hodnotu identifikátoru URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI, jehož `query` hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*dotaz – hodnota*> | Řetězec | `query`Hodnota pro zadaný identifikátor URI |
||||

*Příklad*

Tento příklad vrátí `query` hodnotu pro tento identifikátor URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí tento výsledek: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Vrátí `scheme` hodnotu identifikátoru URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*identifikátor URI*> | Ano | Řetězec | Identifikátor URI, jehož `scheme` hodnota má být |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*hodnota schématu*> | Řetězec | `scheme`Hodnota pro zadaný identifikátor URI |
||||

*Příklad*

Tento příklad vrátí `scheme` hodnotu pro tento identifikátor URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

A vrátí tento výsledek: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Vrátí aktuální časové razítko.

```
utcNow('<format>')
```

Volitelně můžete zadat jiný formát s parametrem < *formát* >.


| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*aktuální – časové razítko*> | Řetězec | Aktuální datum a čas |
||||

*Příklad 1*

Dnes Předpokládejme 15. dubna 2018 v 1:00:00 odp.
Tento příklad získá aktuální časové razítko:

```
utcNow()
```

A vrátí tento výsledek: `"2018-04-15T13:00:00.0000000Z"`

*Příklad 2*

Dnes Předpokládejme 15. dubna 2018 v 1:00:00 odp.
Tento příklad získá aktuální časové razítko pomocí volitelného formátu "D":

```
utcNow('D')
```

A vrátí tento výsledek: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>proměnné

Vrátí hodnotu pro zadanou proměnnou.

```
variables('<variableName>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Ano | Řetězec | Název proměnné, jejíž hodnotu chcete |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Proměnná – hodnota*> | Libovolný | Hodnota pro zadanou proměnnou |
||||

*Příklad*

Předpokládejme, že aktuální hodnota proměnné "numItems" je 20.
Tento příklad získá celočíselnou hodnotu pro tuto proměnnou:

```
variables('numItems')
```

A vrátí tento výsledek: `20`

<a name="workflow"></a>

### <a name="workflow"></a>pracovní postup

Vrátí všechny podrobnosti o pracovním postupu samotném během běhu.

```
workflow().<property>
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*majetek*> | No | Řetězec | Název vlastnosti pracovního postupu, jejíž hodnota má být <p>Objekt pracovního postupu má tyto vlastnosti: **název** , **typ** , **ID** , **umístění** a **spuštění**. Hodnota vlastnosti **spuštění** je také objekt, který má tyto vlastnosti: **název** , **typ** a **ID**. |
|||||

*Příklad*

Tento příklad vrátí název aktuálního běhu pracovního postupu:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Vrátí verzi XML pro řetězec, který obsahuje objekt JSON.

```
xml('<value>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec s objektem JSON, který se má převést <p>Objekt JSON musí mít pouze jednu kořenovou vlastnost, která nemůže být polem. <br>\\Jako řídicí znak dvojité uvozovky (") použijte znak zpětného lomítka (). |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*verze XML*> | Objekt | Kódovaný kód XML pro zadaný řetězec nebo objekt JSON |
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

### <a name="xpath"></a>cestou

Zkontroluje XML pro uzly nebo hodnoty, které odpovídají výrazu XPath (XML Path Language), a vrátí odpovídající uzly nebo hodnoty. Výraz XPath, nebo pouze "XPath", vám pomůže přejít na strukturu dokumentu XML, abyste mohli vybrat uzly nebo výpočetní hodnoty v obsahu XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Požaduje se | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*XML*> | Yes | Libovolný | Řetězec XML pro vyhledání uzlů nebo hodnot, které odpovídají hodnotě výrazu XPath |
| <*cestou*> | Yes | Libovolný | Výraz XPath, který slouží k vyhledání odpovídajícího uzlu XML nebo hodnot |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*uzel XML*> | XML | Uzel XML, pokud pouze jeden uzel odpovídá zadanému výrazu XPath |
| <*osa*> | Libovolný | Hodnota z uzlu XML, pokud je pouze jedna hodnota shodná se zadaným výrazem XPath |
| [< *XML-uzel1* >, < *xml-Uzel2* >,...] </br>-nebo- </br>[< *hodnota1* >, < *hodnota2* >,...] | Pole | Pole s uzly XML nebo hodnotami, které odpovídají zadanému výrazu XPath |
||||

*Příklad 1*

Předpokládejme, že máte tento `'items'` řetězec XML: 

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Tento příklad projde ve výrazu XPath, `'/produce/item/name'` k nalezení uzlů, které se shodují s `<name></name>` uzlem v `'items'` řetězci XML a vrátí pole s těmito hodnotami uzlu:

`xpath(xml(parameters('items')), '/produce/item/name')`

Příklad také používá funkci [Parameters ()](#parameters) pro získání řetězce XML z `'items'` a převod řetězce do formátu XML pomocí funkce [XML ()](#xml) .

Toto je pole výsledku s uzly, které se shodují `<name></name` :

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Příklad 2*

V příkladu 1 Tento příklad předává ve výrazu XPath, `'/produce/item/name[1]'` k nalezení prvního `name` prvku, který je podřízený `item` elementu.

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Tady je výsledek: `Gala`

*Příklad 3*

V příkladu 1 Tento příklad přechází ve výrazu XPath, `'/produce/item/name[last()]'` , aby bylo možné najít poslední `name` prvek, který je podřízený `item` elementu.

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Tady je výsledek: `Honeycrisp`

*Příklad 4*

V tomto příkladu Předpokládejme, že váš `items` řetězec XML obsahuje také atributy `expired='true'` a `expired='false'` :

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Tento příklad projde ve výrazu XPath, `'//name[@expired]'` a najde všechny `name` prvky, které mají `expired` atribut:

`xpath(xml(parameters('items')), '//name[@expired]')`

Tady je výsledek: `[ Gala, Honeycrisp ]`

*Příklad 5*

V tomto příkladu Předpokládejme, že váš `items` řetězec XML obsahuje pouze tento atribut `expired = 'true'` :

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Tento příklad projde ve výrazu XPath, `'//name[@expired = 'true']'` a k nalezení všech `name` prvků, které mají atribut `expired = 'true'` :

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Tady je výsledek: `[ Gala ]`

*Příklad 6*

V tomto příkladu Předpokládejme, že váš `items` řetězec XML obsahuje také tyto atributy: 

* `expired='true' price='12'`
* `expired='false' price='40'`

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true' price='12'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false' price='40'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Tento příklad projde ve výrazu XPath, `'//name[price>35]'` , aby vyhledal všechny `name` prvky, které mají `price > 35` :

`xpath(xml(parameters('items')), '//name[price>35]')`

Tady je výsledek: `Honeycrisp`

*Příklad 7*

V tomto příkladu Předpokládejme, že váš `items` řetězec XML je stejný jako v příkladu 1:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Tento příklad najde uzly, které odpovídají `<count></count>` uzlu, a přidá tyto hodnoty uzlů pomocí `sum()` funkce:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Tady je výsledek: `30`

*Příklad 8*

V tomto příkladu Předpokládejme, že máte tento řetězec XML, který obsahuje obor názvů dokumentu XML `xmlns="http://contoso.com"` :

```xml
<?xml version="1.0"?><file xmlns="http://contoso.com"><location>Paris</location></file>
```

Tyto výrazy používají buď výraz XPath, `/*[name()="file"]/*[name()="location"]` nebo `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]` k vyhledání uzlů, které odpovídají `<location></location>` uzlu. Tyto příklady znázorňují syntaxi, kterou používáte buď v návrháři aplikace logiky, nebo v editoru výrazů:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Zde je výsledný uzel, který odpovídá `<location></location>` uzlu: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Pokud pracujete v zobrazení kódu, vydejte dvojité uvozovky (") pomocí znaku zpětného lomítka ( \\ ). 
> Například je třeba použít řídicí znaky při serializaci výrazu jako řetězce JSON. 
> Pokud však pracujete v návrháři aplikace logiky nebo editoru výrazů, nemusíte klikat na uvozovky, protože znak zpětného lomítka je automaticky přidán do podkladové definice, například:
> 
> * Zobrazení kódu: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Editor výrazů: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*Příklad 9*

V příkladu 8 Tento příklad používá výraz XPath, `'string(/*[name()="file"]/*[name()="location"])'` k nalezení hodnoty v `<location></location>` uzlu:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Tady je výsledek: `Paris`

## <a name="next-steps"></a>Další kroky

Další informace o [jazyku definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md)

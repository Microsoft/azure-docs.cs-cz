---
title: Výraz a funkce v Azure Data Factory
description: Tento článek obsahuje informace o výrazech a funkcích, které můžete použít při vytváření entit datové továrny.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: c456e486217886510a1fd2ef1aeabae47d35b53c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417191"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Výrazy a funkce ve službě Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-functions-variables.md)
> * [Aktuální verze](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek obsahuje podrobnosti o výrazy a funkce podporované Azure Data Factory. 

## <a name="expressions"></a>Výrazy

JSON hodnoty v definici může být literál nebo výrazy, které jsou vyhodnocovány za běhu. Příklad:  
  
```json
"name": "value"
```

 – nebo –  
  
```json
"name": "@pipeline().parameters.password"
```

Výrazy se mohou objevit kdekoli v řetězcové hodnotě JSON a vždy vyústí v jinou hodnotu JSON. Pokud json hodnota je výraz, tělo výrazu je extrahován odebráním at-sign (\@). Pokud je potřeba literál \@řetězec, který začíná \@ \@, musí být uvozena pomocí . Následující příklady ukazují, jak jsou vyhodnocovány výrazy.  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|"parametry"|Znaky 'parametry' jsou vráceny.|  
|"parametry[1]"|Znaky 'parameters[1]' jsou vráceny.|  
|"\@\@"|Je vrácen řetězec 1\@znaků, který obsahuje ' '.|  
|" \@"|Je vrácen řetězec 2 \@znaků, který obsahuje ' '.|  
  
 Výrazy se mohou také objevit uvnitř řetězců pomocí funkce nazývané *interpolace řetězce,* kde jsou výrazy zabaleny do . `@{ ... }` Příklad: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Pomocí interpolace řetězce je výsledkem vždy řetězec. Řekněme, `myNumber` že `42` `myString` jsem `foo`definoval jako a as:  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Vrátí `foo` jako řetězec.|  
|"\@{pipeline().parameters.myString}"| Vrátí `foo` jako řetězec.|  
|"\@pipeline().parameters.myNumber"| Vrátí `42` jako *číslo*.|  
|"\@{pipeline().parameters.myNumber}"| Vrátí `42` jako *řetězec*.|  
|"Odpověď je: @{pipeline().parameters.myNumber}"| Vrátí řetězec `Answer is: 42`.|  
|"\@concat('Answer je: ', string(pipeline().parameters.myNumber))"| Vrátí řetězec.`Answer is: 42`|  
|"Odpověď je: \@ \@{pipeline().parameters.myNumber}"| Vrátí řetězec `Answer is: @{pipeline().parameters.myNumber}`.|  
  
## <a name="examples"></a>Příklady

### <a name="a-dataset-with-a-parameter"></a>Datová sada s parametrem
V následujícím příkladu blobDataset trvá parametr s názvem **cesta**. Jeho hodnota se používá k nastavení hodnoty **vlastnosti folderPath** pomocí výrazu: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>Potrubí s parametrem
V následujícím příkladu kanálu trvá **inputPath** a **outputPath** parametry. **Cesta** pro parametrizovanou datovou sadu objektů blob je nastavena pomocí hodnot těchto parametrů. Zde použitá syntaxe je: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>Kurz
Tento [kurz](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) vás provede, jak předat parametry mezi kanálu a aktivity, jakož i mezi aktivitami.

  
## <a name="functions"></a>Functions

Můžete volat funkce v rámci výrazů. V následujících částech jsou uvedeny informace o funkcích, které lze použít ve výrazu.  

## <a name="string-functions"></a>Funkce řetězců  

Chcete-li pracovat s řetězci, můžete použít tyto funkce řetězce a také některé [funkce kolekce](#collection-functions).
Řetězcové funkce fungují pouze na řetězcích.

| Funkce řetězce | Úkol |
| --------------- | ---- |
| [Concat](control-flow-expression-language-functions.md#concat) | Zkombinujte dva nebo více řetězců a vraťte kombinovaný řetězec. |
| [Endswith](control-flow-expression-language-functions.md#endswith) | Zkontrolujte, zda řetězec končí zadaným podřetězcem. |
| [Identifikátor guid](control-flow-expression-language-functions.md#guid) | Generovat globálně jedinečný identifikátor (GUID) jako řetězec. |
| [Indexof](control-flow-expression-language-functions.md#indexof) | Vraťte počáteční pozici pro podřetězec. |
| [Lastindexof](control-flow-expression-language-functions.md#lastindexof) | Vrátí počáteční pozici pro poslední výskyt podřetězce. |
| [Nahradit](control-flow-expression-language-functions.md#replace) | Nahraďte podřetězec zadaným řetězcem a vraťte aktualizovaný řetězec. |
| [Rozdělit](control-flow-expression-language-functions.md#split) | Vrátí pole, které obsahuje podřetězce oddělené čárkami, z většího řetězce založeného na zadaném znaku oddělovače v původním řetězci. |
| [Startswith](control-flow-expression-language-functions.md#startswith) | Zkontrolujte, zda řetězec začíná určitým podřetězcem. |
| [Podřetězec](control-flow-expression-language-functions.md#substring) | Vrátí znaky z řetězce, počínaje zadanou pozici. |
| [Tolower](control-flow-expression-language-functions.md#toLower) | Vrátí řetězec ve formátu s malou písmena. |
| [Toupper](control-flow-expression-language-functions.md#toUpper) | Vrátí řetězec ve formátu velkých písmen. |
| [Trim](control-flow-expression-language-functions.md#trim) | Odeberte úvodní a koncové mezery z řetězce a vraťte aktualizovaný řetězec. |

## <a name="collection-functions"></a>Funkce sběru

Chcete-li pracovat s kolekcemi, obecně pole, řetězce a někdy slovníky, můžete použít tyto funkce kolekce.

| Funkce sběru | Úkol |
| ------------------- | ---- |
| [Obsahuje](control-flow-expression-language-functions.md#contains) | Zkontrolujte, zda kolekce má určitou položku. |
| [empty](control-flow-expression-language-functions.md#empty) | Zkontrolujte, zda je kolekce prázdná. |
| [První](control-flow-expression-language-functions.md#first) | Vrátí první položku z kolekce. |
| [Průsečíku](control-flow-expression-language-functions.md#intersection) | Vrátí kolekci, která má *pouze* běžné položky v rámci zadané kolekce. |
| [Připojit](control-flow-expression-language-functions.md#join) | Vrátí řetězec, který má *všechny* položky z pole, oddělené zadaným znakem. |
| [Poslední](control-flow-expression-language-functions.md#last) | Vrátí poslední položku z kolekce. |
| [Délka](control-flow-expression-language-functions.md#length) | Vrátí počet položek v řetězci nebo matici. |
| [Přeskočit](control-flow-expression-language-functions.md#skip) | Odeberte položky z přední části kolekce a vraťte *všechny ostatní* položky. |
| [vzít](control-flow-expression-language-functions.md#take) | Vrátit položky z přední části kolekce. |
| [Unie](control-flow-expression-language-functions.md#union) | Vrátí kolekci, která má *všechny* položky ze zadaných kolekcí. | 

## <a name="logical-functions"></a>Logické funkce  

Tyto funkce jsou užitečné uvnitř podmínky, mohou být použity k vyhodnocení libovolného typu logiky.  
  
| Funkce logického porovnání | Úkol |
| --------------------------- | ---- |
| [A](control-flow-expression-language-functions.md#and) | Zkontrolujte, zda jsou všechny výrazy pravdivé. |
| [equals](control-flow-expression-language-functions.md#equals) | Zkontrolujte, zda jsou obě hodnoty ekvivalentní. |
| [greater](control-flow-expression-language-functions.md#greater) | Zkontrolujte, zda je první hodnota větší než druhá hodnota. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Zkontrolujte, zda je první hodnota větší nebo rovna druhé hodnotě. |
| [if](control-flow-expression-language-functions.md#if) | Zkontrolujte, zda je výraz pravdivý nebo nepravdivý. Na základě výsledku vrátí zadanou hodnotu. |
| [less](control-flow-expression-language-functions.md#less) | Zkontrolujte, zda je první hodnota menší než druhá hodnota. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Zkontrolujte, zda je první hodnota menší nebo rovna druhé hodnotě. |
| [Ne](control-flow-expression-language-functions.md#not) | Zkontrolujte, zda je výraz false. |
| [Nebo](control-flow-expression-language-functions.md#or) | Zkontrolujte, zda je pravdivý alespoň jeden výraz. |
  
## <a name="conversion-functions"></a>Funkce převodu  

 Tyto funkce se používají k převodu mezi jednotlivými nativními typy v jazyce:  
-   řetězec
-   celé číslo
-   float
-   Boolean
-   Pole
-   Slovníky

| Funkce převodu | Úkol |
| ------------------- | ---- |
| [Pole](control-flow-expression-language-functions.md#array) | Vrátí pole z jednoho zadaného vstupu. Více vstupů naleznete v tématu [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Vraťte verzi kódu base64 pro řetězec. |
| [base64ToBinární](control-flow-expression-language-functions.md#base64ToBinary) | Vrátí binární verzi pro řetězec kódovaný base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Vraťte verzi řetězce pro řetězec kódovaný base64. |
| [binární](control-flow-expression-language-functions.md#binary) | Vrátí binární verzi pro vstupní hodnotu. |
| [bool](control-flow-expression-language-functions.md#bool) | Vraťte logickou verzi pro vstupní hodnotu. |
| [Coalesce](control-flow-expression-language-functions.md#coalesce) | Vrátí první hodnotu bez nuly z jednoho nebo více parametrů. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Vrátí pole z více vstupů. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Vrátí identifikátor URI dat pro vstupní hodnotu. |
| [dataUriToBinární](control-flow-expression-language-functions.md#dataUriToBinary) | Vraťte binární verzi identifikátoru URI dat. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Vraťte verzi řetězce pro identifikátor URI dat. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Vraťte verzi řetězce pro řetězec kódovaný base64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Vraťte binární verzi identifikátoru URI dat. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Vrátí řetězec, který nahradí řídicí znaky dekódované verze. |
| [kódUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Vrátí řetězec, který nahradí znaky url nebezpečné řídicími znaky. |
| [float](control-flow-expression-language-functions.md#float) | Vrátí číslo s plovoucí desetinnou tácem pro vstupní hodnotu. |
| [int](control-flow-expression-language-functions.md#int) | Vraťte verzi celého čísla pro řetězec. |
| [Json](control-flow-expression-language-functions.md#json) | Vraťte hodnotu nebo objekt typu Zápis objektu JavaScriptu (JSON) pro řetězec nebo XML. |
| [Řetězec](control-flow-expression-language-functions.md#string) | Vraťte verzi řetězce pro vstupní hodnotu. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Vraťte verzi kódovky URI pro vstupní hodnotu nahrazením znaků nebezpečných adres URL řídicími znaky. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Vraťte binární verzi pro řetězec kódovaný identifikátorem URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Vraťte verzi řetězce pro řetězec kódovaný identifikátorem URI. |
| [Xml](control-flow-expression-language-functions.md#xml) | Vraťte verzi XML pro řetězec. |
| [Xpath](control-flow-expression-language-functions.md#xpath) | Zkontrolujte, zda jazyk XML neodpovídá uzlům nebo hodnotám, které odpovídají výrazu XPath (JAZYK CESTY XML), a vraťte odpovídající uzly nebo hodnoty. |

## <a name="math-functions"></a>Matematické funkce  
 Tyto funkce lze použít pro oba typy čísel: **celá čísla** a **plováky**.  

| Matematické funkce | Úkol |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Vrátit výsledek přidáním dvou čísel. |
| [div](control-flow-expression-language-functions.md#div) | Vrátit výsledek z dělení dvou čísel. |
| [Max](control-flow-expression-language-functions.md#max) | Vrátí nejvyšší hodnotu ze sady čísel nebo pole. |
| [Min](control-flow-expression-language-functions.md#min) | Vrátí nejnižší hodnotu ze sady čísel nebo pole. |
| [Mod](control-flow-expression-language-functions.md#mod) | Vraťte zbytek z dělení dvou čísel. |
| [mul](control-flow-expression-language-functions.md#mul) | Vraťte produkt z násobení dvou čísel. |
| [Rand](control-flow-expression-language-functions.md#rand) | Vrátí náhodné celé číslo ze zadaného rozsahu. |
| [Rozsah](control-flow-expression-language-functions.md#range) | Vrátí celé pole, které začíná od zadaného celého čísla. |
| [Dílčí](control-flow-expression-language-functions.md#sub) | Vrátit výsledek od odečtením druhé číslo od prvního čísla. |
  
## <a name="date-functions"></a>Datové funkce  

| Funkce data nebo času | Úkol |
| --------------------- | ---- |
| [Adddays](control-flow-expression-language-functions.md#addDays) | Přidejte k časovému razítku počet dní. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Přidejte k časovému razítku několik hodin. |
| [Addminutes](control-flow-expression-language-functions.md#addMinutes) | Přidejte k časovému razítku několik minut. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Přidejte k časovému razítku počet sekund. |
| [přidatToTime](control-flow-expression-language-functions.md#addToTime) | Přidejte k časovému razítku počet časových jednotek. Viz také [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [převéstFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Převeďte časové razítko z časové souřadnice (UTC) na cílové časové pásmo. |
| [převéstTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Převeďte časové razítko ze zdrojového časového pásma na cílové časové pásmo. |
| [převéstToUtc](control-flow-expression-language-functions.md#convertToUtc) | Převést časové razítko ze zdrojového časového pásma na časovou souřadnou (UTC). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Vrátí součást den v měsíci z časového razítka. |
| [Dayofweek](control-flow-expression-language-functions.md#dayOfWeek) | Vrátí součást den v týdnu z časového razítka. |
| [Dayofyear](control-flow-expression-language-functions.md#dayOfYear) | Vrátí součást den v roce z časového razítka. |
| [Formatdatetime](control-flow-expression-language-functions.md#formatDateTime) | Vrátit datum z časového razítka. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Vrátí aktuální časové razítko plus zadané časové jednotky. Viz také [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Vrátí aktuální časové razítko mínus zadané časové jednotky. Viz také [odečístFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [začátekDne](control-flow-expression-language-functions.md#startOfDay) | Vrátit začátek dne pro časové razítko. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Vraťte začátek hodiny pro časové razítko. |
| [začátekMěsíce](control-flow-expression-language-functions.md#startOfMonth) | Vrátit začátek měsíce pro časové razítko. |
| [odečístFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Odečtěte počet časových jednotek od časového razítka. Viz také [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [Klíšťata](control-flow-expression-language-functions.md#ticks) | Vrátí `ticks` hodnotu vlastnosti pro zadané časové razítko. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Vrátí aktuální časové razítko jako řetězec. |

## <a name="function-reference"></a>Odkaz na funkci

V této části jsou uvedeny všechny dostupné funkce v abecedním pořadí.

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
| <*Destinationtimezone*> | Ano | Řetězec | Název cílového časového pásma. Názvy časových pásem naleznete v [tématu Microsoft Time Zone Index Values](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale bude pravděpodobně muset odebrat všechny interpunkce z názvu časového pásma. |
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
| <*Sourcetimezone*> | Ano | Řetězec | Název zdrojového časového pásma. Názvy časových pásem naleznete v [tématu Microsoft Time Zone Index Values](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale bude pravděpodobně muset odebrat všechny interpunkce z názvu časového pásma. |
| <*Destinationtimezone*> | Ano | Řetězec | Název cílového časového pásma. Názvy časových pásem naleznete v [tématu Microsoft Time Zone Index Values](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale bude pravděpodobně muset odebrat všechny interpunkce z názvu časového pásma. |
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
| <*Sourcetimezone*> | Ano | Řetězec | Název zdrojového časového pásma. Názvy časových pásem naleznete v [tématu Microsoft Time Zone Index Values](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values), ale bude pravděpodobně muset odebrat všechny interpunkce z názvu časového pásma. |
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

Následující v příkladu 1 tento příklad `<count></count>` vyhledá uzly, které odpovídají `sum()` uzlu a přidá tyto hodnoty uzlů s funkcí:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

A vrátí tento výsledek:`30`

*Příklad 2*

V tomto příkladu oba výrazy najít `<location></location>` uzly, které odpovídají uzlu, v zadané argumenty, které zahrnují XML s oborem názvů. Výrazy používají znak zpětného\\lomítka ( ) jako řídicí znak pro dvojité uvozovky (").

* *Výraz 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Výraz 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Zde jsou argumenty:

* Tento kód XML, který zahrnuje `xmlns="http://contoso.com"`obor názvů dokumentu XML:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Zde výraz XPath:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Zde je výsledek uzlu, `<location></location>` který odpovídá uzlu:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Příklad 3*

Následující na příkladu 3, tento `<location></location>` příklad najde hodnotu v uzlu:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

A vrátí tento výsledek:`"Paris"`

## <a name="next-steps"></a>Další kroky
Seznam systémových proměnných, které lze použít ve výrazech, naleznete v tématu [Systémové proměnné](control-flow-system-variables.md).

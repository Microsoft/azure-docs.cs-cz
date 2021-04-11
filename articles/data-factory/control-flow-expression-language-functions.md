---
title: Výraz a funkce v Azure Data Factory
description: Tento článek poskytuje informace o výrazech a funkcích, které můžete použít při vytváření entit Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: af365ef9b94702fa6634235a95a91297d6b7ae50
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107122"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Výrazy a funkce ve službě Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-functions-variables.md)
> * [Aktuální verze](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek poskytuje podrobné informace o výrazech a funkcích, které podporuje Azure Data Factory. 

## <a name="expressions"></a>Výrazy

Hodnoty JSON v definici můžou být literály nebo výrazy, které se vyhodnocují za běhu. Například:  
  
```json
"name": "value"
```

 nebo  
  
```json
"name": "@pipeline().parameters.password"
```

Výrazy se můžou objevit kdekoli v hodnotě řetězce JSON a vždycky mají za následek jinou hodnotu JSON. Pokud je hodnotou JSON výraz, tělo výrazu se extrahuje odebráním znaku při přihlášení ( \@ ). Pokud je vyžadován řetězcový literál, který začíná \@ , musí být uvozen pomocí \@ \@ . Následující příklady znázorňují, jak jsou výrazy vyhodnocovány.  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|ukazatelů|Vrátí se znaky Parameters.|  
|"Parameters [1]"|Vrátí se znaky Parameters [1].|  
|"\@\@"|Vrátí se 1 řetězec znaků, který obsahuje znak \@ .|  
|" \@"|Je vrácen 2 řetězec znaků, který obsahuje znak ' \@ '.|  
  
 Výrazy lze také zobrazit uvnitř řetězců pomocí funkce s názvem *interpolace řetězce* , kde jsou výrazy zabaleny `@{ ... }` . Příklad: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Při použití řetězcové interpolace je výsledkem vždy řetězec. Řekněme, že jsem definoval (a) jako `myNumber` `42`  `myString`  `foo` :  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|" \@ Pipeline (). Parameters. MyString"| Vrátí hodnotu `foo` jako řetězec.|  
|" \@ {Pipeline (). Parameters. MyString}"| Vrátí hodnotu `foo` jako řetězec.|  
|" \@ Pipeline (). Parameters. MyNumber"| Vrátí hodnotu `42` jako *číslo*.|  
|" \@ {Pipeline (). Parameters. MyNumber}"| Vrátí hodnotu `42` jako *řetězec*.|  
|"Odpověď je: @ {Pipeline (). Parameters. myNumber}"| Vrátí řetězec `Answer is: 42` .|  
|" \@ Concat (' Answer: '; String (Pipeline (). Parameters. MyNumber))"| Vrátí řetězec `Answer is: 42`|  
|"Odpověď je: \@ \@ {Pipeline (). Parameters. MyNumber}"| Vrátí řetězec `Answer is: @{pipeline().parameters.myNumber}` .|  

## <a name="examples"></a>Příklady

### <a name="complex-expression-example"></a>Příklad komplexního výrazu
Následující příklad ukazuje komplexní příklad, který odkazuje na hloubkové dílčí pole výstupu aktivity. Chcete-li odkazovat na parametr kanálu, který je vyhodnocen jako dílčí pole, použijte syntaxi [] namísto operátoru tečka (.) (jako v případě subpole1 a subfield2)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Editor dynamického obsahu

Editor dynamického obsahu automaticky řídí znaky v obsahu při dokončení úprav. Například následující obsah v Editoru obsahu je interpolace řetězce se dvěma funkcemi výrazu. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

Editor dynamického obsahu převádí obsah nad rámec na výraz `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` . Výsledek tohoto výrazu je řetězec formátu JSON, který se zobrazil níže.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with-a-parameter"></a>Datová sada s parametrem
V následujícím příkladu převezme BlobDataset parametr s názvem **path**. Jeho hodnota se používá k nastavení hodnoty vlastnosti **FolderPath** pomocí výrazu: `dataset().path` . 

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

### <a name="a-pipeline-with-a-parameter"></a>Kanál s parametrem
V následujícím příkladu kanál přijímá parametry **inputPath** a **outputPath** . **Cesta** pro parametrizovanou datovou sadu objektů BLOB je nastavena pomocí hodnot těchto parametrů. Zde použitá syntaxe je: `pipeline().parameters.parametername` . 

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

### <a name="replacing-special-characters"></a>Nahrazení speciálních znaků

Editor dynamického obsahu automaticky řídí znaky, jako jsou dvojité uvozovky, zpětné lomítko v obsahu při dokončení úprav. To způsobuje problémy, pokud chcete nahradit posun řádku nebo tabulátory pomocí **znaku \n**, **\t** ve funkci Replace (). Můžete upravit dynamický obsah v zobrazení kódu pro odebrání nadbytečného výrazu ve výrazu, nebo můžete postupovat podle následujících kroků pro nahrazení speciálních znaků pomocí jazyka výrazů:

1. Kódování adresy URL proti původní hodnotě řetězce
1. Nahraďte řetězec kódovaný v adrese URL, například posun řádku (% 0A), znak návratu na začátek řádku (% 0D), horizontální kartu (%09).
1. Dekódování adresy URL

Například proměnná *CompanyName* s znakem nového řádku ve své hodnotě, výraz `@uriComponentToString(replace(uriComponent(variables('companyName')), '%0A', ''))` může odebrat znak nového řádku. 

```json
Contoso-
Corporation
```

### <a name="escaping-single-quote-character"></a>Řídicí znak jednoduché uvozovky

Funkce výrazu používají jednoduché uvozovky pro parametry řetězcové hodnoty. Pomocí dvou jednoduchých uvozovek můžete v řetězcových funkcích řídicího znaku. Například výraz `@concat('Baba', ''' ', 'book store')` bude vrácen pod výsledek.

```
Baba's book store
```

### <a name="tutorial"></a>Kurz
Tento [kurz](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) vás provede procesem předávání parametrů mezi kanálem a aktivitou a mezi aktivitami.

  
## <a name="functions"></a>Functions

Můžete volat funkce v rámci výrazů. Následující části obsahují informace o funkcích, které lze použít ve výrazu.  

## <a name="string-functions"></a>Funkce řetězců  

Chcete-li pracovat s řetězci, můžete použít tyto řetězcové funkce a také některé [funkce kolekce](#collection-functions).
Funkce řetězce fungují pouze v řetězcích.

| Funkce String | Úkol |
| --------------- | ---- |
| [spojuje](control-flow-expression-language-functions.md#concat) | Kombinací dvou nebo více řetězců a vrácení kombinovaného řetězce. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Zkontroluje, zda řetězec končí zadaným podřetězcem. |
| [guid](control-flow-expression-language-functions.md#guid) | Vygeneruje globálně jedinečný identifikátor (GUID) jako řetězec. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Vrátí počáteční pozici podřetězce. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Vrátí počáteční pozici pro poslední výskyt podřetězce. |
| [náhrady](control-flow-expression-language-functions.md#replace) | Nahraďte podřetězec zadaným řetězcem a vraťte aktualizovaný řetězec. |
| [rozdělení](control-flow-expression-language-functions.md#split) | Vrátí pole, které obsahuje podřetězce oddělené čárkami, z většího řetězce na základě zadaného oddělovače v původním řetězci. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Kontroluje, zda řetězec začíná konkrétním podřetězcem. |
| [podřetězec](control-flow-expression-language-functions.md#substring) | Vrátí znaky z řetězce počínaje od zadané pozice. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Vrátí řetězec ve formátu s malými písmeny. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Vrátí řetězec ve formátu velkými písmeny. |
| [sklon](control-flow-expression-language-functions.md#trim) | Odstraňte úvodní a koncové mezery z řetězce a vraťte aktualizovaný řetězec. |

## <a name="collection-functions"></a>Funkce kolekcí

Chcete-li pracovat s kolekcemi, všeobecně se jedná o pole, řetězce a někdy slovníky, můžete použít tyto funkce kolekce.

| Funkce kolekce | Úkol |
| ------------------- | ---- |
| [zobrazí](control-flow-expression-language-functions.md#contains) | Kontroluje, zda kolekce obsahuje konkrétní položku. |
| [empty](control-flow-expression-language-functions.md#empty) | Zkontroluje, jestli je kolekce prázdná. |
| [první](control-flow-expression-language-functions.md#first) | Vrátí první položku z kolekce. |
| [průnik](control-flow-expression-language-functions.md#intersection) | Vrátí kolekci, která má v zadaných kolekcích *jenom* společné položky. |
| [zúčastnit](control-flow-expression-language-functions.md#join) | Vrátí řetězec, který obsahuje *všechny* položky z pole oddělené zadaným znakem. |
| [posledního](control-flow-expression-language-functions.md#last) | Vrátí poslední položku z kolekce. |
| [length](control-flow-expression-language-functions.md#length) | Vrátí počet položek v řetězci nebo poli. |
| [přímo](control-flow-expression-language-functions.md#skip) | Odebere položky z přední části kolekce a vrátí *všechny ostatní* položky. |
| [nezbytná](control-flow-expression-language-functions.md#take) | Vrátí položky od začátku kolekce. |
| [sjednocovací](control-flow-expression-language-functions.md#union) | Vrátí kolekci, která obsahuje *všechny* položky ze zadaných kolekcí. | 

## <a name="logical-functions"></a>Logické funkce  

Tyto funkce jsou užitečné v rámci podmínek, které je možné použít k vyhodnocení jakéhokoli typu logiky.  
  
| Funkce logického porovnání | Úkol |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Ověřte, zda jsou všechny výrazy pravdivé. |
| [rovná](control-flow-expression-language-functions.md#equals) | Zkontroluje, jestli jsou obě hodnoty ekvivalentní. |
| [greater](control-flow-expression-language-functions.md#greater) | Ověřte, zda je první hodnota větší než druhá hodnota. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Ověřte, zda je první hodnota větší než nebo rovna druhé hodnotě. |
| [Přestože](control-flow-expression-language-functions.md#if) | Zkontroluje, jestli je výraz pravdivý, nebo nepravdivý. Na základě výsledku vrátí zadanou hodnotu. |
| [tolik](control-flow-expression-language-functions.md#less) | Ověřte, zda je první hodnota menší než druhá hodnota. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Ověřte, zda je první hodnota menší nebo rovna druhé hodnotě. |
| [mění](control-flow-expression-language-functions.md#not) | Zkontroluje, jestli je výraz nepravdivý. |
| [nebo](control-flow-expression-language-functions.md#or) | Ověřte, zda je alespoň jeden výraz pravdivý. |
  
## <a name="conversion-functions"></a>Převodní funkce  

 Tyto funkce slouží k převodu mezi jednotlivými nativními typy v jazyku:  
-   řetězec
-   integer
-   float
-   boolean
-   řadách
-   slovníky

| Převodní funkce | Úkol |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Vrátí pole z jednoho zadaného vstupu. Více vstupů naleznete v tématu [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Vrátí verzi kódovanou pro řetězec ve formátu base64. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Vrátí binární verzi řetězce zakódovaného ve formátu base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Vrátí verzi řetězce pro řetězec kódovaný v kódování Base64. |
| [tvaru](control-flow-expression-language-functions.md#binary) | Vrátí binární verzi pro vstupní hodnotu. |
| [bool](control-flow-expression-language-functions.md#bool) | Vrátí logickou verzi pro vstupní hodnotu. |
| [COALESCE](control-flow-expression-language-functions.md#coalesce) | Vrátí první hodnotu, která není null, z jednoho nebo více parametrů. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Vrátí pole z více vstupů. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Vrátí identifikátor URI dat pro vstupní hodnotu. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Vrátí binární verzi pro identifikátor URI dat. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Vrátí verzi řetězce pro identifikátor URI dat. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Vrátí verzi řetězce pro řetězec kódovaný v kódování Base64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Vrátí binární verzi pro identifikátor URI dat. |
| [decodeURIComponent –](control-flow-expression-language-functions.md#decodeUriComponent) | Vrátí řetězec, který nahradí řídicí znaky pomocí dekódování verzí. |
| [encodeURIComponent –](control-flow-expression-language-functions.md#encodeUriComponent) | Vrátí řetězec, který nahradí URL – nebezpečné znaky pomocí řídicích znaků. |
| [float](control-flow-expression-language-functions.md#float) | Vrátí číslo s plovoucí desetinnou čárkou pro vstupní hodnotu. |
| [int](control-flow-expression-language-functions.md#int) | Vrátí celočíselnou verzi řetězce. |
| [JSON](control-flow-expression-language-functions.md#json) | Vrátí hodnotu nebo objekt typu JavaScript Object Notation (JSON) pro řetězec nebo XML. |
| [řetězec](control-flow-expression-language-functions.md#string) | Vrátí verzi řetězce pro vstupní hodnotu. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Vrátí verzi kódovanou identifikátorem URI pro vstupní hodnotu nahrazením znaků, které nejsou bezpečné, pomocí řídicích znaků. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Vrátí binární verzi řetězce zakódovaného identifikátorem URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Vrátí verzi řetězce pro řetězec kódovaný pomocí identifikátoru URI. |
| [XML](control-flow-expression-language-functions.md#xml) | Vrátí verzi XML pro řetězec. |
| [cestou](control-flow-expression-language-functions.md#xpath) | Zkontroluje XML pro uzly nebo hodnoty, které odpovídají výrazu XPath (XML Path Language), a vrátí odpovídající uzly nebo hodnoty. |

## <a name="math-functions"></a>Matematické funkce  
 Tyto funkce lze použít pro oba typy čísel: **celá** čísla a **Floaty**.  

| Funkce Math | Úkol |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Vrátí výsledek z přidání dvou čísel. |
| [div](control-flow-expression-language-functions.md#div) | Vrátí výsledek z dělení dvou čísel. |
| [počet](control-flow-expression-language-functions.md#max) | Vrátí nejvyšší hodnotu ze sady čísel nebo pole. |
| [min](control-flow-expression-language-functions.md#min) | Vrátí nejnižší hodnotu ze sady čísel nebo pole. |
| [střední](control-flow-expression-language-functions.md#mod) | Vrátí zbytek po dělení dvou čísel. |
| [mul](control-flow-expression-language-functions.md#mul) | Vrátí produkt pro vynásobení dvou čísel. |
| [funkcí](control-flow-expression-language-functions.md#rand) | Vrátí náhodné celé číslo ze zadaného rozsahu. |
| [oblasti](control-flow-expression-language-functions.md#range) | Vrátí celočíselné pole, které začíná zadaným celým číslem. |
| [jednotk](control-flow-expression-language-functions.md#sub) | Vrátí výsledek odečtením druhého čísla od prvního čísla. |
  
## <a name="date-functions"></a>Datové funkce  

| Funkce data a času | Úkol |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Přidání počtu dní do časového razítka. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Přidejte do časového razítka několik hodin. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Přidejte do časového razítka počet minut. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Přidejte do časového razítka počet sekund. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Přidání počtu časových jednotek k časovému razítku. Viz také [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Převeďte časové razítko ze koordinovaného světového času (UTC) na cílové časové pásmo. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Převede časové razítko ze zdrojového časového pásma na cílové časové pásmo. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Převede časové razítko ze zdrojového časového pásma na koordinovaný světový čas (UTC). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Vrátí den v měsíci součásti z časového razítka. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Vrátí den v týdnu komponenty z časového razítka. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Vrátí den komponenty v roce z časového razítka. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Vrátí časové razítko jako řetězec v volitelném formátu. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Vrátí aktuální časové razítko plus zadané časové jednotky. Viz také [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Vrátí aktuální časové razítko minus zadané časové jednotky. Viz také [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Vrátí začátek dne pro časové razítko. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Vrátí začátek hodiny pro časové razítko. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Vrátí začátek měsíce pro časové razítko. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Odečte počet časových jednotek od časového razítka. Viz také [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [Ticks](control-flow-expression-language-functions.md#ticks) | Vrátí `ticks` hodnotu vlastnosti pro zadané časové razítko. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Vrátí aktuální časové razítko jako řetězec. |

## <a name="function-reference"></a>Odkaz na funkci

V této části jsou uvedeny všechny dostupné funkce v abecedním pořadí.

<a name="add"></a>

### <a name="add"></a>add

Vrátí výsledek z přidání dvou čísel.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*summand_1*> <*summand_2*> | Yes | Integer, float nebo mixed | Čísla, která mají být přidána |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Přidejte do časového razítka počet sekund.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*doba*> | Yes | Integer | Počet zadaných časových jednotek, které se mají přidat |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, která se má použít v *intervalu*: "druhé", "Minute", "hodina", "den", "týden", "měsíc", "rok" |
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

Ověřte, zda jsou oba výrazy pravdivé.
Vrátí hodnotu true, pokud mají oba výrazy hodnotu true, nebo vrátí hodnotu false, pokud je alespoň jeden výraz nepravdivý.

```
and(<expression1>, <expression2>)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*výraz1*>, <*Výraz2*> | Yes | Logická hodnota | Výrazy, které mají být zkontrolovány |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | -----| ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud jsou oba výrazy pravdivé. Vrátí hodnotu false, pokud je alespoň jeden výraz nepravdivý. |
||||

*Příklad 1*

Tyto příklady kontrolují, zda jsou zadané logické hodnoty pravdivé:

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

Tyto příklady kontrolují, jestli jsou splněné konkrétní výrazy:

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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec pro vytvoření pole |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [<*value*>] | Pole | Pole, které obsahuje jeden zadaný vstup |
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

```
base64('<value>')
```

| Parametr | Povinné | Typ | Description |
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

```
base64ToBinary('<value>')
```

| Parametr | Povinné | Typ | Description |
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

Vrátí verzi řetězce pro řetězec kódovaný v kódování Base64 a efektivně dekódování řetězce base64.
Použijte tuto funkci místo [decodeBase64 ()](#decodeBase64).
I když obě funkce fungují stejným způsobem, `base64ToString()` jsou upřednostňovány.

```
base64ToString('<value>')
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

<a name="bool"></a>

### <a name="bool"></a>bool

Vrátí logickou verzi pro hodnotu.

```
bool(<value>)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Yes | Všechny | Hodnota, která se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Logická verze zadané hodnoty |
||||

*Příklad*

Tyto příklady převádějí zadané hodnoty na logické hodnoty:

```
bool(1)
bool(0)
```

A vrátí tyto výsledky:

* První příklad: `true`
* Druhý příklad: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>COALESCE

Vrátí první hodnotu, která není null, z jednoho nebo více parametrů.
Prázdné řetězce, prázdné pole a prázdné objekty nejsou null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*object_1*> <*object_2*>... | Yes | Libovolný, můžou kombinovat typy. | Jedna nebo více položek pro kontrolu hodnoty null |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*první položka, která není null,*> | Všechny | První položka nebo hodnota, která není null. Pokud jsou všechny parametry null, tato funkce vrátí hodnotu null. |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*Text2*>,... | Ano | Řetězec | Aspoň dva řetězce, které se mají zkombinovat |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

A vrátí tento výsledek: `"2018-01-01T00:00:00Z"`

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

| Parametr | Povinné | Typ | Description |
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
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

A vrátí tento výsledek: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Převede časové razítko ze zdrojového časového pásma na koordinovaný světový čas (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*objekt1*> <*objekt2*>... | Yes | Libovolná, ale ne smíšená | Aspoň dvě položky pro vytvoření pole |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [<*objekt1*>, <*objekt2*>,...] | Pole | Pole vytvořené ze všech vstupních položek |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

A vrátí tento výsledek: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Vrátí den v roce z časového razítka.

```
dayOfYear('<timestamp>')
```

| Parametr | Povinné | Typ | Description |
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

### <a name="decodebase64"></a>decodeBase64

Vrátí verzi řetězce pro řetězec kódovaný v kódování Base64 a efektivně dekódování řetězce base64.
Zvažte použití [base64ToString ()](#base64ToString) spíše než `decodeBase64()` .
I když obě funkce fungují stejným způsobem, `base64ToString()` jsou upřednostňovány.

```
decodeBase64('<value>')
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Ano | Řetězec | Řetězec kódovaný v kódování Base64 k dekódování |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*dekódované – řetězec base64*> | Řetězec | Verze řetězce pro řetězec kódovaný v kódování Base64 |
||||

*Příklad*

Tento příklad vytvoří řetězec pro řetězec kódovaný v kódování Base64:

```
decodeBase64('aGVsbG8=')
```

A vrátí tento výsledek: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Vrátí binární verzi pro datový identifikátor URI (Uniform Resource Identifier).
Zvažte použití [dataUriToBinary ()](#dataUriToBinary)místo `decodeDataUri()` .
I když obě funkce fungují stejným způsobem, `dataUriToBinary()` jsou upřednostňovány.

```
decodeDataUri('<value>')
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

Vrátí celočíselný výsledek z vydělení dvou čísel.
Chcete-li získat zbývající výsledek, viz [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*podíl*> | Yes | Integer nebo float | Číslo, které se má rozdělit *dělitelem* |
| <*dělitel*> | Yes | Integer nebo float | Číslo, které vydělí *dividendy*, ale nemůže být 0 |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*podíl – výsledek*> | Integer | Celočíselný výsledek vydělení prvního čísla druhým číslem |
||||

*Příklad*

Oba příklady vydělí první číslo druhým číslem:

```
div(10, 5)
div(11, 5)
```

A vrátí tento výsledek: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeURIComponent –

Nahrazením znaků, které nejsou v adrese URL, pomocí řídicích znaků vrátíte verzi kódovanou podle identifikátoru URI (Uniform Resource Identifier) pro řetězec.
Zvažte použití [uriComponent ()](#uriComponent)místo `encodeUriComponent()` .
I když obě funkce fungují stejným způsobem, `uriComponent()` jsou upřednostňovány.

```
encodeUriComponent('<value>')
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*objekt1*>, <*objekt2*> | Yes | Některé | Hodnoty, výrazy nebo objekty, které se mají porovnat |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Řetězec nebo pole | Kolekce, ve které se má najít první položka |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*první kolekce – položka*> | Všechny | První položka v kolekci |
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

```
float('<value>')
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Vrátí aktuální časové razítko plus zadané časové jednotky.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*doba*> | Yes | Integer | Počet zadaných časových jednotek, které se mají přidat |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, která se má použít v *intervalu*: "druhé", "Minute", "hodina", "den", "týden", "měsíc", "rok" |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*doba*> | Yes | Integer | Počet zadaných časových jednotek k odečtení |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, která se má použít v *intervalu*: "druhé", "Minute", "hodina", "den", "týden", "měsíc", "rok" |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*formátovat*> | No | Řetězec | Jeden [specifikátor formátu](/dotnet/api/system.guid.tostring) pro vrácený identifikátor GUID. Ve výchozím nastavení je formát "D", ale můžete použít "N", "D", "B", "P" nebo "X". |
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

Zkontroluje, jestli je výraz pravdivý, nebo nepravdivý.
Na základě výsledku vrátí zadanou hodnotu.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*vyjádření*> | Yes | Logická hodnota | Výraz, který se má kontrolovat |
| <*valueIfTrue*> | Yes | Všechny | Hodnota, která se má vrátit, když je výraz pravdivý |
| <*valueIfFalse*> | Yes | Všechny | Hodnota, která se má vrátit, pokud je výraz nepravdivý |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*zadané-návratové hodnoty*> | Všechny | Zadaná hodnota, která se vrátí na základě toho, zda je výraz true nebo false |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

<a name="json"></a>

### <a name="json"></a>json

Vrátí hodnotu nebo objekt typu JavaScript Object Notation (JSON) pro řetězec nebo XML.

```
json('<value>')
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*> <*Collection2*>... | Yes | Pole nebo objekt, ale ne obojí | Kolekce, ze kterých mají být *pouze* běžné položky |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Pole | Pole, ve kterém se mají položky připojit |
| <*oddělovač*> | Ano | Řetězec | Oddělovač, který se zobrazí mezi jednotlivými znaky ve výsledném řetězci |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*CHAR1* >< *oddělovač* >< *CHAR2* >< *oddělovač*>... | Řetězec | Výsledný řetězec vytvořený ze všech položek v zadaném poli |
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

| Parametr | Povinné | Typ | Description |
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

Vrátí počáteční pozici nebo hodnotu indexu pro poslední výskyt podřetězce.
Tato funkce nerozlišuje velká a malá písmena a indexy začínají číslem 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který obsahuje dílčí řetězec, který se má najít |
| <*Prohledávanýtext*> | Ano | Řetězec | Dílčí řetězec, který se má najít |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*konec-index-hodnota*> | Integer | Počáteční pozice nebo hodnota indexu pro poslední výskyt zadaného podřetězce. <p>Pokud řetězec nebyl nalezen, vrátí číslo-1. |
||||

*Příklad*

Tento příklad najde počáteční hodnotu indexu pro poslední výskyt podřetězce "World" v řetězci "Hello World":

```
lastIndexOf('hello world', 'world')
```

A vrátí tento výsledek: `6`

<a name="length"></a>

### <a name="length"></a>length

Vrátí počet položek v kolekci.

```
length('<collection>')
length([<collection>])
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

<a name="max"></a>

### <a name="max"></a>max

Vrátí nejvyšší hodnotu ze seznamu nebo pole s čísly, která jsou zahrnuta na obou koncích.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*číslo1*>, <*číslo2*>,... | Yes | Integer, float nebo Both | Sada čísel, ze kterých má být nejvyšší hodnota |
| [<*číslo1*>, <*číslo2*>,...] | Yes | Array – celé číslo, float nebo obojí | Pole čísel, ze kterých má být nejvyšší hodnota |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*číslo1*>, <*číslo2*>,... | Yes | Integer, float nebo Both | Sada čísel, ze kterých má být nejnižší hodnota |
| [<*číslo1*>, <*číslo2*>,...] | Yes | Array – celé číslo, float nebo obojí | Pole čísel, ze kterých má být nejnižší hodnota |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*podíl*> | Yes | Integer nebo float | Číslo, které se má rozdělit *dělitelem* |
| <*dělitel*> | Yes | Integer nebo float | Číslo, které vydělí *dividendy*, ale nemůže být 0. |
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

| Parametr | Povinné | Typ | Description |
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

<a name="not"></a>

### <a name="not"></a>not

Zkontroluje, jestli je výraz nepravdivý.
Vrátí hodnotu true, pokud má výraz hodnotu false, nebo vrátí hodnotu false, pokud má hodnotu true.

```json
not(<expression>)
```

| Parametr | Povinné | Typ | Description |
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
Vrátí hodnotu true, pokud má alespoň jeden výraz hodnotu true, nebo vrátí hodnotu false, pokud jsou obě hodnoty false.

```
or(<expression1>, <expression2>)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*výraz1*>, <*Výraz2*> | Yes | Logická hodnota | Výrazy, které mají být zkontrolovány |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| true nebo false | Logická hodnota | Vrátí hodnotu true, pokud má alespoň jeden výraz hodnotu true. Vrátí hodnotu false, pokud jsou oba výrazy nepravdivé. |
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

<a name="rand"></a>

### <a name="rand"></a>rand

Vrátí náhodné celé číslo ze zadaného rozsahu, který je zahrnut pouze na počátečním konci.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*Indexu*> | Yes | Integer | Celočíselná hodnota, která spouští pole jako první položku |
| <*výpočtu*> | Yes | Integer | Počet celých čísel v poli |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [<*Rozsah-> výsledků* ] | Pole | Pole s celými čísly počínaje zadaným indexem |
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

| Parametr | Povinné | Typ | Description |
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

<a name="skip"></a>

### <a name="skip"></a>Přeskočit

Odebere položky z přední části kolekce a vrátí *všechny ostatní* položky.

```
skip([<collection>], <count>)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Pole | Kolekce, jejíž položky chcete odebrat |
| <*výpočtu*> | Yes | Integer | Kladné celé číslo pro počet položek, které se mají odebrat na začátku |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [<*aktualizováno> kolekce* ] | Pole | Aktualizovaná kolekce po odebrání zadaných položek |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, který se má oddělit k podřetězcům na základě zadaného oddělovače v původním řetězci |
| <*oddělovač*> | Ano | Řetězec | Znak v původním řetězci, který má být použit jako oddělovač |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| [<*subřetězec1*>, <*subřetězec2*>,...] | Pole | Pole, které obsahuje podřetězce z původního řetězce oddělené čárkami |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*formátovat*> | No | Řetězec | Buď [jeden specifikátor formátu](/dotnet/standard/base-types/standard-date-and-time-format-strings) , nebo [vlastní vzorek formátu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Výchozí formát pro časové razítko je ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (RRRR-MM-DDThh: mm: SS: fffffffK), který vyhovuje [normě ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) a uchovává informace o časovém pásmu. |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*Aktualizováno – časové razítko*> | Řetězec | Zadané časové razítko, ale počínaje prvním dnem v měsíci s označením nulové hodiny |
||||

*Příklad*

Tento příklad vrátí začátek měsíce pro toto časové razítko:

```
startOfMonth('2018-03-15T13:30:30Z')
```

A vrátí tento výsledek: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Kontroluje, zda řetězec začíná konkrétním podřetězcem.
Vrátí hodnotu true, pokud se podřetězec najde, nebo vrátí hodnotu false, pokud nebyla nalezena.
Tato funkce nerozlišuje velká a malá písmena.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*osa*> | Yes | Všechny | Hodnota, která se má převést |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*hodnota řetězce*> | Řetězec | Verze řetězce pro zadanou hodnotu |
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

| Parametr | Povinné | Typ | Description |
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

Vrátí znaky z řetězce počínaje zadaným umístěním nebo indexem.
Hodnoty indexu začínají číslem 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*textové*> | Ano | Řetězec | Řetězec, jehož znaky chcete |
| <*Indexu*> | Yes | Integer | Kladné číslo větší nebo rovno 0, které chcete použít jako počáteční pozici nebo hodnotu indexu. |
| <*časový*> | Yes | Integer | Kladný počet znaků, které chcete v podřetězci. |
|||||

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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*časové razítko*> | Ano | Řetězec | Řetězec, který obsahuje časové razítko |
| <*doba*> | Yes | Integer | Počet zadaných časových jednotek k odečtení |
| <*timeUnit*> | Ano | Řetězec | Jednotka času, která se má použít v *intervalu*: "druhé", "Minute", "hodina", "den", "týden", "měsíc", "rok" |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*kolekce*> | Yes | Řetězec nebo pole | Kolekce, jejíž položky chcete |
| <*výpočtu*> | Yes | Integer | Celé kladné číslo pro počet položek, které požadujete od začátku |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*podmnožina*> nebo [ *podmnožina* <>] | Řetězec nebo pole, v uvedeném pořadí | Řetězec nebo pole, které má zadaný počet položek od začátku původní kolekce |
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

Vrátí `ticks` hodnotu vlastnosti pro zadané časové razítko.
*Takt* je interval 100 – nanosekund.

```
ticks('<timestamp>')
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

<a name="trim"></a>

### <a name="trim"></a>sklon

Odstraňte úvodní a koncové mezery z řetězce a vraťte aktualizovaný řetězec.

```
trim('<text>')
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*> <*Collection2*>...  | Yes | Pole nebo objekt, ale ne obojí | Kolekce, ze kterých chcete *všechny* položky |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
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

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Vrátí aktuální časové razítko.

```
utcNow('<format>')
```

Volitelně můžete zadat jiný formát s parametrem <*formát*>.

| Parametr | Povinné | Typ | Description |
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

<a name="xml"></a>

### <a name="xml"></a>xml

Vrátí verzi XML pro řetězec, který obsahuje objekt JSON.

```
xml('<value>')
```

| Parametr | Povinné | Typ | Description |
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

| Parametr | Povinné | Typ | Description |
| --------- | -------- | ---- | ----------- |
| <*XML*> | Yes | Všechny | Řetězec XML pro vyhledání uzlů nebo hodnot, které odpovídají hodnotě výrazu XPath |
| <*cestou*> | Yes | Všechny | Výraz XPath, který slouží k vyhledání odpovídajícího uzlu XML nebo hodnot |
|||||

| Vrácená hodnota | Typ | Description |
| ------------ | ---- | ----------- |
| <*uzel XML*> | XML | Uzel XML, pokud pouze jeden uzel odpovídá zadanému výrazu XPath |
| <*osa*> | Všechny | Hodnota z uzlu XML, pokud je pouze jedna hodnota shodná se zadaným výrazem XPath |
| [<*XML-uzel1*>, <*xml-Uzel2*>,...] </br>-nebo- </br>[<*hodnota1*>, <*hodnota2*>,...] | Pole | Pole s uzly XML nebo hodnotami, které odpovídají zadanému výrazu XPath |
||||

*Příklad 1*

V příkladu 1 Tento příklad najde uzly, které odpovídají uzlu, `<count></count>` a přidá tyto hodnoty uzlů s `sum()` funkcí:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

A vrátí tento výsledek: `30`

*Příklad 2*

V tomto příkladu oba výrazy vyhledají uzly, které odpovídají `<location></location>` uzlu, v zadaných argumentech, které obsahují XML s oborem názvů. Výrazy používají znak zpětného lomítka ( \\ ) jako řídicí znak pro dvojité uvozovky (").

* *Výraz 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Výraz 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Tady jsou argumenty:

* Tento kód XML, který zahrnuje obor názvů dokumentu XML, `xmlns="http://contoso.com"` :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Buď výraz XPath tady:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Zde je výsledný uzel, který odpovídá `<location></location>` uzlu:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Příklad 3*

Následující příklad v příkladu 3 najde hodnotu v `<location></location>` uzlu:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

A vrátí tento výsledek: `"Paris"`

## <a name="next-steps"></a>Další kroky
Seznam systémových proměnných, které můžete použít ve výrazech, najdete v tématu [systémové proměnné](control-flow-system-variables.md).

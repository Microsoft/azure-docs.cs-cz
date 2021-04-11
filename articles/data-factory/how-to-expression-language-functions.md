---
title: Použití parametrů a výrazů v Azure Data Factory
description: V tomto článku najdete informace o výrazech a funkcích, které můžete použít při vytváření entit Data Factory.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: c9e1abc5fb6f66981f56bc262319587d9fc4265e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566655"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Jak používat parametry, výrazy a funkce v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-functions-variables.md)
> * [Aktuální verze](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto dokumentu se primárně zaměřujeme na základní koncepty učení s různými příklady, které vám pomohou při vytváření parametrizovaných datových kanálů v rámci Azure Data Factory. Parametrizace a dynamické výrazy jsou takové významné přídavky na ADF, protože můžou ušetřit obrovské množství času a umožňují mnohem flexibilnější extrakci, transformaci, načítání (ETL) nebo extrakci, načítání, transformaci (ELT), což výrazně sníží náklady na údržbu řešení a urychlí implementaci nových funkcí do stávajících kanálů. Tyto zisky jsou způsobeny tím, že Parametrizace minimalizuje množství pevného kódu a zvyšuje počet opakovaně použitelných objektů a procesů v řešení.

## <a name="azure-data-factory-ui-and-parameters"></a>Uživatelské rozhraní a parametry Azure Data Factory

Pokud v uživatelském rozhraní ADF začínáte s využitím parametrů Azure Data Factory, přečtěte si prosím [uživatelské rozhraní Data Factory pro propojené služby s parametry](./parameterize-linked-services.md#data-factory-ui)  a [uživatelským rozhraním Data Factory pro kanál řízený metadaty s parametry](./how-to-use-trigger-parameterization.md#data-factory-ui) pro vizuální vysvětlení.

## <a name="parameter-and-expression-concepts"></a>Koncepce parametrů a výrazů 

Pomocí parametrů můžete předat externí hodnoty do kanálů, datových sad, propojených služeb a toků dat. Po předání parametru do prostředku ho nelze změnit. Pomocí prostředků parametrizace je můžete kdykoli znovu použít s různými hodnotami. Parametry lze použít jednotlivě nebo jako součást výrazů. Hodnoty JSON v definici můžou být literály nebo výrazy, které se vyhodnocují za běhu.

Například:  
  
```json
"name": "value"
```

 nebo  
  
```json
"name": "@pipeline().parameters.password"
```

Výrazy se můžou objevit kdekoli v hodnotě řetězce JSON a vždycky mají za následek jinou hodnotu JSON. Tady je *heslo* parametr kanálu ve výrazu. Pokud je hodnotou JSON výraz, tělo výrazu se extrahuje odebráním znaku při přihlášení ( \@ ). Pokud je vyžadován řetězcový literál, který začíná \@ , musí být uvozen pomocí \@ \@ . Následující příklady znázorňují, jak jsou výrazy vyhodnocovány.  
  
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

## <a name="examples-of-using-parameters-in-expressions"></a>Příklady použití parametrů ve výrazech 

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

### <a name="a-dataset-with--parameters"></a>Datová sada s parametry

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

### <a name="a-pipeline-with--parameters"></a>Kanál s parametry

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

  
## <a name="calling-functions-within-expressions"></a>Volání funkcí v rámci výrazů 

Můžete volat funkce v rámci výrazů. Následující části obsahují informace o funkcích, které lze použít ve výrazu.  

### <a name="string-functions"></a>Funkce řetězců  

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

### <a name="collection-functions"></a>Funkce kolekcí

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

### <a name="logical-functions"></a>Logické funkce  

Tyto funkce jsou užitečné v rámci podmínek, které je možné použít k vyhodnocení jakéhokoli typu logiky.  
  
| Funkce logického porovnání | Úkol |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Ověřte, zda jsou všechny výrazy pravdivé. |
| [rovná](control-flow-expression-language-functions.md#equals) | Zkontroluje, jestli jsou obě hodnoty ekvivalentní. |
| [greater](control-flow-expression-language-functions.md#greater) | Ověřte, zda je první hodnota větší než druhá hodnota. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Ověřte, zda je první hodnota větší než nebo rovna druhé hodnotě. |
| [if](control-flow-expression-language-functions.md#if) | Zkontroluje, jestli je výraz pravdivý, nebo nepravdivý. Na základě výsledku vrátí zadanou hodnotu. |
| [tolik](control-flow-expression-language-functions.md#less) | Ověřte, zda je první hodnota menší než druhá hodnota. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Ověřte, zda je první hodnota menší nebo rovna druhé hodnotě. |
| [mění](control-flow-expression-language-functions.md#not) | Zkontroluje, jestli je výraz nepravdivý. |
| [nebo](control-flow-expression-language-functions.md#or) | Ověřte, zda je alespoň jeden výraz pravdivý. |
  
### <a name="conversion-functions"></a>Převodní funkce  

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

### <a name="math-functions"></a>Matematické funkce  
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
  
### <a name="date-functions"></a>Datové funkce  

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

## <a name="detailed-examples-for-practice"></a>Podrobné příklady pro postupy

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Podrobný kanál kopírování služby Azure Data Factory s parametry 

Tento [parametr kanálu kopírování kanálu služby Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) vás provede procesem předávání parametrů mezi kanálem a aktivitou a také mezi aktivitami.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Podrobný kanál mapování toku dat s parametry 

Sledujte prosím [tok dat mapování s parametry](./parameters-data-flow.md) pro komplexní příklad používání parametrů v toku dat.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Podrobné kanály založené na metadatech s parametry

Pokud chcete získat další informace o použití parametrů pro návrh kanálů řízených metadaty, postupujte prosím podle [kanálu založeného na metadatech s parametry](./how-to-use-trigger-parameterization.md) . Toto je oblíbený případ použití pro parametry.


## <a name="next-steps"></a>Další kroky
Seznam systémových proměnných, které můžete použít ve výrazech, najdete v tématu [systémové proměnné](control-flow-system-variables.md).
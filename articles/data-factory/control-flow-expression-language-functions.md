---
title: Výraz a funkce v Azure Data Factory | Microsoft Docs
description: Tento článek poskytuje informace o výrazech a funkcích, které můžete použít při vytváření entit Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9fc8dd1e22e16ef5342b405b602f8baaf8ce7edf
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692858"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Výrazy a funkce ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-functions-variables.md)
> * [Aktuální verze](control-flow-expression-language-functions.md)

Tento článek poskytuje podrobné informace o výrazech a funkcích, které podporuje Azure Data Factory. 

## <a name="introduction"></a>Úvod
Hodnoty JSON v definici můžou být literály nebo výrazy, které se vyhodnocují za běhu. Příklad:  
  
```json
"name": "value"
```

 ani  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Výrazy  
Výrazy se můžou objevit kdekoli v hodnotě řetězce JSON a vždycky mají za následek jinou hodnotu JSON. Pokud je hodnotou JSON výraz, tělo výrazu se extrahuje odebráním znaku při přihlášení (\@). Pokud je vyžadován řetězcový literál, který začíná na \@, musí být uvozen pomocí \@\@. Následující příklady znázorňují, jak jsou výrazy vyhodnocovány.  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|ukazatelů|Vrátí se znaky Parameters.|  
|"Parameters [1]"|Vrátí se znaky Parameters [1].|  
|"\@\@"|Vrátí se 1 řetězec znaků obsahující '\@'.|  
|"\@"|Vrátí se 2 řetězec znaků obsahující ' \@'.|  
  
 Výrazy lze také zobrazit uvnitř řetězců pomocí funkce s názvem *interpolace řetězce* , kde jsou výrazy zabaleny do `@{ ... }`. Příklad: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Při použití řetězcové interpolace je výsledkem vždy řetězec. Řekněme, že jsem definoval (a) `myNumber` jako `42` a `myString` jako `foo`:  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|"\@Pipeline (). Parameters. myString"| Vrátí `foo` jako řetězec.|  
|"\@{Pipeline (). Parameters. myString}"| Vrátí `foo` jako řetězec.|  
|"\@Pipeline (). Parameters. myNumber"| Vrátí `42` jako *číslo*.|  
|"\@{Pipeline (). Parameters. myNumber}"| Vrátí `42` jako *řetězec*.|  
|"Odpověď je: @ {Pipeline (). Parameters. myNumber}"| Vrátí řetězec `Answer is: 42`.|  
|"\@Concat (' odpověď je: ', řetězec (kanál (). Parameters. myNumber))"| Vrátí řetězec `Answer is: 42`|  
|"Odpověď: \@\@{Pipeline (). Parameters. myNumber}"| Vrátí řetězec `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Příklady

#### <a name="a-dataset-with-a-parameter"></a>Datová sada s parametrem
V následujícím příkladu převezme BlobDataset parametr s názvem **path**. Jeho hodnota se používá k nastavení hodnoty vlastnosti **FolderPath** pomocí výrazu: `dataset().path`. 

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

#### <a name="a-pipeline-with-a-parameter"></a>Kanál s parametrem
V následujícím příkladu kanál přijímá parametry **inputPath** a **outputPath** . **Cesta** pro parametrizovanou datovou sadu objektů BLOB je nastavena pomocí hodnot těchto parametrů. Zde použitá syntaxe je: `pipeline().parameters.parametername`. 

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
#### <a name="tutorial"></a>Kurz
Tento [kurz](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) vás provede procesem předávání parametrů mezi kanálem a aktivitou a mezi aktivitami.

  
## <a name="functions"></a>Functions  
 Můžete volat funkce v rámci výrazů. Následující části obsahují informace o funkcích, které lze použít ve výrazu.  

## <a name="string-functions"></a>Funkce řetězců  
 Následující funkce platí pouze pro řetězce. Můžete také použít několik funkcí kolekce na řetězce.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|spojuje|Kombinuje libovolný počet řetězců dohromady. Pokud například `foo,` parametr1, vrátí následující výraz `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Číslo parametru**: 1... *n*<br /><br /> **Název**: řetězec *n*<br /><br /> **Popis**: povinné. Řetězce, které se mají zkombinovat do jednoho řetězce.|  
|podřetězec|Vrátí podmnožinu znaků z řetězce. Například následující výraz:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> Vrátí<br /><br /> `foo`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, ze kterého je dílčí řetězec proveden.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: počáteční index<br /><br /> **Popis**: povinné. Index, kde dílčí řetězec začíná v parametru 1. Počáteční index je založený na nule. <br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: délka<br /><br /> **Popis**: povinné. Délka podřetězce|  
|náhrady|Nahradí řetězec daným řetězcem. Například výraz:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> Vrátí<br /><br /> `the new string`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné.  Pokud je v parametru 1 nalezen parametr 2, řetězec, který je vyhledán parametr 2 a aktualizován pomocí parametru 3.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: starý řetězec<br /><br /> **Popis**: povinné. Řetězec, který má být nahrazen parametrem 3, pokud je nalezena shoda v parametru 1<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: nový řetězec<br /><br /> **Popis**: povinné. Řetězec, který se používá k nahrazení řetězce v parametru 2, pokud je nalezena shoda v parametru 1.|  
|Hlavních| Generuje globálně jedinečný řetězec (neboli. identifikátor GUID). Například následující výstup může být vygenerován `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Jeden specifikátor formátu, který určuje, jak se má [Formátovat hodnota tohoto identifikátoru GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Parametr format může být "N", "D", "B", "P" nebo "X". Pokud není zadán formát, bude použit znak "D".|  
|toLower|Převede řetězec na malá písmena. Například následující příkaz vrátí `two by two is four`: `toLower('Two by Two is Four')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, který má být převeden na malá písmena. Pokud znak v řetězci nemá malý ekvivalent, je zahrnut ve vráceném řetězci beze změny.|  
|toUpper|Převede řetězec na velká písmena. Například následující výraz vrátí `TWO BY TWO IS FOUR`: `toUpper('Two by Two is Four')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, který má být převeden na velká písmena. Pokud znak v řetězci nemá velká písmena, je ve vráceném řetězci zahrnut beze změny.|  
|IndexOf|Najde index hodnoty v rámci řetězcového řetězce nezávisle na velikosti. Index je založený na nule. Například následující výraz vrátí `7`: `indexof('hello, world.', 'world')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, který může obsahovat hodnotu.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Hodnota, kterou chcete vyhledat v indexu.|  
|LastIndexOf|Najde poslední index hodnoty v řetězci řetězce, který nerozlišuje. Index je založený na nule. Například následující výraz vrátí `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, který může obsahovat hodnotu.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Hodnota, kterou chcete vyhledat v indexu.|  
|StartsWith|Kontroluje, zda řetězec začíná s hodnotou Case bez rozlišení. Například následující výraz vrátí `true`: `startswith('hello, world', 'hello')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, který může obsahovat hodnotu.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Hodnota, se kterou může řetězec začínat.|  
|EndsWith|Kontroluje, zda řetězec končí nezávisle na hodnotě Case. Například následující výraz vrátí `true`: `endswith('hello, world', 'world')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, který může obsahovat hodnotu.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Hodnota, na kterou řetězec může končit.|  
|split|Rozdělí řetězec pomocí oddělovače. Například následující výraz vrátí `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, který je rozdělen.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Oddělovač.|  
  
  
## <a name="collection-functions"></a>Funkce kolekce  
 Tyto funkce pracují s kolekcemi, jako jsou pole, řetězce a někdy slovníky.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|Zobrazí|Vrátí hodnotu true, pokud Dictionary obsahuje klíč, seznam obsahuje hodnotu nebo řetězec obsahuje podřetězec. Například následující výraz vrací `true:``contains('abacaba','aca')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: v kolekci<br /><br /> **Popis**: povinné. Kolekce, ve které se má hledat<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: najít objekt<br /><br /> **Popis**: povinné. Objekt, který se má najít v **rámci kolekce**.|  
|časový|Vrátí počet prvků v poli nebo řetězci. Například následující výraz vrátí `3`: `length('abc')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: povinné. Kolekce, pro kterou se má získat délka|  
|Obsahovat|Vrátí hodnotu true, pokud je objekt, pole nebo řetězec prázdný. Například následující výraz vrátí `true`:<br /><br /> `empty('')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: povinné. Kolekce pro kontrolu, zda je prázdná.|  
|Průnik|Vrátí jedno pole nebo objekt se společnými prvky mezi poli nebo objekty, které jsou předány. Tato funkce například vrací `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Parametry funkce mohou být buď sadou objektů, nebo sadou polí (nikoli jejich směsí). Pokud existují dva objekty se stejným názvem, bude poslední objekt s tímto názvem zobrazen v konečném objektu.<br /><br /> **Číslo parametru**: 1... *n*<br /><br /> **Název**: kolekce *n*<br /><br /> **Popis**: povinné. Kolekce, které se mají vyhodnotit Objekt musí být ve všech kolekcích, které jsou předány k zobrazení ve výsledku.|  
|sjednocovací|Vrátí jedno pole nebo objekt se všemi elementy, které jsou předány do něj buď pole, nebo objekt. Tato funkce například vrací `[1, 2, 3, 10, 101]:`<br /><br /> : `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Parametry funkce mohou být buď sadou objektů, nebo sadou polí (nikoli jejich směsí). Pokud jsou v konečném výstupu dva objekty se stejným názvem, bude poslední objekt s tímto názvem zobrazen v konečném objektu.<br /><br /> **Číslo parametru**: 1... *n*<br /><br /> **Název**: kolekce *n*<br /><br /> **Popis**: povinné. Kolekce, které se mají vyhodnotit Ve výsledku se zobrazí objekt, který se zobrazí ve všech kolekcích.|  
|První|Vrátí první prvek v předaném poli nebo řetězci. Tato funkce například vrací `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: povinné. Kolekce, ze které se má převzít první objekt|  
|Posledního|Vrátí poslední prvek v předaném poli nebo řetězci. Tato funkce například vrací `3`:<br /><br /> `last('0123')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: povinné. Kolekce, ze které se má převzít poslední objekt|  
|Nezbytná|Vrátí první **počet** prvků z předaného pole nebo řetězce, například tato funkce vrací `[1, 2]`: `take([1, 2, 3, 4], 2)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: povinné. Kolekce, ze které se mají převzít první objekty **počtu** .<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: počet<br /><br /> **Popis**: povinné. Počet objektů, které mají být z **kolekce**přebírat. Musí být kladné celé číslo.|  
|přímo|Vrátí prvky v poli začínající v **počtu**indexů, například tato funkce vrací `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: povinné. Kolekce, ze které se mají přeskočit objekty prvního **počtu** .<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: počet<br /><br /> **Popis**: povinné. Počet objektů, které mají být odebrány z před **kolekcí**. Musí být kladné celé číslo.|  
  
## <a name="logical-functions"></a>Logické funkce  
 Tyto funkce jsou užitečné v rámci podmínek, které je možné použít k vyhodnocení jakéhokoli typu logiky.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|Rovná|Vrátí hodnotu true, pokud jsou dvě hodnoty stejné. Pokud například parametr1 je foo, následující výraz vrátí `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: povinné. Objekt, který se má porovnat s **objektem 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: povinné. Objekt, který se má porovnat s **objektem 1**.|  
|tolik|Vrátí hodnotu true, pokud je první argument menší než druhý. Všimněte si, že hodnoty mohou být pouze typu Integer, float nebo String. Například následující výraz vrátí `true`: `less(10,100)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: povinné. Objekt, který zkontroluje, zda je menší než **objekt 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: povinné. Objekt, který zkontroluje, zda je větší než **objekt 1**.|  
|lessOrEquals|Vrátí hodnotu true, pokud je první argument menší nebo roven druhému. Všimněte si, že hodnoty mohou být pouze typu Integer, float nebo String. Například následující výraz vrátí `true`: `lessOrEquals(10,10)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: povinné. Objekt, který zkontroluje, zda je menší nebo roven **objektu 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: povinné. Objekt, který zkontroluje, zda je větší nebo roven **objektu 1**.|  
|zvýšen|Vrátí hodnotu true, pokud je první argument větší než druhý. Všimněte si, že hodnoty mohou být pouze typu Integer, float nebo String. Například následující výraz vrátí `false`: `greater(10,10)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: povinné. Objekt, který zkontroluje, zda je větší než **objekt 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: povinné. Objekt, který zkontroluje, zda je menší než **objekt 1**.|  
|greaterOrEquals|Vrátí hodnotu true, pokud je první argument větší než nebo roven druhému. Všimněte si, že hodnoty mohou být pouze typu Integer, float nebo String. Například následující výraz vrátí `false`: `greaterOrEquals(10,100)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: povinné. Objekt, který zkontroluje, zda je větší nebo roven **objektu 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: povinné. Objekt, který zkontroluje, zda je menší nebo roven **objektu 1**.|  
|a|Vrátí hodnotu true, pokud jsou oba parametry true. Oba argumenty musí být logické hodnoty. Následující vrátí `false`: `and(greater(1,10),equals(0,0))`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: logická hodnota 1<br /><br /> **Popis**: povinné. První argument, který musí být `true`.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: logická hodnota 2<br /><br /> **Popis**: povinné. Druhý argument musí být `true`.|  
|nebo|Vrátí hodnotu true, pokud má jeden z parametrů hodnotu true. Oba argumenty musí být logické hodnoty. Následující vrátí `true`: `or(greater(1,10),equals(0,0))`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: logická hodnota 1<br /><br /> **Popis**: povinné. První argument, který může být `true`.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: logická hodnota 2<br /><br /> **Popis**: povinné. Druhý argument může být `true`.|  
|Mění|Vrátí hodnotu true, pokud je parametr `false`. Oba argumenty musí být logické hodnoty. Následující vrátí `true`: `not(contains('200 Success','Fail'))`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: logická hodnota<br /><br /> **Description**: vrátí hodnotu true, pokud je parametr `false`. Oba argumenty musí být logické hodnoty. Následující vrátí `true`: `not(contains('200 Success','Fail'))`|  
|Přestože|Vrátí zadanou hodnotu na základě toho, zda je výsledkem výrazu `true` nebo `false`.  Například následující příkaz vrátí `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Name**: – výraz<br /><br /> **Popis**: povinné. Logická hodnota, která určuje, která hodnota je vrácena výrazem.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: true<br /><br /> **Popis**: povinné. Hodnota, která se má vrátit, pokud je výraz `true`.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: false<br /><br /> **Popis**: povinné. Hodnota, která se má vrátit, pokud je výraz `false`.|  
  
## <a name="conversion-functions"></a>Převodní funkce  
 Tyto funkce slouží k převodu mezi jednotlivými nativními typy v jazyku:  
  
-   řetězec  
  
-   celé číslo  
  
-   float  
  
-   Boolean  
  
-   řadách  
  
-   slovníky  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|int|Převeďte parametr na celé číslo. Například následující výraz vrátí 100 jako číslo, nikoli řetězec: `int('100')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: povinné. Hodnota, která je převedena na celé číslo.|  
|řetězec|Převeďte parametr na řetězec. Například následující výraz vrací `'10'`: `string(10)` můžete také převést objekt na řetězec, například pokud je parametr **foo** objektem s jednou vlastností `bar : baz`, pak následující by vrátilo `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: povinné. Hodnota, která je převedena na řetězec.|  
|JSON|Převést parametr na hodnotu typu JSON. Jedná se o opak řetězce (). Například následující výraz vrátí `[1,2,3]` jako pole, nikoli řetězec:<br /><br /> `json('[1,2,3]')`<br /><br /> Podobně můžete převést řetězec na objekt. `json('{"bar" : "baz"}')` například vrátí:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, který je převeden na hodnotu nativního typu.<br /><br /> Funkce JSON podporuje také vstup XML. Například hodnota parametru:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> je převedena na následující kód JSON:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Převeďte argument parametru na číslo s plovoucí desetinnou čárkou. Například následující výraz vrátí `10.333`: `float('10.333')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: povinné. Hodnota, která je převedena na číslo s plovoucí desetinnou čárkou.|  
|bool|Převeďte parametr na logickou hodnotu. Například následující výraz vrátí `false`: `bool(0)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: povinné. Hodnota, která je převedena na logickou hodnotu.|  
|COALESCE|Vrátí první objekt, který není null, v předaných argumentech. Poznámka: prázdný řetězec není null. Například pokud parametry 1 a 2 nejsou definovány, vrátí `fallback`: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Číslo parametru**: 1... *n*<br /><br /> **Název**: objekt*n*<br /><br /> **Popis**: povinné. Objekty pro kontrolu `null`.|  
|Base|Vrátí reprezentaci se vstupním řetězcem ve formátu base64. Například následující výraz vrátí `c29tZSBzdHJpbmc=`: `base64('some string')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec 1<br /><br /> **Popis**: povinné. Řetězec, který se má kódovat do reprezentace base64.|  
|base64ToBinary|Vrátí binární reprezentaci řetězce kódovaného ve formátu base64. Například následující výraz vrátí binární reprezentaci některého řetězce: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec kódovaný v kódování Base64.|  
|base64ToString|Vrátí řetězcovou reprezentaci based64 kódovaného řetězce. Například následující výraz vrátí řetězec: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec kódovaný v kódování Base64.|  
|Tvaru|Vrátí binární reprezentaci hodnoty.  Například následující výraz vrátí binární reprezentaci některého řetězce: `binary(‘some string’).`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: povinné. Hodnota, která je převedena do binárního souboru.|  
|dataUriToBinary|Vrátí binární reprezentaci identifikátoru URI dat. Například následující výraz vrátí binární reprezentaci některého řetězce: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Identifikátor URI dat, který má být převeden na binární reprezentaci.|  
|dataUriToString|Vrátí řetězcovou reprezentaci identifikátoru URI dat. Například následující výraz vrátí nějaký řetězec: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br />**Popis**: povinné. Identifikátor URI dat pro převod na řetězcovou reprezentaci.|  
|dataUri|Vrátí identifikátor URI dat hodnoty. Například následující výraz vrátí data: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Číslo parametru**: 1<br /><br />**Název**: hodnota<br /><br />**Popis**: povinné. Hodnota, která má být převedena na identifikátor URI dat.|  
|decodeBase64|Vrátí řetězcovou reprezentaci vstupního based64 řetězce. Například následující výraz vrátí `some string`: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vrátí řetězcovou reprezentaci vstupního based64 řetězce.|  
|encodeURIComponent –|Adresa URL – řídí řetězec, který se předává. Například následující výraz vrátí `You+Are%3ACool%2FAwesome`: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, ze kterého mají být uvozeny adresy URL – nebezpečné znaky.|  
|decodeURIComponent –|Un-URL – řídí řetězec, který se předává. Například následující výraz vrátí `You Are:Cool/Awesome`: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. Řetězec, ze kterého se mají dekódovat nezabezpečené znaky URL|  
|decodeDataUri|Vrátí binární reprezentaci řetězce identifikátoru URI vstupních dat. Například následující výraz vrátí binární reprezentaci `some string`: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: povinné. DataURI, který se má dekódovat do binární reprezentace.|  
|uriComponent|Vrátí reprezentaci hodnoty kódované identifikátorem URI. Například následující výraz vrací `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Podrobnosti parametru: číslo: 1, název: řetězec, popis: povinné. Řetězec, který má být kódovaný identifikátor URI.|  
|uriComponentToBinary|Vrátí binární reprezentaci řetězce kódovaného identifikátorem URI. Například následující výraz vrátí binární reprezentaci `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br />**Popis**: povinné. Řetězec kódovaný v identifikátoru URI.|  
|uriComponentToString|Vrátí řetězcovou reprezentaci řetězce kódovaného identifikátorem URI. Například následující výraz vrátí `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Číslo parametru**: 1<br /><br />**Název**: řetězec<br /><br />**Popis**: povinné. Řetězec kódovaný v identifikátoru URI.|  
|xml|Vrátí reprezentaci hodnoty v jazyce XML. Například následující výraz vrátí obsah XML reprezentovaný `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. Funkce XML podporuje také vstup objektu JSON. Například parametr `{ "abc": "xyz" }` je převeden na obsah XML `\<abc>xyz\</abc>`<br /><br /> **Číslo parametru**: 1<br /><br />**Název**: hodnota<br /><br />**Popis**: povinné. Hodnota, která má být převedena do formátu XML.|  
|cestou|Vrátí pole uzlů XML, které odpovídají výrazu XPath hodnoty, na kterou se výraz XPath vyhodnocuje.<br /><br />  **Příklad 1**<br /><br /> Předpokládejme, že hodnota parametru P1 je řetězcová reprezentace následujícího kódu XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Tento kód: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> Vrátí<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> tomu<br /><br /> 2. Tento kód: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> Vrátí<br /><br /> `13`<br /><br /> <br /><br /> **Příklad 2**<br /><br /> S ohledem na následující obsah XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1. Tento kód: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> nebo<br /><br /> 2. Tento kód: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Vrátí<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> a<br /><br /> 3. Tento kód: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Vrátí<br /><br /> ``bar``<br /><br /> **Číslo parametru**: 1<br /><br />**Název**: XML<br /><br />**Popis**: povinné. Soubor XML, na kterém se má vyhodnotit výraz XPath<br /><br /> **Číslo parametru**: 2<br /><br />**Název**: XPath<br /><br />**Popis**: povinné. Výraz XPath, který se má vyhodnotit|  
|pole|Převeďte parametr na pole.  Například následující výraz vrátí `["abc"]`: `array('abc')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: povinné. Hodnota, která je převedena na pole.|
|createArray|Vytvoří pole z parametrů.  Například následující výraz vrátí `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Číslo parametru**: 1... n<br /><br /> **Název**: Any n<br /><br /> **Popis**: povinné. Hodnoty, které mají být kombinovány do pole.|

## <a name="math-functions"></a>Matematické funkce  
 Tyto funkce lze použít pro oba typy čísel: **celá** čísla a **Floaty**.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|add|Vrátí výsledek sčítání dvou čísel. Tato funkce například vrací `20.333`: `add(10,10.333)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: summand 1<br /><br /> **Popis**: povinné. Číslo, které se má přidat do **summand 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: summand 2<br /><br /> **Popis**: povinné. Číslo, které se má přidat do **summand 1**|  
|jednotk|Vrátí výsledek odčítání dvou čísel. Tato funkce například vrátí: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: minuend<br /><br /> **Popis**: povinné. Číslo, ze kterého se **subtrahend** odebere<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: subtrahend<br /><br /> **Popis**: povinné. Číslo, které se má z **minuend**odebrat.|  
|mul|Vrátí výsledek násobení dvou čísel. Například následující příkaz vrátí `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Multiplicand 1<br /><br /> **Popis**: povinné. Číslo, kterým se má vynásobit **Multiplicand 2** .<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Multiplicand 2<br /><br /> **Popis**: povinné. Číslo, kterým se má vynásobit **Multiplicand 1** .|  
|značek|Vrátí výsledek dělení dvou čísel. Například následující příkaz vrátí `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: dividendy<br /><br /> **Popis**: povinné. Číslo, které se má rozdělit **dělitelem**<br /><br /> **Číslo parametru**: 2<br /><br /> **Name**: dělitel<br /><br /> **Popis**: povinné. Číslo, na které se má dělit **dělenec**|  
|střední|Vrátí výsledek zbytku po dělení dvou čísel (modulo). Například následující výraz vrátí `2`:<br /><br /> `mod(10,4)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: dividendy<br /><br /> **Popis**: povinné. Číslo, které se má rozdělit **dělitelem**<br /><br /> **Číslo parametru**: 2<br /><br /> **Name**: dělitel<br /><br /> **Popis**: povinné. Číslo, na které se má dělit **dělenec** Po dělení se zbytek povede.|  
|min|Existují dva různé vzory pro volání této funkce: `min([0,1,2])` sem má pole min. Tento výraz vrací `0`. Alternativně může tato funkce použít seznam hodnot oddělených čárkami: `min(0,1,2)` Tato funkce také vrátí hodnotu 0. Všimněte si, že všechny hodnoty musí být čísla, takže pokud je parametr pole, musí mít pouze čísla.<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: kolekce nebo hodnota<br /><br /> **Popis**: povinné. Může se jednat o pole hodnot, aby bylo možné najít minimální hodnotu nebo první hodnotu sady.<br /><br /> **Číslo parametru**: 2... *n*<br /><br /> **Název**: hodnota *n*<br /><br /> **Popis**: volitelné. Je-li prvním parametrem hodnota, můžete předat další hodnoty a je vráceno minimum všech předaných hodnot.|  
|Počet|Existují dva různé vzory pro volání této funkce: `max([0,1,2])`<br /><br /> Toto maximum získá pole. Tento výraz vrací `2`. Alternativně může tato funkce použít seznam hodnot oddělených čárkami: `max(0,1,2)` Tato funkce vrátí 2. Všimněte si, že všechny hodnoty musí být čísla, takže pokud je parametr pole, musí mít pouze čísla.<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: kolekce nebo hodnota<br /><br /> **Popis**: povinné. Může se jednat o pole hodnot, aby bylo možné najít maximální hodnotu, nebo první hodnotu sady.<br /><br /> **Číslo parametru**: 2... *n*<br /><br /> **Název**: hodnota *n*<br /><br /> **Popis**: volitelné. Pokud je první parametr hodnota, můžete předat další hodnoty a vrátí se maximum všech předaných hodnot.|  
|oblasti| Generuje pole celých čísel počínaje určitým číslem a definujete délku vráceného pole. Tato funkce například vrací `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: počáteční index<br /><br /> **Popis**: povinné. Je to první celé číslo v poli.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: počet<br /><br /> **Popis**: povinné. Počet celých čísel, která jsou v poli.|  
|funkcí| Vygeneruje náhodné celé číslo v zadaném rozsahu (včetně na obou koncích. To může například vracet `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: minimum<br /><br /> **Popis**: povinné. Nejnižší celé číslo, které by mohlo být vráceno.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: maximum<br /><br /> **Popis**: povinné. Nejvyšší celé číslo, které by mohlo být vráceno.|  
  
## <a name="date-functions"></a>Funkce data  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|utcnow|Vrátí aktuální časové razítko jako řetězec. Například `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) , nebo [vlastní vzorek formátu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , který určuje, jak se má naformátovat hodnota tohoto časového razítka. Pokud není zadán formát, je použit formát ISO 8601 ("o").|  
|addseconds|Přidá celočíselný počet sekund do předaného řetězcového časového razítka. Počet sekund může být kladný nebo záporný. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud není zadán specifikátor formátu. Například `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: povinné. Řetězec, který obsahuje čas.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: sekundy<br /><br /> **Popis**: povinné. Počet sekund, které se mají přidat Může být negativní na odečtení sekund.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) , nebo [vlastní vzorek formátu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , který určuje, jak se má naformátovat hodnota tohoto časového razítka. Pokud není zadán formát, je použit formát ISO 8601 ("o").|  
|addminutes|Přidá celočíselný počet minut do předaného řetězcového časového razítka. Počet minut může být kladný nebo záporný. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud není zadán specifikátor formátu. Například `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: povinné. Řetězec, který obsahuje čas.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: minuty<br /><br /> **Popis**: povinné. Počet minut, po který se má přidat. Může být negativní na odečtení minut.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) , nebo [vlastní vzorek formátu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , který určuje, jak se má naformátovat hodnota tohoto časového razítka. Pokud není zadán formát, je použit formát ISO 8601 ("o").|  
|addhours|Přidá celočíselný počet hodin do předaného řetězcového časového razítka. Počet hodin může být kladný nebo záporný. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud není zadán specifikátor formátu. Například `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: povinné. Řetězec, který obsahuje čas.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: hodiny<br /><br /> **Popis**: povinné. Počet hodin, které se mají přidat. Může být negativní na odečtení hodin.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) , nebo [vlastní vzorek formátu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , který určuje, jak se má naformátovat hodnota tohoto časového razítka. Pokud není zadán formát, je použit formát ISO 8601 ("o").|  
|adddays|Přidá celočíselný počet dní do předaného řetězcového časového razítka. Počet dní může být kladný nebo záporný. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud není zadán specifikátor formátu. Například `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: povinné. Řetězec, který obsahuje čas.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: dny<br /><br /> **Popis**: povinné. Počet dní, po které se má přidat Může být negativní na odečtení dní.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) , nebo [vlastní vzorek formátu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , který určuje, jak se má naformátovat hodnota tohoto časového razítka. Pokud není zadán formát, je použit formát ISO 8601 ("o").|  
|formatDateTime|Vrátí řetězec ve formátu data. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud není zadán specifikátor formátu. Například `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br />Pokud chcete naformátovat datum v ' RRRR/MM/DD ', použijte formatDateTime (UtcNow (), ' RRRR/MM/DD ').</br>Chcete-li k datu připojit název, použijte @concat(' foo-', '/', formatDateTime (UtcNow (), ' yyyy/MM/DD ')).<br><br> **Číslo parametru**: 1<br /><br /> **Název**: datum<br /><br /> **Popis**: povinné. Řetězec, který obsahuje datum.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) , nebo [vlastní vzorek formátu](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , který určuje, jak se má naformátovat hodnota tohoto časového razítka. Pokud není zadán formát, je použit formát ISO 8601 ("o"). |  

## <a name="next-steps"></a>Další kroky
Seznam systémových proměnných, které můžete použít ve výrazech, najdete v tématu [systémové proměnné](control-flow-system-variables.md).

---
title: Výraz a funkcí ve službě Azure Data Factory | Dokumentace Microsoftu
description: Tento článek obsahuje informace o výrazy a funkce, které můžete použít při vytváření entit datové továrny.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 8a2a080ee87d48d25d7d793ca0aca463f25e52eb
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021675"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Výrazy a funkce v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-functions-variables.md)
> * [Aktuální verze](control-flow-expression-language-functions.md)

Tento článek obsahuje podrobnosti o výrazů a funkcí podporovaných službou Azure Data Factory. 

## <a name="introduction"></a>Úvod
Hodnoty JSON v definici mohou být literál nebo výrazy, které se vyhodnotí za běhu. Příklad:  
  
```json
"name": "value"
```

 (nebo)  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Výrazy  
Výrazy může vyskytovat kdekoli v řetězcové hodnotě JSON a vždy mít za následek další hodnoty JSON. Pokud je hodnota JSON výrazu, hlavní část výrazu je extrahován odebráním znaku @ (\@). Potřeby řetězcový literál, který začíná \@, musejí být uvozeny pomocí \@ \@. Následující příklady ukazují, jak jsou výrazy vyhodnocovány.  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|"parametrů"|Vrátí se znaky "parametrů".|  
|"parametry [1]"|Vrátí se znaky "parametry [1]".|  
|"\@\@"|1 znak řetězec, který obsahuje '\@"je vrácena.|  
|" \@"|2 znakový řetězec, který obsahuje ' \@"je vrácena.|  
  
 Výrazy se může zobrazit i uvnitř řetězce, pomocí funkce se nazývá *interpolace* kde výrazy jsou zabaleny v `@{ ... }`. Příklad: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Pomocí interpolace řetězců, výsledek je vždy řetězec. Řekněme, že jsem definoval `myNumber` jako `42` a `myString` jako `foo`:  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|"\@kanálu.parameters.myString ()"| Vrátí `foo` jako řetězec.|  
|"\@{kanálu ().parameters.myString}"| Vrátí `foo` jako řetězec.|  
|"\@kanálu.parameters.myNumber ()"| Vrátí `42` jako *číslo*.|  
|"\@{kanálu ().parameters.myNumber}"| Vrátí `42` jako *řetězec*.|  
|"Odpověď je: @{kanálu ().parameters.myNumber}"| Vrátí řetězec `Answer is: 42`.|  
|"\@concat (" odpověď je: ", string(pipeline().parameters.myNumber))"| Vrátí řetězec `Answer is: 42`|  
|"Odpověď je: \@ \@{kanálu ().parameters.myNumber}"| Vrátí řetězec `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Příklady

#### <a name="a-dataset-with-a-parameter"></a>Datové sady s parametrem
V následujícím příkladu BlobDataset přebírá parametr s názvem **cesta**. Jeho hodnota se používá k nastavení hodnoty **folderPath** vlastnost s použitím těchto výrazů: `@{dataset().path}`. 

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
V následujícím příkladu tento kanál dostává **inputPath** a **outputPath** parametry. **Cesta** parametrizované objektu BLOB je nastavení datové sady s použitím hodnoty těchto parametrů. Tady použít syntaxe je: `pipeline().parameters.parametername`. 

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
  
## <a name="functions"></a>Functions  
 Bude možné volat funkce ve výrazech. Následující části obsahují informace o funkcích, které můžete použít ve výrazu.  

## <a name="string-functions"></a>Funkce řetězců  
 Následující funkce platí jenom pro řetězce. Můžete také řadu funkcí kolekce na řetězce.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|concat|Spojuje dohromady libovolný počet řetězců. Například, pokud má parametr1 hodnotu `foo,` vrátí následující výraz `somevalue-foo-somevalue`:  `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Číslo parametru**: 1... *n*<br /><br /> **Název**: Řetězec *n*<br /><br /> **Popis**: Povinná hodnota. Řetězce zkombinovat do jednoho řetězce.|  
|dílčí řetězec|Vrátí podmnožinu znaků z řetězce. Například následující výraz:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> Vrátí:<br /><br /> `foo`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, ze kterého má získat podřetězec.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Počáteční index<br /><br /> **Popis**: Povinná hodnota. Index, kde začíná podřetězec v parametru 1.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: Délka<br /><br /> **Popis**: Povinná hodnota. Délka podřetězce.|  
|nahradit|Nahradí řetězec daným řetězcem. Například výraz:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> Vrátí:<br /><br /> `the new string`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: Povinná hodnota.  Pokud se parametr 2 najde v parametru 1, řetězec, který se hledá parametr 2 a aktualizuje na parametr 3.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Starý řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který má nahradit parametrem 3, když se najde shoda v parametru 1<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: Nový řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který se používá k nahrazení řetězce v parametru 2, když se najde shoda v parametru 1.|  
|identifikátor GUID| Vygeneruje globálně jedinečný řetězec (neboli. identifikátor GUID). Například následující výstup nejde generovat `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Formát<br /><br /> **Popis**: Volitelné. Jeden specifikátor formátu, který označuje [jak formátovat hodnotu tohoto Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Formát parametru může být "N", "D", "B", "P" nebo "X". Pokud není formát zadaný, použije se "D".|  
|toLower|Převede řetězec na malá písmena. Například následující vrátí `two by two is four`:  `toLower('Two by Two is Four')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který se má převést na nižší velká a malá písmena. Pokud nemá malým ekvivalentem znaku v řetězci, je zahrnut ve vráceném řetězci beze změny.|  
|toUpper|Převede řetězec na velká písmena. Například následující výraz vrací `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který se má převést na velká písmena. Pokud znak v řetězci nemá velkým ekvivalentem, je zahrnut ve vráceném řetězci beze změny.|  
|IndexOf|Najdete insensitively index určité hodnoty v rámci případu řetězce. Například následující výraz vrací `7`: `indexof('hello, world.', 'world')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který může obsahovat hodnotu.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Hodnota k vyhledání index.|  
|lastindexof|Insensitively najdete poslední index určité hodnoty v rámci případu řetězce. Například následující výraz vrací `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který může obsahovat hodnotu.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Hodnota k vyhledání index.|  
|StartsWith|Kontroluje se, pokud začíná řetězec znaménkem hodnotu případu insensitively. Například následující výraz vrací `true`: `startswith('hello, world', 'hello')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který může obsahovat hodnotu.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Hodnota řetězce mohou začínat.|  
|EndsWith|Kontroluje se, pokud řetězec končí hodnotu případu insensitively. Například následující výraz vrací `true`: `endswith('hello, world', 'world')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který může obsahovat hodnotu.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Hodnota řetězce mohou končit.|  
|split|Rozdělí řetězec pomocí oddělovače. Například následující výraz vrací `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který je rozdělit.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Oddělovač.|  
  
  
## <a name="collection-functions"></a>Kolekce funkcí  
 Tyto funkce pracují přes kolekce například pole, řetězce a někdy slovníky.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|obsahuje|Vrátí hodnotu PRAVDA, pokud slovník obsahuje seznam klíčů, obsahuje hodnotu nebo řetězec obsahuje podřetězec. Například následující výraz vrací `true:``contains('abacaba','aca')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: V rámci kolekce<br /><br /> **Popis**: Povinná hodnota. Kolekce, kterou chcete vyhledávat.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Najít objekt<br /><br /> **Popis**: Povinná hodnota. Objekt, který chcete najít **v rámci kolekce**.|  
|Délka|Vrátí počet prvků v poli nebo řetězci. Například následující výraz vrací `3`:  `length('abc')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Kolekce<br /><br /> **Popis**: Povinná hodnota. Kolekce, kterou chcete získat délku.|  
|Prázdná|Vrátí true, pokud objekt, pole nebo řetězec je prázdný. Například následující výraz vrací `true`:<br /><br /> `empty('')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Kolekce<br /><br /> **Popis**: Povinná hodnota. Kolekce, kterou chcete zkontrolovat, zda není prázdná.|  
|Průnik|Vrátí jedno pole nebo objekt s společné prvky pole nebo objekty do něho předaný. Například tato funkce vrací `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Parametry pro funkci může být buď sada objektů, nebo sada polí (ne kombinace). Pokud jsou dva objekty se stejným názvem, zobrazí se do konečného objektu poslední objekt s tímto názvem.<br /><br /> **Číslo parametru**: 1... *n*<br /><br /> **Název**: Kolekce *n*<br /><br /> **Popis**: Povinná hodnota. Kolekce k vyhodnocení. Objekt musí být ve všech kolekcích předaná do výsledku.|  
|sjednocení|Vrátí jedno pole nebo objekt se všemi prvky, které jsou v pole nebo objekt předaný k němu. Například tato funkce vrací `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Parametry pro funkci může být buď sada objektů, nebo sada polí (ne kombinace). Pokud existují se stejným názvem v konečném výstupu dva objekty, zobrazí se do konečného objektu poslední objekt s tímto názvem.<br /><br /> **Číslo parametru**: 1... *n*<br /><br /> **Název**: Kolekce *n*<br /><br /> **Popis**: Povinná hodnota. Kolekce k vyhodnocení. Objekt, který se zobrazí v některém z kolekce se zobrazí ve výsledku.|  
|první|Vrátí první prvek v poli nebo předaný řetězec. Například tato funkce vrací `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Kolekce<br /><br /> **Popis**: Povinná hodnota. Kolekce, která se má získat první objekt z.|  
|poslední|Vrátí poslední prvek v poli nebo předaný řetězec. Například tato funkce vrací `3`:<br /><br /> `last('0123')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Kolekce<br /><br /> **Popis**: Povinná hodnota. Kolekce, která se má získat poslední objekt z.|  
|Take|Vrátí první **počet** předaný prvky z pole nebo řetězec, například tato funkce vrací `[1, 2]`:  `take([1, 2, 3, 4], 2)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Kolekce<br /><br /> **Popis**: Povinná hodnota. Kolekce se má získat první **počet** objekty z.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Počet<br /><br /> **Popis**: Povinná hodnota. Počet objektů trvat, než **kolekce**. Musí být kladné celé číslo.|  
|přeskočit|Vrátí prvek pole, počínaje indexem **počet**, například tato funkce vrací `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Kolekce<br /><br /> **Popis**: Povinná hodnota. Kolekce, kterou chcete přeskočit první **počet** objekty z.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Počet<br /><br /> **Popis**: Povinná hodnota. Počet objektů, které chcete odebrat z přední části **kolekce**. Musí být kladné celé číslo.|  
  
## <a name="logical-functions"></a>Logické funkce  
 Tyto funkce jsou užitečné v podmínkách, slouží k vyhodnocení jakéhokoli typu logiku.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|rovná se|Vrátí true, pokud dvě hodnoty rovnají. Například pokud má parametr1 hodnotu foo, následující výraz by vrátil `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: Povinná hodnota. Objekt k porovnání s **objektu 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: Povinná hodnota. Objekt k porovnání s **objektu 1**.|  
|méně|Vrátí hodnotu PRAVDA, pokud je první argument menší než druhý. Mějte na paměti, může být pouze hodnoty typu integer, float nebo řetězec. Například následující výraz vrací `true`:  `less(10,100)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: Povinná hodnota. Objekt, který chcete zkontrolovat, zda není menší než **objektu 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: Povinná hodnota. Objekt, který chcete zkontrolovat, zda není větší než **objektu 1**.|  
|lessOrEquals|Vrátí true, pokud je první argument menší než nebo roven druhému. Mějte na paměti, může být pouze hodnoty typu integer, float nebo řetězec. Například následující výraz vrací `true`:  `lessOrEquals(10,10)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: Povinná hodnota. Objekt, který chcete zkontrolovat, zda není menší nebo rovno **objektu 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: Povinná hodnota. Objekt, který chcete zkontrolovat, zda není větší než nebo rovna hodnotě **objektu 1**.|  
|větší|Vrátí true, pokud je první argument větší než druhý. Mějte na paměti, může být pouze hodnoty typu integer, float nebo řetězec. Například následující výraz vrací `false`:  `greater(10,10)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: Povinná hodnota. Objekt, který chcete zkontrolovat, zda není větší než **objektu 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: Povinná hodnota. Objekt, který chcete zkontrolovat, zda není menší než **objektu 1**.|  
|greaterOrEquals|Vrátí true, pokud je první argument větší než nebo roven druhému. Mějte na paměti, může být pouze hodnoty typu integer, float nebo řetězec. Například následující výraz vrací `false`:  `greaterOrEquals(10,100)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: objekt 1<br /><br /> **Popis**: Povinná hodnota. Objekt, který chcete zkontrolovat, zda není větší než nebo rovna hodnotě **objektu 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: Povinná hodnota. Objekt, který chcete zkontrolovat, zda není menší než nebo rovno **objektu 1**.|  
|a|Vrátí true, pokud jsou oba parametry true. Oba argumenty musí být logické hodnoty. Vrátí následující `false`:  `and(greater(1,10),equals(0,0))`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Logická 1<br /><br /> **Popis**: Povinná hodnota. První argument, který musí být `true`.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Logická 2<br /><br /> **Popis**: Povinná hodnota. Druhý argument musí být `true`.|  
|nebo|Vrátí true, pokud platí některý z parametrů. Oba argumenty musí být logické hodnoty. Vrátí následující `true`:  `or(greater(1,10),equals(0,0))`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Logická 1<br /><br /> **Popis**: Povinná hodnota. První argument, který může být `true`.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Logická 2<br /><br /> **Popis**: Povinná hodnota. Druhý argument může být `true`.|  
|ne|Vrátí hodnotu PRAVDA, pokud je parametr `false`. Oba argumenty musí být logické hodnoty. Vrátí následující `true`:  `not(contains('200 Success','Fail'))`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Logická hodnota<br /><br /> **Popis**: Vrátí hodnotu PRAVDA, pokud je parametr `false`. Oba argumenty musí být logické hodnoty. Vrátí následující `true`:  `not(contains('200 Success','Fail'))`|  
|if|Vrátí zadanou hodnotu na základě-li výsledkem výrazů, které jsou k dispozici `true` nebo `false`.  Například následující vrátí `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Výraz<br /><br /> **Popis**: Povinná hodnota. Logická hodnota, která určuje, která hodnota je vracenému výrazem.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: True<br /><br /> **Popis**: Povinná hodnota. Hodnota, která má vrátit, pokud má výraz `true`.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: False<br /><br /> **Popis**: Povinná hodnota. Hodnota, která má vrátit, pokud má výraz `false`.|  
  
## <a name="conversion-functions"></a>Převodní funkce  
 Tyto funkce slouží k převodu mezi nativní typy v jazyce:  
  
-   řetězec  
  
-   integer  
  
-   float  
  
-   Boolean  
  
-   Pole  
  
-   slovníky  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|int|Převede parametr na celé číslo. Například následující výraz vrací 100 jako číslo, spíše než řetězec:  `int('100')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Hodnota<br /><br /> **Popis**: Povinná hodnota. Hodnota, která je převést na celé číslo.|  
|řetězec|Převede parametr na řetězec. Například následující výraz vrací `'10'`:  `string(10)` Můžete také převést objekt na řetězec, například pokud **foo** parametru je objekt s jednou vlastností `bar : baz`, pak by vrátil následující `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Hodnota<br /><br /> **Popis**: Povinná hodnota. Hodnota, která je převedena na řetězec.|  
|json|Převede parametr na hodnotu typu JSON. Je opakem string(). Například následující výraz vrací `[1,2,3]` jako pole, spíše než řetězec:<br /><br /> `json('[1,2,3]')`<br /><br /> Podobně můžete převést řetězec na objekt. Například `json('{"bar" : "baz"}')` vrátí:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec, který je převedena na hodnotu nativního typu.<br /><br /> Funkce json podporuje také vstup xml. Například, hodnota parametru:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> je převedena na následující json:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Převede argument parametru na číslo s plovoucí desetinnou čárkou. Například následující výraz vrací `10.333`:  `float('10.333')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Hodnota<br /><br /> **Popis**: Povinná hodnota. Hodnota, která je převeden na číslo s plovoucí desetinnou čárkou.|  
|BOOL|Převede parametr na logickou hodnotu. Například následující výraz vrací `false`:  `bool(0)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Hodnota<br /><br /> **Popis**: Povinná hodnota. Hodnota, která se převádí na logickou hodnotu.|  
|sloučení|Vrátí první nenulový objekt v předaných argumentech. Poznámka: prázdný řetězec není null. Například, pokud nejsou definované parametry 1 a 2, vrátí se `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Číslo parametru**: 1... *n*<br /><br /> **Název**: Objekt*n*<br /><br /> **Popis**: Povinná hodnota. Objekty, které chcete vyhledat `null`.|  
|ve formátu Base64|Vrátí reprezentaci ve formátu base64 vstupního řetězce. Například následující výraz vrací `c29tZSBzdHJpbmc=`:  `base64('some string')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec 1<br /><br /> **Popis**: Povinná hodnota. Řetězec určený ke kódování do formátu Base64.|  
|base64ToBinary|Vrátí binární reprezentaci řetězce s kódováním base64. Například následující výraz vrátí binární reprezentaci některé řetězce: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec s kódováním base64.|  
|base64ToString|Vrátí řetězcovou reprezentaci based64 kódovaný řetězec. Například následující výraz vrací některé řetězce: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec s kódováním base64.|  
|Binární hodnota|Vrátí binární reprezentaci hodnoty.  Například následující výraz vrátí binární reprezentaci některých řetězce: `binary(‘some string’).`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Hodnota<br /><br /> **Popis**: Povinná hodnota. Hodnota, která se převádí do binárního formátu.|  
|dataUriToBinary|Vrátí binární reprezentaci identifikátoru URI dat. Například následující výraz vrátí binární reprezentaci některých řetězce: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Identifikátor URI dat k převést do binárního formátu.|  
|dataUriToString|Vrátí řetězcovou reprezentaci identifikátoru URI dat. Například následující výraz vrací některé řetězce: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br />**Popis**: Povinná hodnota. Identifikátor URI převést řetězcovou reprezentaci dat.|  
|Parametr|Vrátí identifikátor URI určité hodnoty. Například následující výraz vrací data: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Číslo parametru**: 1<br /><br />**Název**: Hodnota<br /><br />**Popis**: Povinná hodnota. Hodnota převedená na identifikátor URI dat.|  
|decodeBase64|Vrátí řetězcovou reprezentaci based64 vstupního řetězce. Například následující výraz vrací `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Vrátí řetězcovou reprezentaci based64 vstupního řetězce.|  
|encodeuricomponent –|Adresa URL – řídicí sekvence řetězců, které je předáno. Například následující výraz vrací `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec k uvození URL problematické znaky z.|  
|decodeuricomponent –|Zrušit – adresa URL – řídicí sekvence řetězců, které je předáno. Například následující výraz vrací `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Řetězec pro dekódování adresu URL problematické znaky z.|  
|decodeDataUri|Vrátí binární reprezentaci vstupního řetězce identifikátoru URI dat. Například následující výraz vrátí binární reprezentaci `some string`:  `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br /> **Popis**: Povinná hodnota. Parametr má dekódovat do binárního formátu.|  
|uriComponent|Vrátí identifikátor URI kódovaný reprezentaci hodnoty. Například následující výraz vrací `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Parametr podrobnosti: Číslo: 1, název: Řetězec, popis: Povinná hodnota. Řetězec, který má být zakódovat do identifikátoru URI.|  
|uriComponentToBinary|Vrátí že řetězec s kódováním binární reprezentaci identifikátoru URI. Například následující výraz vrátí binární reprezentaci `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Řetězec<br /><br />**Popis**: Povinná hodnota. Řetězec kódovaný identifikátor URI.|  
|uriComponentToString|Vrátí že řetězec s kódováním řetězcovou reprezentaci identifikátoru URI. Například následující výraz vrací `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Číslo parametru**: 1<br /><br />**Název**: Řetězec<br /><br />**Popis**: Povinná hodnota. Řetězec kódovaný identifikátor URI.|  
|xml|Vrátí reprezentaci xml hodnoty. Například následující výraz vrací obsahu xml reprezentována `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. Podporuje funkce xml také vstupu objekt JSON. Například parametr `{ "abc": "xyz" }` je převedena na obsahem xml `\<abc>xyz\</abc>`<br /><br /> **Číslo parametru**: 1<br /><br />**Název**: Hodnota<br /><br />**Popis**: Povinná hodnota. Hodnota pro převod do formátu XML.|  
|výraz XPath|Vrátí pole z uzlů xml odpovídající výraz xpath, který se vyhodnotí výraz xpath hodnoty.<br /><br />  **Příklad 1**<br /><br /> Předpokládejme, že hodnota parametru 'p1' je řetězcová reprezentace následující kód XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Tento kód: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> Vrátí<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> vzhledem k tomu<br /><br /> 2. Tento kód: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> Vrátí<br /><br /> `13`<br /><br /> <br /><br /> **Příklad 2**<br /><br /> Daný následující obsah XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Tento kód: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> nebo<br /><br /> 2. Tento kód: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Vrátí<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> a<br /><br /> 3. Tento kód: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Vrátí<br /><br /> ``bar``<br /><br /> **Číslo parametru**: 1<br /><br />**Název**: XML<br /><br />**Popis**: Povinná hodnota. Kód XML, na kterém se má vyhodnotit výraz jazyka XPath.<br /><br /> **Číslo parametru**: 2<br /><br />**Název**: Výraz XPath<br /><br />**Popis**: Povinná hodnota. Výraz XPath pro vyhodnocení.|  
|pole|Převede parametr na pole.  Například následující výraz vrací `["abc"]`: `array('abc')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Hodnota<br /><br /> **Popis**: Povinná hodnota. Hodnota, která se převádí na pole.|
|createArray|Vytvoří z parametrů pole.  Například následující výraz vrací `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Číslo parametru**: 1... n<br /><br /> **Název**: Žádné n<br /><br /> **Popis**: Povinná hodnota. Hodnoty, které mají sloučit do pole.|

## <a name="math-functions"></a>Matematické funkce  
 Tyto funkce můžete používat pro některé typy čísel: **celých čísel** a **čísel s plovoucí čárkou**.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|add|Vrátí výsledek součtu daných dvou čísel. Například tato funkce vrací `20.333`:  `add(10,10.333)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Sčítanci 1<br /><br /> **Popis**: Povinná hodnota. Číslo, které má přidat do **Sčítanci 2**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Sčítanci 2<br /><br /> **Popis**: Povinná hodnota. Číslo, které má přidat do **Sčítanci 1**.|  
|Sub|Vrátí výsledek odečtení dvou čísel. Například tato funkce vrací: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: menšenec<br /><br /> **Popis**: Povinná hodnota. Číslo, které **Subtrahend** je odebráno.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: menšitel<br /><br /> **Popis**: Povinná hodnota. Počet odebrání **Minuend**.|  
|mul|Vrátí výsledek násobení dvou čísel. Například následující vrátí `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Násobenec 1<br /><br /> **Popis**: Povinná hodnota. Číslo pro vynásobení **násobenec 2** s.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Násobenec 2<br /><br /> **Popis**: Povinná hodnota. Číslo pro vynásobení **násobenec 1** s.|  
|div|Vrátí výsledek podílu daných dvou čísel. Například následující vrátí `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: podíl<br /><br /> **Popis**: Povinná hodnota. Číslo, které má dělit **dělitel**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: dělitel<br /><br /> **Popis**: Povinná hodnota. Číslo, které se má dělit **Delenec** podle.|  
|MOD|Vrátí výsledek zbytek po dělení daných dvou čísel (modulo). Například následující výraz vrací `2`:<br /><br /> `mod(10,4)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: podíl<br /><br /> **Popis**: Povinná hodnota. Číslo, které má dělit **dělitel**.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: dělitel<br /><br /> **Popis**: Povinná hodnota. Číslo, které se má dělit **Delenec** podle. Po dělení se zbytek odebere.|  
|min|Existují dva různé vzorce pro volání této funkce: `min([0,1,2])` Tady min přijímá pole. Tento výraz vrátí `0`. Tato funkce také může trvat čárkou oddělený seznam hodnot:  `min(0,1,2)` Tato funkce také vrátí hodnotu 0. Všimněte si, že se všechny hodnoty musí být číslo, takže pokud je parametr pole musí obsahovat pouze čísla v ní.<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Kolekce nebo hodnota<br /><br /> **Popis**: Povinná hodnota. Může jí být buď pole hodnot najít minimální hodnota, nebo první hodnota sady.<br /><br /> **Číslo parametru**: 2... *n*<br /><br /> **Název**: Hodnota *n*<br /><br /> **Popis**: Volitelné. Pokud je první parametr hodnotu, pak můžete předat další hodnoty a minimální všechny předané hodnoty jsou vráceny.|  
|max|Existují dva různé vzorce pro volání této funkce:  `max([0,1,2])`<br /><br /> Maximální počet zde přijímá pole. Tento výraz vrátí `2`. Tato funkce také může trvat čárkou oddělený seznam hodnot:  `max(0,1,2)` Tato funkce vrátí hodnotu 2. Všimněte si, že se všechny hodnoty musí být číslo, takže pokud je parametr pole musí obsahovat pouze čísla v ní.<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Kolekce nebo hodnota<br /><br /> **Popis**: Povinná hodnota. Může jí být buď pole hodnot najít maximální hodnota, nebo první hodnota sady.<br /><br /> **Číslo parametru**: 2... *n*<br /><br /> **Název**: Hodnota *n*<br /><br /> **Popis**: Volitelné. Pokud je první parametr hodnotu, pak můžete předat další hodnoty a maximálně všechny předané hodnoty jsou vráceny.|  
|rozsah| Vygeneruje pole celých čísel od určitého počtu, a definujte Délka vráceného pole. Například tato funkce vrací `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Počáteční index<br /><br /> **Popis**: Povinná hodnota. Je první celé číslo v poli.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Počet<br /><br /> **Popis**: Povinná hodnota. Počet celých čísel, která jsou v poli.|  
|rand| Vygeneruje náhodné celé číslo v zadaném rozsahu (zahrnuje obě krajní. Například to může vrátit `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Minimální<br /><br /> **Popis**: Povinná hodnota. Nejnižší celé číslo, které mohou být vráceny.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Maximum<br /><br /> **Popis**: Povinná hodnota. Nejvyšší číslo, které mohou být vráceny.|  
  
## <a name="date-functions"></a>Datové funkce  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|utcnow|Vrátí aktuální časové razítko jako řetězec. Například `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Formát<br /><br /> **Popis**: Volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formátovací vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , která určuje, jak formátovat hodnotu tohoto časového razítka. Pokud není formát zadaný, použije se formát ISO 8601 ("o").|  
|přidat_sekundy|Přidá celočíselný počet sekund do předaného řetězcového časového razítka. Počet sekund může být pozitivní nebo negativní. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Časové razítko<br /><br /> **Popis**: Povinná hodnota. Řetězec, který obsahuje čas.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Sekundy<br /><br /> **Popis**: Povinná hodnota. Počet sekund se má přidat. Může být záporná hodnota, při které se sekundy odečtou.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: Formát<br /><br /> **Popis**: Volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formátovací vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , která určuje, jak formátovat hodnotu tohoto časového razítka. Pokud není formát zadaný, použije se formát ISO 8601 ("o").|  
|addminutes|Přidá celočíselný počet minut do předaného řetězcového časového razítka. Počet minut, může být pozitivní nebo negativní. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Časové razítko<br /><br /> **Popis**: Povinná hodnota. Řetězec, který obsahuje čas.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Minut<br /><br /> **Popis**: Povinná hodnota. Počet minut pro přidání. Může být záporná hodnota, při které se minuty odečtou.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: Formát<br /><br /> **Popis**: Volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formátovací vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , která určuje, jak formátovat hodnotu tohoto časového razítka. Pokud není formát zadaný, použije se formát ISO 8601 ("o").|  
|addhours|Přidá celočíselný počet hodin do předaného řetězcového časového razítka. Počet hodin, může být pozitivní nebo negativní. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Časové razítko<br /><br /> **Popis**: Povinná hodnota. Řetězec, který obsahuje čas.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Hodiny<br /><br /> **Popis**: Povinná hodnota. Počet hodin, které chcete přidat. Může být záporná hodnota, při které se hodiny odečtou.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: Formát<br /><br /> **Popis**: Volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formátovací vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , která určuje, jak formátovat hodnotu tohoto časového razítka. Pokud není formát zadaný, použije se formát ISO 8601 ("o").|  
|přidat_dny|Přidá celočíselný počet dní do předaného řetězcového časového razítka. Počet dní, mohou být kladné nebo záporné. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Časové razítko<br /><br /> **Popis**: Povinná hodnota. Řetězec, který obsahuje čas.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Dnů<br /><br /> **Popis**: Povinná hodnota. Počet dní, které chcete přidat. Může být záporná hodnota, při které se dny odečtou.<br /><br /> **Číslo parametru**: 3<br /><br /> **Název**: Formát<br /><br /> **Popis**: Volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formátovací vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , která určuje, jak formátovat hodnotu tohoto časového razítka. Pokud není formát zadaný, použije se formát ISO 8601 ("o").|  
|formatDateTime|Vrací řetězec ve formátu data. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Číslo parametru**: 1<br /><br /> **Název**: Datum<br /><br /> **Popis**: Povinná hodnota. Řetězec, který obsahuje datum.<br /><br /> **Číslo parametru**: 2<br /><br /> **Název**: Formát<br /><br /> **Popis**: Volitelné. Buď [jeden znak specifikátoru formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formátovací vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) , která určuje, jak formátovat hodnotu tohoto časového razítka. Pokud není formát zadaný, použije se formát ISO 8601 ("o").|  

## <a name="next-steps"></a>Další postup
Seznam systémových proměnných můžete použít ve výrazech najdete v tématu [systémové proměnné](control-flow-system-variables.md).

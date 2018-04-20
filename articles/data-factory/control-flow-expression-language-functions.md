---
title: Výraz a funkce v Azure Data Factory | Microsoft Docs
description: Tento článek obsahuje informace o výrazy a funkce, které můžete použít při vytváření entit data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 1625b37a41082f8536d103701b1356a13a5dd837
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Výrazy a funkce v Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-functions-variables.md)
> * [Verze 2 – Preview](control-flow-expression-language-functions.md)

Tento článek obsahuje podrobné informace o výrazů a funkcí podporovaných službou Azure Data Factory (verze 2). 

## <a name="introduction"></a>Úvod
Hodnoty JSON v definici může být literál nebo výrazy, které jsou vyhodnocovány v době běhu. Příklad:  
  
```json
"name": "value"
```

 (nebo)  
  
```json
"name": "@pipeline().parameters.password"
```


> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [funkce a proměnné v V1 objekt pro vytváření dat](v1/data-factory-functions-variables.md).


## <a name="expressions"></a>Výrazy  
Výrazy může vyskytovat kdekoli v hodnotu řetězce formátu JSON a vždy mít za následek jinou hodnotu JSON. Pokud je hodnota JSON výrazu, těle výrazu je extrahován odebráním znaku @ (@). Pokud je potřeba řetězcový literál, který začíná @, je nutné uvést pomocí@. Následující příklady ukazují, jak se vyhodnocují výrazy.  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|"parametry"|Znaky, parametry, jsou vráceny.|  
|"parametry [1]"|Jsou vráceny znaky 'parametry [1]'.|  
|"@@"|Řetězec 1 znak, který obsahuje ' @' je vrácen.|  
|" @"|Řetězec 2 znak, který obsahuje ' @' je vrácen.|  
  
 Výrazy se může zobrazit i uvnitř řetězce, pomocí funkce volá *řetězec interpolace* kde jsou výrazy uzavřen do `@{ ... }`. Příklad: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Pomocí řetězce interpolace, výsledek je vždy řetězec. Řekněme, byla definována `myNumber` jako `42` a `myString` jako `foo`:  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|"@pipeline().parameters.myString"| Vrátí `foo` jako řetězec.|  
|"@{pipeline().parameters.myString}"| Vrátí `foo` jako řetězec.|  
|"@pipeline().parameters.myNumber"| Vrátí `42` jako *číslo*.|  
|"@{pipeline().parameters.myNumber}"| Vrátí `42` jako *řetězec*.|  
|"Odpověď je: @{kanálu ().parameters.myNumber}"| Vrátí řetězec `Answer is: 42`.|  
|"@concat(' Odpověď je: ', string(pipeline().parameters.myNumber))"| Vrátí řetězec `Answer is: 42`|  
|"Odpověď je: @@ {kanálu ().parameters.myNumber}"| Vrátí řetězec `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Příklady

#### <a name="a-dataset-with-a-parameter"></a>Datovou sadu s parametrem
V následujícím příkladu BlobDataset přebírá parametr s názvem **cestu**. Jeho hodnota se používá k nastavení hodnoty **folderPath** vlastnost pomocí těchto výrazů: `@{dataset().path}`. 

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
V následujícím příkladu kanálu trvá **inputPath** a **outputPath** parametry. **Cesta** pro parametrizované blob datovou sadu nastavenou pomocí hodnoty těchto parametrů. Použít se zde syntaxe je: `pipeline().parameters.parametername`. 

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
  
## <a name="functions"></a>Funkce  
 Můžete volat funkce v rámci výrazy. Následující části obsahují informace o funkcích, které můžete použít ve výrazu.  

## <a name="string-functions"></a>Řetězcové funkce  
 Následující funkce se vztahuje pouze na řetězce. Také můžete použít několik funkcí kolekce řetězce.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|concat|Kombinuje libovolný počet řetězce společně. Například, pokud je parameter1 `foo,` by vrátit následující výraz `somevalue-foo-somevalue`:  `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Parametr číslo**: 1... *n*<br /><br /> **Název**: řetězec *n*<br /><br /> **Popis**: vyžaduje. Řetězce se zkombinovat do jednoho řetězce.|  
|dílčí řetězec|Vrátí podmnožinu znaků z řetězce. Například následující výraz:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> Vrátí:<br /><br /> `foo`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, ze kterého je provedena dílčí řetězec.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: počáteční index<br /><br /> **Popis**: vyžaduje. Index kde dílčí řetězec začíná v parametru 1.<br /><br /> **Parametr číslo**: 3<br /><br /> **Název**: délka<br /><br /> **Popis**: vyžaduje. Délka dílčí řetězec.|  
|Nahradit|Nahradí řetězec daný řetězec. Například ve výrazu:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> Vrátí:<br /><br /> `the new string`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje.  Pokud parametr 2 nachází v parametru 1, řetězec, který je hledán parametr 2 a aktualizovat pomocí parametru 3.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: původní řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, který má nahradit parametr 3, pokud je nalezena shoda v parametru 1<br /><br /> **Parametr číslo**: 3<br /><br /> **Název**: nový řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, který se používá k nahrazení řetězec v parametru 2, pokud je nalezena shoda v parametru 1.|  
|Identifikátor GUID| Generuje globálně jedinečné řetězce (neboli. identifikátor GUID). Například nebylo možné vygenerovat následující výstup `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Specifikátor jednoho formátu, který označuje [způsob formátování hodnotu identifikátoru Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Formát parametru může být "N", "D", "B", "P" nebo "X". Pokud formát není zadaný, použije se "D".|  
|toLower|Převede řetězec na malá písmena. Například následující vrátí `two by two is four`:  `toLower('Two by Two is Four')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec převést na nižší velká a malá písmena. Pokud znak v řetězci nemá malá ekvivalentní, je zahrnut v vrácený řetězec beze změny.|  
|toUpper|Převede řetězec na velká písmena. Například následující výraz vrací `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec převést na horní velká a malá písmena. Pokud znak v řetězci nemá ekvivalentní velká, je součástí beze změny v vrácený řetězec.|  
|IndexOf|Index hodnoty v případě řetězec insensitively najít. Například následující výraz vrací `7`: `indexof('hello, world.', 'world')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, který může obsahovat hodnotu.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Hodnota k vyhledání index.|  
|lastindexof|Najít poslední index na hodnotu v případě řetězec insensitively. Například následující výraz vrací `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, který může obsahovat hodnotu.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Hodnota k vyhledání index.|  
|StartsWith|Kontroluje, zda řetězec začíná případu hodnotu insensitively. Například následující výraz vrací `true`: `lastindexof('hello, world', 'hello')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, který může obsahovat hodnotu.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Hodnota řetězce může začínat.|  
|EndsWith|Kontroluje, zda řetězec končí případem hodnotu insensitively. Například následující výraz vrací `true`: `lastindexof('hello, world', 'world')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, který může obsahovat hodnotu.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Hodnota řetězce mohou končit.|  
|split|Rozdělí řetězec pomocí oddělovače. Například následující výraz vrací `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, který je rozdělení.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Oddělovač.|  
  
  
## <a name="collection-functions"></a>Kolekce funkcí  
 Tyto funkce pracovat s kolekcí, jako je například pole, řetězce a někdy slovník.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|obsahuje|Vrátí hodnotu PRAVDA, pokud slovník obsahuje seznam klíčů, obsahuje hodnotu nebo řetězec obsahuje dílčí řetězec. Například následující výraz vrací `true:``contains('abacaba','aca')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: v rámci kolekce<br /><br /> **Popis**: vyžaduje. Kolekce se hledat v.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: najít objekt<br /><br /> **Popis**: vyžaduje. Objekt, který chcete najít uvnitř **v rámci kolekce**.|  
|Délka|Vrátí počet prvků v pole nebo řetězec. Například následující výraz vrací `3`:  `length('abc')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: vyžaduje. Kolekce získat délku.|  
|prázdný|Vrátí hodnotu true Pokud objekt, pole nebo řetězec je prázdný. Například následující výraz vrací `true`:<br /><br /> `empty('')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: vyžaduje. Kolekce ke kontrole, jestli je prázdný.|  
|průnik|Vrátí objekt s běžných elementech nebo jednoho pole mezi pole nebo objekty do ní předán. Například funkce vrátí hodnotu `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Parametry pro funkci může být buď sadu objektů, nebo sadu pole (není jejich směs). Existují dva objekty se stejným názvem, zobrazí se v poslední objekt poslední objekt se stejným názvem.<br /><br /> **Parametr číslo**: 1... *n*<br /><br /> **Název**: kolekce *n*<br /><br /> **Popis**: vyžaduje. Kolekce k vyhodnocení. Objekt musí být ve všech kolekcích předaná se objeví ve výsledku.|  
|sjednocení|Vrátí objekt s všechny elementy, které jsou v pole nebo objekt předaný nebo jednoho pole. Například funkce vrátí hodnotu `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Parametry pro funkci může být buď sadu objektů, nebo sadu pole (není jejich směs). Pokud jsou dva objekty se stejným názvem v finální výstup, se zobrazí v posledním objekt poslední objekt se stejným názvem.<br /><br /> **Parametr číslo**: 1... *n*<br /><br /> **Název**: kolekce *n*<br /><br /> **Popis**: vyžaduje. Kolekce k vyhodnocení. Objekt, který se zobrazí v některém z kolekce se zobrazí ve výsledku.|  
|první|Vrátí první prvek pole nebo předaný řetězec. Například funkce vrátí hodnotu `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: vyžaduje. Provést první objekt z kolekce.|  
|poslední|Vrátí poslední prvek v poli nebo předaný řetězec. Například funkce vrátí hodnotu `3`:<br /><br /> `last('0123')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: vyžaduje. Provést poslední objekt z kolekce.|  
|proveďte|Vrátí první **počet** předaná elementy z pole nebo řetězec, například funkce vrátí hodnotu `[1, 2]`:  `take([1, 2, 3, 4], 2)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: vyžaduje. Kolekce provést první **počet** objekty z.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: počet<br /><br /> **Popis**: vyžaduje. Počet objektů, které chcete od **kolekce**. Musí být kladné celé číslo.|  
|Přeskočit|Vrátí elementy v poli začínající na indexu **počet**, například funkce vrátí hodnotu `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: vyžaduje. Kolekce tak, aby přeskočil první **počet** objekty z.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: počet<br /><br /> **Popis**: vyžaduje. Počet objektů, které chcete odebrat z před **kolekce**. Musí být kladné celé číslo.|  
  
## <a name="logical-functions"></a>Logické funkce  
 Tyto funkce jsou užitečné v podmínkách, slouží k vyhodnocení libovolného typu logiku.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|rovná se|Pokud vrátí hodnotu true jsou dvě hodnoty rovny. Například pokud parameter1 foo, následující výraz by vrátit `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: objektu 1<br /><br /> **Popis**: vyžaduje. Objekt k porovnání s **objekt 2**.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: vyžaduje. Objekt k porovnání s **objektu 1**.|  
|menší|Vrátí hodnotu PRAVDA, pokud první argument je menší než druhý. Všimněte si, může být pouze hodnoty typu integer, float nebo řetězec. Například následující výraz vrací `true`:  `less(10,100)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: objektu 1<br /><br /> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je menší než **objekt 2**.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je větší než **objektu 1**.|  
|lessOrEquals|Vrátí hodnotu true Pokud první argument je menší než nebo rovna hodnotě druhý. Všimněte si, může být pouze hodnoty typu integer, float nebo řetězec. Například následující výraz vrací `true`:  `lessOrEquals(10,10)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: objektu 1<br /><br /> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je menší nebo rovno **objekt 2**.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je větší než nebo rovna hodnotě **objektu 1**.|  
|větší|Vrátí hodnotu true Pokud první argument je větší než druhý. Všimněte si, může být pouze hodnoty typu integer, float nebo řetězec. Například následující výraz vrací `false`:  `greater(10,10)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: objektu 1<br /><br /> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je větší než **objekt 2**.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je menší než **objektu 1**.|  
|greaterOrEquals|První argument větší než nebo rovna hodnotě druhý vrátí hodnotu PRAVDA. Všimněte si, může být pouze hodnoty typu integer, float nebo řetězec. Například následující výraz vrací `false`:  `greaterOrEquals(10,100)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: objektu 1<br /><br /> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je větší než nebo rovna hodnotě **objekt 2**.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: objekt 2<br /><br /> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je menší než nebo rovna hodnotě **objektu 1**.|  
|a|Vrátí hodnotu true Pokud jsou splněny obě parametry. Oba argumenty musí být logické hodnoty. Vrátí následující `false`:  `and(greater(1,10),equals(0,0))`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: logická hodnota 1<br /><br /> **Popis**: vyžaduje. První argument, který musí být `true`.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: logická hodnota 2<br /><br /> **Popis**: vyžaduje. Musí být druhým argumentem `true`.|  
|nebo|Vrátí hodnotu true pokud platí některá z parametrů. Oba argumenty musí být logické hodnoty. Vrátí následující `true`:  `or(greater(1,10),equals(0,0))`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: logická hodnota 1<br /><br /> **Popis**: vyžaduje. První argument, který může být `true`.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: logická hodnota 2<br /><br /> **Popis**: vyžaduje. Může být druhým argumentem `true`.|  
|není|Vrátí hodnotu PRAVDA, pokud je parametr `false`. Oba argumenty musí být logické hodnoty. Vrátí následující `true`:  `not(contains('200 Success','Fail'))`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: logická hodnota<br /><br /> **Popis**: vrátí hodnotu true, pokud je parametr `false`. Oba argumenty musí být logické hodnoty. Vrátí následující `true`:  `not(contains('200 Success','Fail'))`|  
|if|Vrátí zadanou hodnotu podle Pokud výsledky výraz určený v `true` nebo `false`.  Například následující vrátí `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: výraz<br /><br /> **Popis**: vyžaduje. Logická hodnota, která určuje, která hodnota je vracenému výrazem.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: True<br /><br /> **Popis**: vyžaduje. Hodnota, která má být vrácena, pokud je výraz `true`.<br /><br /> **Parametr číslo**: 3<br /><br /> **Název**: False<br /><br /> **Popis**: vyžaduje. Hodnota, která má být vrácena, pokud je výraz `false`.|  
  
## <a name="conversion-functions"></a>Převodní funkce  
 Pro převod mezi jednotlivé nativní typy v jazyce, se používají tyto funkce:  
  
-   řetězec  
  
-   celé číslo  
  
-   Plovoucí desetinná čárka  
  
-   Boolean  
  
-   Pole  
  
-   slovník  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|celá čísla|Parametr převeďte na celé číslo. Například následující výraz vrátí 100 jako číslo, nikoli řetězec:  `int('100')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: vyžaduje. Hodnota, která je převést na celé číslo.|  
|řetězec|Převeďte parametr na řetězec. Například následující výraz vrací `'10'`: `string(10)` můžete také převést objekt na řetězec, například pokud **foo** parametr je objekt s jednu vlastnost `bar : baz`, pak by následující Vrátí `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: vyžaduje. Hodnota, která je převedeno na řetězec.|  
|JSON|Převeďte parametr na hodnotu typu formátu JSON. Je opakem string(). Například následující výraz vrací `[1,2,3]` jako pole, nikoli řetězec:<br /><br /> `parse('[1,2,3]')`<br /><br /> Podobně můžete převést řetězec na objekt. Například `json('{"bar" : "baz"}')` vrátí:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, který je převést na hodnotu nativním typu.<br /><br /> Funkce json podporuje také vstup xml. Například hodnota parametru:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> jsou převedeny na následujícím kódu json:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|Plovoucí desetinná čárka|Argument parametr převeďte na číslo s plovoucí desetinnou čárkou. Například následující výraz vrací `10.333`:  `float('10.333')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: vyžaduje. Hodnota, která je převedena na číslo s plovoucí desetinnou čárkou.|  
|BOOL|Převeďte parametr na booleovskou hodnotu. Například následující výraz vrací `false`:  `bool(0)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: vyžaduje. Hodnota, která je převést na logickou hodnotu.|  
|sloučení|Vrátí první objekt nesmí být nulová v předané argumenty. Poznámka: prázdný řetězec není null. Například, pokud nejsou definovány parametry 1 a 2, tento příkaz vrátí `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Parametr číslo**: 1... *n*<br /><br /> **Název**: objekt*n*<br /><br /> **Popis**: vyžaduje. Objekty, které chcete zkontrolovat `null`.|  
|formátu Base64.|Vrátí reprezentaci base64 vstupní řetězec. Například následující výraz vrací `c29tZSBzdHJpbmc=`:  `base64('some string')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec 1<br /><br /> **Popis**: vyžaduje. Řetězec určený ke kódování do reprezentace base64.|  
|base64ToBinary|Vrátí binární reprezentace řetězec s kódováním base64. Například následující výraz vrací binární reprezentace některé řetězce: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec s kódováním base64.|  
|base64ToString|Vrátí řetězcovou reprezentaci based64 kódovaný řetězec. Například následující výraz vrátí některé řetězec: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec s kódováním base64.|  
|Binární hodnota|Vrátí binární reprezentace hodnoty.  Například následující výraz vrací binární reprezentace některé řetězce: `binary(‘some string’).`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: vyžaduje. Hodnota, která je převést na binární formát.|  
|dataUriToBinary|Vrátí binární reprezentace dat identifikátor URI. Například následující výraz vrací binární reprezentace některé řetězce: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Data URI převést na binární reprezentace.|  
|dataUriToString|Vrátí řetězcovou reprezentaci dat identifikátor URI. Například následující výraz vrátí některé řetězec: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br />**Popis**: vyžaduje. Identifikátor URI převést řetězcovou reprezentaci dat.|  
|dataUri|Vrací data URI hodnoty. Například následující výraz vrací data: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Parametr číslo**: 1<br /><br />**Název**: hodnota<br /><br />**Popis**: vyžaduje. Hodnota převést na identifikátor URI dat.|  
|decodeBase64|Vrátí řetězcovou reprezentaci řetězce vstupní based64. Například následující výraz vrací `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vrátí řetězcovou reprezentaci řetězce vstupní based64.|  
|encodeuricomponent –|URL – řídicí sekvence řetězec, který se předává v. Například následující výraz vrací `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec, abyste se vyhnuli URL unsafe znaky z.|  
|decodeuricomponent –|Zrušení – adresa URL – řídicí sekvence řetězec, který se předává v. Například následující výraz vrací `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. Řetězec k dekódování URL unsafe znaků z.|  
|decodeDataUri|Vrátí binární reprezentace vstupní data řetězce URI. Například následující výraz vrací binární reprezentace `some string`:  `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br /> **Popis**: vyžaduje. DataURI dekódování do binární reprezentace.|  
|uriComponent|Vrátí identifikátor URI kódovaný reprezentaci hodnoty. Například následující výraz vrací `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Podrobnosti o parametru: Číslo: 1, název: řetězec, popis: vyžaduje. Řetězec, který má být kódovaný identifikátor URI.|  
|uriComponentToBinary|Vrátí že řetězec kódovaný binární reprezentace identifikátoru URI. Například následující výraz vrací binární reprezentace `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: řetězec<br /><br />**Popis**: vyžaduje. Řetězec kódovaný identifikátor URI.|  
|uriComponentToString|Vrátí že řetězec kódovaný řetězcová reprezentace identifikátoru URI. Například následující výraz vrací `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Parametr číslo**: 1<br /><br />**Název**: řetězec<br /><br />**Popis**: vyžaduje. Řetězec kódovaný identifikátor URI.|  
|xml|Vrátí reprezentaci xml hodnoty. Například následující výraz vrací obsahu xml reprezentována `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. Funkce xml podporuje také vstupu objekt JSON. Například parametr `{ "abc": "xyz" }` jsou převedeny na obsah xml `\<abc>xyz\</abc>`<br /><br /> **Parametr číslo**: 1<br /><br />**Název**: hodnota<br /><br />**Popis**: vyžaduje. Hodnota pro převod do formátu XML.|  
|výraz XPath|Vrátí pole z uzlů xml odpovídající výraz xpath hodnotu, která je výsledkem výrazu xpath.<br /><br />  **Příklad 1**<br /><br /> Předpokládejme, že hodnota parametru 'p1' je řetězcová reprezentace následující kód XML:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Tento kód: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> Vrátí<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> zatímco<br /><br /> 2. Tento kód: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> Vrátí<br /><br /> `13`<br /><br /> <br /><br /> **Příklad 2**<br /><br /> Zadané následující obsah XML:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Tento kód: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> nebo<br /><br /> 2. Tento kód: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> Vrátí<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> a<br /><br /> 3. Tento kód: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> Vrátí<br /><br /> ``bar``<br /><br /> **Parametr číslo**: 1<br /><br />**Název**: Xml<br /><br />**Popis**: vyžaduje. Soubor XML, na které při vyhodnocování výrazu XPath.<br /><br /> **Parametr číslo**: 2<br /><br />**Název**: XPath<br /><br />**Popis**: vyžaduje. Výraz XPath pro vyhodnocení.|  
|pole|Převeďte parametr na pole.  Například následující výraz vrací `["abc"]`: `array('abc')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: hodnota<br /><br /> **Popis**: vyžaduje. Hodnota, která je převedena na pole.|
|createArray|Vytvoří pole parametrů.  Například následující výraz vrací `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Parametr číslo**: 1... n<br /><br /> **Název**: všechny n<br /><br /> **Popis**: vyžaduje. Hodnoty k zkombinovat do pole.|

## <a name="math-functions"></a>Matematické funkce  
 Tyto funkce lze použít pro buď typy čísel: **celá čísla** a **jako plovoucí**.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|přidat|Vrátí výsledek přidání dvou čísel. Například funkce vrátí hodnotu `20.333`:  `add(10,10.333)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: Summand 1<br /><br /> **Popis**: vyžaduje. Číslo, které má přidat do **Summand 2**.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: Summand 2<br /><br /> **Popis**: vyžaduje. Číslo, které má přidat do **Summand 1**.|  
|Sub –|Vrátí výsledek odčítání dvou čísel. Například funkce vrátí hodnotu: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: Minuend<br /><br /> **Popis**: vyžaduje. Číslo, **Subtrahend** se odebere z.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: Subtrahend<br /><br /> **Popis**: vyžaduje. Číslo, které má odebrat z **Minuend**.|  
|mul|Vrátí výsledek násobení dvou čísel. Například následující vrátí `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: násobenec 1<br /><br /> **Popis**: vyžaduje. Číslo, které má násobení **násobenec 2** s.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: násobenec 2<br /><br /> **Popis**: vyžaduje. Číslo, které má násobení **násobenec 1** s.|  
|div|Vrátí výsledek dělení dvou čísel. Například následující vrátí `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: dělenec<br /><br /> **Popis**: vyžaduje. Číslo, které má dělit **dělitel**.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: dělitel<br /><br /> **Popis**: vyžaduje. Číslo, které má rozdělit **dělenec** podle.|  
|MOD|Vrátí výsledek zbytek po dělení dvou čísel (modulo). Například následující výraz vrací `2`:<br /><br /> `mod(10,4)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: dělenec<br /><br /> **Popis**: vyžaduje. Číslo, které má dělit **dělitel**.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: dělitel<br /><br /> **Popis**: vyžaduje. Číslo, které má rozdělit **dělenec** podle. Po rozdělení je zbývající zabraný.|  
|min|Existují dva různé vzorce pro volání této funkce: `min([0,1,2])` zde min přijímá pole. Tento výraz vrátí `0`. Alternativně můžete tuto funkci trvat seznam hodnot oddělených čárkami: `min(0,1,2)` tato funkce také vrátí hodnotu 0. Všimněte si, že se všechny hodnoty musí být číslo, takže pokud je parametr pole musí obsahovat pouze čísla v ní.<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: kolekce nebo hodnota<br /><br /> **Popis**: vyžaduje. Může to být pole hodnot najít minimální hodnotu, nebo první hodnotu sady.<br /><br /> **Parametr číslo**: 2... *n*<br /><br /> **Název**: hodnota *n*<br /><br /> **Popis**: volitelné. Pokud první parametr je hodnota, pak můžete předat další hodnoty a jsou vráceny minimum všechny předané hodnoty.|  
|max|Existují dva různé vzorce pro volání této funkce:  `max([0,1,2])`<br /><br /> Zde maximální přijímá pole. Tento výraz vrátí `2`. Alternativně můžete tuto funkci trvat seznam hodnot oddělených čárkami: `max(0,1,2)` funkce vrátí hodnotu 2. Všimněte si, že se všechny hodnoty musí být číslo, takže pokud je parametr pole musí obsahovat pouze čísla v ní.<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: kolekce nebo hodnota<br /><br /> **Popis**: vyžaduje. Může to být pole hodnot najít maximální hodnota, nebo první hodnotu sady.<br /><br /> **Parametr číslo**: 2... *n*<br /><br /> **Název**: hodnota *n*<br /><br /> **Popis**: volitelné. Pokud první parametr je hodnota, pak můžete předat další hodnoty a maximální počet všechny předané hodnoty jsou vráceny.|  
|rozsah| Generuje pole celá čísla od určité číslo, a definujte délka vrácený pole. Například funkce vrátí hodnotu `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: počáteční index<br /><br /> **Popis**: vyžaduje. Je první celé číslo v poli.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: počet<br /><br /> **Popis**: vyžaduje. Počet celých čísel, které jsou v poli.|  
|rand –| Generuje náhodné celé číslo v rámci zadaného rozsahu (včetně na obou koncích. Například to může vrátit `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: minimální<br /><br /> **Popis**: vyžaduje. Nejnižší celé číslo, kterým nemohl být vrácen.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: maximální<br /><br /> **Popis**: vyžaduje. Nejvyšší celé číslo, kterým nemohl být vrácen.|  
  
## <a name="date-functions"></a>Datové funkce  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|utcnow|Vrátí aktuální časové razítko jako řetězec. Například `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|přidat_sekundy|Přidá do časovým razítkem řetězec předaná celočíselný počet sekund. Počet sekund může být kladná nebo záporná. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Řetězec, který obsahuje čas.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: sekund<br /><br /> **Popis**: vyžaduje. Počet sekund, které chcete přidat. Může být záporná má odečíst sekund.<br /><br /> **Parametr číslo**: 3<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|addminutes|Přidá do časovým razítkem řetězec předaná celočíselný počet minut. Počet minut, může být kladná nebo záporná. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Řetězec, který obsahuje čas.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: minut<br /><br /> **Popis**: vyžaduje. Počet minut, které chcete přidat. Může být záporná má odečíst minut.<br /><br /> **Parametr číslo**: 3<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|addhours|Přidá do časovým razítkem řetězec předaná celočíselný počet hodin. Počet hodin, může být kladná nebo záporná. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Řetězec, který obsahuje čas.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: čas<br /><br /> **Popis**: vyžaduje. Počet hodin na Přidat. Může být záporná má odečíst hodin.<br /><br /> **Parametr číslo**: 3<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|přidat_dny|Přidá do časovým razítkem řetězec předaná celočíselný počet dnů. Počet dní, může být kladná nebo záporná. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Řetězec, který obsahuje čas.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: počet dnů<br /><br /> **Popis**: vyžaduje. Počet dní, které chcete přidat. Může být záporná má odečíst dnů.<br /><br /> **Parametr číslo**: 3<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|FormatDateTime|Vrací řetězec ve formátu data. Výsledkem je řetězec ve formátu ISO 8601 ("o") ve výchozím nastavení, pokud je k dispozici specifikátor formátu. Například `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: datum<br /><br /> **Popis**: vyžaduje. Řetězec, který obsahuje data.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  

## <a name="next-steps"></a>Další postup
Seznam můžete použít ve výrazech proměnných systému najdete v tématu [systémové proměnné](control-flow-system-variables.md).

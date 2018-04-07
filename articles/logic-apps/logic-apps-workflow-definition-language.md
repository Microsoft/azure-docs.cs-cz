---
title: Pracovní postup schema Definition Language - Azure Logic Apps | Microsoft Docs
description: Definovat pracovní postupy založené na schéma definice pracovního postupu pro Azure Logic Apps
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 42932e6d1727a1444c62f565ae3c48dc178aeb2b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Schema Definition Language pracovního postupu pro Azure Logic Apps

Definice pracovního postupu obsahuje skutečné logiky, která spouští jako součást aplikace logiky. Tato definice obsahuje jeden nebo více aktivačních událostí, které začínají aplikaci logiky a jednu nebo více akcí pro aplikaci logiky pořizovat.  
  
## <a name="basic-workflow-definition-structure"></a>Základní pracovní postup definice struktury

Tady je základní strukturu definice pracovního postupu:  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> [Rozhraní API REST správy pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows) dokumentace obsahuje informace o tom, jak vytvořit a spravovat pracovní postupy aplikace logiky.
  
|Název elementu|Požaduje se|Popis|  
|------------------|--------------|-----------------|  
|$schema|Ne|Určuje umístění pro soubor schématu JSON, který popisuje verzi definice jazyka. Toto umístění je požadováno, když odkazujete definici externě. Tady je umístění na tomto dokumentu: <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`|  
|contentVersion|Ne|Určuje verzi definice. Když nasadíte pomocí definice pracovního postupu, můžete tuto hodnotu a ujistěte se, že se používá definici správné.|  
|parameters|Ne|Určuje parametry použité jako vstup dat na definici. Může být definováno maximálně 50 parametry.|  
|Aktivační události|Ne|Určuje informace o aktivační události, které zahájí pracovního postupu. Může být definováno maximálně 10 aktivační události.|  
|Akce|Ne|Určuje akce, které se provádějí jako provede toku. Maximálně 250 akce může být definováno.|  
|výstupy|Ne|Určuje informace o nasazené prostředku. Může být definováno maximálně 10 výstupy.|  
  
## <a name="parameters"></a>Parametry

V této části určuje všechny parametry, které se používají v definici pracovního postupu v době nasazení. Všechny parametry musí být deklarován v této části, před použitím v dalších částech definici.  
  
Následující příklad ukazuje definici parametru strukturu:  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|Název elementu|Požaduje se|Popis|  
|------------------|--------------|-----------------|  
|type|Ano|**Typ**: řetězec <p> **Deklarace**: `"parameters": {"parameter1": {"type": "string"}}` <p> **Specifikace**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Typ**: securestring <p> **Deklarace**: `"parameters": {"parameter1": {"type": "securestring"}}` <p> **Specifikace**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **Typ**: int <p> **Deklarace**: `"parameters": {"parameter1": {"type": "int"}}` <p> **Specifikace**: `"parameters": {"parameter1": {"value" : 5}}` <p> **Typ**: bool <p> **Deklarace**: `"parameters": {"parameter1": {"type": "bool"}}` <p> **Specifikace**: `"parameters": {"parameter1": { "value": true }}` <p> **Typ**: pole <p> **Deklarace**: `"parameters": {"parameter1": {"type": "array"}}` <p> **Specifikace**: `"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **Typ**: objekt <p> **Deklarace**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Specifikace**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Typ**: secureobject <p> **Deklarace**: `"parameters": {"parameter1": {"type": "object"}}` <p> **Specifikace**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **Poznámka:** `securestring` a `secureobject` typy nebudou zobrazeny v `GET` operace. Tento typ měli používat hesla, klíče a tajné klíče.|  
|Výchozí hodnota|Ne|Určuje výchozí hodnota pro parametr, pokud není zadaná žádná hodnota v době, kdy byl vytvořený.|  
|allowedValues|Ne|Určuje pole povolených hodnot pro parametr.|  
|zprostředkovatele identity|Ne|Určuje další informace o parametru, jako je čitelný popis nebo návrhu dat používané v sadě Visual Studio nebo jiných nástrojů.|  
  
Tento příklad ukazuje, jak můžete použít parametr v části textu, akce:  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 Parametry můžete použít také v výstupy.  
  
## <a name="triggers-and-actions"></a>Aktivační události a akce  

Triggery a akce zadejte volání, které se můžou zapojit při provádění pracovního postupu. Podrobnosti o této části najdete v tématu [akce pracovního postupu a aktivační události](logic-apps-workflow-actions-triggers.md).
  
## <a name="outputs"></a>Výstupy  

Výstupy zadejte informace, které mohou být vráceny z pracovního postupu spustit. Například pokud máte konkrétní stav nebo hodnotu, která chcete sledovat při každém spuštění, můžete zahrnout data v běhu výstupy. Data se zobrazí v REST API pro správu pro tohoto spustit a správu uživatelského rozhraní pro kterých běží na portálu Azure. Tyto výstupy do jiných externími systémy jako PowerBI může obtékat také pro vytváření řídicích panelů. Výstupy jsou *není* použít reagovat na příchozí požadavky na rozhraní API služby REST. Reagovat na příchozí žádosti o pomocí `response` typ akce, tady je příklad:
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|Název elementu|Požaduje se|Popis|  
|------------------|--------------|-----------------|  
|key1|Ano|Určuje identifikátor klíče pro výstup. Nahraďte **key1** s názvem, který chcete použít k identifikaci výstup.|  
|hodnota|Ano|Určuje hodnotu výstupu.|  
|type|Ano|Určuje typ pro hodnotu, která byla zadána. Možné typy hodnoty jsou: <ul><li>`string`</li><li>`securestring`</li><li>`int`</li><li>`bool`</li><li>`array`</li><li>`object`</li></ul>|
  
## <a name="expressions"></a>Výrazy  

Hodnoty JSON v definici mohou být literál nebo můžou být výrazů, které se vyhodnocují při definici. Příklad:  
  
```json
"name": "value"
```

 nebo  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> Některé výrazy získat své hodnoty z modulu runtime akce, které nemusí existovat na začátku spuštění. Můžete použít **funkce** pomohou načíst některé z těchto hodnot.  
  
Výrazy může vyskytovat kdekoli v hodnotu řetězce formátu JSON a vždy mít za následek jinou hodnotu JSON. Je-li být výraz byl vyhodnocen hodnotu formátu JSON, těle výrazu je extrahovat odebráním znaku @ (@). Pokud je potřeba řetězcový literál, který začíná @, že řetězec, je nutné uvést pomocí@. Následující příklady ukazují, jak se vyhodnocují výrazy.  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|"parametry"|Znaky, parametry, jsou vráceny.|  
|"parametry [1]"|Jsou vráceny znaky 'parametry [1]'.|  
|"@@"|Řetězec 1 znak, který obsahuje ' @' je vrácen.|  
|" @"|Řetězec 2 znak, který obsahuje ' @' je vrácen.|  
  
S *řetězec interpolace*, výrazy se může zobrazit i uvnitř řetězců, kde jsou výrazy uzavřen do `@{ ... }`. Příklad: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName')}"`

Výsledkem je vždy řetězec, takže tato funkce je podobná `concat` funkce. Předpokládejme, že jste definovali `myNumber` jako `42` a `myString` jako `sampleString`:  
  
|Hodnota JSON|Výsledek|  
|----------------|------------|  
|"@parameters('myString')"|Vrátí `sampleString` jako řetězec.|  
|"@{parameters('myString')}"|Vrátí `sampleString` jako řetězec.|  
|"@parameters('myNumber')"|Vrátí `42` jako *číslo*.|  
|"@{parameters('myNumber')}"|Vrátí `42` jako *řetězec*.|  
|"Odpověď je: @{parameters('myNumber')}"|Vrátí řetězec `Answer is: 42`.|  
|"@concat(' Odpověď je: ', string(parameters('myNumber')))"|Vrátí řetězec `Answer is: 42`|  
|"Odpověď je: @@ {parameters('myNumber')}"|Vrátí řetězec `Answer is: @{parameters('myNumber')}`.|  
  
## <a name="operators"></a>Operátory  

Operátory jsou znaky, které můžete použít ve výrazech nebo funkce. 
  
|Operátor|Popis|  
|--------------|-----------------|  
|.|Operátor tečky umožňuje odkazovat vlastnosti objektu|  
|?|Operátor otazník umožňuje hodnotu null vlastnosti objektu bezchybně runtime odkazovat. Například můžete použít tento výraz pro zpracování výstupy null aktivační události: <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|Jednoduché uvozovky je jediný způsob, jak zabalit textové literály. Dvojité uvozovky uvnitř výrazy nelze použít, protože tohoto rozdělení je v konfliktu s uvozovky JSON, který zabalí celý výraz.|  
|[]|Hranaté závorky lze použít k získání hodnoty z pole s indexem konkrétní. Například, pokud máte akci, která předá `range(0,10)`v do `forEach` funkce, tuto funkci můžete použít k získání položky z pole:  <p>`myArray[item()]`|  
  
## <a name="functions"></a>Funkce  

Také můžete volat funkce v rámci výrazy. Následující tabulka uvádí funkce, které můžete použít ve výrazu.  
  
|Výraz|Vyhodnocení|  
|----------------|----------------|  
|"@function("Hello")"|Volání funkce členem definici pomocí řetězcového literálu Hello jako první parametr.|  
|"@function(" Jej '' nástrojů s!') "|Volání funkce členem definici pomocí řetězcového literálu, je nástrojů!. jako první parametr|  
|"@function().prop1"|Vrátí hodnotu vlastnosti vlastnost1 `myfunction` členem definici.|  
|"@function('Hello').prop1"|Volání funkce členem definici pomocí řetězcového literálu "Hello" jako první parametr a vrátí vlastnost1 vlastnost objektu.|  
|"@function(parameters('Hello'))"|Vyhodnotí parametr Hello a předá hodnotu funkce|  
  
### <a name="referencing-functions"></a>Odkazování na funkce  

Tyto funkce slouží k odkazování výstupy z jiné akce v aplikaci logiky nebo hodnoty předané při vytvoření aplikace logiky. Například můžete odkazovat data z jednoho kroku používat v jiném.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|parameters|Vrátí hodnotu parametru, která je definována v definici. <p>`parameters('password')` <p> **Parametr číslo**: 1 <p> **Název**: parametr <p> **Popis**: vyžaduje. Název parametru, jejichž hodnoty se mají.|  
|akce|Umožňuje výrazu odvození svou hodnotu z jiných název JSON a páry hodnota nebo z výstupu aktuální akce. modul runtime. Vlastnost reprezentována propertyPath v následujícím příkladu je nepovinná. Pokud není zadán propertyPath, je odkaz na objekt celé akce. Tuto funkci lze použít pouze uvnitř proveďte – dokud podmínky akce. <p>`action().outputs.body.propertyPath`|  
|Akce|Umožňuje výrazu odvození svou hodnotu z jiných název JSON a páry hodnota nebo z výstupu akce modulu runtime. Tyto výrazy explicitně deklarovat, že jedna akce závisí na jiné akce. Vlastnost reprezentována propertyPath v následujícím příkladu je nepovinná. Pokud není zadán propertyPath, je odkaz na objekt celé akce. Tento element nebo element podmínky můžete použít k určení závislostí, ale není potřeba použít pro stejné závislý prostředek. <p>`actions('myAction').outputs.body.propertyPath` <p> **Parametr číslo**: 1 <p> **Název**: název akce <p> **Popis**: vyžaduje. Název akce, jejichž hodnoty se mají. <p> Jsou k dispozici vlastnosti objektu akce: <ul><li>`name`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Najdete v článku [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850646) podrobnosti o tyto vlastnosti.|
|Aktivační události|Umožňuje výrazu odvození svou hodnotu z jiných JSON název a hodnotu páry nebo z výstupu aktivační události modulu runtime. Vlastnost reprezentována propertyPath v následujícím příkladu je nepovinná. Pokud není zadán propertyPath, odkaz je celý aktivační události objektu. <p>`trigger().outputs.body.propertyPath` <p>Při použití uvnitř vstupy aktivační události, funkce vrátí výstupy předchozího spuštění. Nicméně, je-li použít uvnitř aktivační podmínku, `trigger` funkce vrátí výstupy aktuální spuštění. <p> Jsou k dispozici vlastnosti aktivační události objektu: <ul><li>`name`</li><li>`scheduledTime`</li><li>`startTime`</li><li>`endTime`</li><li>`inputs`</li><li>`outputs`</li><li>`status`</li><li>`code`</li><li>`trackingId`</li><li>`clientTrackingId`</li></ul> <p>Najdete v článku [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=850644) podrobnosti o tyto vlastnosti.|
|actionOutputs|Tato funkce je sdružená vlastnost `actions('actionName').outputs` <p> **Parametr číslo**: 1 <p> **Název**: název akce <p> **Popis**: vyžaduje. Název akce, jejichž hodnoty se mají.|  
|actionBody a text|Tyto funkce jsou sdružená vlastnost `actions('actionName').outputs.body` <p> **Parametr číslo**: 1 <p> **Název**: název akce <p> **Popis**: vyžaduje. Název akce, jejichž hodnoty se mají.|  
|triggerOutputs|Tato funkce je sdružená vlastnost `trigger().outputs`|  
|triggerBody|Tato funkce je sdružená vlastnost `trigger().outputs.body`|  
|Položka|Při použití uvnitř opakující se akce, funkce vrátí hodnotu položku, která je v poli pro tento iterace akce. Pokud máte akci, která spouští pro každou položku pole zpráv, můžete například použijte následující syntaxi: <p>`"input1" : "@item().subject"`| 
  
### <a name="collection-functions"></a>Kolekce funkcí  

Tyto funkce pracovat s kolekcí a obecně platí pro pole řetězce a někdy slovník.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|obsahuje|Vrátí hodnotu PRAVDA, pokud slovník obsahuje seznam klíčů, obsahuje hodnotu nebo řetězec obsahuje dílčí řetězec. Například funkce vrátí hodnotu `true`: <p>`contains('abacaba','aca')` <p> **Parametr číslo**: 1 <p> **Název**: v rámci kolekce <p> **Popis**: vyžaduje. Kolekce se hledat v. <p> **Parametr číslo**: 2 <p> **Název**: najít objekt <p> **Popis**: vyžaduje. Objekt, který chcete najít uvnitř **v rámci kolekce**.|  
|Délka|Vrátí počet prvků v pole nebo řetězec. Například funkce vrátí hodnotu `3`:  <p>`length('abc')` <p> **Parametr číslo**: 1 <p> **Název**: kolekce <p> **Popis**: vyžaduje. Kolekce, pro které chcete získat délku.|  
|prázdný|Vrátí hodnotu true Pokud objekt, pole nebo řetězec je prázdný. Například funkce vrátí hodnotu `true`: <p>`empty('')` <p> **Parametr číslo**: 1 <p> **Název**: kolekce <p> **Popis**: vyžaduje. Kolekce ke kontrole, jestli je prázdný.|  
|průnik|Vrací jednu pole nebo objekt, který má společné prvky mezi pole nebo objekty předaná. Například funkce vrátí hodnotu `[1, 2]`: <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>Parametry pro funkci může být buď sadu objektů, nebo sadu pole (ne kombinaci těchto dvou možností). Existují dva objekty se stejným názvem, zobrazí se v poslední objekt poslední objekt se stejným názvem. <p> **Parametr číslo**: 1... *n* <p> **Název**: kolekce *n* <p> **Popis**: vyžaduje. Kolekce k vyhodnocení. Objekt musí být ve všech kolekcích předaná se objeví ve výsledku.|  
|sjednocení|Vrátí objekt s všechny prvky, které jsou v pole nebo objekt předaný této funkce nebo jednoho pole. Například funkce vrátí hodnotu `[1, 2, 3, 10, 101]`: <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>Parametry pro funkci může být buď sadu objektů, nebo sadu pole (není jejich směs). Pokud jsou dva objekty se stejným názvem v finální výstup, se zobrazí v posledním objekt poslední objekt se stejným názvem. <p> **Parametr číslo**: 1... *n* <p> **Název**: kolekce *n* <p> **Popis**: vyžaduje. Kolekce k vyhodnocení. Objekt, který se zobrazí v některém z kolekce se zobrazí ve výsledku.|  
|první|Vrátí první prvek pole nebo předaný řetězec. Například funkce vrátí hodnotu `0`: <p>`first([0,2,3])` <p> **Parametr číslo**: 1 <p> **Název**: kolekce <p> **Popis**: vyžaduje. Provést první objekt z kolekce.|  
|poslední|Vrátí poslední prvek v poli nebo předaný řetězec. Například funkce vrátí hodnotu `3`: <p>`last('0123')` <p> **Parametr číslo**: 1 <p> **Název**: kolekce <p> **Popis**: vyžaduje. Provést poslední objekt z kolekce.|  
|proveďte|Vrátí první **počet** předaná elementy z pole nebo řetězec. Například funkce vrátí hodnotu `[1, 2]`:  <p>`take([1, 2, 3, 4], 2)` <p> **Parametr číslo**: 1 <p> **Název**: kolekce <p> **Popis**: vyžaduje. Kolekce odkud provést první **počet** objekty. <p> **Parametr číslo**: 2 <p> **Název**: počet <p> **Popis**: vyžaduje. Počet objektů, které chcete od **kolekce**. Musí být kladné celé číslo.|  
|Přeskočit|Vrátí elementy v poli začínající na indexu **počet**. Například funkce vrátí hodnotu `[3, 4]`: <p>`skip([1, 2 ,3 ,4], 2)` <p> **Parametr číslo**: 1 <p> **Název**: kolekce <p> **Popis**: vyžaduje. Kolekce tak, aby přeskočil první **počet** objekty z. <p> **Parametr číslo**: 2 <p> **Název**: počet <p> **Popis**: vyžaduje. Počet objektů, které chcete odebrat z před **kolekce**. Musí být kladné celé číslo.|  
|join|Vrátí řetězec s každou položkou pole spojena oddělovač, například tento příkaz vrátí `"1,2,3,4"`:<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: kolekce<br /><br /> **Popis**: vyžaduje. Pro připojení položky z kolekce.<br /><br /> **Parametr číslo**: 2<br /><br /> **Název**: oddělovač<br /><br /> **Popis**: vyžaduje. Řetězec pro vymezení položky.|  
  
### <a name="string-functions"></a>Řetězcové funkce

Následující funkce se vztahuje pouze na řetězce. Můžete taky některé funkce kolekce řetězce.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|concat|Kombinuje libovolný počet řetězce společně. Například, pokud je parametr 1 `p1`, funkce vrátí hodnotu `somevalue-p1-somevalue`: <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **Parametr číslo**: 1... *n* <p> **Název**: řetězec *n* <p> **Popis**: vyžaduje. Řetězce se zkombinovat do jednoho řetězce.|  
|dílčí řetězec|Vrátí podmnožinu znaků z řetězce. Například funkce vrátí hodnotu `abc`: <p>`substring('somevalue-abc-somevalue',10,3)` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, ze kterého je provedena dílčí řetězec. <p> **Parametr číslo**: 2 <p> **Název**: počáteční index <p> **Popis**: vyžaduje. Index kde dílčí řetězec začíná v parametru 1. <p> **Parametr číslo**: 3 <p> **Název**: délka <p> **Popis**: vyžaduje. Délka dílčí řetězec.|  
|Nahradit|Nahradí řetězec daný řetězec. Například funkce vrátí hodnotu `the new string`: <p>`replace('the old string', 'old', 'new')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, který je hledán parametr 2 a aktualizovat s parametrem 3, když v parametru 1 je nalezen parametr 2. <p> **Parametr číslo**: 2 <p> **Název**: původní řetězec <p> **Popis**: vyžaduje. Řetězec, který má nahradit parametr 3, pokud je nalezena shoda v parametru 1 <p> **Parametr číslo**: 3 <p> **Název**: nový řetězec <p> **Popis**: vyžaduje. Řetězec, který se používá k nahrazení řetězec v parametru 2, pokud je nalezena shoda v parametru 1.|  
|Identifikátor GUID|Tato funkce generuje řetězec globálně jedinečný (identifikátor GUID). Tuto funkci můžete například vygenerovat tento identifikátor GUID: `c2ecc88d-88c8-4096-912c-d6f2e2b138ce` <p>`guid()` <p> **Parametr číslo**: 1 <p> **Název**: formát <p> **Popis**: volitelné. Specifikátor jednoho formátu, který označuje [způsob formátování hodnotu identifikátoru Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Formát parametru může být "N", "D", "B", "P" nebo "X". Pokud formát není zadaný, použije se "D".|  
|toLower|Převede řetězec na malá písmena. Například funkce vrátí hodnotu `two by two is four`: <p>`toLower('Two by Two is Four')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec převést na nižší velká a malá písmena. Znak v řetězci nemá malá ekvivalentní, znak je zahrnuta v vrácený řetězec beze změny.|  
|toUpper|Převede řetězec na velká písmena. Například funkce vrátí hodnotu `TWO BY TWO IS FOUR`: <p>`toUpper('Two by Two is Four')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec převést na horní velká a malá písmena. Znak v řetězci nemá velká ekvivalentní, znak je zahrnuta v vrácený řetězec beze změny.|  
|indexof|Index hodnoty v případě řetězec insensitively najít. Například funkce vrátí hodnotu `7`: <p>`indexof('hello, world.', 'world')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, který může obsahovat hodnotu. <p> **Parametr číslo**: 2 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Hodnota k vyhledání index.|  
|lastindexof|Najít poslední index na hodnotu v případě řetězec insensitively. Například funkce vrátí hodnotu `3`: <p>`lastindexof('foofoo', 'foo')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, který může obsahovat hodnotu. <p> **Parametr číslo**: 2 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Hodnota k vyhledání index.|  
|StartsWith|Kontroluje, zda řetězec začíná případu hodnotu insensitively. Například funkce vrátí hodnotu `true`: <p>`startswith('hello, world', 'hello')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, který může obsahovat hodnotu. <p> **Parametr číslo**: 2 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Hodnota řetězce může začínat.|  
|EndsWith|Kontroluje, zda řetězec končí případem hodnotu insensitively. Například funkce vrátí hodnotu `true`: <p>`endswith('hello, world', 'world')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, který může obsahovat hodnotu. <p> **Parametr číslo**: 2 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Hodnota řetězce mohou končit.|  
|split|Rozdělí řetězec pomocí oddělovače. Například funkce vrátí hodnotu `["a", "b", "c"]`: <p>`split('a;b;c',';')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, který je rozdělení. <p> **Parametr číslo**: 2 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Oddělovač.|  
  
### <a name="logical-functions"></a>Logické funkce  

Tyto funkce jsou užitečné v podmínkách a slouží k vyhodnocení libovolného typu logiku.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|rovná se|Pokud vrátí hodnotu true jsou dvě hodnoty rovny. Například pokud parameter1 someValue, funkce vrátí hodnotu `true`: <p>`equals(parameters('parameter1'), 'someValue')` <p> **Parametr číslo**: 1 <p> **Název**: objektu 1 <p> **Popis**: vyžaduje. Objekt k porovnání s **objekt 2**. <p> **Parametr číslo**: 2 <p> **Název**: objekt 2 <p> **Popis**: vyžaduje. Objekt k porovnání s **objektu 1**.|  
|menší|Vrátí hodnotu PRAVDA, pokud první argument je menší než druhý. Všimněte si, může být pouze hodnoty typu integer, float nebo řetězec. Například funkce vrátí hodnotu `true`: <p>`less(10,100)` <p> **Parametr číslo**: 1 <p> **Název**: objektu 1 <p> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je menší než **objekt 2**. <p> **Parametr číslo**: 2 <p> **Název**: objekt 2 <p> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je větší než **objektu 1**.|  
|lessOrEquals|Vrátí hodnotu true Pokud první argument je menší než nebo rovna hodnotě druhý. Všimněte si, může být pouze hodnoty typu integer, float nebo řetězec. Například funkce vrátí hodnotu `true`: <p>`lessOrEquals(10,10)` <p> **Parametr číslo**: 1 <p> **Název**: objektu 1 <p> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je menší nebo rovno **objekt 2**. <p> **Parametr číslo**: 2 <p> **Název**: objekt 2 <p> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je větší než nebo rovna hodnotě **objektu 1**.|  
|větší|Vrátí hodnotu true Pokud první argument je větší než druhý. Všimněte si, může být pouze hodnoty typu integer, float nebo řetězec. Například funkce vrátí hodnotu `false`:  <p>`greater(10,10)` <p> **Parametr číslo**: 1 <p> **Název**: objektu 1 <p> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je větší než **objekt 2**. <p> **Parametr číslo**: 2 <p> **Název**: objekt 2 <p> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je menší než **objektu 1**.|  
|greaterOrEquals|První argument větší než nebo rovna hodnotě druhý vrátí hodnotu PRAVDA. Všimněte si, může být pouze hodnoty typu integer, float nebo řetězec. Například funkce vrátí hodnotu `false`: <p>`greaterOrEquals(10,100)` <p> **Parametr číslo**: 1 <p> **Název**: objektu 1 <p> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je větší než nebo rovna hodnotě **objekt 2**. <p> **Parametr číslo**: 2 <p> **Název**: objekt 2 <p> **Popis**: vyžaduje. Objekt, který chcete zkontrolovat, zda je menší než nebo rovna hodnotě **objektu 1**.|  
|a|Vrátí hodnotu true, pokud oba parametry mají hodnotu true. Oba argumenty musí být logické hodnoty. Například funkce vrátí hodnotu `false`: <p>`and(greater(1,10),equals(0,0))` <p> **Parametr číslo**: 1 <p> **Název**: logická hodnota 1 <p> **Popis**: vyžaduje. První argument, který musí být `true`. <p> **Parametr číslo**: 2 <p> **Název**: logická hodnota 2 <p> **Popis**: vyžaduje. Musí být druhým argumentem `true`.|  
|nebo|Hodnota true, pokud buď parametr vrátí hodnotu true. Oba argumenty musí být logické hodnoty. Například funkce vrátí hodnotu `true`: <p>`or(greater(1,10),equals(0,0))` <p> **Parametr číslo**: 1 <p> **Název**: logická hodnota 1 <p> **Popis**: vyžaduje. První argument, který může být `true`. <p> **Parametr číslo**: 2 <p> **Název**: logická hodnota 2 <p> **Popis**: vyžaduje. Může být druhým argumentem `true`.|  
|není|Vrátí hodnotu PRAVDA, pokud jsou parametry `false`. Oba argumenty musí být logické hodnoty. Například funkce vrátí hodnotu `true`: <p>`not(contains('200 Success','Fail'))` <p> **Parametr číslo**: 1 <p> **Název**: logická hodnota <p> **Popis**: vrátí hodnotu true, pokud jsou parametry `false`. Oba argumenty musí být logické hodnoty. Funkce vrátí hodnotu `true`:  `not(contains('200 Success','Fail'))`|  
|if|Vrátí hodnotu zadaného v závislosti na tom, zda výraz výsledkem `true` nebo `false`.  Například funkce vrátí hodnotu `"yes"`: <p>`if(equals(1, 1), 'yes', 'no')` <p> **Parametr číslo**: 1 <p> **Název**: výraz <p> **Popis**: vyžaduje. Logická hodnota, která určuje hodnotu, která by měla vrátit výraz. <p> **Parametr číslo**: 2 <p> **Název**: True <p> **Popis**: vyžaduje. Hodnota, která má být vrácena, pokud je výraz `true`. <p> **Parametr číslo**: 3 <p> **Název**: False <p> **Popis**: vyžaduje. Hodnota, která má být vrácena, pokud je výraz `false`.|  
  
### <a name="conversion-functions"></a>Převodní funkce  

Pro převod mezi jednotlivé nativní typy v jazyce, se používají tyto funkce:  
  
- řetězec  
  
- celé číslo  
  
- Plovoucí desetinná čárka  
  
- Boolean  
  
- Pole  
  
- slovník  

-   Formuláře  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|celá čísla|Parametr převeďte na celé číslo. Například funkce vrátí hodnotu 100 jako číslo, nikoli řetězec: <p>`int('100')` <p> **Parametr číslo**: 1 <p> **Název**: hodnota <p> **Popis**: vyžaduje. Hodnota, která je převést na celé číslo.|  
|řetězec|Převeďte parametr na řetězec. Například funkce vrátí hodnotu `'10'`: <p>`string(10)` <p>Také můžete převést objekt na řetězec. Například pokud `myPar` parametr je objekt s jednu vlastnost `abc : xyz`, potom funkce vrátí hodnotu `{"abc" : "xyz"}`: <p>`string(parameters('myPar'))` <p> **Parametr číslo**: 1 <p> **Název**: hodnota <p> **Popis**: vyžaduje. Hodnota, která je převedeno na řetězec.|  
|JSON|Převést parametr na hodnotu typu JSON a je opakem `string()`. Například funkce vrátí hodnotu `[1,2,3]` jako pole, nikoli řetězec: <p>`json('[1,2,3]')` <p>Podobně můžete převést řetězec na objekt. Například funkce vrátí hodnotu `{ "abc" : "xyz" }`: <p>`json('{"abc" : "xyz"}')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, který je převést na hodnotu nativním typu. <p>`json()` Funkce podporuje příliš vstup XML. Například hodnota parametru: <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>je převést na tento formát JSON: <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|Plovoucí desetinná čárka|Argument parametr převeďte na číslo s plovoucí desetinnou čárkou. Například funkce vrátí hodnotu `10.333`: <p>`float('10.333')` <p> **Parametr číslo**: 1 <p> **Název**: hodnota <p> **Popis**: vyžaduje. Hodnota, která je převedena na číslo s plovoucí desetinnou čárkou.|  
|BOOL|Převeďte parametr na booleovskou hodnotu. Například funkce vrátí hodnotu `false`: <p>`bool(0)` <p> **Parametr číslo**: 1 <p> **Název**: hodnota <p> **Popis**: vyžaduje. Hodnota, která je převést na logickou hodnotu.|  
|base64|Vrátí reprezentaci base64 vstupní řetězec. Například funkce vrátí hodnotu `c29tZSBzdHJpbmc=`: <p>`base64('some string')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec 1 <p> **Popis**: vyžaduje. Řetězec určený ke kódování do reprezentace base64.|  
|base64ToBinary|Vrátí binární reprezentace řetězec s kódováním base64. Například funkce vrátí hodnotu binární reprezentace `some string`: <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec s kódováním base64.|  
|base64ToString|Vrátí řetězcovou reprezentaci based64 kódovaný řetězec. Například funkce vrátí hodnotu `some string`: <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec s kódováním base64.|  
|Binární hodnota|Vrátí binární reprezentace hodnoty.  Například funkce vrátí hodnotu binární reprezentace `some string`: <p>`binary('some string')` <p> **Parametr číslo**: 1 <p> **Název**: hodnota <p> **Popis**: vyžaduje. Hodnota, která je převést na binární formát.|  
|dataUriToBinary|Vrátí binární reprezentace dat identifikátor URI. Například funkce vrátí hodnotu binární reprezentace `some string`: <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Data URI převést na binární reprezentace.|  
|dataUriToString|Vrátí řetězcovou reprezentaci dat identifikátor URI. Například funkce vrátí hodnotu `some string`: <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec<p> **Popis**: vyžaduje. Identifikátor URI převést řetězcovou reprezentaci dat.|  
|dataUri|Vrací data URI hodnoty. Například funkce vrátí hodnotu tento identifikátor URI dat. `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=`: <p>`dataUri('some string')` <p> **Parametr číslo**: 1<p> **Název**: hodnota<p> **Popis**: vyžaduje. Hodnota převést na identifikátor URI dat.|  
|decodeBase64|Vrátí řetězcovou reprezentaci řetězce vstupní based64. Například funkce vrátí hodnotu `some string`: <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vrátí řetězcovou reprezentaci řetězce vstupní based64.|  
|encodeuricomponent –|URL – řídicí sekvence řetězec, který se předává v. Například funkce vrátí hodnotu `You+Are%3ACool%2FAwesome`: <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, abyste se vyhnuli URL unsafe znaky z.|  
|decodeuricomponent –|Zrušení – adresa URL – řídicí sekvence řetězec, který se předává v. Například funkce vrátí hodnotu `You Are:Cool/Awesome`: <p>`decodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec k dekódování URL unsafe znaků z.|  
|decodeDataUri|Vrátí binární reprezentace vstupní data řetězce URI. Například funkce vrátí hodnotu binární reprezentace `some string`: <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec <p> **Popis**: vyžaduje. DataURI dekódování do binární reprezentace.|  
|uriComponent|Vrátí identifikátor URI kódovaný reprezentaci hodnoty. Například funkce vrátí hodnotu `You+Are%3ACool%2FAwesome`: <p>`uriComponent('You Are:Cool/Awesome')` <p> **Parametr číslo**: 1<p> **Název**: řetězec <p> **Popis**: vyžaduje. Řetězec, který má být kódovaný identifikátor URI.|  
|uriComponentToBinary|Vrátí že řetězec kódovaný binární reprezentace identifikátoru URI. Například funkce vrátí hodnotu binární reprezentace `You Are:Cool/Awesome`: <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **Parametr číslo**: 1 <p> **Název**: řetězec<p> **Popis**: vyžaduje. Řetězec kódovaný identifikátor URI.|  
|uriComponentToString|Vrátí že řetězec kódovaný řetězcová reprezentace identifikátoru URI. Například funkce vrátí hodnotu `You Are:Cool/Awesome`: <p>`uriComponentToString('You+Are%3ACool%2FAwesome')` <p> **Parametr číslo**: 1<p> **Název**: řetězec<p> **Popis**: vyžaduje. Řetězec kódovaný identifikátor URI.|  
|xml|Vrátí reprezentaci XML hodnoty. Například funkce vrátí hodnotu XML obsahu reprezentována `'\<name>Alan\</name>'`: <p>`xml('\<name>Alan\</name>')` <p>`xml()` Funkce podporuje vstup příliš objekt JSON. Například parametr `{ "abc": "xyz" }` jsou převedeny na obsah XML: `\<abc>xyz\</abc>` <p> **Parametr číslo**: 1<p> **Název**: hodnota<p> **Popis**: vyžaduje. Hodnota pro převod do formátu XML.|  
|pole|Převeďte parametr na pole. Například funkce vrátí hodnotu `["abc"]`: <p>`array('abc')` <p> **Parametr číslo**: 1 <p> **Název**: hodnota <p> **Popis**: vyžaduje. Hodnota, která je převedena na pole.|
|createArray|Vytvoří pole parametrů. Například funkce vrátí hodnotu `["a", "c"]`: <p>`createArray('a', 'c')` <p> **Parametr číslo**: 1... *n* <p> **Název**: všechny *n* <p> **Popis**: vyžaduje. Hodnoty k zkombinovat do pole.|
|triggerFormDataValue|Vrátí jednu hodnotu odpovídající název klíče z dat formuláře nebo výstupní formulářem kódované aktivační události.  Pokud máte více odpovídá se bude chyba.  Například následující vrátí `bar`: `triggerFormDataValue('foo')`<br /><br />**Parametr číslo**: 1<br /><br />**Název**: název klíče<br /><br />**Popis**: vyžaduje. Název klíče vrátit hodnotu dat formuláře.|
|triggerFormDataMultiValues|Vrátí pole hodnot odpovídající název klíče z dat formuláře nebo výstupní formulářem kódované aktivační události.  Například následující vrátí `["bar"]`: `triggerFormDataValue('foo')`<br /><br />**Parametr číslo**: 1<br /><br />**Název**: název klíče<br /><br />**Popis**: vyžaduje. Název klíče datových hodnot formuláře vrátit.|
|triggerMultipartBody|Vrátí pro část textu ve vícedílné zprávy výstupu aktivační události.<br /><br />**Parametr číslo**: 1<br /><br />**Název**: Index<br /><br />**Popis**: vyžaduje. Index rámci k načtení.|
|formDataValue|Vrátí jednu hodnotu odpovídající název klíče z dat formuláře nebo výstup formulářem kódované akce.  Pokud máte více odpovídá se bude chyba.  Například následující vrátí `bar`: `formDataValue('someAction', 'foo')`<br /><br />**Parametr číslo**: 1<br /><br />**Název**: název akce<br /><br />**Popis**: vyžaduje. Název akce s dat formuláře nebo kódovaná formuláře odpovědi.<br /><br />**Parametr číslo**: 2<br /><br />**Název**: název klíče<br /><br />**Popis**: vyžaduje. Název klíče vrátit hodnotu dat formuláře.|
|formDataMultiValues|Vrátí pole hodnot odpovídající název klíče z dat formuláře nebo výstup formulářem kódované akce.  Například následující vrátí `["bar"]`: `formDataMultiValues('someAction', 'foo')`<br /><br />**Parametr číslo**: 1<br /><br />**Název**: název akce<br /><br />**Popis**: vyžaduje. Název akce s dat formuláře nebo kódovaná formuláře odpovědi.<br /><br />**Parametr číslo**: 2<br /><br />**Název**: název klíče<br /><br />**Popis**: vyžaduje. Název klíče datových hodnot formuláře vrátit.|
|multipartBody|Vrátí pro část textu ve vícedílné zprávy výstupu akce.<br /><br />**Parametr číslo**: 1<br /><br />**Název**: název akce<br /><br />**Popis**: vyžaduje. Název akce s vícedílné zprávy odpovědi.<br /><br />**Parametr číslo**: 2<br /><br />**Název**: Index<br /><br />**Popis**: vyžaduje. Index rámci k načtení.|

### <a name="manipulation-functions"></a>Funkce pro zpracování
 
Tyto funkce se vztahuje na soubor XML a objekty.
 
|Název funkce|Popis|  
|-------------------|-----------------| 
|sloučení|Vrátí první objekt nesmí být nulová v předané argumenty. **Poznámka:**: prázdný řetězec není null. Například, pokud nejsou definovány parametry 1 a 2, funkce vrátí hodnotu `fallback`:  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **Parametr číslo**: 1... *n* <p> **Název**: objekt*n* <p> **Popis**: vyžaduje. Objekty, které chcete vyhledat hodnotu null.|
|AddProperty –|Vrátí objekt s další vlastnosti. Pokud již existuje vlastnost za běhu bude vyvolána k chybě. Například tato funkce vrátí objekt `{ "abc" : "xyz", "def": "uvw" }`: <p>`addProperty(json('{"abc" : "xyz"}'), 'def', 'uvw')` <p> **Parametr číslo**: 1 <p> **Název**: objekt <p> **Popis**: vyžaduje. Objekt, který chcete přidat novou vlastnost, která má. <p> **Parametr číslo**: 2 <p> **Název**: název vlastnosti <p> **Popis**: vyžaduje. Název nové vlastnosti. <p> **Parametr číslo**: 3 <p> **Název**: hodnota <p> **Popis**: vyžaduje. Hodnota pro přiřazení nové vlastnosti.|
|SetProperty –|Vrátí objekt s další vlastnosti nebo existující vlastnost nastavit na zadanou hodnotu. Například tato funkce vrátí objekt `{ "abc" : "uvw" }`: <p>`setProperty(json('{"abc" : "xyz"}'), 'abc', 'uvw')` <p> **Parametr číslo**: 1 <p> **Název**: objekt <p> **Popis**: vyžaduje. Objekt, do kterého chcete nastavit vlastnost.<p> **Parametr číslo**: 2 <p> **Název**: název vlastnosti<p> **Popis**: vyžaduje. Název nové nebo existující vlastnosti. <p> **Parametr číslo**: 3 <p> **Název**: hodnota <p> **Popis**: vyžaduje. Hodnota pro přiřazení k vlastnosti.|
|removeProperty|Vrátí objekt s vlastností odebrat. Pokud vlastnost odebrat neexistuje je vrácen původní objekt. Například tato funkce vrátí objekt `{ "abc" : "xyz" }`: <p>`removeProperty(json('{"abc" : "xyz", "def": "uvw"}'), 'def')` <p> **Parametr číslo**: 1 <p> **Název**: objekt <p> **Popis**: vyžaduje. Objekt, který má odeberte vlastnost z.<p> **Parametr číslo**: 2 <p> **Název**: název vlastnosti <p> **Popis**: vyžaduje. Název vlastnosti, které chcete odebrat. <p>|
|výraz XPath|Vrátí pole z uzlů XML odpovídající výraz xpath hodnotu, která je výsledkem výrazu xpath. <p> **Příklad 1** <p>Předpokládejme, hodnota parametru `p1` je řetězcová reprezentace tohoto kódu XML: <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>Tento kód: `xpath(xml(parameters('p1')), '/lab/robot/name')` <p>Vrátí <p>`[ <name>R1</name>, <name>R2</name> ]` <p>Při tomto kódu: <p>`xpath(xml(parameters('p1')), ' sum(/lab/robot/parts)')` <p>Vrátí <p>`13` <p> <p> **Příklad 2** <p>Zadané následující obsah XML: <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>Tento kód: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>nebo tento kód: <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>Vrátí <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>A tento kód: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>Vrátí <p>``xyz`` <p> **Parametr číslo**: 1 <p> **Název**: Xml <p> **Popis**: vyžaduje. Soubor XML, na které při vyhodnocování výrazu XPath. <p> **Parametr číslo**: 2 <p> **Název**: XPath <p> **Popis**: vyžaduje. Výraz XPath pro vyhodnocení.|

### <a name="math-functions"></a>Matematické funkce  

Tyto funkce lze použít pro buď typy čísel: **celá čísla** a **jako plovoucí**.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|přidat|Vrátí výsledek v přidávání dvou čísel. Například funkce vrátí hodnotu `20.333`: <p>`add(10,10.333)` <p> **Parametr číslo**: 1 <p> **Název**: Summand 1 <p> **Popis**: vyžaduje. Číslo, které má přidat do **Summand 2**. <p> **Parametr číslo**: 2 <p> **Název**: Summand 2 <p> **Popis**: vyžaduje. Číslo, které má přidat do **Summand 1**.|  
|Sub –|Vrací výsledek z odečtením dvou čísel. Například funkce vrátí hodnotu `-0.333`: <p>`sub(10,10.333)` <p> **Parametr číslo**: 1 <p> **Název**: Minuend <p> **Popis**: vyžaduje. Číslo, **Subtrahend** se odebere z. <p> **Parametr číslo**: 2 <p> **Název**: Subtrahend <p> **Popis**: vyžaduje. Číslo, které má odebrat z **Minuend**.|  
|mul|Vrátí výsledek násobení dvou čísel. Například funkce vrátí hodnotu `103.33`: <p>`mul(10,10.333)` <p> **Parametr číslo**: 1 <p> **Název**: násobenec 1 <p> **Popis**: vyžaduje. Číslo, které má násobení **násobenec 2** s. <p> **Parametr číslo**: 2 <p> **Název**: násobenec 2 <p> **Popis**: vyžaduje. Číslo, které má násobení **násobenec 1** s.|  
|div|Vrátí výsledek dělení dvou čísel. Například funkce vrátí hodnotu `1.0333`: <p>`div(10.333,10)` <p> **Parametr číslo**: 1 <p> **Název**: dělenec <p> **Popis**: vyžaduje. Číslo, které má dělit **dělitel**. <p> **Parametr číslo**: 2 <p> **Název**: dělitel <p> **Popis**: vyžaduje. Číslo, které má rozdělit **dělenec** podle.|  
|MOD|Vrátí zbytek po dělení dvou čísel (modulo). Například funkce vrátí hodnotu `2`: <p>`mod(10,4)` <p> **Parametr číslo**: 1 <p> **Název**: dělenec <p> **Popis**: vyžaduje. Číslo, které má dělit **dělitel**. <p> **Parametr číslo**: 2 <p> **Název**: dělitel <p> **Popis**: vyžaduje. Číslo, které má rozdělit **dělenec** podle. Po rozdělení je zbývající zabraný.|  
|min|Existují dva různé vzorce pro volání této funkce. <p>Zde `min` trvá na pole a funkce vrátí `0`: <p>`min([0,1,2])` <p>Alternativně může tuto funkci trvat seznamu hodnoty a také vrátí `0`: <p>`min(0,1,2)` <p> **Poznámka:**: všechny hodnoty musí být číslo, takže pokud je parametr pole, pole musí obsahovat pouze čísla. <p> **Parametr číslo**: 1 <p> **Název**: kolekce nebo hodnota <p> **Popis**: vyžaduje. Buď pole hodnot najít minimální hodnota nebo hodnota první sady. <p> **Parametr číslo**: 2... *n* <p> **Název**: hodnota *n* <p> **Popis**: volitelné. Pokud první parametr je hodnota, můžete předat další hodnoty a vrátí minimum všech předané hodnoty.|  
|max|Existují dva různé vzorce pro volání této funkce. <p>Zde `max` trvá na pole a funkce vrátí `2`: <p>`max([0,1,2])` <p>Alternativně může tuto funkci trvat seznamu hodnoty a také vrátí `2`: <p>`max(0,1,2)` <p> **Poznámka:**: všechny hodnoty musí být číslo, takže pokud je parametr pole, pole musí obsahovat pouze čísla. <p> **Parametr číslo**: 1 <p> **Název**: kolekce nebo hodnota <p> **Popis**: vyžaduje. Buď pole hodnoty najít maximální hodnota, nebo první hodnotu sady. <p> **Parametr číslo**: 2... *n* <p> **Název**: hodnota *n* <p> **Popis**: volitelné. Pokud je první parametr je hodnota, můžete předat další hodnoty a vrací maximálně všechny předané hodnoty.|  
|rozsah|Generuje pole celá čísla od určitého počtu. Délka pole vrácené definujete. <p>Například funkce vrátí hodnotu `[3,4,5,6]`: <p>`range(3,4)` <p> **Parametr číslo**: 1 <p> **Název**: počáteční index <p> **Popis**: vyžaduje. První celé číslo v poli. <p> **Parametr číslo**: 2 <p> **Název**: počet <p> **Popis**: vyžaduje. Tato hodnota je číslo celých čísel, která je v poli.|  
|rand –|Generuje náhodné celé číslo v rámci zadaného rozsahu (včetně pouze na konci prvního). Tato funkce může vrátit například buď `0` nebo '1': <p>`rand(0,2)` <p> **Parametr číslo**: 1 <p> **Název**: minimální <p> **Popis**: vyžaduje. Nejnižší číslo, které mohou být vráceny. <p> **Parametr číslo**: 2 <p> **Název**: maximální <p> **Popis**: vyžaduje. Tato hodnota je celé číslo další po nejvyšší celé číslo, které může být vrácen.|  
 
### <a name="date-functions"></a>Datové funkce  

|Název funkce|Popis|  
|-------------------|-----------------|  
|utcnow|Vrátí aktuální časové razítko jako řetězec, například: `2017-03-15T13:27:36Z`: <p>`utcnow()` <p> **Parametr číslo**: 1 <p> **Název**: formát <p> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|přidat_sekundy|Přidá do časovým razítkem řetězec předaná celočíselný počet sekund. Počet sekund může být kladná nebo záporná. Ve výchozím nastavení výsledkem je řetězec ve formátu ISO 8601 ("o"), pokud je k dispozici specifikátor formátu. Příklad: `2015-03-15T13:27:00Z`: <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **Parametr číslo**: 1 <p> **Název**: časové razítko <p> **Popis**: vyžaduje. Řetězec, který obsahuje čas. <p> **Parametr číslo**: 2 <p> **Název**: sekund <p> **Popis**: vyžaduje. Počet sekund, které chcete přidat. Můžete mít zápornou hodnotu má odečíst sekund. <p> **Parametr číslo**: 3 <p> **Název**: formát <p> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|addminutes|Přidá do časovým razítkem řetězec předaná celočíselný počet minut. Počet minut, může být kladná nebo záporná. Ve výchozím nastavení výsledkem je řetězec ve formátu ISO 8601 ("o"), pokud je k dispozici specifikátor formátu. Příklad: `2015-03-15T14:00:36Z`: <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **Parametr číslo**: 1 <p> **Název**: časové razítko <p> **Popis**: vyžaduje. Řetězec, který obsahuje čas. <p> **Parametr číslo**: 2 <p> **Název**: minut <p> **Popis**: vyžaduje. Počet minut, které chcete přidat. Můžete mít zápornou hodnotu má odečíst minut. <p> **Parametr číslo**: 3 <p> **Název**: formát <p> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|addhours|Přidá do časovým razítkem řetězec předaná celočíselný počet hodin. Počet hodin, může být kladná nebo záporná. Ve výchozím nastavení výsledkem je řetězec ve formátu ISO 8601 ("o"), pokud je k dispozici specifikátor formátu. Příklad: `2015-03-16T01:27:36Z`: <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **Parametr číslo**: 1 <p> **Název**: časové razítko <p> **Popis**: vyžaduje. Řetězec, který obsahuje čas. <p> **Parametr číslo**: 2 <p> **Název**: čas <p> **Popis**: vyžaduje. Počet hodin na Přidat. Můžete mít zápornou hodnotu má odečíst hodin. <p> **Parametr číslo**: 3 <p> **Název**: formát <p> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|přidat_dny|Přidá do časovým razítkem řetězec předaná celočíselný počet dnů. Počet dní, může být kladná nebo záporná. Ve výchozím nastavení výsledkem je řetězec ve formátu ISO 8601 ("o"), pokud je k dispozici specifikátor formátu. Příklad: `2015-03-13T13:27:36Z`: <p>`adddays('2015-03-15T13:27:36Z', -2)` <p> **Parametr číslo**: 1 <p> **Název**: časové razítko <p> **Popis**: vyžaduje. Řetězec, který obsahuje čas. <p> **Parametr číslo**: 2 <p> **Název**: počet dnů <p> **Popis**: vyžaduje. Počet dní, které chcete přidat. Můžete mít zápornou hodnotu má odečíst dnů. <p> **Parametr číslo**: 3 <p> **Název**: formát <p> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|formatDateTime|Vrací řetězec ve formátu data. Ve výchozím nastavení výsledkem je řetězec ve formátu ISO 8601 ("o"), pokud je k dispozici specifikátor formátu. Příklad: `2015-03-15T13:27:36Z`: <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **Parametr číslo**: 1 <p> **Název**: datum <p> **Popis**: vyžaduje. Řetězec, který obsahuje data. <p> **Parametr číslo**: 2 <p> **Název**: formát <p> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|startOfHour|Vrátí začátek hodiny časové razítko řetězec předaná. Například `2017-03-15T13:00:00Z`:<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Toto je řetězec, který obsahuje čas.<br /><br />**Parametr číslo**: 2<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").|  
|startOfDay|Vrátí začátek dne časovým razítkem řetězec předaná. Například `2017-03-15T00:00:00Z`:<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Toto je řetězec, který obsahuje čas.<br /><br />**Parametr číslo**: 2<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").| 
|startOfMonth|Vrátí začátek měsíce časovým razítkem řetězec předaná. Například `2017-03-01T00:00:00Z`:<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Toto je řetězec, který obsahuje čas.<br /><br />**Parametr číslo**: 2<br /><br /> **Název**: formát<br /><br /> **Popis**: volitelné. Buď [jeden znak specifikátor formátu](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) nebo [vlastní formát vzor](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) určující způsob formátování hodnotu této časové razítko. Pokud formát není zadaný, použije se formátu ISO 8601 ("o").| 
|DayOfWeek|Vrátí den týdne součást časové razítko řetězec.  Neděle je 0, pondělí je 1 a tak dále. Například `3`:<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Toto je řetězec, který obsahuje čas.| 
|DayOfMonth|Vrátí den měsíce součást časové razítko řetězec. Například `15`:<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Toto je řetězec, který obsahuje čas.| 
|DayOfYear|Vrátí den roku součást časové razítko řetězec. Například `74`:<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Toto je řetězec, který obsahuje čas.| 
|rysky|Vrátí o rysky vlastnost časového razítka řetězec. Například `1489603019`:<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **Parametr číslo**: 1<br /><br /> **Název**: časové razítko<br /><br /> **Popis**: vyžaduje. Toto je řetězec, který obsahuje čas.| 
  
### <a name="workflow-functions"></a>Funkce pracovního postupu  

Tyto funkce umožňují získat informace o pracovním postupu, sám za běhu.  
  
|Název funkce|Popis|  
|-------------------|-----------------|  
|listCallbackUrl|Vrátí řetězec tak, aby volání pro vyvolání aktivační události nebo akce. <p> **Poznámka:**: tuto funkci lze použít pouze v **httpWebhook** a **apiConnectionWebhook**není v **ruční**, **opakování**, **http**, nebo **apiConnection**. <p>Například `listCallbackUrl()` funkce vrátí: <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|pracovní postup|Tato funkce poskytuje všechny podrobnosti pro samotné pracovního postupu v modulu runtime. <p> Jsou k dispozici vlastnosti objektu pracovního postupu: <ul><li>`name`</li><li>`type`</li><li>`id`</li><li>`location`</li><li>`run`</li></ul> <p> Hodnota `run` vlastnost je objekt s následujícími vlastnostmi: <ul><li>`name`</li><li>`type`</li><li>`id`</li></ul> <p>Najdete v článku [Rest API](http://go.microsoft.com/fwlink/p/?LinkID=525617) podrobnosti o tyto vlastnosti.<p> Například název aktuální spustit získáte pomocí `"@workflow().run.name"` výraz. |

## <a name="next-steps"></a>Další postup

[Triggery a akce pracovních postupů](logic-apps-workflow-actions-triggers.md)

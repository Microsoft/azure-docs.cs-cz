---
title: Zpracování typy obsahu – Azure Logic Apps | Microsoft Docs
description: Jak se má Azure Logic Apps zacházet s typy obsahu v návrhu a prostředí runtime
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 809cc8524bf0d9922aec1f88aa5bfe3b8f2f4d78
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297117"
---
# <a name="handle-content-types-in-logic-apps"></a>Zpracování typů obsahu v aplikace logiky

Mnoho různých typů obsahu můžete procházet skrz aplikace logiky, včetně JSON, XML, ploché soubory a binární data. I když modul logiku aplikace podporuje všechny typy obsahu, některé jsou nativně srozumitelné modul aplikace logiky. Ostatní může vyžadovat přetypování nebo převody podle potřeby. Tento článek popisuje, jak modul zpracuje různých typů obsahu a jak se správně zpracovat tyto typy, pokud je to nezbytné.

## <a name="content-type-header"></a>Hlavička Content-Type

Pokud chcete spustit v podstatě, podíváme se na dvou `Content-Types` nevyžaduje převod nebo přetypování, který můžete použít v aplikaci logiky: `application/json` a `text/plain`.

## <a name="applicationjson"></a>Application/JSON

Modul pracovních postupů spoléhá na `Content-Type` záhlaví z HTTP volá určit příslušné zpracování. Každá žádost s typem obsahu `application/json` uložena a zpracována jako objekt JSON. Navíc můžete obsah JSON analyzovat ve výchozím nastavení bez nutnosti jakékoli přetypování. 

Například může analyzovat požadavek, který obsahuje záhlaví typu obsahu `application/json ` v pracovním postupu pomocí výrazu jako `@body('myAction')['foo'][0]` k získání hodnoty `bar` v tomto případě:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

Je potřeba žádné další přetypování. Při práci s daty, která je JSON, ale neměly hlavičku zadána, můžete ručně obsadit ho pomocí JSON `@json()` funkce, například: `@json(triggerBody())['foo']`.

### <a name="schema-and-schema-generator"></a>Schéma a schéma generátor

Aktivační událost požadavku umožňuje zadejte schéma JSON pro datové části, které chcete dostávat. Toto schéma umožňuje návrháře generování tokenů, můžete využívat obsah žádosti. Pokud nemáte schéma připraven, vyberte **datová část ukázky použít ke generování schématu**, takže může generovat schéma JSON z ukázkové datové části.

![Schéma](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>Akce, analyzovat JSON.

`Parse JSON` Akce umožňuje analyzovat obsah JSON do popisný tokenů pro používání aplikace logiky. Podobně jako na žádost o aktivaci, tato akce vám umožní zadat nebo Generovat schéma JSON pro obsah, který chcete analyzovat. Tento nástroj umožňuje využívání data ze služby Service Bus, Azure Cosmos DB a tak dále, mnohem jednodušší.

![Parsovat JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>text/plain

Podobně jako `application/json`, obdrželi s portálem zpráv protokolu HTTP `Content-Type` záhlaví `text/plain` jsou uloženy v základním formátu. Navíc pokud se tyto zprávy jsou zahrnuty v následných akcí bez přetypování, tyto požadavky přejděte s `Content-Type`: `text/plain` záhlaví. Například při práci s plochý soubor, vám může získat tento HTTP obsah jako `text/plain`:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

Pokud v další akci, odešlete žádost jako text jinou žádost (`@body('flatfile')`), žádost by měla `text/plain` hlavičku Content-Type. Při práci s daty, která je prostý text, ale neměly hlavičku zadána, můžete ručně přetypovat data pomocí textu `@string()` funkce, například: `@string(triggerBody())`.

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml a funkce Application/octet-stream a převaděč

Modul aplikace logiky vždy zachovává `Content-Type` přijatou v požadavku HTTP nebo odpovědi. Pokud modul přijímá obsah s `Content-Type` z `application/octet-stream`, a uvedete, že obsah v rámci následné akce bez přetypování, odchozí žádost má `Content-Type`: `application/octet-stream`. Tímto způsobem modul může zaručit, že data nejsou ztraceny při přesouvání v pracovním postupu. Však stavu akce (vstupy a výstupy) je uložena v objektu JSON, protože stav prochází přes pracovního postupu. Takže pokud chcete zachovat některé typy dat, modul převede obsah do binární kódováním base64 řetězec s příslušnou metadata, která chrání i `$content` a `$content-type`, které jsou automaticky převést. 

* `@json()` -vrhá dat `application/json`
* `@xml()` -vrhá dat `application/xml`
* `@binary()` -vrhá dat `application/octet-stream`
* `@string()` -vrhá dat `text/plain`
* `@base64()` -Převede obsah na řetězec ve formátu base64
* `@base64toString()` -Převede řetězec s kódováním base64 do `text/plain`
* `@base64toBinary()` -Převede řetězec s kódováním base64 do `application/octet-stream`
* `@encodeDataUri()` -kóduje řetězce jako bajtové pole dataUri
* `@decodeDataUri()` -dekóduje dataUri do bajtového pole

Například, pokud se vám zobrazila požadavek HTTP s `Content-Type`: `application/xml`:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Může přetypování a pozdější použití se něco podobného jako `@xml(triggerBody())`, nebo ve funkci, jako je `@xpath(xml(triggerBody()), '/CustomerName')`.

## <a name="other-content-types"></a>Jiné typy obsahu

Jiné typy obsahu jsou podporovány a pracovat s logic apps, ale můžou vyžadovat ruční načítání textu zprávy podle dekódování `$content`. Předpokládejme například, že spustíte `application/x-www-url-formencoded` požadavku kde `$content` je datová část kódovaný jako base64 řetězec pro zachování všech dat:

```
CustomerName=Frank&Address=123+Avenue
```

Vzhledem k tomu, že daný požadavek není ve formátu prostého textu nebo JSON, se v akci požadavku ukládají následujícím způsobem:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

V současné době není k dispozici nativní funkci pro data formuláře, tak můžete pořád použít tato data v pracovním postupu ručně přístupu k dat pomocí funkce jako `@string(body('formdataAction'))`. Pokud byste chtěli odchozí požadavek také mít `application/x-www-url-formencoded` záhlaví typu obsahu, můžete přidat požadavku k tělu akce bez jakékoli přetypování jako `@body('formdataAction')`. Však tato metoda funguje jenom v případě je jediný parametr v těle `body` vstupní. Pokud se pokusíte použít `@body('formdataAction')` v `application/json` požádat, můžete získat Chyba za běhu, protože je odeslán kódovaného textu.


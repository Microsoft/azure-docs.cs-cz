---
title: Zpracování typů obsahu – Azure Logic Apps | Dokumentace Microsoftu
description: Zjistěte, jak Logic Apps popisovače typů obsahu v době návrhu a běhu
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 82eb9c895f016efe569651dc89885d2e4850fd59
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159087"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Zpracování typů obsahu v Azure Logic Apps

Různé typy obsahu může probíhat přes aplikaci logiky, například JSON, XML, ploché soubory a binární data. I když Logic Apps podporuje všechny typy obsahu, některé mají nativní podporu a nevyžadují přetypování či převodu ve svých aplikacích logiky. Jiné typy může vyžadovat přetypování či převodu podle potřeby. Tento článek popisuje způsob, jakým aplikace logiky zpracovává typy obsahu a jak vám může správně přetypujte nebo převeďte těchto typů, pokud je to nezbytné.

Pokud chcete určit vhodný způsob pro zpracování typů obsahu, Logic Apps využívá `Content-Type` volá hodnota hlavičky protokolu HTTP, třeba:

* [Application/json](#application-json) (nativní typ)
* [text/plain](#text-plain) (nativní typ)
* [Application/xml a application/octet-stream](#application-xml-octet-stream)
* [Jiné typy obsahu](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps jsou uloženy a zpracuje všechny žádosti s *application/json* obsah typu jako objekt jazyka JavaScript Notation (JSON). Ve výchozím nastavení můžete analyzovat obsah JSON bez žádné přetypování. K analyzování žádosti, která obsahuje hlavičku s typem obsahu "application/json", můžete použít výraz. V tomto příkladu vrátí hodnotu `dog` z `animal-type` pole bez přetypování: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Pokud pracujete s daty JSON, který nemá určenou hlavičku, můžete ručně tato data do formátu JSON pomocí přetypovat [json() funkce](../logic-apps/workflow-definition-language-functions-reference.md#json), například: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Vytváření tokenů pro vlastnosti JSON

Logic Apps poskytuje možnosti pro vygenerování uživatelsky přívětivé tokeny, které představují vlastnosti v obsahu JSON, abyste mohli odkazovat a používat tyto vlastnosti snadněji v pracovním postupu vaší aplikace logiky.

* **Aktivační událost požadavek**

  Při použití této aktivační události v návrháři aplikace logiky můžete zadat schématu JSON, který popisuje datové části, které chcete dostávat. 
  Návrhář analyzuje obsah JSON s použitím tohoto schématu a generuje uživatelsky přívětivé tokeny, které představují vlastnosti do vlastního obsahu JSON. 
  Pak můžete snadno odkazovat a používat tyto vlastnosti v průběhu pracovního postupu aplikace logiky. 
  
  Pokud nemáte k dispozici schéma, můžete vygenerovat schéma. 
  
  1. V triggeru požadavku vyberte **k vygenerování schématu použít ukázkovou datovou část**.  
  
  2. V části **zadejte nebo vložte ukázkovou datovou část JSON**, zadejte ukázkovou datovou část a pak zvolte **provádí**. Příklad: 

     ![Zadejte ukázkovou datovou část JSON](./media/logic-apps-content-type/request-trigger.png)

     Generované schéma se nyní zobrazí v triggeru.

     ![Zadejte ukázkovou datovou část JSON](./media/logic-apps-content-type/generated-schema.png)

     Tady je základní definici pro trigger požadavku v editoru kódu zobrazení:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. V žádosti, ujistěte se, že zahrnete `Content-Type` záhlaví a nastavte hodnotu této hlavičky `application/json`.

* **Parsovat JSON akce**

  Při použití této akce v návrháři aplikace logiky můžete analyzovat výstup ve formátu JSON a vygenerovat uživatelsky přívětivé tokeny, které představují vlastnosti do vlastního obsahu JSON. 
  Pak můžete snadno odkazovat a používat tyto vlastnosti v průběhu pracovního postupu aplikace logiky. Podobně jako u triggeru požadavku, můžete zadat nebo generování schématu JSON, který popisuje obsah JSON, který chcete analyzovat. 
  Díky tomu můžete snadněji zpracovat data ze služby Azure Service Bus, Azure Cosmos DB a tak dále.

  ![Parsovat JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/plain

Pokud aplikace logiky přijme zprávy protokolu HTTP, které mají `Content-Type` záhlaví nastavena na `text/plain`, aplikace logiky ukládá zprávy v nezpracovaném tvaru. Pokud zahrnete tyto zprávy v následné akce bez přetypování, požadavky na přechod s `Content-Type` záhlaví nastavena na `text/plain`. 

Například když pracujete s plochého souboru, může být získáte požadavek HTTP s `Content-Type` záhlaví nastavena na `text/plain` typ obsahu:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Pokud pak odešlete tento požadavek novější akce jako text pro jinou žádost, například `@body('flatfile')`, má také druhý žádosti `Content-Type` hlavičku, která je nastavena na `text/plain`. Při práci s daty, která je ve formátu prostého textu, ale neurčili, nevložily záhlaví, můžete ručně tato data na text pomocí přetypování [string() funkce](../logic-apps/workflow-definition-language-functions-reference.md#string) jako je například tento výraz: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Application/xml a application/octet-stream

Vždy zachovává Logic Apps `Content-Type` v přijaté žádosti protokolu HTTP nebo odpovědi. Pokud vaše aplikace logiky přijme obsah s `Content-Type` nastavena na `application/octet-stream`, a zahrnují také, že obsah v rámci novější akce bez přetypování, odchozí požadavek je `Content-Type` nastavena na `application/octet-stream`. Tímto způsobem, Logic Apps může zaručit, že data nezíská ke ztrátě při procházení pracovního postupu. Ale stav akce nebo vstupy a výstupy, je uložen v objektu JSON při přesune do stavu v pracovním postupu. 

## <a name="converter-functions"></a>Převaděč funkce

Pokud chcete zachovat některé typy dat, Logic Apps převede obsah binárního řetězce s kódováním base64 s odpovídající metadata, která zachová i `$content` datové části a `$content-type`, které se automaticky převedou. 

Seznam popisuje, jak Logic Apps převede obsah, když použijete tyto [funkce](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: Data přetypování `application/json`
* `xml()`: Data přetypování `application/xml`
* `binary()`: Data přetypování `application/octet-stream`
* `string()`: Data přetypování `text/plain`
* `base64()`: Obsah převede na řetězec ve formátu base64
* `base64toString()`: Převede řetězec kódovaný ve formátu base64 `text/plain`
* `base64toBinary()`: Převede řetězec kódovaný ve formátu base64 `application/octet-stream`
* `encodeDataUri()`: Kóduje řetězec jako parametr bajtové pole
* `decodeDataUri()`: Dekóduje `dataUri` do bajtového pole

Například, pokud se zobrazí požadavek HTTP kde `Content-Type` nastavena na `application/xml`, jako je například tento obsah:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Tento obsah můžete přetypovat pomocí `@xml(triggerBody())` výraz s `xml()` a `triggerBody()` funkce a pozdější použití tohoto obsahu. Nebo můžete použít `@xpath(xml(triggerBody()), '/CustomerName')` výraz s `xpath()` a `xml()` funkce. 

## <a name="other-content-types"></a>Jiné typy obsahu

Logic Apps pracuje s a podporuje další typy obsahu, ale můžou vyžadovat ruční získat tělo zprávy o dekódování `$content` proměnné.

Předpokládejme například, že žádost se získá aktivuje aplikace logiky `application/x-www-url-formencoded` typ obsahu. Chcete-li zachovat všechna data `$content` proměnné v textu požadavku má datovou část, která je zakódovaný jako řetězec ve formátu base64:

`CustomerName=Frank&Address=123+Avenue`

Vzhledem k tomu, že daný požadavek není ve formátu prostého textu nebo formátu JSON, požadavek uložený v akci následujícím způsobem:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps poskytuje nativní funkce pro zpracování dat formuláře, třeba: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Nebo můžete ručně přístup k datům pomocí výrazu, jako je například v tomto příkladu:

`@string(body('formdataAction'))` 

Pokud byste chtěli odchozí požadavek mít stejný `application/x-www-url-formencoded` záhlaví typu obsahu, můžete přidat požadavek do textu akce bez žádné přetypování pomocí výrazu `@body('formdataAction')`. Ale tato metoda funguje pouze v případě text je jediným parametrem v `body` vstupu. Pokud se pokusíte použít `@body('formdataAction')` výrazu v `application/json` požadavku dojde, protože obsah je odeslán kódovaného Chyba za běhu.

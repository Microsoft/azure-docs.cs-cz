---
title: Zpracování typů obsahu
description: Naučte se zvládnout různé typy obsahu v pracovních postupech během doby návrhu a dobu běhu v Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 04642e69adba82cf33b933829d72c51109ad2ee1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578173"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Zpracování typů obsahu v Azure Logic Apps

Různé typy obsahu mohou procházet aplikace logiky, například JSON, XML, ploché soubory a binární data. I když Logic Apps podporuje všechny typy obsahu, některé mají nativní podporu a nevyžadují přetypování nebo převod ve vašich aplikacích logiky. Další typy mohou vyžadovat přetypování nebo převod podle potřeby. Tento článek popisuje, jak Logic Apps zpracovává typy obsahu a jakým způsobem je možné tyto typy v případě potřeby správně přetypovat nebo převést.

Aby bylo možné určit vhodný způsob pro zpracování typů obsahu, Logic Apps spoléhá na `Content-Type` hodnotu hlavičky v volání http, například:

* [Application/JSON](#application-json) (nativní typ)
* [Text/prostý](#text-plain) (nativní typ)
* [Application/XML a Application/oktet-Stream](#application-xml-octet-stream)
* [Další typy obsahu](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps ukládá a zpracovává jakýkoliv požadavek s typem obsahu *Application/JSON* jako objekt JavaScript Notation (JSON). Ve výchozím nastavení můžete analyzovat obsah JSON bez přetypování. Chcete-li analyzovat požadavek, který obsahuje hlavičku s typem obsahu "Application/JSON", můžete použít výraz. Tento příklad vrátí hodnotu `dog` z `animal-type` pole bez přetypování: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Pokud pracujete s daty JSON, která neurčují hlavičku, můžete tato data ručně přetypovat do formátu JSON pomocí [funkce JSON ()](../logic-apps/workflow-definition-language-functions-reference.md#json), například: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Vytváření tokenů pro vlastnosti JSON

Logic Apps poskytuje možnost generovat uživatelsky přívětivé tokeny, které představují vlastnosti v obsahu JSON, takže je můžete v pracovním postupu aplikace logiky snadněji odkazovat a používat.

* **Aktivační událost žádosti**

  Když použijete tuto aktivační událost v návrháři aplikace logiky, můžete zadat schéma JSON, které popisuje datovou část, kterou očekáváte k příjmu. 
  Návrhář analyzuje obsah JSON pomocí tohoto schématu a generuje uživatelsky přívětivé tokeny, které reprezentují vlastnosti v obsahu JSON. 
  Pak můžete tyto vlastnosti snadno odkazovat a používat v rámci pracovního postupu vaší aplikace logiky. 
  
  Pokud schéma nemáte, můžete schéma vytvořit. 
  
  1. V triggeru žádosti vyberte **použít ukázkovou datovou část k vygenerování schématu**.  
  
  2. V části **Zadejte nebo vložte ukázkovou datovou část JSON**zadejte ukázkovou datovou část a pak zvolte **Hotovo**. Například: 

     ![Snímek obrazovky zobrazující akci při přijetí požadavku HTTP s ukázkovou datovou částí JSON](./media/logic-apps-content-type/request-trigger.png)

     Ve triggeru se nyní zobrazí vygenerované schéma.

     ![Poskytnout ukázkovou datovou část JSON](./media/logic-apps-content-type/generated-schema.png)

     Tady je základní definice triggeru vaší žádosti v editoru zobrazení kódu:

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

  3. V žádosti se ujistěte, že jste zahrnuli `Content-Type` hlavičku a nastavili hodnotu hlavičky na `application/json` .

* **Analyzovat akci JSON**

  Při použití této akce v návrháři aplikace logiky můžete analyzovat výstup JSON a generovat uživatelsky přívětivé tokeny, které reprezentují vlastnosti v obsahu JSON. 
  Pak můžete tyto vlastnosti snadno odkazovat a používat v rámci pracovního postupu vaší aplikace logiky. Podobně jako u triggeru žádosti můžete zadat nebo vygenerovat schéma JSON, které popisuje obsah JSON, který chcete analyzovat. 
  Tímto způsobem můžete snadněji využívat data z Azure Service Bus, Azure Cosmos DB a tak dále.

  ![Parsovat JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>Text/prostý

Když aplikace logiky obdrží zprávy HTTP s `Content-Type` hlavičkou nastavenou na `text/plain` , vaše aplikace logiky ukládá tyto zprávy v nezpracované podobě. Pokud zahrnete tyto zprávy do následujících akcí bez přetypování, žádosti dostanou s `Content-Type` hlavičkou nastavenou na `text/plain` . 

Například když pracujete s plochým souborem, můžete získat požadavek HTTP s `Content-Type` hlavičkou nastavenou na `text/plain` typ obsahu:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Pokud pak tuto žádost odešlete v pozdější akci jako tělo jiné žádosti, například `@body('flatfile')` , tento druhý požadavek má také `Content-Type` záhlaví, které je nastaveno na `text/plain` . Pokud pracujete s daty, která jsou prostého textu, ale nezadali jste záhlaví, můžete tato data ručně přetypovat na text pomocí [funkce String ()](../logic-apps/workflow-definition-language-functions-reference.md#string) , jako je tento výraz: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Application/XML a Application/oktet-Stream

Logic Apps vždy zachová `Content-Type` v přijatém požadavku nebo odpovědi HTTP. Takže pokud vaše aplikace logiky obdrží obsah s `Content-Type` nastavením na `application/octet-stream` , a tento obsah zahrnete do pozdější akce bez přetypování, odchozí požadavek má také `Content-Type` nastaven na `application/octet-stream` . Tímto způsobem Logic Apps můžou zaručit, že se data během přesouvání přes pracovní postup nebudou ztratit. Stav akce nebo vstupy a výstupy jsou však uloženy v objektu JSON při přesunu stavu prostřednictvím pracovního postupu. 

## <a name="converter-functions"></a>Funkce převaděče

Aby bylo možné zachovat některé typy dat, Logic Apps převede obsah na binární řetězec s kódováním base64 s odpovídajícími metadaty, která zachovává jak `$content` datovou část, tak i `$content-type` , které jsou automaticky převedeny. 

Tento seznam popisuje, jak Logic Apps převádí obsah při použití těchto [funkcí](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: Přetypování dat na `application/json`
* `xml()`: Přetypování dat na `application/xml`
* `binary()`: Přetypování dat na `application/octet-stream`
* `string()`: Přetypování dat na `text/plain`
* `base64()`: Převede obsah na řetězec kódovaný v kódování Base64.
* `base64toString()`: Převede řetězec kódovaný v kódování Base64 na `text/plain`
* `base64toBinary()`: Převede řetězec kódovaný v kódování Base64 na `application/octet-stream`
* `dataUri()`: Převede řetězec na identifikátor URI dat.
* `dataUriToBinary()`: Převede identifikátor URI dat na binární řetězec.
* `dataUriToString()`: Převede identifikátor URI dat na řetězec.

Pokud například obdržíte požadavek HTTP, kde je `Content-Type` nastaveno na `application/xml` , například tento obsah:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Tento obsah můžete přetypovat pomocí `@xml(triggerBody())` výrazu s `xml()` `triggerBody()` funkcemi a a pak později použít tento obsah. Nebo můžete použít `@xpath(xml(triggerBody()), '/CustomerName')` výraz s `xpath()` `xml()` funkcemi a. 

## <a name="other-content-types"></a>Další typy obsahu

Logic Apps pracuje s a podporuje další typy obsahu, ale může vyžadovat, abyste ručně získali tělo zprávy dekódováním `$content` proměnné.

Předpokládejme například, že vaše aplikace logiky bude aktivována žádostí s `application/x-www-url-formencoded` typem obsahu. Chcete-li zachovat všechna data, `$content` Proměnná v textu požadavku má datovou část, která je zakódována jako řetězec Base64:

`CustomerName=Frank&Address=123+Avenue`

Vzhledem k tomu, že požadavek není prostý text nebo JSON, je požadavek uložený v akci následujícím způsobem:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps poskytuje nativní funkce pro zpracování dat formuláře, například: 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Nebo můžete ručně získat přístup k datům pomocí výrazu, jako je například tento příklad:

`@string(body('formdataAction'))` 

Pokud jste chtěli, aby odchozí požadavek měl stejné `application/x-www-url-formencoded` záhlaví typu obsahu, můžete žádost přidat do těla akce bez přetypování pomocí výrazu, jako je `@body('formdataAction')` . Tato metoda však funguje pouze v případě, že je text jediným parametrem ve `body` vstupu. Pokud se pokusíte použít `@body('formdataAction')` výraz v `application/json` žádosti, zobrazí se chyba za běhu, protože tělo je odesláno jako kódované.

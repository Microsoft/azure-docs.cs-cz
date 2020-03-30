---
title: Zpracování typů obsahu
description: Zjistěte, jak zpracovat různé typy obsahu v pracovních postupech během doby návrhu a běhu v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: ae0abe288edda2ce01311d8533b1f104409efce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666869"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Zpracování typů obsahu v Aplikacích Azure Logic Apps

Různé typy obsahu může tok prostřednictvím aplikace logiky, například JSON, XML, ploché soubory a binární data. Zatímco Logic Apps podporuje všechny typy obsahu, některé mají nativní podporu a nevyžadují přetypování nebo konverzi ve vašich aplikacích logiky. Jiné typy mohou vyžadovat odlévání nebo převod podle potřeby. Tento článek popisuje, jak logic Apps zpracovává typy obsahu a jak můžete správně přetypování nebo převést tyto typy v případě potřeby.

Chcete-li určit vhodný způsob zpracování typů obsahu, `Content-Type` Logic Apps spoléhá na hodnotu záhlaví v volání HTTP, například:

* [aplikace/json](#application-json) (nativní typ)
* [text/prostý](#text-plain) (nativní typ)
* [aplikace/xml a aplikace/oktet-stream](#application-xml-octet-stream)
* [Jiné typy obsahu](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps ukládá a zpracovává jakýkoli požadavek s typem obsahu *aplikace/json* jako objekt zápisu JavaScript (JSON). Ve výchozím nastavení můžete analyzovat obsah JSON bez přetypování. Chcete-li analyzovat požadavek, který má záhlaví s typem obsahu "aplikace/json", můžete použít výraz. Tento příklad vrátí `dog` hodnotu z `animal-type` pole bez přetypování: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Pokud pracujete s daty JSON, která neurčují záhlaví, můžete tato data ručně přetypovat do jSON pomocí [funkce json()](../logic-apps/workflow-definition-language-functions-reference.md#json), například: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>Vytvoření tokenů pro vlastnosti JSON

Logic Apps poskytuje možnost vytvářet uživatelsky přívětivé tokeny, které představují vlastnosti v obsahu JSON, takže můžete odkazovat a používat tyto vlastnosti snadněji v pracovním postupu aplikace logiky.

* **Aktivační událost požadavku**

  Při použití této aktivační události v Návrháři aplikace logiky můžete poskytnout schéma JSON, které popisuje datovou část, kterou očekáváte k příjmu. 
  Návrhář analyzuje obsah JSON pomocí tohoto schématu a generuje uživatelsky přívětivé tokeny, které představují vlastnosti v obsahu JSON. 
  Potom můžete snadno odkazovat a používat tyto vlastnosti v celém pracovním postupu aplikace logiky. 
  
  Pokud nemáte schéma, můžete vygenerovat schéma. 
  
  1. V aktivační události Požadavek vyberte **použít ukázkovou datovou část ke generování schématu**.  
  
  2. V části **Zadejte nebo vložte ukázkovou datovou část JSON**, zadejte ukázkovou datovou část a pak zvolte **Hotovo**. Například: 

     ![Poskytnout ukázkovou datovou část JSON](./media/logic-apps-content-type/request-trigger.png)

     Vygenerované schéma se nyní zobrazí v aktivační události.

     ![Poskytnout ukázkovou datovou část JSON](./media/logic-apps-content-type/generated-schema.png)

     Zde je základní definice aktivační události požadavku v editoru zobrazení kódu:

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

  3. V žádosti nezapomeňte zahrnout `Content-Type` záhlaví a nastavit hodnotu záhlaví `application/json`na .

* **Analyzovat akci JSON**

  Při použití této akce v návrháři aplikace logiky můžete analyzovat výstup JSON a generovat uživatelsky přívětivé tokeny, které představují vlastnosti v obsahu JSON. 
  Potom můžete snadno odkazovat a používat tyto vlastnosti v celém pracovním postupu aplikace logiky. Podobně jako požadavek aktivační události můžete poskytnout nebo generovat schéma JSON, které popisuje obsah JSON, který chcete analyzovat. 
  Tímto způsobem můžete snadněji využívat data z Azure Service Bus, Azure Cosmos DB a tak dále.

  ![Parsovat JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/prostý

Když vaše aplikace logiky obdrží `Content-Type` zprávy HTTP, které mají záhlaví nastaveno na `text/plain`, aplikace logiky ukládá tyto zprávy v nezpracované podobě. Pokud tyto zprávy zahrnete do následujících akcí `Content-Type` bez `text/plain`přetypování, požadavky budou s hlavicí nastavenou na . 

Pokud například pracujete s plochým souborem, může se `Content-Type` vám stát `text/plain` požadavek HTTP s hlavičkou nastavenou na typ obsahu:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Pokud potom odešlete tento požadavek v pozdější akci jako `@body('flatfile')`tělo pro jiný požadavek, například , tento druhý požadavek má také `Content-Type` záhlaví, které je nastaveno na `text/plain`. Pokud pracujete s daty, která jsou prostým textem, ale nezadali záhlaví, můžete tato data ručně přetypovat do textu pomocí [funkce string(),](../logic-apps/workflow-definition-language-functions-reference.md#string) jako je tento výraz: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>aplikace/xml a aplikace/oktet-stream

Aplikace logiky vždy `Content-Type` zachová v přijatém požadavku HTTP nebo odpovědi. Takže pokud vaše aplikace logiky `Content-Type` `application/octet-stream`přijímá obsah s nastavena na , a zahrnout tento `Content-Type` obsah `application/octet-stream`v pozdější akci bez obsazení, odchozí požadavek má také nastavena na . Aplikace Logic Apps tak mohou zaručit, že se při procházení pracovního postupu neztratí data. Stav akce nebo vstupy a výstupy je však uložen v objektu JSON, zatímco stav prochází pracovním postupem. 

## <a name="converter-functions"></a>Funkce převaděče

Chcete-li zachovat některé datové typy, Logic Apps převede obsah na binární base64 kódovaný řetězec s příslušnými metadaty, která zachová `$content` datové části a `$content-type`, které jsou automaticky převedeny. 

Tento seznam popisuje, jak logic Apps převádí obsah při použití těchto [funkcí](../logic-apps/workflow-definition-language-functions-reference.md):

* `json()`: Přetypováno data do`application/json`
* `xml()`: Přetypováno data do`application/xml`
* `binary()`: Přetypováno data do`application/octet-stream`
* `string()`: Přetypováno data do`text/plain`
* `base64()`: Převede obsah na řetězec kódovaný base64.
* `base64toString()`: Převede řetězec kódovaný base64 na`text/plain`
* `base64toBinary()`: Převede řetězec kódovaný base64 na`application/octet-stream`
* `dataUri()`: Převede řetězec na identifikátor URI dat.
* `dataUriToBinary()`: Převede identifikátor URI dat na binární řetězec.
* `dataUriToString()`: Převede identifikátor URI dat na řetězec.

Pokud například obdržíte požadavek `Content-Type` HTTP, pokud je nastaven na `application/xml`, například tento obsah:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Tento obsah můžete přetypovat pomocí `xml()` `triggerBody()` výrazu `@xml(triggerBody())` s funkcemi a později použít tento obsah. Nebo můžete použít `@xpath(xml(triggerBody()), '/CustomerName')` výraz s `xpath()` `xml()` funkcemi a. 

## <a name="other-content-types"></a>Jiné typy obsahu

Logic Apps pracuje s a podporuje jiné typy obsahu, ale může vyžadovat, `$content` abyste ručně získat text zprávy dekódováním proměnné.

Předpokládejme například, že vaše aplikace logiky se aktivuje požadavek s typem `application/x-www-url-formencoded` obsahu. Chcete-li zachovat všechna `$content` data, proměnná v těle požadavku má datovou část, která je kódována jako řetězec base64:

`CustomerName=Frank&Address=123+Avenue`

Vzhledem k tomu, že požadavek není prostý text nebo JSON, požadavek je uložen v akci takto:

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

Nebo můžete ručně přistupovat k datům pomocí výrazu, jako je například tento příklad:

`@string(body('formdataAction'))` 

Pokud jste chtěli, aby odchozí `application/x-www-url-formencoded` požadavek měl stejnou hlavičku typu obsahu, můžete požadavek přidat do `@body('formdataAction')`těla akce bez přetypování pomocí výrazu, například . Tato metoda však funguje pouze v případě, `body` že tělo je jediný parametr ve vstupu. Pokud se pokusíte `@body('formdataAction')` použít `application/json` výraz v požadavku, zobrazí se chyba za běhu, protože tělo je odesláno kódované.

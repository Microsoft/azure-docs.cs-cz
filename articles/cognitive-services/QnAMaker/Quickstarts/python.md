---
title: Rozhraní REST API (V4) - Python - QnA Maker
titleSuffix: Azure Cognitive Services
description: Get Python informace a ukázky kódu můžete rychle začít používat Microsoft Translator Text API ve službě Microsoft Cognitive Services v Azure.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 10/19/2018
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6eb8e265dc415e39f9c8728d32f3deecc2d4e646
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163799"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-python"></a>Jak používat rozhraní REST API nástroje QnA Maker pomocí Pythonu
<a name="HOLTop"></a>

V tomto článku se dozvíte, jak používat [API nástroje QnA Maker Microsoft](../Overview/overview.md) s využitím Pythonu můžete provádět následující.

- [Vytvoření nové znalostní báze](#Create)
- [Aktualizace existující znalostní báze](#Update)
- [Získání stavu požadavku na vytvoření nebo aktualizaci znalostní báze](#Status)
- [Publikování existující znalostní báze](#Publish)
- [Nahrazení obsahu existující znalostní báze](#Replace)
- [Stažení obsahu znalostní báze](#GetQnA)
- [Získání odpovědí na otázku s využitím znalostní báze](#GetAnswers)
- [Získání informací o znalostní bázi](#GetKB)
- [Získání informací o všech znalostních bázích patřících zadanému uživateli](#GetKBsByUser)
- [Odstranění znalostní báze](#Delete)
- [Získání klíčů aktuálního koncového bodu](#GetKeys)
- [Opětovné vygenerování klíčů aktuálního koncového bodu](#PutKeys)
- [Získání aktuální sady změn slov](#GetAlterations)
- [Nahrazení aktuální sady změn slov](#PutAlterations)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="prerequisites"></a>Požadavky

Ke spuštění tohoto kódu budete potřebovat [Python 3.x](https://www.python.org/downloads/).

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraním API služby Microsoft QnA Maker**. Budete potřebovat placené klíče předplatného ze svého [řídicího panelu Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Vytvoření znalostní báze

Následující kód vytvoří novou znalostní bázi pomocí metody [Create](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def create_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()
# /knowledgebases/create returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read ()

def check_status (path):
    print ('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read ()

req = {
  "name": "QnA Maker FAQ",
  "qnaList": [
    {
      "id": 0,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    }
  ],
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "files": []
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
operation, result = create_kb (path, content)
print (pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status (path)
    print (pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print ('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True
```

**Odpověď na vytvoření znalostní báze**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Zpět na začátek](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Aktualizace znalostní báze

Následující kód aktualizuje existující znalostní báze pomocí metody [Update](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def update_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()
# PATCH /knowledgebases returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read ()

def check_status (path):
    print ('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read ()

req = {
  'add': {
    'qnaList': [
      {
        'id': 1,
        'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
        'source': 'Custom Editorial',
        'questions': [
          'How can I change the default message from QnA Maker?'
        ],
        'metadata': []
      }
    ],
    'urls': [
      'https://docs.microsoft.com/azure/cognitive-services/Emotion/FAQ'
    ]
  },
  'update' : {
    'name' : 'New KB Name'
  },
  'delete': {
    'ids': [
      0
    ]
  }
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
operation, result = update_kb (path, content)
print (pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status (path)
    print (pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print ('Waiting ' + wait + ' seconds...')
        time.sleep (int(wait))
    else:
        done = True
```

**Odpověď na aktualizaci znalostní báze**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Zpět na začátek](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>Stav žádosti Get

Metodu [Operation](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails) můžete volat za účelem kontroly stavu žádosti o vytvoření nebo aktualizaci znalostní báze. Pokud chcete zjistit, jak se tato metoda používá, podívejte se na vzorový kód pro metodu [Create](#Create) nebo [Update](#Update).

[Zpět na začátek](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Publikování znalostní báze

Následující kód publikuje existující znalostní bázi pomocí metody [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def publish_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = publish_kb (path, '')
print (pretty_print(result))
```

**Odpověď na publikování znalostní báze**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "result": "Success."
}
```

[Zpět na začátek](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Nahrazení znalostní báze

Následující kód nahrazuje obsah zadané znalostní báze pomocí metody [Replace](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_publish).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def replace_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PUT", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

req = {
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my Knowledge Base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ]
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
result = replace_kb (path, content)
print (pretty_print(result))
```

**Odpověď na nahrazení znalostní báze**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
    "result": "Success."
}
```

[Zpět na začátek](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Stažení obsahu znalostní báze

Následující kód stáhne obsah zadané znalostní báze pomocí metody [Download knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_download).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

# Replace this with "test" or "prod".
env = 'test';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/{0}/{1}/qna/'.format(kb, env);

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_qna (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_qna (path)
print (pretty_print(result))
```

**Odpověď na stažení znalostní báze**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Zpět na začátek](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-using-a-knowledge-base"></a>Získání odpovědí na otázku pomocí znalostní báze

Následující kód získá odpovědi na dotazy pomocí zadané znalostní báze a pomocí metody **Generate answers**.

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
1. Přidejte níže uvedený kód.
1. Hodnotu `host` nahraďte názvem webu pro vaše předplatné služby QnA Maker. Další informace najdete v tématu [Vytvoření služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md).
1. Hodnotu `endpoint_key` nahraďte platným klíčem koncového bodu pro vaše předplatné. Všimněte si, že tento klíč není stejný jako klíč vašeho předplatného. Klíče koncového bodu můžete získat pomocí metody [Get endpoint keys](#GetKeys).
1. Hodnotu `kb` nahraďte ID znalostní báze, ve které se chcete dotazovat na odpovědi. Mějte na paměti, že tato znalostní báze už musí být publikovaná pomocí metody [Publish](#Publish).
1. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# NOTE: Replace this with a valid host name.
host = "ENTER HOST HERE"

# NOTE: Replace this with a valid endpoint key.
# This is not your subscription key.
# To get your endpoint keys, call the GET /endpointkeys method.
endpoint_key = "ENTER KEY HERE"

# NOTE: Replace this with a valid knowledge base ID.
# Make sure you have published the knowledge base with the
# POST /knowledgebases/{knowledge base ID} method.
kb = "ENTER KB ID HERE"

method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer"

question = {
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_answers (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Authorization': 'EndpointKey ' + endpoint_key,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()
    return response.read ()

# Convert the request to a string.
content = json.dumps(question)
result = get_answers (method, content)
print (pretty_print(result))
```

**Odpověď na získání odpovědi**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Zpět na začátek](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Získání informací o znalostní bázi

Následující kód získá informace o zadané znalostní bázi pomocí metody [Get knowledge base details](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasedetails).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_kb (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method + kb
result = get_kb (path)
print (pretty_print(result))
```

**Odpověď na získání podrobností o znalostní bázi**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Zpět na začátek](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Získání všech znalostních bází uživatele

Následující kód získá informace o všech znalostních bázích zadaného uživatele pomocí metody [Get knowledge bases for user](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_getknowledgebasesforuser).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_kbs (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_kbs (path)
print (pretty_print(result))
```

**Odpověď na získání znalostních bází uživatele**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Zpět na začátek](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Následující kód odstraní zadanou znalostní bázi pomocí metody [Delete knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/knowledgebases_delete).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def delete_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("DELETE", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = delete_kb (path, '')
print (pretty_print(result))
```

**Odpověď na odstranění znalostní báze**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "result": "Success."
}
```

[Zpět na začátek](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Získání klíčů koncového bodu

Následující kód získá klíče aktuálního koncového bodu pomocí metody [Get endpoint keys](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_getendpointkeys).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_keys (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_keys (path)
print (pretty_print(result))
```

**Odpověď na získání klíčů koncového bodu**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Zpět na začátek](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Aktualizace klíčů koncového bodu

Následující kód znovu vygeneruje klíče aktuálního koncového bodu pomocí metody [Refresh endpoint keys](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/endpointkeys_refreshendpointkeys).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with "PrimaryKey" or "SecondaryKey."
key_type = "PrimaryKey";

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def refresh_keys (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PATCH", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + key_type
result = refresh_keys (path, '')
print (pretty_print(result))
```

**Odpověď na aktualizaci klíčů koncového bodu**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Zpět na začátek](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Získání změn slov

Následující kód získá aktuální změny slov pomocí metody [Download alterations](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def get_alterations (path):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, '', headers)
    response = conn.getresponse ()
    return response.read ()

path = service + method
result = get_alterations (path)
print (pretty_print(result))
```

**Odpověď na získání změn slov**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Zpět na začátek](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Nahrazení změn slov

Následující kód nahradí aktuální změny slov pomocí metody [Replace alterations](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd).

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def put_alterations (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("PUT", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

req = {
  'wordAlterations': [
    {
      'alterations': [
        'botframework',
        'bot frame work'
      ]
    }
  ]
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
result = put_alterations (path, content)
print (pretty_print(result))
```

**Odpověď na nahrazení změn slov**

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
  "result": "Success."
}
```

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled služby QnA Maker](../Overview/overview.md)

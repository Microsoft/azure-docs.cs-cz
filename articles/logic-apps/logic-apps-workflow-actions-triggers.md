---
title: Referenční informace pro typy triggerů a akcí v jazyce definice pracovního postupu – Azure Logic Apps
description: Referenční příručka pro typy triggerů a akcí v jazyce definice pracovního postupu pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/19/2019
ms.openlocfilehash: df1b03d5fbb5b8ef8cda9407e4a595bc2de8ce54
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918958"
---
# <a name="reference-for-trigger-and-action-types-in-workflow-definition-language-for-azure-logic-apps"></a>Referenční informace pro typy triggerů a akcí v jazyce definice pracovního postupu pro Azure Logic Apps

Tento odkaz popisuje obecné typy používané k identifikaci triggerů a akcí v definici pracovního postupu vaší aplikace logiky, která je popsána a ověřována [jazykem definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md).
Pokud chcete najít triggery a akce konkrétního konektoru, které můžete použít ve svých aplikacích logiky, přečtěte si seznam v části [Přehled konektorů](https://docs.microsoft.com/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Přehled aktivačních událostí

Každý pracovní postup zahrnuje Trigger, který definuje volání, která vytvářejí instance a spouštějí pracovní postup. Tady jsou obecné kategorie aktivačních událostí:

* Aktivační událost cyklického dotazování, která kontroluje koncový bod služby v pravidelných intervalech

* Aktivační událost *nabízených oznámení* , která vytvoří předplatné koncového bodu a poskytuje *adresu URL zpětného volání* , takže koncový bod může upozornění aktivovat, když dojde k zadané události nebo jsou data k dispozici. Trigger potom počká na odpověď koncového bodu před jeho vypálením. 

Triggery mají tyto prvky nejvyšší úrovně, i když jsou některé volitelné:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*trigger-name*> | Řetězec | Název triggeru | 
| <*trigger-type*> | Řetězec | Typ triggeru, například "http" nebo "vstupech apiconnection" | 
| <*trigger-inputs*> | Objekt JSON | Vstupy, které definují chování triggeru | 
| <*Časová jednotka*> | Řetězec | Časová jednotka, která popisuje, jak často se Trigger aktivuje: "Druhé", "minuta", "hodina", "den", "týden", "měsíc" | 
| <*number-of-time-units*> | Integer | Hodnota, která určuje, jak často se Trigger aktivuje na základě frekvence, což je počet časových jednotek, které se mají počkat, dokud se Trigger znovu nespustí. <p>Tady jsou minimální a maximální intervaly: <p>Měsíčně 1-16 měsíců </br>Dnu 1-500 dní </br>Hodiny 1 – 12000 hodiny </br>Za 1 – 72000 minut </br>První 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month", opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*array-with-conditions*> | Array | Pole, které obsahuje jednu nebo více [podmínek](#trigger-conditions) , které určují, zda má být pracovní postup spuštěn. K dispozici pouze pro aktivační události. | 
| <*runtime-config-options*> | Objekt JSON | Můžete změnit chování `runtimeConfiguration` spuštění triggeru nastavením vlastností. Další informace najdete v tématu [nastavení konfigurace modulu runtime](#runtime-config-options). | 
| <*splitOn – výraz*> | Řetězec | U triggerů, které vracejí pole, můžete zadat výraz, který [rozdělí nebo oddělí ](#split-on-debatch) položky pole na více instancí pracovního postupu pro zpracování. | 
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Seznam typů triggerů

Každý typ triggeru má jiné rozhraní a vstupy, které definují chování triggeru. 

### <a name="built-in-triggers"></a>Předdefinované aktivační události

| Typ aktivační události | Popis | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Zkontroluje nebo provede dotazování libovolného koncového bodu. Tento koncový bod musí splňovat konkrétní kontrakt triggeru buď pomocí asynchronního vzoru "202" nebo vrácením pole. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Vytvoří pro vaši aplikaci logiky volatelné koncové body, ale zavolá zadanou adresu URL k registraci nebo zrušení registrace. |
| [**Vzorec**](#recurrence-trigger) | Aktivuje se na základě definovaného plánu. Můžete nastavit budoucí datum a čas pro aktivaci této aktivační události. Na základě frekvence můžete zadat také časy a dny pro spuštění pracovního postupu. | 
| [**Request**](#request-trigger)  | Vytvoří pro vaši aplikaci logiky volatelné koncové body, které se taky označuje jako Trigger "ruční". Podívejte se například na [pracovní postupy volání, Trigger nebo vnořování s koncovými body http](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Spravované triggery rozhraní API

| Typ aktivační události | Popis | 
|--------------|-------------| 
| [**Vstupech apiconnection**](#apiconnection-trigger) | Kontroluje nebo *dotazuje* koncový bod pomocí [rozhraní API spravovaných Microsoftem](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Vytvoří pro vaši aplikaci logiky volatelné koncové body voláním [rozhraní API spravovaných Microsoftem](../connectors/apis-list.md) pro přihlášení k odběru a zrušení odběru. | 
||| 

## <a name="triggers---detailed-reference"></a>Aktivační události – podrobný odkaz

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Aktivační událost vstupech apiconnection  

Tato aktivační událost zkontroluje nebo provede *dotaz* na koncový bod pomocí [rozhraní API spravovaných Microsoftem](../connectors/apis-list.md) , takže se parametry této aktivační události můžou lišit podle koncového bodu. Mnoho oddílů v této definici triggeru je volitelné. Chování triggeru závisí na tom, jestli jsou zahrnuté oddíly.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | Řetězec | Název triggeru | 
| <*název připojení*> | Řetězec | Název připojení ke spravovanému rozhraní API, které používá pracovní postup | 
| <*typ metody*> | Řetězec | Metoda HTTP pro komunikaci se spravovaným rozhraním API: "GET", "PUT", "POST", "PATCH", "DELETE" | 
| <*api-operation*> | Řetězec | Operace rozhraní API, která se má volat | 
| <*Časová jednotka*> | Řetězec | Časová jednotka, která popisuje, jak často se Trigger aktivuje: "Druhé", "minuta", "hodina", "den", "týden", "měsíc" | 
| <*number-of-time-units*> | Integer | Hodnota, která určuje, jak často se Trigger aktivuje na základě frekvence, což je počet časových jednotek, které se mají počkat, dokud se Trigger znovu nespustí. <p>Tady jsou minimální a maximální intervaly: <p>Měsíčně 1-16 měsíců </br>Dnu 1-500 dní </br>Hodiny 1 – 12000 hodiny </br>Za 1 – 72000 minut </br>První 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month", opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*opakování – chování*> | Objekt JSON | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objekt JSON | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*max-runs*> | Integer | Ve výchozím nastavení se instance pracovních postupů spouští ve stejnou dobu, nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*splitOn – výraz*> | Řetězec | Pro aktivační události, které vracejí pole, tento výraz odkazuje na pole, které se má použít, abyste mohli vytvořit a spustit instanci pracovního postupu pro každou položku pole, a ne použít smyčku For Each. <p>Tento výraz například reprezentuje položku v poli vrácené v obsahu těla triggeru:`@triggerbody()?['value']` |
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). |
||||

*Výstupy*
 
| Prvek | type | Popis |
|---------|------|-------------|
| Záhlaví | Objekt JSON | Hlavičky z odpovědi |
| hlavní část | Objekt JSON | Tělo odpovědi |
| Stavový kód | Integer | Stavový kód z odpovědi |
|||| 

*Příklad*

Tato definice triggeru každý den kontroluje e-maily v doručené poště pro účet Office 365 Outlook: 

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>Aktivační událost vstupech apiconnectionwebhook

Tato aktivační událost pošle požadavek na předplatné na koncový bod pomocí [rozhraní API spravovaného Microsoftem](../connectors/apis-list.md), který poskytuje *adresu URL zpětného volání* , kde koncový bod může odeslat odpověď, a počká, až koncový bod odpoví. Další informace najdete v tématu [odběry koncových bodů](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*název připojení*> | Řetězec | Název připojení ke spravovanému rozhraní API, které používá pracovní postup | 
| <*body-content*> | Objekt JSON | Veškerý obsah zprávy, který se odešle jako datová část spravovanému rozhraní API | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*opakování – chování*> | Objekt JSON | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objekt JSON | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*max-runs*> | Integer | Ve výchozím nastavení se instance pracovních postupů spouští ve stejnou dobu, nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*splitOn – výraz*> | Řetězec | Pro aktivační události, které vracejí pole, tento výraz odkazuje na pole, které se má použít, abyste mohli vytvořit a spustit instanci pracovního postupu pro každou položku pole, a ne použít smyčku For Each. <p>Tento výraz například reprezentuje položku v poli vrácené v obsahu těla triggeru:`@triggerbody()?['value']` |
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
|||| 

*Příklad*

Tato definice triggeru se přihlásí k odběru rozhraní API pro Office 365 Outlook, poskytuje adresu URL zpětného volání koncového bodu rozhraní API a počká, až bude koncový bod reagovat, když přijde nový e-mail.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>Trigger HTTP

Tato aktivační událost zkontroluje nebo provede dotazování zadaného koncového bodu na základě zadaného plánu opakování. Odpověď koncového bodu Určuje, zda je pracovní postup spuštěn.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<endpoint-URL>",
      "headers": { "<header-content>" },
      "body": "<body-content>",
      "authentication": { "<authentication-method>" },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro cyklické dotazování zadaného koncového bodu: "GET", "PUT", "POST", "PATCH", "DELETE" | 
| <*endpoint-URL*> | Řetězec | Adresa URL protokolu HTTP nebo HTTPS pro koncový bod, který se má dotazovat <p>Maximální velikost řetězce: 2 KB | 
| <*Časová jednotka*> | Řetězec | Časová jednotka, která popisuje, jak často se Trigger aktivuje: "Druhé", "minuta", "hodina", "den", "týden", "měsíc" | 
| <*number-of-time-units*> | Integer | Hodnota, která určuje, jak často se Trigger aktivuje na základě frekvence, což je počet časových jednotek, které se mají počkat, dokud se Trigger znovu nespustí. <p>Tady jsou minimální a maximální intervaly: <p>Měsíčně 1-16 měsíců </br>Dnu 1-500 dní </br>Hodiny 1 – 12000 hodiny </br>Za 1 – 72000 minut </br>První 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month", opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*header-content*> | Objekt JSON | Hlavičky, které se mají odeslat s požadavkem <p>Například pro nastavení jazyka a typu pro požadavek: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Řetězec | Obsah zprávy, která má být odeslána jako datová část s požadavkem | 
| <*ověřování – metoda*> | Objekt JSON | Metoda, kterou požadavek používá k ověření. Další informace najdete v tématu [odchozí ověřování Scheduleru](../scheduler/scheduler-outbound-authentication.md). Kromě Scheduleru je `authority` Tato vlastnost podporovaná. Není-li zadána, je použita výchozí `https://login.windows.net`hodnota, ale můžete použít jinou hodnotu,`https://login.windows\-ppe.net`například. |
| <*opakování – chování*> | Objekt JSON | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |  
 <*query-parameters*> | Objekt JSON | Všechny parametry dotazu, které se mají zahrnout do žádosti <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do žádosti. | 
| <*max-runs*> | Integer | Ve výchozím nastavení se instance pracovních postupů spouští ve stejnou dobu, nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
|||| 

*Výstupy*

| Prvek | type | Popis |
|---------|------|-------------| 
| Záhlaví | Objekt JSON | Hlavičky z odpovědi | 
| hlavní část | Objekt JSON | Tělo odpovědi | 
| Stavový kód | Integer | Stavový kód z odpovědi | 
|||| 

*Požadavky na příchozí požadavky*

Aby bylo možné dobře pracovat s aplikací logiky, musí koncový bod splňovat konkrétní vzor triggeru nebo kontrakt a tyto vlastnosti rozpoznat:  
  
| Odpověď | Požadováno | Popis | 
|----------|----------|-------------| 
| Kód stavu | Ano | Stavový kód "200 OK" spustí spuštění. Jakýkoli jiný stavový kód nespustí běh. | 
| Záhlaví opakování – za | Ne | Počet sekund do opětovného dotazování koncového bodu aplikace logiky | 
| Hlavička umístění | Ne | Adresa URL, která má být volána při dalším intervalu dotazování. Pokud není zadaný, použije se původní adresa URL. | 
|||| 

*Příklad chování pro různé požadavky*

| Kód stavu | Opakovat po | Chování | 
|-------------|-------------|----------|
| 200 | nTato | Spusťte pracovní postup a pak znovu zkontrolujte další data po definovaném opakování. | 
| 200 | 10 sekund | Spusťte pracovní postup a potom se znovu podívejte na další data po 10 sekundách. |  
| 202 | 60 sekund | Neaktivujte pracovní postup. K dalšímu pokusu dochází za jednu minutu v závislosti na definovaném opakování. Pokud je definované opakování méně než jedna minuta, má přednost záhlaví opakování. V opačném případě se použije definované opakování. | 
| 400 | nTato | Chybný požadavek, nespouštějte pracovní postup. `retryPolicy` Pokud není definován, použije se výchozí zásada. Po dosažení počtu opakovaných pokusů aktivační událost znovu zkontroluje data po definovaném opakování. | 
| 500 | nTato| Chyba serveru, nespouštějte pracovní postup. `retryPolicy` Pokud není definován, použije se výchozí zásada. Po dosažení počtu opakovaných pokusů aktivační událost znovu zkontroluje data po definovaném opakování. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Aktivační událost HTTPWebhook  

Tato aktivační událost umožňuje, aby se aplikace logiky volala vytvořením koncového bodu, který může zaregistrovat odběr voláním zadané adresy URL koncového bodu. Při vytváření této aktivační události v pracovním postupu odchozí požadavek provede volání registrace předplatného. Tímto způsobem může Trigger začít naslouchat událostem. Pokud operace neprovede tuto aktivační událost jako neplatnou, odchozí žádost automaticky provede volání, aby zrušilo odběr. Další informace najdete v tématu [odběry koncových bodů](#subscribe-unsubscribe).

Můžete také zadat [asynchronní omezení](#asynchronous-limits) pro aktivační událost **HTTPWebhook** .
Chování triggeru závisí na oddílech, které použijete nebo vynecháte. 

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Některé hodnoty, například <*metody typu*>, jsou k dispozici pro `"subscribe"` objekty a. `"unsubscribe"`

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro požadavek předplatného: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*koncový bod – přihlášení k odběru – adresa URL*> | Řetězec | Adresa URL koncového bodu, kam se má odeslat žádost o předplatné | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro žádost o zrušení: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*koncový bod – zrušení odběru adresy URL*> | Řetězec | Adresa URL koncového bodu, kam se má odeslat žádost o zrušení | 
| <*body-content*> | Řetězec | Veškerý obsah zprávy pro odeslání v rámci předplatného nebo žádosti o zrušení | 
| <*ověřování – metoda*> | Objekt JSON | Metoda, kterou požadavek používá k ověření. Další informace najdete v tématu [odchozí ověřování Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
| <*opakování – chování*> | Objekt JSON | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*max-runs*> | Integer | Ve výchozím nastavení se instance pracovních postupů spouští současně nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
|||| 

*Výstupy* 

| Prvek | type | Popis |
|---------|------|-------------| 
| Záhlaví | Objekt JSON | Hlavičky z odpovědi | 
| hlavní část | Objekt JSON | Tělo odpovědi | 
| Stavový kód | Integer | Stavový kód z odpovědi | 
|||| 

*Příklad*

Tato aktivační událost vytvoří odběr zadaného koncového bodu, poskytne jedinečnou adresu URL zpětného volání a počká na nově publikované technologické články.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Trigger opakování  

Tato aktivační událost se spouští na základě zadaného plánu opakování a poskytuje snadný způsob, jak vytvořit pravidelně běžící pracovní postup. 

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*Časová jednotka*> | Řetězec | Časová jednotka, která popisuje, jak často se Trigger aktivuje: "Druhé", "minuta", "hodina", "den", "týden", "měsíc" | 
| <*number-of-time-units*> | Integer | Hodnota, která určuje, jak často se Trigger aktivuje na základě frekvence, což je počet časových jednotek, které se mají počkat, dokud se Trigger znovu nespustí. <p>Tady jsou minimální a maximální intervaly: <p>Měsíčně 1-16 měsíců </br>Dnu 1-500 dní </br>Hodiny 1 – 12000 hodiny </br>Za 1 – 72000 minut </br>První 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month", opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | Řetězec | Počáteční datum a čas v tomto formátu: <p>RRRR-MM-DDThh: mm: SS Pokud zadáte časové pásmo <p>-nebo- <p>RRRR-MM-DDThh: mm: ssZ, pokud nezadáte časové pásmo <p>Pokud například požadujete 18. září 2017 na 2:00 odp., zadejte "2017-09-18T14:00:00" a zadejte časové pásmo, například "Tichomoří (běžný čas"), nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas má v budoucnosti maximálně 49 let a musí následovat za [specifikací data a času ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)a času UTC, ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Pokud nezadáte časové pásmo, je nutné na konci přidat písmeno "Z" bez mezer. Tento "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). <p>V případě jednoduchých plánů je počáteční čas prvním výskytem, ale u složitých plánů se Trigger neaktivuje dříve, než je čas spuštění. Další informace o počátečních datech a časech najdete v tématu [vytváření a plánování pravidelného spouštění úloh](../connectors/connectors-native-recurrence.md). | 
| <*časové pásmo*> | Řetězec | Platí pouze v případě, že zadáte čas spuštění, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Určete časové pásmo, které chcete použít. | 
| <*jedna nebo více hodin*> | Celočíselné nebo celočíselné pole | Pokud zadáte "Day" nebo "Week" pro `frequency`, můžete zadat jedno nebo více celých čísel od 0 do 23, které jsou odděleny čárkami, jako hodiny dne, kdy chcete pracovní postup spustit. <p>Pokud například zadáte "10", "12" a "14", dostanete jako hodiny 10 DOP, 12 ODP a 2 ODP. | 
| <*jedna nebo více minut – známky*> | Celočíselné nebo celočíselné pole | Pokud zadáte "Day" nebo "Week" pro `frequency`, můžete zadat jedno nebo více celých čísel od 0 do 59, které jsou odděleny čárkami, jako minuty hodiny, kdy chcete pracovní postup spustit. <p>Například můžete zadat "30" jako znak minuty a použít předchozí příklad pro hodiny dne, získáte 10:30 dop. 12:30 ODP. a 2:30 PM. | 
| weekDays | Řetězec nebo pole řetězců | Pokud zadáte "Week" pro `frequency`, můžete zadat jeden nebo více dní, které jsou odděleny čárkami, pokud chcete spustit pracovní postup: "Pondělí", "úterý", "Středa", "čtvrtek", "pátek", "Sobota" a "neděle" | 
| <*max-runs*> | Integer | Ve výchozím nastavení se instance pracovních postupů spouští současně nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
|||| 

*Příklad 1*

Tato základní aktivační událost opakování běží denně:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Příklad 2*

Můžete zadat počáteční datum a čas pro aktivaci triggeru. Tato aktivační událost opakování se spustí v zadaném datu a pak se aktivuje denně:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Příklad 3*

Tato aktivační událost opakování začíná 9. září 2017 na 2:00 odp. každý týden se spustí každý pondělí v 10:30, 12:30 ODP. a 2:30 ODP. (běžný čas):

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Další informace a příklady pro tuto aktivační událost najdete v tématu [vytváření a plánování pravidelného spouštění úloh](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Aktivační událost žádosti

Tato aktivační událost umožní, aby se aplikace logiky vyvolala vytvořením koncového bodu, který může přijímat příchozí požadavky. Pro tuto aktivační událost zadejte schéma JSON, které popisuje a ověří datovou část nebo vstupy, které Trigger obdrží od příchozího požadavku. Schéma také usnadňuje odkazování na vlastnosti triggeru z pozdějších akcí pracovního postupu. 

Chcete-li zavolat tuto aktivační událost, je `listCallbackUrl` nutné použít rozhraní API, které je popsáno v [REST API služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows). Informace o tom, jak tento Trigger použít jako koncový bod HTTP, najdete v tématu [pracovní postupy volání, triggeru nebo vnoření pracovních postupů s koncovými body http](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*property-name*> | Řetězec | Název vlastnosti ve schématu JSON, která popisuje datovou část | 
| <*property-type*> | Řetězec | Typ vlastnosti | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda, kterou příchozí požadavky musí použít k volání aplikace logiky: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*relative-path-for-accepted-parameter*> | Řetězec | Relativní cesta k parametru, který může adresa URL koncového bodu přijmout | 
| <*požadované – vlastnosti*> | Array | Jedna nebo více vlastností, které vyžadují hodnoty | 
| <*max-runs*> | Integer | Ve výchozím nastavení se instance pracovních postupů spouští současně nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*max-runs-queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
|||| 

*Příklad*

Tato aktivační událost určuje, že příchozí požadavek musí použít metodu HTTP POST pro volání triggeru a zahrnuje schéma, které ověřuje vstup z příchozího požadavku: 

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Aktivační podmínky

Pro všechny triggery a triggery můžete zahrnout pole obsahující jeden nebo více výrazů pro podmínky, které určují, jestli se má pracovní postup spustit. Chcete-li `conditions` přidat vlastnost do triggeru v pracovním postupu, otevřete aplikaci logiky v editoru zobrazení kódu.

Můžete například určit, že se Trigger aktivuje jenom v případě, že web vrátí vnitřní chybu serveru odkazem na stavový kód triggeru ve `conditions` vlastnosti:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Ve výchozím nastavení se aktivační událost aktivuje až po získání odpovědi "200 OK". Když výraz odkazuje na stavový kód triggeru, nahradí se výchozí chování triggeru. Pokud tedy chcete, aby se Trigger spouštěl pro více než jeden stavový kód, jako je například stavový kód "200" a "201", je nutné tento výraz zahrnout jako podmínku: 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Aktivovat více spuštění

Pokud aktivační událost vrátí pole pro zpracování vaší aplikace logiky, může někdy trvat příliš dlouho smyčka "for each", aby bylo možné zpracovat každou položku pole. Místo toho můžete použít vlastnost **SplitOn** ve triggeru k oddávkování pole. Při dedávkování se položky pole rozdělí a spustí se nová instance pracovního postupu, která se spustí pro každou položku pole. Tento přístup je užitečný například v případě, že chcete dotazovat koncový bod, který může vracet více nových položek mezi intervaly dotazování.
V případě maximálního počtu položek pole, které **SplitOn** může zpracovat v jednom spuštění aplikace logiky, viz [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> **SplitOn** se nedá použít se vzorem synchronní odpovědi. Libovolný pracovní postup, který používá **SplitOn** a zahrnuje akci odpovědi, se spouští asynchronně a okamžitě `202 ACCEPTED` pošle odpověď.

Pokud soubor Swagger triggeru popisuje datovou část, která je pole, do triggeru se automaticky přidá vlastnost **SplitOn** . V opačném případě přidejte tuto vlastnost do datové části odpovědi, která obsahuje pole, které chcete dedávkovat. 

*Příklad*

Předpokládejme, že máte rozhraní API, které vrací tuto odpověď: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```
 
Aplikace logiky potřebuje jenom obsah z pole v `Rows`, takže můžete vytvořit Trigger podobný tomuto příkladu:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Pokud použijete `SplitOn` příkaz, nemůžete získat vlastnosti, které jsou mimo pole. Proto v tomto příkladu nemůžete získat `status` vlastnost v odpovědi vrácené z rozhraní API.
> 
> Chcete-li se vyhnout selhání `Rows` , pokud vlastnost neexistuje, tento příklad `?` používá operátor.

Vaše definice pracovního postupu teď může `@triggerBody().name` použít k `name` získání hodnot, které jsou `"customer-name-one"` z prvního spuštění a `"customer-name-two"` z druhého spuštění. Výstupy triggerů tak vypadají jako tyto příklady:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Přehled akcí

Azure Logic Apps poskytuje různé typy akcí – každý s různými vstupy, které definují jedinečné chování akce. 

Akce mají tyto prvky na nejvyšší úrovni, i když jsou některé volitelné:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------|
| <*název akce*> | Řetězec | Název akce | 
| <*Typ akce*> | Řetězec | Typ akce, například "http" nebo "vstupech apiconnection"| 
| <*Název vstupu*> | Řetězec | Název vstupu, který definuje chování akce | 
| <*Hodnota vstupu*> | Některé | Vstupní hodnota, která může být řetězec, celé číslo, objekt JSON atd. | 
| <*previous-trigger-or-action-status*> | Objekt JSON | Název a výsledný stav triggeru nebo akce, které se musí spustit bezprostředně před spuštěním této aktuální akce | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------|
| <*opakování – chování*> | Objekt JSON | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu Zásady opakování. | 
| <*runtime-config-options*> | Objekt JSON | U některých akcí můžete chování akce v době běhu změnit nastavením `runtimeConfiguration` vlastností. Další informace najdete v tématu [nastavení konfigurace modulu runtime](#runtime-config-options). | 
| <*operace – možnost*> | Řetězec | U některých akcí můžete změnit výchozí chování `operationOptions` nastavením vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Seznam typů akcí

Tady jsou některé běžně používané typy akcí: 

* [Předdefinované typy akcí](#built-in-actions) , jako jsou tyto příklady a další: 

  * [**Http**](#http-action) pro volání koncových bodů přes HTTP nebo https

  * [**Odpověď na reakci**](#response-action) na žádosti

  * [**Spustit javascriptový kód**](#run-javascript-code) pro spouštění fragmentů kódu JavaScriptu

  * [**Funkce**](#function-action) pro volání Azure Functions

  * Akce operací s daty, jako je [**spojení**](#join-action), [**psaní**](#compose-action), [**tabulka**](#table-action), [**Výběr**](#select-action)a další, které vytvářejí nebo transformují data z různých vstupů

  * [**Pracovní postup**](#workflow-action) pro volání jiného pracovního postupu aplikace logiky

* [Spravované typy akcí rozhraní API](#managed-api-actions) , jako je [**vstupech apiconnection**](#apiconnection-action) a [**vstupech apiconnectionwebhook**](#apiconnectionwebhook-action) , které volají různé konektory a rozhraní api spravovaná Microsoftem, například Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub a další

* [Řízení typů akcí pracovního postupu](#control-workflow-actions) , například [**if**](#if-action), [**foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action)a [**dokud**](#until-action), které obsahují jiné akce a které vám pomůžou organizovat provádění pracovního postupu

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Integrované akce

| Typ akce | Popis | 
|-------------|-------------| 
| [**Sestavit**](#compose-action) | Vytvoří jeden výstup ze vstupů, které mohou mít různé typy. | 
| [**Spustit JavaScriptový kód**](#run-javascript-code) | Spustí fragmenty kódu JavaScriptu, které se vejdou do konkrétních kritérií. Požadavky na kód a další informace naleznete v tématu [Přidání a spuštění fragmentů kódu s vloženým kódem](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Slouží**](#function-action) | Zavolá funkci Azure Function. | 
| [**HTTP**](#http-action) | Volá koncový bod HTTP. | 
| [**Zúčastnit**](#join-action) | Vytvoří řetězec ze všech položek v poli a oddělí tyto položky zadaným znakem oddělovače. | 
| [**Analyzovat JSON**](#parse-json-action) | Vytvoří uživatelsky přívětivé tokeny z vlastností v obsahu JSON. Pak můžete odkazovat na tyto vlastnosti zahrnutím tokenů do aplikace logiky. | 
| [**Zadávání**](#query-action) | Vytvoří pole z položek v jiném poli na základě podmínky nebo filtru. | 
| [**Základě**](#response-action) | Vytvoří odpověď na příchozí volání nebo požadavek. | 
| [**Vybrali**](#select-action) | Vytvoří pole s objekty JSON transformací položek z jiného pole na základě zadané mapy. | 
| [**Stolní**](#table-action) | Vytvoří z pole tabulku CSV nebo HTML. | 
| [**Ruší**](#terminate-action) | Zastaví aktivně běžící pracovní postup. | 
| [**Počkej**](#wait-action) | Pozastaví pracovní postup na určitou dobu nebo do zadaného data a času. | 
| [**Workflow**](#workflow-action) | Vnoření pracovního postupu do jiného pracovního postupu. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Spravované akce rozhraní API

| Typ akce | Popis | 
|-------------|-------------|  
| [**Vstupech apiconnection**](#apiconnection-action) | Volá koncový bod HTTP pomocí [rozhraní API spravovaného Microsoftem](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funguje jako Webhook HTTP, ale používá [rozhraní API spravované Microsoftem](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Řízení akcí pracovního postupu

Tyto akce vám pomůžou řídit spouštění pracovních postupů a zahrnovat další akce. Mimo akci pracovního postupu ovládacího prvku můžete přímo odkazovat na akce v rámci akce pracovního postupu ovládacího prvku. Například pokud máte v oboru nějakou `Http` akci, můžete odkazovat na `@body('Http')` výraz z libovolného místa v pracovním postupu. Akce, které existují v akci pracovního postupu řízení, ale mohou být spouštěny pouze za jinými akcemi, které jsou ve stejné struktuře pracovního postupu řízení.

| Typ akce | Popis | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Spustí stejné akce ve smyčce pro každou položku v poli. | 
| [**If**](#if-action) | Spustí akce na základě toho, jestli je zadaná podmínka pravdivá, nebo false. | 
| [**Oboru**](#scope-action) | Spustí akce založené na stavu skupiny ze sady akcí. | 
| [**Přepnutí**](#switch-action) | Spustí akce uspořádané do případů, kdy hodnoty z výrazů, objektů nebo tokenů odpovídají hodnotám určeným každým případem. | 
| [**Vrátí**](#until-action) | Spustí akce ve smyčce, dokud není zadaná podmínka pravdivá. | 
|||  

## <a name="actions---detailed-reference"></a>Akce – podrobný odkaz

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Akce vstupech apiconnection

Tato akce odešle požadavek HTTP na [rozhraní API spravované Microsoftem](../connectors/apis-list.md) a vyžaduje informace o rozhraní API a parametrech plus odkaz na platné připojení. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*název akce*> | Řetězec | Název akce poskytované konektorem | 
| <*api-name*> | Řetězec | Název rozhraní API spravovaného Microsoftem používaného pro připojení | 
| <*typ metody*> | Řetězec | Metoda HTTP pro volání rozhraní API: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*api-operation*> | Řetězec | Operace rozhraní API, která se má volat | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*jiné – specifické pro akce-vstup – vlastnosti*> | Objekt JSON | Všechny další vstupní vlastnosti, které se vztahují na tuto konkrétní akci | 
| <*opakování – chování*> | Objekt JSON | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objekt JSON | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*jiné – vlastnosti specifické pro akci*> | Objekt JSON | Všechny další vlastnosti, které se vztahují na tuto konkrétní akci | 
|||| 

*Příklad*

Tato definice popisuje akci **odeslání e-mailu** pro Office 365 Outlook Connector, což je rozhraní API spravované Microsoftem: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>Akce vstupech apiconnectionwebhook

Tato akce odešle požadavek na předplatné přes HTTP do koncového bodu pomocí [rozhraní API spravovaného Microsoftem](../connectors/apis-list.md), poskytne *adresu URL zpětného volání* , kde koncový bod může poslat odpověď, a počká, až koncový bod odpoví. Další informace najdete v tématu [odběry koncových bodů](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Některé hodnoty, například <*metody typu*>, jsou k dispozici pro `"subscribe"` objekty a. `"unsubscribe"`

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*název akce*> | Řetězec | Název akce poskytované konektorem | 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro odběr nebo zrušení odběru koncového bodu: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*api-subscribe-URL*> | Řetězec | Identifikátor URI, který se má použít pro přihlášení k odběru rozhraní API | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | Řetězec | Identifikátor URI, který se má použít k odhlášení odběru rozhraní API | 
| <*header-content*> | Objekt JSON | Libovolná záhlaví, která se mají poslat v žádosti <p>Například chcete-li nastavit jazyk a typ pro požadavek: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Objekt JSON | Veškerý obsah zprávy, který se má odeslat v žádosti | 
| <*ověřování – metoda*> | Objekt JSON | Metoda, kterou požadavek používá k ověření. Další informace najdete v tématu [odchozí ověřování Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
| <*opakování – chování*> | Objekt JSON | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objekt JSON | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*jiné – specifické pro akce-vstup – vlastnosti*> | Objekt JSON | Všechny další vstupní vlastnosti, které se vztahují na tuto konkrétní akci | 
| <*jiné – vlastnosti specifické pro akci*> | Objekt JSON | Všechny další vlastnosti, které se vztahují na tuto konkrétní akci | 
|||| 

Omezení pro akci **vstupech apiconnectionwebhook** můžete zadat také stejným způsobem jako [asynchronní limity protokolu HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Akce psaní

Tato akce vytvoří jeden výstup z více vstupů, včetně výrazů. Výstup i vstupy mohou mít jakýkoli typ, který Azure Logic Apps nativně podporuje, například pole, objekty JSON, XML a binární soubory.
Výstup akce pak můžete použít v jiných akcích. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Požadováno* 

| Value | type | Popis | 
|-------|------|-------------| 
| <*inputs-to-compose*> | Any | Vstupy pro vytvoření jednoho výstupu | 
|||| 

*Příklad 1*

<!-- markdownlint-disable MD038 -->
Tato definice akce `abcdefg ` se sloučí s koncovým místem a hodnotou `1234`:
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Zde je výstup, který tato akce vytvoří:

`abcdefg 1234`

*Příklad 2*

Tato definice akce slučuje řetězcovou proměnnou, která obsahuje `abcdefg` , a celočíselnou proměnnou, `1234`která obsahuje:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Zde je výstup, který tato akce vytvoří:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Akce spuštění kódu JavaScriptu

Tato akce spustí fragment kódu JavaScriptu a vrátí výsledky prostřednictvím tokenu, `Result` který mohou později odkazovat na jiné akce.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Požadováno*

| Value | type | Popis |
|-------|------|-------------|
| <*JavaScript-code-snippet*> | Různé | Kód JavaScriptu, který chcete spustit. Požadavky na kód a další informace naleznete v tématu [Přidání a spuštění fragmentů kódu s vloženým kódem](../logic-apps/logic-apps-add-run-inline-code.md). <p>V atributu může fragment kódu jako vstup použít objekt jen `workflowContext` pro čtení. `code` Tento objekt má podvlastnost, které přidávají vašemu kódu přístup k výsledkům triggeru a předchozím akcím v pracovním postupu. Další informace o `workflowContext` objektu naleznete [v tématu Referenční Trigger a výsledky akcí ve vašem kódu](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Vyžadováno v některých případech*

`explicitDependencies` Atribut určuje, že chcete explicitně zahrnout výsledky z triggeru, předchozí akce nebo obojí jako závislosti pro váš fragment kódu. Další informace o přidání těchto závislostí naleznete v tématu [Add Parameters for a line Code](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Pro atribut můžete zadat `true` nebo `false` hodnoty. `includeTrigger`

| Value | type | Popis |
|-------|------|-------------|
| <*previous-actions*> | Pole řetězců | Pole se zadanými názvy akcí Použijte názvy akcí, které se zobrazí v definici pracovního postupu, kde názvy akcí používají podtržítka (_), ne mezery (""). |
||||

*Příklad 1*

Tato akce spustí kód, který získá název vaší aplikace logiky a vrátí text "Hello World \<z Logic-App-Name >" jako výsledek. V tomto příkladu kód odkazuje na název pracovního postupu pomocí přístupu `workflowContext.workflow.name` k vlastnosti prostřednictvím objektu jen `workflowContext` pro čtení. Další informace o používání `workflowContext` objektu naleznete v tématu [referenční Trigger a výsledky akcí ve vašem kódu](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Příklad 2*

Tato akce spustí kód v aplikaci logiky, která se aktivuje při přijetí nového e-mailu v účtu Office 365 Outlook. Aplikace logiky také používá akci odeslání e-mailu pro schválení, která přesměruje obsah z přijatého e-mailu spolu se žádostí o schválení. 

Kód extrahuje e-mailové adresy z `Body` vlastnosti triggeru a vrátí tyto e-mailové adresy spolu `SelectedOption` s hodnotou vlastnosti z akce schválení. Akce explicitně zahrnuje akci odeslání e-mailu o schválení jako závislost v `explicitDependencies`  >  `actions` atributu.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Akce funkce

Tato akce volá dříve vytvořenou [funkci Azure Functions](../azure-functions/functions-create-first-azure-function.md).

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------|  
| <*Azure-function-ID*> | Řetězec | ID prostředku pro funkci Azure, kterou chcete volat. Zde je formát pro tuto hodnotu:<p>"/Subscriptions/<*Azure-Subscription-ID*>/ResourceGroups/<*Azure-resource-group*>/Providers/Microsoft.Web/Sites/<*Azure-function-app-Name*>/Functions/<*Azure-Function-Name*> " | 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro volání funkce: "GET", "PUT", "POST", "PATCH" nebo "DELETE" <p>Pokud není zadán, výchozí hodnota je metoda "POST". | 
||||

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------|  
| <*header-content*> | Objekt JSON | Všechna záhlaví k odeslání pomocí volání <p>Například chcete-li nastavit jazyk a typ pro požadavek: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Objekt JSON | Veškerý obsah zprávy, který se má odeslat v žádosti | 
| <*query-parameters*> | Objekt JSON | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*jiné – specifické pro akce-vstup – vlastnosti*> | Objekt JSON | Všechny další vstupní vlastnosti, které se vztahují na tuto konkrétní akci | 
| <*jiné – vlastnosti specifické pro akci*> | Objekt JSON | Všechny další vlastnosti, které se vztahují na tuto konkrétní akci | 
||||

Při ukládání aplikace logiky modul Logic Apps provádí tyto kontroly na odkazované funkci:

* Váš pracovní postup musí mít přístup k této funkci.

* Pracovní postup může používat jenom standardní Trigger HTTP nebo aktivační událost obecného Webhooku JSON. 

  Modul Logic Apps získává a ukládá do mezipaměti adresu URL triggeru, která se používá za běhu. Nicméně pokud nějaká operace zruší platnost adresy URL v mezipaměti, akce **funkce** se za běhu nezdařila. Pokud chcete tento problém vyřešit, uložte aplikaci logiky znovu, aby aplikace logiky předávala adresu URL triggeru a znovu ji zařadí do mezipaměti.

* Funkce nemůže mít definovánu žádnou trasu.

* Povolují se jenom úrovně autorizace Function a Anonymous. 

*Příklad*

Tato definice akce volá dříve vytvořenou funkci getproductid:

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>Akce HTTP

Tato akce odešle požadavek na zadaný koncový bod a zkontroluje odpověď, aby určil, zda by měl být pracovní postup spuštěn. 

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>"
   },
   "runAfter": {}
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda, která se má použít pro odeslání žádosti: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*HTTP-or-HTTPS-endpoint-URL*> | Řetězec | Koncový bod HTTP nebo HTTPS, který chcete zavolat. Maximální velikost řetězce: 2 KB | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*header-content*> | Objekt JSON | Všechna záhlaví, která se mají poslat pomocí žádosti <p>Chcete-li například nastavit jazyk a typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | Objekt JSON | Veškerý obsah zprávy, který se má odeslat v žádosti | 
| <*opakování – chování*> | Objekt JSON | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*query-parameters*> | Objekt JSON | Všechny parametry dotazu, které se mají zahrnout do žádosti <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*jiné – specifické pro akce-vstup – vlastnosti*> | Objekt JSON | Všechny další vstupní vlastnosti, které se vztahují na tuto konkrétní akci | 
| <*jiné – vlastnosti specifické pro akci*> | Objekt JSON | Všechny další vlastnosti, které se vztahují na tuto konkrétní akci | 
|||| 

*Příklad*

Tato definice akce načte poslední novinky odesláním žádosti do zadaného koncového bodu:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Akce JOIN

Tato akce vytvoří řetězec ze všech položek v poli a oddělí tyto položky zadaným znakem oddělovače. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*skupin*> | Array | Pole nebo výraz, který poskytuje zdrojové položky. Pokud zadáte výraz, uzavřete tento výraz do dvojitých uvozovek. | 
| <*oddělovač*> | Řetězec s jedním znakem | Znak, který odděluje každou položku v řetězci | 
|||| 

*Příklad*

Předpokládejme, že máte dříve vytvořenou proměnnou "myIntegerArray", která obsahuje toto celočíselné pole: 

`[1,2,3,4]` 

Tato definice akce Získá hodnoty z proměnné pomocí `variables()` funkce ve výrazu a vytvoří tento řetězec s těmito hodnotami, které jsou odděleny čárkou:`"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Analyzovat akci JSON

Tato akce vytvoří uživatelsky přívětivá pole nebo tokeny z vlastností v obsahu JSON. Pak můžete k těmto vlastnostem přistupovat ve vaší aplikaci logiky pomocí tokenů místo toho. Například pokud chcete použít výstup JSON ze služeb, jako jsou Azure Service Bus a Azure Cosmos DB, můžete tuto akci zahrnout do aplikace logiky, abyste mohli snadněji odkazovat na data v tomto výstupu. 

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*JSON-source*> | Objekt JSON | Obsah JSON, který chcete analyzovat | 
| <*Schéma JSON*> | Objekt JSON | Schéma JSON, které popisuje základní obsah JSON, který akce používá pro analýzu zdrojového obsahu JSON. <p>**Tip**: V Návrháři Logic Apps můžete buď zadat schéma, nebo zadat ukázkovou datovou část, aby tato akce mohla generovat schéma. | 
|||| 

*Příklad*

Tato definice akce vytvoří tyto tokeny, které můžete použít v pracovním postupu, ale pouze v akcích, které se spouštějí po akci **analyzovat JSON** : 

`FirstName`, `LastName`a`Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

V tomto příkladu vlastnost content určuje obsah JSON pro akci, která se má analyzovat. Tento obsah JSON můžete také poskytnout jako ukázkovou datovou část pro generování schématu.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Vlastnost Schema Určuje schéma JSON používané pro popis obsahu JSON:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Akce dotazu

Tato akce vytvoří pole z položek v jiném poli na základě zadané podmínky nebo filtru.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*skupin*> | Array | Pole nebo výraz, který poskytuje zdrojové položky. Pokud zadáte výraz, uzavřete tento výraz do dvojitých uvozovek. |
| <*condition-or-filter*> | Řetězec | Podmínka použitá pro filtrování položek ve zdrojovém poli <p>**Poznámka:** Pokud žádná hodnota nevyhovuje podmínkám, pak akce vytvoří prázdné pole. |
|||| 

*Příklad*

Tato definice akce vytvoří pole, které obsahuje hodnoty větší než zadaná hodnota, která je dvě:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Akce odpovědi  

Tato akce vytvoří datovou část pro odpověď na požadavek HTTP. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*response-status-code*> | Integer | Stavový kód protokolu HTTP, který je odeslán na příchozí požadavek. Výchozí kód je "200 OK", ale kód může být platný stavový kód, který začíná na 2xx, 4xx nebo 5xx, ale ne s 3xxx. | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*response-headers*> | Objekt JSON | Jedna nebo více hlaviček, které mají být zahrnuty do odpovědi | 
| <*text odpovědi*> | Některé | Tělo odpovědi, což může být řetězec, objekt JSON nebo i binární obsah z předchozí akce | 
|||| 

*Příklad*

Tato definice akce vytvoří odpověď na požadavek HTTP se zadaným stavovým kódem, textem zprávy a hlavičkou zpráv:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Podléhající*

Na rozdíl od jiných akcí má akce **reakce** zvláštní omezení: 

* Pracovní postup může použít akci **odpovědi** jenom v případě, že se pracovní postup spustí s triggerem požadavku HTTP, což znamená, že váš pracovní postup musí být aktivovaný požadavkem http.

* Pracovní postup může použít akci **odpovědi** kdekoli *s výjimkou* vnitřních smyček, **do** smyček, včetně sekvenčních smyček a paralelních větví. 

* Původní požadavek HTTP vrátí odpověď pracovního postupu pouze v případě, že všechny akce vyžadované akcí **Response** jsou dokončeny v rámci [limitu požadavku protokolu HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  Pokud ale váš pracovní postup volá jinou aplikaci logiky jako vnořený pracovní postup, nadřazený pracovní postup počká, dokud se nedokončí vnořený pracovní postup, bez ohledu na to, kolik času proběhlo před dokončením vnořeného pracovního postupu.

* Když pracovní postup používá akci **odpovědi** a vzor synchronní odpovědi, pracovní postup nemůže v definici triggeru použít také příkaz **splitOn** , protože tento příkaz vytvoří více spuštění. Pokud je použita metoda PUT a v případě hodnoty true, vyhledejte odpověď "špatný požadavek".

  V opačném případě, pokud váš pracovní postup používá příkaz **splitOn** a akci **odpovědi** , pracovní postup se spustí asynchronně a okamžitě vrátí odpověď "202 přijato".

* Když zpracování pracovního postupu dosáhne akce **odpovědi** , ale příchozí požadavek už odpověď přijal, je akce **odpověď** označená jako "neúspěšná" kvůli konfliktu. A v důsledku toho je vaše spuštění aplikace logiky označeno jako "neúspěšné".

<a name="select-action"></a>

### <a name="select-action"></a>Vybrat akci

Tato akce vytvoří pole s objekty JSON transformací položek z jiného pole na základě zadané mapy. Výstupní pole a zdrojové pole mají vždy stejný počet položek. I když nemůžete změnit počet objektů ve výstupním poli, můžete přidat nebo odebrat vlastnosti a jejich hodnoty mezi těmito objekty. `select` Vlastnost určuje alespoň jednu dvojici klíč-hodnota, která definuje mapu pro transformaci položek ve zdrojovém poli. Pár klíč-hodnota představuje vlastnost a její hodnotu napříč všemi objekty ve výstupním poli. 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Požadováno* 

| Value | type | Popis | 
|-------|------|-------------| 
| <*skupin*> | Array | Pole nebo výraz, který poskytuje zdrojové položky. Ujistěte se, že uzavřete výraz do dvojitých uvozovek. <p>**Poznámka:** Pokud je zdrojové pole prázdné, akce vytvoří prázdné pole. | 
| <*název klíče*> | Řetězec | Název vlastnosti přiřazený výsledku z*výrazu* <> <p>Chcete-li přidat novou vlastnost napříč všemi objekty ve výstupním poli, zadejte < >*název klíče*pro tuto vlastnost a*výraz*< > pro hodnotu vlastnosti. <p>Chcete-li odebrat vlastnost ze všech objektů v poli, vynechejte > <*název klíče*pro tuto vlastnost. | 
| <*vyjádření*> | Řetězec | Výraz, který transformuje položku ve zdrojovém poli a přiřadí výsledek do <ho*názvu klíče*> | 
|||| 

Akce **Select** vytvoří pole jako výstup, takže jakákoli akce, která chce použít tento výstup, musí buď přijmout pole, nebo musíte převést pole na typ, který přijímá akce příjemce. Chcete-li například převést výstupní pole na řetězec, můžete toto pole předat do akce **psaní** a pak odkazovat na výstup z akce **psaní** v dalších akcích.

*Příklad*

Tato definice akce vytvoří pole objektu JSON z celočíselného pole. Akce prochází ze zdrojového pole, získává jednotlivé celočíselné hodnoty pomocí `@item()` výrazu a přiřazuje každou hodnotu vlastnosti "`number`" v každém objektu JSON: 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Toto je pole, které tato akce vytvoří:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Chcete-li použít tento výstup pole v jiných akcích, předejte tento výstup do akce **psaní** :

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Pak můžete použít výstup z akce **psaní** v dalších akcích, například **Office 365 Outlook-odeslat akci e-mailu** :

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Akce tabulky

Tato akce vytvoří z pole tabulku CSV nebo HTML. U polí s objekty JSON Tato akce automaticky vytvoří záhlaví sloupců z názvů vlastností objektů. Pro pole s jinými datovými typy musíte zadat záhlaví sloupců a hodnoty. Například toto pole obsahuje vlastnosti "ID" a "Product_Name", které tato akce může použít pro názvy záhlaví sloupců:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Požadováno* 

| Value | type | Popis | 
|-------|------|-------------| 
| \<> CSV *nebo* HTML| Řetězec | Formát tabulky, kterou chcete vytvořit | 
| <*skupin*> | Array | Pole nebo výraz, který poskytuje zdrojové položky pro tabulku <p>**Poznámka:** Pokud je zdrojové pole prázdné, akce vytvoří prázdnou tabulku. | 
|||| 

*Volitelné*

Chcete-li zadat nebo přizpůsobit záhlaví a hodnoty sloupců, `columns` použijte pole. Pokud `header-value` mají páry stejný název záhlaví, zobrazí se jejich hodnoty ve stejném sloupci pod tímto názvem záhlaví. V opačném případě každá jedinečná hlavička definuje jedinečný sloupec.

| Value | type | Popis | 
|-------|------|-------------| 
| <*název sloupce*> | Řetězec | Název záhlaví sloupce | 
| <*hodnota sloupce*> | Any | Hodnota v tomto sloupci | 
|||| 

*Příklad 1*

Předpokládejme, že máte dříve vytvořenou proměnnou "myItemArray", která aktuálně obsahuje toto pole: 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Tato definice akce vytvoří tabulku CSV z proměnné "myItemArray". Výraz použitý `from` vlastností získá pole z "myItemArray" `variables()` pomocí funkce: 

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Tady je tabulka CSV, kterou tato akce vytvoří: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Příklad 2*

Tato definice akce vytvoří tabulku HTML z proměnné "myItemArray". Výraz použitý `from` vlastností získá pole z "myItemArray" `variables()` pomocí funkce: 

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Tady je tabulka HTML, kterou tato akce vytvoří: 

<table><thead><tr><th>id</th><th>Název produktu</th></tr></thead><tbody><tr><td>0</td><td>Jablk</td></tr><tr><td>1</td><td>Oranges</td></tr></tbody></table>

*Příklad 3*

Tato definice akce vytvoří tabulku HTML z proměnné "myItemArray". Tento příklad však přepíše výchozí názvy hlaviček sloupců "Stock_ID" a "Description" a přidá slovo "organické" do hodnot ve sloupci "Popis".

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Tady je tabulka HTML, kterou tato akce vytvoří: 

<table><thead><tr><th>Stock_ID</th><th>Popis</th></tr></thead><tbody><tr><td>0</td><td>Organické jablka</td></tr><tr><td>1</td><td>Organické pomeranče</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Akce ukončení

Tato akce zastaví běh instance pracovního postupu, zruší všechny probíhající akce, přeskočí všechny zbývající akce a vrátí zadaný stav. Můžete například použít akci **ukončit** , pokud se aplikace logiky musí úplně ukončit z chybového stavu. Tato akce nemá vliv na již dokončené akce a nemůže se objevit uvnitř **foreach** a **do** smyček, včetně sekvenčních smyček. 

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*status*> | Řetězec | Stav, který se má vrátit ke spuštění: "Neúspěšné", "zrušené" nebo "úspěšné" |
|||| 

*Volitelné*

Vlastnosti objektu "runStatus" platí pouze v případě, že vlastnost "runStatus" je nastavena na hodnotu "Failed".

| Value | type | Popis | 
|-------|------|-------------| 
| <*Chyba – kód-nebo-název*> | Řetězec | Kód nebo název chyby |
| <*chyba – zpráva*> | Řetězec | Zpráva nebo text, který popisuje chybu a všechny akce, které může uživatel aplikace provést | 
|||| 

*Příklad*

Tato definice akce zastaví běh pracovního postupu, nastaví stav spuštění na "neúspěšné" a vrátí stav, kód chyby a chybovou zprávu:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Akce čekání  

Tato akce pozastaví provádění pracovního postupu v zadaném intervalu nebo do zadaného času, ale ne obojího. 

*Zadaný interval*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Zadaný čas*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*počet jednotek*> | Integer | Pro akci **zpoždění** počet jednotek, které se mají čekat | 
| <*interval*> | Řetězec | Pro akci **zpoždění** interval čekání: "Druhé", "minuta", "hodina", "den", "týden", "měsíc" | 
| <*date-time-stamp*> | Řetězec | Pro **zpoždění až** do akce, datum a čas, kdy se má pokračovat v provádění. Tato hodnota musí používat [formát data a času UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Příklad 1*

Tato definice akce pozastaví pracovní postup na 15 minut:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Příklad 2*

Tato definice akce pozastaví pracovní postup do zadaného času:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Akce pracovního postupu

Tato akce volá další dříve vytvořenou aplikaci logiky, což znamená, že můžete zahrnout a znovu použít jiné pracovní postupy aplikace logiky. V akcích, které následují po vnořené aplikaci logiky, můžete také použít výstupy z podřízené aplikace logiky, za předpokladu, že podřízená aplikace logiky vrátí odpověď.

Modul Logic Apps kontroluje přístup k triggeru, který chcete volat, takže se ujistěte, že k této aktivační události máte přístup. Také vnořená aplikace logiky musí splňovat tato kritéria:

* Aktivační událost zpřístupňuje vnořenou aplikaci logiky, jako je [požadavek](#request-trigger) nebo Trigger [http](#http-trigger) .

* Stejné předplatné Azure jako vaše nadřazená aplikace logiky

* Chcete-li použít výstupy z vnořené aplikace logiky v nadřazené aplikaci logiky, vnořená aplikace logiky musí mít akci [odpovědi](#response-action) . 

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*nested-logic-app-name*> | Řetězec | Název aplikace logiky, kterou chcete volat | 
| <*trigger-name*> | Řetězec | Název triggeru ve vnořené aplikaci logiky, kterou chcete volat | 
| <*Azure-Subscription-ID*> | Řetězec | ID předplatného Azure pro vnořenou aplikaci logiky |
| <*Azure-resource-group*> | Řetězec | Název skupiny prostředků Azure pro vnořenou aplikaci logiky |
| <*nested-logic-app-name*> | Řetězec | Název aplikace logiky, kterou chcete volat |
||||

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------|  
| <*header-content*> | Objekt JSON | Všechna záhlaví k odeslání pomocí volání | 
| <*body-content*> | Objekt JSON | Veškerý obsah zprávy, který má být odeslán s voláním | 
||||

*Výstupy*

Výstupy této akce se liší v závislosti na akci reakce na vnořenou aplikaci logiky. Pokud vnořená aplikace logiky neobsahuje akci odpovědi, jsou výstupy prázdné.

*Příklad*

Po úspěšném dokončení akce "Start_search" volá Tato definice akce pracovního postupu jinou aplikaci logiky s názvem "Get_product_information", která předává zadané vstupy: 

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Podrobnosti akce kontrolního pracovního postupu

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Akce foreach

Tato akce smyčky projde polem a provede akce u každé položky pole. Ve výchozím nastavení se smyčka "for each" spouští paralelně až do maximálního počtu smyček. Pro toto maximum viz [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Naučte [se vytvářet smyčky for each](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Požadováno* 

| Value | type | Popis | 
|-------|------|-------------| 
| <*akce – 1... n*> | Řetězec | Názvy akcí, které se spouštějí na každé položce pole | 
| <*akce – definice-1... n*> | Objekt JSON | Definice akcí, které se spouštějí | 
| <*for-each-expression*> | Řetězec | Výraz, který odkazuje na každou položku v zadaném poli | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*výpočtu*> | Integer | Ve výchozím nastavení se iterace smyčky for each spouští ve stejnou dobu, nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna pro každou souběžnou smyčku](#change-for-each-concurrency). | 
| <*operace – možnost*> | Řetězec | Chcete-li spustit smyčku "for each" sekvenčně, nikoli paralelně, nastavte buď*možnost < operace-* > na `Sequential` nebo `1`< >*Count*, ale ne obojí. Další informace najdete v tématu [spuštění smyčky for each postupně](#sequential-for-each). | 
|||| 

*Příklad*

Tato smyčka for each pošle e-mail pro každou položku v poli, která obsahuje přílohy z příchozího e-mailu. Smyčka pošle e-mail, včetně přílohy, osobě, která přílohu prověří.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Chcete-li určit pouze pole, které je předáno jako výstup z triggeru, tento výraz získá < pole *-název*> pole z těla triggeru. Chcete-li se vyhnout selhání, pokud pole neexistuje, výraz používá `?` operátor:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Pokud akce

Tato akce, která je podmíněným *příkazem*, vyhodnotí výraz, který představuje podmínku, a spustí jinou větev na základě toho, zda je podmínka pravdivá nebo false. Pokud je podmínka pravdivá, bude podmínka označena stavem "úspěch". Naučte [se vytvářet podmíněné příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Value | type | Popis | 
|-------|------|-------------| 
| <*pomocné*> | Objekt JSON | Podmínka, která může být výrazem, k vyhodnocení | 
| <*akce – 1*> | Objekt JSON | Akce, která se má spustit, když <*podmínka*> vyhodnotí na true | 
| <*definice akce*> | Objekt JSON | Definice akce | 
| <*akce – 2*> | Objekt JSON | Akce, která se má spustit, když <*podmínka*> vyhodnotí na false | 
|||| 

Akce v `actions` objektech nebo `else` získají tyto stavy:

* Úspěch při spuštění a úspěšném dokončení
* Selhání při spuštění a selhání
* "Přeskočeno" při nespuštění příslušné větve

*Příklad*

Tato podmínka Určuje, že když má celočíselná proměnná hodnotu větší než nula, pracovní postup zkontroluje web. Pokud je proměnná nula nebo menší, pracovní postup zkontroluje jiný web.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Jak podmínky používají výrazy

Tady je několik příkladů, které ukazují, jak můžete používat výrazy v podmínkách:
  
| JSON | Výsledek | 
|------|--------| 
| "Expression": "@parameters(' <*hasSpecialAction*> ')" | Pouze pro logické výrazy je podmínka předána pro libovolnou hodnotu, která je vyhodnocena na hodnotu true. <p>Chcete-li převést jiné typy na logické, použijte tyto `empty()` funkce `equals()`: nebo. | 
| "Expression": "@greater(akce (' <*Action*> '). Output. Value; Parameters (' <*prahová hodnota*> ')) | Pro funkce porovnání je akce spuštěna pouze v případě, že výstup z <*action*> je větší než <*prahová*hodnota > Value. | 
| "Expression": "@or(akce (' <*Action*> '). Output. Value, Parameters (' <*prahová hodnota*> ')), less (Actions (' <*stejné akce*> '). Output. Value, 100))" | U logických funkcí a vytváření vnořených logických výrazů se akce spustí, když je výstup z <*action*> větší než <*prahová*hodnota > nebo pod 100. | 
| "Expression": "@equals(délka (Actions (' <*Action*> '). Outputs. Errors)"; 0)) " | Můžete použít funkce pole pro kontrolu, zda pole obsahuje nějaké položky. Akce se spustí, když `errors` je pole prázdné. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Akce oboru

Tato akce logicky seskupuje akce do *oborů*, které po dokončení akcí v daném rozsahu získají svůj vlastní stav. Pak můžete použít stav oboru a určit, zda se spustí jiné akce. Naučte [se vytvářet obory](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------|  
| <*vnitřní akce-1... n*> | Objekt JSON | Jedna nebo více akcí, které se spouštějí v rámci oboru |
| <*akce – vstupy*> | Objekt JSON | Vstupy pro každou akci |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Akce přepnutí

Tato akce, označovaná také jako *příkaz switch*, uspořádá další akce do *případů*a přiřadí hodnotu každému případu s výjimkou výchozího případu, pokud existuje. Když je pracovní postup spuštěn, akce **přepínače** porovná hodnotu z výrazu, objektu nebo tokenu s hodnotami zadanými pro každý případ. Pokud akce **Switch** najde odpovídající případ, bude pracovní postup spouštět pouze akce pro tento případ. Pokaždé, když se spustí akce **Přepnutí** , buď existuje pouze jeden odpovídající případ, nebo žádné shody neexistují. Pokud žádné shody neexistují, akce **Přepnutí** spustí výchozí akce. Naučte [se vytvářet příkazy Switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Požadováno*

| Value | type | Popis | 
|-------|------|-------------| 
| <*expression-object-or-token*> | Různé | Výraz, objekt JSON nebo token k vyhodnocení | 
| <*název akce*> | Řetězec | Název akce, která se má spustit pro případ porovnání | 
| <*definice akce*> | Objekt JSON | Definice akce, která má být spuštěna pro srovnávací případ | 
| <*matching-value*> | Různé | Hodnota, která se má porovnat s vyhodnoceným výsledkem | 
|||| 

*Volitelné*

| Value | type | Popis | 
|-------|------|-------------| 
| <*Výchozí hodnota – název akce*> | Řetězec | Název výchozí akce, která má být spuštěna, pokud neexistuje žádný vyhovující případ | 
| <*výchozí – definice akce*> | Objekt JSON | Definice akce, která má být spuštěna, pokud neexistuje žádný vyhovující případ | 
|||| 

*Příklad*

Tato definice akce vyhodnocuje, zda osoba, která reaguje na e-mail s požadavkem na schválení, vybrala možnost schválit nebo zamítnout. Na základě této volby spustí akce **Switch** akce pro odpovídající případ, což znamená odeslání dalšího e-mailu na respondér, ale s různými slovy v každém případě. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Až do akce

Tato akce smyčky obsahuje akce, které se spustí, dokud není zadaná podmínka pravdivá. Smyčka zkontroluje podmínku jako poslední krok po spuštění všech ostatních akcí. Do `"actions"` objektu můžete zahrnout více než jednu akci a akce musí definovat alespoň jeden limit. Přečtěte si, [jak vytvořit smyčky "do"](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Value | type | Popis | 
|-------|------|-------------| 
| <*název akce*> | Řetězec | Název akce, kterou chcete spustit uvnitř smyčky. | 
| <*Typ akce*> | Řetězec | Typ akce, kterou chcete spustit | 
| <*akce – vstupy*> | Některé | Vstupy pro akci, která se má spustit | 
| <*pomocné*> | Řetězec | Podmínka nebo výraz, který se má vyhodnotit po dokončení všech akcí ve smyčce | 
| <*loop-count*> | Integer | Limit nejvyšší hodnoty smyček, které mohou být akce spuštěny. Výchozí `count` hodnota je 60. | 
| <*loop-timeout*> | Řetězec | Limit v nejdelší době, kdy může být smyčka spuštěna. Výchozí `timeout` hodnota je `PT1H`, což je požadovaný [formát ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Příklad*

Tato definice akce smyčky pošle požadavek HTTP na zadanou adresu URL, dokud není splněna jedna z těchto podmínek: 

* Požadavek obdrží odpověď se stavovým kódem "200 OK".
* Smyčka byla spuštěna 60 krát.
* Smyčka byla spuštěna po dobu jedné hodiny.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooky a předplatná

Triggery a akce založené na Webhooku pravidelně kontrolují koncové body, ale místo toho čekají na konkrétní události nebo data v těchto koncových bodech. Tyto triggery a akce se přihlásí k *odběru* koncových bodů zadáním *adresy URL zpětného volání* , kde koncový bod může odesílat odpovědi.

K `subscribe` volání dojde v případě, že se pracovní postup změní jakýmkoli způsobem, například při obnovení přihlašovacích údajů nebo při změně vstupních parametrů triggeru nebo akce. Toto volání používá stejné parametry jako standardní akce HTTP. 

K `unsubscribe` automatickému vyvolání dojde, když operace provede Trigger nebo akci není platná, například:

* Odstranění nebo zakázání triggeru 
* Odstranění nebo zakázání pracovního postupu. 
* Odstraňuje nebo zakáže odběr. 

Pro podporu těchto volání `@listCallbackUrl()` výraz vrací jedinečnou "adresu URL zpětného volání" pro aktivační událost nebo akci. Tato adresa URL představuje jedinečný identifikátor koncových bodů, které používají REST API služby. Parametry této funkce jsou stejné jako Trigger nebo akce Webhooku.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Změnit asynchronní dobu trvání

Pro aktivační události a akce můžete omezit dobu trvání asynchronního vzoru na určitý časový interval přidáním `limit.timeout` vlastnosti. Tímto způsobem platí, že pokud se akce nedokončila v případě, že uplyne interval, je stav akce `Cancelled` označený jako `ActionTimedOut` kód. Vlastnost používá [formát ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) `timeout` 

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Nastavení konfigurace modulu runtime

Můžete změnit výchozí chování za běhu pro triggery a akce s těmito `runtimeConfiguration` vlastnostmi v definici triggeru nebo akce.

| Vlastnost | Typ | Popis | Aktivační událost nebo akce | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Změňte [*výchozí limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) počtu instancí pracovního postupu, které mohou běžet současně nebo paralelně. Tato hodnota může přispět k omezení počtu požadavků, které systém back-end obdrží. <p>Nastavení vlastnosti na `1` funguje stejnýmzpůsobem`SingleInstance`jako nastavení Vlastnosti.`operationOptions` `runs` Můžete nastavit buď vlastnost, ale ne obojí. <p>Chcete-li změnit výchozí limit, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency) nebo [instancí triggerů](#sequential-trigger). | Všechny aktivační události | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Změňte [*výchozí limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) počtu instancí pracovního postupu, které můžou čekat na spuštění, když váš pracovní postup už běží na maximálním počtu současných instancí. Ve `concurrency.runs` vlastnosti můžete změnit limit souběžnosti. <p>Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | Všechny aktivační události | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Změňte [*výchozí limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) počtu iterací smyčky "for each", které lze spustit současně nebo paralelně. <p>Nastavení vlastnosti na `1` funguje stejnýmzpůsobem`SingleInstance`jako nastavení Vlastnosti.`operationOptions` `repetitions` Můžete nastavit buď vlastnost, ale ne obojí. <p>Chcete-li změnit výchozí limit, přečtěte si téma [Změna "pro každou" souběžnou](#change-for-each-concurrency) "nebo [běh" pro každou "smyčku](#sequential-for-each). | Akce: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Integer | Pro konkrétní akce, které podporují a mají zapnuté stránkování, tato hodnota určuje *minimální* počet výsledků, které se mají načíst. <p>Pokud chcete zapnout stránkování, přečtěte si téma [získání hromadných dat, položek nebo výsledků pomocí stránkování](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) . | Akce: Řadu |
| `runtimeConfiguration.secureData.properties` | Array | U mnoha triggerů a akcí tato nastavení skrývá vstupy, výstupy nebo obojí z historie spuštění aplikace logiky. <p>Chcete-li zabezpečit tato data, přečtěte si téma [skrytí vstupů a výstupů z historie spuštění](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Většina aktivačních událostí a akcí |
| `runtimeConfiguration.staticResult` | Objekt JSON | Pro akce, které podporují a mají nastavení [statického výsledku](../logic-apps/test-logic-apps-mock-data-static-results.md) zapnuto, `staticResult` má objekt tyto atributy: <p>- `name`, který odkazuje na název definice statického výsledku aktuální akce, který se zobrazí uvnitř `staticResults` atributu v `definition` atributu pracovního postupu aplikace logiky. Další informace najdete v tématu [statické výsledky – reference schématu pro jazyk definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, který určuje, zda jsou `Enabled` statické výsledky pro aktuální akci nebo nikoli. <p>Pokud chcete zapnout statické výsledky, přečtěte si téma [test Logic Apps s](../logic-apps/test-logic-apps-mock-data-static-results.md) napodobnou daty nastavením statických výsledků. | Akce: Řadu |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Možnosti operace

Můžete změnit výchozí chování triggerů a akcí pomocí `operationOptions` vlastnosti v definici Trigger nebo Action.

| Možnost operace | type | Popis | Aktivační událost nebo akce | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Řetězec | Spouštějte akce založené na protokolu HTTP synchronně, nikoli asynchronně. <p><p>Chcete-li nastavit tuto možnost, přečtěte si téma [Run Actions synchronně](#asynchronous-patterns). | Činností <p>[Vstupech apiconnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Odpověď](#response-action) | 
| `OptimizedForHighThroughput` | Řetězec | Změňte [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) počtu spuštění akcí za 5 minut na [maximální limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Pokud chcete tuto možnost nastavit, přečtěte si téma [spuštění v režimu vysoké propustnosti](#run-high-throughput-mode). | Všechny akce | 
| `Sequential` | Řetězec | Spouštějte každou iteraci cyklů "for each" v jednom okamžiku, nikoli současně paralelně. <p>Tato možnost funguje stejným způsobem jako nastavení `runtimeConfiguration.concurrency.repetitions` vlastnosti na. `1` Můžete nastavit buď vlastnost, ale ne obojí. <p><p>Chcete-li nastavit tuto možnost, přečtěte si část [spuštění smyčky for each postupně](#sequential-for-each).| Akce: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Řetězec | Spusťte Trigger pro každou instanci aplikace logiky postupně a počkejte na dokončení dříve aktivního běhu a počkejte, než se aktivuje další instance aplikace logiky. <p><p>Tato možnost funguje stejným způsobem jako nastavení `runtimeConfiguration.concurrency.runs` vlastnosti na. `1` Můžete nastavit buď vlastnost, ale ne obojí. <p>Pokud chcete tuto možnost nastavit, přečtěte si téma [instance triggeru postupně](#sequential-trigger). | Všechny aktivační události | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Změna souběžnosti triggeru

Ve výchozím nastavení se instance aplikace logiky spouštějí současně, souběžně nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Proto se každá instance triggeru aktivuje předtím, než se dokončí předchozí instance pracovního postupu. Tento limit pomáhá řídit počet požadavků, které systém back-end obdrží. 

Chcete-li změnit výchozí limit, můžete použít buď editor zobrazení kódu, nebo návrháře Logic Apps, protože Změna nastavení souběžnosti prostřednictvím návrháře přidá nebo aktualizuje `runtimeConfiguration.concurrency.runs` vlastnost v základní definici triggeru a naopak. Tato vlastnost určuje maximální počet instancí pracovních postupů, které mohou běžet paralelně. 

> [!NOTE] 
> Pokud nastavíte Trigger tak, aby běžel sekvenčně buď pomocí návrháře, nebo editoru zobrazení kódu, nenastavte `operationOptions` vlastnost triggeru na `SingleInstance` hodnotu v editoru zobrazení kódu. V opačném případě se zobrazí chyba ověřování. Další informace najdete v tématu [Postup při sekvenčním spuštění instancí](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu 

V definici základní aktivační události přidejte nebo aktualizujte `runtimeConfiguration.concurrency.runs` vlastnost na hodnotu mezi `1` a `50` včetně.

Tady je příklad, který omezí souběžné běhy na 10 instancí:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Upravit v Návrháři Logic Apps

1. V pravém horním rohu triggeru zvolte tlačítko se třemi tečkami (...) a pak zvolte **Nastavení**.

2. V části **řízení souběžnosti**nastavte **omezit** na **zapnuto**. 

3. Přetáhněte jezdec **stupeň paralelismu** na požadovanou hodnotu. Pokud chcete aplikaci logiky spustit sekvenčně, přetáhněte ji na hodnotu **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Změna "pro každou" souběžnost

Ve výchozím nastavení jsou iterace smyčky for each spouštěny současně nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit výchozí limit, můžete použít buď editor zobrazení kódu, nebo návrháře Logic Apps, protože Změna nastavení souběžnosti prostřednictvím návrháře přidá nebo aktualizuje `runtimeConfiguration.concurrency.repetitions` vlastnost v podkladové definici akce "pro každou" a naopak. Tato vlastnost určuje maximální počet iterací, které mohou být spuštěny paralelně.

> [!NOTE] 
> Pokud nastavíte akci "for each" tak, aby se spouštěla sekvenčně buď pomocí návrháře nebo editoru zobrazení kódu, nenastavte `operationOptions` `Sequential` vlastnost Action na Editor zobrazení kódu. V opačném případě se zobrazí chyba ověřování. Další informace najdete v tématu [spuštění smyčky for each postupně](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu 

V podkladové definici "pro každou" přidejte nebo aktualizujte `runtimeConfiguration.concurrency.repetitions` vlastnost na hodnotu mezi `1` a `50` včetně. 

Tady je příklad, který omezí souběžné běhy na 10 iterací:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Upravit v Návrháři Logic Apps

1. V části **pro každou** akci klikněte v pravém horním rohu na tlačítko se třemi tečkami (...) a pak zvolte **Nastavení**.

2. V části **řízení souběžnosti**nastavte **řízení souběžnosti** na **zapnuto**. 

3. Přetáhněte jezdec **stupeň paralelismu** na požadovanou hodnotu. Pokud chcete aplikaci logiky spustit sekvenčně, přetáhněte ji na hodnotu **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Změnit limit čekání na spuštění

Ve výchozím nastavení se instance pracovního postupu aplikace logiky spouštějí současně, souběžně nebo paralelně s [výchozím limitem](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Každá instance triggeru je aktivována dříve, než bude dokončeno spuštění dříve aktivní instance pracovního postupu. I když [Tento výchozí limit můžete změnit](#change-trigger-concurrency), když počet instancí pracovních postupů dosáhne nového limitu souběžnosti, všechny ostatní instance musí počkat na spuštění. 

Počet běhů, které mohou čekat, má také [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits), který lze změnit. Když ale aplikace logiky dosáhne limitu při čekání na spuštění, modul Logic Apps už nepřijímá nová spuštění. Žádosti a Webhook triggery vrátí 429 chyb a opakované triggery začnou pokusy o cyklické dotazování přeskočí.

Chcete-li změnit výchozí limit při čekání na spuštění, v definici základní aktivační události přidejte `runtimeConfiguration.concurency.maximumWaitingRuns` vlastnost s hodnotou mezi `0` a `100`. 

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Postupně triggery instancí

Pokud chcete každou instanci pracovního postupu aplikace logiky spustit až po dokončení předchozí instance, nastavte Trigger tak, aby se spouštěl sekvenčně. Můžete použít buď editor zobrazení kódu, nebo Návrhář Logic Apps, protože Změna nastavení souběžnosti prostřednictvím návrháře také přidá nebo aktualizuje `runtimeConfiguration.concurrency.runs` vlastnost v základní definici triggeru a naopak. 

> [!NOTE] 
> Pokud nastavíte Trigger tak, aby běžel postupně buď pomocí návrháře, nebo editoru zobrazení kódu, nenastavte `operationOptions` vlastnost triggeru na `Sequential` hodnotu v editoru zobrazení kódu. V opačném případě se zobrazí chyba ověřování. 

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu

V definici triggeru nastavte jednu z těchto vlastností, ale ne obojí. 

Nastavte vlastnost na `1`: `runtimeConfiguration.concurrency.runs`

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*ani*

Nastavte vlastnost na `SingleInstance`: `operationOptions`

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Upravit v Návrháři Logic Apps

1. V pravém horním rohu triggeru zvolte tlačítko se třemi tečkami (...) a pak zvolte **Nastavení**.

2. V části **řízení souběžnosti**nastavte **omezit** na **zapnuto**. 

3. Přetáhněte posuvník **stupeň paralelismu** na číslo `1`. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Spuštění smyčky for each postupně

Chcete-li spustit cyklus smyčky "pro každou" až po dokončení předchozí iterace, nastavte akci "pro každý", aby běžela postupně. Můžete použít buď editor zobrazení kódu, nebo návrháře Logic Apps, protože změna souběžnosti akce prostřednictvím návrháře také přidá nebo aktualizuje `runtimeConfiguration.concurrency.repetitions` vlastnost v základní definici akce a naopak. 

> [!NOTE] 
> Když nastavíte akci "for each" tak, aby se spouštěla sekvenčně buď pomocí návrháře nebo editoru zobrazení kódu, nenastavte `operationOptions` vlastnost Action na `Sequential` editor zobrazení kódu. V opačném případě se zobrazí chyba ověřování. 

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu

V definici akce nastavte jednu z těchto vlastností, ale ne obojí. 

Nastavte vlastnost na `1`: `runtimeConfiguration.concurrency.repetitions`

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*ani*

Nastavte vlastnost na `Sequential`: `operationOptions`

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Upravit v Návrháři Logic Apps

1. V pravém horním rohu **pro každou** akci zvolte tlačítko se třemi tečkami (...) a pak zvolte **Nastavení**.

2. V části **řízení souběžnosti**nastavte **řízení souběžnosti** na **zapnuto**. 

3. Přetáhněte posuvník **stupeň paralelismu** na číslo `1`. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Synchronně spustit akce

Ve výchozím nastavení se všechny akce založené na protokolu HTTP řídí standardním vzorem asynchronní operace. Tento vzorec určuje, že když akce založená na protokolu HTTP odešle požadavek na zadaný koncový bod, vzdálený server pošle zpět odpověď "202 PŘIJATÝ". Tato odpověď znamená, že server přijal požadavek na zpracování. Modul Logic Apps uchovává kontrolu adresy URL zadané hlavičkou umístění odpovědi, dokud se zpracování nezastaví, což je jakákoli odpověď, která není 202.

Nicméně požadavky mají časový limit, takže pro dlouhotrvající akce můžete zakázat asynchronní chování přidáním a nastavením `operationOptions` vlastnosti do `DisableAsyncPattern` vstupů akce.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Spustit v režimu vysoké propustnosti

Pro jednu definici aplikace logiky má počet akcí, které se spustí každých 5 minut, [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Chcete-li toto omezení zvýšit na [maximální](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) možnou hodnotu `operationOptions` , nastavte `OptimizedForHighThroughput`vlastnost na hodnotu. Toto nastavení převede aplikaci logiky do režimu vysoké propustnosti. 

> [!NOTE]
> Režim vysoké propustnosti je ve verzi Preview. V případě potřeby můžete také distribuovat úlohy napříč více než jednou aplikací logiky.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>Ověřování aktivačních událostí a akcí HTTP

Koncové body HTTP podporují různé druhy ověřování. Pro tyto triggery a akce HTTP můžete nastavit ověřování:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)

Tady jsou typy ověřování, které můžete nastavit:

* [Základní ověřování](#basic-authentication)
* [Ověřování certifikátu klienta](#client-certificate-authentication)
* [Ověřování OAuth pro Azure Active Directory (Azure AD)](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> Ujistěte se, že jste chránili všechny citlivé informace, které vaše definice pracovního postupu aplikace logiky zpracovává. V případě potřeby používejte zabezpečené parametry a zakódovat data. Další informace o použití a zabezpečení parametrů najdete v tématu [zabezpečení aplikace logiky](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Základní ověřování

Pro [základní ověřování](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) pomocí Azure Active Directory může definice triggeru nebo akce zahrnovat `authentication` objekt JSON, který má vlastnosti určené následující tabulkou. Chcete-li získat přístup k hodnotám parametrů za běhu `@parameters('parameterName')` , můžete použít výraz, který je k dispozici v [jazyce definice pracovního postupu](https://aka.ms/logicappsdocs). 

| Vlastnost | Požadováno | Value | Popis | 
|----------|----------|-------|-------------| 
| **type** | Ano | Basic | Typ ověřování, který má být použit, který je zde "základní" | 
| **uživatelské jméno** | Ano | "@parameters(' userNameParam ')" | Uživatelské jméno pro ověřování přístupu k cílovému koncovému bodu služby |
| **Heslo** | Ano | "@parameters(' passwordParam ')" | Heslo pro ověřování přístupu k cílovému koncovému bodu služby |
||||| 

V tomto příkladu definice `authentication` akce http oddíl Určuje `Basic` ověřování. Další informace o použití a zabezpečení parametrů najdete v tématu [zabezpečení aplikace logiky](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

> [!IMPORTANT]
> Ujistěte se, že jste chránili všechny citlivé informace, které vaše definice pracovního postupu aplikace logiky zpracovává. V případě potřeby používejte zabezpečené parametry a zakódovat data. Další informace o zabezpečení parametrů najdete v tématu [zabezpečení aplikace logiky](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Ověřování certifikátu klienta

Pro [ověřování na základě certifikátů](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) pomocí Azure Active Directory může definice triggeru nebo akce zahrnovat `authentication` objekt JSON, který má vlastnosti určené následující tabulkou. Chcete-li získat přístup k hodnotám parametrů za běhu `@parameters('parameterName')` , můžete použít výraz, který je k dispozici v [jazyce definice pracovního postupu](https://aka.ms/logicappsdocs). Omezení počtu klientských certifikátů, které můžete použít, najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).

| Vlastnost | Požadováno | Value | Popis |
|----------|----------|-------|-------------|
| **type** | Ano | ClientCertificate | Typ ověřování, který se má použít pro SSL (Secure Sockets Layer) (SSL) klientské certifikáty. I když jsou certifikáty podepsané svým držitelem podporovány, certifikáty podepsané svým držitelem pro SSL nejsou podporovány. |
| **pfx** | Ano | "@parameters('pfxParam') | Obsah kódovaný v kódování Base64 ze souboru PFX (Personal Information Exchange) |
| **Heslo** | Ano | "@parameters(' passwordParam ')" | Heslo pro přístup k souboru PFX |
||||| 

V tomto příkladu definice `authentication` akce http oddíl Určuje `ClientCertificate` ověřování. Další informace o použití a zabezpečení parametrů najdete v tématu [zabezpečení aplikace logiky](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Ujistěte se, že jste chránili všechny citlivé informace, které vaše definice pracovního postupu aplikace logiky zpracovává. V případě potřeby používejte zabezpečené parametry a zakódovat data. Další informace o zabezpečení parametrů najdete v tématu [zabezpečení aplikace logiky](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Ověřování OAuth Azure Active Directory (AD)

Pro [ověřování Azure AD OAuth](../active-directory/develop/authentication-scenarios.md)může definice triggeru nebo akce zahrnovat `authentication` objekt JSON, který má vlastnosti určené následující tabulkou. Chcete-li získat přístup k hodnotám parametrů za běhu `@parameters('parameterName')` , můžete použít výraz, který je k dispozici v [jazyce definice pracovního postupu](https://aka.ms/logicappsdocs).

| Vlastnost | Požadováno | Value | Popis |
|----------|----------|-------|-------------|
| **type** | Ano | `ActiveDirectoryOAuth` | Typ ověřování, který se má použít, což je "ActiveDirectoryOAuth" pro Azure AD OAuth |
| **dohled** | Ne | <*URL-for-authority-token-issuer*> | Adresa URL pro autoritu, která poskytuje ověřovací token |
| **tenant** | Ano | <*ID tenanta*> | ID tenanta pro tenanta Azure AD |
| **osoby** | Ano | <*resource-to-authorize*> | Prostředek, který chcete použít pro autorizaci, například`https://management.core.windows.net/` |
| **clientId** | Ano | <*client-ID*> | ID klienta pro aplikaci požadující autorizaci |
| **credentialType** | Ano | "Certifikát" nebo "tajný klíč" | Typ přihlašovacích údajů, který klient používá pro vyžádání autorizace. Tato vlastnost a hodnota se nezobrazí v základní definici, ale určí požadované parametry pro typ přihlašovacích údajů. |
| **pfx** | Ano, pouze pro typ přihlašovacích údajů "certifikát" | "@parameters('pfxParam') | Obsah kódovaný v kódování Base64 ze souboru PFX (Personal Information Exchange) |
| **Heslo** | Ano, pouze pro typ přihlašovacích údajů "certifikát" | "@parameters(' passwordParam ')" | Heslo pro přístup k souboru PFX |
| **secret** | Ano, pouze pro "tajný" typ přihlašovacích údajů | "@parameters(' secretParam ')" | Tajný klíč klienta pro vyžádání autorizace |
|||||

V tomto příkladu definice `authentication` akce http oddíl Určuje `ActiveDirectoryOAuth` ověřování a typ přihlašovacích údajů tajného klíče. Další informace o použití a zabezpečení parametrů najdete v tématu [zabezpečení aplikace logiky](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
         "audience": "https://management.core.windows.net/",
         "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> Ujistěte se, že jste chránili všechny citlivé informace, které vaše definice pracovního postupu aplikace logiky zpracovává. V případě potřeby používejte zabezpečené parametry a zakódovat data. Další informace o zabezpečení parametrů najdete v tématu [zabezpečení aplikace logiky](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

## <a name="next-steps"></a>Další postup

* Další informace o [jazyku definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md)

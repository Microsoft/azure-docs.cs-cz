---
title: Referenční dokumentace schématu pro události typu Trigger a Action
description: Referenční příručka schématu pro Trigger jazyka definice pracovního postupu a typy akcí v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3dbfcacb6ea5922a01d52dfe39189f09f48d4b4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006077"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Referenční příručka schématu pro typy triggerů a akcí v Azure Logic Apps

Tento odkaz popisuje obecné typy používané k identifikaci triggerů a akcí v definici pracovního postupu vaší aplikace logiky, která je popsána a ověřována [jazykem definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md). Pokud chcete najít triggery a akce konkrétního konektoru, které můžete použít ve svých aplikacích logiky, přečtěte si seznam v části [Přehled konektorů](/connectors/).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Přehled aktivačních událostí

Každý pracovní postup zahrnuje Trigger, který definuje volání, která vytvářejí instance a spouštějí pracovní postup. Tady jsou obecné kategorie aktivačních událostí:

* Aktivační událost *cyklického dotazování* , která kontroluje koncový bod služby v pravidelných intervalech

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*Název aktivační události*> | Řetězec | Název triggeru | 
| <*typ triggeru*> | Řetězec | Typ triggeru, například "http" nebo "vstupech apiconnection" | 
| <*aktivační události – vstupy*> | JSON – objekt | Vstupy, které definují chování triggeru | 
| <*Časová jednotka*> | Řetězec | Časová jednotka, která popisuje, jak často se Trigger aktivuje: "druhé", "Minute", "hodina", "den", "týden", "měsíc" | 
| <*počet jednotek v čase*> | Integer | Hodnota, která určuje, jak často se Trigger aktivuje na základě frekvence, což je počet časových jednotek, které se mají počkat, dokud se Trigger znovu nespustí. <p>Tady jsou minimální a maximální intervaly: <p>-Month: 1-16 měsíců </br>Denní: 1-500 dní </br>-Hodina: 1 – 12000 hodin </br>-Minute: 1 – 72000 minut </br>-Sekunda: 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month", opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*pole s podmínkami*> | Pole | Pole, které obsahuje jednu nebo více [podmínek](#trigger-conditions) , které určují, zda má být pracovní postup spuštěn. K dispozici pouze pro aktivační události. | 
| <*Runtime – konfigurace – možnosti*> | JSON – objekt | Můžete změnit chování spuštění triggeru nastavením `runtimeConfiguration` vlastností. Další informace najdete v tématu [nastavení konfigurace modulu runtime](#runtime-config-options). | 
| <*splitOn – výraz*> | Řetězec | U triggerů, které vracejí pole, můžete zadat výraz, který [rozdělí nebo oddělí *debatches*](#split-on-debatch) položky pole na více instancí pracovního postupu pro zpracování. | 
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` Vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Seznam typů triggerů

Každý typ triggeru má jiné rozhraní a vstupy, které definují chování triggeru. 

### <a name="built-in-triggers"></a>Předdefinované aktivační události

| Typ aktivační události | Description | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Zkontroluje nebo provede *dotazování* libovolného koncového bodu. Tento koncový bod musí splňovat konkrétní kontrakt triggeru buď pomocí `202` asynchronního vzoru, nebo vrácením pole. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Vytvoří pro vaši aplikaci logiky volatelné koncové body, ale zavolá zadanou adresu URL k registraci nebo zrušení registrace. |
| [**Opakování**](#recurrence-trigger) | Aktivuje se na základě definovaného plánu. Můžete nastavit budoucí datum a čas pro aktivaci této aktivační události. Na základě frekvence můžete zadat také časy a dny pro spuštění pracovního postupu. | 
| [**Žádost**](#request-trigger)  | Vytvoří pro vaši aplikaci logiky volatelné koncové body, které se taky označuje jako Trigger "ruční". Podívejte se například na [pracovní postupy volání, Trigger nebo vnořování s koncovými body http](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Spravované triggery rozhraní API

| Typ aktivační události | Description | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Kontroluje nebo *dotazuje* koncový bod pomocí [rozhraní API spravovaných Microsoftem](../connectors/apis-list.md). | 
| [**Vstupech apiconnectionwebhook**](#apiconnectionwebhook-trigger) | Vytvoří pro vaši aplikaci logiky volatelné koncové body voláním [rozhraní API spravovaných Microsoftem](../connectors/apis-list.md) pro přihlášení k odběru a zrušení odběru. | 
||| 

## <a name="triggers---detailed-reference"></a>Triggery – Podrobné referenční informace

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

*Povinné*

| Hodnota | Typ | Description |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Řetězec | Název triggeru |
| <*název připojení*> | Řetězec | Název připojení ke spravovanému rozhraní API, které používá pracovní postup |
| <*typ metody*> | Řetězec | Metoda HTTP pro komunikaci se spravovaným rozhraním API: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*rozhraní API – operace*> | Řetězec | Operace rozhraní API, která se má volat |
| <*Časová jednotka*> | Řetězec | Časová jednotka, která popisuje, jak často se Trigger aktivuje: "druhé", "Minute", "hodina", "den", "týden", "měsíc" |
| <*počet jednotek v čase*> | Integer | Hodnota, která určuje, jak často se Trigger aktivuje na základě frekvence, což je počet časových jednotek, které se mají počkat, dokud se Trigger znovu nespustí. <p>Tady jsou minimální a maximální intervaly: <p>-Month: 1-16 měsíců </br>Denní: 1-500 dní </br>-Hodina: 1 – 12000 hodin </br>-Minute: 1 – 72000 minut </br>-Sekunda: 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month", opakování je každých 6 měsíců. |
||||

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*opakování – chování*> | JSON – objekt | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*dotaz – parametry*> | JSON – objekt | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*Max – běhy*> | Integer | Ve výchozím nastavení se instance pracovních postupů spouští ve stejnou dobu (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*Max-běhy-Queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*splitOn – výraz*> | Řetězec | Pro aktivační události, které vracejí pole, tento výraz odkazuje na pole, které se má použít, abyste mohli vytvořit a spustit instanci pracovního postupu pro každou položku pole, a ne použít smyčku For Each. <p>Tento výraz například reprezentuje položku v poli vrácené v obsahu těla triggeru: `@triggerbody()?['value']` |
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` Vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). |
||||

*Výstupy*
 
| Prvek | Typ | Description |
|---------|------|-------------|
| záhlaví | JSON – objekt | Hlavičky z odpovědi |
| text | JSON – objekt | Tělo odpovědi |
| stavový kód | Integer | Stavový kód z odpovědi |
|||| 

*Příklad*

Tato definice triggeru kontroluje každý den v doručené poště pracovní nebo školní účet:

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*název připojení*> | Řetězec | Název připojení ke spravovanému rozhraní API, které používá pracovní postup | 
| <*text obsahu*> | JSON – objekt | Veškerý obsah zprávy, který se odešle jako datová část spravovanému rozhraní API | 
||||

*Volitelné*

| Hodnota | Typ | Description |
|-------|------|-------------|
| <*opakování – chování*> | JSON – objekt | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*dotaz – parametry*> | JSON – objekt | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. |
| <*Max – běhy*> | Integer | Ve výchozím nastavení se instance pracovních postupů spouští ve stejnou dobu (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). |
| <*Max-běhy-Queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*splitOn – výraz*> | Řetězec | Pro aktivační události, které vracejí pole, tento výraz odkazuje na pole, které se má použít, abyste mohli vytvořit a spustit instanci pracovního postupu pro každou položku pole, a ne použít smyčku For Each. <p>Tento výraz například reprezentuje položku v poli vrácené v obsahu těla triggeru: `@triggerbody()?['value']` |
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` Vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
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

### <a name="http-trigger"></a>HTTP trigger

Tato aktivační událost pošle požadavek na zadaný koncový bod HTTP nebo HTTPS na základě zadaného plánu opakování. Aktivační událost pak zkontroluje odezvu a určí, zda je pracovní postup spuštěn. Další informace najdete v tématu [volání koncových bodů služby pomocí protokolu HTTP nebo HTTPS z Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
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

*Povinné*

| Vlastnost | Hodnota | Typ | Description |
|----------|-------|------|-------------|
| `method` | <*typ metody*> | Řetězec | Metoda, která se má použít pro odeslání odchozí žádosti: "GET", "PUT", "POST", "PATCH" nebo "DELETE" |
| `uri` | <*HTTP-nebo-HTTPS-Endpoint-URL*> | Řetězec | Adresa URL koncového bodu HTTP nebo HTTPS, kam chcete odeslat odchozí požadavek. Maximální velikost řetězce: 2 KB <p>V případě služby nebo prostředku Azure Tato syntaxe identifikátoru URI zahrnuje ID prostředku a cestu k prostředku, ke kterému chcete získat přístup. |
| `frequency` | <*Časová jednotka*> | Řetězec | Časová jednotka, která popisuje, jak často se Trigger aktivuje: "druhé", "Minute", "hodina", "den", "týden", "měsíc" |
| `interval` | <*počet jednotek v čase*> | Integer | Hodnota, která určuje, jak často se Trigger aktivuje na základě frekvence, což je počet časových jednotek, které se mají počkat, dokud se Trigger znovu nespustí. <p>Tady jsou minimální a maximální intervaly: <p>-Month: 1-16 měsíců </br>Denní: 1-500 dní </br>-Hodina: 1 – 12000 hodin </br>-Minute: 1 – 72000 minut </br>-Sekunda: 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month", opakování je každých 6 měsíců. |
|||||

*Volitelné*

| Vlastnost | Hodnota | Typ | Description |
|----------|-------|------|-------------|
| `headers` | <*Hlavička-obsah*> | JSON – objekt | Libovolná záhlaví, která je potřeba zahrnout do žádosti <p>Chcete-li například nastavit jazyk a typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*dotaz – parametry*> | JSON – objekt | Všechny parametry dotazů, které je třeba v žádosti použít <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do žádosti. |
| `body` | <*text obsahu*> | JSON – objekt | Obsah zprávy, která má být odeslána jako datová část s požadavkem |
| `authentication` | <*hodnoty typu ověřování a vlastností*> | JSON – objekt | Model ověřování, který požadavek používá pro ověřování odchozích požadavků. Další informace najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Kromě Scheduleru `authority` je tato vlastnost podporovaná. Pokud není zadaný, použije se výchozí hodnota `https://management.azure.com/` , ale můžete použít jinou hodnotu. |
| `retryPolicy` > `type` | <*opakování – chování*> | JSON – objekt | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*Max – běhy*> | Integer | Ve výchozím nastavení se instance pracovních postupů spouští ve stejnou dobu (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*Max-běhy-Queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). |
| `operationOptions` | <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` Vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). |
|||||

*Výstupy*

| Prvek | Typ | Description |
|---------|------|-------------|
| `headers` | JSON – objekt | Hlavičky z odpovědi |
| `body` | JSON – objekt | Tělo odpovědi |
| `status code` | Integer | Stavový kód z odpovědi |
||||

*Požadavky na příchozí požadavky*

Aby bylo možné v aplikaci logiky dobře fungovat, musí koncový bod splňovat konkrétní vzor triggeru nebo kontrakt a rozpoznat tyto vlastnosti odpovědi:

| Vlastnost | Povinné | Popis |
|----------|----------|-------------|
| Stavový kód | Yes | Stavový kód "200 OK" spustí spuštění. Jakýkoli jiný stavový kód nespustí běh. |
| Záhlaví opakování – za | No | Počet sekund do opětovného dotazování koncového bodu aplikace logiky |
| Hlavička umístění | No | Adresa URL, která má být volána při dalším intervalu dotazování. Pokud není zadaný, použije se původní adresa URL. |
|||| 

*Příklad chování pro různé požadavky*

| Stavový kód | Opakovat po | Chování | 
|-------------|-------------|----------|
| 200 | nTato | Spusťte pracovní postup a pak znovu zkontrolujte další data po definovaném opakování. | 
| 200 | 10 sekund | Spusťte pracovní postup a potom se znovu podívejte na další data po 10 sekundách. |  
| 202 | 60 sekund | Neaktivujte pracovní postup. K dalšímu pokusu dochází za jednu minutu v závislosti na definovaném opakování. Pokud je definované opakování méně než jedna minuta, má přednost záhlaví opakování. V opačném případě se použije definované opakování. | 
| 400 | nTato | Chybný požadavek, nespouštějte pracovní postup. Pokud `retryPolicy` není definován, použije se výchozí zásada. Po dosažení počtu opakovaných pokusů aktivační událost znovu zkontroluje data po definovaném opakování. | 
| 500 | nTato| Chyba serveru, nespouštějte pracovní postup. Pokud `retryPolicy` není definován, použije se výchozí zásada. Po dosažení počtu opakovaných pokusů aktivační událost znovu zkontroluje data po definovaném opakování. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Aktivační událost HTTPWebhook  

Tato aktivační událost umožňuje, aby se aplikace logiky volala vytvořením koncového bodu, který může zaregistrovat odběr voláním zadané adresy URL koncového bodu. Při vytváření této aktivační události v pracovním postupu odchozí požadavek provede volání registrace předplatného. Tímto způsobem může Trigger začít naslouchat událostem. Pokud operace neprovede tuto aktivační událost jako neplatnou, odchozí žádost automaticky provede volání, aby zrušilo odběr. Další informace najdete v tématu [odběry koncových bodů](#subscribe-unsubscribe).

Můžete také zadat [asynchronní omezení](#asynchronous-limits) pro aktivační událost **HTTPWebhook** . Chování triggeru závisí na oddílech, které použijete nebo vynecháte.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
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

Některé hodnoty, například <*metody typu*>, jsou k dispozici pro `"subscribe"` `"unsubscribe"` objekty a.

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro požadavek předplatného: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*koncový bod – přihlášení k odběru – adresa URL*> | Řetězec | Adresa URL koncového bodu, kam se má odeslat žádost o předplatné | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro žádost o zrušení: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*koncový bod – zrušení odběru adresy URL*> | Řetězec | Adresa URL koncového bodu, kam se má odeslat žádost o zrušení | 
| <*text obsahu*> | Řetězec | Veškerý obsah zprávy pro odeslání v rámci předplatného nebo žádosti o zrušení | 
| <*typ ověřování*> | JSON – objekt | Model ověřování, který požadavek používá pro ověřování odchozích požadavků. Další informace najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*opakování – chování*> | JSON – objekt | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*Max – běhy*> | Integer | Ve výchozím nastavení jsou instance pracovních postupů všechny spouštěny současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*Max-běhy-Queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` Vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
|||| 

*Výstupy* 

| Prvek | Typ | Description |
|---------|------|-------------| 
| záhlaví | JSON – objekt | Hlavičky z odpovědi | 
| text | JSON – objekt | Tělo odpovědi | 
| stavový kód | Integer | Stavový kód z odpovědi | 
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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*Časová jednotka*> | Řetězec | Časová jednotka, která popisuje, jak často se Trigger aktivuje: "druhé", "Minute", "hodina", "den", "týden", "měsíc" | 
| <*počet jednotek v čase*> | Integer | Hodnota, která určuje, jak často se Trigger aktivuje na základě frekvence, což je počet časových jednotek, které se mají počkat, dokud se Trigger znovu nespustí. <p>Tady jsou minimální a maximální intervaly: <p>-Month: 1-16 měsíců </br>Denní: 1-500 dní </br>-Hodina: 1 – 12000 hodin </br>-Minute: 1 – 72000 minut </br>-Sekunda: 1 – 9999999 sekund<p>Pokud má například interval hodnotu 6 a frekvence je "Month", opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*počáteční – datum a čas – ve formátu-RRRR-MM-DDThh: mm: SS*> | Řetězec | Počáteční datum a čas v tomto formátu: <p>RRRR-MM-DDThh: mm: SS Pokud zadáte časové pásmo <p>-nebo- <p>RRRR-MM-DDThh: mm: ssZ, pokud nezadáte časové pásmo <p>Pokud například požadujete 18. září 2017 na 2:00 odp., zadejte "2017-09-18T14:00:00" a zadejte časové pásmo, například "Tichomoří (běžný čas"), nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas má v budoucnosti maximálně 49 let a musí následovat za [specifikací data a času ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)a času UTC, ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Pokud nezadáte časové pásmo, je nutné na konci přidat písmeno "Z" bez mezer. Tento "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). <p>V případě jednoduchých plánů je počáteční čas prvním výskytem, ale u složitých plánů se Trigger neaktivuje dříve, než je čas spuštění. Další informace o počátečních datech a časech najdete v tématu [vytváření a plánování pravidelného spouštění úloh](../connectors/connectors-native-recurrence.md). | 
| <*časové pásmo*> | Řetězec | Platí pouze v případě, že zadáte čas spuštění, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Určete časové pásmo, které chcete použít. | 
| <*jedna nebo více hodin*> | Celočíselné nebo celočíselné pole | Pokud zadáte "Day" nebo "Week" pro `frequency` , můžete zadat jedno nebo více celých čísel od 0 do 23, které jsou odděleny čárkami, jako hodiny dne, kdy chcete pracovní postup spustit. <p>Pokud například zadáte "10", "12" a "14", dostanete jako hodiny 10 DOP, 12 ODP a 2 ODP. | 
| <*jedna nebo více minut – známky*> | Celočíselné nebo celočíselné pole | Pokud zadáte "Day" nebo "Week" pro `frequency` , můžete zadat jedno nebo více celých čísel od 0 do 59, které jsou odděleny čárkami, jako minuty hodiny, kdy chcete pracovní postup spustit. <p>Například můžete zadat "30" jako znak minuty a použít předchozí příklad pro hodiny dne, získáte 10:30 dop. 12:30 ODP. a 2:30 PM. | 
| weekDays | Řetězec nebo pole řetězců | Pokud zadáte "Week" pro `frequency` , můžete zadat jeden nebo více dní, které jsou odděleny čárkami, pokud chcete spustit pracovní postup: "pondělí", "úterý", "Středa", "čtvrtek", "pátek", "Sobota" a "neděle" | 
| <*Max – běhy*> | Integer | Ve výchozím nastavení jsou instance pracovních postupů všechny spouštěny současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*Max-běhy-Queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` Vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
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

Chcete-li zavolat tuto aktivační událost, je nutné použít `listCallbackUrl` rozhraní API, které je popsáno v [REST API služby pracovního postupu](/rest/api/logic/workflows). Informace o tom, jak tento Trigger použít jako koncový bod HTTP, najdete v tématu [pracovní postupy volání, triggeru nebo vnoření pracovních postupů s koncovými body http](../logic-apps/logic-apps-http-endpoint.md).

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*vlastnost – název*> | Řetězec | Název vlastnosti ve schématu JSON, která popisuje datovou část | 
| <*typ vlastnosti*> | Řetězec | Typ vlastnosti | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda, kterou příchozí požadavky musí použít k volání aplikace logiky: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*relativní – cesta-pro-přijatý parametr*> | Řetězec | Relativní cesta k parametru, který může adresa URL koncového bodu přijmout | 
| <*požadované – vlastnosti*> | Pole | Jedna nebo více vlastností, které vyžadují hodnoty | 
| <*Max – běhy*> | Integer | Ve výchozím nastavení jsou instance pracovních postupů všechny spouštěny současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency). | 
| <*Max-běhy-Queue*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které lze změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, budou všechny nové běhy vloženy do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | 
| <*operace – možnost*> | Řetězec | Výchozí chování můžete změnit nastavením `operationOptions` Vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
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

## <a name="trigger-conditions"></a>Podmínky triggeru

Pro všechny triggery a triggery můžete zahrnout pole obsahující jeden nebo více výrazů pro podmínky, které určují, jestli se má pracovní postup spustit. Chcete-li přidat `conditions` vlastnost do triggeru v pracovním postupu, otevřete aplikaci logiky v editoru zobrazení kódu.

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

## <a name="trigger-multiple-runs"></a>Aktivace více spuštění

Pokud aktivační událost vrátí pole pro zpracování vaší aplikace logiky, může někdy trvat příliš dlouho smyčka "for each", aby bylo možné zpracovat každou položku pole. Místo toho můžete použít vlastnost **SplitOn** ve triggeru k *oddávkování* pole. Při dedávkování se položky pole rozdělí a spustí se nová instance pracovního postupu, která se spustí pro každou položku pole. Tento přístup je užitečný například v případě, že chcete dotazovat koncový bod, který může vracet více nových položek mezi intervaly dotazování. V případě maximálního počtu položek pole, které **SplitOn** může zpracovat v jednom spuštění aplikace logiky, viz [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> **SplitOn** se nedá použít se vzorem synchronní odpovědi. Libovolný pracovní postup, který používá **SplitOn** a zahrnuje akci odpovědi, se spouští asynchronně a okamžitě pošle `202 ACCEPTED` odpověď.
>
> Pokud je povolená souběžnost triggeru, [limit SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) se výrazně sníží. Pokud počet položek překročí tento limit, funkce SplitOn je zakázaná.
 
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

Aplikace logiky potřebuje jenom obsah z pole v `Rows` , takže můžete vytvořit Trigger podobný tomuto příkladu:

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
> Chcete-li se vyhnout selhání `Rows` , pokud vlastnost neexistuje, tento příklad používá `?` operátor.

Vaše definice pracovního postupu teď může použít `@triggerBody().name` k získání `name` hodnot, které jsou `"customer-name-one"` z prvního spuštění a `"customer-name-two"` z druhého spuštění. Výstupy triggerů tak vypadají jako tyto příklady:

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

Azure Logic Apps poskytuje různé typy akcí – každý s různými vstupy, které definují jedinečné chování akce. Akce mají tyto prvky na nejvyšší úrovni, i když jsou některé volitelné:

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------|
| <*název akce*> | Řetězec | Název akce | 
| <*Typ akce*> | Řetězec | Typ akce, například "http" nebo "vstupech apiconnection"| 
| <*Název vstupu*> | Řetězec | Název vstupu, který definuje chování akce | 
| <*Hodnota vstupu*> | Některé | Vstupní hodnota, která může být řetězec, celé číslo, objekt JSON atd. | 
| <*předchozí – Trigger nebo-Action-status*> | JSON – objekt | Název a výsledný stav triggeru nebo akce, které se musí spustit bezprostředně před spuštěním této aktuální akce | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------|
| <*opakování – chování*> | JSON – objekt | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu Zásady opakování. | 
| <*Runtime – konfigurace – možnosti*> | JSON – objekt | U některých akcí můžete chování akce v době běhu změnit nastavením `runtimeConfiguration` vlastností. Další informace najdete v tématu [nastavení konfigurace modulu runtime](#runtime-config-options). | 
| <*operace – možnost*> | Řetězec | U některých akcí můžete změnit výchozí chování nastavením `operationOptions` Vlastnosti. Další informace najdete v tématu [Možnosti operací](#operation-options). | 
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

| Typ akce | Description | 
|-------------|-------------| 
| [**Vytvořit**](#compose-action) | Vytvoří jeden výstup ze vstupů, které mohou mít různé typy. | 
| [**Spustit JavaScriptový kód**](#run-javascript-code) | Spustí fragmenty kódu JavaScriptu, které se vejdou do konkrétních kritérií. Požadavky na kód a další informace naleznete v tématu [Přidání a spuštění fragmentů kódu s vloženým kódem](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Slouží**](#function-action) | Zavolá funkci Azure Function. | 
| [**HTTP**](#http-action) | Volá koncový bod HTTP. | 
| [**Spojit**](#join-action) | Vytvoří řetězec ze všech položek v poli a oddělí tyto položky zadaným znakem oddělovače. | 
| [**Analyzovat JSON**](#parse-json-action) | Vytvoří uživatelsky přívětivé tokeny z vlastností v obsahu JSON. Pak můžete odkazovat na tyto vlastnosti zahrnutím tokenů do aplikace logiky. | 
| [**Dotaz**](#query-action) | Vytvoří pole z položek v jiném poli na základě podmínky nebo filtru. | 
| [**Odpověď**](#response-action) | Vytvoří odpověď na příchozí volání nebo požadavek. | 
| [**Vyberte**](#select-action) | Vytvoří pole s objekty JSON transformací položek z jiného pole na základě zadané mapy. | 
| [**Stolní**](#table-action) | Vytvoří z pole tabulku CSV nebo HTML. | 
| [**Terminate (Ukončení)**](#terminate-action) | Zastaví aktivně běžící pracovní postup. | 
| [**Počkej**](#wait-action) | Pozastaví pracovní postup na určitou dobu nebo do zadaného data a času. | 
| [**Pracovní postup**](#workflow-action) | Vnoření pracovního postupu do jiného pracovního postupu. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Spravované akce rozhraní API

| Typ akce | Description | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Volá koncový bod HTTP pomocí [rozhraní API spravovaného Microsoftem](../connectors/apis-list.md). | 
| [**Vstupech apiconnectionwebhook**](#apiconnectionwebhook-action) | Funguje jako Webhook HTTP, ale používá [rozhraní API spravované Microsoftem](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Řízení akcí pracovního postupu

Tyto akce vám pomůžou řídit spouštění pracovních postupů a zahrnovat další akce. Mimo akci pracovního postupu ovládacího prvku můžete přímo odkazovat na akce v rámci akce pracovního postupu ovládacího prvku. Například pokud máte `Http` v oboru nějakou akci, můžete odkazovat na `@body('Http')` výraz z libovolného místa v pracovním postupu. Akce, které existují v akci pracovního postupu řízení, ale mohou být spouštěny pouze za jinými akcemi, které jsou ve stejné struktuře pracovního postupu řízení.

| Typ akce | Description | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Spustí stejné akce ve smyčce pro každou položku v poli. | 
| [**Přestože**](#if-action) | Spustí akce na základě toho, jestli je zadaná podmínka pravdivá, nebo false. | 
| [**Rozsah**](#scope-action) | Spustí akce založené na stavu skupiny ze sady akcí. | 
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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*název akce*> | Řetězec | Název akce poskytované konektorem | 
| <*rozhraní API – název*> | Řetězec | Název rozhraní API spravovaného Microsoftem používaného pro připojení | 
| <*typ metody*> | Řetězec | Metoda HTTP pro volání rozhraní API: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*rozhraní API – operace*> | Řetězec | Operace rozhraní API, která se má volat | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*jiné – specifické pro akce-vstup – vlastnosti*> | JSON – objekt | Všechny další vstupní vlastnosti, které se vztahují na tuto konkrétní akci | 
| <*opakování – chování*> | JSON – objekt | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*dotaz – parametry*> | JSON – objekt | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*jiné – vlastnosti specifické pro akci*> | JSON – objekt | Všechny další vlastnosti, které se vztahují na tuto konkrétní akci | 
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
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Některé hodnoty, například <*metody typu*>, jsou k dispozici pro `"subscribe"` `"unsubscribe"` objekty a.

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*název akce*> | Řetězec | Název akce poskytované konektorem | 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít k přihlášení k odběru nebo odhlášení odběru koncového bodu: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*API – přihlášení k odběru – adresa URL*> | Řetězec | Identifikátor URI, který se má použít pro přihlášení k odběru rozhraní API | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*rozhraní API – zrušení odběru adresy URL*> | Řetězec | Identifikátor URI, který se má použít k odhlášení odběru rozhraní API | 
| <*Hlavička-obsah*> | JSON – objekt | Libovolná záhlaví, která se mají poslat v žádosti <p>Například chcete-li nastavit jazyk a typ pro požadavek: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*text obsahu*> | JSON – objekt | Veškerý obsah zprávy, který se má odeslat v žádosti | 
| <*typ ověřování*> | JSON – objekt | Model ověřování, který požadavek používá pro ověřování odchozích požadavků. Další informace najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*opakování – chování*> | JSON – objekt | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*dotaz – parametry*> | JSON – objekt | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*jiné – specifické pro akce-vstup – vlastnosti*> | JSON – objekt | Všechny další vstupní vlastnosti, které se vztahují na tuto konkrétní akci | 
| <*jiné – vlastnosti specifické pro akci*> | JSON – objekt | Všechny další vlastnosti, které se vztahují na tuto konkrétní akci | 
|||| 

Omezení pro akci **vstupech apiconnectionwebhook** můžete zadat také stejným způsobem jako [asynchronní limity protokolu HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Akce psaní

Tato akce vytvoří jeden výstup z více vstupů, včetně výrazů. Výstup i vstupy mohou mít jakýkoli typ, který Azure Logic Apps nativně podporuje, například pole, objekty JSON, XML a binární soubory. Výstup akce pak můžete použít v jiných akcích. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Povinné* 

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*vstupy do sestavení*> | Libovolný | Vstupy pro vytvoření jednoho výstupu | 
|||| 

*Příklad 1*

<!-- markdownlint-disable MD038 -->
Tato definice akce se sloučí `abcdefg ` s koncovým místem a hodnotou `1234` :
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

Tato definice akce slučuje řetězcovou proměnnou, která obsahuje, `abcdefg` a celočíselnou proměnnou, která obsahuje `1234` :

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

Tato akce spustí fragment kódu JavaScriptu a vrátí výsledky prostřednictvím `Result` tokenu, který mohou později odkazovat na jiné akce.

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

*Povinné*

| Hodnota | Typ | Description |
|-------|------|-------------|
| <*JavaScript – fragment kódu*> | Různé | Kód JavaScriptu, který chcete spustit. Požadavky na kód a další informace naleznete v tématu [Přidání a spuštění fragmentů kódu s vloženým kódem](../logic-apps/logic-apps-add-run-inline-code.md). <p>V `code` atributu může fragment kódu jako vstup použít objekt jen pro čtení `workflowContext` . Tento objekt má podvlastnost, které přidávají vašemu kódu přístup k výsledkům triggeru a předchozím akcím v pracovním postupu. Další informace o `workflowContext` objektu naleznete [v tématu Referenční Trigger a výsledky akcí ve vašem kódu](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Vyžadováno v některých případech*

`explicitDependencies`Atribut určuje, že chcete explicitně zahrnout výsledky z triggeru, předchozí akce nebo obojí jako závislosti pro váš fragment kódu. Další informace o přidání těchto závislostí naleznete v tématu [Add Parameters for a line Code](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Pro `includeTrigger` atribut můžete zadat `true` nebo `false` hodnoty.

| Hodnota | Typ | Description |
|-------|------|-------------|
| <*předchozí akce*> | Pole řetězců | Pole se zadanými názvy akcí Použijte názvy akcí, které se zobrazí v definici pracovního postupu, kde názvy akcí používají podtržítka (_), ne mezery (""). |
||||

*Příklad 1*

Tato akce spustí kód, který získá název vaší aplikace logiky a vrátí text "Hello World from \<logic-app-name> " jako výsledek. V tomto příkladu kód odkazuje na název pracovního postupu pomocí přístupu k `workflowContext.workflow.name` vlastnosti prostřednictvím objektu jen pro čtení `workflowContext` . Další informace o používání `workflowContext` objektu naleznete [v tématu Referenční Trigger a výsledky akcí ve vašem kódu](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

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

Tato akce spustí kód v aplikaci logiky, která se aktivuje při přijetí nového e-mailu v pracovním nebo školním účtu. Aplikace logiky také používá akci odeslání e-mailu pro schválení, která přesměruje obsah z přijatého e-mailu spolu se žádostí o schválení.

Kód extrahuje e-mailové adresy z vlastnosti triggeru `Body` a vrátí adresy spolu s `SelectedOption` hodnotou vlastnosti z akce schválení. Akce explicitně zahrnuje akci odeslání e-mailu o schválení jako závislost v `explicitDependencies`  >  `actions` atributu.

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------|  
| <*Azure-Function-ID*> | Řetězec | ID prostředku pro funkci Azure, kterou chcete volat. Zde je formát pro tuto hodnotu:<p>"/Subscriptions/<*Azure-Subscription-ID*>/ResourceGroups/<*Azure-resource-group*>/Providers/Microsoft.Web/Sites/<*Azure-function-app-Name*>/Functions/<*Azure-Function-Name*>" | 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro volání funkce: "GET", "PUT", "POST", "PATCH" nebo "DELETE" <p>Pokud není zadán, výchozí hodnota je metoda "POST". | 
||||

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------|  
| <*Hlavička-obsah*> | JSON – objekt | Všechna záhlaví k odeslání pomocí volání <p>Například chcete-li nastavit jazyk a typ pro požadavek: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*text obsahu*> | JSON – objekt | Veškerý obsah zprávy, který se má odeslat v žádosti | 
| <*dotaz – parametry*> | JSON – objekt | Všechny parametry dotazu, které se mají zahrnout do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*jiné – specifické pro akce-vstup – vlastnosti*> | JSON – objekt | Všechny další vstupní vlastnosti, které se vztahují na tuto konkrétní akci | 
| <*jiné – vlastnosti specifické pro akci*> | JSON – objekt | Všechny další vlastnosti, které se vztahují na tuto konkrétní akci | 
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

Tato akce odešle požadavek na zadaný koncový bod HTTP nebo HTTPS a zkontroluje odpověď, aby zjistil, zda pracovní postup běží. Další informace najdete v tématu [volání koncových bodů služby pomocí protokolu HTTP nebo HTTPS z Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Povinné*

| Vlastnost | Hodnota | Typ | Description |
|----------|-------|------|-------------|
| `method` | <*typ metody*> | Řetězec | Metoda, která se má použít pro odeslání odchozí žádosti: "GET", "PUT", "POST", "PATCH" nebo "DELETE" |
| `uri` | <*HTTP-nebo-HTTPS-Endpoint-URL*> | Řetězec | Adresa URL koncového bodu HTTP nebo HTTPS, kam chcete odeslat odchozí požadavek. Maximální velikost řetězce: 2 KB <p>V případě služby nebo prostředku Azure Tato syntaxe identifikátoru URI zahrnuje ID prostředku a cestu k prostředku, ke kterému chcete získat přístup. |
|||||

*Volitelné*

| Vlastnost | Hodnota | Typ | Description |
|----------|-------|------|-------------|
| `headers` | <*Hlavička-obsah*> | JSON – objekt | Libovolná záhlaví, která je potřeba zahrnout do žádosti <p>Chcete-li například nastavit jazyk a typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*dotaz – parametry*> | JSON – objekt | Všechny parametry dotazů, které je třeba v žádosti použít <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. |
| `body` | <*text obsahu*> | JSON – objekt | Obsah zprávy, která má být odeslána jako datová část s požadavkem |
| `authentication` | <*hodnoty typu ověřování a vlastností*> | JSON – objekt | Model ověřování, který požadavek používá pro ověřování odchozích požadavků. Další informace najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Kromě Scheduleru `authority` je tato vlastnost podporovaná. Pokud není zadaný, použije se výchozí hodnota `https://management.azure.com/` , ale můžete použít jinou hodnotu. |
| `retryPolicy` > `type` | <*opakování – chování*> | JSON – objekt | Přizpůsobuje chování opakování pro přerušované výpadky, které mají stavový kód 408, 429 a 5XX, a jakékoli výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*jiné – specifické pro akce-vstup – vlastnosti*> | <*vstupní vlastnost*> | JSON – objekt | Všechny další vstupní vlastnosti, které se vztahují na tuto konkrétní akci |
| <*jiné – vlastnosti specifické pro akci*> | <*hodnota vlastnosti*> | JSON – objekt | Všechny další vlastnosti, které se vztahují na tuto konkrétní akci |
|||||

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*skupin*> | Pole | Pole nebo výraz, který poskytuje zdrojové položky. Pokud zadáte výraz, uzavřete tento výraz do dvojitých uvozovek. | 
| <*oddělovač*> | Řetězec s jedním znakem | Znak, který odděluje každou položku v řetězci | 
|||| 

*Příklad*

Předpokládejme, že máte dříve vytvořenou proměnnou "myIntegerArray", která obsahuje toto celočíselné pole: 

`[1,2,3,4]` 

Tato definice akce Získá hodnoty z proměnné pomocí `variables()` funkce ve výrazu a vytvoří tento řetězec s těmito hodnotami, které jsou odděleny čárkou: `"1,2,3,4"`

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

Tato akce vytvoří uživatelsky přívětivá pole nebo *tokeny* z vlastností v obsahu JSON. Pak můžete k těmto vlastnostem přistupovat ve vaší aplikaci logiky pomocí tokenů místo toho. Například pokud chcete použít výstup JSON ze služeb, jako jsou Azure Service Bus a Azure Cosmos DB, můžete tuto akci zahrnout do aplikace logiky, abyste mohli snadněji odkazovat na data v tomto výstupu.

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*JSON – zdroj*> | JSON – objekt | Obsah JSON, který chcete analyzovat | 
| <*Schéma JSON*> | JSON – objekt | Schéma JSON, které popisuje základní obsah JSON, který akce používá pro analýzu zdrojového obsahu JSON. <p>**Tip**: v Návrháři Logic Apps můžete buď zadat schéma, nebo zadat ukázkovou datovou část, aby tato akce mohla generovat schéma. | 
|||| 

*Příklad*

Tato definice akce vytvoří tyto tokeny, které můžete použít v pracovním postupu, ale pouze v akcích, které se spouštějí po akci **analyzovat JSON** :

`FirstName`, `LastName` a `Email`

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*skupin*> | Pole | Pole nebo výraz, který poskytuje zdrojové položky. Pokud zadáte výraz, uzavřete tento výraz do dvojitých uvozovek. |
| <*podmínka nebo filtr*> | Řetězec | Podmínka použitá pro filtrování položek ve zdrojovém poli <p>**Poznámka**: Pokud žádné hodnoty neodpovídají této podmínce, pak akce vytvoří prázdné pole. |
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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*odpověď – stavový kód*> | Integer | Stavový kód protokolu HTTP, který je odeslán na příchozí požadavek. Výchozí kód je "200 OK", ale kód může být platný stavový kód, který začíná na 2xx, 4xx nebo 5xx, ale ne s 3xxx. | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*odpovědi – hlavičky*> | JSON – objekt | Jedna nebo více hlaviček, které mají být zahrnuty do odpovědi | 
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

*Omezení*

Na rozdíl od jiných akcí má akce **reakce** zvláštní omezení: 

* Pracovní postup může použít akci **odpovědi** jenom v případě, že se pracovní postup spustí s triggerem požadavku HTTP, což znamená, že váš pracovní postup musí být aktivovaný požadavkem http.

* Pracovní postup může použít akci **odpovědi** kdekoli *s výjimkou* vnitřních smyček, **do** smyček, včetně sekvenčních **smyček a** paralelních větví. 

* Původní požadavek HTTP vrátí odpověď pracovního postupu pouze v případě, že všechny akce vyžadované akcí **Response** jsou dokončeny v rámci [limitu požadavku protokolu HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  Pokud ale váš pracovní postup volá jinou aplikaci logiky jako vnořený pracovní postup, nadřazený pracovní postup počká, dokud se nedokončí vnořený pracovní postup, bez ohledu na to, kolik času proběhlo před dokončením vnořeného pracovního postupu.

* Když pracovní postup používá akci **odpovědi** a vzor synchronní odpovědi, pracovní postup nemůže v definici triggeru použít také příkaz **splitOn** , protože tento příkaz vytvoří více spuštění. Pokud je použita metoda PUT a v případě hodnoty true, vyhledejte odpověď "špatný požadavek".

  V opačném případě, pokud váš pracovní postup používá příkaz **splitOn** a akci **odpovědi** , pracovní postup se spustí asynchronně a okamžitě vrátí odpověď "202 přijato".

* Když zpracování pracovního postupu dosáhne akce **odpovědi** , ale příchozí požadavek už odpověď přijal, je akce **odpověď** označená jako "neúspěšná" kvůli konfliktu. A v důsledku toho je vaše spuštění aplikace logiky označeno jako "neúspěšné".

<a name="select-action"></a>

### <a name="select-action"></a>Vybrat akci

Tato akce vytvoří pole s objekty JSON transformací položek z jiného pole na základě zadané mapy. Výstupní pole a zdrojové pole mají vždy stejný počet položek. I když nemůžete změnit počet objektů ve výstupním poli, můžete přidat nebo odebrat vlastnosti a jejich hodnoty mezi těmito objekty. `select`Vlastnost určuje alespoň jednu dvojici klíč-hodnota, která definuje mapu pro transformaci položek ve zdrojovém poli. Pár klíč-hodnota představuje vlastnost a její hodnotu napříč všemi objekty ve výstupním poli.

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

*Povinné* 

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*skupin*> | Pole | Pole nebo výraz, který poskytuje zdrojové položky. Ujistěte se, že uzavřete výraz do dvojitých uvozovek. <p>**Poznámka**: Pokud je zdrojové pole prázdné, akce vytvoří prázdné pole. | 
| <*název klíče*> | Řetězec | Název vlastnosti přiřazený výsledku z *výrazu* <> <p>Chcete-li přidat novou vlastnost napříč všemi objekty ve výstupním poli, zadejte <> *název klíče* pro tuto vlastnost a *výraz* <> pro hodnotu vlastnosti. <p>Chcete-li odebrat vlastnost ze všech objektů v poli, vynechejte> <*název klíče* pro tuto vlastnost. | 
| <*vyjádření*> | Řetězec | Výraz, který transformuje položku ve zdrojovém poli a přiřadí výsledek do <ho *názvu klíče*> | 
|||| 

Akce **Select** vytvoří pole jako výstup, takže jakákoli akce, která chce použít tento výstup, musí buď přijmout pole, nebo musíte převést pole na typ, který přijímá akce příjemce. Chcete-li například převést výstupní pole na řetězec, můžete toto pole předat do akce **psaní** a pak odkazovat na výstup z akce **psaní** v dalších akcích.

*Příklad*

Tato definice akce vytvoří pole objektu JSON z celočíselného pole. Akce prochází ze zdrojového pole, získává jednotlivé celočíselné hodnoty pomocí `@item()` výrazu a přiřazuje každou hodnotu `number` Vlastnosti "" v každém objektu JSON:

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

Tato akce vytvoří z pole tabulku CSV nebo HTML. U polí s objekty JSON Tato akce automaticky vytvoří záhlaví sloupců z názvů vlastností objektů. Pro pole s jinými datovými typy musíte zadat záhlaví sloupců a hodnoty. Například toto pole zahrnuje vlastnosti "ID" a "Product_Name", které tato akce může použít pro názvy záhlaví sloupců:

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

*Povinné* 

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| \<CSV *or* HTML>| Řetězec | Formát tabulky, kterou chcete vytvořit | 
| <*skupin*> | Pole | Pole nebo výraz, který poskytuje zdrojové položky pro tabulku <p>**Poznámka**: Pokud je zdrojové pole prázdné, akce vytvoří prázdnou tabulku. | 
|||| 

*Volitelné*

Chcete-li zadat nebo přizpůsobit záhlaví a hodnoty sloupců, použijte `columns` pole. Pokud `header-value` mají páry stejný název záhlaví, zobrazí se jejich hodnoty ve stejném sloupci pod tímto názvem záhlaví. V opačném případě každá jedinečná hlavička definuje jedinečný sloupec.

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*název sloupce*> | Řetězec | Název záhlaví sloupce | 
| <*hodnota sloupce*> | Libovolný | Hodnota v tomto sloupci | 
|||| 

*Příklad 1*

Předpokládejme, že máte dříve vytvořenou proměnnou "myItemArray", která aktuálně obsahuje toto pole:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Tato definice akce vytvoří tabulku CSV z proměnné "myItemArray". Výraz použitý `from` vlastností získá pole z "myItemArray" pomocí `variables()` funkce:

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

Tato definice akce vytvoří tabulku HTML z proměnné "myItemArray". Výraz použitý `from` vlastností získá pole z "myItemArray" pomocí `variables()` funkce:

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

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Jablk</td></tr><tr><td>1</td><td>Oranges</td></tr></tbody></table>

*Příklad 3*

Tato definice akce vytvoří tabulku HTML z proměnné "myItemArray". Tento příklad však přepíše výchozí názvy záhlaví sloupců "Stock_ID" a "Description" a přidá slovo "organické" do hodnot ve sloupci "Popis".

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*stav*> | Řetězec | Stav, který se má vrátit pro spuštění: "neúspěšné", "zrušené" nebo "úspěch" |
|||| 

*Volitelné*

Vlastnosti objektu "runStatus" platí pouze v případě, že vlastnost "runStatus" je nastavena na hodnotu "Failed".

| Hodnota | Typ | Description | 
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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*počet jednotek*> | Integer | Pro akci **zpoždění** počet jednotek, které se mají čekat | 
| <*doba*> | Řetězec | Pro akci **zpoždění** interval čekání: "Second", "Minute", "hour", "Day", "Week", "Month" | 
| <*časové razítko pro datum a čas*> | Řetězec | Pro **zpoždění až** do akce, datum a čas, kdy se má pokračovat v provádění. Tato hodnota musí používat [formát data a času UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
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

Tato akce volá další dříve vytvořenou aplikaci logiky, což znamená, že můžete zahrnout a znovu použít jiné pracovní postupy aplikace logiky. V akcích, které následují po vnořené aplikaci logiky, můžete také použít výstupy z podřízené *Aplikace logiky* , za předpokladu, že podřízená aplikace logiky vrátí odpověď.

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*vnořená logika – App-Name*> | Řetězec | Název aplikace logiky, kterou chcete volat | 
| <*Název aktivační události*> | Řetězec | Název triggeru ve vnořené aplikaci logiky, kterou chcete volat | 
| <*Azure-Subscription-ID*> | Řetězec | ID předplatného Azure pro vnořenou aplikaci logiky |
| <*Azure-Resource-Group*> | Řetězec | Název skupiny prostředků Azure pro vnořenou aplikaci logiky |
| <*vnořená logika – App-Name*> | Řetězec | Název aplikace logiky, kterou chcete volat |
||||

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------|  
| <*Hlavička-obsah*> | JSON – objekt | Všechna záhlaví k odeslání pomocí volání | 
| <*text obsahu*> | JSON – objekt | Veškerý obsah zprávy, který má být odeslán s voláním | 
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

*Povinné* 

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*akce – 1... n*> | Řetězec | Názvy akcí, které se spouštějí na každé položce pole | 
| <*akce – definice-1... n*> | JSON – objekt | Definice akcí, které se spouštějí | 
| <*for-each-Expression*> | Řetězec | Výraz, který odkazuje na každou položku v zadaném poli | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*výpočtu*> | Integer | Ve výchozím nastavení se iterace smyčky for each spouští současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete tento limit změnit nastavením nového <*počet*> hodnoty, přečtěte si téma [Změna pro každou souběžnou smyčku](#change-for-each-concurrency). | 
| <*operace – možnost*> | Řetězec | Chcete-li spustit smyčku "for each" sekvenčně, nikoli paralelně, nastavte buď *možnost <operace-*> na `Sequential` nebo <> *Count* `1` , ale ne obojí. Další informace najdete v tématu [spuštění smyčky for each postupně](#sequential-for-each). | 
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

Chcete-li určit pouze pole, které je předáno jako výstup z triggeru, tento výraz získá <pole *-název*> pole z těla triggeru. Chcete-li se vyhnout selhání, pokud pole neexistuje, výraz používá `?` operátor:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Pokud akce

Tato akce, která je *podmíněným příkazem*, vyhodnotí výraz, který představuje podmínku, a spustí jinou větev na základě toho, zda je podmínka pravdivá nebo false. Pokud je podmínka pravdivá, bude podmínka označena stavem "úspěch". Naučte [se vytvářet podmíněné příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md).

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

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*pomocné*> | JSON – objekt | Podmínka, která může být výrazem, k vyhodnocení | 
| <*akce – 1*> | JSON – objekt | Akce, která se má spustit, když <*podmínka*> vyhodnotí na true | 
| <*definice akce*> | JSON – objekt | Definice akce | 
| <*akce – 2*> | JSON – objekt | Akce, která se má spustit, když <*podmínka*> vyhodnotí na false | 
|||| 

Akce v `actions` `else` objektech nebo získají tyto stavy:

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
| "Expression": " @parameters (' <*hasSpecialAction*> ')" | Pouze pro logické výrazy je podmínka předána pro libovolnou hodnotu, která je vyhodnocena na hodnotu true. <p>Chcete-li převést jiné typy na logické, použijte tyto funkce: `empty()` nebo `equals()` . | 
| "Expression": " @greater (akce (' <*Action*> '). Output. Value; parameters (' <*prahová hodnota*> ')) | Pro funkce porovnání je akce spuštěna pouze v případě, že výstup z <*action*> je větší než <*prahová* hodnota> Value. | 
| "Expression": " @or (akce (' <*Action*> '). Output. Value, parameters (' <*prahová hodnota*> ')), less (Actions (' <*stejné akce*> '). Output. Value, 100))" | U logických funkcí a vytváření vnořených logických výrazů se akce spustí, když je výstup z <*action*> větší než <*prahová* hodnota> nebo pod 100. | 
| "Expression": " @equals (délka (Actions (' <*Action*> '). Outputs. Errors)"; 0)) " | Můžete použít funkce pole pro kontrolu, zda pole obsahuje nějaké položky. Akce se spustí, když `errors` je pole prázdné. | 
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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------|  
| <*vnitřní akce-1... n*> | JSON – objekt | Jedna nebo více akcí, které se spouštějí v rámci oboru |
| <*akce – vstupy*> | JSON – objekt | Vstupy pro každou akci |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Akce Switch

Tato akce, označovaná také jako *příkaz switch*, uspořádá další akce do *případů* a přiřadí hodnotu každému případu s výjimkou výchozího případu, pokud existuje. Když je pracovní postup spuštěn, akce **přepínače** porovná hodnotu z výrazu, objektu nebo tokenu s hodnotami zadanými pro každý případ. Pokud akce **Switch** najde odpovídající případ, bude pracovní postup spouštět pouze akce pro tento případ. Pokaždé, když se spustí akce **Přepnutí** , buď existuje pouze jeden odpovídající případ, nebo žádné shody neexistují. Pokud žádné shody neexistují, akce **Přepnutí** spustí výchozí akce. Naučte [se vytvářet příkazy Switch](../logic-apps/logic-apps-control-flow-switch-statement.md).

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

*Povinné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*výraz-objekt-nebo-token*> | Různé | Výraz, objekt JSON nebo token k vyhodnocení | 
| <*název akce*> | Řetězec | Název akce, která se má spustit pro případ porovnání | 
| <*definice akce*> | JSON – objekt | Definice akce, která má být spuštěna pro srovnávací případ | 
| <*shoda s hodnotou*> | Různé | Hodnota, která se má porovnat s vyhodnoceným výsledkem | 
|||| 

*Volitelné*

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*Výchozí hodnota – název akce*> | Řetězec | Název výchozí akce, která má být spuštěna, pokud neexistuje žádný vyhovující případ | 
| <*výchozí – definice akce*> | JSON – objekt | Definice akce, která má být spuštěna, pokud neexistuje žádný vyhovující případ | 
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

Tato akce smyčky obsahuje akce, které se spustí, dokud není zadaná podmínka pravdivá. Smyčka zkontroluje podmínku jako poslední krok po spuštění všech ostatních akcí. Do objektu můžete zahrnout více než jednu akci `"actions"` a akce musí definovat alespoň jeden limit. Přečtěte si, [jak vytvořit smyčky "do"](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

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

| Hodnota | Typ | Description | 
|-------|------|-------------| 
| <*název akce*> | Řetězec | Název akce, kterou chcete spustit uvnitř smyčky. | 
| <*Typ akce*> | Řetězec | Typ akce, kterou chcete spustit | 
| <*akce – vstupy*> | Některé | Vstupy pro akci, která se má spustit | 
| <*pomocné*> | Řetězec | Podmínka nebo výraz, který se má vyhodnotit po dokončení všech akcí ve smyčce | 
| <*počet cyklů*> | Integer | Limit nejvyšší hodnoty smyček, které mohou být akce spuštěny. Další informace o výchozím omezení a maximálním limitu najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*smyčka – časový limit*> | Řetězec | Limit v nejdelší době, kdy může být smyčka spuštěna. Výchozí `timeout` hodnota je `PT1H` , což je požadovaný [formát ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

> [!NOTE]
> Pokud výraz závisí na výstupu z jakékoli akce v rámci smyčky do, ujistěte se, že jste se přihlédli k jakékoli chybě, která je výsledkem této akce.

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

Triggery a akce založené na Webhooku pravidelně kontrolují koncové body, ale místo toho čekají na konkrétní události nebo data v těchto koncových bodech. Tyto triggery a akce se *přihlásí k odběru* koncových bodů zadáním *adresy URL zpětného volání* , kde koncový bod může odesílat odpovědi.

K `subscribe` volání dojde v případě, že se pracovní postup změní jakýmkoli způsobem, například při obnovení přihlašovacích údajů nebo při změně vstupních parametrů triggeru nebo akce. Toto volání používá stejné parametry jako standardní akce HTTP. 

K `unsubscribe` automatickému vyvolání dojde, když operace provede Trigger nebo akci není platná, například:

* Odstranění nebo zakázání triggeru 
* Odstranění nebo zakázání pracovního postupu. 
* Odstraňuje nebo zakáže odběr. 

Pro podporu těchto volání `@listCallbackUrl()` výraz vrací jedinečnou "adresu URL zpětného volání" pro aktivační událost nebo akci. Tato adresa URL představuje jedinečný identifikátor koncových bodů, které používají REST API služby. Parametry této funkce jsou stejné jako Trigger nebo akce Webhooku.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Změnit asynchronní dobu trvání

Pro aktivační události a akce můžete omezit dobu trvání asynchronního vzoru na určitý časový interval přidáním `limit.timeout` Vlastnosti. Tímto způsobem platí, že pokud se akce nedokončila v případě, že uplyne interval, je stav akce označený jako `Cancelled` `ActionTimedOut` kód. `timeout`Vlastnost používá [formát ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

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

Můžete změnit výchozí chování za běhu pro triggery a akce přidáním těchto `runtimeConfiguration` vlastností do definice triggeru nebo akce.

| Vlastnost | Typ | Description | Aktivační událost nebo akce | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Změňte [*výchozí limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) počtu instancí pracovního postupu, které lze spustit současně (souběžně nebo paralelně). Úprava této hodnoty může přispět k omezení počtu požadavků, které systém back-end obdrží. <p>Nastavení `runs` vlastnosti na `1` funguje stejným způsobem jako nastavení `operationOptions` vlastnosti `SingleInstance` . Můžete nastavit buď vlastnost, ale ne obojí. <p>Chcete-li změnit výchozí limit, přečtěte si téma [Změna souběžnosti triggeru](#change-trigger-concurrency) nebo [instancí triggerů](#sequential-trigger). | Všechny triggery | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Změňte [*výchozí limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) počtu instancí pracovního postupu, které musí čekat na spuštění, když aplikace logiky už používá maximální počet souběžných instancí. <p>Pokud chcete změnit výchozí limit, přečtěte si téma [Změna limitu čekání na spuštění](#change-waiting-runs). | Všechny triggery | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Změňte [*výchozí limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) počtu iterací smyčky "for each", které lze spustit současně (souběžně nebo paralelně). <p>Nastavení `repetitions` vlastnosti na `1` funguje stejným způsobem jako nastavení `operationOptions` vlastnosti `SingleInstance` . Můžete nastavit buď vlastnost, ale ne obojí. <p>Chcete-li změnit výchozí limit, přečtěte si téma [Změna "pro každou" souběžnou](#change-for-each-concurrency) "nebo [běh" pro každou "smyčku](#sequential-for-each). | Akce: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Integer | Pro konkrétní akce, které podporují a mají zapnuté stránkování, tato hodnota určuje *minimální* počet výsledků, které se mají načíst. <p>Pokud chcete zapnout stránkování, přečtěte si téma [získání hromadných dat, položek nebo výsledků pomocí stránkování](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) . | Akce: proměnlivé |
| `runtimeConfiguration.secureData.properties` | Pole | U mnoha triggerů a akcí tato nastavení skrývá vstupy, výstupy nebo obojí z historie spuštění aplikace logiky. <p>Další informace o zabezpečení těchto dat najdete v tématu [skrytí vstupů a výstupů z historie spuštění](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Většina aktivačních událostí a akcí |
| `runtimeConfiguration.staticResult` | JSON – objekt | Pro akce, které podporují a mají nastavení [statického výsledku](../logic-apps/test-logic-apps-mock-data-static-results.md) zapnuto, `staticResult` má objekt tyto atributy: <p>- `name`, který odkazuje na název definice statického výsledku aktuální akce, který se zobrazí uvnitř `staticResults` atributu v atributu pracovního postupu aplikace logiky `definition` . Další informace najdete v tématu [statické výsledky – reference schématu pro jazyk definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, který určuje, zda jsou statické výsledky `Enabled` pro aktuální akci nebo nikoli. <p>Pokud chcete zapnout statické výsledky, přečtěte si téma [test Logic Apps s napodobnou daty nastavením statických výsledků](../logic-apps/test-logic-apps-mock-data-static-results.md) . | Akce: proměnlivé |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Možnosti operace

Můžete změnit výchozí chování triggerů a akcí pomocí `operationOptions` vlastnosti v definici Trigger nebo Action.

| Možnost operace | Typ | Description | Aktivační událost nebo akce | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Řetězec | Spouštějte akce založené na protokolu HTTP synchronně, nikoli asynchronně. <p><p>Chcete-li nastavit tuto možnost, přečtěte si téma [Run Actions synchronně](#disable-asynchronous-pattern). | Činností <p>[Vstupech apiconnection](#apiconnection-action), <br>[Http](#http-action), <br>[Response](#response-action) (Odpověď) | 
| `IncludeAuthorizationHeadersInOutputs` | Řetězec | Pro Logic Apps, které [umožňují Azure Active Directory Open Authentication (Azure AD OAuth)](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) autorizovat přístup pro příchozí volání do koncového bodu triggeru na základě požadavků, zahrňte do `Authorization` výstupů aktivační události hlavičku z přístupového tokenu OAuth. Další informace najdete v tématu [zahrnutí ' autorizace ' do výstupů triggeru žádosti](../logic-apps/logic-apps-securing-a-logic-app.md#include-auth-header). | Zpráv <p>[Žádost](#request-trigger), <br>[HTTP Webhook](#http-webhook-trigger) | 
| `Sequential` | Řetězec | Spouštějte každou iteraci cyklů "for each" v jednom okamžiku, nikoli současně paralelně. <p>Tato možnost funguje stejným způsobem jako nastavení `runtimeConfiguration.concurrency.repetitions` vlastnosti na `1` . Můžete nastavit buď vlastnost, ale ne obojí. <p><p>Chcete-li nastavit tuto možnost, přečtěte si část [spuštění smyčky for each postupně](#sequential-for-each).| Akce: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Řetězec | Spusťte Trigger pro každou instanci aplikace logiky postupně a počkejte na dokončení dříve aktivního běhu a počkejte, než se aktivuje další instance aplikace logiky. <p><p>Tato možnost funguje stejným způsobem jako nastavení `runtimeConfiguration.concurrency.runs` vlastnosti na `1` . Můžete nastavit buď vlastnost, ale ne obojí. <p>Pokud chcete tuto možnost nastavit, přečtěte si téma [instance triggeru postupně](#sequential-trigger). | Všechny triggery | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Změna souběžnosti triggeru

Ve výchozím nastavení se instance pracovního postupu aplikace logiky spouštějí současně (souběžně nebo paralelně). Toto chování znamená, že každá instance triggeru je aktivována předtím, než se spustí dřív aktivní instance pracovního postupu. Počet souběžně spuštěných instancí má však [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Když počet souběžně spuštěných instancí pracovních postupů dosáhne tohoto limitu, všechny ostatní nové instance musí počkat na spuštění. Tento limit pomáhá řídit počet požadavků, které systém back-end obdrží.

Když zapnete řízení souběžnosti triggeru, triggery se spustí paralelně až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit tento výchozí limit souběžnosti, můžete použít buď editor zobrazení kódu, nebo návrháře Logic Apps, protože Změna nastavení souběžnosti prostřednictvím návrháře přidá nebo aktualizuje `runtimeConfiguration.concurrency.runs` vlastnost v základní definici triggeru a naopak. Tato vlastnost určuje maximální počet nových instancí pracovního postupu, které mohou být spuštěny paralelně.

Tady je několik důležitých informací, pokud chcete povolit souběžnost u triggeru:

* Pokud je povolená souběžnost povolená, [limit SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) se výrazně zkracuje pro [pole pro dedávkování](#split-on-debatch). Pokud počet položek překročí tento limit, funkce SplitOn je zakázaná.

* Souběžnost po povolení řízení souběžnosti nemůžete zakázat souběžnost.

* Pokud je povoleno souběžnost, může dlouhotrvající instance aplikace logiky způsobit, že nové instance aplikace logiky vstoupí do stavu čekání. Tento stav zabraňuje Azure Logic Apps vytváření nových instancí a probíhá i v případě, že počet souběžných spuštění je menší, než je stanovený maximální počet souběžných spuštění.

  * Chcete-li tento stav přerušit, zrušte nejstarší instance, které jsou *stále spuštěny*.

    1. V nabídce aplikace logiky zvolte **Přehled**.

    1. V části **historie spuštění** vyberte nejstarší instanci, která je pořád spuštěná, třeba:

       ![Vybrat nejstarší spuštěnou instanci](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Chcete-li zobrazit pouze instance, které jsou stále spuštěny, otevřete seznam **všechny** a vyberte možnost **spuštěno**.

    1. V části **spuštění aplikace logiky** vyberte **zrušit spuštění**.

       ![Najít nejstarší spuštěnou instanci](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Tuto možnost můžete obejít tak, že přidáte časový limit k jakékoli akci, která by mohla obsahovat tyto běhy. Pokud pracujete v editoru kódu, přečtěte si téma [Změna asynchronního trvání](#asynchronous-limits). V opačném případě, pokud používáte návrháře, postupujte podle následujících kroků:

    1. V aplikaci logiky na akci, kam chcete přidat časový limit, v pravém horním rohu vyberte tlačítko se třemi tečkami (**...**) a pak vyberte **Nastavení**.

       ![Otevřít nastavení akce](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. V části **časový limit** zadejte dobu trvání časového limitu ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Zadat dobu trvání časového limitu](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Chcete-li spustit aplikaci logiky sekvenčně, nastavte souběžnost triggeru na `1` buď pomocí editoru zobrazení kódu nebo návrháře. Ujistěte se, že jste vlastnost triggeru nestavili také `operationOptions` na `SingleInstance` v editoru zobrazení kódu. V opačném případě se zobrazí chyba ověřování. Další informace najdete v tématu [Postup při sekvenčním spuštění instancí](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu 

V definici základní aktivační události přidejte `runtimeConfiguration.concurrency.runs` vlastnost, která může mít hodnotu, která je v rozsahu od `1` do `50` .

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

Další informace najdete v tématu [nastavení konfigurace modulu runtime](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Upravit v Návrháři Logic Apps

1. V pravém horním rohu triggeru vyberte tlačítko se třemi tečkami (**...**) a pak vyberte **Nastavení**.

1. V části **řízení souběžnosti** nastavte **omezit** na **zapnuto**. 

1. Přetáhněte jezdec **stupeň paralelismu** na požadovanou hodnotu. Pokud chcete aplikaci logiky spustit sekvenčně, přetáhněte ji na hodnotu **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Změna "pro každou" souběžnost

Ve výchozím nastavení se všechny iterace smyčky for each spustí současně (současně nebo paralelně). Toto chování znamená, že každá iterace začne běžet před dokončením předchozí iterace. Počet souběžně spuštěných iterací však má [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Když počet souběžně spuštěných iterací dosáhne tohoto limitu, musí všechny ostatní iterace počkat na spuštění.

Chcete-li změnit výchozí limit, můžete použít buď editor zobrazení kódu, nebo návrháře Logic Apps, protože Změna nastavení souběžnosti prostřednictvím návrháře přidá nebo aktualizuje `runtimeConfiguration.concurrency.repetitions` vlastnost v podkladové definici akce "pro každou" a naopak. Tato vlastnost určuje maximální počet iterací, které mohou být spuštěny paralelně.

> [!NOTE] 
> Pokud nastavíte akci "for each" tak, aby se spouštěla sekvenčně buď pomocí návrháře nebo editoru zobrazení kódu, nenastavte vlastnost Action na `operationOptions` `Sequential` Editor zobrazení kódu. V opačném případě se zobrazí chyba ověřování. Další informace najdete v tématu [spuštění smyčky for each postupně](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu 

V podkladové definici "pro každou" přidejte nebo aktualizujte `runtimeConfiguration.concurrency.repetitions` vlastnost, která může mít hodnotu, která je v rozsahu od `1` a `50` .

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

Další informace najdete v tématu [nastavení konfigurace modulu runtime](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Upravit v Návrháři Logic Apps

1. V části **pro každou** akci vyberte v pravém horním rohu tlačítko se třemi tečkami (**...**) a pak vyberte **Nastavení**.

1. V části **řízení souběžnosti** nastavte **řízení souběžnosti** na **zapnuto**.

1. Přetáhněte jezdec **stupeň paralelismu** na požadovanou hodnotu. Pokud chcete aplikaci logiky spustit sekvenčně, přetáhněte ji na hodnotu **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Změnit limit čekání na spuštění

Ve výchozím nastavení se instance pracovního postupu aplikace logiky spouštějí současně (souběžně nebo paralelně). Toto chování znamená, že každá instance triggeru je aktivována předtím, než se spustí dřív aktivní instance pracovního postupu. Počet souběžně spuštěných instancí má však [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Když počet souběžně spuštěných instancí pracovních postupů dosáhne tohoto limitu, všechny ostatní nové instance musí počkat na spuštění.

Počet čekajících spuštění má také [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Když počet čekajících spuštění dosáhne tohoto limitu, Logic Apps modul už nepřijímá nová spuštění. Žádosti a Webhook triggery vrátí 429 chyb a opakované triggery začnou pokusy o cyklické dotazování přeskočí.

Pouze můžete [změnit výchozí limit pro souběžnost triggeru](#change-trigger-concurrency), ale můžete také změnit výchozí limit při čekání na spuštění. V definici základní aktivační události přidejte `runtimeConfiguration.concurrency.maximumWaitingRuns` vlastnost, která může mít hodnotu, která je v rozsahu od `1` do `100` .

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

Další informace najdete v tématu [nastavení konfigurace modulu runtime](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Postupně triggery instancí

Pokud chcete každou instanci pracovního postupu aplikace logiky spustit až po dokončení předchozí instance, nastavte Trigger tak, aby se spouštěl sekvenčně. Můžete použít buď editor zobrazení kódu, nebo Návrhář Logic Apps, protože Změna nastavení souběžnosti prostřednictvím návrháře také přidá nebo aktualizuje `runtimeConfiguration.concurrency.runs` vlastnost v základní definici triggeru a naopak.

> [!NOTE] 
> Pokud nastavíte Trigger tak, aby běžel postupně buď pomocí návrháře, nebo editoru zobrazení kódu, nenastavte vlastnost triggeru na hodnotu `operationOptions` `Sequential` v editoru zobrazení kódu. V opačném případě se zobrazí chyba ověřování. 

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu

V definici triggeru nastavte jednu z těchto vlastností, ale ne obojí. 

Nastavte `runtimeConfiguration.concurrency.runs` vlastnost na `1` :

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

Nastavte `operationOptions` vlastnost na `SingleInstance` :

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

Další informace najdete v tématu Možnosti nastavení a [operace](#operation-options) [Konfigurace modulu runtime](#runtime-config-options) .

#### <a name="edit-in-logic-apps-designer"></a>Upravit v Návrháři Logic Apps

1. V pravém horním rohu triggeru vyberte tlačítko se třemi tečkami (**...**) a pak vyberte **Nastavení**.

1. V části **řízení souběžnosti** nastavte **omezit** na **zapnuto**. 

1. Přetáhněte posuvník **stupeň paralelismu** na číslo `1` . 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Spuštění smyčky for each postupně

Chcete-li spustit cyklus smyčky "pro každou" až po dokončení předchozí iterace, nastavte akci "pro každý", aby běžela postupně. Můžete použít buď editor zobrazení kódu, nebo návrháře Logic Apps, protože změna souběžnosti akce prostřednictvím návrháře také přidá nebo aktualizuje `runtimeConfiguration.concurrency.repetitions` vlastnost v základní definici akce a naopak.

> [!NOTE] 
> Když nastavíte akci "for each" tak, aby se spouštěla sekvenčně buď pomocí návrháře nebo editoru zobrazení kódu, nenastavte `operationOptions` vlastnost Action na `Sequential` Editor zobrazení kódu. V opačném případě se zobrazí chyba ověřování. 

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu

V definici akce nastavte jednu z těchto vlastností, ale ne obojí. 

Nastavte `runtimeConfiguration.concurrency.repetitions` vlastnost na `1` :

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

Nastavte `operationOptions` vlastnost na `Sequential` :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Další informace najdete v tématu Možnosti nastavení a [operace](#operation-options) [Konfigurace modulu runtime](#runtime-config-options) .

#### <a name="edit-in-logic-apps-designer"></a>Upravit v Návrháři Logic Apps

1. V pravém horním rohu **pro každou** akci vyberte tlačítko se třemi tečkami (**...**) a pak vyberte **Nastavení**.

1. V části **řízení souběžnosti** nastavte **řízení souběžnosti** na **zapnuto**.

1. Přetáhněte posuvník **stupeň paralelismu** na číslo `1` .

<a name="disable-asynchronous-pattern"></a>

### <a name="run-actions-in-a-synchronous-operation-pattern"></a>Spuštění akcí ve vzoru synchronní operace

Ve výchozím nastavení jsou akce HTTP a akce vstupech apiconnection v Azure Logic Apps následovat po standardním [*vzoru asynchronní operace*](/azure/architecture/patterns/async-request-reply), zatímco akce odpovědi odpovídá *vzoru synchronní operace*. Asynchronní vzor určuje, že po volání akce nebo odeslání požadavku do zadaného koncového bodu, služby, systému nebo rozhraní API příjemce okamžitě vrátí odpověď ["202 přijatý"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) . Tento kód potvrdí, že příjemce požadavek přijal, ale nedokončil zpracování. Odpověď může obsahovat `location` hlavičku, která určuje adresu URL a ID aktualizace, které volající může použít k nepřetržitému cyklickému dotazování nebo kontrole stavu asynchronního požadavku, dokud příjemce neukončí zpracování a vrátí odpověď na úspěch ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) nebo jinou odpověď mimo 202. Další informace najdete v tématu [asynchronní integrace mikroslužeb vynutila autonomii](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)mikroslužeb.

* V návrháři aplikace logiky mají nastavení **asynchronního vzoru** nastavené akce HTTP, akce vstupech apiconnection a akce odpovědi. Pokud je toto nastavení povoleno, určuje, že volající nečeká na dokončení zpracování, a může přejít k další akci, ale pokračuje v kontrole stavu, dokud se zpracování nezastaví. Pokud je toto nastavení zakázáno, určuje, že volající čeká na dokončení zpracování, než přejde k další akci. Toto nastavení můžete najít pomocí těchto kroků:

  1. V záhlaví akce HTTP vyberte tlačítko se třemi tečkami (**...**), které otevře nastavení akce.

  1. Najde nastavení **asynchronního vzoru** .

     ![Nastavení asynchronního vzoru](./media/logic-apps-workflow-actions-triggers/asynchronous-pattern-setting.png)

* V definici základní JavaScript Object Notation (JSON) akce akce HTTP a akce vstupech apiconnection se implicitně řídí vzorkem asynchronní operace.

V některých scénářích může být vhodné provést akci, která bude postupovat podle synchronního vzoru. Například když použijete akci HTTP, můžete chtít:

* [Vyhněte se časovým limitům protokolu HTTP pro dlouhotrvající úlohy](../connectors/connectors-native-http.md#avoid-http-timeouts)
* [Zakázat kontrolu hlaviček umístění](../connectors/connectors-native-http.md#disable-location-header-check)

V těchto případech můžete provést synchronní spuštění akce pomocí těchto možností:

* Pokud je k dispozici verze Webhooku, nahraďte její verzi v dotazech.

* Pomocí jedné z možností zakažte asynchronní chování akce:

  * V návrháři aplikace logiky vypněte [nastavení **asynchronního vzoru**](#turn-off-asynchronous-pattern-setting).

  * V základní definici JSON akce [přidejte `"DisableAsyncPattern"` možnost operace](#add-disable-async-pattern-option).

<a name="turn-off-asynchronous-pattern-setting"></a>

#### <a name="turn-off-asynchronous-pattern-setting"></a>Vypnout nastavení **asynchronního vzoru**

1. V návrháři aplikace logiky v záhlaví akce vyberte tlačítko se třemi tečkami (**...**), které otevře nastavení akce.

1. Vyhledejte nastavení **asynchronního vzoru** , v případě povolení vypněte nastavení **vypnuto** a vyberte možnost **Hotovo**.

   ![Vypnout nastavení "asynchronního" vzoru](./media/logic-apps-workflow-actions-triggers/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

#### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Zakázat asynchronní vzor v definici JSON akce

V základní definici JSON akce přidejte a nastavte [vlastnost "operationOptions"](#operation-options) do `"DisableAsyncPattern"` `"inputs"` oddílu akce, například:

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Ověřování aktivačních událostí a akcí

Koncové body HTTP a HTTPS podporují různé druhy ověřování. V závislosti na triggeru nebo akci, kterou použijete k provedení odchozích volání nebo požadavků pro přístup k těmto koncovým bodům, můžete vybrat z různých rozsahů typů ověřování. Další informace najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Další kroky

* Další informace o [jazyku definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md)

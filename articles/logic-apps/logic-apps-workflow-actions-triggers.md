---
title: Odkaz na schéma pro typy aktivačních událostí a akcí
description: Referenční příručka schématu pro aktivační události jazyka definice pracovního postupu a typy akcí v aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 18e9c9d330ffb8cc4e284fc649cff0840ec2c82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270365"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Referenční příručka schématu pro typy aktivačních událostí a akcí v Aplikacích logiky Azure

Tento odkaz popisuje obecné typy používané pro identifikaci aktivačních událostí a akcí v definici pracovního postupu aplikace logiky, která je popsána a ověřena [jazykem definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md). Pokud chcete najít konkrétní konektor aktivační události a akce, které můžete použít v aplikacích [logiky,](https://docs.microsoft.com/connectors/)naleznete v seznamu v části konektory přehled .

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Přehled aktivačních událostí

Každý pracovní postup obsahuje aktivační událost, která definuje volání, která instanci a spuštění pracovního postupu. Zde jsou obecné kategorie aktivačních událostí:

* Aktivační *událost dotazování,* která v pravidelných intervalech kontroluje koncový bod služby

* *Aktivační událost push,* která vytvoří odběr koncového bodu a poskytne *adresu URL zpětného volání,* aby koncový bod mohl upozornit aktivační událost, když dojde k zadané události nebo k dispozici data. Aktivační událost pak čeká na odpověď koncového bodu před spuštěním.

Aktivační události mají tyto prvky nejvyšší úrovně, i když některé jsou volitelné:  
  
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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*název aktivační události*> | Řetězec | Název aktivační události | 
| <*typ aktivační události*> | Řetězec | Typ aktivační události, například "Http" nebo "ApiConnection" | 
| <*spouštěcí vstupy*> | JSON – objekt | Vstupy, které definují chování aktivační události | 
| <*časová jednotka*> | Řetězec | Jednotka času, která popisuje, jak často se spustí aktivační událost: "Druhý", "Minute", "Hour", "Day", "Week", "Month" | 
| <*počet časových jednotek*> | Integer | Hodnota, která určuje, jak často se aktivační událost aktivuje na základě frekvence, což je počet časových jednotek, které mají čekat, dokud se aktivační událost znovu neaktivuje. <p>Zde jsou minimální a maximální intervaly: <p>- Měsíc: 1-16 měsíců </br>- Den: 1-500 dní </br>- Hodina: 1-12 000 hodin </br>- Minuta: 1-72 000 minut </br>- Druhý: 1-9,999,999 sekund<p>Pokud je například interval 6 a frekvence je "Měsíc", opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*pole s podmínkami*> | Pole | Pole, které obsahuje jednu nebo více [podmínek,](#trigger-conditions) které určují, zda má být pracovní postup spuštěn. K dispozici pouze pro aktivační události. | 
| <*runtime-config-volby*> | JSON – objekt | Chování za běhu aktivační události `runtimeConfiguration` můžete změnit nastavením vlastností. Další informace naleznete v [tématu Runtime configuration settings](#runtime-config-options). | 
| <*splitOn-výraz*> | Řetězec | Pro aktivační události, které vracejí pole, můžete zadat výraz, který [rozdělí nebo *debatuje* položky](#split-on-debatch) pole do více instancí pracovního postupu pro zpracování. | 
| <*možnost operace*> | Řetězec | Výchozí chování můžete změnit nastavením vlastnosti. `operationOptions` Další informace naleznete v [tématu Možnosti operace](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Seznam typů aktivačních událostí

Každý typ aktivační události má jiné rozhraní a vstupy, které definují chování aktivační události. 

### <a name="built-in-triggers"></a>Vestavěné aktivační události

| Typ aktivační události | Popis | 
|--------------|-------------| 
| [**Protokol HTTP**](#http-trigger) | Zkontroluje nebo *dotazování* libovolný koncový bod. Tento koncový bod musí odpovídat konkrétní aktivační smlouvy pomocí "202" asynchronní vzor nebo vrácením pole. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Vytvoří volatelný koncový bod pro vaši aplikaci logiky, ale zavolá zadanou adresu URL k registraci nebo zrušení registrace. |
| [**Opakování**](#recurrence-trigger) | Požáry na základě definovaného plánu. Můžete nastavit budoucí datum a čas pro spuštění této aktivační události. Na základě frekvence můžete také zadat časy a dny spuštění pracovního postupu. | 
| [**Požadavek**](#request-trigger)  | Vytvoří volatelný koncový bod pro vaši aplikaci logiky a je také označován jako aktivační událost "ruční". Například najdete v tématu [Volání, aktivační události nebo vnoření pracovních postupů s koncovými body HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Aktivační události spravovaného rozhraní API

| Typ aktivační události | Popis | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Kontroluje nebo *dotazování* koncového bodu pomocí [microsoft spravovaných rozhraní API](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Vytvoří volatelný koncový bod pro vaši aplikaci logiky voláním [microsoft spravovaných rozhraní API](../connectors/apis-list.md) k odběru a odhlášení. | 
||| 

## <a name="triggers---detailed-reference"></a>Triggery – Podrobné referenční informace

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Aktivační událost APIConnection  

Tato aktivační událost kontroluje nebo *dotazování* koncový bod pomocí [Microsoft spravované rozhraní API,](../connectors/apis-list.md) takže parametry pro tuto aktivační událost se mohou lišit v závislosti na koncovém bodu. Mnoho oddílů v této definici aktivační události jsou volitelné. Chování aktivační události závisí na tom, zda jsou zahrnuty oddíly.

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

| Hodnota | Typ | Popis |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Řetězec | Název aktivační události |
| <*název připojení*> | Řetězec | Název připojení ke spravovanému rozhraní API, které pracovní postup používá |
| <*typ metody*> | Řetězec | Metoda HTTP pro komunikaci se spravovaným rozhraním API: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*api-provoz*> | Řetězec | Operace rozhraní API pro volání |
| <*časová jednotka*> | Řetězec | Jednotka času, která popisuje, jak často se spustí aktivační událost: "Druhý", "Minute", "Hour", "Day", "Week", "Month" |
| <*počet časových jednotek*> | Integer | Hodnota, která určuje, jak často se aktivační událost aktivuje na základě frekvence, což je počet časových jednotek, které mají čekat, dokud se aktivační událost znovu neaktivuje. <p>Zde jsou minimální a maximální intervaly: <p>- Měsíc: 1-16 měsíců </br>- Den: 1-500 dní </br>- Hodina: 1-12 000 hodin </br>- Minuta: 1-72 000 minut </br>- Druhý: 1-9,999,999 sekund<p>Pokud je například interval 6 a frekvence je "Měsíc", opakování je každých 6 měsíců. |
||||

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*opakování chování*> | JSON – objekt | Přizpůsobí chování opakování pro občasné selhání, které mají stavový kód 408, 429 a 5XX a všechny výjimky připojení. Další informace naleznete v tématu [Zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parametry dotazu*> | JSON – objekt | Všechny parametry dotazu zahrnout s voláním rozhraní API. Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*maximální počet spuštění*> | Integer | Ve výchozím nastavení se instance pracovního postupu spouštějí současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit nastavením nového <*počtu*> hodnoty, přečtěte si téma [Změna souběžnosti aktivační události](#change-trigger-concurrency). | 
| <*maximální počet spuštění-fronta*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou zařazeny do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si informace [o omezení počtu přečkání čekání](#change-waiting-runs). | 
| <*splitOn-výraz*> | Řetězec | Pro aktivační události, které vracejí pole, tento výraz odkazuje na pole, které má být používáno, takže můžete vytvořit a spustit instanci pracovního postupu pro každou položku pole, nikoli použít smyčku "pro každou". <p>Tento výraz například představuje položku v poli vráceném v rámci obsahu těla aktivační události:`@triggerbody()?['value']` |
| <*možnost operace*> | Řetězec | Výchozí chování můžete změnit nastavením vlastnosti. `operationOptions` Další informace naleznete v [tématu Možnosti operace](#operation-options). |
||||

*Výstupy*
 
| Element | Typ | Popis |
|---------|------|-------------|
| Záhlaví | JSON – objekt | Záhlaví z odpovědi |
| text | JSON – objekt | Tělo z odpovědi |
| stavový kód | Integer | Stavový kód z odpovědi |
|||| 

*Příklad*

Tato definice aktivační události kontroluje e-maily každý den ve složce Doručená pošta pro účet Office 365 Outlook:

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

### <a name="apiconnectionwebhook-trigger"></a>Aktivační událost ApiConnectionWebhook

Tato aktivační událost odešle požadavek na odběr koncovému bodu pomocí [rozhraní API spravovaného společností Microsoft](../connectors/apis-list.md), poskytuje adresu URL *zpětného volání,* na kterou může koncový bod odeslat odpověď, a čeká na odpověď koncového bodu. Další informace naleznete [v tématu Endpoint subscriptions](#subscribe-unsubscribe).

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*název připojení*> | Řetězec | Název připojení ke spravovanému rozhraní API, které pracovní postup používá | 
| <*obsah těla*> | JSON – objekt | Veškerý obsah zprávy, který se má odeslat jako datová část spravovanému rozhraní API | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*opakování chování*> | JSON – objekt | Přizpůsobí chování opakování pro občasné selhání, které mají stavový kód 408, 429 a 5XX a všechny výjimky připojení. Další informace naleznete v tématu [Zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parametry dotazu*> | JSON – objekt | Všechny parametry dotazu, které mají být zahrnuty do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*maximální počet spuštění*> | Integer | Ve výchozím nastavení se instance pracovního postupu spouštějí současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit nastavením nového <*počtu*> hodnoty, přečtěte si téma [Změna souběžnosti aktivační události](#change-trigger-concurrency). | 
| <*maximální počet spuštění-fronta*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou zařazeny do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si informace [o omezení počtu přečkání čekání](#change-waiting-runs). | 
| <*splitOn-výraz*> | Řetězec | Pro aktivační události, které vracejí pole, tento výraz odkazuje na pole, které má být používáno, takže můžete vytvořit a spustit instanci pracovního postupu pro každou položku pole, nikoli použít smyčku "pro každou". <p>Tento výraz například představuje položku v poli vráceném v rámci obsahu těla aktivační události:`@triggerbody()?['value']` |
| <*možnost operace*> | Řetězec | Výchozí chování můžete změnit nastavením vlastnosti. `operationOptions` Další informace naleznete v [tématu Možnosti operace](#operation-options). | 
|||| 

*Příklad*

Tato definice aktivační události se přihlásí k rozhraní API aplikace Office 365 Outlook, poskytne adresu URL zpětného volání koncovému bodu rozhraní API a čeká, až koncový bod odpoví, když přijde nový e-mail.

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

Tato aktivační událost odešle požadavek zadanému koncovému bodu HTTP nebo HTTPS na základě zadaného plánu opakování. Aktivační událost pak zkontroluje odpověď k určení, zda je spuštěn pracovní postup.

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

*Požadováno*

| Vlastnost | Hodnota | Typ | Popis |
|----------|-------|------|-------------|
| `method` | <*typ metody*> | Řetězec | Metoda, která se používá pro odeslání odchozího požadavku: "GET", "PUT", "POST", "PATCH" nebo "DELETE" |
| `uri` | <*Adresa URL koncového bodu HTTP nebo HTTPS*> | Řetězec | Adresa URL koncového bodu HTTP nebo HTTPS, na kterou chcete odeslat odchozí požadavek. Maximální velikost řetězce: 2 KB <p>Pro službu nebo prostředek Azure tato syntaxe identifikátoru URI zahrnuje ID prostředku a cestu k prostředku, ke kterému chcete získat přístup. |
| `frequency` | <*časová jednotka*> | Řetězec | Jednotka času, která popisuje, jak často se spustí aktivační událost: "Druhý", "Minute", "Hour", "Day", "Week", "Month" |
| `interval` | <*počet časových jednotek*> | Integer | Hodnota, která určuje, jak často se aktivační událost aktivuje na základě frekvence, což je počet časových jednotek, které mají čekat, dokud se aktivační událost znovu neaktivuje. <p>Zde jsou minimální a maximální intervaly: <p>- Měsíc: 1-16 měsíců </br>- Den: 1-500 dní </br>- Hodina: 1-12 000 hodin </br>- Minuta: 1-72 000 minut </br>- Druhý: 1-9,999,999 sekund<p>Pokud je například interval 6 a frekvence je "Měsíc", opakování je každých 6 měsíců. |
|||||

*Volitelné*

| Vlastnost | Hodnota | Typ | Popis |
|----------|-------|------|-------------|
| `headers` | <*obsah záhlaví*> | JSON – objekt | Všechny hlavičky, které je třeba zahrnout do požadavku <p>Chcete-li například nastavit jazyk a typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parametry dotazu*> | JSON – objekt | Všechny parametry dotazu, které je třeba použít v požadavku <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do požadavku. |
| `body` | <*obsah těla*> | JSON – objekt | Obsah zprávy odeslat jako datovou část s požadavkem |
| `authentication` | <*hodnoty typu a vlastnosti ověřování*> | JSON – objekt | Model ověřování, který požadavek používá pro ověřování odchozích požadavků. Další informace naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Kromě Plánovač, `authority` vlastnost je podporována. Pokud není zadán, výchozí `https://management.azure.com/`hodnota je , ale můžete použít jinou hodnotu. |
| `retryPolicy` > `type` | <*opakování chování*> | JSON – objekt | Přizpůsobí chování opakování pro občasné selhání, které mají stavový kód 408, 429 a 5XX a všechny výjimky připojení. Další informace naleznete v tématu [Zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*maximální počet spuštění*> | Integer | Ve výchozím nastavení se instance pracovního postupu spouštějí současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit nastavením nového <*počtu*> hodnoty, přečtěte si téma [Změna souběžnosti aktivační události](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*maximální počet spuštění-fronta*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou zařazeny do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si informace [o omezení počtu přečkání čekání](#change-waiting-runs). |
| `operationOptions` | <*možnost operace*> | Řetězec | Výchozí chování můžete změnit nastavením vlastnosti. `operationOptions` Další informace naleznete v [tématu Možnosti operace](#operation-options). |
|||||

*Výstupy*

| Element | Typ | Popis |
|---------|------|-------------| 
| Záhlaví | JSON – objekt | Záhlaví z odpovědi | 
| text | JSON – objekt | Tělo z odpovědi | 
| stavový kód | Integer | Stavový kód z odpovědi | 
|||| 

*Požadavky na příchozí požadavky*

Chcete-li dobře pracovat s aplikací logiky, koncový bod musí odpovídat konkrétní spouštěcí vzor nebo smlouvy a rozpoznat tyto vlastnosti:  
  
| Odpověď | Požaduje se | Popis | 
|----------|----------|-------------| 
| Kód stavu | Ano | Stavový kód "200 OK" spustí spuštění. Žádný jiný stavový kód nespustí spuštění. | 
| Opakovat záhlaví po | Ne | Počet sekund, než aplikace logiky znovu zahodnotí koncový bod | 
| Záhlaví umístění | Ne | Adresa URL pro volání v dalším intervalu dotazování. Pokud není zadán, použije se původní adresa URL. | 
|||| 

*Příklad chování pro různé požadavky*

| Kód stavu | Opakujte akci po | Chování | 
|-------------|-------------|----------|
| 200 | {none} | Spusťte pracovní postup a po definovaném opakování znovu zkontrolujte další data. | 
| 200 | 10 sekund | Spusťte pracovní postup a po 10 sekundách znovu zkontrolujte další data. |  
| 202 | 60 sekund | Nespouštějte pracovní postup. Další pokus se stane za jednu minutu, s výhradou definované opakování. Pokud definované opakování je menší než jedna minuta, opakování po záhlaví má přednost. V opačném případě se použije definované opakování. | 
| 400 | {none} | Chybný požadavek, nespouštějte pracovní postup. Pokud `retryPolicy` není definována žádná, použije se výchozí zásada. Po dosažení počtu opakování aktivační událost znovu zkontroluje data po definované opakování. | 
| 500 | {none}| Chyba serveru, nespouštějte pracovní postup. Pokud `retryPolicy` není definována žádná, použije se výchozí zásada. Po dosažení počtu opakování aktivační událost znovu zkontroluje data po definované opakování. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>Aktivační událost HTTPWebhooku  

Tato aktivační událost umožňuje, aby vaše aplikace logiky byla volatelná vytvořením koncového bodu, který může zaregistrovat odběr voláním zadané adresy URL koncového bodu. Když vytvoříte tuto aktivační událost v pracovním postupu, odchozí požadavek provede volání k registraci předplatného. Tímto způsobem aktivační událost může začít naslouchat události. Pokud operace způsobí, že tato aktivační událost je neplatná, odchozí požadavek automaticky provede volání ke zrušení předplatného. Další informace naleznete [v tématu Endpoint subscriptions](#subscribe-unsubscribe).

Můžete také zadat [asynchronní omezení](#asynchronous-limits) na aktivační události **HTTPWebhook.** Chování aktivační události závisí na oddílech, které používáte nebo vynechujete.

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

Některé hodnoty, například> *typu metody* <, `"subscribe"` `"unsubscribe"` jsou k dispozici pro objekty i objekty.

*Požadováno*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro požadavek na odběr: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*adresa URL odběru koncového bodu*> | Řetězec | Adresa URL koncového bodu, kam má být odeslánpožadavek na odběr | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda HTTP, která se má použít pro požadavek na zrušení: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*koncová adresa URL pro zrušení odběru*> | Řetězec | Adresa URL koncového bodu, kam má být odeslána žádost o zrušení | 
| <*obsah těla*> | Řetězec | Jakýkoli obsah zprávy, který chcete odeslat v žádosti o předplatné nebo zrušení | 
| <*typ ověřování*> | JSON – objekt | Model ověřování, který požadavek používá pro ověřování odchozích požadavků. Další informace naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*opakování chování*> | JSON – objekt | Přizpůsobí chování opakování pro občasné selhání, které mají stavový kód 408, 429 a 5XX a všechny výjimky připojení. Další informace naleznete v tématu [Zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*maximální počet spuštění*> | Integer | Ve výchozím nastavení jsou všechny instance pracovního postupu spuštěny současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit nastavením nového <*počtu*> hodnoty, přečtěte si téma [Změna souběžnosti aktivační události](#change-trigger-concurrency). | 
| <*maximální počet spuštění-fronta*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou zařazeny do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si informace [o omezení počtu přečkání čekání](#change-waiting-runs). | 
| <*možnost operace*> | Řetězec | Výchozí chování můžete změnit nastavením vlastnosti. `operationOptions` Další informace naleznete v [tématu Možnosti operace](#operation-options). | 
|||| 

*Výstupy* 

| Element | Typ | Popis |
|---------|------|-------------| 
| Záhlaví | JSON – objekt | Záhlaví z odpovědi | 
| text | JSON – objekt | Tělo z odpovědi | 
| stavový kód | Integer | Stavový kód z odpovědi | 
|||| 

*Příklad*

Tato aktivační událost vytvoří odběr zadaného koncového bodu, poskytuje jedinečnou adresu URL zpětného volání a čeká na nově publikované články technologie.

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

### <a name="recurrence-trigger"></a>Aktivační událost opakování  

Tato aktivační událost se spustí na základě zadaného plánu opakování a poskytuje snadný způsob, jak vytvořit pravidelně spuštěný pracovní postup.

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*časová jednotka*> | Řetězec | Jednotka času, která popisuje, jak často se spustí aktivační událost: "Druhý", "Minute", "Hour", "Day", "Week", "Month" | 
| <*počet časových jednotek*> | Integer | Hodnota, která určuje, jak často se aktivační událost aktivuje na základě frekvence, což je počet časových jednotek, které mají čekat, dokud se aktivační událost znovu neaktivuje. <p>Zde jsou minimální a maximální intervaly: <p>- Měsíc: 1-16 měsíců </br>- Den: 1-500 dní </br>- Hodina: 1-12 000 hodin </br>- Minuta: 1-72 000 minut </br>- Druhý: 1-9,999,999 sekund<p>Pokud je například interval 6 a frekvence je "Měsíc", opakování je každých 6 měsíců. | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*počáteční datum-čas-s formátem-YYYY-MM-DDThh:mm:ss*> | Řetězec | Počáteční datum a čas v tomto formátu: <p>YYYY-MM-DDThh:mm:ss, pokud zadáte časové pásmo <p>-nebo- <p>YYYY-MM-DDThh:mm:ssZ, pokud nezadáte časové pásmo <p>Takže například pokud chcete 18.září 2017 v 14:00, zadejte "2017-09-18T14:00:00" a zadejte časové pásmo, jako je například "Tichomoří (běžný čas" nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento čas zahájení má v budoucnu maximálně 49 let a musí se řídit [specifikací data iso 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ve [formátu data času UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [posunu UTC](https://en.wikipedia.org/wiki/UTC_offset). Pokud nezadáte časové pásmo, musíte přidat písmeno "Z" na konci bez mezer. Toto "Z" odkazuje na ekvivalentní [námořní čas](https://en.wikipedia.org/wiki/Nautical_time). <p>Pro jednoduché plány je počáteční čas prvním výskytem, zatímco pro složité plány se aktivační událost nespustí dříve než čas zahájení. Další informace o datech a časech zahájení naleznete v [tématu Vytvoření a plánování pravidelně spouštěcích úloh](../connectors/connectors-native-recurrence.md). | 
| <*časové pásmo*> | Řetězec | Použije se pouze v případě, že zadáte čas zahájení, protože tato aktivační událost nepřijímá [posun UTC](https://en.wikipedia.org/wiki/UTC_offset). Zadejte časové pásmo, které chcete použít. | 
| <*jedna nebo více hodinových značek*> | Celé číslo nebo celé pole | Pokud zadáte "Den" nebo "Týden" pro , `frequency`můžete zadat jedno nebo více celá čísla od 0 do 23, oddělené čárkami, jako hodiny dne, kdy chcete spustit pracovní postup. <p>Například pokud zadáte "10", "12" a "14", dostanete 10:00, 12:00 a 14:00 jako značky hodin. | 
| <*jednonebo více minutových značek*> | Celé číslo nebo celé pole | Pokud zadáte "Den" nebo "Týden" pro , `frequency`můžete zadat jedno nebo více celá čísla od 0 do 59, oddělené čárkami, jako minuty hodiny, kdy chcete spustit pracovní postup. <p>Můžete například zadat "30" jako značku minuty a pomocí předchozího příkladu pro hodiny dne získáte 10:30, 12:30 a 14:30. | 
| weekDays | Pole řetězce nebo řetězce | Pokud zadáte "Týden" `frequency`pro , můžete zadat jeden nebo více dnů oddělených čárkami, pokud chcete spustit pracovní postup: "Pondělí", "Úterý", "Středa", "Čtvrtek", "Pátek", "Sobota" a "Neděle" | 
| <*maximální počet spuštění*> | Integer | Ve výchozím nastavení jsou všechny instance pracovního postupu spuštěny současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit nastavením nového <*počtu*> hodnoty, přečtěte si téma [Změna souběžnosti aktivační události](#change-trigger-concurrency). | 
| <*maximální počet spuštění-fronta*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou zařazeny do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si informace [o omezení počtu přečkání čekání](#change-waiting-runs). | 
| <*možnost operace*> | Řetězec | Výchozí chování můžete změnit nastavením vlastnosti. `operationOptions` Další informace naleznete v [tématu Možnosti operace](#operation-options). | 
|||| 

*Příklad 1*

Tato základní aktivační událost opakování se spouští denně:

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

Můžete zadat počáteční datum a čas pro spuštění aktivační události. Tato aktivační událost opakování se spustí k zadanému datu a pak se aktivuje denně:

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

Tato aktivační událost opakování začíná 9.

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

Další informace a příklady této aktivační události naleznete v [tématu Vytvoření a plánování pravidelně spouštěcích úloh](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Aktivační událost požadavku

Tato aktivační událost umožňuje, aby vaše aplikace logiky byla volatelná vytvořením koncového bodu, který může přijímat příchozí požadavky. Pro tuto aktivační událost zadejte schéma JSON, které popisuje a ověřuje datovou část nebo vstupy, které aktivační událost obdrží z příchozího požadavku. Schéma také usnadňuje odkazy na vlastnosti aktivační události z pozdějších akcí v pracovním postupu.

Chcete-li volat tuto aktivační `listCallbackUrl` událost, musíte použít rozhraní API, které je popsáno v [rozhraní REST API služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows). Informace o použití této aktivační události jako koncového bodu HTTP naleznete v tématu [Volání, aktivace nebo vnoření pracovních postupů pomocí koncových bodů HTTP](../logic-apps/logic-apps-http-endpoint.md).

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*název vlastnosti*> | Řetězec | Název vlastnosti ve schématu JSON, který popisuje datovou část | 
| <*typ vlastnosti*> | Řetězec | Typ nemovitosti | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*typ metody*> | Řetězec | Metoda, která příchozí požadavky musí použít k volání aplikace logiky: "GET", "PUT", "POST", "PATCH", "DELETE" |
| <*relativní parametr cesta pro přijetí*> | Řetězec | Relativní cesta pro parametr, který může adresa URL koncového bodu přijmout | 
| <*požadované vlastnosti*> | Pole | Jedna nebo více vlastností, které vyžadují hodnoty | 
| <*maximální počet spuštění*> | Integer | Ve výchozím nastavení jsou všechny instance pracovního postupu spuštěny současně (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit nastavením nového <*počtu*> hodnoty, přečtěte si téma [Změna souběžnosti aktivační události](#change-trigger-concurrency). | 
| <*maximální počet spuštění-fronta*> | Integer | Pokud je v pracovním postupu již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou zařazeny do této fronty až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Pokud chcete změnit výchozí limit, přečtěte si informace [o omezení počtu přečkání čekání](#change-waiting-runs). | 
| <*možnost operace*> | Řetězec | Výchozí chování můžete změnit nastavením vlastnosti. `operationOptions` Další informace naleznete v [tématu Možnosti operace](#operation-options). | 
|||| 

*Příklad*

Tato aktivační událost určuje, že příchozí požadavek musí k volání aktivační události použít metodu HTTP POST a zahrnuje schéma, které ověřuje vstup z příchozího požadavku:

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

Pro všechny aktivační události a pouze aktivační události můžete zahrnout pole, které obsahuje jeden nebo více výrazů pro podmínky, které určují, zda má být pracovní postup spuštěn. Chcete-li `conditions` přidat vlastnost do aktivační události v pracovním postupu, otevřete aplikaci logiky v editoru zobrazení kódu.

Můžete například určit, že aktivační událost se aktivuje pouze v případě, že web vrátí `conditions` chybu interního serveru odkazem na stavový kód aktivační události ve vlastnosti:

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

Ve výchozím nastavení aktivační událost se aktivuje pouze po získání odpovědi "200 OK". Když výraz odkazuje na stavový kód aktivační události, je nahrazeno výchozí chování aktivační události. Pokud tedy chcete, aby se aktivační událost spustila pro více než jeden stavový kód, například stavový kód 200 a 201, musíte tento výraz uvést jako podmínku:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Aktivace více spuštění

Pokud aktivační událost vrátí pole pro aplikaci logiky ke zpracování, někdy "pro každou" smyčky může trvat příliš dlouho ke zpracování každé položky pole. Místo toho můžete použít **SplitOn** vlastnost v aktivační události *debatovat* pole. Debatching rozdělí položky pole a spustí novou instanci pracovního postupu, která běží pro každou položku pole. Tento přístup je užitečné, například pokud chcete dotazování koncový bod, který může vrátit více nových položek mezi intervaly dotazování. Maximální počet položek pole, které **spliton** může zpracovat v jedné aplikaci logiky spustit, naleznete v [tématu omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> **SplitOn** nelze použít se vzorem synchronní odezvy. Jakýkoli pracovní postup, který používá **SplitOn** a obsahuje akci odpovědi, běží asynchronně a okamžitě odešle `202 ACCEPTED` odpověď.
>
> Pokud je povolena souběžnost aktivační události, [limit SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) je výrazně snížena. Pokud počet položek překročí tento limit, je funkce SplitOn zakázána.
 
Pokud váš aktivační událost swagger soubor popisuje datovou část, která je pole, **SplitOn** vlastnost je automaticky přidán do aktivační události. V opačném případě přidejte tuto vlastnost uvnitř datové části odpovědi, která má pole, které chcete debatovat.

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

Aplikace logiky potřebuje pouze obsah `Rows`z pole v aplikaci , takže můžete vytvořit aktivační událost, jako je tento příklad:

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
> Pokud použijete `SplitOn` příkaz, nelze získat vlastnosti, které jsou mimo pole. Takže v tomto příkladu nelze `status` získat vlastnost v odpovědi vrácené z rozhraní API.
> 
> Chcete-li se `Rows` vyhnout selhání, pokud vlastnost neexistuje, tento příklad používá `?` operátor.

Definice pracovního postupu `@triggerBody().name` nyní `name` můžete použít `"customer-name-one"` k získání hodnot, které jsou z prvního spuštění a `"customer-name-two"` z druhého spuštění. Takže výstupy aktivační události vypadají jako tyto příklady:

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

Azure Logic Apps poskytuje různé typy akcí – každý s různými vstupy, které definují jedinečné chování akce. Akce mají tyto prvky vysoké úrovně, i když některé jsou volitelné:

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

| Hodnota | Typ | Popis | 
|-------|------|-------------|
| <*název akce*> | Řetězec | Název akce | 
| <*typ akce*> | Řetězec | Typ akce, například "Http" nebo "ApiConnection"| 
| <*vstupní název*> | Řetězec | Název vstupu, který definuje chování akce | 
| <*vstupní hodnota*> | Různých | Vstupní hodnota, která může být řetězec, celé číslo, objekt JSON a tak dále | 
| <*předchozí aktivační událost nebo stav akce*> | JSON – objekt | Název a výsledný stav aktivační události nebo akce, která musí být spuštěna bezprostředně před spuštěním této aktuální akce | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------|
| <*opakování chování*> | JSON – objekt | Přizpůsobí chování opakování pro občasné selhání, které mají stavový kód 408, 429 a 5XX a všechny výjimky připojení. Další informace naleznete v tématu Zásady opakování. | 
| <*runtime-config-volby*> | JSON – objekt | U některých akcí můžete změnit chování akce za `runtimeConfiguration` běhu nastavením vlastností. Další informace naleznete v [tématu Runtime configuration settings](#runtime-config-options). | 
| <*možnost operace*> | Řetězec | U některých akcí můžete změnit výchozí `operationOptions` chování nastavením vlastnosti. Další informace naleznete v [tématu Možnosti operace](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Seznam typů akcí

Zde jsou některé běžně používané typy akcí: 

* [Předdefinované typy akcí,](#built-in-actions) jako jsou tyto příklady a další: 

  * [**HTTP**](#http-action) pro volání koncových bodů přes HTTP nebo HTTPS

  * [**Odpověď**](#response-action) na odpovědi na požadavky

  * [**Spuštění kódu JavaScript**](#run-javascript-code) pro spuštění fragmentů kódu JavaScript

  * [**Funkce**](#function-action) pro volání funkcí Azure

  * Akce operace dat, jako je [**spojení**](#join-action), [**vytvoření,**](#compose-action) [**tabulka**](#table-action), [**výběr**](#select-action)a další akce, které vytvářejí nebo transformují data z různých vstupů

  * [**Pracovní postup**](#workflow-action) pro volání jiného pracovního postupu aplikace logiky

* [Typy akcí spravovaného rozhraní API,](#managed-api-actions) jako je [**ApiConnection**](#apiconnection-action) a [**ApiConnectionWebHook,**](#apiconnectionwebhook-action) které volají různé konektory a rozhraní API spravovaná Microsoftem, například Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub a další

* [Řídit typy akcí pracovního postupu,](#control-workflow-actions) například [**If**](#if-action), [**Foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action)a [**Until**](#until-action), které obsahují další akce a pomáhají organizovat provádění pracovního postupu

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Integrované akce

| Typ akce | Popis | 
|-------------|-------------| 
| [**Vytvořit**](#compose-action) | Vytvoří jeden výstup ze vstupů, které mohou mít různé typy. | 
| [**Spuštění kódu JavaScript**](#run-javascript-code) | Spusťte fragmenty kódu JavaScriptu, které odpovídají určitým kritériím. Požadavky na kód a další informace naleznete v tématu [Přidání a spuštění fragmentů kódu s vsazeným kódem](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Funkce**](#function-action) | Volá funkci Azure. | 
| [**Protokol HTTP**](#http-action) | Volá koncový bod HTTP. | 
| [**Připojit**](#join-action) | Vytvoří řetězec ze všech položek v poli a odděluje tyto položky se zadaným znakem oddělovače. | 
| [**Analyzovat JSON**](#parse-json-action) | Vytvoří uživatelsky přívětivé tokeny z vlastností v obsahu JSON. Potom můžete odkazovat na tyto vlastnosti zahrnutím tokeny ve vaší aplikaci logiky. | 
| [**Dotazu**](#query-action) | Vytvoří pole z položek v jiném poli na základě podmínky nebo filtru. | 
| [**Reakce**](#response-action) | Vytvoří odpověď na příchozí hovor nebo požadavek. | 
| [**Vyberte**](#select-action) | Vytvoří pole s objekty JSON transformací položek z jiného pole na základě zadané mapy. | 
| [**Table**](#table-action) | Vytvoří tabulku CSV nebo HTML z pole. | 
| [**Ukončit**](#terminate-action) | Zastaví aktivně spuštěný pracovní postup. | 
| [**Wait**](#wait-action) | Pozastaví pracovní postup na určitou dobu nebo na zadané datum a čas. | 
| [**Pracovního postupu**](#workflow-action) | Vnoří pracovní postup do jiného pracovního postupu. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Akce spravovaného rozhraní API

| Typ akce | Popis | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Volá koncový bod HTTP pomocí [rozhraní API spravovaného společností Microsoft](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funguje jako HTTP Webhook, ale používá [rozhraní API spravované společností Microsoft](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Řízení akcí pracovního postupu

Tyto akce pomáhají řídit provádění pracovního postupu a zahrnují další akce. Mimo akci pracovního postupu ovládacího prvku můžete přímo odkazovat na akce uvnitř této akce pracovního postupu ovládacího prvku. Například pokud máte `Http` akci uvnitř oboru, můžete `@body('Http')` odkazovat na výraz z libovolného místa v pracovním postupu. Akce, které existují uvnitř akce pracovního postupu ovládacího prvku však lze pouze "spustit po" jiné akce, které jsou ve stejné struktuře pracovního postupu ovládacího prvku.

| Typ akce | Popis | 
|-------------|-------------| 
| [**Foreach**](#foreach-action) | Spusťte stejné akce ve smyčce pro každou položku v poli. | 
| [**Pokud**](#if-action) | Spusťte akce na základě toho, zda je zadaná podmínka pravdivá nebo nepravdivá. | 
| [**Rozsah**](#scope-action) | Spouštět akce na základě stavu skupiny ze sady akcí. | 
| [**Přepnout**](#switch-action) | Spusťte akce uspořádané do případů, kdy hodnoty z výrazů, objektů nebo tokenů odpovídají hodnotám určeným jednotlivými případy. | 
| [**Dokud**](#until-action) | Spouštět akce ve smyčce, dokud zadaná podmínka je true. | 
|||  

## <a name="actions---detailed-reference"></a>Akce – podrobný odkaz

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Akce APIConnection

Tato akce odešle požadavek HTTP do [rozhraní API spravovaného společností Microsoft](../connectors/apis-list.md) a vyžaduje informace o rozhraní API a parametrech a odkaz na platné připojení. 

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*název akce*> | Řetězec | Název akce poskytované konektorem | 
| <*název api*> | Řetězec | Název rozhraní API spravovaného společností Microsoft, které se používá pro připojení | 
| <*typ metody*> | Řetězec | Metoda HTTP pro volání rozhraní API: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*api-provoz*> | Řetězec | Operace rozhraní API pro volání | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*vlastnosti specifické pro akci a vstup*> | JSON – objekt | Všechny ostatní vstupní vlastnosti, které se vztahují k této konkrétní akci | 
| <*opakování chování*> | JSON – objekt | Přizpůsobí chování opakování pro občasné selhání, které mají stavový kód 408, 429 a 5XX a všechny výjimky připojení. Další informace naleznete v tématu [Zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parametry dotazu*> | JSON – objekt | Všechny parametry dotazu zahrnout s voláním rozhraní API. <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*vlastnosti specifické pro jiné akce*> | JSON – objekt | Všechny ostatní vlastnosti, které se vztahují k této konkrétní akci | 
|||| 

*Příklad*

Tato definice popisuje akci **Odeslat e-mail** pro konektor Outlooku Office 365, což je rozhraní API spravované společností Microsoft: 

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

### <a name="apiconnectionwebhook-action"></a>Akce APIConnectionWebhook

Tato akce odešle požadavek na odběr přes HTTP do koncového bodu pomocí [rozhraní API spravovaného společností Microsoft](../connectors/apis-list.md), poskytuje adresu URL *zpětného volání,* na které koncový bod může odeslat odpověď a čeká na odpověď koncového bodu. Další informace naleznete [v tématu Endpoint subscriptions](#subscribe-unsubscribe).

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

Některé hodnoty, například> *typu metody* <, `"subscribe"` `"unsubscribe"` jsou k dispozici pro objekty i objekty.

*Požadováno*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*název akce*> | Řetězec | Název akce poskytované konektorem | 
| <*typ metody*> | Řetězec | Metoda HTTP pro přihlášení k odběru nebo odhlášení od koncového bodu: "GET", "PUT", "POST", "PATCH" nebo "DELETE" | 
| <*api-subscribe-URL*> | Řetězec | Identifikátor URI, který se má použít pro přihlášení k odběru rozhraní API | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | Řetězec | Identifikátor URI, který se má použít pro odhlášení z rozhraní API | 
| <*obsah záhlaví*> | JSON – objekt | Všechny hlavičky odeslat v žádosti <p>Chcete-li například nastavit jazyk a typ na žádost: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*obsah těla*> | JSON – objekt | Jakýkoli obsah zprávy, který má být odeslán v žádosti | 
| <*typ ověřování*> | JSON – objekt | Model ověřování, který požadavek používá pro ověřování odchozích požadavků. Další informace naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*opakování chování*> | JSON – objekt | Přizpůsobí chování opakování pro občasné selhání, které mají stavový kód 408, 429 a 5XX a všechny výjimky připojení. Další informace naleznete v tématu [Zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*parametry dotazu*> | JSON – objekt | Všechny parametry dotazu, které mají být zahrnuty do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*vlastnosti specifické pro akci a vstup*> | JSON – objekt | Všechny ostatní vstupní vlastnosti, které se vztahují k této konkrétní akci | 
| <*vlastnosti specifické pro jiné akce*> | JSON – objekt | Všechny ostatní vlastnosti, které se vztahují k této konkrétní akci | 
|||| 

Můžete také zadat omezení akce **ApiConnectionWebhook** stejným způsobem jako [http asynchronní limity](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Akce pro skládání

Tato akce vytvoří jeden výstup z více vstupů, včetně výrazů. Výstup i vstupy mohou mít libovolný typ, který nativně podporuje Aplikace Logika Azure, jako jsou pole, objekty JSON, XML a binární. Výstup akce pak můžete použít v jiných akcích. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Požadováno* 

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*vstupy k komponovat*> | Všechny | Vstupy pro vytvoření jednoho výstupu | 
|||| 

*Příklad 1*

<!-- markdownlint-disable MD038 -->
Tato definice akce `abcdefg ` se sloučí s `1234`koncovým prostorem a hodnotou :
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

Tato definice akce sloučí `abcdefg` řetězcovou proměnnou, která `1234`obsahuje, a celou proměnnou, která obsahuje :

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

### <a name="execute-javascript-code-action"></a>Spustit akci kódu JavaScriptu

Tato akce spustí fragment kódu JavaScriptu a vrátí `Result` výsledky prostřednictvím tokenu, na který mohou odkazovat pozdější akce.

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

| Hodnota | Typ | Popis |
|-------|------|-------------|
| <*Fragment kódu JavaScriptu*> | Různé | Kód JavaScriptu, který chcete spustit. Požadavky na kód a další informace naleznete v tématu [Přidání a spuštění fragmentů kódu s vsazeným kódem](../logic-apps/logic-apps-add-run-inline-code.md). <p>V `code` atributu fragment kódu můžete použít objekt jen `workflowContext` pro čtení jako vstup. Tento objekt má podvlastnosti, které poskytují váš kód přístup k výsledkům aktivační události a předchozí akce v pracovním postupu. Další informace o `workflowContext` objektu naleznete [v tématu Reference trigger and action results in your code](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Povinné v některých případech*

Atribut `explicitDependencies` určuje, že chcete explicitně zahrnout výsledky z aktivační události, předchozí akce nebo obojí jako závislosti pro fragment kódu. Další informace o přidávání těchto závislostí naleznete v tématu [Přidání parametrů pro vsazení kódu](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

Pro `includeTrigger` atribut můžete zadat `true` `false` nebo hodnoty.

| Hodnota | Typ | Popis |
|-------|------|-------------|
| <*předchozí akce*> | Pole řetězců | Pole s názvy zadaných akcí. Použijte názvy akcí, které se zobrazují v definici pracovního postupu, kde názvy akcí používají podtržítka (_), nikoli mezery (" "). |
||||

*Příklad 1*

Tato akce spustí kód, který získá název aplikace logiky \<a vrátí text "Hello world from logic-app-name>" jako výsledek. V tomto příkladu kód odkazuje na název pracovního `workflowContext.workflow.name` postupu přístupem k `workflowContext` vlastnosti prostřednictvím objektu jen pro čtení. Další informace o `workflowContext` použití objektu naleznete [v tématu Reference trigger and action results in your code](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

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

Tato akce spustí kód v aplikaci logiky, která se aktivuje, když nový e-mail dorazí do účtu Office 365 Outlook. Aplikace logiky také používá akci e-mailu schválení odeslat, která předává obsah z přijatého e-mailu spolu s žádostí o schválení.

Kód extrahuje e-mailové adresy z `Body` vlastnosti aktivační události a `SelectedOption` vrátí adresy spolu s hodnotou vlastnosti z akce schválení. Akce explicitně zahrnuje akci e-mailu schválení `explicitDependencies`  >  `actions` typu odeslání jako závislost v atributu.

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

Tato akce volá dříve vytvořenou [funkci Azure](../azure-functions/functions-create-first-azure-function.md).

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

| Hodnota | Typ | Popis | 
|-------|------|-------------|  
| <*ID funkce Azure*> | Řetězec | ID prostředku pro funkci Azure, kterou chcete volat. Zde je formát pro tuto hodnotu:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<> *název funkce Azure"* | 
| <*typ metody*> | Řetězec | Metoda HTTP pro volání funkce: "GET", "PUT", "POST", "PATCH" nebo "DELETE" <p>Pokud není zadán, výchozí je metoda "POST". | 
||||

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------|  
| <*obsah záhlaví*> | JSON – objekt | Všechna záhlaví, která se mají odeslat pomocí hovoru <p>Chcete-li například nastavit jazyk a typ na žádost: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*obsah těla*> | JSON – objekt | Jakýkoli obsah zprávy, který má být odeslán v žádosti | 
| <*parametry dotazu*> | JSON – objekt | Všechny parametry dotazu, které mají být zahrnuty do volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. | 
| <*vlastnosti specifické pro akci a vstup*> | JSON – objekt | Všechny ostatní vstupní vlastnosti, které se vztahují k této konkrétní akci | 
| <*vlastnosti specifické pro jiné akce*> | JSON – objekt | Všechny ostatní vlastnosti, které se vztahují k této konkrétní akci | 
||||

Když uložíte aplikaci logiky, modul Logic Apps provede tyto kontroly na odkazované funkce:

* Pracovní postup musí mít přístup k funkci.

* Váš pracovní postup může používat pouze standardní aktivační událost HTTP nebo obecnou aktivační událost webového háku JSON. 

  Modul Logic Apps získá a uloží do mezipaměti adresu URL aktivační události, která se používá za běhu. Pokud však jakákoli operace zruší platnost adresy URL uložené v mezipaměti, akce **Funkce** se za běhu nezdaří. Chcete-li tento problém vyřešit, uložte aplikaci logiky znovu tak, aby aplikace logiky získá a uloží do mezipaměti adresu URL aktivační události znovu.

* Funkce nemůže mít žádnou definovanou trasu.

* Povoleny jsou pouze úrovně autorizace "funkce" a "anonymní". 

*Příklad*

Tato definice akce volá dříve vytvořenou funkci "GetProductID":

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

Tato akce odešle požadavek zadanému koncovému bodu HTTP nebo HTTPS a zkontroluje odpověď, aby zjistila, zda je pracovní postup spuštěn.

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

*Požadováno*

| Vlastnost | Hodnota | Typ | Popis |
|----------|-------|------|-------------|
| `method` | <*typ metody*> | Řetězec | Metoda, která se používá pro odeslání odchozího požadavku: "GET", "PUT", "POST", "PATCH" nebo "DELETE" |
| `uri` | <*Adresa URL koncového bodu HTTP nebo HTTPS*> | Řetězec | Adresa URL koncového bodu HTTP nebo HTTPS, na kterou chcete odeslat odchozí požadavek. Maximální velikost řetězce: 2 KB <p>Pro službu nebo prostředek Azure tato syntaxe identifikátoru URI zahrnuje ID prostředku a cestu k prostředku, ke kterému chcete získat přístup. |
|||||

*Volitelné*

| Vlastnost | Hodnota | Typ | Popis |
|----------|-------|------|-------------|
| `headers` | <*obsah záhlaví*> | JSON – objekt | Všechny hlavičky, které je třeba zahrnout do požadavku <p>Chcete-li například nastavit jazyk a typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*parametry dotazu*> | JSON – objekt | Všechny parametry dotazu, které je třeba použít v požadavku <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` do volání. |
| `body` | <*obsah těla*> | JSON – objekt | Obsah zprávy odeslat jako datovou část s požadavkem |
| `authentication` | <*hodnoty typu a vlastnosti ověřování*> | JSON – objekt | Model ověřování, který požadavek používá pro ověřování odchozích požadavků. Další informace naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Kromě Plánovač, `authority` vlastnost je podporována. Pokud není zadán, výchozí `https://management.azure.com/`hodnota je , ale můžete použít jinou hodnotu. |
| `retryPolicy` > `type` | <*opakování chování*> | JSON – objekt | Přizpůsobí chování opakování pro občasné selhání, které mají stavový kód 408, 429 a 5XX a všechny výjimky připojení. Další informace naleznete v tématu [Zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*vlastnosti specifické pro akci a vstup*> | <*input-property*> | JSON – objekt | Všechny ostatní vstupní vlastnosti, které se vztahují k této konkrétní akci |
| <*vlastnosti specifické pro jiné akce*> | <*hodnota vlastnosti*> | JSON – objekt | Všechny ostatní vlastnosti, které se vztahují k této konkrétní akci |
|||||

*Příklad*

Tato definice akce získá nejnovější zprávy odesláním požadavku do zadaného koncového bodu:

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

### <a name="join-action"></a>Připojit se k akci

Tato akce vytvoří řetězec ze všech položek v poli a odděluje tyto položky se zadaným znakem oddělovače. 

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*Pole*> | Pole | Pole nebo výraz, který poskytuje zdrojové položky. Pokud zadáte výraz, uzavřete tento výraz dvojitými uvozovkami. | 
| <*Oddělovač*> | Řetězec jednoho znaku | Znak, který odděluje každou položku v řetězci | 
|||| 

*Příklad*

Předpokládejme, že máte dříve vytvořenou proměnnou myIntegerArray, která obsahuje toto celé pole: 

`[1,2,3,4]` 

Tato definice akce získá hodnoty z `variables()` proměnné pomocí funkce ve výrazu a vytvoří tento řetězec s těmito hodnotami, které jsou odděleny čárkou:`"1,2,3,4"`

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

Tato akce vytvoří uživatelsky přívětivá pole nebo *tokeny* z vlastností v obsahu JSON. Potom můžete získat přístup k těmto vlastnostem v aplikaci logiky pomocí tokenů místo. Například pokud chcete použít výstup JSON ze služeb, jako je Azure Service Bus a Azure Cosmos DB, můžete tuto akci zahrnout do aplikace logiky, takže můžete snadněji odkazovat na data v tomto výstupu.

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*JSON-zdroj*> | JSON – objekt | Obsah JSON, který chcete analyzovat | 
| <*Schéma JSON*> | JSON – objekt | Schéma JSON, které popisuje základní obsah JSON, který akce používá pro analýzu zdrojového obsahu JSON. <p>**Tip:** V Návrháři aplikací logiky můžete buď zadat schéma, nebo poskytnout ukázkovou datovou část, aby akce mohla generovat schéma. | 
|||| 

*Příklad*

Tato definice akce vytvoří tyto tokeny, které můžete použít v pracovním postupu, ale pouze v akcích, které běží po akci **Analyzovat JSON:**

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

V tomto příkladu "content" vlastnost určuje obsah JSON pro akci analyzovat. Můžete také zadat tento obsah JSON jako ukázkovou datovou část pro generování schématu.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Vlastnost "schéma" určuje schéma JSON používané pro popis obsahu JSON:

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*Pole*> | Pole | Pole nebo výraz, který poskytuje zdrojové položky. Pokud zadáte výraz, uzavřete tento výraz dvojitými uvozovkami. |
| <*podmínka nebo filtr*> | Řetězec | Podmínka použitá pro filtrování položek ve zdrojovém poli <p>**Poznámka:** Pokud podmínku nesplňují žádné hodnoty, akce vytvoří prázdné pole. |
|||| 

*Příklad*

Tato definice akce vytvoří pole, které má hodnoty větší než zadaná hodnota, což jsou dvě:

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

### <a name="response-action"></a>Akce reakce  

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*kód stavu odpovědi*> | Integer | Stavový kód HTTP, který je odeslán příchozímu požadavku. Výchozí kód je "200 OK", ale kód může být libovolný platný stavový kód, který začíná 2xx, 4xx nebo 5xx, ale ne s 3xxx. | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*hlavičky odpovědí*> | JSON – objekt | Jedno nebo více záhlaví, která mají být zahrnuta do odpovědi | 
| <*odpověď-tělo*> | Různých | Tělo odpovědi, které může být řetězec, objekt JSON nebo dokonce binární obsah z předchozí akce | 
|||| 

*Příklad*

Tato definice akce vytvoří odpověď na požadavek HTTP se zadaným stavovým kódem, textem zprávy a záhlavími zpráv:

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

Na rozdíl od jiných akcí má akce **Response** zvláštní omezení: 

* Váš pracovní postup může akci **Odpověď** použít pouze v případě, že pracovní postup začíná aktivační událostí požadavku HTTP, což znamená, že pracovní postup musí být spuštěn požadavkem HTTP.

* Váš pracovní postup můžete použít **akce Odpověď** kdekoli *kromě* **foreach** smyčky, **Dokud** smyčky, včetně sekvenční smyčky a paralelní větve. 

* Původní požadavek HTTP získá odpověď pracovního postupu pouze v případě, že všechny akce vyžadované akcí **Odpovědi** jsou dokončeny v rámci [časového limitu požadavku HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  Pokud však váš pracovní postup volá jinou aplikaci logiky jako vnořený pracovní postup, nadřazený pracovní postup čeká na dokončení vnořeného pracovního postupu bez ohledu na to, kolik času uplyne před dokončením vnořeného pracovního postupu.

* Pokud váš pracovní postup používá akci **Odpověď** a vzor synchronní odezvy, pracovní postup nemůže také použít příkaz **splitOn** v definici aktivační události, protože tento příkaz vytvoří více spuštění. Zkontrolujte tento případ, kdy put metoda je použita a pokud true, vrátí odpověď "chybný požadavek".

  V opačném případě, pokud váš pracovní postup používá příkaz **splitOn** a akci **Odpověď,** pracovní postup se spustí asynchronně a okamžitě vrátí odpověď "202 ACCEPTED".

* Když spuštění pracovního postupu dosáhne akce **Odpověď,** ale příchozí požadavek již obdržel odpověď, akce **Odpověď** je označena jako "Se nezdařilo" z důvodu konfliktu. A v důsledku toho spuštění aplikace logiky je také označenstav "Nezdařilo se".

<a name="select-action"></a>

### <a name="select-action"></a>Vybrat akci

Tato akce vytvoří pole s objekty JSON transformací položek z jiného pole na základě zadané mapy. Výstupní pole a zdrojové pole mají vždy stejný počet položek. I když nelze změnit počet objektů ve výstupním poli, můžete přidat nebo odebrat vlastnosti a jejich hodnoty mezi těmito objekty. Vlastnost `select` určuje alespoň jeden pár klíč-hodnota, které definují mapu pro transformaci položek ve zdrojovém poli. Pár klíč hodnota představuje vlastnost a jeho hodnotu napříč všemi objekty ve výstupním poli.

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*Pole*> | Pole | Pole nebo výraz, který poskytuje zdrojové položky. Ujistěte se, že jste výraz uzavřeli s dvojitými uvozovkami. <p>**Poznámka:** Pokud je zdrojové pole prázdné, akce vytvoří prázdné pole. | 
| <*název klíče*> | Řetězec | Název vlastnosti přiřazený výsledku z <*výrazu*> <p>Chcete-li přidat novou vlastnost mezi všechny objekty ve výstupním poli, zadejte <> *názvu klíče* pro tuto vlastnost a <*výraz*> pro hodnotu vlastnosti. <p>Chcete-li odebrat vlastnost ze všech objektů v poli, vynechte <*název klíče*> pro tuto vlastnost. | 
| <*Výraz*> | Řetězec | Výraz, který transformuje položku ve zdrojovém poli a přiřadí výsledek <*názvu klíče*> | 
|||| 

Akce **Select** vytvoří pole jako výstup, takže každá akce, která chce použít tento výstup, musí buď přijmout pole, nebo musí převést pole na typ, který akce příjemce přijme. Chcete-li například převést výstupní pole na řetězec, můžete toto pole předat akci **Compose** a potom odkazovat na výstup z akce **Compose** v jiných akcích.

*Příklad*

Tato definice akce vytvoří pole objektu JSON z celého pole. Akce itetuje zdrojové pole, získá každou celou `@item()` hodnotu pomocí výrazu a`number`přiřadí každou hodnotu vlastnosti " " v každém objektu JSON:

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

Zde je pole, které tato akce vytvoří:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Chcete-li použít tento výstup pole v jiných akcích, předavěte tento výstup do akce **Compose:**

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Výstup z akce **Compose** pak můžete použít v jiných akcích, například **v Aplikaci Office 365 Outlook – Odeslat akci e-mailu:**

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

Tato akce vytvoří tabulku CSV nebo HTML z pole. Pro pole s objekty JSON tato akce automaticky vytvoří záhlaví sloupců z názvů vlastností objektů. Pro pole s jinými datovými typy je nutné zadat záhlaví sloupců a hodnoty. Toto pole například obsahuje vlastnosti "ID" a "Product_Name", které tato akce může použít pro názvy záhlaví sloupce:

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| \<> CSV *nebo* HTML| Řetězec | Formát tabulky, kterou chcete vytvořit | 
| <*Pole*> | Pole | Pole nebo výraz, který poskytuje zdrojové položky pro tabulku <p>**Poznámka:** Pokud je zdrojové pole prázdné, akce vytvoří prázdnou tabulku. | 
|||| 

*Volitelné*

Chcete-li určit nebo přizpůsobit záhlaví `columns` sloupců a hodnoty, použijte pole. Pokud `header-value` mají dvojice stejný název záhlaví, jejich hodnoty se zobrazí ve stejném sloupci pod tímto názvem záhlaví. V opačném případě každá jedinečná hlavička definuje jedinečný sloupec.

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*název sloupce*> | Řetězec | Název záhlaví sloupce | 
| <*hodnota sloupce*> | Všechny | Hodnota v tomto sloupci | 
|||| 

*Příklad 1*

Předpokládejme, že máte dříve vytvořenou proměnnou myItemArray, která aktuálně obsahuje toto pole:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Tato definice akce vytvoří tabulku CSV z proměnné myItemArray. Výraz používaný `from` vlastností získá pole z "myItemArray" `variables()` pomocí funkce:

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

Zde je tabulka CSV, kterou tato akce vytvoří: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Příklad 2*

Tato definice akce vytvoří tabulku HTML z proměnné "myItemArray". Výraz používaný `from` vlastností získá pole z "myItemArray" `variables()` pomocí funkce:

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

Zde je tabulka HTML, kterou tato akce vytvoří: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Jablka</td></tr><tr><td>1</td><td>Pomeranče</td></tr></tbody></table>

*Příklad 3*

Tato definice akce vytvoří tabulku HTML z proměnné "myItemArray". Tento příklad však přepíše výchozí názvy záhlaví sloupce "Stock_ID" a "Description" a přidá slovo "Organické" k hodnotám ve sloupci "Popis".

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

Zde je tabulka HTML, kterou tato akce vytvoří: 

<table><thead><tr><th>Stock_ID</th><th>Popis</th></tr></thead><tbody><tr><td>0</td><td>Organická jablka</td></tr><tr><td>1</td><td>Organické pomeranče</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Ukončit akci

Tato akce zastaví spuštění instance pracovního postupu, zruší všechny probíhající akce, přeskočí všechny zbývající akce a vrátí zadaný stav. Například můžete použít **akce Ukončit,** když aplikace logiky musí zcela ukončit z chybového stavu. Tato akce nemá vliv na již dokončené akce a nemůže se zobrazit uvnitř **foreach** a **until** smyčky, včetně sekvenčních smyček.

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*Stav*> | Řetězec | Stav, který chcete vrátit ke spuštění: "Nezdařilo se", "Zrušeno" nebo "Úspěšné" |
|||| 

*Volitelné*

Vlastnosti objektu runStatus platí pouze v případě, že je vlastnost "runStatus" nastavena na stav "Failed".

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*kód chyby nebo název*> | Řetězec | Kód nebo název chyby |
| <*chybová zpráva*> | Řetězec | Zpráva nebo text popisující chybu a všechny akce, které může uživatel aplikace provést | 
|||| 

*Příklad*

Tato definice akce zastaví spuštění pracovního postupu, nastaví stav spuštění na "Nezdařilo se" a vrátí stav, kód chyby a chybovou zprávu:

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

Tato akce pozastaví spuštění pracovního postupu pro zadaný interval nebo až do zadaného času, ale ne obojí.

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*počet jednotek*> | Integer | U akce **Zpoždění** se počet jednotek, které mají čekat | 
| <*Interval*> | Řetězec | Pro akci **Delay** interval čekání: "Druhý", "Minute", "Hodina", "Den", "Týden", "Měsíc" | 
| <*datum-časové razítko*> | Řetězec | Pro **akci Delay Until** datum a čas, kdy má být pokračovat v provádění. Tato hodnota musí používat [formát času data utc](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
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

Tato definice akce pozastaví pracovní postup až do zadaného času:

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

Tato akce volá jinou dříve vytvořenou aplikaci logiky, což znamená, že můžete zahrnout a znovu použít jiné pracovní postupy aplikace logiky. Můžete také použít výstupy z podřízené nebo *vnořené* aplikace logiky v akcích, které následují vnořené aplikace logiky, za předpokladu, že podřízená aplikace logiky vrátí odpověď.

Modul Logic Apps kontroluje přístup k aktivační události, kterou chcete volat, takže se ujistěte, že máte přístup k této aktivační události. Vnořená aplikace logiky musí také splňovat tato kritéria:

* Aktivační událost umožňuje volat aplikaci vnořené logiky, například [aktivační událost Požadavku](#request-trigger) nebo [HTTP](#http-trigger)

* Stejné předplatné Azure jako nadřazená aplikace logiky

* Chcete-li použít výstupy z vnořené aplikace logiky v nadřazené aplikaci logiky, musí mít vnořená aplikace logiky akci [Odpověď.](#response-action)

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*vnořený název logiky-aplikace*> | Řetězec | Název aplikace logiky, kterou chcete volat | 
| <*název aktivační události*> | Řetězec | Název aktivační události v aplikaci vnořené logiky, kterou chcete volat | 
| <*ID předplatného Azure*> | Řetězec | ID předplatného Azure pro vnořenou aplikaci logiky |
| <*Skupina prostředků Azure*> | Řetězec | Název skupiny prostředků Azure pro vnořenou aplikaci logiky |
| <*vnořený název logiky-aplikace*> | Řetězec | Název aplikace logiky, kterou chcete volat |
||||

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------|  
| <*obsah záhlaví*> | JSON – objekt | Všechna záhlaví, která se mají odeslat pomocí hovoru | 
| <*obsah těla*> | JSON – objekt | Jakýkoli obsah zprávy, který se má odeslat pomocí hovoru | 
||||

*Výstupy*

Výstupy této akce se liší v závislosti na akci odpovědi vnořené aplikace logiky. Pokud vnořená aplikace logiky neobsahuje akci Odpověď, výstupy jsou prázdné.

*Příklad*

Po úspěšném dokončení akce "Start_search" tato definice akce pracovního postupu volá jinou aplikaci logiky s názvem "Get_product_information", která předá zadané vstupy:

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

## <a name="control-workflow-action-details"></a>Řízení podrobností akce pracovního postupu

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach akce

Tato opakování akce iterates prostřednictvím pole a provádí akce na každou položku pole. Ve výchozím nastavení "pro každý" smyčky běží paralelně až do maximálního počtu smyček. Pro toto maximum naleznete [v tématu Limity a konfigurace](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Přečtěte [si, jak vytvořit smyčky "pro každou"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*akce-1... N*> | Řetězec | Názvy akcí spuštěných na každé položce pole | 
| <*akce-definice-1... N*> | JSON – objekt | Definice akcí, které jsou spuštěny | 
| <*pro každý výraz*> | Řetězec | Výraz, který odkazuje na každou položku v zadaném poli | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*Počet*> | Integer | Ve výchozím nastavení "pro každou" opakování smyčky spustit ve stejnou dobu (souběžně nebo paralelně) až do [výchozího limitu](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit tento limit nastavením nového <*počet*> hodnotu, naleznete [v tématu Změna souběžnosti smyčky "pro každý"](#change-for-each-concurrency). | 
| <*možnost operace*> | Řetězec | Chcete-li spustit smyčku "pro každý" sekvenčně, nikoli `Sequential` paralelně, nastavte <možnost *operace*> nebo <*počítat*> do `1`, ale ne obojí. Další informace naleznete [v tématu Spuštění "pro každou" smyčky postupně](#sequential-for-each). | 
|||| 

*Příklad*

Tato smyčka "pro každý" odešle e-mail pro každou položku v poli, která obsahuje přílohy z příchozího e-mailu. Smyčka odešle e-mail, včetně přílohy, osobě, která přílohu zkontroluje.

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

Chcete-li zadat pouze pole, které je předáno jako výstup z aktivační události, získá tento výraz <*>* pole z těla aktivační události. Chcete-li se vyhnout selhání, pokud pole neexistuje, výraz používá `?` operátor:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Pokud akce

Tato akce, což je *podmíněný příkaz*, vyhodnotí výraz, který představuje podmínku a spustí jinou větev na základě toho, zda je podmínka pravdivá nebo nepravdivá. Pokud je podmínka splněna, je podmínka označena stavem "Úspěšné". Přečtěte [si, jak vytvořit podmíněné příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md).

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*Podmínka*> | JSON – objekt | Podmínka, která může být výrazem, pro vyhodnocení | 
| <*akce-1*> | JSON – objekt | Akce, která má být spuštěna při> <*podmínka* vyhodnocena jako true. | 
| <*definice akce*> | JSON – objekt | Definice akce | 
| <*akce-2*> | JSON – objekt | Akce, která má být spuštěna při <*stavu,*> vyhodnocena jako nepravdivá. | 
|||| 

Akce v `actions` objektech nebo `else` získají tyto stavy:

* "Úspěšné", když běží a uspět
* "Selhalo" při spuštění a selhání
* "Přeskočeno", když příslušná větev neběží

*Příklad*

Tato podmínka určuje, že pokud má celá proměnná hodnotu větší než nula, pracovní postup zkontroluje web. Pokud je proměnná nula nebo méně, pracovní postup zkontroluje jiný web.

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

Zde je několik příkladů, které ukazují, jak můžete použít výrazy v podmínkách:
  
| JSON | Výsledek | 
|------|--------| 
| "expression":@parameters" ('<*hasSpecialAction*>')" | Pouze pro logické výrazy předá podmínka pro libovolnou hodnotu, která je vyhodnocena jako true. <p>Chcete-li převést jiné typy na `empty()` `equals()`logické hodnoty, použijte tyto funkce: nebo . | 
| "expression":@greater" (akce ('<*akce*>').output.value, parameters('<*práh*>')" | Pro funkce porovnání akce spustí pouze v případě, že výstup z <*akce*> je větší než *prahová hodnota* <>. | 
| "expression":@or" (greater(actions('<*action*>').output.value, parameters('<*threshold*>')), less(actions('<*same-action*>').output.value, 100))" | Pro logické funkce a vytváření vnořených logických výrazů se akce spustí, když je výstup z <*akce*> větší než *prahová hodnota* <> nebo pod 100. | 
| "výraz":@equals" (length(actions('<*akce*>').outputs.errors), 0))" | Můžete použít funkce pole pro kontrolu, zda pole má nějaké položky. Akce se spustí, `errors` když je pole prázdné. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Akce oboru

Tato akce logicky seskupí akce do *oborů*, které získají svůj vlastní stav po dokončení akce v tomto oboru. Potom můžete použít stav oboru k určení, zda jsou spuštěny další akce. Přečtěte [si, jak vytvořit obory](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

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

| Hodnota | Typ | Popis | 
|-------|------|-------------|  
| <*vnitřní akce-1... N*> | JSON – objekt | Jedna nebo více akcí spuštěné uvnitř oboru |
| <*vstupy akcí*> | JSON – objekt | Vstupy pro každou akci |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Akce Switch

Tato akce, označovaná také jako *příkaz switch*, uspořádá další akce do *případů*a přiřadí hodnotu každému případu, s výjimkou výchozího případu, pokud existuje. Při spuštění pracovního postupu akce **Přepnout** porovná hodnotu z výrazu, objektu nebo tokenu s hodnotami určenými pro každý případ. Pokud akce **Přepnout** najde odpovídající případ, pracovní postup spustí pouze akce pro tento případ. Při každém spuštění akce **Přepnout** existuje pouze jeden odpovídající případ nebo neexistují žádné shody. Pokud neexistují žádné shody, akce **Přepnout** spustí výchozí akce. Přečtěte [si, jak vytvořit příkazy přepínače](../logic-apps/logic-apps-control-flow-switch-statement.md).

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*výraz-objekt nebo token*> | Různé | Výraz, objekt JSON nebo token k vyhodnocení | 
| <*název akce*> | Řetězec | Název akce, která má být spuštěna pro odpovídající případ | 
| <*definice akce*> | JSON – objekt | Definice akce, která má být spuštěna pro odpovídající případ | 
| <*odpovídající hodnota*> | Různé | Hodnota pro porovnání s vyhodnoceným výsledkem | 
|||| 

*Volitelné*

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*název výchozí akce*> | Řetězec | Název výchozí akce, která má být spuštěna, pokud neexistuje žádný odpovídající případ | 
| <*výchozí definice akce*> | JSON – objekt | Definice akce, která má být spuštěna, pokud neexistuje žádný odpovídající případ | 
|||| 

*Příklad*

Tato definice akce vyhodnocuje, zda osoba, která odpovídá na e-mail žádosti o schválení, vybrala možnost "Schválit" nebo Možnost Odmítnout. Na základě této volby akce **Přepnout** spustí akce pro odpovídající případ, který je poslat další e-mail respondéru, ale s různými formulacemi v každém případě. 

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

### <a name="until-action"></a>Dokud akce

Tato akce smyčky obsahuje akce, které jsou spuštěny, dokud není zadaná podmínka pravdivá. Smyčka zkontroluje podmínku jako poslední krok po spuštění všech ostatních akcí. Do objektu můžete zahrnout `"actions"` více než jednu akci a akce musí definovat alespoň jeden limit. Naučte [se, jak vytvořit smyčky "do"](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

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

| Hodnota | Typ | Popis | 
|-------|------|-------------| 
| <*název akce*> | Řetězec | Název akce, kterou chcete spustit uvnitř smyčky | 
| <*typ akce*> | Řetězec | Typ akce, který chcete spustit | 
| <*vstupy akcí*> | Různých | Vstupy pro spuštění akce | 
| <*Podmínka*> | Řetězec | Podmínka nebo výraz vyhodnotit po dokončení všech akcí ve smyčce | 
| <*počet smyček*> | Integer | Omezení na nejvíce počet smyček, které lze spustit akce. Další informace o výchozím limitu a maximálním limitu najdete v [tématu Limity a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*Časový čas smyčky*> | Řetězec | Limit na nejdelší dobu, po kterou může být smyčka spuštěna. Výchozí `timeout` hodnota `PT1H`je , což je požadovaný [formát ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
|||| 

*Příklad*

Tato definice akce smyčky odešle požadavek HTTP na zadanou adresu URL, dokud nebude splněna jedna z těchto podmínek:

* Požadavek získá odpověď se stavovým kódem "200 OK".
* Smyčka byla spuštěna 60krát.
* Smyčka běží už hodinu.

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

## <a name="webhooks-and-subscriptions"></a>Webové háčky a předplatná

Aktivační události a akce založené na webhooku pravidelně nekontrolují koncové body, ale místo toho v ytwait u těchto koncových bodů počkejte na konkrétní události nebo data. Tyto aktivační události a akce *se přihlásí ke* koncovým bodům tím, že poskytují adresu URL *zpětného volání,* kde koncový bod může odesílat odpovědi.

K `subscribe` volání dojde, když se pracovní postup jakýmkoli způsobem změní, například při obnovení přihlašovacích údajů nebo při změně vstupních parametrů pro aktivační událost nebo akci. Toto volání používá stejné parametry jako standardní akce HTTP. 

K `unsubscribe` volání automaticky dojde, když operace způsobí, že aktivační událost nebo akce bude neplatná, například:

* Odstranění nebo zakázání aktivační události. 
* Odstranění nebo zakázání pracovního postupu. 
* Odstranění nebo zakázání předplatného. 

Pro podporu těchto `@listCallbackUrl()` volání výraz vrátí jedinečnou "adresu URL zpětného volání" pro aktivační událost nebo akci. Tato adresa URL představuje jedinečný identifikátor pro koncové body, které používají rozhraní REST API služby. Parametry pro tuto funkci jsou stejné jako webhooku aktivační události nebo akce.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Změna asynchronní doby trvání

Pro aktivační události a akce můžete omezit dobu trvání asynchronního vzoru na `limit.timeout` určitý časový interval přidáním vlastnosti. Tímto způsobem, pokud akce nebyla dokončena po uplynutí intervalu, stav `Cancelled` akce `ActionTimedOut` je označen jako s kódem. Vlastnost `timeout` používá [formát ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

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

## <a name="runtime-configuration-settings"></a>Nastavení konfigurace runtime

Výchozí chování za běhu pro aktivační události a `runtimeConfiguration` akce můžete změnit přidáním těchto vlastností do definice aktivační události nebo akce.

| Vlastnost | Typ | Popis | Aktivační událost nebo akce | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Změňte [*výchozí limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) počtu instancí pracovního postupu, které lze spustit současně (souběžně nebo paralelně). Úprava této hodnoty může pomoci omezit počet požadavků, které back-endové systémy přijímat. <p>Nastavení `runs` vlastnosti `1` funguje stejným způsobem `operationOptions` jako `SingleInstance`nastavení vlastnosti na . Můžete nastavit buď vlastnost, ale ne obojí. <p>Pokud chcete změnit výchozí limit, přečtěte si následující informace [o změně souběžnosti aktivačníudálosti](#change-trigger-concurrency) nebo [instance aktivační události](#sequential-trigger). | Všechny aktivační události | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Změňte [*výchozí limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) počtu instancí pracovního postupu, které musí čekat na spuštění, když vaše aplikace logiky je již spuštěna maximální souběžné instance. <p>Pokud chcete změnit výchozí limit, přečtěte si informace [o omezení počtu přečkání čekání](#change-waiting-runs). | Všechny aktivační události | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Změňte [*výchozí limit*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) počtu iterací smyčky "pro každou" smyčku, která lze spustit současně (souběžně nebo paralelně). <p>Nastavení `repetitions` vlastnosti `1` funguje stejným způsobem `operationOptions` jako `SingleInstance`nastavení vlastnosti na . Můžete nastavit buď vlastnost, ale ne obojí. <p>Chcete-li změnit výchozí limit, viz [Změna souběžnosti pro každou nebo](#change-for-each-concurrency) Spustit ["pro každou" smyčky postupně](#sequential-for-each). | Akce: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Integer | Pro konkrétní akce, které podporují a mají stránkování zapnuta, tato hodnota určuje *minimální* počet výsledků načíst. <p>Pokud chcete zapnout stránkování, přečtěte si část [Získání hromadných dat, položek nebo výsledků pomocí stránkování.](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Akce: Pestrá |
| `runtimeConfiguration.secureData.properties` | Pole | Na mnoha aktivačních událostí a akcí, tato nastavení skrýt vstupy, výstupy nebo obojí z historie spuštění aplikace logiky. <p>Další informace o zabezpečení těchto dat najdete v [tématu Skrýt vstupy a výstupy z historie spuštění](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | Většina aktivačních událostí a akcí |
| `runtimeConfiguration.staticResult` | JSON – objekt | Pro akce, které podporují a mají zapnuté nastavení [statického výsledku,](../logic-apps/test-logic-apps-mock-data-static-results.md) `staticResult` má objekt tyto atributy: <p>- `name`, který odkazuje na název definice statického výsledku `staticResults` aktuální akce, který se `definition` zobrazí uvnitř atributu v atributu pracovního postupu aplikace logiky. Další informace naleznete [v tématu Statické výsledky – odkaz na schéma pro jazyk definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, který určuje, zda `Enabled` jsou statické výsledky pro aktuální akci nebo ne. <p>Pokud chcete zapnout statické výsledky, přečtěte si témat [test aplikací logiky s falešnými daty nastavením statických výsledků.](../logic-apps/test-logic-apps-mock-data-static-results.md) | Akce: Pestrá |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Možnosti operace

Výchozí chování aktivačních událostí a akcí `operationOptions` můžete změnit s vlastností v definici aktivační události nebo akce.

| Možnost operace | Typ | Popis | Aktivační událost nebo akce | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Řetězec | Spouštět akce založené na protokolu HTTP synchronně, nikoli asynchronně. <p><p>Chcete-li tuto možnost nastavit, přečtěte si téma [Synchronně spustit akce](#asynchronous-patterns). | Akce: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Reakce](#response-action) | 
| `OptimizedForHighThroughput` | Řetězec | Změňte [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) počtu spuštění akcí za 5 minut na maximální [limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Chcete-li tuto možnost nastavit, přečtěte si téma [Spustit v režimu vysoké propustnosti](#run-high-throughput-mode). | Všechny akce | 
| `Sequential` | Řetězec | Spouštět "pro každý" opakování smyčky jeden po druhém, spíše než všechny současně paralelně. <p>Tato možnost funguje stejným způsobem `runtimeConfiguration.concurrency.repetitions` jako `1`nastavení vlastnosti na . Můžete nastavit buď vlastnost, ale ne obojí. <p><p>Chcete-li tuto možnost nastavit, přečtěte [si téma Spuštění smyčků "pro každou" postupně](#sequential-for-each).| Akce: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Řetězec | Spusťte aktivační událost pro každou instanci aplikace logiky postupně a počkejte na dokončení dříve aktivního spuštění před aktivací další instance aplikace logiky. <p><p>Tato možnost funguje stejným způsobem `runtimeConfiguration.concurrency.runs` jako `1`nastavení vlastnosti na . Můžete nastavit buď vlastnost, ale ne obojí. <p>Chcete-li tuto možnost nastavit, přečtěte [si téma Trigger instance postupně](#sequential-trigger). | Všechny aktivační události | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Změna souběžnosti triggeru

Ve výchozím nastavení instance pracovního postupu aplikace logiky všechny spustit ve stejnou dobu (souběžně nebo paralelně). Toto chování znamená, že každá instance aktivační události se aktivuje před dokončením spuštění dříve aktivní instance pracovního postupu. Počet souběžně spuštěných instancí má však [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Když počet souběžně spuštěných instancí pracovního postupu dosáhne tohoto limitu, musí všechny ostatní nové instance čekat na spuštění. Toto omezení pomáhá řídit počet požadavků, které back-endové systémy přijímat.

Chcete-li změnit výchozí limit, můžete použít editor zobrazení kódu nebo Návrhář logic apps, protože `runtimeConfiguration.concurrency.runs` změna nastavení souběžnosti prostřednictvím návrháře přidá nebo aktualizuje vlastnost v definici základní aktivační události a naopak. Tato vlastnost řídí maximální počet instancí pracovního postupu, které lze spustit paralelně. Zde jsou některé důležité informace o tom, kdy chcete povolit řízení souběžnosti:

* Pokud je povolena souběžnost, [limit SplitOn](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) je výrazně snížena pro [debatní pole](#split-on-debatch). Pokud počet položek překročí tento limit, je funkce SplitOn zakázána.

* Při souběžnosti je povolena, dlouhotrvající instance aplikace logiky může způsobit nové instance aplikace logiky pro zadání stavu čekání. Tento stav zabraňuje Azure Logic Apps z vytváření nových instancí a stane i v případě, že počet souběžných spuštění je menší než zadaný maximální počet souběžných spuštění.

  * Chcete-li tento stav přerušit, zrušte nejstarší instance, které jsou *stále spuštěny*.

    1. V nabídce aplikace logiky vyberte **Přehled**.

    1. V části **Historie spuštění** vyberte nejstarší instanci, která je stále spuštěná, například:

       ![Vybrat nejstarší spuštěnou instanci](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Chcete-li zobrazit pouze instance, které jsou stále spuštěné, otevřete seznam **Vše** a vyberte **Možnost Spuštěno**.

    1. V části **Spuštění aplikace Logika**vyberte **Zrušit spuštění**.

       ![Najít nejstarší spuštěnou instanci](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Chcete-li tuto možnost obejít, přidejte časový plán k jakékoli akci, která by mohla tyto spuštění podržet. Pokud pracujete v editoru kódu, přečtěte si informace [o změně asynchronní doby trvání](#asynchronous-limits). V opačném případě, pokud používáte návrháře, postupujte takto:

    1. V aplikaci logiky vakcinaci, do které chcete přidat časový limit, vyberte v pravém horním rohu tlačítko Elipsy (**...**) a pak vyberte **Nastavení**.

       ![Otevřít nastavení akce](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. V části **Časový čas**zadejte dobu trvání časového roku ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Určit dobu trvání časového období](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Chcete-li spustit aplikaci logiky postupně, nastavte `1` souběžnost aktivační události buď pomocí editoru zobrazení kódu nebo návrháře. Ujistěte se, že také nenastavíte `operationOptions` `SingleInstance` vlastnost aktivační události v editoru zobrazení kódu. V opačném případě se zobrazí chyba ověření. Další informace naleznete v tématu [Trigger instance postupně](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Úpravy v zobrazení kódu 

V definici základní aktivační `runtimeConfiguration.concurrency.runs` události přidejte vlastnost, která `1` může `50`mít hodnotu, která se pohybuje od .

Zde je příklad, který omezuje souběžné spuštění na 10 instancí:

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

Další informace naleznete v [tématu Runtime configuration settings](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Úpravy v Návrháři aplikací logiky

1. V pravém horním rohu spouště vyberte tlačítko elipsy (**...**) a pak vyberte **Nastavení**.

1. V části **Řízení souběžnosti**nastavte **limit** na **zapnuto**. 

1. Přetáhněte jezdec **Stupeň paralelismu** na požadovanou hodnotu. Chcete-li spustit aplikaci logiky postupně, přetáhněte hodnotu jezdce na **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Změnit souběžnost "pro každou"

Ve výchozím nastavení "pro každý" opakování smyčky všechny spustit ve stejnou dobu (současně nebo paralelně). Toto chování znamená, že každá iterace spustí před dokončením předchozí iterace. Počet souběžně spuštěných iterací má však [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Když počet souběžně spuštěných iterací dosáhne tohoto limitu, musí všechny ostatní iterace čekat na spuštění.

Chcete-li změnit výchozí limit, můžete použít editor zobrazení kódu nebo Návrhář logic apps, protože `runtimeConfiguration.concurrency.repetitions` změna nastavení souběžnosti prostřednictvím návrháře přidá nebo aktualizuje vlastnost v základní definici akce "pro každou" a naopak. Tato vlastnost řídí maximální počet iterací, které lze spustit paralelně.

> [!NOTE] 
> Pokud nastavíte akci "pro každou" tak, aby se spouštěla postupně pomocí návrháře `operationOptions` nebo `Sequential` editoru zobrazení kódu, nenastavujte vlastnost akce v editoru zobrazení kódu. V opačném případě se zobrazí chyba ověření. Další informace naleznete [v tématu Spuštění "pro každou" smyčky postupně](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Úpravy v zobrazení kódu 

V základní definici "pro každou" `runtimeConfiguration.concurrency.repetitions` přidejte nebo aktualizujte vlastnost, `1` která `50`může mít hodnotu, která se pohybuje od a .

Zde je příklad, který omezuje souběžné spuštění na 10 iterací:

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

Další informace naleznete v [tématu Runtime configuration settings](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Úpravy v Návrháři aplikací logiky

1. V části **Pro každou** akci vyberte v pravém horním rohu tlačítko Elipsy (**...**) a pak vyberte **Nastavení**.

1. V části **Řízení souběžnosti**nastavte **řízení souběžnosti** **na Zapnuto**.

1. Přetáhněte jezdec **Stupeň paralelismu** na požadovanou hodnotu. Chcete-li spustit aplikaci logiky postupně, přetáhněte hodnotu jezdce na **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Omezit limit čekání na počet běhů

Ve výchozím nastavení instance pracovního postupu aplikace logiky všechny spustit ve stejnou dobu (souběžně nebo paralelně). Toto chování znamená, že každá instance aktivační události se aktivuje před dokončením spuštění dříve aktivní instance pracovního postupu. Počet souběžně spuštěných instancí má však [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Když počet souběžně spuštěných instancí pracovního postupu dosáhne tohoto limitu, musí všechny ostatní nové instance čekat na spuštění.

Počet čekání běží má také [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Když počet čekajících spuštění dosáhne tohoto limitu, modul Logic Apps již nepřijímá nové spuštění. Aktivační události požadavku a webového háku vrátí 429 chyb a opakované aktivační události začnou přeskakovat pokusy o dotazování.

Můžete nejen [změnit výchozí limit souběžnosti aktivační události](#change-trigger-concurrency), ale můžete také změnit výchozí limit pro čekání na spuštění. V definici základní aktivační `runtimeConfiguration.concurrency.maximumWaitingRuns` události přidejte vlastnost, která `1` může `100`mít hodnotu, která se pohybuje od .

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

Další informace naleznete v [tématu Runtime configuration settings](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Postupně aktivační události

Chcete-li spustit každou instanci pracovního postupu aplikace logiky až po dokončení spuštění předchozí instance, nastavte aktivační událost spustit postupně. Můžete použít editor zobrazení kódu nebo Návrhář logic apps, protože změna nastavení `runtimeConfiguration.concurrency.runs` souběžnosti prostřednictvím návrháře také přidává nebo aktualizuje vlastnost v definici základní aktivační události a naopak.

> [!NOTE] 
> Pokud nastavíte aktivační událost spustit postupně buď pomocí návrháře nebo editoru zobrazení `operationOptions` kódu, `Sequential` nenastavujte vlastnost aktivační události v editoru zobrazení kódu. V opačném případě se zobrazí chyba ověření. 

#### <a name="edit-in-code-view"></a>Úpravy v zobrazení kódu

V definici aktivační události nastavte některou z těchto vlastností, ale ne obojí. 

Nastavte `runtimeConfiguration.concurrency.runs` vlastnost `1`na :

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

*-nebo-*

Nastavte `operationOptions` vlastnost `SingleInstance`na :

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

Další informace naleznete v [tématu Nastavení konfigurace runtime](#runtime-config-options) a [možnosti operace](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Úpravy v Návrháři aplikací logiky

1. V pravém horním rohu spouště vyberte tlačítko elipsy (**...**) a pak vyberte **Nastavení**.

1. V části **Řízení souběžnosti**nastavte **limit** na **zapnuto**. 

1. Přetáhněte jezdec **Stupeň paralelismu** `1`na číslo . 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Spustit "pro každou" smyčky postupně

Chcete-li spustit iteraci smyčky "pro každou" až po dokončení předchozí iterace, nastavte akci "pro každou" tak, aby se spouštěla postupně. Můžete použít editor zobrazení kódu nebo Návrhář logic apps, protože změna souběžnosti akce `runtimeConfiguration.concurrency.repetitions` prostřednictvím návrháře také přidává nebo aktualizuje vlastnost v definici základní akce a naopak.

> [!NOTE] 
> Pokud nastavíte akci "pro každou" tak, aby se spouštěla postupně pomocí návrháře `operationOptions` nebo `Sequential` editoru zobrazení kódu, nenastavujte vlastnost akce v editoru zobrazení kódu. V opačném případě se zobrazí chyba ověření. 

#### <a name="edit-in-code-view"></a>Úpravy v zobrazení kódu

V definici akce nastavte některou z těchto vlastností, ale ne obojí. 

Nastavte `runtimeConfiguration.concurrency.repetitions` vlastnost `1`na :

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

*-nebo-*

Nastavte `operationOptions` vlastnost `Sequential`na :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Další informace naleznete v [tématu Nastavení konfigurace runtime](#runtime-config-options) a [možnosti operace](#operation-options).

#### <a name="edit-in-logic-apps-designer"></a>Úpravy v Návrháři aplikací logiky

1. V pravém horním rohu **pro každou** akci vyberte tlačítko elipsy (**...**) a pak vyberte **Nastavení**.

1. V části **Řízení souběžnosti**nastavte **řízení souběžnosti** **na Zapnuto**.

1. Přetáhněte jezdec **Stupeň paralelismu** `1`na číslo .

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Spouštění akcí synchronně

Ve výchozím nastavení se všechny akce založené na protokolu HTTP řídí standardním vzorem asynchronní operace. Tento vzor určuje, že když akce založená na protokolu HTTP odešle požadavek na zadaný koncový bod, vzdálený server odešle zpět odpověď "202 ACCEPTED". Tato odpověď znamená, že server přijal požadavek na zpracování. Modul Logic Apps udržuje kontrolu adresy URL určené hlavičkou umístění odpovědi, dokud se zpracování nezastaví, což je jakákoli odpověď bez 202.

Požadavky však mají časový limit, takže pro dlouhotrvající akce můžete zakázat asynchronní chování `operationOptions` přidáním `DisableAsyncPattern` a nastavením vlastnosti pod vstupy akce.

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

Další informace naleznete v [tématu Možnosti operace](#operation-options).

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Spustit v režimu vysoké propustnosti

Pro definici jedné aplikace logiky má počet akcí, které se provádějí každých 5 minut, [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Chcete-li zvýšit [maximum](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) tento limit na `operationOptions` maximální `OptimizedForHighThroughput`možné, nastavte vlastnost na . Toto nastavení přepne aplikaci logiky do režimu "vysoké propustnosti".

> [!NOTE]
> Režim vysoké propustnosti je ve verzi Preview. Můžete také distribuovat úlohy napříč více než jednu aplikaci logiky podle potřeby.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Ověření aktivačních událostí a akcí

Koncové body HTTP a HTTPS podporují různé druhy ověřování. Na základě aktivační události nebo akce, které používáte k odchozím voláním nebo požadavkům na přístup k těmto koncovým bodům, můžete vybrat z různých rozsahů typů ověřování. Další informace naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Další kroky

* Další informace o [definičním jazyce pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md)

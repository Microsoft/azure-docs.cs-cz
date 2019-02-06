---
title: Aktivační událost a akce typů reference – Azure Logic Apps | Dokumentace Microsoftu
description: Další informace o typech aktivační události a akce v Azure Logic Apps, jak je popsáno ve schématu jazyka definice pracovního postupu
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/22/2018
ms.openlocfilehash: 4fc30deb68039130850f87cb70dbb606be463600
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747386"
---
# <a name="trigger-and-action-types-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Aktivační událost a akce referenční typy pro jazyk pro definování pracovních postupů v Azure Logic Apps

V [Azure Logic Apps](../logic-apps/logic-apps-overview.md), spuštění všech pracovních postupů aplikace logiky s triggery a akce. Tento článek popisuje typy aktivační události a akce, které můžete použít při vytváření aplikací logiky pro automatizaci úloh, procesů a pracovních postupů. Můžete vytvořit logiku aplikace pracovních postupů pomocí návrháře pro Logic Apps, vizuálně nebo vytvořením základní definice pracovního postupu s [jazyka definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md). Můžete vytvářet aplikace logiky v portálu Azure portal nebo Visual Studio. Základní definice pracovního postupu včetně triggeru a akcí, používá zápis JSON (Javascript Object).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Přehled aktivační události

Spustit všechny aplikace logiky s triggerem, který definuje volání, která instance a spustit pracovní postup aplikace logiky. Tady jsou kategorie Obecné aktivační události:

* A *dotazování* aktivační události, které slouží k ověření koncového bodu služby v pravidelných intervalech

* A *nabízených* aktivační událost, která vytvoří předplatné na koncový bod a poskytuje *adresu URL zpětného volání* tak koncový bod mohou odesílat upozornění na aktivační událost se stane, zadané události nebo data jsou k dispozici. Aktivační události potom čeká koncový bod odpověď před jeho spuštění.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Název aktivační události*> | String | Název aktivační události |
| <*Typ aktivační události*> | String | Typ aktivační události, jako je například "Http" nebo "ApiConnection" |
| <*vstupy triggeru*> | JSON – objekt | Vstupy, které definují chování triggeru |
| <*time-unit*> | String | Jednotka času, který popisuje, jak často se trigger spustí: "Druhý", "Minute", "Hour", "Day", "Týden", "Měsíc" |
| <*číslo z časových jednotek*> | Integer | Hodnota, která určuje, jak často se trigger spustí na základě četnosti, což je počet časových jednotek počkat, až se trigger spustí znovu <p>Toto jsou minimální a maximální intervaly: <p>-Měsíc: 1 – 16 měsíců </br>-Den: 1 – 500 dnů </br>-Hodinu: 1 – 12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Sekundu: 1-9,999,999 sekund<p>Například pokud je interval 6 a je frekvence "Měsíc", opakování je nastaveno na každých 6 měsíců. |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*array-with-conditions*> | Pole | Pole, které obsahuje jeden nebo více [podmínky](#trigger-conditions) , které určují, jestli se má spustit pracovní postup. K dispozici pouze pro aktivační události. |
| <*runtime-config-options*> | JSON – objekt | Můžete změnit chování modulu runtime triggeru nastavením `runtimeConfiguration` vlastnosti. Další informace najdete v tématu [nastavení konfigurace modulu Runtime](#runtime-config-options). |
| <*splitOn-expression*> | String | Pro aktivační události, které vrací pole můžete zadat výraz, který [rozdělí nebo *debatches* ](#split-on-debatch) položek pole do několika instancí pracovních postupů pro zpracování. |
| <*operation-option*> | String | Výchozí chování můžete změnit nastavením `operationOptions` vlastnost. Další informace najdete v tématu [možnosti operace](#operation-options). |
||||

## <a name="trigger-types-list"></a>Seznam typů aktivační událost

Každý typ aktivační události má jiné rozhraní a vstupy, které definují chování aktivační události.

### <a name="built-in-triggers"></a>Integrované aktivační události

| Typ aktivační události | Popis |
|--------------|-------------|
| [**HTTP**](#http-trigger) | Ověří nebo *hlasování* libovolný koncový bod. Tento koncový bod musí odpovídat kontrakt konkrétní aktivační události pomocí "202" asynchronní vzor nebo tak, že vrací pole. |
| [**HTTPWebhook**](#http-webhook-trigger) | Vytvoří koncový bod volatelná aplikacemi pro aplikaci logiky, ale volá zadaná adresa URL pro registraci nebo zrušíte její registraci. |
| [**Opakování**](#recurrence-trigger) | Je aktivována podle předem definovaného plánu. Můžete nastavit budoucí datum a čas pro aktivaci této aktivační události. Na základě četnosti, můžete také určit časy a dny pro spuštění vašeho pracovního postupu. |
| [**Požadavek**](#request-trigger)  | Vytvoří koncový bod volatelná aplikacemi pro aplikaci logiky a je také označován jako "Ruční" aktivační události. Viz například [volání triggeru, nebo vnořené pracovní postupy s koncovými body HTTP](../logic-apps/logic-apps-http-endpoint.md). |
|||

### <a name="managed-api-triggers"></a>Spravované rozhraní API aktivační události

| Typ aktivační události | Popis |
|--------------|-------------|
| [**ApiConnection**](#apiconnection-trigger) | Ověří nebo *hlasování* koncový bod pomocí [rozhraní API spravovaná Microsoftem](../connectors/apis-list.md). |
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Vytvoří koncový bod volatelná aplikacemi pro aplikaci logiky voláním [rozhraní API spravovaná Microsoftem](../connectors/apis-list.md) pro předplatné a odhlášení odběru. |
|||

## <a name="triggers---detailed-reference"></a>Aktivační události – odkaz na podrobné

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>Triggeru APIConnection

Kontroluje, Tato aktivační událost nebo *hlasování* koncový bod pomocí [rozhraní API spravovaná Microsoftem](../connectors/apis-list.md) tak parametry pro tuto aktivační událost se může lišit podle koncového bodu. Mnoho oddílů v této definici aktivační události jsou volitelné. Aktivační události chování závisí na oddíly Určuje, jestli jsou zahrnuté.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | String | Název aktivační události |
| <*Název připojení*> | String | Název připojení spravovaného rozhraní API, která používá pracovní postup |
| <*Typ metody*> | String | Metoda HTTP pro komunikaci s spravované rozhraní API: "GET", "VLOŽIT", "POST", "OPRAVY", "ODSTRANIT" |
| <*api-operation*> | String | Operace rozhraní API pro volání |
| <*time-unit*> | String | Jednotka času, který popisuje, jak často se trigger spustí: "Druhý", "Minute", "Hour", "Day", "Týden", "Měsíc" |
| <*číslo z časových jednotek*> | Integer | Hodnota, která určuje, jak často se trigger spustí na základě četnosti, což je počet časových jednotek počkat, až se trigger spustí znovu <p>Toto jsou minimální a maximální intervaly: <p>-Měsíc: 1 – 16 měsíců </br>-Den: 1 – 500 dnů </br>-Hodinu: 1 – 12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Sekundu: 1-9,999,999 sekund<p>Například pokud je interval 6 a je frekvence "Měsíc", opakování je nastaveno na každých 6 měsíců. |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*retry-behavior*> | JSON – objekt | Přizpůsobení chování opakování pro občasné chyby, které mají 408, 429 a 5XX stavový kód a všechny výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*query-parameters*> | JSON – objekt | Žádné parametry dotazu zahrnout pomocí rozhraní API volat. Například `"queries": { "api-version": "2018-01-01" }` přidá objekt `?api-version=2018-01-01` volání. |
| <*max-runs*> | Integer | Ve výchozím instancí pracovních postupů logic app spustí ve stejnou dobu nebo paralelní až [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit tak, že nastavíte nový <*počet*> hodnota naleznete v tématu [souběžnosti aktivační události změnit](#change-trigger-concurrency). |
| <*max-runs-queue*> | Integer | Pokud vaše aplikace logiky je již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou vloženy do této fronty [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit výchozí omezení, [omezit spuštění čekajících změn](#change-waiting-runs). |
| <*splitOn-expression*> | String | Pro aktivační události, které vrací pole tento výraz odkazuje na pole, které chcete použít, takže můžete vytvořit a spustit instance pracovního postupu pro každou položku pole, místo použít smyčka "Foreach". Při použití `SplitOn` vlastnost, získáte souběžných instancí až do limitu, který aktivační událost a služba může vrátit. <p>Například tento výraz představuje položku v poli vráceném v rámci obsah textu triggeru: `@triggerbody()?['value']` |
| <*operation-option*> | String | Výchozí chování můžete změnit nastavením `operationOptions` vlastnost. Další informace najdete v tématu [možnosti operace](#operation-options). |
||||

*Výstupy*

| Element | Type | Popis |
|---------|------|-------------|
| Záhlaví | JSON – objekt | Hlavičky z odpovědi |
| hlavní část | JSON – objekt | Text z odpovědi |
| Stavový kód | Integer | Stavový kód z odpovědi |
||||

*Příklad*

Tato definice aktivační události kontrolovat nové e-maily každý den do doručené pošty pro účet Office 365 Outlook: 

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

### <a name="apiconnectionwebhook-trigger"></a>Triggeru ApiConnectionWebhook

Tato aktivační událost odešle žádost předplatné na koncový bod pomocí [rozhraní API spravovaná Microsoftem](../connectors/apis-list.md), poskytuje *adresu URL zpětného volání* k kam poslat na koncový bod odpověď a čeká na koncový bod pro reakce. Další informace najdete v tématu [koncový bod předplatná](#subscribe-unsubscribe).

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Název připojení*> | String | Název připojení spravovaného rozhraní API, která používá pracovní postup |
| <*body-content*> | JSON – objekt | Žádný obsah zprávy k odeslání jako datovou část pro spravované rozhraní API |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*retry-behavior*> | JSON – objekt | Přizpůsobení chování opakování pro občasné chyby, které mají 408, 429 a 5XX stavový kód a všechny výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*query-parameters*> | JSON – objekt | Žádné parametry dotazu zahrnout pomocí volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` přidá objekt `?api-version=2018-01-01` volání. |
| <*max-runs*> | Integer | Ve výchozím instancí pracovních postupů logic app spustí ve stejnou dobu nebo paralelní až [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit tak, že nastavíte nový <*počet*> hodnota naleznete v tématu [souběžnosti aktivační události změnit](#change-trigger-concurrency). |
| <*max-runs-queue*> | Integer | Pokud vaše aplikace logiky je již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou vloženy do této fronty [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit výchozí omezení, [omezit spuštění čekajících změn](#change-waiting-runs). |
| <*splitOn-expression*> | String | Pro aktivační události, které vrací pole tento výraz odkazuje na pole, které chcete použít, takže můžete vytvořit a spustit instance pracovního postupu pro každou položku pole, místo použít smyčka "Foreach". Při použití `SplitOn` vlastnost, získáte souběžných instancí až do limitu, který aktivační událost a služba může vrátit. <p>Například tento výraz představuje položku v poli vráceném v rámci obsah textu triggeru: `@triggerbody()?['value']` |
| <*operation-option*> | String | Výchozí chování můžete změnit nastavením `operationOptions` vlastnost. Další informace najdete v tématu [možnosti operace](#operation-options). |
||||

*Příklad*

Tato definice aktivační události přihlásí k rozhraní API Office 365 Outlook, poskytuje adresu URL zpětného volání pro koncový bod rozhraní API a čeká na koncový bod pro reakce, když přijde nový e-mail.

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

Tento trigger zkontroluje nebo dotazuje zadaný koncový bod na základě plánu opakování zadané. Odpovědi koncový bod určuje, zda pracovní postup probíhá.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Typ metody*> | String | Metoda HTTP pro účely dotazování zadaný koncový bod: "GET", "VLOŽIT", "POST", "OPRAVY", "ODSTRANIT" |
| <*endpoint-URL*> | String | HTTP nebo HTTPS URL pro koncový bod dotazování <p>Maximální velikost řetězce: 2 KB |
| <*time-unit*> | String | Jednotka času, který popisuje, jak často se trigger spustí: "Druhý", "Minute", "Hour", "Day", "Týden", "Měsíc" |
| <*číslo z časových jednotek*> | Integer | Hodnota, která určuje, jak často se trigger spustí na základě četnosti, což je počet časových jednotek počkat, až se trigger spustí znovu <p>Toto jsou minimální a maximální intervaly: <p>-Měsíc: 1 – 16 měsíců </br>-Den: 1 – 500 dnů </br>-Hodinu: 1 – 12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Sekundu: 1-9,999,999 sekund<p>Například pokud je interval 6 a je frekvence "Měsíc", opakování je nastaveno na každých 6 měsíců. |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*header-content*> | JSON – objekt | Hlavičky pro odesílání s požadavkem <p>Chcete-li například nastavit jazyk a typ požadavku: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | String | Obsah zprávy k odeslání jako datovou část s požadavkem |
| <*ověřování – metoda*> | JSON – objekt | Metoda požadavku se používá k ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). Nad rámec Scheduleru `authority` vlastnost je podporována. Pokud není zadán, výchozí hodnota je `https://login.windows.net`, můžete ale použít jinou hodnotu, jako například`https://login.windows\-ppe.net`. |
| <*retry-behavior*> | JSON – objekt | Přizpůsobení chování opakování pro občasné chyby, které mají 408, 429 a 5XX stavový kód a všechny výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
 <*query-parameters*> | JSON – objekt | Žádné parametry dotazu zahrnout s požadavkem <p>Například `"queries": { "api-version": "2018-01-01" }` objekt přidá `?api-version=2018-01-01` na požadavek. |
| <*max-runs*> | Integer | Ve výchozím instancí pracovních postupů logic app spustí ve stejnou dobu nebo paralelní až [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit tak, že nastavíte nový <*počet*> hodnota naleznete v tématu [souběžnosti aktivační události změnit](#change-trigger-concurrency). |
| <*max-runs-queue*> | Integer | Pokud vaše aplikace logiky je již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou vloženy do této fronty [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit výchozí omezení, [omezit spuštění čekajících změn](#change-waiting-runs). |
| <*operation-option*> | String | Výchozí chování můžete změnit nastavením `operationOptions` vlastnost. Další informace najdete v tématu [možnosti operace](#operation-options). |
||||

*Výstupy*

| Element | Type | Popis |
|---------|------|-------------|
| Záhlaví | JSON – objekt | Hlavičky z odpovědi |
| hlavní část | JSON – objekt | Text z odpovědi |
| Stavový kód | Integer | Stavový kód z odpovědi |
||||

*Požadavky na příchozí požadavky*

Aby dobře pracoval s svou aplikaci logiky, musí koncový bod odpovídají vzoru konkrétní aktivační události nebo smlouvy a rozpoznat tyto vlastnosti:

| Odpověď | Požaduje se | Popis |
|----------|----------|-------------|
| Kód stavu | Ano | "200 OK" stavový kód spustí. Každý jiný stavový kód nelze spustit běh. |
| Hlavička retry-after | Ne | Počet sekund do aplikace logiky se dotazuje na koncový bod znovu |
| Hlavička umístění | Ne | Adresa URL k volání na další interval dotazování. Pokud není zadán, použije se původní adresu URL. |
||||

*Příklad chování různých požadavků*

| Kód stavu | Zkuste to znovu za | Chování |
|-------------|-------------|----------|
| 200 | {none} | Spuštění pracovního postupu a pak znovu vyhledat více dat, po definované opakování. |
| 200 | 10 sekund | Spuštění pracovního postupu a pak znovu zkontrolujte další data po 10 sekundách. |
| 202 | 60 sekund | Nemáte aktivaci pracovního postupu. Další pokus se stane v jednu minutu, v souladu s definovanou opakování. Pokud definované opakování je kratší než minuta, hlavičkou retry-after přednost. V opačném případě se používá definované opakování. |
| 400 | {none} | Chybný požadavek, nelze spustit pracovní postup. Pokud ne `retryPolicy` je definován, použije výchozí zásady. Po dosažení počet opakovaných pokusů, trigger bude kontrolovat znovu pro data po definované opakování. |
| 500 | {none}| Chyba serveru, při spuštění pracovního postupu. Pokud ne `retryPolicy` je definován, použije výchozí zásady. Po dosažení počet opakovaných pokusů, trigger bude kontrolovat znovu pro data po definované opakování. |
||||

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook triggeru

Tento trigger umožňuje svou aplikaci logiky volatelná aplikacemi tak, že vytvoříte koncový bod, který může zaregistrovat předplatné voláním zadaný koncový bod adresy URL. Při vytváření této aktivační události v pracovním postupu odchozí požadavek provádí volání k registraci předplatného. Tímto způsobem se aktivační událost můžete spustit naslouchání událostem. Operace provádí tento trigger neplatný, odchozí požadavek automaticky provádí volání pro zrušení odběru. Další informace najdete v tématu [koncový bod předplatná](#subscribe-unsubscribe).

Můžete také určit [asynchronní omezení](#asynchronous-limits) na **HTTPWebhook** aktivační události.
Aktivační události chování závisí na oddíly, které můžete použít nebo vynechejte.

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

Některé hodnoty, jako například <*typ metody*>, jsou k dispozici pro obě `"subscribe"` a `"unsubscribe"` objekty.

*Požadováno*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Typ metody*> | String | Metoda HTTP pro žádost o odběr: "GET", "Vložit", "POST", "Opravy" nebo "Odstranit" |
| <*endpoint-subscribe-URL*> | String | Adresa URL koncového bodu se má odeslat žádost o odběr |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Typ metody*> | String | Metoda HTTP pro žádost o zrušení: "GET", "Vložit", "POST", "Opravy" nebo "Odstranit" |
| <*endpoint-unsubscribe-URL*> | String | Adresa URL koncového bodu se má odeslat žádost o zrušení |
| <*body-content*> | String | Všechny zprávy k odeslání v žádosti o předplatné nebo zrušení obsahu |
| <*ověřování – metoda*> | JSON – objekt | Metoda požadavku se používá k ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
| <*retry-behavior*> | JSON – objekt | Přizpůsobení chování opakování pro občasné chyby, které mají 408, 429 a 5XX stavový kód a všechny výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*max-runs*> | Integer | Ve výchozím instancí pracovních postupů logic app spustí ve stejnou dobu nebo paralelní až [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit tak, že nastavíte nový <*počet*> hodnota naleznete v tématu [souběžnosti aktivační události změnit](#change-trigger-concurrency). |
| <*max-runs-queue*> | Integer | Pokud vaše aplikace logiky je již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou vloženy do této fronty [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit výchozí omezení, [omezit spuštění čekajících změn](#change-waiting-runs). |
| <*operation-option*> | String | Výchozí chování můžete změnit nastavením `operationOptions` vlastnost. Další informace najdete v tématu [možnosti operace](#operation-options). |
||||

*Výstupy*

| Element | Type | Popis |
|---------|------|-------------|
| Záhlaví | JSON – objekt | Hlavičky z odpovědi |
| hlavní část | JSON – objekt | Text z odpovědi |
| Stavový kód | Integer | Stavový kód z odpovědi |
||||

*Příklad*

Tento trigger vytvoří předplatné na zadaný koncový bod, adresu URL zpětného volání jedinečný a čeká technologie přehled nově publikovaných článků.

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

Tato aktivační událost spouští podle plánu zadané opakování na základě a poskytuje snadný způsob pro vytvoření pravidelně spuštěné pracovního postupu. 

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*time-unit*> | String | Jednotka času, který popisuje, jak často se trigger spustí: "Druhý", "Minute", "Hour", "Day", "Týden", "Měsíc" |
| <*číslo z časových jednotek*> | Integer | Hodnota, která určuje, jak často se trigger spustí na základě četnosti, což je počet časových jednotek počkat, až se trigger spustí znovu <p>Toto jsou minimální a maximální intervaly: <p>-Měsíc: 1 – 16 měsíců </br>-Den: 1 – 500 dnů </br>-Hodinu: 1 – 12 000 hodin </br>-Minutu: 1-72,000 minut </br>-Sekundu: 1-9,999,999 sekund<p>Například pokud je interval 6 a je frekvence "Měsíc", opakování je nastaveno na každých 6 měsíců. |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | String | Počáteční datum a čas v tomto formátu: <p>RRRR-MM-ddTHH při zadání časového pásma <p>-nebo- <p>RRRR-MM-: ssZ, pokud nechcete zadat časové pásmo <p>Tak například, pokud chcete 18. září 2017 ve 14:00, zadejte "2017-09-18T14:00:00" a zadejte časové pásmo, jako je například "Standardního tichomořského času", nebo zadejte "2017-09-18T14:00:00Z" bez časového pásma. <p>**Poznámka:** Tento počáteční čas musí následovat [specifikace formátu ISO 8601 datum čas](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) v [formát času UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Pokud nechcete zadat časové pásmo, je nutné přidat písmeno "Z" na konci bez mezer. Tato "Z" odkazuje na ekvivalentní [námořních čas](https://en.wikipedia.org/wiki/Nautical_time). <p>U jednoduchých plánů, počáteční čas je první výskyt, zatímco u složitějších plánů aktivační událost neaktivuje všechny dříve než čas spuštění. Další informace o spuštění data a časy, naleznete v tématu [vytvořit a naplánovat pravidelné spouštění úloh](../connectors/connectors-native-recurrence.md). |
| <*time-zone*> | String | Platí, pouze pokud zadáte čas spuštění protože nepřijme tento trigger [časový posun](https://en.wikipedia.org/wiki/UTC_offset). Určete časové pásmo, které chcete použít. |
| <*one-or-more-hour-marks*> | Integer nebo celočíselné pole | Pokud zadáte "Day" nebo "Týden" pro `frequency`, můžete určit jeden nebo více celá čísla od 0 do 23, oddělených čárkami, jako hodin dne, kdy chcete spustit pracovní postup. <p>Například pokud zadáte "číslo 10", "12" a "14", získáte 10 AM, 12 hodin a 14: 00 jako značky hodinu. |
| <*one-or-more-minute-marks*> | Integer nebo celočíselné pole | Pokud zadáte "Day" nebo "Týden" pro `frequency`, můžete určit jeden nebo více celá čísla od 0 do 59, oddělených čárkami, jako minuty v hodině, kdy chcete spustit pracovní postup. <p>Například můžete zadat "30" jako značku minutu a použijeme předchozí příklad hodin dne, získáte 10:30:00, 12:30 odp. a 2:30 odp. |
| weekDays | Řetězec nebo pole řetězců | Pokud zadáte "Týden" pro `frequency`, můžete určit jeden nebo více dní, oddělených čárkami, pokud chcete spustit pracovní postup: "Pondělí", "Úterý", "Středa", "Čtvrtek", "Pátek", "Sobota" a "Neděle" |
| <*max-runs*> | Integer | Ve výchozím instancí pracovních postupů logic app spustí ve stejnou dobu nebo paralelní až [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit tak, že nastavíte nový <*počet*> hodnota naleznete v tématu [souběžnosti aktivační události změnit](#change-trigger-concurrency). |
| <*max-runs-queue*> | Integer | Pokud vaše aplikace logiky je již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou vloženy do této fronty [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit výchozí omezení, [omezit spuštění čekajících změn](#change-waiting-runs). |
| <*operation-option*> | String | Výchozí chování můžete změnit nastavením `operationOptions` vlastnost. Další informace najdete v tématu [možnosti operace](#operation-options). |
||||

*Příklad 1*

Tento trigger opakování základní spouštěna každý den:

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

Můžete zadat počáteční datum a čas pro aktivaci triggeru. Tento trigger opakování se spouští v zadané datum a potom aktivuje každý den:

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

Tento trigger opakování začíná 9. září 2017 ve 14:00 a aktivuje každý týden každé pondělí v 10:30:00, 12:30 odp. a 2:30 PM Tichomoří (běžný čas):

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

Další informace a příklady pro tuto aktivační událost, naleznete v tématu [vytvořit a naplánovat pravidelné spouštění úloh](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Aktivační událost požadavek

Tato aktivační událost díky aplikaci logiky volatelná aplikacemi tak, že vytvoříte koncový bod, který může přijímat příchozí žádosti. Tato aktivační událost zadejte schéma JSON, který popisuje a ověřuje datové části nebo vstupy, které přijímá aktivační událost z příchozího požadavku. Schéma také usnadňuje vlastnosti aktivační události na odkaz v pozdějších akcích v pracovním postupu.

Chcete-li volat tuto aktivační událost, musíte použít `listCallbackUrl` rozhraní API, která je popsána v [rozhraní REST API služby pracovního postupu](https://docs.microsoft.com/rest/api/logic/workflows). Další informace o použití této aktivační události jako koncový bod HTTP, najdete v článku [volání triggeru, nebo vnořené pracovní postupy s koncovými body HTTP](../logic-apps/logic-apps-http-endpoint.md).

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*property-name*> | String | Název vlastnosti ve schématu JSON, který popisuje datové části |
| <*property-type*> | String | Typ vlastnosti |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Typ metody*> | String | Metoda příchozí požadavky musí používat volat aplikaci logiky: "GET", "VLOŽIT", "POST", "OPRAVY", "ODSTRANIT" |
| <*relative-path-for-accepted-parameter*> | String | Relativní cesta pro parametr, který může přijmout váš koncový bod adresy URL |
| <*required-properties*> | Pole | Jednu nebo více vlastností, které vyžadují hodnoty |
| <*max-runs*> | Integer | Ve výchozím instancí pracovních postupů logic app spustí ve stejnou dobu nebo paralelní až [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit tak, že nastavíte nový <*počet*> hodnota naleznete v tématu [souběžnosti aktivační události změnit](#change-trigger-concurrency). |
| <*max-runs-queue*> | Integer | Pokud vaše aplikace logiky je již spuštěn maximální počet instancí, které můžete změnit na základě `runtimeConfiguration.concurrency.runs` vlastnosti, všechny nové spuštění jsou vloženy do této fronty [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit výchozí omezení, [omezit spuštění čekajících změn](#change-waiting-runs). |
| <*operation-option*> | String | Výchozí chování můžete změnit nastavením `operationOptions` vlastnost. Další informace najdete v tématu [možnosti operace](#operation-options). |
||||

*Příklad*

Tato aktivační událost určuje, že příchozí požadavek musí používat metodu POST protokolu HTTP pro volání triggeru a obsahují schéma, která ověřuje vstup z příchozího požadavku: 

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

## <a name="trigger-conditions"></a>Podmínky aktivace

Pro všechny aktivační události a pouze aktivačních událostí může obsahovat pole, která obsahuje jeden nebo více výrazů pro podmínky k určení, zda by měl spustit pracovní postup. Chcete-li přidat `conditions` vlastnost na trigger v aplikaci logiky otevření aplikace logiky v zobrazení editoru kódu.

Například můžete určit, že aktivační událost aktivuje, pouze když web vrátí interní chybu serveru pomocí odkazu na kód stavu triggeru v `conditions` vlastnost:

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

Ve výchozím nastavení, aktivační událost aktivuje pouze po získání "200 OK" odpověď. Pokud výraz odkazuje na trigger's stavový kód, se nahradí výchozí chování aktivační události. Ano Pokud chcete generovat pro více než jeden kód stavu, jako je například "200", "201" stavový kód aktivační událost, musí obsahovat tento výraz jako vaše podmínka:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))`

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Aktivovat více běhů

Pokud se aktivační událost vrátí pole pro vaši aplikaci logiky ke zpracování, někdy smyčka "for each" může trvat příliš dlouho zpracovat každou položku pole. Místo toho můžete použít **vlastnost SplitOn** vlastnosti v triggeru k *debatch* pole. Rozdělení dávek rozděluje položky pole a spustí novou instanci aplikace logiky, která se spouští pro každou položku pole. Tento přístup je užitečný, třeba když chcete dotázat na koncový bod, který mezi intervaly dotazování může vrátit několik nových položek. 

Při použití `SplitOn` vlastnost, získáte souběžných instancí až do limitu, který aktivační událost a služba může vrátit. Pro maximální počet pole položek, které **vlastnost SplitOn** dokáže zpracovat při spuštění aplikace logiky jednoho a zobrazit [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Nemůžete použít **vlastnost SplitOn** pomocí vzoru synchronní odpověď. Jakýkoli pracovní postup, který používá **vlastnost SplitOn** a obsahuje odpověď akce běží asynchronně a okamžitě odešle `202 ACCEPTED` odpovědi.

Pokud soubor Swagger váš trigger popisuje datovou část, která je pole, **vlastnost SplitOn** vlastnost je automaticky přidána na váš trigger. Jinak přidejte tuto vlastnost uvnitř datové části odpovědi, která má pole, které chcete debatch.

*Příklad*

Předpokládejme, že máte rozhraní API, která vrací tato odpověď:

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

Aplikace logiky potřebuje pouze obsah z pole v `Rows`, abyste mohli vytvořit aktivační událost jako v tomto příkladu:

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
> Pokud používáte `SplitOn` příkazu, nelze získat vlastnosti, které jsou mimo pole. Takže v tomto příkladu nelze získat `status` vrácena vlastnost v odpovědi z rozhraní API.
>
> Aby se zabránilo selhání, pokud `Rows` vlastnost neexistuje, tento příklad používá `?` operátor.

Teď můžete používat vaše definice pracovního postupu `@triggerBody().name` zobrazíte `name` hodnoty, které jsou `"customer-name-one"` od prvního spuštění a `"customer-name-two"` z druhé spuštění. Proto trigger výstupy vzhled, jako jsou tyto příklady:

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

Služba Azure Logic Apps poskytuje různé typy akcí - spolu různé vstupy, které definují chování akce jedinečné.

Akce mají tyto základní prvky, i když některé jsou volitelné:

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Název akce*> | String | Název akce |
| <*action-type*> | String | Typ akce, například "Http" nebo "ApiConnection"|
| <*Název vstupu*> | String | Název pro vstup, který definuje chování akci |
| <*Vstupní hodnota*> | Různé | Vstupní hodnota, která může být řetězec, celé číslo, objekt JSON a tak dále |
| <*previous-trigger-or-action-status*> | JSON – objekt | Název a výsledný stav triggeru nebo akce, které musí spustit okamžitě, před spuštěním této aktuální akce |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*retry-behavior*> | JSON – objekt | Přizpůsobení chování opakování pro občasné chyby, které mají 408, 429 a 5XX stavový kód a všechny výjimky připojení. Další informace najdete v tématu [zásady opakování](#retry-policies). |
| <*runtime-config-options*> | JSON – objekt | U některých akcí, můžete změnit na akci chování za běhu nastavením `runtimeConfiguration` vlastnosti. Další informace najdete v tématu [nastavení konfigurace modulu Runtime](#runtime-config-options). |
| <*operation-option*> | String | U některých akcí můžete změnit výchozí chování tak, že nastavíte `operationOptions` vlastnost. Další informace najdete v tématu [možnosti operace](#operation-options). |
||||

## <a name="action-types-list"></a>Seznam typů akce

Tady jsou některé běžně používané akce:

* [Vestavěná akce typy](#built-in-actions) jako jsou tyto příklady a další:

  * [**HTTP** ](#http-action) pro volání koncových bodů protokolu HTTP nebo HTTPS

  * [**Odpověď** ](#response-action) pro reagování na žádosti

  * [**Funkce** ](#function-action) pro volání funkcí Azure

  * Akce s daty operace, jako [ **připojení**](#join-action), [ **Compose**](#compose-action), [ **tabulky** ](#table-action), [ **Vyberte**](#select-action)a dalšími lidmi, které vytvoří nebo transformovat data z různých vstupy

  * [**Pracovní postup** ](#workflow-action) volání jiný pracovní postup aplikace logiky

* [Spravované typy rozhraní API akce](#managed-api-actions) například [ **ApiConnection** ](#apiconnection-action) a [ **ApiConnectionWebHook** ](#apiconnectionwebhook-action) , které volají různé rozhraní API a konektory spravované microsoftem, například Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub a další

* [Řídit typy akcí pracovního postupu](#control-workflow-actions) například [ **Pokud**](#if-action), [ **Foreach**](#foreach-action), [ **přepínače**  ](#switch-action), [ **Oboru**](#scope-action), a [ **dokud**](#until-action), které obsahují další akce a umožňují uspořádání provádění pracovního postupu

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Integrované akce

| Typ akce | Popis |
|-------------|-------------|
| [**Compose**](#compose-action) | Vytvoří jeden výstup ze vstupů, což může mít různé typy. |
| [**– funkce**](#function-action) | Volá funkci Azure. |
| [**HTTP**](#http-action) | Zavolá koncový bod HTTP. |
| [**Připojte se k**](#join-action) | Vytvoří řetězec ze všech položek v poli a tyto položky odděluje znak zadaného oddělovače. |
| [**Parsování formátu JSON**](#parse-json-action) | Vytvoří uživatelsky přívětivé tokeny z vlastností ve formátu JSON obsahu. Tyto vlastnosti pak odkazujete včetně tokenů ve vaší aplikaci logiky. |
| [**Dotaz**](#query-action) | Vytvoří pole z položek v jiném poli na základě podmínky nebo filtru. |
| [**Odpověď**](#response-action) | Vytvoří odpověď na příchozí volání nebo požadavek. |
| [**Vyberte**](#select-action) | Pomocí transformace položky z jiného objektu array podle zadaného mapování vytvoří pole objektů JSON. |
| [**Tabulka**](#table-action) | Vytvoří tabulku CSV nebo HTML z pole. |
| [**ukončit**](#terminate-action) | Zastaví aktivně spuštěné pracovního postupu. |
| [**Počkej**](#wait-action) | Pozastavení pracovního postupu po zadanou dobu nebo do zadaného data a času. |
| [**Workflow**](#workflow-action) | Vnoří pracovního postupu uvnitř jiného pracovního postupu. |
|||

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Spravované rozhraní API akce

| Typ akce | Popis |
|-------------|-------------| 
| [**ApiConnection**](#apiconnection-action) | Zavolá koncový bod HTTP pomocí [rozhraní API spravovaná Microsoftem](../connectors/apis-list.md). |
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Funguje jako HTTP Webhook, ale používá [rozhraní API spravovaná Microsoftem](../connectors/apis-list.md). |
|||

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Řízení pracovního postupu akce

Tyto akce pomáhá řídit provádění pracovního postupu a zahrnout další akce. Z mimo ovládací prvek akce pracovního postupu, můžete přímo odkazovat na akce v akci pracovního postupu tohoto ovládacího prvku. Pokud máte například `Http` akci v oboru, můžete odkazovat `@body('Http')` výrazu z libovolného místa v pracovním postupu. Však akce, které existují v akci pracovního postupu ovládacího prvku můžete pouze "spustit" Další akce, které jsou ve stejné struktuře řízení pracovního postupu.

| Typ akce | Popis |
|-------------|-------------|
| [**ForEach**](#foreach-action) | Spusťte stejné akce ve smyčce pro každou položku v poli. |
| [**If**](#if-action) | Spuštění akcí podle toho, jestli je zadaná podmínka true nebo false. |
| [**Obor**](#scope-action) | Spusťte akce na základě stavu skupiny ze skupiny akcí. |
| [**Switch**](#switch-action) | Spouštění akcí, které jsou uspořádány do případy, když hodnoty z výrazů, objektů nebo tokeny odpovídají hodnoty zadané ve všech případech. |
| [**Až do**](#until-action) | Spouštění akcí ve smyčce, dokud je zadaná podmínka pravdivá. |
|||

## <a name="actions---detailed-reference"></a>Akce – odkaz na podrobné

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>Akce APIConnection

Tato akce odešle požadavek HTTP [rozhraní API spravovaná Microsoftem](../connectors/apis-list.md) a vyžaduje informace o rozhraní API a parametry a odkaz na platné připojení.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Název akce*> | String | Název akce zadaný konektorem |
| <*api-name*> | String | Název rozhraní API spravovaná Microsoftem, který se používá pro připojení |
| <*Typ metody*> | String | Metoda HTTP pro volání rozhraní API: "GET", "Vložit", "POST", "Opravy" nebo "Odstranit" |
| <*api-operation*> | String | Operace rozhraní API pro volání |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*other-action-specific-input-properties*> | JSON – objekt | Všechny vstupní vlastnosti, které platí pro tento konkrétní akce |
| <*retry-behavior*> | JSON – objekt | Přizpůsobení chování opakování pro občasné chyby, které mají 408, 429 a 5XX stavový kód a všechny výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*query-parameters*> | JSON – objekt | Žádné parametry dotazu zahrnout pomocí rozhraní API volat. <p>Například `"queries": { "api-version": "2018-01-01" }` přidá objekt `?api-version=2018-01-01` volání. |
| <*other-action-specific-properties*> | JSON – objekt | Další vlastnosti, které se vztahují k této konkrétní akci |
||||

*Příklad*

Popisuje tuto definici **odeslat e-mailu** akce pro konektor Office 365 Outlook, což je rozhraní API spravovaná Microsoftem: 

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

Tato akce odešle žádost o předplatné prostřednictvím protokolu HTTP na koncový bod pomocí [rozhraní API spravovaná Microsoftem](../connectors/apis-list.md), poskytuje *adresu URL zpětného volání* k kam poslat na koncový bod odpověď a čeká na koncový bod pro odpověď. Další informace najdete v tématu [koncový bod předplatná](#subscribe-unsubscribe).

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

Některé hodnoty, jako například <*typ metody*>, jsou k dispozici pro obě `"subscribe"` a `"unsubscribe"` objekty.

*Požadováno*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Název akce*> | String | Název akce zadaný konektorem |
| <*Typ metody*> | String | Metoda HTTP, který se má použít pro přihlášení k odběru nebo odpovězeno koncový bod: "GET", "Vložit", "POST", "Opravy" nebo "Odstranit" |
| <*api-subscribe-URL*> | String | Identifikátor URI pro přihlášení k odběru do rozhraní API |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*api-unsubscribe-URL*> | String | Identifikátor URI pro registraci z rozhraní API. |
| <*header-content*> | JSON – objekt | Záhlaví má poslat v požadavku <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON – objekt | Žádný obsah zprávy k odeslání v požadavku |
| <*ověřování – metoda*> | JSON – objekt | Metoda požadavku se používá k ověřování. Další informace najdete v tématu [odchozí ověření Scheduleru](../scheduler/scheduler-outbound-authentication.md). |
| <*retry-behavior*> | JSON – objekt | Přizpůsobení chování opakování pro občasné chyby, které mají 408, 429 a 5XX stavový kód a všechny výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*query-parameters*> | JSON – objekt | Žádné parametry dotazu zahrnout pomocí volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` přidá objekt `?api-version=2018-01-01` volání. |
| <*other-action-specific-input-properties*> | JSON – objekt | Všechny vstupní vlastnosti, které platí pro tento konkrétní akce |
| <*other-action-specific-properties*> | JSON – objekt | Další vlastnosti, které se vztahují k této konkrétní akci |
||||

Můžete také zadat omezení na **ApiConnectionWebhook** akce stejným způsobem jako [asynchronní omezení HTTP](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Akce psaní

Tato akce vytvoří jeden výstup z více vstupů, včetně výrazy. Výstup a vstupy může mít libovolný typ, který nativně podporuje Azure Logic Apps, jako je například pole, objekty JSON, XML a binární soubor.
Pak můžete výstup akce v dalších akcí.

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Požadováno*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*inputs-to-compose*> | Všechny | Vstupy pro vytvoření jediného výstupu |
||||

*Příklad 1*

Tato akce definice sloučí `abcdefg ` koncové mezery a hodnota `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Zde je výstup, který vytváří tato akce:

`abcdefg 1234`

*Příklad 2*

Tato akce definice sloučí řetězcovou proměnnou, která obsahuje `abcdefg` a celočíselnou proměnnou, která obsahuje `1234`:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Zde je výstup, který vytváří tato akce:

`"abcdefg1234"`

<a name="function-action"></a>

### <a name="function-action"></a>Akce – funkce

Tato akce vyžaduje dříve vytvořeného [funkce Azure Functions](../azure-functions/functions-create-first-azure-function.md).

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Azure-function-ID*> | String | ID prostředku pro funkci Azure, kterou chcete volat. Tady je formát pro tuto hodnotu:<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" |
| <*Typ metody*> | String | Metoda HTTP pro volání funkce: "GET", "Vložit", "POST", "Opravy" nebo "Odstranit" <p>Pokud není zadán, výchozí hodnota je metoda "POST". |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*header-content*> | JSON – objekt | Záhlaví k odeslání volání <p>Chcete-li například nastavení jazyka a typu na vyžádání: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON – objekt | Žádný obsah zprávy k odeslání v požadavku |
| <*query-parameters*> | JSON – objekt | Žádné parametry dotazu zahrnout pomocí volání rozhraní API <p>Například `"queries": { "api-version": "2018-01-01" }` přidá objekt `?api-version=2018-01-01` volání. |
| <*other-action-specific-input-properties*> | JSON – objekt | Všechny vstupní vlastnosti, které platí pro tento konkrétní akce |
| <*other-action-specific-properties*> | JSON – objekt | Další vlastnosti, které se vztahují k této konkrétní akci |
||||

Při uložení aplikace logiky, modul Logic Apps provádí tyto kontroly odkazovaná funkce:

* Váš pracovní postup musí mít přístup k funkci.

* Váš pracovní postup můžete použít jenom standardní triggeru HTTP nebo trigger webhooku obecného formátu JSON.

  Modul Logic Apps získá a uloží do mezipaměti adresy URL triggeru, který se používá v době běhu. Nicméně, pokud všechny operace zruší platnost mezipaměti adresy URL, **funkce** akce se nezdaří za běhu. Chcete-li vyřešit tento problém, aplikace logiky znovu uložte tak, aby aplikace logiky získá a uloží do mezipaměti adresu URL triggeru.

* Funkce nemůže mít žádné trasy definované.

* Jsou povoleny pouze "funkce" a "anonymní" autorizace úrovně.

*Příklad*

Tato akce definice volá funkci dříve vytvořeného "GetProductID":

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

Tato akce odešle žádost na určeném koncovém bodě a zkontroluje odpověď k určení, zda by měl spustit pracovní postup.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Typ metody*> | String | Metody pro použití pro odesílání požadavku: "GET", "Vložit", "POST", "Opravy" nebo "Odstranit" |
| <*HTTP-or-HTTPS-endpoint-URL*> | String | Koncový bod HTTP nebo HTTPS k volání. Maximální velikost řetězce: 2 KB |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*header-content*> | JSON – objekt | Záhlaví k odeslání požadavku <p>Chcete-li například nastavit jazyk a typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON – objekt | Žádný obsah zprávy k odeslání v požadavku |
| <*retry-behavior*> | JSON – objekt | Přizpůsobení chování opakování pro občasné chyby, které mají 408, 429 a 5XX stavový kód a všechny výjimky připojení. Další informace najdete v tématu [zásady opakování](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*query-parameters*> | JSON – objekt | Žádné parametry dotazu zahrnout s požadavkem <p>Například `"queries": { "api-version": "2018-01-01" }` přidá objekt `?api-version=2018-01-01` volání. |
| <*other-action-specific-input-properties*> | JSON – objekt | Všechny vstupní vlastnosti, které platí pro tento konkrétní akce |
| <*other-action-specific-properties*> | JSON – objekt | Další vlastnosti, které se vztahují k této konkrétní akci |
||||

*Příklad*

Tato definice akce získá nejnovější informace o odesláním požadavku do zadaného koncového bodu:

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

### <a name="join-action"></a>Připojte se k akci

Tato akce vytvoří řetězec ze všech položek v poli a tyto položky odděluje znak zadaného oddělovače.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Pole*> | Pole | Pole nebo výraz, který obsahuje zdrojové položky. Pokud chcete zadat výraz, použijte tento výraz s dvojitými uvozovkami. |
| <*delimiter*> | Jeden znak řetězec | Znak oddělující každou položku v řetězci |
||||

*Příklad*

Předpokládejme, že máte dříve vytvořené "myIntegerArray" Proměnná obsahující toto pole celé číslo:

`[1,2,3,4]`

Tato definice akce získá hodnoty z proměnné pomocí `variables()` funkce ve výrazu a vytvoří tento řetězec s těmito hodnotami, které jsou odděleny čárkami: `"1,2,3,4"`

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

### <a name="parse-json-action"></a>Parsovat JSON akce

Tato akce vytvoří uživatelsky přívětivé pole nebo *tokeny* z vlastností v obsah JSON. Tyto vlastnosti můžete poté přistupovat ve vaší aplikaci logiky pomocí tokenů. Například pokud chcete použít výstup JSON ze služeb, jako je Azure Service Bus a Azure Cosmos DB, můžete zahrnout tuto akci v aplikaci logiky tak, aby můžete snadněji odkazují na data ve výstupu.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*JSON-source*> | JSON – objekt | Obsah JSON, který chcete analyzovat |
| <*JSON-schema*> | JSON – objekt | Schéma JSON, který popisuje základní obsah JSON, které akce používá k analýze zdrojový obsah JSON. <p>**Tip**: V návrháři pro Logic Apps můžete poskytovat schématu nebo poskytovat ukázkovou datovou část, aby akce můžete vygenerovat schéma. |
||||

*Příklad*

Tato akce definice vytvoří tyto tokeny, které můžete použít ve vaší aplikaci logiky, ale jenom v akcích spouštění následujících **Parsovat JSON** akce:

`FirstName`, `LastName`, a `Email`

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

V tomto příkladu určuje vlastnost "obsah" obsah akce parsovat JSON. Tento obsah JSON můžete zadat taky jako ukázkovou datovou část pro generování schématu.

```json
"content": {
   "Member": {
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Vlastnost "schéma" Určuje schématu JSON, použít pro popisující obsah JSON:

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

Tato akce vytvoří pole z položek v jiném poli na základě zadaných podmínek nebo filtru.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Pole*> | Pole | Pole nebo výraz, který obsahuje zdrojové položky. Pokud chcete zadat výraz, použijte tento výraz s dvojitými uvozovkami. |
| <*condition-or-filter*> | String | Podmínkou použitou pro filtrování položek v zdrojové pole <p>**Poznámka:** Pokud žádné hodnoty splňují zadanou podmínku, tato akce vytvoří prázdné pole. |
||||

*Příklad*

Tato akce definice vytvoří pole, která má větší než zadaná hodnota, která je dvě hodnoty:

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

Tato akce vytvoří datové části pro odpovědi na požadavek HTTP.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*response-status-code*> | Integer | Stavový kód HTTP, které je odesláno příchozího požadavku. Výchozí kód je "200 OK", ale kód může být libovolný platný stavový kód, který se spustí s 2xx, 4xx a 5xx, ale ne s 3xxx. |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*response-headers*> | JSON – objekt | Jednu nebo víc hlaviček, které mají zahrnout do odpovědi |
| <*response-body*> | Různé | Text odpovědi, který může být řetězec, objekt JSON nebo dokonce binární obsah z předchozí akci |
||||

*Příklad*

Tato akce definice vytvoří odpovědi na požadavek HTTP se zadaným stavovým kódem, text zprávy a záhlaví zpráv:

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

Na rozdíl od dalších akcí **odpovědi** akce má zvláštní omezení:

* Váš pracovní postup můžete použít **odpovědi** akce pouze v případě, že pracovní postup se spustí pomocí triggeru požadavku HTTP, to znamená pracovního postupu musí být aktivované požadavek HTTP.

* Váš pracovní postup můžete použít **odpovědi** akce kdekoli *s výjimkou* uvnitř **Foreach** smyčky, **dokud** smyčky, včetně sekvenčních smyčkách, a paralelní větve.

* Původní požadavek HTTP získá odpověď pracovního postupu, pouze pokud to vyžaduje všechny akce **odpovědi** dokončení akce v rámci [časový limit požadavku HTTP](../logic-apps/logic-apps-limits-and-config.md#request-limits).

  Ale pokud váš pracovní postup volá jiné aplikace logiky jako vnořený pracovní postup, nadřazený pracovní postup čeká na dokončení vnořeného pracovního postupu, bez ohledu na to, kolik času předává před dokončením vnořeného pracovního postupu.

* Pokud váš pracovní postup používá **odpovědi** akce a způsob synchronní odpověď, pracovní postup nelze použít také **vlastnost SplitOn** vlastnost v definici aktivační události vzhledem k tomu tento příkaz vytvoří různých spuštění. Zkontrolujte pro tento případ, při použití metody PUT a pokud je hodnota true, vrátí odpověď "Chybný požadavek".

  Jinak, pokud váš pracovní postup používá **vlastnost SplitOn** vlastnost a **odpovědi** akce, pracovní postup běží asynchronně a okamžitě se vrátí odpověď "202 PŘIJATO".

* Při provádění pracovního postupu dosáhne **odpovědi** akce, ale příchozí požadavek má již obdržel odpověď, **odpovědi** akce je označena jako "Failed" z důvodu konfliktu. A v důsledku toho je také označena spuštění aplikace logiky se stavem "NEÚSPĚCH".

<a name="select-action"></a>

### <a name="select-action"></a>Vyberte akci

Tato akce vytvoří pole objektů JSON pomocí transformace položky z jiného objektu array podle zadaného mapování. Výstup pole a pole zdroje vždy mít stejný počet položek. Sice nemůžete měnit počet objektů v poli výstup, můžete přidat nebo odebrat vlastnosti a jejich hodnoty mezi tyto objekty. `select` Vlastnost určuje alespoň jednu dvojici klíč hodnota definující map pro transformaci položky ve zdrojovém poli. Pár klíč hodnota představuje vlastnosti a její hodnotu ve všech objektech v poli výstup.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Pole*> | Pole | Pole nebo výraz, který obsahuje zdrojové položky. Zajistěte, aby že použijte výraz v uvozovkách. <p>**Poznámka:** Pokud zdrojové pole je prázdné, tato akce vytvoří prázdné pole. |
| <*key-name*> | String | Název vlastnosti, které jsou přiřazené k výsledku z <*výraz*> <p>Přidat nové vlastnosti ve všech objektech v poli výstup, zadejte <*název klíče*> pro tuto vlastnost a <*výraz*> pro hodnotu vlastnosti. <p>Chcete-li odebrat vlastnost ze všech objektů v poli, vynechejte <*název klíče*> pro tuto vlastnost. |
| <*Výraz*> | String | Výraz, který transformuje položku v poli zdroje a přiřadí výsledek do <*název klíče*> |
||||

**Vyberte** akce vytvoří pole jako výstup, takže všechny akce, které chcete využít tento výstup, musíte přijmout buď pole nebo pole je nutné převést na typ, který přijímá akce uživatelů. Například pole výstup převést na řetězec, můžete předat toto pole na **psaní** akce a odkázat na výstup **psaní** akce v dalších akcí.

*Příklad*

Tato akce definice vytvoří pole objektů JSON z pole celé číslo. Akce Iteruje přes pole zdroje, získá každý celočíselnou hodnotu pomocí `@item()` výraz a přiřadí každé hodnoty "`number`" vlastnosti jednotlivých objektů JSON:

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

Tady je pole, které tato akce vytvoří:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Pokud chcete použít toto pole výstup v dalších akcí, předejte tento výstup do **Compose** akce:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Pak můžete použít výstup z **Compose** akce v jiné akce, například **Office 365 Outlook – odeslat e-mail** akce:

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

Tato akce vytvoří tabulku CSV nebo HTML z pole. Pro pole s objekty JSON tato akce automaticky vytvoří záhlaví sloupců z názvy vlastností objektů. Pro pole s jinými datovými typy je nutné zadat záhlaví sloupců a hodnot. Tato pole patří třeba "ID" a "Název produktu" vlastnosti, které tato akce můžete použít pro názvy záhlaví sloupců:

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <CSV *or* HTML>| String | Formát pro tabulku, kterou chcete vytvořit |
| <*Pole*> | Pole | Pole nebo výraz, který obsahuje zdrojové položky tabulky <p>**Poznámka:** Pokud je zdroj pole prázdné, akce vytvoří prázdná tabulka. |
||||

*Volitelné*

Chcete-li zadat nebo upravit záhlaví sloupců a hodnot, použijte `columns` pole. Když `header-value` páry mají stejný název hlavičky, jejich hodnoty se zobrazí ve stejném sloupci pod tímto názvem záhlaví. V opačném případě záhlaví každého jedinečný definuje jedinečný sloupec.

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*název sloupce*> | String | Název záhlaví sloupce |
| <*Hodnota sloupce*> | Všechny | Hodnota ve sloupci |
||||

*Příklad 1*

Předpokládejme, že máte dříve vytvořené "myItemArray" Proměnná, která nyní obsahuje toto pole:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Tato akce definice vytvoří tabulku CSV z proměnné "myItemArray". Výraz používá `from` vlastnost získá pole z "myItemArray" pomocí `variables()` funkce:

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

Tady je tabulka sdíleného svazku clusteru, který vytváří tato akce:

```
ID,Product_Name
0,Apples
1,Oranges
```

*Příklad 2*

Tato akce definice vytvoří tabulku HTML z proměnné "myItemArray". Výraz používá `from` vlastnost získá pole z "myItemArray" pomocí `variables()` funkce:

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

Tady je tabulky HTML, který vytváří tato akce:

<table><thead><tr><th>ID</th><th>Název produktu</th></tr></thead><tbody><tr><td>0</td><td>Jablka</td></tr><tr><td>1</td><td>Oranges</td></tr></tbody></table>

*Příklad 3*

Tato akce definice vytvoří tabulku HTML z proměnné "myItemArray". Však tento příklad přepíše výchozí názvy záhlaví sloupců "Stock_ID" a "Popis" a přidá slovo "Ekologické" hodnoty ve sloupci "Popis".

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

Tady je tabulky HTML, který vytváří tato akce:

<table><thead><tr><th>Stock_ID</th><th>Popis</th></tr></thead><tbody><tr><td>0</td><td>Organické jablka</td></tr><tr><td>1</td><td>Organické Oranges</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Ukončit akci

Tato akce zastaví spuštění pro instanci pracovního postupu aplikace logiky, zruší všechny akce v průběhu, přeskočí všechny zbývající akce a vrátí zadaný stav. Například můžete použít **Terminate** akci, když vaše aplikace logiky ukončíte zcela v chybovém stavu. Tato akce nemá vliv na už dokončených akcí a nemůže být použit v rámci **Foreach** a **dokud** smyčky, včetně sekvenčních smyčkách.

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*status*> | String | Stav, který má být vrácen pro spuštění: "Se nezdařilo", "Zrušeno" nebo "Bylo dokončeno" |
||||

*Volitelné*

Se vztahují požadované vlastnosti pro objekt "runStatus" pouze v případě, že vlastnost "runStatus" je nastavena do stavu "NEÚSPĚCH".

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*error-code-or-name*> | String | Kód nebo název pro chybu |
| <*error-message*> | String | Zpráva nebo text, který popisuje chybu a libovolné akce aplikace uživateli umožňuje pořizovat |
||||

*Příklad*

Tato akce definice ukončí běh pracovního postupu, nastaví stav spuštění na "Chyba" a vrátí stav, kód chyby a chybová zpráva:

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

### <a name="wait-action"></a>Počkejte, akce

Tato akce pozastaví provádění pracovního postupu, nebo dokud zadanou dobu, ale ne obojí u zadaného intervalu.

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

*Určený čas*

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*počet jednotek*> | Integer | Pro **zpoždění** akce, počet jednotek čekání |
| <*interval*> | String | Pro **zpoždění** akce, interval čekání: "Druhý", "Minute", "Hour", "Day", "Týden", "Měsíc" |
| <*date-time-stamp*> | String | Pro **zpoždění až** akce, datum a čas pokračovat v provádění. Musíte použít tuto hodnotu [formát času UTC datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). |
||||

*Příklad 1*

Tato akce definice pozastaví pracovní postup po dobu 15 minut:

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

Tato akce definice pozastaví pracovní postup do zadané doby:

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

Tato akce volá jinou aplikaci logiky dříve vytvořené, což znamená, že můžete zahrnout a opakovaně používat další pracovní postupy aplikace logiky. Můžete také použít výstup z podřízených nebo *vnořené* aplikace logiky v akce, které následují vnořenou aplikaci logiky, za předpokladu, že aplikace logiky podřízené vrátí odpověď.

Modul Logic Apps ověří přístup k aktivační události, kterou chcete volat, proto se ujistěte, že přistupujete k této aktivační události. Také vnořenou aplikaci logiky, musí splňovat tato kritéria:

* Aktivační události je volatelná aplikacemi, například vnořenou aplikaci logiky [žádosti](#request-trigger) nebo [HTTP](#http-trigger) aktivační událost

* Stejné předplatné Azure jako svůj nadřazený prostředek

* Pokud chcete použít výstup z vnořenou aplikaci logiky ve vaší aplikaci logiky nadřazené, musíte mít vnořenou aplikaci logiky [odpovědi](#response-action) akce

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*nested-logic-app-name*> | String | Název aplikace logiky, kterou chcete volat |
| <*Název aktivační události*> | String | Název pro Spouštěč vnořenou aplikaci logiky, kterou chcete volat |
| <*Azure-subscription-ID*> | String | ID předplatného Azure vnořenou aplikaci logiky |
| <*Azure-resource-group*> | String | Název skupiny prostředků Azure vnořenou aplikaci logiky |
| <*nested-logic-app-name*> | String | Název aplikace logiky, kterou chcete volat |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*header-content*> | JSON – objekt | Záhlaví k odeslání volání |
| <*body-content*> | JSON – objekt | Žádný obsah zprávy k odeslání volání |
||||

*Výstupy*

Výstupy této akce se liší v závislosti na vaší aplikace logiky vnořená akce odpovědi. Pokud vnořenou aplikaci logiky neobsahuje akci odpovědi, výstupy jsou prázdné.

*Příklad*

Po úspěšném dokončení akce "Start_search" tuto definici pracovního postupu akce volá jinou aplikací logiky s názvem "Get_product_information", který se předává v zadané vstupy:

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

## <a name="control-workflow-action-details"></a>Podrobnosti akce pracovního postupu ovládacího prvku

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Akce foreach

Tato akce opakování Iteruje přes pole a provede akce pro každou položku pole. Ve výchozím nastavení smyčka "for each" běží paralelně až maximální počet cyklů. Toto maximum, naleznete v tématu [omezení a konfigurace](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Přečtěte si [vytvoření "for each" smyčky](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Akce-1 n*> | String | Názvy akcí, které běží na každou položku pole |
| <*Akce definition-1... n*> | JSON – objekt | Definice akce, na kterých běží |
| <*for-each-expression*> | String | Výraz, který odkazuje na každou položku v zadaném poli |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Počet*> | Integer | Ve výchozím nastavení, iterace až spuštění ve stejnou dobu nebo paralelní smyčka "for each" [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li tento limit změnit tak, že nastavíte nový <*počet*> hodnota naleznete v tématu [změnit smyčka "for each" souběžnosti](#change-for-each-concurrency). |
| <*operation-option*> | String | Ke spuštění smyčka "for each" postupně, nikoli paralelně, nastavte <*možnost operace*> do `Sequential` nebo <*počet*> do `1`, ale ne obojí. Další informace najdete v tématu [spuštění "for each" smyčky postupně](#sequential-for-each). |
||||

*Příklad*

Tato smyčka "for each" odešle e-mailu pro každou položku v poli, který obsahuje přílohy z příchozích e-mailů. Smyčky odešle e-mailu, včetně přílohu osoba, která zkontroluje přílohu.

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

Pokud chcete zadat pouze pole, která je předána jako výstup z triggeru, získá tento výraz <*název pole*> pole z textu triggeru. Aby se zabránilo selhání, pokud neexistuje pole, výraz používá `?` operátor:

`@triggerBody()?['<array-name>']`

<a name="if-action"></a>

### <a name="if-action"></a>Pokud se akce

Tuto akci, která je *podmíněný příkaz*, vyhodnotí výraz, který představuje podmínku a spustí jinou větev, podle toho, jestli je podmínka true nebo false. Pokud je podmínka pravdivá, podmínky je označen stavem "ÚSPĚCH". Přečtěte si [vytvoření podmíněné příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md).

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Podmínka*> | JSON – objekt | Podmínka, která může být výraz k vyhodnocení |
| <*1 akce*> | JSON – objekt | Akce, která se spustí, když <*podmínku*> vyhodnotí jako true |
| <*definice akce*> | JSON – objekt | Definice akce |
| <*Akce – 2*> | JSON – objekt | Akce, která se spustí, když <*podmínku*> vyhodnotí jako false |
||||

Akce v `actions` nebo `else` objekty získat tyto stavy:

* "Úspěšné" při spuštění a úspěšné
* "Se nezdařilo" při spuštění a selhání
* "Vynecháno" při příslušné pobočky se nespustí.

*Příklad*

Tato podmínka určuje to, že pokud celočíselná proměnná obsahuje hodnotu větší než nula, pracovní postup zkontroluje Web. Pokud je proměnná nula nebo méně, ověří pracovní postup jiný web.

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

#### <a name="how-conditions-use-expressions"></a>Použití výrazů podmínek

Tady jsou některé příklady, které ukazují, jak můžete použití výrazů v podmínkách:

| JSON | Výsledek |
|------|--------|
| "výraz": "@parameters('<*hasSpecialAction*>") " | Pro logické výrazy pouze bude podmínka splněna pro libovolnou hodnotu, která se vyhodnotí na hodnotu true. <p>Jiné typy převést na logickou hodnotu, použijte tyto funkce: `empty()` nebo `equals()`. |
| "výraz": "@greater(actions('<*action*>').output.value, parametry ('<*prahová hodnota*>")) " | Pro porovnání funkcí akci spustí jenom v případě výstup z <*akce*> je více než <*prahová hodnota*> hodnota. |
| "výraz": "@or(větší (actions('<*action*>').output.value, parametry ('<*prahová hodnota*>")), méně (akce ('<*stejnou akci*>').Output.Value, 100)) " | Pro funkce logiku a vytváření vnořeného logických výrazů, akce běží, když výstup z <*akce*> je více než <*prahová hodnota*> hodnota nebo v části 100. |
| "výraz": "@equals(délka (actions('<*action*>').outputs.errors), 0))" | Funkce pole můžete použít ke kontrole, jestli pole nemá žádné položky. Tato akce spustí, jakmile `errors` je pole prázdné. |
|||

<a name="scope-action"></a>

### <a name="scope-action"></a>Akce rozsahu

Tato akce logicky skupin akcí do *obory*, který získá svůj vlastní stav za akce v tomto oboru skončí. Stav oboru pak můžete zjistit, zda je spuštěn další akce. Přečtěte si [vytváření oborů](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*vnitřní akce-1... n*> | JSON – objekt | Jednu nebo více akcí, které běží uvnitř oboru |
| <*vstupy akce*> | JSON – objekt | Vstupy pro každou akci |
||||

<a name="switch-action"></a>

### <a name="switch-action"></a>Přepnout akci

Tuto akci, označované také jako *switch – příkaz*, slouží k uspořádání jiných akcí do *případů*a přiřadí hodnotu pro každý případ, s výjimkou na výchozí případ. Pokud existuje. Při spuštění pracovního postupu, **přepínač** akce porovná hodnotu z výrazu, objekt nebo token proti hodnoty zadané pro každý případ. Pokud **přepínač** akce vyhledá odpovídající případu, pracovní postup spouští pouze akce pro tento případ. Pokaždé, když **přepínač** spustí akce, buď pouze jeden případ odpovídající existuje nebo neexistuje žádné odpovídající položky. Pokud neexistují žádné odpovídající položky **přepínač** akce spustí výchozí akce. Přečtěte si [vytvoření příkazů přepínače](../logic-apps/logic-apps-control-flow-switch-statement.md).

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*expression-object-or-token*> | Různé | Výraz, objekt JSON nebo token k vyhodnocení |
| <*Název akce*> | String | Název akce ke spuštění pro odpovídající proces |
| <*definice akce*> | JSON – objekt | Definice pro akci spuštění pro odpovídající proces |
| <*matching-value*> | Různé | Hodnota určená k porovnání s vyhodnocený výsledek |
||||

*Volitelné*

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Výchozí název akce*> | String | Název výchozí akce pro spuštění, pokud neexistuje žádný odpovídající případ |
| <*default-action-definition*> | JSON – objekt | Definice pro akci pro spuštění, pokud neexistuje žádný odpovídající případ |
||||

*Příklad*

Tato akce definice vyhodnocuje, zda osoba reagování na žádosti o schválení e-mailu vybraná možnost "Schválení" nebo "Odmítnout pr". Na základě této volby **přepínač** akce spustí akce pro odpovídající případu, kdy je odeslat jinou e-mailové příjemce, ale s odlišným zněním v každém případě.

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

Tato akce smyčka obsahuje akce, které spustit, dokud je zadaná podmínka pravdivá. Smyčky zkontrolujte tuto podmínku jako poslední krok po spuštění všech dalších akcí. Může obsahovat více než jednu akci v `"actions"` objekt a akci musíte definovat alespoň jednoho limitu. Přečtěte si [vytvoření "smyčky do"](../logic-apps/logic-apps-control-flow-loops.md#until-loop).

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

| Hodnota | Type | Popis |
|-------|------|-------------|
| <*Název akce*> | String | Název akce, kterou chcete spustit uvnitř smyčka |
| <*action-type*> | String | Typ akce, který chcete spustit |
| <*vstupy akce*> | Různé | Vstupy pro akci pro spuštění |
| <*Podmínka*> | String | Podmínka nebo výraz k vyhodnocení koneckonců skončí akcí ve smyčce |
| <*loop-count*> | Integer | Limitu největší počet cyklů, které můžete spouštět akce. Výchozí hodnota `count` hodnota je 60. |
| <*loop-timeout*> | String | Omezení na nejdelší dobu, která můžete spouštět smyčky. Výchozí hodnota `timeout` hodnotu `PT1H`, což je požadované [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
||||

*Příklad*

Tuto akci definici smyčky odešle požadavek HTTP na zadanou adresu URL, dokud nebude splněna jedna z těchto podmínek:

* Získá odpověď obsahující požadavek "200 OK" stavový kód.
* Byl cyklus proveden 60 x.
* Byl cyklus proveden za jednu hodinu.

```json
"Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200])",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooky a předplatná

Založené na Webhoocích aktivační události a akce koncových bodů pravidelně nezaregistrují, ale místo toho počkejte konkrétní události nebo data v těchto koncových bodů. Tyto triggery a akce *odběru* na koncové body tím, že poskytuje *adresu URL zpětného volání* kam poslat na koncový bod odpovědi.

`subscribe` Volání se stane, když pracovní postup změny žádným způsobem, například když jsou obnovit přihlašovací údaje, nebo při změně vstupních parametrů u triggeru nebo akce. Toto volání používá stejné parametry jako standardní akce HTTP.

`unsubscribe` Volání automaticky se stane, když operace provede triggeru nebo akce neplatné, například:

* Odstranění nebo vypnutí aktivační událost.
* Odstranění nebo vypnutí pracovního postupu.
* Odstranění nebo zakázání předplatného.

Pro podporu těchto volání `@listCallbackUrl()` výraz vrátí jedinečný "adresa URL zpětného volání" u triggeru nebo akce. Tuto adresu URL představuje jedinečný identifikátor pro koncové body, které používají rozhraní API služby REST. Parametry pro tuto funkci jsou stejné jako aktivační události webhooku nebo akce.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Změna asynchronní doby trvání

Triggery a akce, můžete omezit přidáním dobu trvání asynchronního zpracování konkrétní časový interval `limit.timeout` vlastnost. Tímto způsobem, pokud akce nebyl dokončen, když interval zavřeli, že stav akce označena jako `Cancelled` s `ActionTimedOut` kódu. `timeout` Používá vlastnost [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

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

Můžete změnit výchozí chování modulu runtime pro aktivační události a akce s těmito `runtimeConfiguration` vlastnosti v definici aktivační události nebo akce.

| Vlastnost | Typ | Popis | Aktivační události nebo akce |
|----------|------|-------------|-------------------|
| `runtimeConfiguration.concurrency.runs` | Integer | Změnit [ *výchozí limit* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) na počet instancí aplikace logiky, spuštěné ve stejnou dobu nebo paralelně. Tato hodnota může pomoct omezit počet požadavků, které přijímají back-endových systémů. <p>Nastavení `runs` vlastnost `1` funguje stejným způsobem jako nastavení `operationOptions` vlastnost `SingleInstance`. Můžete nastavit buď vlastnost, ale ne obojí. <p>Chcete-li změnit výchozí omezení, [souběžnosti aktivační události změnit](#change-trigger-concurrency) nebo [aktivovat instance postupně](#sequential-trigger). | Všechny aktivační události |
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Změnit [ *výchozí limit* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) na počet instancí aplikace logiky, které můžete počkat a spouštět, když vaše aplikace logiky je již spuštěn maximální počet souběžných instancí. Limit souběžnosti v můžete změnit `concurrency.runs` vlastnost. <p>Chcete-li změnit výchozí omezení, [omezit spuštění čekajících změn](#change-waiting-runs). | Všechny aktivační události |
| `runtimeConfiguration.concurrency.repetitions` | Integer | Změnit [ *výchozí limit* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) "pro každý" počet iterací, které lze spustit ve stejnou dobu nebo paralelní smyčky. <p>Nastavení `repetitions` vlastnost `1` funguje stejným způsobem jako nastavení `operationOptions` vlastnost `SingleInstance`. Můžete nastavit buď vlastnost, ale ne obojí. <p>Chcete-li změnit výchozí omezení, [změnit "for each" souběžnosti](#change-for-each-concurrency) nebo [spuštění "for each" smyčky postupně](#sequential-for-each). | Akce: <p>[Foreach](#foreach-action) |
|||||

<a name="operation-options"></a>

## <a name="operation-options"></a>Možnosti operace

Můžete změnit výchozí chování pro aktivační události a akce s `operationOptions` vlastnost v definici aktivační události nebo akce.

| Možnost operace | Type | Popis | Aktivační události nebo akce |
|------------------|------|-------------|-------------------|
| `DisableAsyncPattern` | String | Spusťte akce založené na protokolu HTTP, synchronně místo asynchronně. <p><p>Pokud chcete nastavit tuto možnost, naleznete v tématu [spouštění akcí synchronně](#asynchronous-patterns). | Akce: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[Odpověď](#response-action) |
| `OptimizedForHighThroughput` | String | Změnit [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) počtu provedení akcí za 5 minut, než [maximální limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). <p><p>Pokud chcete nastavit tuto možnost, naleznete v tématu [spouštění v režimu vysoké propustnosti](#run-high-throughput-mode). | Všechny akce |
| `Sequential` | String | Spuštění "for each" jeden iterací smyčky na najednou, spíše než všechny ve stejnou dobu paralelně. <p>Tato možnost funguje stejným způsobem jako nastavení `runtimeConfiguration.concurrency.repetitions` vlastnost `1`. Můžete nastavit buď vlastnost, ale ne obojí. <p><p>Nastavení této možnosti najdete v článku [spuštění "for each" smyčky postupně](#sequential-for-each).| Akce: <p>[Foreach](#foreach-action) |
| `SingleInstance` | String | Aktivační událost pro každou instanci aplikace logiky spouští sekvenčně a počkejte na dokončení než aktivujete další instanci aplikace logiky dříve aktivní spuštění. <p><p>Tato možnost funguje stejným způsobem jako nastavení `runtimeConfiguration.concurrency.runs` vlastnost `1`. Můžete nastavit buď vlastnost, ale ne obojí. <p>Pokud chcete nastavit tuto možnost, naleznete v tématu [aktivovat instance postupně](#sequential-trigger). | Všechny aktivační události |
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Změnit aktivační událost souběžnosti

Ve výchozím nastavení, logiku aplikace instance spuštěné ve stejnou dobu, současně nebo paralelně až [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Každá instance aktivační událost tak, aktivuje se před dokončením předchozí instanci aplikace logiky spuštěná. Toto omezení pomáhá řídit počet požadavků, které přijímají back-endových systémů.

Chcete-li změnit výchozí omezení, můžete použít editor kódu zobrazit nebo návrhář pro Logic Apps vzhledem k tomu, že změna nastavení souběžnosti prostřednictvím návrháře přidá nebo aktualizuje `runtimeConfiguration.concurrency.runs` vlastnost definice základní aktivační události a naopak. Tato vlastnost určuje maximální počet instancí aplikace logiky, které můžou běžet paralelně.

> [!NOTE] 
> Pokud jste nastavili spouštějí postupně buď pomocí návrháře nebo zobrazení editoru kódu, nemají nastavený triggeru `operationOptions` vlastnost `SingleInstance` v zobrazení editoru kódu. Jinak dojde k chybě ověřování. Další informace najdete v tématu [aktivovat instance postupně](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu

V základní definici spustí, přidat nebo aktualizovat `runtimeConfiguration.concurrency.runs` vlastnost na hodnotu v rozmezí `1` a `50` (včetně).

Tady je příklad, který omezuje souběžných spuštění na 10 instancí:

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

#### <a name="edit-in-logic-apps-designer"></a>Upravit v návrháři pro Logic Apps

1. V pravém horním rohu aktivační události, klikněte na tlačítko se třemi tečkami (...) a klikněte na tlačítko **nastavení**.

2. V části **řízení souběžnosti**, nastavte **Limit** k **na**.

3. Přetáhněte **stupeň paralelismu** posuvník na hodnotu, kterou chcete. Chcete-li aplikace logiky se spouští sekvenčně, přetáhněte hodnota posuvníku, která má **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Změňte "for each" souběžnosti

Ve výchozím nastavení, smyčka "for each" iterací spuštění ve stejnou dobu nebo paralelně, až [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Chcete-li změnit výchozí omezení, můžete použít editor kódu zobrazit nebo návrhář pro Logic Apps vzhledem k tomu, že změna nastavení souběžnosti prostřednictvím návrháře přidá nebo aktualizuje `runtimeConfiguration.concurrency.repetitions` vlastnosti v základním akce "for each" definice a naopak. Tato vlastnost určuje maximální počet iterací, které můžou běžet paralelně.

> [!NOTE] 
> Pokud jste nastavili akci "for each" pro spuštění postupně buď pomocí návrháře nebo zobrazení editoru kódu, nemají nastavený na akci `operationOptions` vlastnost `Sequential` v zobrazení editoru kódu. Jinak dojde k chybě ověřování. Další informace najdete v tématu [spuštění "for each" smyčky postupně](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu

V základním "for each" definice, přidat nebo aktualizovat `runtimeConfiguration.concurrency.repetitions` vlastnost na hodnotu v rozmezí `1` a `50` (včetně).

Tady je příklad, který omezuje souběžných spuštění k iteracím v 10:

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

#### <a name="edit-in-logic-apps-designer"></a>Upravit v návrháři pro Logic Apps

1. V **pro každou** akce v pravém horním rohu klikněte na tlačítko se třemi tečkami (...) a klikněte na tlačítko **nastavení**.

2. V části **řízení souběžnosti**, nastavte **řízení souběžnosti** k **na**.

3. Přetáhněte **stupeň paralelismu** posuvník na hodnotu, kterou chcete. Chcete-li aplikace logiky se spouští sekvenčně, přetáhněte hodnota posuvníku, která má **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Změna čekání na spuštění limit

Ve výchozím nastavení, logiku aplikace instance spuštěné ve stejnou dobu, současně nebo paralelně až [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Každá instance aktivační událost se aktivuje před doběhnutí instanci aplikace logiky dříve aktivní. Můžete sice [změnit toto výchozí omezení](#change-trigger-concurrency), když počet instancí aplikace logiky dosáhne nový limit souběžnosti každé nové instance musíte počkat na spuštění.

Má také počet běhů, které mohou čekat [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits), které můžete změnit. Ale po vaše aplikace logiky dosáhne limitu čekajících běhů, modul Logic Apps již přijímá nová spuštění. Aktivační události požadavku a webhook vrátit 429 chyby a opakované triggery spuštění, přeskakuje se pokusy o dotazování.

Chcete-li změnit výchozí omezení na spuštění čeká v základní definici spustí, přidejte `runtimeConfiguration.concurency.maximumWaitingRuns` vlastnost s hodnotou mezi `0` a `100`. 

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

### <a name="trigger-instances-sequentially"></a>Postupně aktivace instance

Ke spuštění každého logiky spuštěn, instance aplikace až po dokončení předchozí instanci nastavením aktivační události spouští sekvenčně. Můžete použít zobrazení editoru kódu nebo návrhář pro Logic Apps, protože změna nastavení souběžnosti prostřednictvím návrháře také přidá nebo aktualizuje `runtimeConfiguration.concurrency.runs` vlastnost definice základní aktivační události a naopak.

> [!NOTE] 
> Když nastavíte aktivační událost pro spuštění postupně buď pomocí návrháře nebo zobrazení editoru kódu, nemají nastavený triggeru `operationOptions` vlastnost `Sequential` v zobrazení editoru kódu. Jinak dojde k chybě ověřování.

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu

V definici aktivační události nastavte některý z těchto vlastností, ale ne obojí.

Nastavte `runtimeConfiguration.concurrency.runs` vlastnost `1`:

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

*- nebo -*

Nastavte `operationOptions` vlastnost `SingleInstance`:

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

#### <a name="edit-in-logic-apps-designer"></a>Upravit v návrháři pro Logic Apps

1. V pravém horním rohu aktivační události, klikněte na tlačítko se třemi tečkami (...) a klikněte na tlačítko **nastavení**.

2. V části **řízení souběžnosti**, nastavte **Limit** k **na**.

3. Přetáhněte **stupeň paralelismu** posuvník číslo `1`.

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Spuštění "for each" postupně smyčky

Abyste mohli spustit smyčka "for each" spuštění, iterace až po dokončení předchozí iteraci nastavena akci "for each", která se spouští sekvenčně. Můžete použít zobrazení editoru kódu nebo návrhář pro Logic Apps, protože změna souběžnosti akce prostřednictvím návrháře také přidá nebo aktualizuje `runtimeConfiguration.concurrency.repetitions` vlastnost v definici základní akce a naopak.

> [!NOTE] 
> Když nastavíte akce "for each" ke spuštění postupně buď pomocí Návrhář nebo editor zobrazení kódu, nemají nastavený na akci `operationOptions` vlastnost `Sequential` v zobrazení editoru kódu. Jinak dojde k chybě ověřování. 

#### <a name="edit-in-code-view"></a>Upravit v zobrazení kódu

V definici akce nastavte jednu z těchto vlastností, ale ne obojí.

Nastavte `runtimeConfiguration.concurrency.repetitions` vlastnost `1`:

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

*- nebo -*

Nastavte `operationOptions` vlastnost `Sequential`:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Upravit v návrháři pro Logic Apps

1. V **pro každou** akce v pravém horním rohu klikněte na tlačítko se třemi tečkami (...) a klikněte na tlačítko **nastavení**.

2. V části **řízení souběžnosti**, nastavte **řízení souběžnosti** k **na**.

3. Přetáhněte **stupeň paralelismu** posuvník číslo `1`.

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>Spustit akce synchronně

Ve výchozím nastavení všechny akce založené na protokolu HTTP mají tvar standardní asynchronní operace. Tento model Určuje, že při akci založenou na protokolu HTTP odešle požadavek na zadaný koncový bod, vzdálený server odešle zpět odpověď "202 PŘIJATO". Tato odpověď znamená, že server přijal požadavek na zpracování. Modul Logic Apps udržuje kontrolu adrese URL zadané hodnotou hlavičky location odpovědi až do zpracování zastaveno, což je mimo 202 odpovědi.

Ale mají vypršení časového limitu žádosti o omezit, tak pro dlouhodobé akce, můžete zakázat asynchronní chování přidáním a nastavení `operationOptions` vlastnost `DisableAsyncPattern` ve vstupech akce.

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>Spustit v režimu Vysoká propustnost

Pro běh aplikace logiky jeden, počet akcí, které jsou spouštěny každých 5 minut, které má [výchozí limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Chcete-li tento limit zvýšit [maximální](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) je to možné, můžete nastavit `operationOptions` vlastnost `OptimizedForHighThroughput`. Toto nastavení umístí do režimu "Vysoká propustnost" aplikace logiky.

> [!NOTE]
> Režim vysoké propustnosti je ve verzi preview. Můžete také distribuovat zatížení napříč více než jedné aplikace logiky podle potřeby.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-triggers-or-actions"></a>Ověření aktivační události nebo akce

Koncové body HTTP podporují různé typy ověřování. Můžete nastavit ověřování pro tyto HTTP triggery a akce:

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)

Tady jsou typy ověřování, které můžete nastavit:

* [Základní ověřování](#basic-authentication)
* [Ověření klientského certifikátu](#client-certificate-authentication)
* [Ověřování Azure Active Directory (Azure AD) OAuth](#azure-active-directory-oauth-authentication)

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Základní ověřování

Pro tento typ ověřování, může obsahovat definice aktivační události nebo akce `authentication` objekt JSON, který má tyto vlastnosti:

| Vlastnost | Požaduje se | Value | Popis |
|----------|----------|-------|-------------|
| **type** | Ano | "Základní" | Typ ověřování použít, což je "Basic" zde |
| **uživatelské jméno** | Ano | "@parameters(userNameParam)" | Parametr, který předává uživatelské jméno k ověření pro přístup k cílový koncový bod služby |
| **Heslo** | Ano | "@parameters(passwordParam)" | Parametr, který se předá heslo k ověření pro přístup k cílový koncový bod služby |
|||||

Například tady je formát `authentication` objektu v definici aktivační události nebo akce. Další informace o zabezpečení parametry najdete v tématu [zabezpečení citlivých informací](#secure-info).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>Ověření klientského certifikátu

Pro tento typ ověřování, může obsahovat definice aktivační události nebo akce `authentication` objekt JSON, který má tyto vlastnosti:

| Vlastnost | Požaduje se | Value | Popis |
|----------|----------|-------|-------------|
| **type** | Ano | "ClientCertificate" | Typ ověřování pro klientské certifikáty vrstvy SSL (Secure Sockets) |
| **pfx** | Ano | <*base64-encoded-pfx-file*> | Obsah s kódováním base64 z soubor Personal Information Exchange (PFX) |
| **Heslo** | Ano | "@parameters(passwordParam)" | Parametr s heslo pro přístup k souboru PFX |
|||||

Například tady je formát `authentication` objektu v definici aktivační události nebo akce. Další informace o zabezpečení parametry najdete v tématu [zabezpečení citlivých informací](#secure-info).

```json
"authentication": {
   "password": "@parameters('passwordParam')",
   "pfx": "aGVsbG8g...d29ybGQ=",
   "type": "ClientCertificate"
}
```

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Ověřování Azure Active Directory (AD) OAuth

Pro tento typ ověřování, může obsahovat definice aktivační události nebo akce `authentication` objekt JSON, který má tyto vlastnosti:

| Vlastnost | Požaduje se | Value | Popis |
|----------|----------|-------|-------------|
| **type** | Ano | `ActiveDirectoryOAuth` | Typ ověřování chcete používat, což je "ActiveDirectoryOAuth" pro Azure AD OAuth |
| **Autorita** | Ne | <*URL-for-authority-token-issuer*> | Adresa URL autority, která poskytuje ověřovací token | 
| **tenanta** | Ano | <*tenant-ID*> | ID tenanta pro tenanta Azure AD |
| **audience** | Ano | <*resource-to-authorize*> | Prostředek, který má oprávnění k použití, například `https://management.core.windows.net/` |
| **clientId** | Ano | <*client-ID*> | ID klienta aplikace požaduje ověření |
| **credentialType** | Ano | "Tajné" nebo "Certifikátu" | Typ přihlašovacích údajů klienta se používá pro žádosti o autorizaci. Tuto vlastnost a hodnota se nezobrazují v základní definici, ale určuje požadované parametry pro typ přihlašovacích údajů. |
| **Heslo** | Ano, jenom pro typ přihlašovacích údajů "Certifikátu" | "@parameters(passwordParam)" | Parametr s heslo pro přístup k souboru PFX |
| **pfx** | Ano, jenom pro typ přihlašovacích údajů "Certifikátu" | <*base64-encoded-pfx-file*> | Obsah s kódováním base64 z soubor Personal Information Exchange (PFX) |
| **secret** | Ano, jenom pro "Tajné" typ přihlašovacích údajů | <*secret-for-authentication*> | Tajný klíč s kódováním base64, který klient používá pro žádost o autorizaci |
|||||

Například tady je formát `authentication` objektu při vaší definice aktivační události nebo akce na základě typu "Tajné" přihlašovacích údajů: Další informace o zabezpečení parametry najdete v tématu [zabezpečení citlivých informací](#secure-info).

```json
"authentication": {
   "audience": "https://management.core.windows.net/",
   "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
   "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo=",
   "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
   "type": "ActiveDirectoryOAuth"
}
```

<a name="secure-info"></a>

## <a name="secure-sensitive-information"></a>Zabezpečení citlivých informací

Kvůli ochraně citlivých informací, který používáte pro ověřování, jako jsou uživatelská jména a hesla, vaše definice aktivační události a akce můžete použít parametry a `@parameters()` výraz tak, aby tyto informace není viditelný, až uložíte svoji logiku aplikace.

Předpokládejme například, že používáte "Základní" ověřování v definici aktivační události nebo akce. Tady je příklad `authentication` objekt, který určuje uživatelské jméno a heslo:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

V `parameters` části pro definici aplikace logiky, definování parametrů použitých v definici aktivační události nebo akce:

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "HTTP": {
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
},
```

Pokud už vytváříte nebo pomocí šablony nasazení Azure Resource Manageru, je také nutné zahrnout vnějším `parameters` části definice šablony. Další informace o zabezpečení parametry najdete v tématu [zabezpečený přístup k aplikacím logiky](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters).

## <a name="next-steps"></a>Další postup

* Další informace o [jazyka definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md)

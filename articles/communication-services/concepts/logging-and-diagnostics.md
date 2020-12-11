---
title: Protokoly služby Communication Services
titleSuffix: An Azure Communication Services concept document
description: Další informace o protokolování komunikačních služeb Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: c4c9808813de80beea55e083c5bd80667ae2861f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033115"
---
# <a name="communication-services-logs"></a>Protokoly služby Communication Services

Komunikační služby Azure nabízejí možnosti protokolování, které můžete použít k monitorování a ladění řešení komunikačních služeb. Tyto možnosti se dají nakonfigurovat pomocí Azure Portal.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Povolení diagnostických protokolů v prostředku

Při vytvoření prostředku je protokolování vypnuto ve výchozím nastavení. Chcete-li povolit protokolování, přejděte do okna **nastavení diagnostiky** v nabídce prostředek v části **monitorování** . Pak klikněte na **Přidat nastavení diagnostiky**.

Potom vyberte cíl archivu, který chcete. V současné době podporujeme účty úložiště a Log Analytics jako cíle archivace. Po výběru typů protokolů, které chcete zachytit, uložte nastavení diagnostiky.
 
Nové nastavení se projeví přibližně po deseti minutách. Protokoly se začnou zobrazovat v nakonfigurovaném cíli archivace v podokně protokoly v prostředku služby Communications.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="Možnosti nastavení diagnostiky ACS":::

Další informace o konfiguraci diagnostiky najdete v tématu Přehled [protokolů prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="resource-log-categories"></a>Kategorie protokolu prostředků

Komunikační služby nabízí tři typy protokolů, které můžete povolit:

* **Protokoly využití** – poskytuje data o využití přidružená k jednotlivým položkám s fakturovanými službami.
* **Provozní protokoly chatu** – poskytuje základní informace týkající se služby chatu.
* **Operační protokoly SMS** – poskytuje základní informace týkající se služby SMS.
* **Provozní protokoly ověřování** – poskytuje základní informace týkající se ověřovací služby.

### <a name="usage-logs-schema"></a>Schéma protokolů využití

| Vlastnost | Popis |
| -------- | ---------------|
| Timestamp | Časové razítko (UTC), kdy se protokol vygeneroval. |
| Název operace | Operace přidružená k záznamu protokolu |
| Verze operace | `api-version`Přidružená k operaci, pokud byl operace provedena pomocí rozhraní API. Pokud neexistuje žádné rozhraní API, které by odpovídalo této operaci, verze představuje verzi této operace pro případ, že se vlastnosti přidružené k operaci v budoucnu mění. |
| Kategorie | Kategorie protokolu události Kategorie je členitost, na které můžete povolit nebo zakázat protokoly na konkrétním prostředku. Vlastnosti, které se zobrazují v objektu BLOB vlastností události, jsou stejné v rámci konkrétní kategorie protokolu a typu prostředku. |
| ID korelace | ID pro korelační události Dá se použít k identifikaci korelačních událostí mezi několika tabulkami. |
| Vlastnosti | Další data vztahující se na různé režimy komunikačních služeb. |
| ID záznamu | Jedinečné ID pro daný záznam využití. |
| Typ využití | Režim použití. (například chat, PSTN, NAT atd.) |
| Typ jednotky | Typ jednotky, která je založená na využití pro daný režim použití. (například minuty, megabajty, zprávy atd.). |
| Množství | Počet jednotek použitých nebo spotřebovaných pro tento záznam. |

### <a name="chat-operational-logs"></a>Chat – provozní protokoly

| Vlastnost | Popis |
| -------- | ---------------|
| TimeGenerated | Časové razítko (UTC), kdy se protokol vygeneroval. |
| OperationName | Operace přidružená k záznamu protokolu |
| ID | ID pro korelační události Dá se použít k identifikaci korelačních událostí mezi několika tabulkami. |
| OperationVersion | Verze rozhraní API přidružená k operaci, pokud byla operace provedena pomocí rozhraní API. Pokud neexistuje žádné rozhraní API, které by odpovídalo této operaci, verze představuje verzi této operace pro případ, že se vlastnosti přidružené k operaci v budoucnu mění. |
| Kategorie | Kategorie protokolu události Kategorie je členitost, na které můžete povolit nebo zakázat protokoly na konkrétním prostředku. Vlastnosti, které se zobrazují v objektu BLOB vlastností události, jsou stejné v rámci konkrétní kategorie protokolu a typu prostředku. |
| ResultType | Stav operace. |
| ResultSignature | Stav sub operace. Pokud tato operace odpovídá volání REST API, toto pole je stavový kód HTTP odpovídajícího volání REST. |
| ResultDescription | Popis statického textu této operace. |
| DurationMs | Doba trvání operace v milisekundách. |
| CallerIpAddress | IP adresa volajícího, pokud operace odpovídá volání rozhraní API, které pochází z entity s veřejně dostupnou IP adresou. |
| Level | Úroveň závažnosti události |
| Identifikátor URI | Identifikátor URI požadavku |
| UserId | ID uživatele žádosti odesílatele. |
| ChatThreadId | ID vlákna chatu přidružené k žádosti |
| ChatMessageId | ID zprávy chatu přidružené k žádosti |
| SdkType | Typ sady SDK použitý v žádosti. |
| PlatformType | Typ platformy použitý v žádosti |

### <a name="sms-operational-logs"></a>Operační protokoly SMS

| Vlastnost | Popis |
| -------- | ---------------|
| TimeGenerated | Časové razítko (UTC), kdy se protokol vygeneroval. |
| OperationName | Operace přidružená k záznamu protokolu |
| ID | ID pro korelační události Dá se použít k identifikaci korelačních událostí mezi několika tabulkami. |
| OperationVersion | Verze rozhraní API přidružená k operaci, pokud byla operace provedena pomocí rozhraní API. Pokud neexistuje žádné rozhraní API, které by odpovídalo této operaci, verze představuje verzi této operace pro případ, že se vlastnosti přidružené k operaci v budoucnu mění. |
| Kategorie | Kategorie protokolu události Kategorie je členitost, na které můžete povolit nebo zakázat protokoly na konkrétním prostředku. Vlastnosti, které se zobrazují v objektu BLOB vlastností události, jsou stejné v rámci konkrétní kategorie protokolu a typu prostředku. |
| ResultType | Stav operace. |
| ResultSignature | Stav sub operace. Pokud tato operace odpovídá volání REST API, toto pole je stavový kód HTTP odpovídajícího volání REST. |
| ResultDescription | Popis statického textu této operace. |
| DurationMs | Doba trvání operace v milisekundách. |
| CallerIpAddress | IP adresa volajícího, pokud operace odpovídá volání rozhraní API, které pochází z entity s veřejně dostupnou IP adresou. |
| Level | Úroveň závažnosti události |
| Identifikátor URI | Identifikátor URI požadavku |
| OutgoingMessageLength | Počet znaků v odchozí zprávě. |
| IncomingMessageLength | Počet znaků v příchozí zprávě. |
| DeliveryAttempts | Počet pokusů o doručení této zprávy. |
| PhoneNumber | Telefonní číslo, na které se odesílá zpráva SMS |
| SdkType | Typ sady SDK použitý v žádosti. |
| PlatformType | Typ platformy použitý v žádosti |
| Metoda | Metoda použitá v žádosti |

### <a name="authentication-operational-logs"></a>Provozní protokoly ověřování

| Vlastnost | Popis |
| -------- | ---------------|
| TimeGenerated | Časové razítko (UTC), kdy se protokol vygeneroval. |
| OperationName | Operace přidružená k záznamu protokolu |
| ID | ID pro korelační události Dá se použít k identifikaci korelačních událostí mezi několika tabulkami. |
| OperationVersion | `api-version`Přidružená k operaci, pokud `operationName` byla provedena pomocí rozhraní API. Pokud neexistuje žádné rozhraní API, které by odpovídalo této operaci, verze představuje verzi této operace pro případ, že se vlastnosti přidružené k operaci v budoucnu mění. |
| Kategorie | Kategorie protokolu události Kategorie je členitost, na které můžete povolit nebo zakázat protokoly na konkrétním prostředku. Vlastnosti, které se zobrazují v objektu BLOB vlastností události, jsou stejné v rámci konkrétní kategorie protokolu a typu prostředku. |
| ResultType | Stav operace. |
| ResultSignature | Dílčí stav operace. Pokud tato operace odpovídá volání REST API, toto pole je stavový kód HTTP odpovídajícího volání REST. |
| DurationMs | Doba trvání operace v milisekundách. |
| CallerIpAddress | IP adresa volajícího, pokud operace odpovídá volání rozhraní API, které pochází z entity s veřejně dostupnou IP adresou. |
| Level | Úroveň závažnosti události |
| Identifikátor URI | Identifikátor URI požadavku |
| SdkType | Typ sady SDK použitý v žádosti. |
| PlatformType | Typ platformy použitý v žádosti |
| Identita | Identita komunikačních služeb, která souvisí s operací. |
| Obory | Obory komunikačních služeb přítomné v přístupovém tokenu. |

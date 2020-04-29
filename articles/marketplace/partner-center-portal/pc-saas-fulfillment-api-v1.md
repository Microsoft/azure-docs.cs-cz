---
title: Rozhraní API pro plnění SaaS v1 | Azure Marketplace
description: Vysvětluje, jak vytvořit a spravovat nabídku SaaS na Azure Marketplace s použitím přidružených rozhraní API pro splnění verze V1.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288338"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>Rozhraní API pro splnění SaaS verze 1 (zastaralé)

Tento článek vysvětluje, jak vytvořit SaaS nabídku s rozhraními API. Rozhraní API, skládající se z metod REST a koncových bodů, jsou nutná pro povolení předplatných vaší nabídky SaaS, pokud jste prodávali pomocí Azure Selected.  

> [!WARNING]
> Tato počáteční verze rozhraní API pro plnění SaaS je zastaralá. místo toho použijte [rozhraní API pro splnění SaaS verze V2](./pc-saas-fulfillment-api-v2.md).  Tato počáteční verze rozhraní API se v současné době udržuje jenom pro poskytování stávajících vydavatelů. 

K dispozici jsou následující rozhraní API, která vám pomohou integrovat službu SaaS s Azure:

-   Vyřešit
-   Odběr
-   Převést
-   Přestat odebírat


## <a name="api-methods-and-endpoints"></a>Metody a koncové body rozhraní API

Následující části popisují metody a koncové body rozhraní API, které jsou k dispozici pro povolení předplatných nabídky SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Koncový bod rozhraní API Marketplace a verze rozhraní API

Koncový bod pro rozhraní Azure Marketplace API `https://marketplaceapi.microsoft.com`je.

Aktuální verze rozhraní API je `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Vyřešit předplatné

Akce po vyřešení koncového bodu umožňuje uživatelům přeložit token Marketplace na trvalé ID prostředku.  ID prostředku je jedinečný identifikátor pro předplatné SAAS. 

Když se uživatel přesměruje na web ISV, adresa URL obsahuje token v parametrech dotazu. U nezávislého výrobce softwaru se očekává, že tento token použijete, a požádejte ho, aby ho vyřešil. Odpověď obsahuje jedinečné ID předplatného SAAS, název, ID nabídky a plán pro daný prostředek. Tento token je platný jenom pro hodinu.

*Request*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Název parametru** |     **Popis**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  verze-api        |  Verze operace, která se má použít pro tento požadavek.   |
|  |  |


*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-RequestId     | Ne           | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi.  |
| x-MS-ID korelace | Ne           | Jedinečná řetězcová hodnota pro operaci na klientovi. Toto pole koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi. |
| Typ obsahu       | Ano          | `application/json`                                        |
| autorizace      | Ano          | Token nosiče webového tokenu JSON (JWT).                    |
| x-MS-Marketplace – token| Ano| Parametr dotazu tokenu v adrese URL, když se uživatel přesměruje na web SaaS ISV z Azure **Poznámka:** Tento token je platný jenom 1 hodina. Kromě toho adresa URL před použitím překóduje hodnotu tokenu z prohlížeče.|
|  |  |  |
  

*Tělo odpovědi*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Název parametru** | **Datový typ** | **Popis**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Řetězec        | ID předplatného SaaS          |
| subscriptionName| Řetězec| Název předplatného SaaS nastaveného uživatelem v Azure během přihlášení k odběru služby SaaS.|
| Hodnotami OfferId            | Řetězec        | ID nabídky, na kterou se uživatel přihlásil |
| planId             | Řetězec        | ID plánu, se kterým se uživatel přihlásil k odběru.  |
|  |  |  |


*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token byl úspěšně vyřešen.                                                            |
| 400                  | `BadRequest`         | Buď chybí požadovaná záhlaví, nebo je zadaná neplatná verze API-Version. Nepodařilo se přeložit token, protože token je poškozený nebo vypršela jeho platnost (token je platný jenom 1 hodina po vygenerování). |
| 403                  | `Forbidden`          | Volající nemá autorizaci k provedení této operace.                                 |
| 429                  | `RequestThrottleId`  | Služba zpracovává požadavky, které jsou zaneprázdněné, a zkuste to znovu později.                                |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, zkuste to znovu později.                                        |
|  |  |  |


*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ano          | ID žádosti přijaté od klienta                                                                   |
| x-MS-ID korelace | Ano          | ID korelace, je-li klient předán, jinak je tato hodnota ID korelace serveru.                   |
| x-MS-ActivityId    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Toto ID se používá pro všechna odsouhlasení. |
| Opakovat – za        | Ne           | Tato hodnota se nastavuje jenom pro odpověď 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Odběr

Koncový bod přihlášení k odběru umožňuje uživatelům spustit předplatné služby SaaS pro daný plán a povolit účtování v Commerce systému.

**PŘEVÉST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{SubscriptionId}*? API-Version = 2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu prostřednictvím řešení API.                              |
| verze-api         | Verze operace, která se má použít pro tento požadavek. |
|  |  |

*Hlavičky*

|  **Klíč záhlaví**        | **Požadováno** |  **Popis**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-MS-RequestId         |   Ne         | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud není zadán, bude vygenerována a uvedena v hlavičkách odpovědi. |
| x-MS-ID korelace     |   Ne         | Jedinečná řetězcová hodnota pro operaci na klientovi. Tato hodnota je určena pro korelaci všech událostí od klientské operace s událostmi na straně serveru. Pokud není zadán, bude vygenerována a uvedena v hlavičkách odpovědi. |
| If-Match/If-None-Match |   Ne         |   Silná ověřovací hodnota ETag.                                                          |
| typ obsahu           |   Ano        |    `application/json`                                                                   |
|  autorizace         |   Ano        |    Token nosiče webového tokenu JSON (JWT).                                               |
| x-MS-Marketplace – režim relace| Ne | Příznak, který povolí režim suchého běhu při přihlášení k odběru nabídky SaaS. Pokud je nastaveno, předplatné se nebude účtovat. To je užitečné pro scénáře testování ISV. Nastavte ho prosím na **' dryrun '.**|
|  |  |  |

*Text*

``` json
{
    "lanId": "",
}
```

| **Název elementu** | **Datový typ** | **Popis**                      |
|------------------|---------------|--------------------------------------|
| planId           | Požadovanou Řetezce        | ID plánu uživatele služby SaaS se přihlašuje k odběru.  |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Byla přijata Aktivace předplatného SaaS pro daný plán.                   |
| 400                  | `BadRequest`         | Chybí buď požadované hlavičky, nebo tělo JSON má špatný formát. |
| 403                  | `Forbidden`          | Volající nemá autorizaci k provedení této operace.                   |
| 404                  | `NotFound`           | Předplatné se nenašlo s daným ID.                                  |
| 409                  | `Conflict`           | V rámci předplatného probíhá jiná operace.                     |
| 429                  | `RequestThrottleId`  | Služba zpracovává požadavky, které jsou zaneprázdněné, a zkuste to znovu později.                  |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, zkuste to znovu později.                          |
|  |  |  |

V případě odpovědi 202 se řiďte stavem operace požadavku v hlavičce Operational Location. Ověřování je stejné jako u ostatních rozhraní API Marketplace.

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ano          | ID žádosti přijaté od klienta                                                                   |
| x-MS-ID korelace | Ano          | ID korelace, je-li klient předán, jinak je tato hodnota ID korelace serveru.                   |
| x-MS-ActivityId    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Tato hodnota se používá pro všechna odsouhlasení. |
| Opakovat – za        | Ano          | Interval, se kterým může klient kontrolovat stav.                                                       |
| Operace – umístění | Ano          | Připojte se k prostředku, abyste získali stav operace.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Změnit koncový bod plánu

Koncový bod změny umožňuje uživateli převést aktuálně přihlášený odběr plánu na nový plán.

**POUŽITA**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{SubscriptionId}*? API-Version = 2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID předplatného SaaS                              |
| verze-api         | Verze operace, která se má použít pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**          | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-MS-RequestId          | Ne           | Jedinečná řetězcová hodnota pro sledování požadavku od klienta. Doporučujeme identifikátor GUID. Pokud není zadán, bude vygenerována a uvedena v hlavičkách odpovědi.   |
| x-MS-ID korelace      | Ne           | Jedinečná řetězcová hodnota pro operaci na klientovi. Tato hodnota je určena pro korelaci všech událostí od klientské operace s událostmi na straně serveru. Pokud není zadán, bude vygenerována a uvedena v hlavičkách odpovědi. |
| /If-None-Match If-Match | Ne           | Silná ověřovací hodnota ETag.                              |
| typ obsahu            | Ano          | `application/json`                                        |
| autorizace           | Ano          | Token nosiče webového tokenu JSON (JWT).                    |
|  |  |  |

*Text*

```json
{
    "planId": ""
}
```

|  **Název elementu** |  **Datový typ**  | **Popis**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  Požadovanou Řetezce         | ID plánu uživatele služby SaaS se přihlašuje k odběru.          |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Byla přijata Aktivace předplatného SaaS pro daný plán.                   |
| 400                  | `BadRequest`         | Chybí buď požadované hlavičky, nebo tělo JSON má špatný formát. |
| 403                  | `Forbidden`          | Volající nemá autorizaci k provedení této operace.                   |
| 404                  | `NotFound`           | Předplatné se nenašlo s daným ID.                                  |
| 409                  | `Conflict`           | V rámci předplatného probíhá jiná operace.                     |
| 429                  | `RequestThrottleId`  | Služba zpracovává požadavky, které jsou zaneprázdněné, a zkuste to znovu později.                  |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, zkuste to znovu později.                          |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ano          | ID žádosti přijaté od klienta                                                                   |
| x-MS-ID korelace | Ano          | ID korelace, je-li klient předán, jinak je tato hodnota ID korelace serveru.                   |
| x-MS-ActivityId    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Tato hodnota se používá pro všechna odsouhlasení. |
| Opakovat – za        | Ano          | Interval, se kterým může klient kontrolovat stav.                                                       |
| Operace – umístění | Ano          | Připojte se k prostředku, abyste získali stav operace.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Odstranění předplatného

Akce Odstranit na koncovém bodu odběru umožňuje uživateli odstranit předplatné s daným ID.

*Request*

**DSTRANIT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{SubscriptionId}*? API-Version = 2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID předplatného SaaS                              |
| verze-api         | Verze operace, která se má použít pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-MS-RequestId     | Ne           | Jedinečná řetězcová hodnota pro sledování požadavku od klienta. Doporučujeme identifikátor GUID. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi.                                                           |
| x-MS-ID korelace | Ne           | Jedinečná řetězcová hodnota pro operaci na klientovi. Tato hodnota je určena pro korelaci všech událostí od klientské operace s událostmi na straně serveru. Pokud není zadán, bude vygenerována a uvedena v hlavičkách odpovědi. |
| autorizace      | Ano          | Token nosiče webového tokenu JSON (JWT).                    |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Byla přijata Aktivace předplatného SaaS pro daný plán.                   |
| 400                  | `BadRequest`         | Chybí buď požadované hlavičky, nebo tělo JSON má špatný formát. |
| 403                  | `Forbidden`          | Volající nemá autorizaci k provedení této operace.                   |
| 404                  | `NotFound`           | Předplatné se nenašlo s daným ID.                                  |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněná zpracováním požadavků. zkuste to prosím znovu později.                  |
| 503                  | `ServiceUnavailable` | Služba je dočasně mimo provoz. Zkuste to prosím znovu později.                          |
|  |  |  |

V případě odpovědi 202 se řiďte stavem operace požadavku v hlavičce Operational Location. Ověřování je stejné jako u ostatních rozhraní API Marketplace.

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ano          | ID žádosti přijaté od klienta                                                                   |
| x-MS-ID korelace | Ano          | ID korelace, pokud je klient předává, jinak se jedná o ID korelace serveru.                   |
| x-MS-ActivityId    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení. |
| Opakovat – za        | Ano          | Interval, se kterým může klient kontrolovat stav.                                                       |
| Operace – umístění | Ano          | Připojte se k prostředku, abyste získali stav operace.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Získání stavu operace

Tento koncový bod umožňuje uživateli sledovat stav aktivované asynchronní operace (přihlášení k odběru/zrušení předplatných nebo změna plánu).

*Request*

**Čtěte**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*? API-Version = 2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Jedinečné ID pro aktivaci operace.                |
| verze-api         | Verze operace, která se má použít pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ne           | Jedinečná řetězcová hodnota pro sledování požadavku od klienta. Doporučujeme identifikátor GUID. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi.   |
| x-MS-ID korelace | Ne           | Jedinečná řetězcová hodnota pro operaci na klientovi. Tato hodnota je určena pro korelaci všech událostí od klientské operace s událostmi na straně serveru. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi.  |
| autorizace      | Ano          | Token nosiče webového tokenu JSON (JWT).                    |
|  |  |  | 

*Tělo odpovědi*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Název parametru** | **Datový typ** | **Popis**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Řetězec        | ID operace                                                                      |
| status             | Výčet          | Stav operace, jedna z následujících možností: `In Progress`, `Succeeded`nebo `Failed`.          |
| resourceLocation   | Řetězec        | Připojte se k předplatnému, které jste vytvořili nebo upravili. Díky tomu může klient získat aktualizovanou operaci po stavu. Tato hodnota není nastavená pro `Unsubscribe` operace. |
| vytvářejí            | DateTime      | Čas vytvoření operace v UTC                                                           |
| lastModified       | DateTime      | Poslední aktualizace operace v UTC                                                      |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Požadavek GET byl úspěšně vyřešen a tělo obsahuje odpověď.    |
| 400                  | `BadRequest`         | Chybí buď požadovaná záhlaví, nebo byla zadána neplatná verze API-Version. |
| 403                  | `Forbidden`          | Volající nemá autorizaci k provedení této operace.                      |
| 404                  | `NotFound`           | Předplatné se nepodařilo najít s daným ID.                                     |
| 429                  | `RequestThrottleId`  | Služba zpracovává požadavky, které jsou zaneprázdněné, a zkuste to znovu později.                     |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, zkuste to znovu později.                             |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ano          | ID žádosti přijaté od klienta                                                                   |
| x-MS-ID korelace | Ano          | ID korelace, pokud je klient předává, jinak se jedná o ID korelace serveru.                   |
| x-MS-ActivityId    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení. |
| Opakovat – za        | Ano          | Interval, se kterým může klient kontrolovat stav.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Získat předplatné

Akce získat při přihlášení k odběru koncového bodu umožňuje uživateli načíst předplatné s daným identifikátorem prostředku.

*Request*

**Čtěte**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{SubscriptionId}*? API-Version = 2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID předplatného SaaS                              |
| verze-api         | Verze operace, která se má použít pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ne           | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi.                                                           |
| x-MS-ID korelace | Ne           | Jedinečná řetězcová hodnota pro operaci na klientovi. Tato hodnota je určena pro korelaci všech událostí od klientské operace s událostmi na straně serveru. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi. |
| autorizace      | Ano          | Token nosiče webového tokenu JSON (JWT).                                                                    |
|  |  |  |

*Tělo odpovědi*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Název parametru**     | **Datový typ** | **Popis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Řetězec        | ID prostředku předplatného SaaS v Azure    |
| Hodnotami OfferId                | Řetězec        | ID nabídky, na kterou se uživatel přihlásil         |
| planId                 | Řetězec        | ID plánu, se kterým se uživatel přihlásil k odběru.          |
| saasSubscriptionName   | Řetězec        | Název předplatného SaaS                |
| saasSubscriptionStatus | Výčet          | Stav operace.  Jeden z následujících produktů:  <br/> - `Subscribed`: Předplatné je aktivní.  <br/> - `Pending`: Uživatel vytvoří prostředek, ale neaktivuje ho ISV.   <br/> - `Unsubscribed`: Uživatel zrušil odběr.   <br/> - `Suspended`: Uživatel pozastavil předplatné.   <br/> - `Deactivated`: Předplatné Azure je pozastavené.  |
| vytvářejí                | DateTime      | Hodnota časového razítka vytváření předplatného ve standardu UTC |
| lastModified           | DateTime      | Hodnota časového razítka změny předplatného ve standardu UTC |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Požadavek GET byl úspěšně vyřešen a tělo obsahuje odpověď.    |
| 400                  | `BadRequest`         | Chybí buď požadovaná záhlaví, nebo byla zadána neplatná verze API-Version. |
| 403                  | `Forbidden`          | Volající nemá autorizaci k provedení této operace.                      |
| 404                  | `NotFound`           | Předplatné se nenašlo s daným ID.                                     |
| 429                  | `RequestThrottleId`  | Služba zpracovává požadavky, které jsou zaneprázdněné, a zkuste to znovu později.                     |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, zkuste to znovu později.                             |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ano          | ID žádosti přijaté od klienta                                                                   |
| x-MS-ID korelace | Ano          | ID korelace, pokud je klient předává, jinak se jedná o ID korelace serveru.                   |
| x-MS-ActivityId    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení. |
| Opakovat – za        | Ne           | Interval, se kterým může klient kontrolovat stav.                                                       |
| značk               | Ano          | Připojte se k prostředku, abyste získali stav operace.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Získání předplatných

Koncový bod přihlášení k předplatným umožňuje uživateli načíst všechna předplatná pro všechny nabídky od nezávislého výrobce softwaru.

*Request*

**Čtěte**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| verze-api         | Verze operace, která se má použít pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-MS-RequestId     | Ne           | Jedinečná řetězcová hodnota pro sledování požadavku od klienta. Doporučujeme identifikátor GUID. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi.             |
| x-MS-ID korelace | Ne           | Jedinečná řetězcová hodnota pro operaci na klientovi. Tato hodnota je určena pro korelaci všech událostí od klientské operace s událostmi na straně serveru. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi. |
| autorizace      | Ano          | Token nosiče webového tokenu JSON (JWT).                    |
|  |  |  |

*Tělo odpovědi*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Název parametru**     | **Datový typ** | **Popis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Řetězec        | ID prostředku předplatného SaaS v Azure    |
| Hodnotami OfferId                | Řetězec        | ID nabídky, na kterou se uživatel přihlásil         |
| planId                 | Řetězec        | ID plánu, kterému se uživatel přihlásil          |
| saasSubscriptionName   | Řetězec        | Název předplatného SaaS                |
| saasSubscriptionStatus | Výčet          | Stav operace.  Jeden z následujících produktů:  <br/> - `Subscribed`: Předplatné je aktivní.  <br/> - `Pending`: Uživatel vytvoří prostředek, ale neaktivuje ho ISV.   <br/> - `Unsubscribed`: Uživatel zrušil odběr.   <br/> - `Suspended`: Uživatel pozastavil předplatné.   <br/> - `Deactivated`: Předplatné Azure je pozastavené.  |
| vytvářejí                | DateTime      | Hodnota časového razítka vytváření předplatného v UTC |
| lastModified           | DateTime      | Hodnota časového razítka změny předplatného ve standardu UTC |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Požadavek GET byl úspěšně vyřešen a tělo obsahuje odpověď.    |
| 400                  | `BadRequest`         | Chybí buď požadovaná záhlaví, nebo byla zadána neplatná verze API-Version. |
| 403                  | `Forbidden`          | Volající nemá autorizaci k provedení této operace.                      |
| 404                  | `NotFound`           | Předplatné se nenašlo s daným ID.                                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněná zpracováním požadavků. zkuste to prosím znovu později.                     |
| 503                  | `ServiceUnavailable` | Služba je dočasně mimo provoz. Zkuste to prosím znovu později.                             |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-MS-RequestId     | Ano          | ID žádosti přijaté od klienta                                                                   |
| x-MS-ID korelace | Ano          | ID korelace, pokud je klient předává, jinak se jedná o ID korelace serveru.                   |
| x-MS-ActivityId    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení. |
| Opakovat – za        | Ne           | Interval, se kterým může klient kontrolovat stav.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>Webhook SaaS

Webhook SaaS slouží k proaktivnímu upozorňování změn na službu SaaS. Očekává se, že tento příspěvek rozhraní API nebude ověřený a služba Microsoftu ho bude volat. Očekává se, že služba SaaS před provedením akce v oznámení Webhooku zavolá rozhraní API operací k ověření a autorizaci. 

*Text*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Název parametru**     | **Datový typ** | **Popis**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | Řetězec       | Jedinečné ID pro aktivaci operace.                |
| activityId   | Řetězec        | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení.               |
| subscriptionId                     | Řetězec        | ID prostředku předplatného SaaS v Azure    |
| Hodnotami OfferId                | Řetězec        | ID nabídky, na kterou se uživatel přihlásil Tato možnost je k dispozici pouze s akcí Update.        |
| publisherId                | Řetězec        | ID vydavatele nabídky SaaS         |
| planId                 | Řetězec        | ID plánu, se kterým se uživatel přihlásil k odběru. Tato možnost je k dispozici pouze s akcí Update.          |
| action                 | Řetězec        | Akce, která aktivuje toto oznámení. Možné hodnoty – aktivovat, odstranit, pozastavit, obnovit, aktualizovat          |
| Časové razítko                 | Řetězec        | Hodnota časového razítka ve standardu UTC, kdy se toto oznámení aktivovalo.          |
|  |  |  |


## <a name="next-steps"></a>Další kroky

Vývojáři také mohou programově načítat a manipulaci s úlohami, nabídkami a profily vydavatelů pomocí [rozhraní portál partnerů cloudu REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).

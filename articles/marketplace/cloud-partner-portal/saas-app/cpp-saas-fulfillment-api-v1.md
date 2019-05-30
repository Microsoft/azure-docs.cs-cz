---
title: SaaS splnění API V1 | Azure Marketplace
description: Vysvětluje, jak vytvořit nabídky SaaS na Azure Marketplace pomocí plnění přidružené rozhraní API V1.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 03/28/2019
ms.author: pabutler
ROBOTS: NOINDEX
ms.openlocfilehash: 9b80f0fd36545de94e7128080dba5e516344c107
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257505"
---
# <a name="saas-fulfillment-apis-version-1--deprecated"></a>SaaS splnění rozhraní API verze 1 (zastaralé)

Tento článek vysvětluje, jak vytvořit nabídky SaaS s rozhraními API. Rozhraní API, skládá z koncových bodů a metody REST jsou nezbytné pro povolení předplatných pro vaši nabídku SaaS, pokud mají prodávat prostřednictvím Azure vybrané.  

> [!IMPORTANT] 
> SaaS nabízejí funkce se migroval na [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Všechny nové zdroje musí pomocí partnerského centra pro vytvoření nové nabídky SaaS a správu existující nabídky.  Aktuální vydavatele se nabídky SaaS se batchwise migrují z portál partnerů cloudu do partnerského centra.  Portál partnerů cloudu se zobrazí stavové zprávy k označení, když se konkrétní stávající nabídky migrovaly.
> Další informace najdete v tématu [vytvoření nové nabídky SaaS](../../partner-center-portal/create-new-saas-offer.md).

> [!WARNING]
> Tato počáteční verze rozhraní API pro splnění SaaS je zastaralá; Místo toho použijte [API V2 SaaS splnění](./cpp-saas-fulfillment-api-v2.md).  Toto rozhraní API v současné době se spravují pouze k poskytování existujících vydavatelů. 

Jsou k dispozici následující rozhraní API umožňují integrovat vaší služby SaaS pomocí Azure:

-   Vyřešit
-   Přihlásit odběr
-   Převést
-   odhlášení odběru


## <a name="api-methods-and-endpoints"></a>Koncové body a metody rozhraní API

Následující části popisují metody rozhraní API a koncové body, které jsou k dispozici pro povolení předplatná pro nabídky SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Koncový bod rozhraní API Marketplace a verzi rozhraní API

Koncový bod pro rozhraní API služby Azure Marketplace je `https://marketplaceapi.microsoft.com`.

Aktuální verze rozhraní API je `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Vyřešit předplatného

Akce POST na vyřešit koncový bod umožňuje uživatelům vyřešit marketplace token trvalého ID prostředku.  ID prostředku je jedinečný identifikátor pro předplatné SAAS. 

Když uživatel se přesměruje na web nezávislých výrobců softwaru, adresa URL obsahuje token v parametry dotazu. Výrobci má používat tento token a požádat o jeho vyřešení. Odpověď obsahuje ID předplatného SAAS jedinečný název, ID nabídky a plán pro prostředek. Tento token je platný pouze jedna hodina.

*Požadavek*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Název parametru** |     **Popis**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  verze API-version        |  Verze operace pro tento požadavek.   |
|  |  |


*Hlavičky*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
| x-ms-correlationid | Ne           | Jedinečnou hodnotu řetězce pro operaci na straně klienta. To koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
| Content-type       | Ano          | `application/json`                                        |
| Autorizace      | Ano          | JSON web token (JWT) nosný token.                    |
| x-ms-marketplace-token| Ano| Parametr token dotazu v adrese URL, když je uživatel přesměrován na SaaS ISV webu z Azure. **Poznámka:** Tento token je platný jenom 1 hodinu. Kromě toho adresa URL dekódovat hodnota tokenu z prohlížeče před jeho použitím.|
|  |  |  |
  

*Text odpovědi*

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
| id                 | String        | ID předplatného SaaS.          |
| subscriptionName| String| Název předplatného SaaS nastavena podle uživatele v Azure při přihlášení k odběru ve službě SaaS.|
| offerId            | String        | ID nabídky, který uživatel přihlášen k odběru. |
| planId             | String        | ID plánu, který uživatel přihlášen k odběru.  |
|  |  |  |


*Kódy odpovědí*

| **Kód stavu HTTP** | **Kód chyby:**     | **Popis**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token úspěšně vyřešen.                                                            |
| 400                  | `BadRequest`         | Vyžaduje buď chybí záhlaví nebo zadaná neplatná api-version. Nepovedlo se analyzovat token, protože buď token je poškozené nebo jejichž platnost vypršela (token je platný jenom 1 hodinu po vygenerování). |
| 403                  | `Forbidden`          | Volající nemá oprávnění k provedení této operace.                                 |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněná zpracováním žádostí, zkuste to znovu později.                                |
| 503                  | `ServiceUnavailable` | Služba je dolů dočasně, zkuste to znovu později.                                        |
|  |  |  |


*Hlavičky odpovědi*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | Požadovat ID přijatou od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud předaná klientem, jinak tato hodnota je ID serveru korelace.                   |
| x-ms-activityid    | Ano          | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Toto ID se používá pro všechny odsouhlasení. |
| Retry-After        | Ne           | Tato hodnota je nastavena pouze pro odpověď 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Přihlásit odběr

Koncový bod přihlásit k odběru umožňuje uživatelům spustit předplatné služby SaaS pro daný plán a povolte fakturace v systému obchodování.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Jedinečné ID SaaS předplatné, které získáte po vyřešení token prostřednictvím rozhraní API vyřešit.                              |
| verze API-version         | Verze operace pro tento požadavek. |
|  |  |

*Hlavičky*

|  **Klíč hlavičky**        | **Požadováno** |  **Popis**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Ne         | Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud není zadáno, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
| x-ms-correlationid     |   Ne         | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud není zadáno, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
| If-Match nebo If-None-Match |   Ne         |   Silné ověřování hodnota ETag.                                                          |
| content-type           |   Ano        |    `application/json`                                                                   |
|  Autorizace         |   Ano        |    JSON web token (JWT) nosný token.                                               |
| x-ms-marketplace-session-mode| Ne | Příznak pro povolení zkušebním režimu při přihlášení k odběru nabídky SaaS. Pokud nastavíte, předplatné nebude nic účtovat. To je užitečné pro nezávislé výrobce softwaru testování scénářů. Nastavte ji na **"dryrun.**|
|  |  |  |

*Text*

``` json
{
    "lanId": "",
}
```

| **Název elementu** | **Datový typ** | **Popis**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Povinné) Řetězec        | Id plánu uživatele služby SaaS je přihlášena k odběru.  |
|  |  |  |

*Kódy odpovědí*

| **Kód stavu HTTP** | **Kód chyby:**     | **Popis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktivace předplatného SaaS pro daný plán.                   |
| 400                  | `BadRequest`         | Vyžaduje buď chybí záhlaví nebo text ve formátu JSON má chybný formát. |
| 403                  | `Forbidden`          | Volající nemá oprávnění k provedení této operace.                   |
| 404                  | `NotFound`           | Předplatné se nenašlo se zadaným ID.                                  |
| 409                  | `Conflict`           | Jiná operace probíhá v rámci předplatného.                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněná zpracováním žádostí, zkuste to znovu později.                  |
| 503                  | `ServiceUnavailable` | Služba je dolů dočasně, zkuste to znovu později.                          |
|  |  |  |

Pro odpovědi 202 vyřídit stav operace žádosti v hlavičce operace umístění. Ověřování je stejný jako jiná rozhraní API Marketplace.

*Hlavičky odpovědi*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | Požadovat ID přijatou od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud předaná klientem, jinak tato hodnota je ID serveru korelace.                   |
| x-ms-activityid    | Ano          | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Tato hodnota se používá pro všechny odsouhlasení. |
| Retry-After        | Ano          | Interval, pomocí kterého klienta můžete zkontrolovat stav.                                                       |
| Operace umístění | Ano          | Propojit k prostředku získat stav operace.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Změna plánu koncového bodu

Změna koncového bodu mu umožní převést jejich aktuálně předplacenému plán na nový plán.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID SaaS předplatného.                              |
| verze API-version         | Verze operace pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč hlavičky**          | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Ne           | Jedinečnou hodnotu řetězce pro sledování žádosti z klienta. Doporučujeme identifikátor GUID. Pokud není zadáno, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.   |
| x-ms-correlationid      | Ne           | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud není zadáno, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
| If-Match /If-None-Match | Ne           | Silné ověřování hodnota ETag.                              |
| content-type            | Ano          | `application/json`                                        |
| Autorizace           | Ano          | JSON web token (JWT) nosný token.                    |
|  |  |  |

*Text*

```json
{
    "planId": ""
}
```

|  **Název elementu** |  **Datový typ**  | **Popis**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Povinné) Řetězec         | Id plánu uživatele služby SaaS je přihlášena k odběru.          |
|  |  |  |

*Kódy odpovědí*

| **Kód stavu HTTP** | **Kód chyby:**     | **Popis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktivace předplatného SaaS pro daný plán.                   |
| 400                  | `BadRequest`         | Vyžaduje buď chybí záhlaví nebo text ve formátu JSON má chybný formát. |
| 403                  | `Forbidden`          | Volající nemá oprávnění k provedení této operace.                   |
| 404                  | `NotFound`           | Předplatné se nenašlo se zadaným ID.                                  |
| 409                  | `Conflict`           | Jiná operace probíhá v rámci předplatného.                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněná zpracováním žádostí, zkuste to znovu později.                  |
| 503                  | `ServiceUnavailable` | Služba je dolů dočasně, zkuste to znovu později.                          |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | Požadovat ID přijatou od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud předaná klientem, jinak tato hodnota je ID serveru korelace.                   |
| x-ms-activityid    | Ano          | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Tato hodnota se používá pro všechny odsouhlasení. |
| Retry-After        | Ano          | Interval, pomocí kterého klienta můžete zkontrolovat stav.                                                       |
| Operace umístění | Ano          | Propojit k prostředku získat stav operace.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Odstranit odběr

Akce odstranění na koncovém bodu přihlásit k odběru umožňuje uživateli odstranit odběr s daným ID.

*Požadavek*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID SaaS předplatného.                              |
| verze API-version         | Verze operace pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečnou hodnotu řetězce pro sledování žádosti z klienta. Doporučujeme identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.                                                           |
| x-ms-correlationid | Ne           | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud není zadáno, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
| Autorizace      | Ano          | JSON web token (JWT) nosný token.                    |
|  |  |  |

*Kódy odpovědí*

| **Kód stavu HTTP** | **Kód chyby:**     | **Popis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktivace předplatného SaaS pro daný plán.                   |
| 400                  | `BadRequest`         | Vyžaduje buď chybí záhlaví nebo text ve formátu JSON má chybný formát. |
| 403                  | `Forbidden`          | Volající nemá oprávnění k provedení této operace.                   |
| 404                  | `NotFound`           | Předplatné se nenašlo se zadaným ID.                                  |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněná zpracováním žádostí, zkuste to prosím znovu později.                  |
| 503                  | `ServiceUnavailable` | Služba je dočasně mimo provoz. Zkuste to prosím znovu později.                          |
|  |  |  |

Pro odpovědi 202 vyřídit stav operace žádosti v hlavičce operace umístění. Ověřování je stejný jako jiná rozhraní API Marketplace.

*Hlavičky odpovědi*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | Požadovat ID přijatou od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud předaná klientem, jinak to je ID serveru korelace.                   |
| x-ms-activityid    | Ano          | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Používá se pro všechny odsouhlasení. |
| Retry-After        | Ano          | Interval, pomocí kterého klienta můžete zkontrolovat stav.                                                       |
| Operace umístění | Ano          | Propojit k prostředku získat stav operace.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Načíst stav operace

Tento koncový bod umožňuje uživateli sledovat stav aktivovaných asynchronní operace (přihlásit k odběru nebo zrušení odběru/změnit plán).

*Požadavek*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/ *{IDoperace}* ?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Jedinečné ID pro operaci aktivuje.                |
| verze API-version         | Verze operace pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečnou hodnotu řetězce pro sledování žádosti z klienta. Doporučujeme identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.   |
| x-ms-correlationid | Ne           | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
| Autorizace      | Ano          | JSON web token (JWT) nosný token.                    |
|  |  |  | 

*Text odpovědi*

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
| id                 | String        | ID operace.                                                                      |
| status             | Výčet          | Stav operace, jednu z následujících: `In Progress`, `Succeeded`, nebo `Failed`.          |
| resourceLocation   | String        | Propojit s předplatným, které vytvořil nebo změnil. To pomáhá klientům získat aktualizovaný stav operace post. Tato hodnota není nastavená pro `Unsubscribe` operace. |
| Vytvoření            | DateTime      | Operace vytvoření čas ve standardu UTC.                                                           |
| lastModified       | DateTime      | Poslední aktualizace na operaci ve standardu UTC.                                                      |
|  |  |  |

*Kódy odpovědí*

| **Kód stavu HTTP** | **Kód chyby:**     | **Popis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Požadavek na získání úspěšně vyřešen a obsahuje tělo odpovědi.    |
| 400                  | `BadRequest`         | Vyžaduje buď chybí záhlaví nebo byla zadána neplatná api-version. |
| 403                  | `Forbidden`          | Volající nemá oprávnění k provedení této operace.                      |
| 404                  | `NotFound`           | Předplatné se nenašlo se zadaným ID.                                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněná zpracováním žádostí, zkuste to znovu později.                     |
| 503                  | `ServiceUnavailable` | Služba je dolů dočasně, zkuste to znovu později.                             |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | Požadovat ID přijatou od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud předaná klientem, jinak to je ID serveru korelace.                   |
| x-ms-activityid    | Ano          | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Používá se pro všechny odsouhlasení. |
| Retry-After        | Ano          | Interval, pomocí kterého klienta můžete zkontrolovat stav.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Získat předplatné

Přihlášení k odběru akce Get na koncový bod umožňuje uživateli získat předplatné s identifikátorem daný prostředek.

*Požadavek*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/ *{subscriptionId}* ?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID SaaS předplatného.                              |
| verze API-version         | Verze operace pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.                                                           |
| x-ms-correlationid | Ne           | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
| Autorizace      | Ano          | JSON web token (JWT) nosný token.                                                                    |
|  |  |  |

*Text odpovědi*

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
| id                     | String        | Prostředek předplatného ID SaaS v Azure.    |
| offerId                | String        | ID nabídky, který uživatel přihlášen k odběru.         |
| planId                 | String        | ID plánu, který uživatel přihlášen k odběru.          |
| saasSubscriptionName   | String        | Název předplatného SaaS.                |
| saasSubscriptionStatus | Výčet          | Stav operace.  Jeden z následujících akcí:  <br/> - `Subscribed`: Je předplatné aktivní.  <br/> - `Pending`: Uživatel vytvoří prostředek služby, ale nebude aktivní nezávislí.   <br/> - `Unsubscribed`: Uživatel má zrušili.   <br/> - `Suspended`: Uživatel pozastavil předplatné.   <br/> - `Deactivated`:  Předplatné Azure je pozastaveno.  |
| Vytvoření                | DateTime      | Hodnotu časové razítko vytvoření předplatného ve standardu UTC. |
| lastModified           | DateTime      | Předplatné změnit hodnotu časové razítko ve formátu UTC. |
|  |  |  |

*Kódy odpovědí*

| **Kód stavu HTTP** | **Kód chyby:**     | **Popis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Požadavek na získání úspěšně vyřešen a obsahuje tělo odpovědi.    |
| 400                  | `BadRequest`         | Vyžaduje buď chybí záhlaví nebo byla zadána neplatná api-version. |
| 403                  | `Forbidden`          | Volající nemá oprávnění k provedení této operace.                      |
| 404                  | `NotFound`           | Předplatné se nenašlo se zadaným ID.                                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněná zpracováním žádostí, zkuste to znovu později.                     |
| 503                  | `ServiceUnavailable` | Služba je dolů dočasně, zkuste to znovu později.                             |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | Požadovat ID přijatou od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud předaná klientem, jinak to je ID serveru korelace.                   |
| x-ms-activityid    | Ano          | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Používá se pro všechny odsouhlasení. |
| Retry-After        | Ne           | Interval, pomocí kterého klienta můžete zkontrolovat stav.                                                       |
| značka eTag               | Ano          | Propojit k prostředku získat stav operace.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Získat předplatná

Akce Get na koncový bod předplatná umožňuje uživateli získat všechna předplatná pro všechny nabídky z ISV.

*Požadavek*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| verze API-version         | Verze operace pro tento požadavek. |
|  |  |

*Hlavičky*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečnou hodnotu řetězce pro sledování žádosti z klienta. Doporučujeme identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.             |
| x-ms-correlationid | Ne           | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
| Autorizace      | Ano          | JSON web token (JWT) nosný token.                    |
|  |  |  |

*Text odpovědi*

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
| id                     | String        | Prostředek předplatného ID SaaS v Azure.    |
| offerId                | String        | ID nabídky, který uživatel přihlášen k odběru.         |
| planId                 | String        | ID plánu, který uživatel přihlášen k odběru.          |
| saasSubscriptionName   | String        | Název předplatného SaaS.                |
| saasSubscriptionStatus | Výčet          | Stav operace.  Jeden z následujících akcí:  <br/> - `Subscribed`: Je předplatné aktivní.  <br/> - `Pending`: Uživatel vytvoří prostředek služby, ale nebude aktivní nezávislí.   <br/> - `Unsubscribed`: Uživatel má zrušili.   <br/> - `Suspended`: Uživatel pozastavil předplatné.   <br/> - `Deactivated`:  Předplatné Azure je pozastaveno.  |
| Vytvoření                | DateTime      | Hodnotu časové razítko vytvoření předplatného ve standardu UTC. |
| lastModified           | DateTime      | Předplatné změnit hodnotu časové razítko ve formátu UTC. |
|  |  |  |

*Kódy odpovědí*

| **Kód stavu HTTP** | **Kód chyby:**     | **Popis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Požadavek na získání úspěšně vyřešen a obsahuje tělo odpovědi.    |
| 400                  | `BadRequest`         | Vyžaduje buď chybí záhlaví nebo byla zadána neplatná api-version. |
| 403                  | `Forbidden`          | Volající nemá oprávnění k provedení této operace.                      |
| 404                  | `NotFound`           | Předplatné se nenašlo se zadaným ID.                                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněná zpracováním žádostí, zkuste to prosím znovu později.                     |
| 503                  | `ServiceUnavailable` | Služba je dočasně mimo provoz. Zkuste to prosím znovu později.                             |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč hlavičky**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | Požadovat ID přijatou od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud předaná klientem, jinak to je ID serveru korelace.                   |
| x-ms-activityid    | Ano          | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Používá se pro všechny odsouhlasení. |
| Retry-After        | Ne           | Interval, pomocí kterého klienta můžete zkontrolovat stav.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

SaaS webhook se používá pro oznamování změn aktivně ve službě SaaS. Toto rozhraní API příspěvku má neověřené a zavolá se službou společnosti Microsoft. Očekává se, že služba SaaS volají operace rozhraní API k ověření a autorizaci před provedením akce na oznámení webhooku. 

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
| id  | String       | Jedinečné ID pro operaci aktivuje.                |
| activityId   | String        | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Používá se pro všechny odsouhlasení.               |
| subscriptionId                     | String        | Prostředek předplatného ID SaaS v Azure.    |
| offerId                | String        | ID nabídky, který uživatel přihlášen k odběru. K dispozici pouze s akcí "Úpravy".        |
| publisherId                | String        | ID vydavatele dané nabídky SaaS         |
| planId                 | String        | ID plánu, který uživatel přihlášen k odběru. K dispozici pouze s akcí "Úpravy".          |
| Akce                 | String        | Akce, která se aktivuje toto oznámení. Možné hodnoty - aktivovat, odstranit, pozastavit, obnovit, aktualizace          |
| Časové razítko                 | String        | Hodnota časového razítka ve standardu UTC, kdy tato upozornění byla aktivována.          |
|  |  |  |


## <a name="next-steps"></a>Další postup

Můžete také programově načítat vývojáři a manipulaci s úlohami, nabídky a vydavatel profilů pomocí [Cloud Partner Portal, rozhraní REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).

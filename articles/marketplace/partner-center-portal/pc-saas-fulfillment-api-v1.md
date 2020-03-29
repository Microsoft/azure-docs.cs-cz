---
title: SaaS Plnění API v1 | Azure Marketplace
description: Vysvětluje, jak vytvořit a spravovat nabídku SaaS na Azure Marketplace pomocí přidružených virtuálních api plnění v1.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288338"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS Plnění API verze 1 (zastaralé)

Tento článek vysvětluje, jak vytvořit nabídku SaaS s api. Api, složené z rest metod a koncových bodů, jsou nezbytné pro povolení předplatného vaší nabídky SaaS, pokud máte vyvolený Prodávat přes Azure.  

> [!WARNING]
> Tato počáteční verze rozhraní API plnění SaaS je zastaralá; místo toho použijte [rozhraní SaaS Fulfillment API V2](./pc-saas-fulfillment-api-v2.md).  Tato počáteční verze rozhraní API je aktuálně udržována pouze tak, aby sloužila existujícím vydavatelům. 

Následující api jsou k dispozici, které vám pomohou integrovat službu SaaS s Azure:

-   Vyřešit
-   Odběr
-   Převést
-   Přestat odebírat


## <a name="api-methods-and-endpoints"></a>Metody rozhraní API a koncové body

Následující části popisují metody rozhraní API a koncové body, které jsou k dispozici pro povolení předplatných pro nabídku SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Koncový bod rozhraní API marketplace a verze rozhraní API

Koncový bod pro rozhraní `https://marketplaceapi.microsoft.com`API Azure Marketplace je .

Aktuální verze rozhraní `api-version=2017-04-15`API je .


### <a name="resolve-subscription"></a>Vyřešit předplatné

Akce POST na konci řešení umožňuje uživatelům přeložit token tržiště na trvalé ID prostředku.  ID prostředku je jedinečný identifikátor pro odběr SAAS. 

Když je uživatel přesměrován na web uživatele isv, adresa URL obsahuje token v parametrech dotazu. Očekává se, že isv používat tento token a požádat o jeho vyřešení. Odpověď obsahuje jedinečné ID předplatného SAAS, název, ID nabídky a plán pro prostředek. Tento token je platný pouze hodinu.

*Požadavek*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Název parametru** |     **Popis**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  verze-api        |  Verze operace, která má být pro tento požadavek používána.   |
|  |  |


*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi.  |
| x-ms-correlationid | Ne           | Jedinečná hodnota řetězce pro operaci na straně klienta. Toto pole koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi. |
| Typ obsahu       | Ano          | `application/json`                                        |
| autorizace      | Ano          | Token nosiče JSON (JWT).                    |
| x-ms-marketplace-token| Ano| Parametr dotazu tokenu v adrese URL při přesměrování uživatele na web saas isv z Azure. **Poznámka:** Tento token je platný pouze po dobu 1 hodiny. Adresa URL navíc dekóduje hodnotu tokenu z prohlížeče před jeho použitím.|
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
| id                 | Řetězec        | ID předplatného SaaS.          |
| subscriptionName| Řetězec| Název předplatného SaaS nastaveného uživatelem v Azure při přihlášení ke službě SaaS.|
| OfferId            | Řetězec        | ID nabídky, k jehož odběru se uživatel přihlásil. |
| planId             | Řetězec        | ID plánu, k jehož odběru se uživatel přihlásil.  |
|  |  |  |


*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token byl úspěšně vyřešen.                                                            |
| 400                  | `BadRequest`         | Chybí buď požadovaná záhlaví, nebo je zadána neplatná verze rozhraní API. Nepodařilo se vyřešit token, protože token je poškozený nebo vypršela (token je platný pouze po dobu 1 hodiny po vygenerování). |
| 403                  | `Forbidden`          | Volající není oprávněn provést tuto operaci.                                 |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněna zpracováním požadavků, opakujte akci později.                                |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, opakujte akci později.                                        |
|  |  |  |


*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | ID požadavku přijaté od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud je klient předán, jinak je tato hodnota ID korelace serveru.                   |
| x-ms-activityid    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Toto ID se používá pro všechna odsouhlasení. |
| Opakovat po opakování        | Ne           | Tato hodnota je nastavena pouze pro odpověď 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Odběr

Koncový bod odběru umožňuje uživatelům spustit předplatné služby SaaS pro daný plán a povolit fakturaci v commerce systému.

**Dát**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Jedinečné ID předplatného SaaS, které se získá po vyřešení tokenu pomocí rozhraní Resolve API.                              |
| verze-api         | Verze operace, která má být pro tento požadavek používána. |
|  |  |

*Hlavičky*

|  **Klíč záhlaví**        | **Požadováno** |  **Popis**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Ne         | Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud to není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
| x-ms-correlationid     |   Ne         | Jedinečná hodnota řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud to není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
| Pokud-Shoda/If-None-Shoda |   Ne         |   Silná hodnota validátoru ETag.                                                          |
| typ obsahu           |   Ano        |    `application/json`                                                                   |
|  autorizace         |   Ano        |    Token nosiče JSON (JWT).                                               |
| x-ms-marketplace-session-mode| Ne | Příznak pro povolení režimu suchého běhu při přihlášení k odběru nabídky SaaS. Pokud je nastaveno, předplatné se nebude účtovat. To je užitečné pro scénáře testování isv. Nastavte jej prosím na **"dryrun"**|
|  |  |  |

*Text*

``` json
{
    "lanId": "",
}
```

| **Název elementu** | **Datový typ** | **Popis**                      |
|------------------|---------------|--------------------------------------|
| planId           | (Povinné) Řetězec        | ID plánu uživatele služby SaaS se přihlásí k odběru.  |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktivace předplatného SaaS přijata pro daný plán.                   |
| 400                  | `BadRequest`         | Chybí buď požadované hlavičky nebo je poškozeno tělo jsonu. |
| 403                  | `Forbidden`          | Volající není oprávněn provést tuto operaci.                   |
| 404                  | `NotFound`           | Předplatné nebylo nalezeno s daným ID                                  |
| 409                  | `Conflict`           | Další operace probíhá na předplatné.                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněna zpracováním požadavků, opakujte akci později.                  |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, opakujte akci později.                          |
|  |  |  |

Pro odpověď 202, v návaznosti na stav operace požadavku v záhlaví "Umístění operace". Ověřování je stejné jako u ostatních souborů API marketplace.

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | ID požadavku přijaté od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud je klient předán, jinak je tato hodnota ID korelace serveru.                   |
| x-ms-activityid    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Tato hodnota se používá pro všechna odsouhlasení. |
| Opakovat po opakování        | Ano          | Interval, se kterým může klient zkontrolovat stav.                                                       |
| Umístění operace | Ano          | Odkaz na prostředek pro získání stavu operace.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Změnit koncový bod plánu

Koncový bod změny umožňuje uživateli převést svůj aktuálně odebíaný plán na nový plán.

**Oprava**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID předplatného SaaS.                              |
| verze-api         | Verze operace, která má být pro tento požadavek používána. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**          | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Ne           | Jedinečná hodnota řetězce pro sledování požadavku od klienta. Doporučte identifikátor GUID. Pokud to není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.   |
| x-ms-correlationid      | Ne           | Jedinečná hodnota řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud to není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
| Pokud shoda /If-None-Shoda | Ne           | Silná hodnota validátoru ETag.                              |
| typ obsahu            | Ano          | `application/json`                                        |
| autorizace           | Ano          | Token nosiče JSON (JWT).                    |
|  |  |  |

*Text*

```json
{
    "planId": ""
}
```

|  **Název elementu** |  **Datový typ**  | **Popis**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (Povinné) Řetězec         | ID plánu uživatele služby SaaS se přihlásí k odběru.          |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktivace předplatného SaaS přijata pro daný plán.                   |
| 400                  | `BadRequest`         | Chybí buď požadované hlavičky nebo je poškozeno tělo jsonu. |
| 403                  | `Forbidden`          | Volající není oprávněn provést tuto operaci.                   |
| 404                  | `NotFound`           | Předplatné nebylo nalezeno s daným ID                                  |
| 409                  | `Conflict`           | Další operace probíhá na předplatné.                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněna zpracováním požadavků, opakujte akci později.                  |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, opakujte akci později.                          |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | ID požadavku přijaté od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud je klient předán, jinak je tato hodnota ID korelace serveru.                   |
| x-ms-activityid    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Tato hodnota se používá pro všechna odsouhlasení. |
| Opakovat po opakování        | Ano          | Interval, se kterým může klient zkontrolovat stav.                                                       |
| Umístění operace | Ano          | Odkaz na prostředek pro získání stavu operace.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Odstranění předplatného

Akce Odstranit na koncovém bodu odběru umožňuje uživateli odstranit odběr s daným ID.

*Požadavek*

**Odstranit**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID předplatného SaaS.                              |
| verze-api         | Verze operace, která má být pro tento požadavek používána. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečná hodnota řetězce pro sledování požadavku od klienta. Doporučte identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi.                                                           |
| x-ms-correlationid | Ne           | Jedinečná hodnota řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud to není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
| autorizace      | Ano          | Token nosiče JSON (JWT).                    |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Aktivace předplatného SaaS přijata pro daný plán.                   |
| 400                  | `BadRequest`         | Chybí buď požadované hlavičky nebo je poškozeno tělo jsonu. |
| 403                  | `Forbidden`          | Volající není oprávněn provést tuto operaci.                   |
| 404                  | `NotFound`           | Předplatné nebylo nalezeno s daným ID                                  |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněna zpracováním požadavků, opakujte akci později.                  |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá. Opakujte akci později.                          |
|  |  |  |

Pro odpověď 202, v návaznosti na stav operace požadavku v záhlaví "Umístění operace". Ověřování je stejné jako u ostatních souborů API marketplace.

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | ID požadavku přijaté od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud je předán klientem, jinak se jedná o ID korelace serveru.                   |
| x-ms-activityid    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení. |
| Opakovat po opakování        | Ano          | Interval, se kterým může klient zkontrolovat stav.                                                       |
| Umístění operace | Ano          | Odkaz na prostředek pro získání stavu operace.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Získání stavu operace

Tento koncový bod umožňuje uživateli sledovat stav spuštěné asynchronní operace (Subscribe/Unsubscribe/Change plan).

*Požadavek*

**Dostat**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | Jedinečné ID pro spuštěnou operaci.                |
| verze-api         | Verze operace, která má být pro tento požadavek používána. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečná hodnota řetězce pro sledování požadavku od klienta. Doporučte identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi.   |
| x-ms-correlationid | Ne           | Jedinečná hodnota řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi.  |
| autorizace      | Ano          | Token nosiče JSON (JWT).                    |
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
| id                 | Řetězec        | ID operace.                                                                      |
| status             | Výčet          | Stav operace, jeden z `In Progress` `Succeeded`následujících: , , nebo `Failed`.          |
| resourceLocation   | Řetězec        | Odkaz na předplatné, které bylo vytvořeno nebo změněno. To pomáhá klientovi získat aktualizované stav po operaci. Tato hodnota není `Unsubscribe` nastavena pro operace. |
| Vytvořen            | DateTime      | Čas vytvoření operace v čase UTC.                                                           |
| lastModified       | DateTime      | Poslední aktualizace operace v UTC.                                                      |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Úspěšně vyřešen požadavek get a tělo obsahuje odpověď.    |
| 400                  | `BadRequest`         | Buď chybí požadovaná záhlaví, nebo byla zadána neplatná verze rozhraní API. |
| 403                  | `Forbidden`          | Volající není oprávněn provést tuto operaci.                      |
| 404                  | `NotFound`           | Předplatné nebylo nalezeno s daným ID.                                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněna zpracováním požadavků, opakujte akci později.                     |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, opakujte akci později.                             |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | ID požadavku přijaté od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud je předán klientem, jinak se jedná o ID korelace serveru.                   |
| x-ms-activityid    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení. |
| Opakovat po opakování        | Ano          | Interval, se kterým může klient zkontrolovat stav.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Získat předplatné

Akce Získat na koncový bod odběru umožňuje uživateli načíst odběr s daným identifikátorem prostředku.

*Požadavek*

**Dostat**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID předplatného SaaS.                              |
| verze-api         | Verze operace, která má být pro tento požadavek používána. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi.                                                           |
| x-ms-correlationid | Ne           | Jedinečná hodnota řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi. |
| autorizace      | Ano          | Token nosiče JSON (JWT).                                                                    |
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
| id                     | Řetězec        | ID prostředku předplatného SaaS v Azure.    |
| offerId                | Řetězec        | ID nabídky, k jehož odběru se uživatel přihlásil.         |
| planId                 | Řetězec        | ID plánu, k jehož odběru se uživatel přihlásil.          |
| saasSubscriptionNázev   | Řetězec        | Název předplatného SaaS.                |
| saasSubscriptionStav | Výčet          | Stav operace.  Jeden z následujících produktů:  <br/> - `Subscribed`: Předplatné je aktivní.  <br/> - `Pending`: Uživatel vytvoří prostředek, ale není aktivován uživatelem ISV.   <br/> - `Unsubscribed`: Uživatel se odhlásil.   <br/> - `Suspended`: Uživatel pozastavil předplatné.   <br/> - `Deactivated`: Předplatné Azure je pozastaveno.  |
| Vytvořen                | DateTime      | Hodnota časového razítka vytvoření předplatného v čase UTC. |
| lastModified           | DateTime      | Předplatné změněné časové razítko hodnotu v UTC. |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Úspěšně vyřešen požadavek get a tělo obsahuje odpověď.    |
| 400                  | `BadRequest`         | Buď chybí požadovaná záhlaví, nebo byla zadána neplatná verze rozhraní API. |
| 403                  | `Forbidden`          | Volající není oprávněn provést tuto operaci.                      |
| 404                  | `NotFound`           | Předplatné nebylo nalezeno s daným ID                                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněna zpracováním požadavků, opakujte akci později.                     |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá, opakujte akci později.                             |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | ID požadavku přijaté od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud je předán klientem, jinak se jedná o ID korelace serveru.                   |
| x-ms-activityid    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení. |
| Opakovat po opakování        | Ne           | Interval, se kterým může klient zkontrolovat stav.                                                       |
| Etag               | Ano          | Odkaz na prostředek pro získání stavu operace.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Získat odběry

Get akce na odběry koncový bod umožňuje uživateli načíst všechna předplatná pro všechny nabídky z ISV.

*Požadavek*

**Dostat**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| verze-api         | Verze operace, která má být pro tento požadavek používána. |
|  |  |

*Hlavičky*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Ne           | Jedinečná hodnota řetězce pro sledování požadavku od klienta. Doporučte identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi.             |
| x-ms-correlationid | Ne           | Jedinečná hodnota řetězce pro operaci na straně klienta. Tato hodnota je pro korelaci všech událostí z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičce odpovědi. |
| autorizace      | Ano          | Token nosiče JSON (JWT).                    |
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
| offerId                | Řetězec        | ID nabídky, k jehož odběru se uživatel přihlásil         |
| planId                 | Řetězec        | ID plánu, k jehož odběru se uživatel přihlásil          |
| saasSubscriptionNázev   | Řetězec        | Název předplatného SaaS                |
| saasSubscriptionStav | Výčet          | Stav operace.  Jeden z následujících produktů:  <br/> - `Subscribed`: Předplatné je aktivní.  <br/> - `Pending`: Uživatel vytvoří prostředek, ale není aktivován uživatelem ISV.   <br/> - `Unsubscribed`: Uživatel se odhlásil.   <br/> - `Suspended`: Uživatel pozastavil předplatné.   <br/> - `Deactivated`: Předplatné Azure je pozastaveno.  |
| Vytvořen                | DateTime      | Hodnota časového razítka vytvoření předplatného v čase UTC |
| lastModified           | DateTime      | Hodnota časového razítka upraveného předplatného v čase UTC |
|  |  |  |

*Kódy odpovědí*

| **Stavový kód HTTP** | **Kód chyby**     | **Popis**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | Úspěšně vyřešen požadavek get a tělo obsahuje odpověď.    |
| 400                  | `BadRequest`         | Buď chybí požadovaná záhlaví, nebo byla zadána neplatná verze rozhraní API. |
| 403                  | `Forbidden`          | Volající není oprávněn provést tuto operaci.                      |
| 404                  | `NotFound`           | Předplatné nebylo nalezeno s daným ID                                     |
| 429                  | `RequestThrottleId`  | Služba je zaneprázdněna zpracováním požadavků, opakujte akci později.                     |
| 503                  | `ServiceUnavailable` | Služba je dočasně vypnutá. Opakujte akci později.                             |
|  |  |  |

*Hlavičky odpovědi*

| **Klíč záhlaví**     | **Požadováno** | **Popis**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Ano          | ID požadavku přijaté od klienta.                                                                   |
| x-ms-correlationid | Ano          | ID korelace, pokud je předán klientem, jinak se jedná o ID korelace serveru.                   |
| x-ms-activityid    | Ano          | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení. |
| Opakovat po opakování        | Ne           | Interval, se kterým může klient zkontrolovat stav.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS webový hák

Webový hák SaaS se používá pro aktivní upozornění změn na službu SaaS. Očekává se, že toto rozhraní POST API bude neověřené a bude voláno službou společnosti Microsoft. Očekává se, že služba SaaS zavolá rozhraní API operací k ověření a autorizaci před přijetím akce na oznámení webhooku. 

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
| id  | Řetězec       | Jedinečné ID pro spuštěnou operaci.                |
| activityId   | Řetězec        | Jedinečná hodnota řetězce pro sledování požadavku ze služby. Používá se pro všechna odsouhlasení.               |
| subscriptionId                     | Řetězec        | ID prostředku předplatného SaaS v Azure.    |
| offerId                | Řetězec        | ID nabídky, k jehož odběru se uživatel přihlásil. K dispozici pouze s akcí "Aktualizovat".        |
| id vydavatele                | Řetězec        | ID vydavatele nabídky SaaS         |
| planId                 | Řetězec        | ID plánu, k jehož odběru se uživatel přihlásil. K dispozici pouze s akcí "Aktualizovat".          |
| action                 | Řetězec        | Akce, která spouští toto oznámení. Možné hodnoty - Aktivovat, odstranit, pozastavit, obnovit, aktualizovat          |
| Časové razítko                 | Řetězec        | Hodnota TImestamp v utc při aktivaci tohoto oznámení.          |
|  |  |  |


## <a name="next-steps"></a>Další kroky

Vývojáři mohou také programově načítat a manipulovat s úlohami, nabídkami a profily vydavatelů pomocí rozhraní [REST rozhraní REST portálu cloudových partnerů](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).

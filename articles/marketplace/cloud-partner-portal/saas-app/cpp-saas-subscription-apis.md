---
title: Zákazník SaaS pomocí Azure – rozhraní API | Dokumentace Microsoftu
description: Vysvětluje, jak vytvořit nabídky SaaS prostřednictvím webu marketplace rozhraní API.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: a778723093b226ee0e681c2a95ce4db597a310e5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198992"
---
# <a name="saas-sell-through-azure---apis"></a>Zákazník SaaS pomocí Azure – rozhraní API

Tento článek vysvětluje, jak vytvořit nabídky SaaS s rozhraními API. Rozhraní API jsou nezbytné pro povolení předplatných pro vaši nabídku SaaS, pokud jste zákazník prostřednictvím Azure vybrané.  Tento článek je rozdělený do dvou částí:

-   Ověřování služba služba mezi služby SaaS a webu Azure Marketplace
-   Koncové body a metody rozhraní API

Jsou k dispozici následující rozhraní API umožňují integrovat vaší služby SaaS pomocí Azure:

-   Vyřešit
-   Přihlásit odběr
-   Převést
-   Odhlásit odběr

Následující diagram znázorňuje tok předplatného nového zákazníka, a pokud se používá tato rozhraní API:

![Nabídky SaaS API toku](./media/saas-offer-publish-api-flow.png)


## <a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>Služby pro službu ověřování mezi službou SaaS a webu Azure marketplace

Azure nepředstavuje jakákoliv omezení u ověřování, které služba SaaS poskytuje koncovým uživatelům. Pokud jde o službě SaaS komunikaci s rozhraními API Azure Marketplace, ověřování provádí v kontextu aplikace služby Azure Active Directory (Azure AD).

Následující část popisuje, jak vytvořit aplikaci Azure AD.


### <a name="register-an-azure-ad-application"></a>Registrace aplikace Azure AD

Všechny aplikace, které chtějí využívat možnosti Azure AD, musí být nejdřív zaregistrované v tenantovi Azure AD. Tento proces registrace zahrnuje poskytuje Azure AD podrobnosti o vaší aplikaci, jako je například adresa URL, kde je umístěn, adresa URL pro odeslání odpovědi po ověření uživatele, identifikátor URI, který identifikuje aplikaci a tak dále.

Registrace nové aplikace pomocí webu Azure portal, postupujte následovně:

1.  Přihlaste se k [Portálu Azure](https://portal.azure.com/).
2.  Pokud váš účet umožňuje přístup k více účtům, klikněte na požadovaný účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
3.  V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikací**a klikněte na tlačítko **registrace nové aplikace**.

    ![Registrace aplikací SaaS AD](./media/saas-offer-app-registration.png)

4.  Na stránce pro vytvoření, zadejte vaše aplikace\'s informace o registraci:
    -   **Název**: Zadejte název smysluplné aplikace
    -   **Typ aplikace**: 
        - Vyberte **Nativní** pro [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application), které jsou nainstalované místně na zařízení. Toto nastavení se používá pro veřejné [nativní klienty](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) OAuth.
        - Vyberte **webová aplikace / rozhraní API** pro [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) a [prostředků nebo rozhraní API aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) , které jsou nainstalovány na zabezpečení serveru. Toto nastavení se používá pro OAuth důvěrné [webových klientů](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) a veřejné [uživatelského agenta – klienti se systémem](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        Stejná aplikace může také zpřístupnit klienta i prostředek / rozhraní API.
    -   **Adresa URL přihlašování**: Pro webové aplikace nebo rozhraní API aplikace zadejte základní adresu URL vaší aplikace. Například **http://localhost:31544** může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by pak pomocí této adresy URL pro přihlášení k webové klientské aplikace.
    -   **Identifikátor URI pro přesměrování**: U nativních aplikací zadejte identifikátor URI používá Azure AD k vracení odpovědí na tokeny. Zadejte hodnotu specifickou pro vaši aplikaci, například **http://MyFirstAADApp**.

        ![Registrace aplikací SaaS AD](./media/saas-offer-app-registration-2.png) konkrétní příklady webových nebo nativních aplikací, podívejte se na rychlý start na základě nastavení, které jsou k dispozici v části Začínáme [příručku pro vývojáře v Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#get-started).

5.  Jakmile budete hotovi, klikněte na **Vytvořit**. Azure AD jedinečný Identifikátor aplikace přiřadí vaší aplikaci a\'znovu provést do vaší aplikace\'s hlavním registrační stránku. V závislosti na tom, jestli je vaše aplikace webová nebo nativní, jsou k dispozici různé volby pro přidání dalších možností do vaší aplikace.

    **Poznámka:** ve výchozím nastavení, nově registrovaných aplikací umožňují povolit pouze uživatele ze stejného tenanta k přihlášení do vaší aplikace.

<a name="api-methods-and-endpoints"></a>Koncové body a metody rozhraní API
-------------------------

Následující části popisují metody rozhraní API a koncové body, které jsou k dispozici pro povolení předplatná pro nabídky SaaS.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Získání tokenu podle aplikace Azure AD

Metoda HTTP

`GET`

*URL požadavku*

**https://login.microsoftonline.com/*{ID Tenanta}*/oauth2/token**

*Parametr URI*

|  **Název parametru**  | **Požadováno**  | **Popis**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| ID Tenanta             | True          | ID klienta registrované aplikace AAD   |
|  |  |  |


*Hlavička požadavku*

|  **Název hlavičky**  | **Požadováno** |  **Popis**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Typ obsahu     | True         | Typ obsahu přidruženého k požadavku. Výchozí hodnota je `application/x-www-form-urlencoded`.  |
|  |  |  |


*Text žádosti*

| **Název vlastnosti**   | **Požadováno** |  **Popis**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Typ udělení oprávnění. Výchozí hodnota je `client_credentials`.                    |
|  Client_id          | True         |  Identifikátor klienta nebo aplikace přidružené k aplikaci Azure AD.                  |
|  client_secret      | True         |  Heslo přidružené k aplikaci Azure AD.                               |
|  Prostředek           | True         |  Cílový prostředek, pro kterou je požadována token. Výchozí hodnota je `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Odpověď*

|  **Název**  | **Typ**       |  **Popis**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Požadavek byl úspěšný.   |
|  |  |  |

*TokenResponse*

Ukázkové odpovědi tokenu:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

### <a name="marketplace-api-endpoint-and-api-version"></a>Koncový bod rozhraní API Marketplace a verzi rozhraní API

Koncový bod pro rozhraní API služby Azure Marketplace je `https://marketplaceapi.microsoft.com`.

Aktuální verze rozhraní API je `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Vyřešit předplatného

Akce POST na vyřešit koncový bod umožňuje uživatelům vyřešit token trvalého ID prostředku.

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
        “id”: “”, 
        “subscriptionName”: “”,
        “offerId”:””, 
         “planId”:””
    }     
```

| **Název parametru** | **Datový typ** | **Popis**                       |
|--------------------|---------------|---------------------------------------|
| id                 | Řetězec        | ID předplatného SaaS.          |
| subscriptionName| Řetězec| Název předplatného SaaS nastavena podle uživatele v Azure při přihlášení k odběru ve službě SaaS.|
| OfferId            | Řetězec        | ID nabídky, který uživatel přihlášen k odběru. |
| planId             | Řetězec        | ID plánu, který uživatel přihlášen k odběru.  |
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
| x-ms-activityid    | Ano          | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Používá se pro všechny odsouhlasení. |
| Retry-After        | Ne           | Tato hodnota je nastavena pouze pro odpověď 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Přihlásit odběr

Koncový bod přihlásit k odběru umožňuje uživatelům spustit předplatné služby SaaS pro daný plán a povolte fakturace v systému obchodování.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Název parametru**  | **Popis**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Jedinečné Id předplatného saas, která se získá po vyřešení token prostřednictvím rozhraní API vyřešit.                              |
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
      “planId”:””
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

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

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

``` json
                { 
                    “planId”:””
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

### <a name="delete-subscription"></a>Odstranit předplatné

Akce odstranění na koncovém bodu přihlásit k odběru umožňuje uživateli odstranit odběr s daným ID.

*Požadavek*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

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

**https://marketplaceapi.microsoft.com/api/saas/operations/*{IDoperace}*?api-version=2017-04-15**

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

``` json
  { 
      “id”: “”, 
      “status”:””, 
       “resourceLocation”:””, 
      “created”:””, 
      “lastModified”:”” 
  } 
```

| **Název parametru** | **Datový typ** | **Popis**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | Řetězec        | ID operace.                                                                      |
| status             | Výčet          | Stav operace, jednu z následujících: `In Progress`, `Succeeded`, nebo `Failed`.          |
| resourceLocation   | Řetězec        | Propojit s předplatným, které vytvořil nebo změnil. To pomáhá klientům získat aktualizovaný stav operace post. Tato hodnota není nastavená pro `Unsubscribe` operace. |
| vytvořené            | DateTime      | Operace vytvoření čas ve standardu UTC.                                                           |
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

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

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

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “” 
  }
```
| **Název parametru**     | **Datový typ** | **Popis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Řetězec        | Prostředek předplatného ID SaaS v Azure.    |
| offerId                | Řetězec        | ID nabídky, který uživatel přihlášen k odběru.         |
| planId                 | Řetězec        | ID plánu, který uživatel přihlášen k odběru.          |
| saasSubscriptionName   | Řetězec        | Název předplatného SaaS.                |
| saasSubscriptionStatus | Výčet          | Stav operace.  Jeden z následujících akcí:  <br/> - `Subscribed`: Je předplatné aktivní.  <br/> - `Pending`: Uživatel vytvoří prostředek služby, ale nebude aktivní nezávislí.   <br/> - `Unsubscribed`: Uživatel má zrušili.   <br/> - `Suspended`: Uživatel pozastavil předplatné.   <br/> - `Deactivated`:  Předplatné Azure je pozastaveno.  |
| vytvořené                | DateTime      | Hodnotu časové razítko vytvoření předplatného ve standardu UTC. |
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

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “”
  }
```

| **Název parametru**     | **Datový typ** | **Popis**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | Řetězec        | Prostředek předplatného ID SaaS v Azure.    |
| offerId                | Řetězec        | ID nabídky, který uživatel přihlášen k odběru.         |
| planId                 | Řetězec        | ID plánu, který uživatel přihlášen k odběru.          |
| saasSubscriptionName   | Řetězec        | Název předplatného SaaS.                |
| saasSubscriptionStatus | Výčet          | Stav operace.  Jeden z následujících akcí:  <br/> - `Subscribed`: Je předplatné aktivní.  <br/> - `Pending`: Uživatel vytvoří prostředek služby, ale nebude aktivní nezávislí.   <br/> - `Unsubscribed`: Uživatel má zrušili.   <br/> - `Suspended`: Uživatel pozastavil předplatné.   <br/> - `Deactivated`:  Předplatné Azure je pozastaveno.  |
| vytvořené                | DateTime      | Hodnotu časové razítko vytvoření předplatného ve standardu UTC. |
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
    "offerId": "sampleSaaSOffer", // Provided with "Update" action
    "publisherId": "contoso", 
    "planId": "silver",     // Provided with "Update" action
    "action": "Activate", // Activate/Delete/Suspend/Reinstate/Update
    "timeStamp": "2018-12-01T00:00:00"
  }
```

| **Název parametru**     | **Datový typ** | **Popis**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | Řetězec       | Jedinečné ID pro operaci aktivuje.                |
| activityId   | Řetězec        | Jedinečnou hodnotu řetězce pro sledování žádosti ze služby. Používá se pro všechny odsouhlasení.               |
| subscriptionId                     | Řetězec        | Prostředek předplatného ID SaaS v Azure.    |
| offerId                | Řetězec        | ID nabídky, který uživatel přihlášen k odběru. K dispozici pouze s akcí "Úpravy".        |
| publisherId                | Řetězec        | ID vydavatele dané nabídky SaaS         |
| planId                 | Řetězec        | ID plánu, který uživatel přihlášen k odběru. K dispozici pouze s akcí "Úpravy".          |
| action                 | Řetězec        | Akce, která se aktivuje toto oznámení. Možné hodnoty - aktivovat, odstranit, pozastavit, obnovit, aktualizace          |
| Časové razítko                 | Řetězec        | Hodnota časového razítka ve standardu UTC, kdy tato upozornění byla aktivována.          |
|  |  |  |

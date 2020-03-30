---
title: Rozhraní Api pro plnění SaaS v2 | Azure Marketplace
description: Tento článek vysvětluje, jak vytvořit a spravovat nabídku SaaS na AppSource a Azure Marketplace pomocí přidruženého plnění v2 API.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275760"
---
# <a name="saas-fulfillment-apis-version-2"></a>Rozhraní API pro dodávky SaaS verze 2 

Tento článek podrobně popisuje api, která umožňují partnerům prodávat své aplikace SaaS na webu AppSource marketplace a na Azure Marketplace. Tato api jsou požadavkem pro transaktable SaaS nabídky na AppSource a Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Správa životního cyklu předplatného SaaS

Azure SaaS spravuje celý životní cyklus nákupu předplatného SaaS. Používá plnění API jako mechanismus řídit skutečné plnění, změny plánů a odstranění předplatného s partnerem. Účet zákazníka je založen na stavu předplatného SaaS, které společnost Microsoft spravuje. Následující diagram znázorňuje stavy a operace, které řídí změny mezi stavy.

![Stavy životního cyklu předplatného SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stavy předplatného SaaS

V následující tabulce jsou uvedeny stavy zřizování pro odběr SaaS, včetně popisa a sekvenční diagram pro každý (pokud je k dispozici). 

#### <a name="provisioning"></a>Zřizování

Když zákazník zahájí nákup, partner obdrží tyto informace v autorizačním kódu na interaktivní webové stránce zákazníka, která používá parametr adresy URL. Příkladem je `https://contoso.com/signup?token=..`, zatímco adresa URL vstupní `https://contoso.com/signup`stránky v Centru partnerů je . Autorizační kód lze ověřit a vyměnit za podrobnosti zřizovací služby voláním vyřešit rozhraní API.  Když služba SaaS dokončí zřizování, odešle aktivovat volání, které signalizuje, že plnění je dokončeno a zákazník může být fakturován. 

Následující diagram znázorňuje posloupnost volání rozhraní API pro scénář zřizování.  

![Rozhraní API volá pro zřizování služby SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Zřízena

Tento stav je ustálený stav zřízené služby.

##### <a name="provisioning-for-update"></a>Zřizování pro aktualizaci 

Tento stav znamená, že aktualizace existující služby čeká na vyřízení. Takovou aktualizaci může iniciovat zákazník, buď z tržiště, nebo ve službě SaaS (pouze pro transakce s přímým zákazníkem).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Zřizování aktualizací (při jeho iniciaci z trhu)

Následující diagram znázorňuje posloupnost akcí při zahájení aktualizace z trhu.

![Volání rozhraní API při zahájení aktualizace z trhu](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Zřizování aktualizací (při jeho inicializaci ze služby SaaS)

Následující diagram znázorňuje akce při spuštění aktualizace ze služby SaaS. (Volání webhooku je nahrazeno aktualizací předplatného iniciovaného službou SaaS.) 

![Volání rozhraní API při zahájení aktualizace ze služby SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Dočasně blokován.

Tento stav označuje, že platba odběratele nebyla přijata. Podle zásad poskytneme zákazníkovi lhůtu před zrušením předplatného. Pokud je předplatné v tomto stavu: 

- Jako partner se můžete rozhodnout snížit nebo zablokovat přístup uživatele ke službě.
- Předplatné musí být udržováno v obnovitelném stavu, který může obnovit plnou funkčnost bez ztráty dat nebo nastavení. 
- Očekávejte, že na konci období odkladu získáte žádost o obnovení tohoto předplatného prostřednictvím api pro plnění nebo žádosti o zrušení zřizování. 

#### <a name="unsubscribed"></a>Nepronajmeme 

Odběry dosáhnou tohoto stavu v reakci na explicitní požadavek zákazníka nebo nezaplacení náhrad. Očekává se od partnera, že data zákazníka jsou uchovávána pro obnovení na vyžádání po určitý počet dní a poté odstraněna. 


## <a name="api-reference"></a>referenční dokumentace k rozhraní API

V této části je dokumentováno *rozhraní API pro odběr* SaaS a rozhraní OPERATIONS *API*.  Hodnota parametru `api-version` pro rozhraní API `2018-08-31`verze 2 je .  


### <a name="parameter-and-entity-definitions"></a>Definice parametrů a entit

V následující tabulce jsou uvedeny definice běžných parametrů a entit používaných rozhraními API pro plnění.

|     Entita/parametr     |     Definice                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identifikátor IDENTIFIKÁTOR IDENTIFIKÁTOR PRO Prostředek SaaS.  |
| `name`                   | Popisný název poskytnutý pro tento prostředek zákazníkem. |
| `publisherId`            | Jedinečný identifikátor řetězce pro každého vydavatele (například "contoso"). |
| `offerId`                | Jedinečný identifikátor řetězce pro každou nabídku (například "offer1").  |
| `planId`                 | Jedinečný identifikátor řetězce pro každý plán/skladovou položku (například "stříbrná"). |
| `operationId`            | Identifikátor IDENTIFIKÁTOR GUID pro konkrétní operaci.  |
|  `action`                | Akce prováděná u prostředku `Unsubscribe`, `Suspend` `Reinstate`, `ChangePlan`, `ChangeQuantity` `Transfer`nebo , . |
|   |   |

Globálně jedinečné identifikátory ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) jsou 128bitová (32šestnáctimárová) čísla, která jsou obvykle generována automaticky. 

#### <a name="resolve-a-subscription"></a>Řešení předplatného 

Koncový bod řešení umožňuje vydavateli přeložit token tržiště na trvalé ID prostředku. ID prostředku je jedinečný identifikátor pro předplatné SaaS. Když je uživatel přesměrován na web partnera, adresa URL obsahuje token v parametrech dotazu. Očekává se, že partner použije tento token a požádá o jeho vyřešení. Odpověď obsahuje jedinečné ID předplatného SaaS, název, ID nabídky a plán pro prostředek. Tento token je platný pouze po dobu jedné hodiny. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Publikovat<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která má být pro tento požadavek používána.  |

*Hlavičky požadavku:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json` |
|  x-ms-requestid    |  Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  autorizace     |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Například:`Bearer <access_token>`" ". |
|  x-ms-marketplace-token  |  Parametr dotazu tokenu v adrese URL, když je uživatel přesměrován na web partnera `https://contoso.com/signup?token=..`SaaS z Azure (například: ). *Poznámka:* Adresa URL dekóduje hodnotu tokenu z prohlížeče před jeho použitím.  |

*Kódy odpovědí:*

Kód: 200<br>
Řeší neprůhledný token na předplatné SaaS. Tělo odezvy:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kód: 400<br>
Špatná žádost. x-ms-marketplace-token chybí, je poškozený nebo vypršela jeho platnost.

Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli.

Kód: 404<br>
Nebyl nalezen.

Kód: 500<br>
Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>Rozhraní API předplatného

Rozhraní API pro odběr podporuje následující operace HTTPS: **Get**, **Post**, **Patch**a **Delete**.


#### <a name="list-subscriptions"></a>Seznam předplatných

Zobrazí seznam všech předplatných SaaS pro vydavatele.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Získat<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametry dotazu:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Verze operace, která má být pro tento požadavek používána.  |

*Hlavičky požadavku:*

|                    |                   |
|  ---------------   |  ---------------  |
| Typ obsahu       |  `application/json`  |
| x-ms-requestid     |  Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
| x-ms-correlationid |  Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
| autorizace      |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Například:`Bearer <access_token>`" ".  |

*Kódy odpovědí:*

Kód: 200 <br/>
Získá vydavatele a odpovídající odběry pro všechny nabídky vydavatele, na základě ověřovací token.

>[!Note]
>[Mock API se](#mock-apis) používají při prvním vývoji nabídky, zatímco skutečná api je třeba použít při skutečném publikování nabídky.  Skutečná api a mock API se liší podle prvního řádku kódu.  V reálném rozhraní API `subscription` je oddíl, zatímco tato část neexistuje pro mock API.

Datová část odpovědi pro falešné rozhraní API:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
A pro skutečné API: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
Token pokračování bude k dispozici pouze v případě, že existují další "stránky" plány načíst. 

Kód: 403 <br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli. 

Kód: 500<br>
Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Získání předplatného

Získá zadané předplatné SaaS. Pomocí tohoto hovoru můžete získat informace o licenci a informace o plánu.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu prostřednictvím rozhraní Resolve API.   |
|  ApiVersion        |   Verze operace, která má být pro tento požadavek používána.   |

*Hlavičky požadavku:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      |  `application/json`  |
|  x-ms-requestid    |  Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  autorizace     |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Například:`Bearer <access_token>`" ".  |

*Kódy odpovědí:*

Kód: 200<br>
Získá odběr SaaS z identifikátoru. Datová část odpovědi:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli.

Kód: 404<br>
Nebyl nalezen.<br> 

Kód: 500<br>
Vnitřní chyba serveru.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Seznam dostupných plánů

Pomocí tohoto hovoru zjistíte, zda pro aktuálního vydavatele existují nějaké soukromé nebo veřejné nabídky.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Verze operace, která má být pro tento požadavek používána.  |

*Hlavičky požadavku:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     |  `application/json` |
|   x-ms-requestid   |   Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
|  x-ms-correlationid  | Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
|  autorizace     |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například:`Bearer <access_token>`" ". |

*Kódy odpovědí:*

Kód: 200<br>
Získá seznam dostupných plánů pro zákazníka. Tělo odezvy:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Kód: 404<br>
Nebyl nalezen.<br> 

Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli. <br> 

Kód: 500<br>
Vnitřní chyba serveru.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aktivace předplatného

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Publikovat<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která má být pro tento požadavek používána.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní RESOLVE API.  |

*Hlavičky požadavku:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json`  |
|  x-ms-requestid    | Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  x-ms-correlationid  | Jedinečná hodnota řetězce pro operaci na straně klienta. Tento řetězec koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  autorizace     |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například:`Bearer <access_token>`" ". |

*Nákladová část požadavku:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Kódy odpovědí:*

Kód: 200<br>
Aktivuje předplatné.<br>

Kód: 400<br>
Chybný požadavek: selhání ověření.

Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli.

Kód: 404<br>
Nebyl nalezen.

Kód: 500<br>
Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Změna plánu předplatného

Aktualizujte plán na předplatné.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která má být pro tento požadavek používána.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní RESOLVE API.  |

*Hlavičky požadavku:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json` |
|  x-ms-requestid    |   Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  x-ms-correlationid  |  Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.    |
| autorizace      |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například:`Bearer <access_token>`" ".  |

*Nákladová část požadavku:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Hlavičky požadavku:*

|                    |                   |
|  ---------------   |  ---------------  |
| Umístění operace | Odkaz na prostředek získat stav operace.   |

*Kódy odpovědí:*

Kód: 202<br>
Požadavek na změnu plánu byl přijat. Očekává se, že partner dotazování Operace Umístění k určení úspěchu nebo neúspěchu. <br>

Kód: 400<br>
Chybný požadavek: selhání ověření.

Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli.

Kód: 404<br>
Nebyl nalezen.

Kód: 500<br>
Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Oprava lze opravit pouze plán nebo množství najednou, nikoli obojí. Úpravy předplatného pomocí **služby** Update `allowedCustomerOperations`nejsou v aplikaci .

#### <a name="change-the-quantity-on-the-subscription"></a>Změna množství v předplatném

Aktualizujte množství v předplatném.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Oprava:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která má být pro tento požadavek používána.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní RESOLVE API.  |

*Hlavičky požadavku:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json` |
|  x-ms-requestid    |   Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  x-ms-correlationid  |  Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.    |
| autorizace      |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například:`Bearer <access_token>`" ".  |

*Nákladová část požadavku:*

```json
Request Body:
{
    "quantity": 5
}
```

*Hlavičky požadavku:*

|                    |                   |
|  ---------------   |  ---------------  |
| Umístění operace | Odkaz na prostředek získat stav operace.   |

*Kódy odpovědí:*

Kód: 202<br>
Požadavek na změnu množství byl přijat. Očekává se, že partner dotazování Operace Umístění k určení úspěchu nebo neúspěchu. <br>

Kód: 400<br>
Chybný požadavek: selhání ověření.


Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli.

Kód: 404<br>
Nebyl nalezen.

Kód: 500<br>
Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Oprava lze opravit pouze plán nebo množství najednou, nikoli obojí. Úpravy předplatného pomocí **služby** Update `allowedCustomerOperations`nejsou v aplikaci .

#### <a name="delete-a-subscription"></a>Odstranění předplatného

Odhlaste se a odstraňte zadané předplatné.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Odstranění<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která má být pro tento požadavek používána.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní RESOLVE API.  |

*Hlavičky požadavku:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     |  `application/json` |
|  x-ms-requestid    |   Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.   |
|  x-ms-correlationid  |  Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.   |
|  autorizace     |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například:`Bearer <access_token>`" ".  |

*Kódy odpovědí:*

Kód: 202<br>
Partner inicioval výzvu k odhlášení odběru SaaS.<br>

Kód: 400<br>
Odstranit v předplatným s `allowedCustomerOperations` **odstranit** není v .

Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli.

Kód: 404<br>
Nebyl nalezen.

Kód: 500<br>
Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Rozhraní API pro operace

Rozhraní API operací podporuje následující operace Patch and Get.

#### <a name="list-outstanding-operations"></a>Seznam nevyřízených operací 

Zobrazí seznam nevyřízených operací pro aktuálního vydavatele. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametry dotazu:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Verze operace, která má být pro tento požadavek používána.                |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní RESOLVE API.  |

*Hlavičky požadavku:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     |  `application/json` |
|  x-ms-requestid    |  Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  x-ms-correlationid |  Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  autorizace     |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například:`Bearer <access_token>`" ".  |

*Kódy odpovědí:*

Kód: 200<br> Získá seznam čekající operace na předplatné. Datová část odpovědi:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Kód: 400<br>
Chybný požadavek: selhání ověření.

Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli.

Kód: 404<br>
Nebyl nalezen.

Kód: 500<br>
Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Získání stavu operace

Umožňuje vydavateli sledovat stav zadané aktivované asynchronní `Subscribe` `Unsubscribe`operace `ChangePlan`(například , , , nebo `ChangeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která má být pro tento požadavek používána.  |

*Hlavičky požadavku:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      |  `application/json`   |
|  x-ms-requestid    |   Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  x-ms-correlationid |  Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi.  |
|  autorizace     |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Například:`Bearer <access_token>`" ".  |

*Kódy odpovědí:*<br>

Kód: 200<br> Získá zadané čekající operace SaaS. Datová část odpovědi:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Kód: 400<br>
Chybný požadavek: selhání ověření.

Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli.
 
Kód: 404<br>
Nebyl nalezen.

Kód: 500<br> Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Aktualizace stavu operace

Aktualizujte stav operace a označte úspěch nebo neúspěch pomocí zadaných hodnot.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Verze operace, která má být pro tento požadavek používána.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní RESOLVE API.  |
|  operationId       | Operace, která se dokončuje. |

*Hlavičky požadavku:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     | `application/json`   |
|   x-ms-requestid   |   Jedinečná hodnota řetězce pro sledování požadavku od klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečná hodnota řetězce pro operaci na straně klienta. Tento parametr koreluje všechny události z operace klienta s událostmi na straně serveru. Pokud tato hodnota není k dispozici, jeden bude generována a poskytnuta v hlavičkách odpovědi. |
|  autorizace     |  [Získejte JSON webový token (JWT) nosné token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například:`Bearer <access_token>`" ".  |

*Nákladová část požadavku:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Kódy odpovědí:*

Kód: 200<br> Výzva k informování o dokončení operace na straně partnera. Tato reakce by například mohla signalizovat změnu sedadel nebo plánů.

Kód: 400<br>
Chybný požadavek: selhání ověření.

Kód: 403<br>
Neoprávněným. Ověřovací token nebyl poskytnut nebo je neplatný nebo se požadavek pokouší o přístup k akvizici, která nepatří aktuálnímu vydavateli.

Kód: 404<br>
Nebyl nalezen.

Kód: 409<br>
Konflikt. Například novější transakce je již splněna.

Kód: 500<br> Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementace webového háku ve službě SaaS

Vydavatel musí implementovat webhooku v této službě SaaS proaktivně upozornit uživatele na změny ve své službě. Očekává se, že služba SaaS zavolá rozhraní API operací k ověření a autorizaci před přijetím akce na oznámení webhooku.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Pokud může být akce jedna z následujících: 
- `Unsubscribe`(po odstranění zdroje)
- `ChangePlan`(po dokončení operace plánu změn)
- `ChangeQuantity`(po dokončení operace změny množství)
- `Suspend`(pokud byl zdroj pozastaven)
- `Reinstate`(pokud byl zdroj po pozastavení obnoven)

Pokud může být stav jedním z následujících: 
- **Notstarted** <br>
 - **Probíhá** <br>
- **Úspěch** <br>
- **Failed** <br>
- **Konflikt** <br>

V oznámení webhooku jsou užitečné stavy buď **succeeded** a **Failed**. Životní cyklus operace je z **NotStarted** do terminálového stavu, jako **je Succeeded**, **Failed**nebo **Conflict**. Pokud obdržíte **NotStarted** nebo **InProgress**, nadále požadovat stav prostřednictvím rozhraní GET API, dokud operace nedosáhne terminálového stavu před přijetím akce. 

## <a name="mock-apis"></a>Mock API

Můžete použít naše falešná api, která vám pomohou začít s vývojem, zejména s vytvářením prototypů, stejně jako s testováním projektů. 

Koncový bod `https://marketplaceapi.microsoft.com/api` hostitele: (není vyžadováno žádné ověřování)<br/>
Verze rozhraní API:`2018-09-15`<br/>
Ukázkový identifikátor URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Cesty koncového bodu rozhraní API jsou stejné napříč mock i skutečné rozhraní API, ale verze rozhraní API se liší. Verze je `2018-09-15` pro falešnou `2018-08-31` verzi a pro produkční verzi. 

Některé volání rozhraní API v tomto článku lze provést do mock koncového bodu hostitele. Obecně lze očekávat, že získat falešná data zpět jako odpověď. Volání metody předplatného aktualizace na mock ROZHRANÍ API vždy vrátí 500. 

## <a name="next-steps"></a>Další kroky

Vývojáři mohou také programově načítat a manipulovat s úlohami, nabídkami a profily vydavatelů pomocí rozhraní [REST rozhraní REST portálu cloudových partnerů](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).

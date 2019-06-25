---
title: SaaS splnění rozhraní API v2 | Azure Marketplace
description: Tento článek vysvětluje, jak vytvořit a spravovat nabídky SaaS na AppSource a webu Azure Marketplace pomocí plnění přidružené rozhraní API v2.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: f5be0b8886500bdce50b95846826e5fdc53b5df1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202669"
---
# <a name="saas-fulfillment-apis-version-2"></a>Splnění SaaS rozhraní API, verze 2 

Tento článek podrobně popisuje rozhraní API, která mohou partneři k prodeji jejich SaaS aplikací v AppSource marketplace a na webu Azure Marketplace. Tato rozhraní API se požadavek na transactable SaaS nabídky na webu Azure Marketplace a AppSource.

## <a name="managing-the-saas-subscription-life-cycle"></a>Správa životního cyklu předplatného SaaS

Azure SaaS spravuje celý životní cyklus nákupu předplatného SaaS. Používá k řízení skutečné splnění splnění rozhraní API jako vhodný mechanismus, se změní na plánech a odstranění předplatného s partnerem. Faktura za zákazníka je založená na stav předplatného SaaS, které Microsoft udržuje. Následující diagram znázorňuje stavy a operace, které jednotky změny mezi stavy.

![Stavy životního cyklu předplatného SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stavy předplatného SaaS

V následující tabulce jsou uvedeny stavy zřizování pro předplatné SaaS, včetně popisu a pořadí diagram pro každou (pokud existuje). 

#### <a name="provisioning"></a>Zřizování

Když zákazník iniciuje nákup, partner obdrží tyto informace v autorizační kód na zákazníka interaktivní webové stránce, která používá parametr adresy URL. Příkladem je `https://contoso.com/signup?token=..`, že je adresa URL stránky cílové v partnerském centru `https://contoso.com/signup`. Autorizační kód lze ověřit a vyměňují podrobnosti o službě zřizování pomocí volání rozhraní API pro řešení.  Po dokončení zřizování služby SaaS odešle signál, že dokončení splnění a zákazníka může znamenat volání rozhraní aktivovat. 

Následující diagram znázorňuje posloupnost volání rozhraní API pro zřizování scénář.  

![Volání rozhraní API pro SaaS služby zřizování](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Zřízené

Tento stav se stálé zřízené služby.

##### <a name="provisioning-for-update"></a>Zřizování pro aktualizace 

Tento stav znamená, že aktualizace do existující služby čeká na vyřízení. Tato aktualizace může vyvolat zákazník, a to buď z marketplace, nebo ve službě SaaS (pouze pro přímé zákazníky transakce).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Zřizování pro aktualizaci (když je zahájeno z webu marketplace)

Následující diagram znázorňuje posloupnost akcí, když je aktualizace iniciované z marketplace.

![Volání rozhraní API, když je aktualizace iniciované z marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Zřizování pro aktualizace (po inicializaci ve službě SaaS)

Následující diagram znázorňuje akce při zahájení aktualizace ve službě SaaS. (Volání webhooku se nahrazuje aktualizace do předplatného, inicializuje služby SaaS.) 

![Volání rozhraní API při zahájení aktualizace ve službě SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Pozastaveno

Tento stav označuje, že nebyl přijat platby zákazníků. Pomocí zásad poskytujeme zákazníka a období odkladu před zrušením předplatného. Pokud je předplatné v tomto stavu: 

- Jako partner můžete snížit nebo blokovat přístup ke službě.
- Předplatné musí uchovávat v obnovitelných stavu, ve kterém můžete obnovit všechny funkce bez ztráty dat nebo nastavení. 
- Můžete očekávat obnovit žádost pro toto předplatné přes rozhraní API pro splnění nebo žádost o zrušení zřízení na konci období odkladu. 

#### <a name="unsubscribed"></a>Odhlášení odběru 

Předplatná kontaktovat tento stav v reakci na žádost explicitní zákazníka nebo nezaplacení poplatků. Očekávání od partnera je, že zákaznická data se uchovávají po dobu obnovení na vyžádání pro určitý počet dní a pak odstraní. 


## <a name="api-reference"></a>API – referenční informace

Tato část popisuje SaaS *rozhraní Subscription API* a *operace rozhraní API*.  Hodnota `api-version` parametr pro verzi 2 rozhraní API je `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definice parametru a entity

Následující tabulka obsahuje seznam definic pro společné parametry a entity používané v plnění rozhraní API.

|     Parametr/entity     |     Definice                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identifikátor GUID prostředku SaaS.  |
| `name`                   | Popisný název pro tento prostředek k dispozici příslušného zákazníka. |
| `publisherId`            | Identifikátor jedinečný řetězec pro jednotlivé vydavatele (například: "contoso"). |
| `offerId`                | Identifikátor jedinečný řetězec pro každou nabídku (například: "offer1").  |
| `planId`                 | Jedinečný řetězec identifikátoru pro každý plán nebo SKU (například: "stříbrná"). |
| `operationId`            | Identifikátor GUID pro určitou operaci.  |
|  `action`                | Akce prováděná s prostředkem, buď `subscribe`, `unsubscribe`, `suspend`, `reinstate`, nebo `changePlan`, `changeQuantity`, `transfer`.  |
|   |   |

Globálně jedinečné identifikátory ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) jsou čísla 128-bit (32 šestnáctkového čísla), která jsou obvykle automaticky generovány. 

#### <a name="resolve-a-subscription"></a>Vyřešit předplatné 

Umožňuje vyřešit koncový bod vydavatele řešení marketplace token ID trvalý prostředků. ID prostředku je jedinečný identifikátor pro předplatné SaaS. Když uživatel se přesměruje na web partnera, adresa URL obsahuje token v parametry dotazu. Partner má používat tento token a požádat o jeho vyřešení. Odpověď obsahuje ID předplatného SaaS jedinečný název, ID nabídky a plán pro prostředek. Tento token je platný pouze jednu hodinu. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Příspěvek<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace pro tento požadavek.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  authorization     |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |
|  x-ms-marketplace-token  |  Parametr token dotazu v adrese URL, když se uživatel přesměruje na web partnera SaaS od Azure (například: `https://contoso.com/signup?token=..`). *Poznámka:* Adresa URL dekóduje hodnoty tokenu z prohlížeče před jeho použitím.  |

*Kódy odpovědí:*

Kód: 200<br>
Přeloží neprůhledné token k odběru služby SaaS. Text odpovědi:
 

```json
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Kód: 400<br>
Chybný požadavek. x-ms-marketplace-token je chybí, je poškozený nebo jejichž platnost vypršela.

Kód: 403<br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele.

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

### <a name="subscription-api"></a>Předplatné rozhraní API

Předplatné rozhraní API podporuje následující operace HTTPS: **Získat**, **příspěvek**, **opravy**, a **odstranit**.


#### <a name="list-subscriptions"></a>Seznam předplatných

Zobrazí všechny odběry SaaS pro vydavatele.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Získat<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametry dotazu:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Verze operace pro tento požadavek.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
| x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
| authorization      |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kódy odpovědí:*

Kód: 200 <br/>
Získá vydavatele a odpovídajících předplatných všechny vydavatele, nabídky, na základě tokenu ověřování.
Vrácená data:<br>

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
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Token pro pokračování bude k dispozici pouze v případě, že existují další výraz "stránky" plánů pro načtení. 

Kód: 403 <br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele. 

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

#### <a name="get-subscription"></a>Získat předplatné

Získá určený odběr SaaS. Pomocí tohoto volání můžete získat informace o licencích a informacemi o plánu.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení token prostřednictvím rozhraní API vyřešit.   |
|  ApiVersion        |   Verze operace pro tento požadavek.   |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  authorization     |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Kódy odpovědí:*

Kód: 200<br>
Získá předplatné SaaS z identifikátoru. Vrácená data:<br>

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
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kód: 403<br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele.

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

Pomocí tohoto volání můžete zjistit, jestli jsou všechny privátní nebo veřejné nabídky pro aktuální vydavatele.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Verze operace pro tento požadavek.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  x-ms-correlationid  | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  authorization     |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Kódy odpovědí:*

Kód: 200<br>
Získá seznam dostupných plánů pro zákazníka. Text odpovědi:

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
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele. <br> 

Kód: 500<br>
Vnitřní chyba serveru.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Aktivovat předplatné

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Příspěvek<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  x-ms-correlationid  | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento řetězec koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  authorization     |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Datová část požadavku:*

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
Chybná žádost: selhalo ověřování.

Kód: 403<br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele.

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

#### <a name="change-the-plan-on-the-subscription"></a>Změnit plán v rámci předplatného

Aktualizujte plán v rámci předplatného.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Oprava<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  x-ms-correlationid  |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.    |
| authorization      |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Datová část požadavku:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operace umístění | Odkaz na prostředek se získat stav operace.   |

*Kódy odpovědí:*

Kód: 202<br>
Byl přijat požadavek na změnu plánu. Partner má dotazovat umístění operace určit úspěch nebo neúspěch. <br>

Kód: 400<br>
Chybná žádost: selhalo ověřování.

Kód: 403<br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele.

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
>Patched najednou, nikoli oba současně, může být pouze plán nebo množství. Upraví na předplatné s **aktualizace** není v `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Změnit množství na předplatné

Aktualizujte množství u daného předplatného.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Oprava:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  x-ms-correlationid  |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.    |
| authorization      |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Datová část požadavku:*

```json
Request Body:
{
    "quantity": 5
}
```

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operace umístění | Odkaz na prostředek se získat stav operace.   |

*Kódy odpovědí:*

Kód: 202<br>
Byl přijat požadavek na změnu množství. Partner má dotazovat umístění operace určit úspěch nebo neúspěch. <br>

Kód: 400<br>
Chybná žádost: selhalo ověřování.


Kód: 403<br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele.

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
>Patched najednou, nikoli oba současně, může být pouze plán nebo množství. Upraví na předplatné s **aktualizace** není v `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Odstranění předplatného

Odhlásit a odstranit zadaný odběr.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Odstranění<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.   |
|  x-ms-correlationid  |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.   |
|  authorization     |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kódy odpovědí:*

Kód: 202<br>
Partner inicializovalo volání, chcete-li odhlásit odběr SaaS.<br>

Kód: 400<br>
Odstranit v rámci předplatného pomocí **odstranit** není v `allowedCustomerOperations`.

Kód: 403<br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele.

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


### <a name="operations-api"></a>Operace rozhraní API

Operace rozhraní API podporuje následující operace opravy a Get.

#### <a name="list-outstanding-operations"></a>Seznam zbývajících operací. 

Vypíše zbývající operace pro aktuální vydavatele. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametry dotazu:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Verze operace pro tento požadavek.                |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  authorization     |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kódy odpovědí:*

Kód: 200<br> Získá seznam čekajících operací v rámci předplatného. Vrácená data:

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
Chybná žádost: selhalo ověřování.

Kód: 403<br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele.

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

#### <a name="get-operation-status"></a>Načíst stav operace

Umožňuje vydavatele a sledovat stav zadaného aktivovaných asynchronní operace (například `subscribe`, `unsubscribe`, `changePlan`, nebo `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace pro tento požadavek.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  authorization     |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Kódy odpovědí:*<br>

Kód: 200<br> Získá zadaný nevyřízenou operaci SaaS. Vrácená data:

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
Chybná žádost: selhalo ověřování.

Kód: 403<br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele.
 
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
#### <a name="update-the-status-of-an-operation"></a>Aktualizovat stav operace

Aktualizujte stav operace indikuje úspěch nebo selhání pomocí zadané hodnoty.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Oprava<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Verze operace pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |
|  operationId       | Operace, která se dokončuje. |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  authorization     |  [Získání tokenu JSON web token (JWT) nosiče](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Datová část požadavku:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Kódy odpovědí:*

Kód: 200<br> Volání k informování o dokončení operace na straně partnera. Tato odpověď může například signalizovat změnu licence nebo plány.

Kód: 400<br>
Chybná žádost: selhalo ověřování.

Kód: 403<br>
Neoprávněný přístup. Ověřovací token nebylo zadáno nebo je neplatný nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuální vydavatele.

Kód: 404<br>
Nebyl nalezen.

Kód: 409<br>
Ke konfliktu. Například novější transakce je již splněny.

Kód: 500<br> Vnitřní chyba serveru.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementace webhook ve službě SaaS

Vydavatel musí implementovat webhooku v této službě SaaS k proaktivně upozorní uživatele na změny ve své službě. Očekává se, že služba SaaS volají operace rozhraní API k ověření a autorizaci před provedením akce na oznámení webhooku.

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Kde akce může být jeden z následujících akcí: 
- `subscribe` (když prostředek se aktivovala.)
- `unsubscribe` (Pokud prostředek se odstranil)
- `changePlan` (po operaci změnit plán dokončení)
- `changeQuantity` (Pokud změna množství dokončení operace)
- `suspend` (když prostředků bylo pozastaveno)
- `reinstate` (když prostředků má byly obnoveny po pozastavení se vaše)

Kde stav může být jeden z následujících akcí: 
- **NotStarted** <br>
 - **Probíhá zpracování** <br>
- **Bylo úspěšně dokončeno** <br>
- **Se nezdařilo** <br>
- **Konflikt** <br>

V oznámení webhooku užitečné stavy jsou buď **Succeeded** a **neúspěšné**. Operace životního cyklu pochází z **NotStarted** do konečného stavu, jako je **Succeeded**, **neúspěšné**, nebo **konflikt**. Pokud se zobrazí **NotStarted** nebo **InProgress**, požádat o stav přes získat rozhraní API, dokud operace přejde do konečného stavu před provedením akce i nadále. 

## <a name="mock-apis"></a>Napodobení rozhraní API

Vám pomůže naše mock rozhraní API vám pomůžou začít s vývojem, zejména při vytváření prototypů, projekty a testování. 

Hostování koncový bod: `https://marketplaceapi.microsoft.com/api` (ověření není vyžadováno)<br/>
Verze rozhraní API: `2018-09-15`<br/>
Vzorek identifikátoru URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Cesty koncový bod rozhraní API jsou stejné ve mock a skutečné rozhraní API, ale verze rozhraní API se liší. Verze je `2018-09-15` pro verzi mock a `2018-08-31` pro produkční verzi. 

Některé z volání rozhraní API v tomto článku provádět mock hostitele koncového bodu. Obecně můžete očekávat mock dat zpět jako odpověď. Volání metody předplatného aktualizace pomocí mock rozhraní API vrací vždy 500. 

## <a name="next-steps"></a>Další postup

Vývojáři také programově načíst a manipulace s úlohy, nabídky a vydavatel profilů pomocí [Cloud Partner Portal, rozhraní REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).

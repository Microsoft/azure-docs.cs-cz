---
title: Rozhraní API pro plnění SaaS v2 | Azure Marketplace
description: Tento článek vysvětluje, jak vytvořit a spravovat nabídku SaaS pro AppSource a Azure Marketplace s použitím přidružených rozhraní API pro splnění verze v2.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 276699b9316a0c4fd428038f2c967bdf934f449c
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016045"
---
# <a name="saas-fulfillment-apis-version-2"></a>Rozhraní API pro splnění SaaS verze 2 

Tento článek podrobně popisuje rozhraní API, které umožňuje partnerům prodávat své aplikace SaaS na webu AppSource Marketplace a v Azure Marketplace. Tato rozhraní API jsou požadavkem pro SaaS nabídky AppSource a Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Správa životního cyklu předplatného SaaS

Azure SaaS spravuje celý životní cyklus nákupu předplatného SaaS. Používá rozhraní API pro plnění jako mechanismus pro řízení skutečného plnění, změny plánů a odstraňování předplatného partnera. Faktura zákazníka vychází ze stavu předplatného SaaS, které Microsoft udržuje. Následující diagram znázorňuje stavy a operace, které určují změny mezi stavy.

![Stavy životního cyklu předplatného SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stavy předplatného SaaS

V následující tabulce jsou uvedeny stavy zřizování pro předplatné SaaS, včetně popisu a sekvenčního diagramu pro každý (Pokud je k dispozici). 

#### <a name="provisioning"></a>Zřizování

Když zákazník zahájí nákup, partner obdrží tyto informace v autorizačním kódu na interaktivní webové stránce zákazníka, která používá parametr adresy URL. Příkladem je `https://contoso.com/signup?token=..`, že adresa URL cílové stránky v partnerském centru `https://contoso.com/signup`je. Autorizační kód lze ověřit a vyměňovat pro podrobnosti o službě zřizování voláním rozhraní API pro řešení.  Když se dokončí zřizování služby SaaS, pošle volání metody Activate signalizaci, že se plnění dokončilo, a zákazník se může účtovat. 

Následující diagram znázorňuje posloupnost volání rozhraní API pro scénář zřizování.  

![Volání rozhraní API pro zřizování služby SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Zajištěno

Tento stav je stabilním stavem zřízené služby.

##### <a name="provisioning-for-update"></a>Zřizování pro aktualizaci 

Tento stav znamená, že probíhá aktualizace existující služby. Tuto aktualizaci může iniciovat zákazník, ať už z webu Marketplace nebo služby SaaS (jenom pro transakce z přímých na zákazníka).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Zřizování pro aktualizaci (při zahájení z Marketplace)

Následující diagram znázorňuje posloupnost akcí při zahájení aktualizace z webu Marketplace.

![Volání rozhraní API při zahájení aktualizace z Marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Zřizování pro aktualizaci (při zahájení ze služby SaaS)

Následující diagram znázorňuje akce při zahájení aktualizace ze služby SaaS. (Volání Webhooku se nahradí aktualizací předplatného iniciované službou SaaS.) 

![Volání rozhraní API při zahájení aktualizace ze služby SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Pozastaveno

Tento stav indikuje, že platba zákazníka nebyla přijata. Podle zásad poskytneme období odkladu pro zákazníka před zrušením předplatného. Když je předplatné v tomto stavu: 

- Jako partner se můžete rozhodnout snížit nebo zablokovat přístup uživatele ke službě.
- Odběr musí být uložený v obnovitelném stavu, který může obnovit všechny funkce bez ztráty dat nebo nastavení. 
- Po skončení období odkladu se očekává, že získáte žádost o obnovení pro toto předplatné prostřednictvím rozhraní API pro splnění nebo žádosti o zrušení zřízení. 

#### <a name="unsubscribed"></a>Odběr se odhlásil 

Předplatná dosáhnou tohoto stavu v reakci na výslovný požadavek zákazníka nebo na nedoplatky poplatků. Od partnera se očekává, že se data zákazníka uchovávají pro obnovení na vyžádání po určitý počet dnů a pak se odstraní. 


## <a name="api-reference"></a>API – referenční informace

Tato část popisuje *rozhraní API* pro SaaS odběr a *rozhraní Operations API*.  Hodnota `api-version` parametru pro rozhraní API verze 2 je `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definice parametrů a entit

V následující tabulce jsou uvedeny definice běžných parametrů a entit používaných rozhraními API pro plnění.

|     Entita/parametr     |     Definice                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identifikátor GUID pro prostředek SaaS.  |
| `name`                   | Popisný název, který pro tento prostředek zadal zákazník. |
| `publisherId`            | Jedinečný identifikátor řetězce pro každého vydavatele (například: contoso). |
| `offerId`                | Jedinečný identifikátor řetězce pro každou nabídku (například: "offer1").  |
| `planId`                 | Jedinečný identifikátor řetězce pro každý plán/SKU (například: "stříbrné"). |
| `operationId`            | Identifikátor GUID konkrétní operace.  |
|  `action`                | Akce prováděná na prostředku, buď `unsubscribe` `suspend`,, `reinstate`nebo `changePlan`, `changeQuantity`, `transfer`.  |
|   |   |

Globálně jedinečné identifikátory ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) jsou 128 (32 – šestnáctková) čísel, která se obvykle generují automaticky. 

#### <a name="resolve-a-subscription"></a>Vyřešit předplatné 

Koncový bod vyřešení umožňuje vydavateli přeložit token Marketplace na trvalé ID prostředku. ID prostředku je jedinečný identifikátor předplatného SaaS. Když se uživatel přesměruje na web partnera, adresa URL obsahuje token v parametrech dotazu. U partnera se očekává, že tento token použijete a vyžádáte ho k vyřešení. Odpověď obsahuje jedinečné ID předplatného SaaS, název, ID nabídky a plán pro daný prostředek. Tento token je platný jenom pro jednu hodinu. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Publikovat<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která se má použít pro tento požadavek.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json` |
|  x-ms-requestid    |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  authorization     |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Například: "`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  Parametr dotazu tokenu v adrese URL, když se uživatel přesměruje na web partnera SaaS z Azure (například: `https://contoso.com/signup?token=..`). *Poznámka:* Adresa URL překóduje hodnotu tokenu z prohlížeče před jeho použitím.  |

*Kódy odpovědí:*

Kód: 200<br>
Vyřeší neprůhledný token na SaaS předplatné. Tělo odpovědi:
 

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
Chybný požadavek. x-MS-Marketplace – token chybí, je poškozený nebo vypršela jeho platnost.

Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli.

Kód: 404<br>
Nenalezeno

Kód: 500<br>
Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>Rozhraní API pro předplatné

Rozhraní API pro předplatné podporuje následující operace HTTPS: **Získat**,vystavit, **opravit**a **Odstranit**.


#### <a name="list-subscriptions"></a>Seznam předplatných

Zobrazí seznam všech předplatných SaaS pro vydavatele.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Získat<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametry dotazu:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Verze operace, která se má použít pro tento požadavek.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
| Typ obsahu       |  `application/json`  |
| x-ms-requestid     |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
| x-ms-correlationid |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
| authorization      |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Například: "`Bearer <access_token>`".  |

*Kódy odpovědí:*

Kód: 200 <br/>
Získá vydavatele a odpovídající odběry pro všechny nabídky vydavatele na základě ověřovacího tokenu.
Datová část odpovědi:<br>

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
          "isFreeTrial": "true", // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Token pokračování bude přítomen pouze v případě, že existují další "stránky" plánů k načtení. 

Kód: 403 <br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli. 

Kód: 500<br>
Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Získat předplatné

Získá zadané předplatné SaaS. Pomocí tohoto volání získáte informace o licenci a informace o plánu.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu prostřednictvím řešení API.   |
|  ApiVersion        |   Verze operace, která se má použít pro tento požadavek.   |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      |  `application/json`  |
|  x-ms-requestid    |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  authorization     |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Například: "`Bearer <access_token>`".  |

*Kódy odpovědí:*

Kód: 200<br>
Získá předplatné SaaS z identifikátoru. Datová část odpovědi:<br>

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
        "isFreeTrial": "true", // true – customer subscription is currently in free trial, false – customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli.

Kód: 404<br>
Nenalezeno<br> 

Kód: 500<br>
Vnitřní chyba serveru<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Výpis dostupných plánů

Pomocí tohoto volání zjistíte, jestli pro aktuálního vydavatele existují soukromé nebo veřejné nabídky.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Verze operace, která se má použít pro tento požadavek.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     |  `application/json` |
|   x-ms-requestid   |   Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  x-ms-correlationid  | Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  authorization     |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například: "`Bearer <access_token>`". |

*Kódy odpovědí:*

Kód: 200<br>
Načte seznam dostupných plánů pro zákazníka. Tělo odpovědi:

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
Nenalezeno<br> 

Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli. <br> 

Kód: 500<br>
Vnitřní chyba serveru<br>

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
|  ApiVersion        |  Verze operace, která se má použít pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json`  |
|  x-ms-requestid    | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  x-ms-correlationid  | Jedinečná řetězcová hodnota pro operaci na klientovi. Tento řetězec koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  authorization     |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například: "`Bearer <access_token>`". |

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
Chybný požadavek: Chyby ověření.

Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli.

Kód: 404<br>
Nenalezeno

Kód: 500<br>
Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Změna plánu předplatného

Aktualizujte plán v předplatném.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Oprava<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která se má použít pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json` |
|  x-ms-requestid    |   Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  x-ms-correlationid  |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.    |
| authorization      |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například: "`Bearer <access_token>`".  |

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
| Operace – umístění | Odkaz na prostředek, který získá stav operace.   |

*Kódy odpovědí:*

Kód: 202<br>
Požadavek na změnu plánu byl přijat. U partnera se očekává, že se bude dotazovat na umístění operace, aby bylo možné určit úspěch nebo neúspěch. <br>

Kód: 400<br>
Chybný požadavek: Chyby ověření.

Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli.

Kód: 404<br>
Nenalezeno

Kód: 500<br>
Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Současně lze opravit pouze plán nebo množství, nikoli obojí. Úpravy v předplatném s **aktualizací** nejsou `allowedCustomerOperations`v.

#### <a name="change-the-quantity-on-the-subscription"></a>Změna množství v předplatném

Aktualizujte množství v předplatném.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Použita<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která se má použít pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json` |
|  x-ms-requestid    |   Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  x-ms-correlationid  |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.    |
| authorization      |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například: "`Bearer <access_token>`".  |

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
| Operace – umístění | Připojte se k prostředku, abyste získali stav operace.   |

*Kódy odpovědí:*

Kód: 202<br>
Žádost o změnu množství byla přijata. U partnera se očekává, že se bude dotazovat na umístění operace, aby bylo možné určit úspěch nebo neúspěch. <br>

Kód: 400<br>
Chybný požadavek: Chyby ověření.


Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli.

Kód: 404<br>
Nenalezeno

Kód: 500<br>
Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Současně lze opravit pouze plán nebo množství, nikoli obojí. Úpravy v předplatném s **aktualizací** nejsou `allowedCustomerOperations`v.

#### <a name="delete-a-subscription"></a>Odstraní předplatné

Zruší odběr a odstraní zadané předplatné.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Odstranění<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která se má použít pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     |  `application/json` |
|  x-ms-requestid    |   Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.   |
|  x-ms-correlationid  |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.   |
|  authorization     |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například: "`Bearer <access_token>`".  |

*Kódy odpovědí:*

Kód: 202<br>
Partner inicioval volání odhlášení odběru předplatného SaaS.<br>

Kód: 400<br>
Odstraní se v rámci předplatného, `allowedCustomerOperations`které není v.

Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli.

Kód: 404<br>
Nenalezeno

Kód: 500<br>
Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Rozhraní API pro provoz

Rozhraní API operací podporuje následující operace patch a Get.

#### <a name="list-outstanding-operations"></a>Výpis nezpracovaných operací 

Zobrazí nedokončené operace pro aktuálního vydavatele. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametry dotazu:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Verze operace, která se má použít pro tento požadavek.                |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     |  `application/json` |
|  x-ms-requestid    |  Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  x-ms-correlationid |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  authorization     |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například: "`Bearer <access_token>`".  |

*Kódy odpovědí:*

Kód: 200<br> Získá seznam nevyřízených operací v rámci předplatného. Datová část odpovědi:

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
Chybný požadavek: Chyby ověření.

Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli.

Kód: 404<br>
Nenalezeno

Kód: 500<br>
Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Získat stav operace

Umožňuje vydavateli sledovat `subscribe`stav zadané aktivované asynchronní operace (například, `unsubscribe`, `changePlan`nebo `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Získat<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace, která se má použít pro tento požadavek.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      |  `application/json`   |
|  x-ms-requestid    |   Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  x-ms-correlationid |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi.  |
|  authorization     |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Například: "`Bearer <access_token>`".  |

*Kódy odpovědí:*<br>

Kód: 200<br> Načte zadanou probíhající operaci SaaS. Datová část odpovědi:

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
Chybný požadavek: Chyby ověření.

Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli.
 
Kód: 404<br>
Nenalezeno

Kód: 500<br> Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Aktualizace stavu operace

Aktualizuje stav operace, aby označovala úspěch nebo neúspěch se zadanými hodnotami.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Oprava<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Verze operace, která se má použít pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, který se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |
|  operationId       | Operace, která je dokončena. |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     | `application/json`   |
|   x-ms-requestid   |   Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe identifikátor GUID. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
|  authorization     |  [Získat token nosiče webového tokenu JSON (Jwt)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Například: "`Bearer <access_token>`".  |

*Datová část požadavku:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Kódy odpovědí:*

Kód: 200<br> Výzva k informování o dokončení operace na straně partnera. Tato odpověď by například mohla signalizovat změnu míst nebo plánů.

Kód: 400<br>
Chybný požadavek: Chyby ověření.

Kód: 403<br>
Neautorizováno. Ověřovací token nebyl poskytnut nebo je neplatný nebo se pokouší o přístup k akvizici, který nepatří k aktuálnímu vydavateli.

Kód: 404<br>
Nenalezeno

Kód: 409<br>
Došlo. Například novější transakce je již splněna.

Kód: 500<br> Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementace Webhooku ve službě SaaS

Vydavatel musí implementovat Webhook v této službě SaaS, aby proaktivně upozornil uživatele na změny ve své službě. Očekává se, že služba SaaS před provedením akce v oznámení Webhooku zavolá rozhraní API operací k ověření a autorizaci.

Aby bylo zajištěno zabezpečení komunikace, společnost Microsoft jako součást volání zahrnuje token Azure Active Directory JWT v autorizační hlavičce. Poskytovatelé SaaS jsou doporučováni k ověření tokenu JWT, jak je popsáno v článku [Microsoft Identity Platform Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) , aby bylo zajištěno, že budou přijímána pouze platná volání.

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
Kde může být akce jedna z následujících: 
- `unsubscribe`(při odstranění prostředku)
- `changePlan`(po dokončení operace změnit plán)
- `changeQuantity`(po dokončení operace změny množství)
- `suspend`(pokud byl prostředek pozastaven)
- `reinstate`(pokud byl prostředek obnoven po pozastavení)

Kde může být stav jedna z následujících: 
- **NotStarted** <br>
 - **InProgress** <br>
- **Úspěchu** <br>
- **Se nezdařilo** <br>
- **Došlo** <br>

V oznámení Webhooku jsou možné stavy **úspěšné** a neúspěšné. Životní cyklus operace je od **NotStarted** do stavu terminálu, jako je **úspěch**, **Chyba**nebo **konflikt**. Pokud obdržíte **NotStarted** nebo InProgress, pokračujte v žádosti o stav prostřednictvím rozhraní Get API, dokud operace nedosáhne stavu terminálu před provedením akce. 

## <a name="mock-apis"></a>Rozhraní API pro návrhy

Můžete použít naše rozhraní API, které vám pomohou začít s vývojem, zejména vytváření prototypů a testování projektů. 

Koncový bod hostitele `https://marketplaceapi.microsoft.com/api` : (není vyžadováno ověřování)<br/>
Verze rozhraní API:`2018-09-15`<br/>
Vzorový identifikátor URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Cesty koncových bodů rozhraní API jsou stejné napříč modely i skutečnými rozhraními API, ale verze rozhraní API se liší. Verze je určena `2018-09-15` pro verzi v modelu a `2018-08-31` pro produkční verzi. 

Jakékoli volání rozhraní API v tomto článku se dá udělat na koncový bod hostitele s přípravou. Obecně se očekává, že se jako odpověď vrátí napodobná data. Volání metod aktualizace předplatného na rozhraní API pro návrhy vždy vrátí 500. 

## <a name="next-steps"></a>Další postup

Vývojáři můžou také programově načítat a manipulovat s úlohami, nabídkami a profily vydavatelů pomocí [rozhraní portál partnerů cloudu REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).

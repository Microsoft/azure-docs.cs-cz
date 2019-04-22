---
title: SaaS splnění rozhraní API V2 – Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak vytvořit nabídky SaaS na Azure Marketplace pomocí plnění přidružené rozhraní API V2.
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
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pbutlerm
ms.openlocfilehash: 437009079c1bebe3694aaa26f945bd726b3c9fb9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59010563"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS splnění rozhraní API verze 2 

Tento článek podrobně popisuje rozhraní API, která umožňuje nezávislí výrobci softwaru (ISV) můžete integrovat své aplikace SaaS pomocí Azure Marketplace. Toto rozhraní API umožňuje aplikace nezávislých výrobců softwaru se účastnit všech kanálů commerce povoleno: s přímým přístupem, vedené partnerem (prodejci) a vedla pole.  Toto rozhraní API je požadavek na výpis transactable SaaS nabídky na webu Azure Marketplace.


## <a name="managing-the-saas-subscription-lifecycle"></a>Správa životního cyklu předplatného SaaS

Microsoft SaaS Service spravuje celý životní cyklus nákupu předplatného SaaS a používá rozhraní API pro splnění jako mechanismus k řízení skutečné splnění změní na plány a odstranění předplatného se nezávislé výrobce softwaru. Zákazník se účtuje na základě stavu předplatného SaaS, které Microsoft udržuje. Následující diagram znázorňuje stavy a operace, které jednotky změny mezi stavy.

![Stavy životního cyklu předplatného SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stavy předplatného SaaS

V následující tabulce jsou uvedeny stavy zřizování pro předplatné SaaS, včetně popisu a pořadí diagram pro každou (pokud existuje). 

#### <a name="provisioning"></a>Zřizování

Když zákazník iniciuje nákup, ISV obdrží tyto informace v AuthCode na zákazníka interaktivní webové stránky s využitím parametr adresy URL. AuthCode můžete ověřit a vyměňují podrobnosti o co musí být zřízená.  Po dokončení zřizování služby SaaS odešle signál, že dokončení splnění a zákazníka může znamenat volání rozhraní aktivovat.  Následující diagram znázorňuje posloupnost volání rozhraní API pro zřizování scénář.  

![Volání rozhraní API pro zřizování služby SaaS.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Zajištěno

Tento stav se stálé zřízené služby.

#### <a name="provisioning-for-update"></a>Zřizování pro aktualizace
(z webu marketplace) 

Tento stav představuje aktualizaci do existující služby čeká na vyřízení. Tato aktualizace může vyvolat zákazníků na webu marketplace, nebo ve službě SaaS (pouze pro přímé zákazníky transakcí.) Následující diagram znázorňuje akce, když je aktualizace iniciované z marketplace.

![Volání rozhraní API při zahájení aktualizace z webu marketplace.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>Zřizování pro aktualizace  
(ze služby SaaS) 

Následující diagram znázorňuje akce při zahájení aktualizace ve službě SaaS. (Volání webhooku se nahrazuje aktualizace do předplatného, inicializuje služby SaaS. 

![Volání rozhraní API při zahájení aktualizace ve službě SaaS.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Pozastaveno

Tento stav označuje, že nebyl přijat platby zákazníků. Pomocí zásad budeme poskytovat období odkladu před unfulfilling předplatného zákazníka. Pokud je předplatné v tomto stavu: 

- Jako nezávislým výrobcem softwaru můžete snížit nebo blokovat přístup ke službě. 
- Předplatné musí uchovávat v obnovitelných stavu, ve kterém můžete obnovit všechny funkce bez ztráty dat nebo nastavení. 
- Můžete očekávat získat obnovit žádost o toto předplatné přes splnění rozhraní API nebo žádost o zrušení zřízení na konci období odkladu. 

#### <a name="unsubscribed"></a>Odběr se odhlásil 

Předplatná dosažení tohoto stavu v odpovědi na požadavek explicitní zákazníka nebo jako reakci na neuhrazení poplatků. Očekávání z ISV je, že zákaznická data se uchovávají po dobu obnovení v požadavku po dobu minimálně X dní a pak odstraní. 


## <a name="api-reference"></a>API – referenční informace

Tato část popisuje SaaS *rozhraní Subscription API* a *operace rozhraní API*.  Hodnota `api-version` parametr pro verzi 2 rozhraní API je `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definice parametru a entity

Následující tabulka obsahuje seznam definic pro společné parametry a entity používané v plnění rozhraní API.

|     Parametr/entity     |     Definice                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identifikátor GUID prostředku SaaS  |
| `name`                   | Popisný název pro tento prostředek k dispozici zákazníkem |
| `publisherId`            | Jedinečný řetězec identifikátor automaticky generovaný pro jednotlivé vydavatele, například "conotosocorporation" |
| `offerId`                | Jedinečný řetězec identifikátor automaticky generovaný pro každou nabídku, například "contosooffer1"  |
| `planId`                 | Jedinečný řetězec identifikátor automaticky generovaný pro každý plán nebo sku, například "contosobasicplan" |
| `operationId`            | Identifikátor GUID pro určitou operaci  |
|  `action`                | Akce prováděná s prostředkem, buď `subscribe`, `unsubscribe`, `suspend`, `reinstate`, nebo `changePlan`  |
|   |   |

Globálně jedinečné identifikátory ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) jsou čísla 128-bit (32 šestnáctkového čísla), která jsou obvykle automaticky generovány. 


### <a name="subscription-api"></a>Předplatné rozhraní API

Předplatné rozhraní API podporuje následující operace HTTPS: **Získat**, **příspěvek**, **opravy**, a **odstranit**.


#### <a name="list-subscriptions"></a>Seznam předplatných

Zobrazí všechny odběry SaaS pro vydavatele.

**Získáte:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Parametry dotazu:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  Verze operace pro tento požadavek.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
| Typ obsahu       |  `application/json`  |
| x-ms-requestid     |  Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
| x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
| Autorizace      |  JSON web token (JWT) nosný token.  |

*Kódy odpovědí:*

Kód: 200<br>
Podle ověřovací token, získejte vydavatele a odpovídajících předplatných pro všechny vydavatele, nabídky.<br> Vrácená data:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "cont-cld-tier2",
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
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Token pro pokračování bude pouze k dispozici, pokud existují další výraz "stránky" plánů pro načtení. 


Kód: 403 <br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele. 

Kód: 500 – Interní chyba serveru

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

**Získáte:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Jedinečný identifikátor SaaS předplatné, která se získá po vyřešení token prostřednictvím řešení rozhraní API   |
|  ApiVersion        |   Verze operace k použití pro tuto žádost   |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      |  `application/json`  |
|  x-ms-requestid    |  Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  Autorizace     |  JSON web token (JWT) nosný token  |

*Kódy odpovědí:*

Kód: 200<br>
Získá předplatné SaaS z identifikátoru<br> Vrácená data:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "cont-cld-tier2",
        "planId": "silver",
        "quantity": "10"",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Kód: 404<br>
Nenalezené<br> 

Kód: 403<br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele.

Kód: 500<br>
Vnitřní chyba serveru<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Seznam dostupných plánů

Pomocí tohoto volání můžete zjistit, jestli jsou všechny nabídky private/public pro aktuálního uživatele.

**Získáte:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Verze operace k použití pro tuto žádost  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     |  `application/json` |
|   x-ms-requestid   |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  x-ms-correlationid  | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  Autorizace     |  JSON web token (JWT) nosný token |

*Kódy odpovědí:*

Kód: 200<br>
Získáte seznam dostupných plánů pro zákazníka.<br>

Text odpovědi:

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
Nenalezené<br> 

Kód: 403<br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele. <br> 

Kód: 500<br>
Vnitřní chyba serveru<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="resolve-a-subscription"></a>Vyřešit předplatné 

Koncový bod řešení umožňuje uživatelům vyřešit marketplace token trvalého ID prostředku. ID prostředku je jedinečný identifikátor pro předplatné SAAS.  Když uživatel se přesměruje na web nezávislých výrobců softwaru, adresa URL obsahuje token v parametry dotazu. Výrobci má používat tento token a požádat o jeho vyřešení. Odpověď obsahuje ID předplatného SAAS jedinečný název, ID nabídky a plán pro prostředek. Tento token je platný pouze jedna hodina. 

**Příspěvek:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace k použití pro tuto žádost  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json` |
|  x-ms-requestid    |  Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  Autorizace     |  JSON web token (JWT) nosný token  |
|  x-ms-marketplace-token  |  Parametr token dotazu v adrese URL, když je uživatel přesměrován na SaaS ISV webu z Azure. *Poznámka:* Adresa URL dekóduje hodnoty tokenu z prohlížeče před jeho použitím. |

*Kódy odpovědí:*

Kód: 200<br>
Přeloží neprůhledné token k odběru služby SaaS.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "cont-cld-tier2",
    "planId": "silver",
    "quantity": "20",
    "operationId": "<guid>"  
}
```

Kód: 404<br>
Nenalezené

Kód: 400<br>
Chybná žádost o ověření chyb

Kód: 403<br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele.

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

#### <a name="activate-a-subscription"></a>Aktivovat předplatné

**Příspěvek:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace k použití pro tuto žádost  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json`  |
|  x-ms-requestid    | Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  x-ms-correlationid  | Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento řetězec koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  Autorizace     |  JSON web token (JWT) nosný token |

*Žádost:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Kódy odpovědí:*

Kód: 202<br>
Aktivuje předplatné.<br>

Kód: 404<br>
Nenalezené

Kód: 400<br>
Chybná žádost o ověření chyb

Kód: 403<br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele.

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

#### <a name="update-a-subscription"></a>Aktualizace předplatného

Aktualizovat nebo změnit plán předplatného se zadaným hodnotám.

**Oprava:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      | `application/json` |
|  x-ms-requestid    |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  x-ms-correlationid  |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.    |
| Autorizace      |  JSON web token (JWT) nosný token.  |

*Datová část požadavku:*

```json
Request Body:
{
    "planId": "gold",
    "quantity": ""
}
```

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operace umístění | Odkaz na prostředek se získat stav operace.   |

*Kódy odpovědí:*

Kód: 202<br>
Nezávislý výrobce softwaru inicializuje změnit plán nebo množství změn. <br>

Kód: 404<br>
Nenalezené

Kód: 400<br>
Chybná žádost o ověření chyby.

>[!Note]
>Patched najednou, nikoli oba současně, může být pouze plán nebo množství. Upraví na předplatné s **aktualizace** není v `allowedCustomerOperations`.

Kód: 403<br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele.

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

#### <a name="delete-a-subscription"></a>Odstraní předplatné

Odhlásit a odstranit zadaný odběr.

**Odstraňte:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     |  `application/json` |
|  x-ms-requestid    |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.   |
|  x-ms-correlationid  |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.   |
|  Autorizace     |  JSON web token (JWT) nosný token.   |

*Kódy odpovědí:*

Kód: 200<br>
Nezávislý výrobce softwaru iniciované volání k označení zrušit odběr v rámci předplatného SaaS.<br>

Kód: 404<br>
Nenalezené

Kód: 400<br>
Odstranit v rámci předplatného pomocí **odstranit** není v `allowedCustomerOperations`.

Kód: 403<br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele.

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


### <a name="operations-api"></a>Operace rozhraní API

Operace rozhraní API podporuje následující operace opravy a Get.


#### <a name="update-a-subscription"></a>Aktualizace předplatného

Aktualizujte odběr zadané hodnoty.

**Oprava:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  Verze operace pro tento požadavek.  |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |
|  operationId       | Operace, která se dokončuje. |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     | `application/json`   |
|   x-ms-requestid   |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi. |
|  Autorizace     |  JSON web token (JWT) nosný token.  |

*Datová část požadavku:*

```json
{
    "planId": "cont-cld-tier2",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}
```

*Kódy odpovědí:*

Kód: 200<br> Volání za účelem informování o dokončení operace na straně nezávislý výrobce softwaru. Tuto odpověď může například signalizovat změnu licence/plány.

Kód: 404<br>
Nenalezené

Kód: 400<br>
Chybná žádost o ověření chyb

Kód: 403<br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele.

Kód: 409<br>
Ke konfliktu. Například je novější transakce již splněny

Kód: 500<br> Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="list-outstanding-operations"></a>Seznam zbývajících operací. 

Vypíše zbývající operace pro aktuálního uživatele. 

**Získáte:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Parametry dotazu:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   Verze operace pro tento požadavek.                |
| subscriptionId     | Jedinečný identifikátor předplatného SaaS, která se získá po vyřešení tokenu pomocí rozhraní API pro řešení.  |

*Hlavičky žádosti:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Typ obsahu     |  `application/json` |
|  x-ms-requestid    |  Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  Autorizace     |  JSON web token (JWT) nosný token.  |

*Kódy odpovědí:*

Kód: 200<br> Získá seznam čekajících operací v rámci předplatného.<br>
Vrácená data:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "cont-cld-tier2",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```

Kód: 404<br>
Nenalezené

Kód: 400<br>
Chybná žádost o ověření chyb

Kód: 403<br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele.

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

#### <a name="get-operation-status"></a>Načíst stav operace

Umožňuje sledovat stav zadaného aktivovaných asynchronní operace (přihlásit k odběru nebo zrušení odběru/změnit plán).

**Získáte:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parametry dotazu:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Verze operace pro tento požadavek.  |

*Hlavičky žádosti:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Typ obsahu      |  `application/json`   |
|  x-ms-requestid    |   Jedinečnou hodnotu řetězce pro sledování žádosti z klienta, pokud možno identifikátor GUID. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  x-ms-correlationid |  Jedinečnou hodnotu řetězce pro operaci na straně klienta. Tento parametr koreluje všech událostí z operace klienta s událostmi na straně serveru. Pokud tuto hodnotu nezadáte, jeden se vygeneruje a k dispozici v hlavičkách odpovědi.  |
|  Autorizace     | JSON web token (JWT) nosný token.  |

*Kódy odpovědí:* Kód: 200<br> Získá zadaný nevyřízenou operaci SaaS<br>
Vrácená data:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "cont-cld-tier2",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Kód: 404<br>
Nenalezené

Kód: 400<br>
Chybná žádost o ověření chyb

Kód: 403<br>
Neautorizováno. Neposkytl se ověřovací token, je neplatný, nebo požadavek se pokouší o přístup akvizice, který nepatří do aktuálního uživatele.
 
Kód: 500<br> Vnitřní chyba serveru

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Webhook ve službě SaaS

Vydavatel musí implementovat webhooku v této službě SaaS k proaktivně upozorní uživatele na změny ve své službě. Rozhraní API očekává neověřené a bude volat služby v rámci Microsoft SaaS. Očekává se, že služba SaaS volají operace rozhraní API k ověření a autorizaci před provedením akce na oznámení webhooku.

```json
{
    "operationId": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "cont-cld-tier2",
    "publisherId": "contoso",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Activate",   // Activate/Delete/Suspend/Reinstate/Change[new]  
    "timeStamp": "2018-12-01T00:00:00"
}

```


## <a name="mock-api"></a>Napodobení rozhraní API

Vám pomůže naše mock rozhraní API vám pomůžou začít s vývojem, zejména při vytváření prototypů a testování projektů. 

Hostitel koncového bodu: `https://marketplaceapi.microsoft.com/api` Verze rozhraní API: `2018-09-15` Ukázkový identifikátor Uri není nutné žádné ověření: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15`

Některé z volání rozhraní API v tomto článku provádět mock hostitele koncového bodu. Můžete očekávat při získávání mock dat zpět jako odpověď.


## <a name="next-steps"></a>Další postup

Můžete také programově načítat vývojáři a manipulaci s úlohami, nabídky a vydavatel profilů pomocí [Cloud Partner Portal, rozhraní REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).

---
title: Rozhraní API služby měření – komerční tržiště Microsoftu
description: Rozhraní API události využití umožňuje generovat události využití pro nabídky SaaS v Microsoft AppSource a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b75964f8cfc41efc35858284dbffded3aa406eb6
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436058"
---
# <a name="marketplace-metered-billing-apis"></a>Rozhraní API pro fakturaci měření na webu Marketplace

Rozhraní API měřeného účtování by se měla použít, když Vydavatel vytvoří vlastní rozměry měření pro nabídku, která se má publikovat v partnerském centru. Pro všechny koupené nabídky, které mají jeden nebo více plánů s vlastními dimenzemi k vygenerování událostí využití, se vyžaduje integrace s použitím rozhraní API pro účtované účtování.

Další informace o vytváření vlastních dimenzí měření pro SaaS najdete v tématu [SaaS měřených faktur](saas-metered-billing.md).

Další informace o vytváření vlastních dimenzí měření pro nabídku aplikací Azure s plánem spravované aplikace najdete v tématu [konfigurace Azure Application Offer Details Setup](../create-new-azure-apps-offer.md#configure-your-azure-application-offer-setup-details).

## <a name="enforcing-tls-12-note"></a>Poznámka k vynucení TLS 1,2

Verze TLS verze 1,2 se vynutila jako minimální verze komunikace pomocí protokolu HTTPS. Ujistěte se, že ve svém kódu používáte tuto verzi protokolu TLS. TLS verze 1,0 a 1,1 jsou zastaralé a pokusy o připojení budou odmítnuty.

## <a name="metered-billing-single-usage-event"></a>Událost jednorázového účtování s měřením využití

Rozhraní API události využití by mělo volat Vydavatel, aby vygeneroval události využití proti aktivnímu prostředku (odebíranému) pro plán zakoupený konkrétním zákazníkem. Událost použití je vyvolána samostatně pro každou vlastní dimenzi plánu definovaného vydavatelem při publikování nabídky.

Pro každou hodinu kalendářního dne se dá vygenerovat jenom jedna událost použití. Například na 8:5:15 dnes můžete vygenerovat jednu událost použití. Pokud se tato událost přijme, bude se v současnosti akceptovat další událost použití od 9:00. Pokud odešlete další událost mezi 8:15 a 8:59:59 v dnešním dni, bude odmítnuta jako duplicitní. Měli byste nashromáždit všechny jednotky spotřebované za hodinu a pak je vygenerovat v rámci jedné události.

Pro každou hodinu kalendářního dne a prostředku se dá vygenerovat jenom jedna událost použití. Pokud se více než jedna jednotka spotřebovává za hodinu, pak se nashromáždí všechny spotřebované jednotky za hodinu a pak se vygeneruje v jedné události. Události využívání se dají vygenerovat jenom za posledních 24 hodin. Pokud událost využití vygenerujete kdykoli mezi 8:00 a 8:59:59 (a je přijata) a poslat další událost pro stejný den mezi 8:00 a 8:59:59, bude odmítnuta jako duplicitní.

**Příspěvek**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametry dotazu:*

| Parametru | Doporučení          |
| ---------- | ---------------------- |
| `ApiVersion` | Použijte 2018-08-31. |
| | |

*Hlavičky žádosti:*

| Typ obsahu       | Použití `application/json`  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe pro identifikátor GUID. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `x-ms-correlationid` | Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `authorization`   | Jedinečný přístupový token, který identifikuje ISV, který provádí toto volání rozhraní API. Formát je v `"Bearer <access_token>"` případě, že je hodnota tokenu načtena vydavatelem, jak je vysvětleno pro <br> <ul> <li> SaaS v [získání tokenu pomocí HTTP POST](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Spravovaná aplikace v [strategiích ověřování](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |

*Příklad textu žádosti:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` má různý význam pro SaaS aplikaci a pro spravovanou aplikaci, která vysílá vlastní měřič. 

Pro plány aplikací spravovaných aplikací Azure `resourceId` je to spravovaná aplikace `resource group Id` . Ukázkový skript pro načtení najdete v [části použití tokenu identity spravovaného službou Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

V případě nabídek SaaS `resourceId` je to ID předplatného SaaS. Další podrobnosti o předplatných SaaS najdete v tématu [seznam předplatných](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Odpovědi

Kód: 200<br>
OK. Emise využití se přijaly a zaznamenaly na straně Microsoftu k dalšímu zpracování a fakturaci.

Příklad datové části odpovědi: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Kód: 400 <br>
Chybný požadavek.

* Byla zadána chybějící nebo neplatná data žádosti.
* `effectiveStartTime` je v minulosti více než 24 hodin. Vypršela platnost události.
* Předplatné SaaS není ve stavu odebíraného.

Příklad datové části odpovědi: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kód: 403<br>

Zakázán. Autorizační token není zadaný, je neplatný nebo vypršela jeho platnost.  Nebo se požadavek pokouší o přístup k předplatnému pro nabídku, která byla publikována s jiným ID Aplikace Azure AD z verze, která byla použita k vytvoření autorizačního tokenu.

Kód: 409<br>
Došlo. Událost použití již byla úspěšně hlášena pro zadané ID prostředku, efektivní datum využití a hodinu.

Příklad datové části odpovědi: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Událost využití dávky měřených faktur

Rozhraní API události využití dávky umožňuje generovat události využití pro více než jeden koupený prostředek najednou. Umožňuje také generovat několik událostí použití pro stejný prostředek, pokud jsou pro různé kalendářní hodiny. Maximální počet událostí v jedné dávce je 25.

**Příspěvek:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametry dotazu:*

| Parametr  | Doporučení     |
| ---------- | -------------------- |
| `ApiVersion` | Použijte 2018-08-31. |

*Hlavičky žádosti:*

| Typ obsahu       | Použití `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Jedinečná řetězcová hodnota pro sledování požadavku z klienta, nejlépe pro identifikátor GUID. Pokud tato hodnota není zadána, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `x-ms-correlationid` | Jedinečná řetězcová hodnota pro operaci na klientovi. Tento parametr koreluje všechny události z klientské operace s událostmi na straně serveru. Pokud tato hodnota není k dispozici, bude vygenerována a uvedena v hlavičkách odpovědi. |
| `authorization`      | Jedinečný přístupový token, který identifikuje ISV, který provádí toto volání rozhraní API. Formát je v `Bearer <access_token>` případě, že je hodnota tokenu načtena vydavatelem, jak je vysvětleno pro <br> <ul> <li> SaaS v [získání tokenu pomocí HTTP POST](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Spravovaná aplikace v [strategiích ověřování](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |


*Příklad textu žádosti:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` má různý význam pro SaaS aplikaci a pro spravovanou aplikaci, která vysílá vlastní měřič. 

Pro plány aplikací spravovaných aplikací Azure `resourceId` je to spravovaná aplikace `resource group Id` . Ukázkový skript pro načtení najdete v [části použití tokenu identity spravovaného službou Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

V případě nabídek SaaS `resourceId` je to ID předplatného SaaS. Další podrobnosti o předplatných SaaS najdete v tématu [seznam předplatných](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Odpovědi

Kód: 200<br>
OK. Emise využití dávky byly přijaty a zaznamenány na straně Microsoftu pro další zpracování a fakturaci. Seznam odpovědí se vrátí se stavem pro každou jednotlivou událost v dávce. Projděte si datovou část odpovědi, abyste pochopili odpovědi na jednotlivé události použití odeslané jako součást události Batch.

Příklad datové části odpovědi: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

Popis stavového kódu odkazovaného v `BatchUsageEvent` odpovědi rozhraní API:

| Stavový kód  | Description |
| ---------- | -------------------- |
| `Accepted` | Přijata. |
| `Expired` | Použití vypršelo. |
| `Duplicate` | Bylo zadáno duplicitní použití. |
| `Error` | Kód chyby |
| `ResourceNotFound` | Poskytnutý prostředek využití je neplatný. |
| `ResourceNotAuthorized` | Nemáte oprávnění k zajištění využití tohoto prostředku. |
| `InvalidDimension` | Dimenze, pro kterou je toto použití předáno, není pro tuto nabídku nebo plán platná. |
| `InvalidQuantity` | Předané množství je menší nebo rovno 0. |
| `BadArgument` | Vstup chybí nebo je poškozený. |

Kód: 400<br>
Chybný požadavek. Dávka obsahovala více než 25 událostí použití.

Kód: 403<br>
Zakázán. Autorizační token není zadaný, je neplatný nebo vypršela jeho platnost.  Nebo se požadavek pokouší o přístup k předplatnému pro nabídku, která byla publikována s jiným ID Aplikace Azure AD z verze, která byla použita k vytvoření autorizačního tokenu.

## <a name="development-and-testing-best-practices"></a>Osvědčené postupy pro vývoj a testování

Pokud chcete testovat vlastní emise měřičů, implementujte integraci s rozhraním API pro měření, vytvořte plán pro vaši nabídku publikovaných SaaS s vlastními dimenzemi, které jsou v ní definované, s nulovou cenou za jednotku. A publikuje tuto nabídku jako verzi Preview, takže přístup k integraci a její otestování budou moct jenom uživatelé s omezeným přístupem.

K omezení přístupu k tomuto plánu během testování na omezenou cílovou skupinu můžete použít také soukromý plán pro stávající živou nabídku.

## <a name="get-support"></a>Získání podpory

Postupujte podle pokynů v [části Podpora programu komerčního tržiště v partnerském centru](../support.md) , abyste pochopili možnosti podpory vydavatelů a otevřeli lístek podpory s Microsoftem.

## <a name="next-steps"></a>Další kroky

Další informace o rozhraních API služby měření najdete v tématu [Nejčastější dotazy k rozhraním API služby pro měření na webu Marketplace](./marketplace-metering-service-apis-faq.md).

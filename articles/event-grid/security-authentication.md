---
title: Azure Event Grid zabezpečení a ověřování
description: Popisuje Azure Event Grid a jeho koncepty.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: babanisa
ms.openlocfilehash: db6db54d362e7ef6373271e238fdb1cf543a142e
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413475"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid zabezpečení a ověřování 

Azure Event Grid má tři typy ověřování:

* Doručování událostí Webhooku
* Odběry událostí
* Publikování vlastního tématu

## <a name="webhook-event-delivery"></a>Doručování událostí Webhooku

Webhooky jsou jedním z mnoha způsoby příjem událostí ze služby Azure Event Grid. Při vytvoření nové události je připraven, služby Event Grid odešle požadavek HTTP na konfigurovaný koncový bod s událostí v textu požadavku.

Stejně jako mnoho dalších služeb, které podporují webhooky služby Event Grid vyžaduje, abyste prokázali vlastnictví váš koncový bod Webhooku před spuštěním doručování událostí do tohoto koncového bodu. Tento požadavek uživatel se zlými úmysly zabraňuje zahlcení váš koncový bod s událostmi. Když použijete některou z níže uvedených tří služby Azure, infrastruktura Azure automaticky zpracovává toto ověření:

* Azure Logic Apps
* Azure Automation
* Azure Functions pro Trigger služby Event Grid.

Pokud používáte jiný typ koncového bodu, jako například aktivační událost HTTP na základě funkcí Azure, musí koncový bod kódu účastnit handshake ověření pomocí služby Event Grid. Event Grid podporuje dva způsoby ověření předplatného.

1. **Metoda handshake ValidationCode (prostřednictvím kódu programu)**: Pokud zdrojový kód pro vaše koncové body, tato metoda se doporučuje. V době vytvoření odběru událostí služby Event Grid odešle událost ověření předplatného do vašeho koncového bodu. Schéma této události je podobně jako ostatní události Event gridu. Obsahuje datovou část této události `validationCode` vlastnost. Vaše aplikace ověřuje, že žádost o ověření pro předplatné Očekávaná událost a vrátí kód pro ověření do služby Event Grid. Tento mechanismus handshake je podporováno ve všech verzích služby Event Grid.

2. **Metoda handshake ValidationURL (ručně)**: V některých případech, systém nelze přístup ke zdrojovému kódu pro implementaci metody handshake ValidationCode koncového bodu. Například, pokud používáte službu třetí strany (například [Zapier](https://zapier.com) nebo [IFTTT](https://ifttt.com/)), nemůže reagovat na prostřednictvím kódu programu pomocí ověřovacího kódu.

   Počínaje verzí 2018-05-01-preview, podporuje Služba Event Grid handshake ruční ověření. Pokud vytváříte odběr událostí pomocí sady SDK nebo nástroj, který používá rozhraní API verze 2018-05-01-preview nebo později, odešle služby Event Grid `validationUrl` vlastnost v datové části události ověření předplatného. Dokončete signalizace najít tuto adresu URL v datech událostí a ručně odeslat požadavek GET. Můžete použít klienta REST nebo ve webovém prohlížeči.

   Zadaná adresa URL je platný 10 minut. Během této doby je stav zřizování odběr události `AwaitingManualAction`. Pokud neprovedete ruční ověření během 10 minut, Stav zřizování nastavená na `Failed`. Budete muset vytvořit odběr události znovu před zahájením ruční ověření.

### <a name="validation-details"></a>Podrobnosti ověřování

* V době vytvoření/aktualizace předplatného událostí služby Event Grid odešle událost ověření odběru do cílového koncového bodu. 
* Událost obsahuje hodnotu hlavičky "Æg typu události: SubscriptionValidation".
* Text událost má stejné schéma jako ostatní události služby Event Grid.
* Vlastnost typ eventType události je `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Vlastnost dat události obsahuje `validationCode` vlastnost náhodně generované řetězcem. Například "validationCode: acb13...".
* Pokud používáte rozhraní API verze 2018-05-01-preview, také obsahuje data události `validationUrl` vlastnost s adresou URL pro ruční ověření předplatného.
* Pole obsahuje pouze událost ověření. Další události se odesílají v samostatné žádosti o po vracení ověřovacího kódu.
* Sady SDK roviny dat EventGrid mít třídy odpovídající data události ověření předplatného a odpověď ověření předplatného.

V následujícím příkladu je uveden příklad SubscriptionValidationEvent:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Pro koncový bod vlastnictví prokázat způsobem, vracení kód pro ověření ve vlastnosti validationResponse, jak je znázorněno v následujícím příkladu:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Musíte se vrátit stavového kódu odpovědi HTTP 200 OK. HTTP 202 přijato nebyl rozpoznán jako platná odpověď ověřování odběr služby Event Grid.

Nebo můžete ručně ověřit předplatné z odesláním požadavku GET na adresu URL pro ověření. Odběr událostí zůstane ve stavu čekající na vyřízení, dokud nebude ověření.

Příklad zpracování handshake ověření předplatného, najdete v článku [ C# ukázka](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Kontrolní seznam

Při vytvoření odběru událostí, pokud dojde k chybě, jako "pokus o ověření poskytnutý koncový bod https://your-endpoint-here se nezdařilo. Další podrobnosti najdete na webu https://aka.ms/esvalidation", znamená to, že dojde k selhání v ověření metody handshake. Chcete-li tuto chybu vyřešit, ověřte následující aspekty:

* Máte kontrolu nad kódem aplikace v cílový koncový bod? Například pokud píšete triggeru HTTP na základě funkce Azure, máte přístup ke kódu aplikace provádět změny?
* Pokud máte přístup ke kódu aplikace, implementujte mechanismus handshake ValidationCode na základě, jak je znázorněno v příkladu výše.

* Pokud nemáte přístup ke kódu aplikace (například při použití služby třetí strany, která podporuje webhooky), můžete použít mechanismus ruční metodu handshake. Ujistěte se, že používáte verzi rozhraní API 2018-05-01-preview nebo novější (instalace rozšíření rozhraní příkazového řádku Azure Event Grid) pro získání validationUrl v událost ověření. Dokončete ruční ověření metody handshake získání hodnoty `validationUrl` vlastnost a navštivte tuto adresu URL ve webovém prohlížeči. Pokud je ověření úspěšné, měli byste vidět zprávu ve webovém prohlížeči, je ověřování úspěšné. Uvidíte, že stav zřizování odběr události je "bylo úspěšné". 

### <a name="event-delivery-security"></a>Zabezpečení doručování událostí

Váš koncový bod webhooku můžete zabezpečit přidáním parametrů dotazu na adresu URL webhooku, při vytváření odběru událostí. Nastavte jednu z těchto parametrů dotazu jako tajný kód [přístupový token](https://en.wikipedia.org/wiki/Access_token). Webhook můžete tajný kód rozpoznal, že události pocházejí ze služby Event Grid s platná oprávnění. Event Grid bude obsahovat tyto parametry dotazu v každé doručování událostí k webhooku.

Při úpravě odběr události, parametry dotazu nejsou zobrazena nebo vrácena, pokud [– zahrnout full – – adresa url koncového bodu](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) parametr se používá v Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

A konečně je důležité si uvědomit, že služby Azure Event Grid podporuje jenom koncové body HTTPS webhooku.

## <a name="event-subscription"></a>Odběr události

K odběru události, musíte prokázat, že máte přístup ke zdroji události a obslužné rutiny. Prokázání, že vlastníte Webhooku byla popsané v předchozí části. Pokud používáte obslužnou rutinu události, která není Webhooku (například služby event hubu nebo služby queue storage), potřebujete přístup pro zápis do tohoto prostředku. Tato kontrola oprávnění zabrání neoprávněný uživatel možnost odesílat události do vašeho prostředku.

Musíte mít **Microsoft.EventGrid/EventSubscriptions/Write** oprávnění na prostředku, který je zdrojem událostí. Budete potřebovat toto oprávnění, protože psaní si nové předplatné v rámci prostředku. Požadovaný prostředek se liší podle toho, jestli jste už přihlášení k odběru téma system nebo vlastního tématu. Oba typy jsou popsány v této části.

### <a name="system-topics-azure-service-publishers"></a>Témata systém (vydavateli služby Azure)

Témata systému budete potřebovat oprávnění k zápisu nového předplatného událostí v oboru prostředků publikování události. Formát tohoto prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Například chcete-li přihlásit odběr události v účtu úložiště s názvem **UCET**, potřebujete oprávnění Microsoft.EventGrid/EventSubscriptions/Write na: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Vlastní témata

Vlastní témata budete potřebovat oprávnění k zápisu nového předplatného událostí v oboru téma event gridu. Formát tohoto prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Například pro přihlášení k odběru vlastního tématu s názvem **mytopic**, potřebujete oprávnění Microsoft.EventGrid/EventSubscriptions/Write na: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikování vlastního tématu

Vlastní témata pomocí sdíleného přístupového podpisu (SAS) nebo ověřování pomocí klíče. Doporučujeme, abyste SAS, ale ověření pomocí klíče poskytuje jednoduché programování a je kompatibilní s mnoha existujících vydavatelů webhooku. 

Hodnota ověřování je zahrnout v hlavičce protokolu HTTP. SAS, použijte **token sas Æg** pro hodnotu hlavičky. Pro ověření pomocí klíče, použijte **klíč sas Æg** pro hodnotu hlavičky.

### <a name="key-authentication"></a>Ověření pomocí klíče

Ověření pomocí klíče je nejjednodušší formu ověřování. Použijte formát: `aeg-sas-key: <your key>`

Například předáte klíč s:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny SAS

Tokeny SAS pro Event Grid zahrnují prostředek, čas vypršení platnosti a podpisu. Formát tokenu SAS: `r={resource}&e={expiration}&s={signature}`.

Prostředek je cesta k tématu event gridu, do kterého odesíláte události. Například je cesta platná prostředku: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Generování podpisu z klíče.

Například platný **token sas Æg** hodnota je:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Následující příklad vytvoří SAS token pro použití s využitím služby Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Správa řízení přístupu

Azure Event Grid umožňuje řídit úroveň přístupu k různým uživatelům provádět různé operace správy, jako je například seznam odběrů událostí, vytvářet nové a vygenerujte klíče. Event Grid pomocí řízení přístupu na základě rolí Azure (RBAC).

### <a name="operation-types"></a>Typy operací

Podporuje Služba Event Grid následující akce:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Poslední tři operace vracejí potenciálně tajné informace, které získá z běžných operací čtení odfiltrována. Doporučujeme omezit přístup k těmto operacím. 

### <a name="built-in-roles"></a>Vestavěné role

Event Grid obsahuje dvě předdefinované role pro správu odběry událostí. Při implementaci jsou důležité [událostí domény](event-domains.md) vzhledem k tomu, že uživatelům udělit oprávnění, které potřebují k odběru témat ve vaší doméně události. Tyto role jsou zaměřené na odběry událostí a není udělit přístup pro akce, jako je vytváření témat.

Je možné [těchto rolí přiřadit uživateli nebo skupině](../role-based-access-control/quickstart-assign-role-user-portal.md).

**Přispěvatel EventSubscription EventGrid (Preview)**: Správa operací odběru Event gridu

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**Čtenář EventSubscription EventGrid (Preview)**: přečíst odběry služby Event Grid

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>Vlastní role

Pokud je třeba zadat oprávnění, která se liší od předdefinované role, můžete vytvořit vlastní role.

Následují definice rolí služby Event Grid ukázky, které umožňují uživatelům provádět různé akce. Tyto vlastní role se liší od předdefinované role, protože udělují širší přístup než jenom odběry událostí.

**EventGridReadOnlyRole.json**: Povolte pouze operace určené jen pro čtení.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: Povolit akce post s omezeným přístupem, ale zakázat akce odstranění.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: Umožňuje všechny akce event grid.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Můžete vytvořit vlastní role se [PowerShell](../role-based-access-control/custom-roles-powershell.md), [rozhraní příkazového řádku Azure](../role-based-access-control/custom-roles-cli.md), a [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Další postup

* Úvod do služby Event Grid najdete v tématu [služby Event Grid](overview.md)

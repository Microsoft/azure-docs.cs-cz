---
title: Azure Event Grid zabezpečení a ověřování
description: Popisuje Azure Event Grid a jeho koncepty.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: f22d8c57b0127e646321a20587d0cd89f5c9ea45
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72325407"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid zabezpečení a ověřování 

Azure Event Grid má tři typy ověřování:

* Doručení události Webhooku
* Odběry událostí
* Publikování vlastního tématu

## <a name="webhook-event-delivery"></a>Doručení události Webhooku

Webhooky jsou jedním z mnoha způsobů, jak přijímat události z Azure Event Grid. Když je nová událost připravena, Služba Event Grid odešle požadavek HTTP do nakonfigurovaného koncového bodu s událostí v textu požadavku.

Stejně jako mnoho dalších služeb, které podporují Webhooky, Event Grid vyžaduje, abyste prokáže vlastnictví koncového bodu Webhooku předtím, než začne doručovat události do tohoto koncového bodu. Tento požadavek brání uživateli se zlými úmysly v přeplavení koncového bodu s událostmi. Když použijete některou ze tří služeb Azure uvedených níže, infrastruktura Azure toto ověření automaticky zpracuje:

* Azure Logic Apps s [konektorem Event Grid](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation prostřednictvím [Webhooku](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions s [triggerem Event Grid](../azure-functions/functions-bindings-event-grid.md)

Pokud používáte jiný typ koncového bodu, jako je Azure Functions založený na triggeru HTTP, váš kód koncového bodu musí být součástí ověřovací metody handshake s Event Grid. Event Grid podporuje dva způsoby ověření předplatného.

1. **ValidationCode handshake (program)** : Pokud řídíte zdrojový kód pro koncový bod, doporučuje se tato metoda. V době vytváření odběru událostí Event Grid odešle do vašeho koncového bodu událost ověření předplatného. Schéma této události je podobné jako jakákoli jiná událost Event Grid. Datová část této události obsahuje vlastnost @no__t 0. Vaše aplikace ověří, zda je žádost o ověření určena pro očekávané předplatné události, a vrátí ověřovací kód na Event Grid. Tento mechanismus handshake je podporován ve všech Event Grid verzích.

2. **ValidationURL handshake (ruční)** : v některých případech nebudete mít přístup ke zdrojovému kódu koncového bodu pro implementaci metody handshake ValidationCode. Například pokud používáte službu třetí strany (například [Zapier](https://zapier.com) nebo [IFTTT](https://ifttt.com/)), nemůžete programově reagovat pomocí ověřovacího kódu.

   Počínaje verzí 2018-05-01-Preview Event Grid podporuje manuální ověřování metodou handshake. Pokud vytváříte odběr událostí pomocí sady SDK nebo nástroje, který používá rozhraní API verze 2018-05-01-Preview nebo novější, Event Grid odešle vlastnost `validationUrl` v datové části události ověření předplatného. K dokončení metody handshake Najděte tuto adresu URL v datech události a ručně odešlete požadavek GET. Můžete použít buď klienta REST, nebo webový prohlížeč.

   Zadaná adresa URL je platná 5 minut. Během této doby je stav zřizování odběru události `AwaitingManualAction`. Pokud nedokončíte ruční ověření během 5 minut, stav zřizování je nastaven na `Failed`. Než začnete s ručním ověřováním, budete muset vytvořit odběr událostí znovu.

    Tento mechanismus ověřování také vyžaduje, aby koncový bod Webhooku vrátil stavový kód HTTP 200, aby věděl, že příspěvek pro událost ověření byl přijat předtím, než mohl být vložen do režimu ručního ověření. Jinými slovy, pokud koncový bod vrátí 200, ale nevrátí zpět odpověď na ověření programově, režim se převede do režimu ručního ověření. Pokud je adresa URL pro ověření v rámci 5 minut k dispozici, považuje se ověřovací metoda handshake za úspěšnou.

> [!NOTE]
> Použití certifikátů podepsaných svým držitelem k ověření se nepodporuje. Místo toho použijte podepsaný certifikát od certifikační autority (CA).

### <a name="validation-details"></a>Podrobnosti ověření

* V okamžiku vytvoření nebo aktualizace odběru události Event Grid účtuje událost ověření předplatného do cílového koncového bodu. 
* Událost obsahuje hodnotu hlavičky "AEG-Event-Type: SubscriptionValidation".
* Tělo události má stejné schéma jako jiné události Event Grid.
* Vlastnost eventType události je `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Vlastnost dat události obsahuje vlastnost @no__t 0 s náhodně generovaným řetězcem. Například "validationCode: acb13...".
* Data události také obsahují vlastnost `validationUrl` s adresou URL pro ruční ověření předplatného.
* Pole obsahuje pouze událost ověření. Další události se odesílají v samostatném požadavku po návratu zpět ověřovacího kódu.
* Sady SDK pro EventGrid dataplan mají třídy odpovídající datům události ověření předplatného a odpovědi na ověření předplatného.

V následujícím příkladu je uveden příklad SubscriptionValidationEvent:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Chcete-li prokázat vlastnictví koncového bodu, vraťte kód ověření do vlastnosti validationResponse, jak je znázorněno v následujícím příkladu:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Je nutné vrátit stavový kód odpovědi HTTP 200 OK. Přijatý protokol HTTP 202 nebyl rozpoznán jako platná odpověď ověření předplatného Event Grid.

Nebo můžete předplatné ověřit ručně odesláním požadavku GET na adresu URL pro ověření. Předplatné události zůstane v nedokončeném stavu, dokud se neověří.

Příklad manipulace s ověřovací metodou handshake předplatného najdete v [ C# ukázce](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Kontrolní seznam

Pokud se při vytváření odběru událostí zobrazuje chybová zpráva, například "pokus o ověření poskytnutého koncového bodu https: \//koncový bod, se nezdařilo. Další podrobnosti najdete na adrese https: \//neboli. MS/esvalidation ", označuje, že došlo k chybě ověřovací metody handshake. Chcete-li tuto chybu vyřešit, ověřte následující aspekty:

* Máte pod cílovým koncovým bodem řízení kódu aplikace? Pokud například píšete funkci Azure Functions Trigger založenou na protokolu HTTP, máte přístup k kódu aplikace, abyste je mohli změnit?
* Máte-li přístup k kódu aplikace, implementujte mechanismus handshake založený na ValidationCode, jak je znázorněno v ukázce výše.

* Pokud nemáte přístup k kódu aplikace (například pokud používáte službu třetí strany, která podporuje Webhooky), můžete použít mechanismus ručního ověřování. Ujistěte se, že používáte verzi rozhraní API 2018-05-01-Preview nebo novější (nainstalujte Event Grid rozšíření Azure CLI), abyste obdrželi validationUrl v události ověření. Pokud chcete dokončit ruční ověření handshake, Získejte hodnotu vlastnosti `validationUrl` a navštivte tuto adresu URL ve webovém prohlížeči. Pokud je ověření úspěšné, měla by se ve webovém prohlížeči zobrazit zpráva, že ověření proběhlo úspěšně. Uvidíte, že provisioningState předplatného události je "úspěch". 

### <a name="event-delivery-security"></a>Zabezpečení doručení událostí

Můžete zabezpečit koncový bod Webhooku přidáním parametrů dotazu k adrese URL Webhooku při vytváření odběru události. Nastavte jeden z těchto parametrů dotazu na tajný klíč, jako je [přístupový token](https://en.wikipedia.org/wiki/Access_token). Webhook může pomocí tajného klíče rozpoznat, že událost pochází z Event Grid s platnými oprávněními. Event Grid budou tyto parametry dotazu zahrnovat při každém doručování události do Webhooku.

Při úpravách odběru události se parametry dotazu nezobrazí ani nevrátí, pokud se v rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)používá parametr [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) .

Nakonec je důležité si uvědomit, že Azure Event Grid podporuje pouze koncové body Webhooku protokolu HTTPS.

## <a name="event-subscription"></a>Odběr události

Chcete-li se přihlásit k odběru události, je nutné prokázat, že máte přístup ke zdroji a obslužné rutině události. Prokázání, že vlastní Webhook byl popsaný v předchozí části. Pokud používáte obslužnou rutinu události, která není webhookem (například centrum událostí nebo úložiště front), budete k tomuto prostředku potřebovat přístup pro zápis. Tato oprávnění znemožňují neoprávněnému uživateli odesílat události do vašeho prostředku.

Musíte mít oprávnění **Microsoft. EventGrid/EventSubscriptions/Write** pro prostředek, který je zdrojem událostí. Toto oprávnění budete potřebovat, protože píšete nové předplatné v oboru prostředku. Požadovaný prostředek se liší v závislosti na tom, jestli jste se přihlásili k odběru systémového tématu nebo vlastního tématu. Oba typy jsou popsány v této části.

### <a name="system-topics-azure-service-publishers"></a>Systémová témata (vydavatelé služeb Azure)

Pro systémová témata potřebujete oprávnění k zápisu nového odběru události v oboru prostředku, který publikuje událost. Formát prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Například pro přihlášení k odběru události v účtu úložiště s názvem **ucet**budete potřebovat oprávnění Microsoft. EventGrid/EventSubscriptions/Write pro: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Vlastní témata

Pro vlastní témata potřebujete oprávnění zapsat nové předplatné události v oboru tématu Event Grid. Formát prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Například pro přihlášení k odběru vlastního tématu s názvem **mytopic**potřebujete oprávnění Microsoft. EventGrid/EventSubscriptions/Write na: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikování vlastního tématu

Vlastní témata používají buď sdílený přístupový podpis (SAS), nebo ověřování pomocí klíče. Doporučujeme použít SAS, ale ověřování pomocí klíče poskytuje jednoduché programování a je kompatibilní s mnoha existujícími vydavateli webhooků. 

Do hlavičky HTTP zadáte hodnotu ověřování. Pro SAS použijte **AEG-SAS-token** pro hodnotu hlavičky. Pro ověřování pomocí klíče použijte **AEG-SAS-Key** pro hodnotu hlavičky.

### <a name="key-authentication"></a>Ověřování klíčů

Ověřování klíčem je nejjednodušší forma ověřování. Použijte formát: `aeg-sas-key: <your key>`

Například můžete předat klíč pomocí:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny SAS

Tokeny SAS pro Event Grid zahrnují prostředek, čas vypršení platnosti a podpis. Formát tokenu SAS je: `r={resource}&e={expiration}&s={signature}`.

Prostředek je cesta k tématu Event gridu, do kterého odesíláte události. Platná cesta k prostředku je například: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Vygenerujete podpis z klíče.

Platná hodnota **AEG-SAS-token** je například:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Následující příklad vytvoří token SAS pro použití s Event Grid:

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

## <a name="management-access-control"></a>Access Control správy

Azure Event Grid umožňuje řídit úroveň přístupu daným různým uživatelům a provádět různé operace správy, jako jsou odběry událostí seznamu, vytváření nových a generování klíčů. Event Grid používá řízení přístupu na základě role (RBAC) Azure.

### <a name="operation-types"></a>Typy operací

Event Grid podporuje následující akce:

* Microsoft. EventGrid/*/Read
* Microsoft. EventGrid/*/Write
* Microsoft. EventGrid/*/DELETE
* Microsoft. EventGrid/eventSubscriptions/getFullUrl/Action
* Microsoft. EventGrid/témata/klíče listkey/Action
* Microsoft. EventGrid/témata/regenerateKey/Action

Poslední tři operace vrátí potenciálně tajné informace, které se vyfiltrují z běžných operací čtení. Doporučuje se omezit přístup k těmto operacím. 

### <a name="built-in-roles"></a>Vestavěné role

Event Grid poskytuje dvě předdefinované role pro správu odběrů událostí. Jsou důležité při implementaci [domén událostí](event-domains.md) , protože uživatelům poskytují oprávnění, která potřebují k přihlášení k odběru témat v doméně události. Tyto role jsou zaměřené na odběry událostí a neudělují přístup k akcím, jako je vytváření témat.

[Tyto role můžete přiřadit uživateli nebo skupině](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Přispěvatel (Preview)** : Správa operací předplatného Event Grid

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

**EventGrid EventSubscription Reader (Preview)** : čtení předplatných Event Grid

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

Pokud potřebujete zadat oprávnění, která se liší od předdefinovaných rolí, můžete vytvořit vlastní role.

Níže jsou uvedené ukázkové Event Grid definice rolí, které umožňují uživatelům provádět různé akce. Tyto vlastní role se liší od předdefinovaných rolí, protože udělují širší přístup než jenom odběry událostí.

**EventGridReadOnlyRole. JSON**: povoluje jenom operace jen pro čtení.

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

**EventGridNoDeleteListKeysRole. JSON**: povolí omezené akce příspěvku, ale zakáže akce odstranění.

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

**EventGridContributorRole. JSON**: umožňuje všechny akce v mřížce událostí.

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

Můžete vytvářet vlastní role pomocí [PowerShellu](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md)a [REST](../role-based-access-control/custom-roles-rest.md).

## <a name="next-steps"></a>Další kroky

* Úvod do Event Grid najdete v tématu [o Event Grid](overview.md)

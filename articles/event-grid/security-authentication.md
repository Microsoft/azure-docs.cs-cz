---
title: "Azure mřížky událostí zabezpečení a ověřování"
description: "Popisuje mřížky událostí Azure a jeho koncepty."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 03/15/2018
ms.author: babanisa
ms.openlocfilehash: 0b7ef71cf940f82f46a7f053e5c9f7ef64342b6e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="event-grid-security-and-authentication"></a>Události zabezpečení mřížky a ověřování 

Azure mřížky událostí má tři typy ověřování:

* Odběry událostí
* Publikování události
* Doručení události Webhooku

## <a name="webhook-event-delivery"></a>Události Webhooku doručení

Webhooky jsou jedním z mnoha způsoby, jak přijímat události z události mřížky Azure. Až o novou událost připravena, odešle Webhooku mřížky událostí nakonfigurovaný koncový bod HTTP s událostí v textu požadavku HTTP.

Při registraci svůj vlastní koncový bod Webhooku s událostí mřížky, odešle požadavek POST s kódem jednoduché ověření prokázat vlastnictví koncový bod. Vaše aplikace musí odpovídat tak, že odezva zpět ověřovacího kódu. Mřížky událostí není doručovat události Webhooku koncových bodů, které nebyly ověření proběhlo úspěšně.

### <a name="validation-details"></a>Podrobnosti o ověření

* Během aktualizace vytvoření odběru událostí odešle událost mřížky "SubscriptionValidationEvent" událostí do cílového koncového bodu.
* Události obsahuje hodnotu hlavičky "Æg typu události: SubscriptionValidation".
* V textu události má stejné schéma jako ostatní události událostí mřížky.
* Data události obsahuje vlastnost s náhodně generované řetězec "validationCode". Například "validationCode: acb13...".
* Toto pole obsahuje pouze událost ověření. Další události jsou zasílány v samostatné žádosti po vracení ověřovacího kódu.

V následujícím příkladu je uveden příklad SubscriptionValidationEvent:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

K prokázání vlastnictví koncový bod, vracení ověřovacího kódu ve vlastnosti validationResponse, jak je znázorněno v následujícím příkladu:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```
### <a name="event-delivery-security"></a>Události doručení zabezpečení

Váš koncový bod webhooku můžete zabezpečit přidáním parametry dotazu na adresu URL webhooku při vytváření předplatného služby událostí. Nastavte jednu z těchto parametrů dotazu jako tajný klíč [přístupový token](https://en.wikipedia.org/wiki/Access_token) webhooku můžete rozpoznat událost pochází z událostí mřížky s platné oprávnění. Mřížky události bude obsahovat tyto parametry dotazu v každé události doručení do webhooku.

Při úpravě předplatného události, parametry dotazu se nezobrazí nebo vrácena, pokud [– zahrnout full--adresu url koncového bodu](https://docs.microsoft.com/en-us/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_show) parametr se používá v Azure [rozhraní příkazového řádku](https://docs.microsoft.com/en-us/cli/azure?view=azure-cli-latest).

Nakonec je důležité si uvědomit, mřížky událostí Azure podporuje pouze HTTPS webhooku koncové body.

## <a name="event-subscription"></a>Odběru událostí

K odběru události, musíte mít **Microsoft.EventGrid/EventSubscriptions/Write** oprávnění na požadovaný prostředek. Je nutné toto oprávnění, protože psaní nové předplatné v oboru prostředku. Požadovaný prostředek se liší v závislosti na tom, jestli můžete jste přihlášení k odběru tématu systému nebo vlastní téma. Oba typy jsou popsané v této části.

### <a name="system-topics-azure-service-publishers"></a>Témata týkající se systému (vydavateli služby Azure)

Pro systém témata budete potřebovat oprávnění k zápisu nového odběru události v oboru prostředku publikování události. Formát prostředku není: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Například k odběru události na účet úložiště s názvem **UCET**, potřebujete oprávnění Microsoft.EventGrid/EventSubscriptions/Write na: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Vlastní témata

Pro vlastní témata budete potřebovat oprávnění k zápisu nového odběru události v oboru tohoto tématu mřížky událostí. Formát prostředku není: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Například k odběru vlastní téma s názvem **mytopic**, potřebujete oprávnění Microsoft.EventGrid/EventSubscriptions/Write na: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>Téma publikování

Témata týkající se použití sdíleného přístupového podpisu (SAS) nebo ověření pomocí klíče. Doporučujeme, abyste SAS, ale ověření pomocí klíče poskytuje jednoduché programování a je kompatibilní s mnoha existující webhooku vydavatelů. 

Můžete zahrnout ověřování hodnota v hlavičce protokolu HTTP. SAS, použijte **tokenu sas Æg** pro hodnotu hlavičky. Pro ověření pomocí klíče, použít **klíč sas Æg** pro hodnotu hlavičky.

### <a name="key-authentication"></a>Ověření pomocí klíče

Ověření pomocí klíče je nejjednodušší formu ověřování. Použijte formát: `aeg-sas-key: <your key>`

Například předáte klíč se:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny SAS

Tokeny SAS pro událost mřížky zahrnují prostředku, čas vypršení platnosti a podpis. Formát tokenu SAS: `r={resource}&e={expiration}&s={signature}`.

Prostředek je cesta k tématu mřížky událostí, které události chcete odeslat. Například je cesta platná prostředku: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Generování podpisu z klíče.

Například platná **tokenu sas Æg** hodnota je:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Následující příklad vytvoří SAS token pro použití s mřížky událostí:

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

Azure mřížky událostí můžete řídit úroveň přístupu k různým uživatelům dělat různé operace správy, jako je například seznam událostí odběry, vytvořte nové a generovat klíče. Událost mřížky používá Azure na základě Role přístupu zkontrolujte (RBAC).

### <a name="operation-types"></a>Typy operací

Mřížky událostí Azure podporuje následující akce:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Vrátí poslední tři operace potenciálně tajné informace, které získá filtrované mimo normální operace čtení. Je osvědčeným postupem můžete omezit přístup na tyto operace. Můžete vytvořit vlastní role pomocí [prostředí Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md), [rozhraní příkazového řádku Azure (CLI)](../active-directory/role-based-access-control-manage-access-azure-cli.md)a [REST API](../active-directory/role-based-access-control-manage-access-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Vynucování Role na základě kontroly přístupu (RBAC)

Použijte následující postup k vynucení RBAC pro různé uživatele:

#### <a name="create-a-custom-role-definition-file-json"></a>Vytvořte soubor definice vlastních rolí (.json)

Následuje ukázka události mřížky role definice, které umožní uživatelům provádět různé sady akcí.

**EventGridReadOnlyRole.json**: Povolí jenom operace jen pro čtení.

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

**EventGridNoDeleteListKeysRole.json**: Povolit akce s omezeným přístupem po ale zakáže akce odstranění.

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

**EventGridContributorRole.json**: umožňuje všechny akce mřížky událostí.

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

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>Vytvořit a přiřadit vlastní role pomocí rozhraní příkazového řádku Azure

Chcete-li vytvořit vlastní roli, použijte:

```azurecli
az role definition create --role-definition @<file path>
```

Pokud chcete přiřadit role pro uživatele, použijte:

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>Další postup

* Úvod k mřížce událostí, naleznete v části [o mřížky událostí](overview.md)

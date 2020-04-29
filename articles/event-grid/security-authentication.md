---
title: Azure Event Grid zabezpečení a ověřování
description: Tento článek popisuje různé způsoby ověřování přístupu k prostředkům Event Grid (Webhook, odběry, vlastní témata).
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 4b2d65c9523f32eed01baa8d63c3d0119d00de1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532389"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Ověřování přístupu k prostředkům Event Grid

Azure Event Grid má tři typy ověřování:

- Doručení události Webhooku
- Odběry událostí
- Publikování vlastního tématu

## <a name="webhook-event-delivery"></a>Doručení události Webhooku

Webhooky jsou jedním z mnoha způsobů, jak přijímat události z Azure Event Grid. Když je nová událost připravena, Služba Event Grid odešle požadavek HTTP do nakonfigurovaného koncového bodu s událostí v textu požadavku.

Stejně jako mnoho dalších služeb, které podporují Webhooky, Event Grid vyžaduje, abyste prokáže vlastnictví koncového bodu Webhooku předtím, než začne doručovat události do tohoto koncového bodu. Tento požadavek brání uživateli se zlými úmysly v přeplavení koncového bodu s událostmi. Když použijete některou ze tří služeb Azure uvedených níže, infrastruktura Azure toto ověření automaticky zpracuje:

- Azure Logic Apps s [konektorem Event Grid](https://docs.microsoft.com/connectors/azureeventgrid/)
- Azure Automation prostřednictvím [Webhooku](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Azure Functions s [triggerem Event Grid](../azure-functions/functions-bindings-event-grid.md)

Pokud používáte jiný typ koncového bodu, jako je Azure Functions založený na triggeru HTTP, váš kód koncového bodu musí být součástí ověřovací metody handshake s Event Grid. Event Grid podporuje dva způsoby ověření předplatného.

1. **Synchronní metoda handshake**: v době vytváření odběru událostí Event Grid odesílá do vašeho koncového bodu událost ověření předplatného. Schéma této události je podobné jako jakákoli jiná událost Event Grid. Datová část této události obsahuje `validationCode` vlastnost. Vaše aplikace ověřuje, zda je žádost o ověření určena pro očekávané předplatné události, a v případě synchronního vrátí ověřovací kód v odpovědi. Tento mechanismus handshake je podporován ve všech Event Grid verzích.

2. **Asynchronní metoda handshake**: v některých případech nemůžete v případě synchronních odpovědí vracet ValidationCode. Například pokud používáte službu třetí strany (například [`Zapier`](https://zapier.com) nebo [IFTTT](https://ifttt.com/)), nemůžete programově reagovat pomocí ověřovacího kódu.

   Počínaje verzí 2018-05-01-Preview Event Grid podporuje manuální ověřování metodou handshake. Pokud vytváříte odběr událostí pomocí sady SDK nebo nástroje, který používá rozhraní API verze 2018-05-01-Preview nebo novější, Event Grid odešle `validationUrl` vlastnost v datové části události ověření předplatného. K dokončení metody handshake Najděte tuto adresu URL v datech události a proveďte do ní požadavek GET. Můžete použít buď klienta REST, nebo webový prohlížeč.

   Zadaná adresa URL je platná **5 minut**. Během této doby je `AwaitingManualAction`stav zřizování odběru události. Pokud nedokončíte ruční ověření během 5 minut, stav zřizování je nastaven na `Failed`. Než začnete s ručním ověřováním, budete muset vytvořit odběr událostí znovu.

   Tento mechanismus ověřování také vyžaduje, aby koncový bod Webhooku vrátil stavový kód HTTP 200, aby věděl, že příspěvek pro událost ověření byl přijat předtím, než mohl být vložen do režimu ručního ověření. Jinými slovy, pokud koncový bod vrátí 200, ale nevrátí zpět odpověď ověření synchronně, režim se převede do režimu ručního ověření. Pokud se v ověřovací adrese URL objeví během 5 minut, považuje se ověřovací metoda handshake za úspěšnou.

> [!NOTE]
> Použití certifikátů podepsaných svým držitelem k ověření se nepodporuje. Místo toho použijte podepsaný certifikát od certifikační autority (CA).

### <a name="validation-details"></a>Podrobnosti ověření

- V okamžiku vytvoření nebo aktualizace odběru události Event Grid účtuje událost ověření předplatného do cílového koncového bodu.
- Událost obsahuje hodnotu hlavičky "AEG-Event-Type: SubscriptionValidation".
- Tělo události má stejné schéma jako jiné události Event Grid.
- Vlastnost eventType události je `Microsoft.EventGrid.SubscriptionValidationEvent`.
- Vlastnost dat události obsahuje `validationCode` vlastnost s náhodně generovaným řetězcem. Například "validationCode: acb13...".
- Data události také obsahují `validationUrl` vlastnost s adresou URL pro ruční ověření předplatného.
- Pole obsahuje pouze událost ověření. Další události se odesílají v samostatném požadavku po návratu zpět ověřovacího kódu.
- Sady SDK pro EventGrid dataplan mají třídy odpovídající datům události ověření předplatného a odpovědi na ověření předplatného.

V následujícím příkladu je uveden příklad SubscriptionValidationEvent:

```json
[
  {
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
  }
]
```

Chcete-li prokázat vlastnictví koncového bodu, vraťte kód ověření do vlastnosti validationResponse, jak je znázorněno v následujícím příkladu:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Je nutné vrátit stavový kód odpovědi HTTP 200 OK. Přijatý protokol HTTP 202 nebyl rozpoznán jako platná odpověď ověření předplatného Event Grid. Požadavek HTTP musí být dokončen do 30 sekund. Pokud operace nekončí do 30 sekund, operace se zruší a může se pokusit o její opakování po 5 sekundách. Pokud všechny pokusy selžou, bude zpracována jako chyba handshake ověření.

Nebo můžete předplatné ověřit ručně odesláním požadavku GET na adresu URL pro ověření. Předplatné události zůstane v nedokončeném stavu, dokud se neověří. Ověřovací adresa URL používá port 553. Pokud vaše pravidla brány firewall blokují port 553, může být potřeba aktualizovat pravidla pro úspěšnou ruční handshaki.

Příklad manipulace s metodou handshake ověření předplatného najdete v tématu [ukázka jazyka C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="troubleshooting-eventsubsciption-validation"></a>Řešení potíží s ověřováním EventSubsciption

Pokud se při vytváření odběru událostí zobrazuje chybová zpráva, například "pokus o ověření poskytnutého koncového bodu https:\//Your-Endpoint-here se nezdařil. Další podrobnosti najdete na adrese https:\//aka.MS/esvalidation ", která označuje, že došlo k chybě ověřovací metody handshake. Chcete-li tuto chybu vyřešit, ověřte následující aspekty:

- Proveďte příspěvek HTTP na adresu URL Webhooku s [ukázkovým](#validation-details) textem žádosti SubscriptionValidationEvent pomocí metody post nebo kudrlinkou nebo podobného nástroje.
- Pokud Webhook implementuje mechanismus synchronního ověřování metodou handshake, ověřte, že se ValidationCode vrací jako součást odpovědi.
- Pokud Webhook implementuje mechanismus ověřování metodou handshake s asynchronním ověřováním, ověřte, že jste příspěvek HTTP vrátil 200 OK.
- Pokud Webhook vrací v odpovědi 403 (zakázáno), ověřte, jestli je Webhook za Application Gateway nebo bránou firewall webových aplikací. Pokud je, je nutné zakázat tato pravidla brány firewall a znovu provést operaci HTTP POST:

  920300 (žádost neobsahuje hlavičku Accept, můžeme to opravit)

  942430 (omezené zjištění anomálií znaků SQL (args): počet speciálních znaků překročen (12))

  920230 (bylo zjištěno více kódování adresy URL)

  942130 (útok injektáže SQL: byl zjištěn SQL Tautology.)

  931130 (možný útok na vzdálené zahrnutí souborů (RFI) = odkaz mimo doménu/odkaz)

### <a name="event-delivery-security"></a>Zabezpečení doručení událostí

#### <a name="azure-ad"></a>Azure AD

Koncový bod Webhooku můžete zabezpečit pomocí Azure Active Directory k ověřování a autorizaci Event Grid k publikování událostí do koncových bodů. Budete muset vytvořit aplikaci Azure Active Directory, vytvořit zásadu role a služby v aplikaci pro autorizaci Event Grid a nakonfigurovat odběr událostí tak, aby používal aplikaci Azure AD. [Naučte se konfigurovat AAD pomocí Event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Parametry dotazu

Můžete zabezpečit koncový bod Webhooku přidáním parametrů dotazu k adrese URL Webhooku při vytváření odběru události. Nastavte jeden z těchto parametrů dotazu na tajný klíč, jako je [přístupový token](https://en.wikipedia.org/wiki/Access_token). Webhook může pomocí tajného klíče rozpoznat, že událost pochází z Event Grid s platnými oprávněními. Event Grid budou tyto parametry dotazu zahrnovat při každém doručování události do Webhooku.

Při úpravách odběru události se parametry dotazu nezobrazí ani nevrátí, pokud se v rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)používá parametr [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) .

Nakonec je důležité si uvědomit, že Azure Event Grid podporuje pouze koncové body Webhooku protokolu HTTPS.

## <a name="event-subscription"></a>Odběr události

Chcete-li se přihlásit k odběru události, je nutné prokázat, že máte přístup ke zdroji a obslužné rutině události. Prokázání, že vlastní Webhook byl popsaný v předchozí části. Pokud používáte obslužnou rutinu události, která není webhookem (například centrum událostí nebo úložiště front), budete k tomuto prostředku potřebovat přístup pro zápis. Tato oprávnění znemožňují neoprávněnému uživateli odesílat události do vašeho prostředku.

Musíte mít oprávnění **Microsoft. EventGrid/EventSubscriptions/Write** pro prostředek, který je zdrojem událostí. Toto oprávnění budete potřebovat, protože píšete nové předplatné v oboru prostředku. Požadovaný prostředek se liší v závislosti na tom, jestli jste se přihlásili k odběru systémového tématu nebo vlastního tématu. Oba typy jsou popsány v této části.

### <a name="system-topics-azure-service-publishers"></a>Systémová témata (vydavatelé služeb Azure)

Pro systémová témata potřebujete oprávnění k zápisu nového odběru události v oboru prostředku, který publikuje událost. Formát prostředku je:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Například pro přihlášení k odběru události v účtu úložiště s názvem **ucet**budete potřebovat oprávnění Microsoft. EventGrid/EventSubscriptions/Write pro:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Vlastní témata

Pro vlastní témata potřebujete oprávnění zapsat nové předplatné události v oboru tématu Event Grid. Formát prostředku je:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Například pro přihlášení k odběru vlastního tématu s názvem **mytopic**potřebujete oprávnění Microsoft. EventGrid/EventSubscriptions/Write pro:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikování vlastního tématu

Vlastní témata používají buď sdílený přístupový podpis (SAS), nebo ověřování pomocí klíče. Doporučujeme použít SAS, ale ověřování pomocí klíče poskytuje jednoduché programování a je kompatibilní s mnoha existujícími vydavateli webhooků.

Do hlavičky HTTP zadáte hodnotu ověřování. Pro SAS použijte **AEG-SAS-token** pro hodnotu hlavičky. Pro ověřování pomocí klíče použijte **AEG-SAS-Key** pro hodnotu hlavičky.

### <a name="key-authentication"></a>Ověřování klíčů

Ověřování klíčem je nejjednodušší forma ověřování. Použijte formát:`aeg-sas-key: <your key>`

Například můžete předat klíč pomocí:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny SAS

Tokeny SAS pro Event Grid zahrnují prostředek, čas vypršení platnosti a podpis. Formát tokenu SAS je: `r={resource}&e={expiration}&s={signature}`.

Prostředek je cesta k tématu Event gridu, do kterého odesíláte události. Například platná cesta prostředku je: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`. Pokud chcete zobrazit všechny podporované verze rozhraní API, přečtěte si téma [typy prostředků Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

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

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Všechny události nebo data zapsaná na disk službou Event Grid službou jsou šifrovány klíčem spravovaným společností Microsoft, který zajišťuje, aby byl zašifrovaný v klidovém stavu. Kromě toho maximální doba, po kterou jsou události nebo data uchovávány, je 24 hodin v dodržování [zásad Event Grid opakování](delivery-and-retry.md). Event Grid budou automaticky odstraňovat všechny události nebo data po 24 hodinách nebo na hodnotu TTL (Time to Live), podle toho, co je méně.

## <a name="endpoint-validation-with-cloudevents-v10"></a>Ověření koncového bodu pomocí CloudEvents v 1.0
Pokud už jste obeznámeni s Event Grid, možná jste si všimli, že je Event Grid ověření koncového bodu handshake pro předcházení zneužití. CloudEvents v 1.0 implementuje svou vlastní [sémantiku ochrany proti zneužívání](security-authentication.md#webhook-event-delivery) pomocí metody HTTP. Další informace si můžete přečíst [tady](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Při použití schématu CloudEvents pro výstup Event Grid použití s ochranou zneužití CloudEvents v 1.0 místo mechanismu události ověřování Event Grid.

## <a name="next-steps"></a>Další kroky

- Úvod do Event Grid najdete v tématu [o Event Grid](overview.md)

---
title: Zabezpečení a ověřování služby Azure Event Grid
description: Tento článek popisuje různé způsoby ověřování přístupu k prostředkům event gridu (WebHook, odběry, vlastní témata)
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281012"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Ověřování přístupu k prostředkům sítě událostí

Azure Event Grid má tři typy ověřování:

* Doručování událostí WebHook
* Odběry událostí
* Publikování vlastního tématu

## <a name="webhook-event-delivery"></a>Doručení události WebHook

Webhooky jsou jedním z mnoha způsobů, jak přijímat události z Azure Event Grid. Když je připravena nová událost, služba Event Grid posts požadavek HTTP na nakonfigurovaný koncový bod s událostí v těle požadavku.

Stejně jako mnoho jiných služeb, které podporují webhooky, Event Grid vyžaduje, abyste prokázat vlastnictví koncového bodu Webhook u tohoto koncového bodu před tím, než začne doručovat události do tohoto koncového bodu. Tento požadavek zabraňuje uživateli se zlými úmysly zaplavit koncový bod událostmi. Když použijete některou ze tří níže uvedených služeb Azure, infrastruktura Azure toto ověření automaticky zpracovává:

* Aplikace logiky Azure s [konektorem mřížky událostí](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation přes [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Funkce Azure s [aktivační událostí](../azure-functions/functions-bindings-event-grid.md)

Pokud používáte jiný typ koncového bodu, jako je například funkce Azure založená na aktivační události HTTP, musí se váš kód koncového bodu účastnit ověřovacího handshake s událostí. Event Grid podporuje dva způsoby ověření předplatného.

1. **ValidationCode handshake (programový)**: Pokud řídíte zdrojový kód pro koncový bod, doporučuje me z této metody. V době vytvoření odběru události Event Grid odešle událost ověření předplatného do koncového bodu. Schéma této události je podobné jakékoli jiné události Event Grid. Datová část této události `validationCode` zahrnuje vlastnost. Aplikace ověří, zda je žádost o ověření pro očekávané odběr událostí a vrátí ověřovací kód do programu Event Grid. Tento mechanismus handshake je podporován ve všech verzích Event Grid.

2. **ValidationURL handshake (ruční)**: V některých případech nelze získat přístup ke zdrojovému kódu koncového bodu k implementaci handshake ValidCode. Pokud například používáte službu jiného výrobce (například [Zapier](https://zapier.com) nebo [IFTTT](https://ifttt.com/)), nemůžete programově odpovědět ověřovacím kódem.

   Počínaje verzí 2018-05-01-preview, Event Grid podporuje ruční ověření handshake. Pokud vytváříte odběr událostí pomocí sady SDK nebo nástroje, který používá rozhraní API verze 2018-05-01-preview nebo novější, event grid odešle `validationUrl` vlastnost v datové části události ověření předplatného. Chcete-li dokončit handshake, najít tuto adresu URL v datech události a ručně odeslat požadavek GET na něj. Můžete použít klienta REST nebo webový prohlížeč.

   Poskytnutá adresa URL je platná po dobu 5 minut. Během této doby je `AwaitingManualAction`stav zřizování předplatného události . Pokud ruční ověření nedokončíte do 5 minut, je stav `Failed`zřizování nastaven na . Před zahájením ručního ověření budete muset znovu vytvořit odběr událostí.

    Tento mechanismus ověřování také vyžaduje, aby koncový bod webhooku vrátil stavový kód HTTP 200, aby věděl, že post pro ověřovací událost byla přijata před tím, než může být uvedena v režimu ručního ověřování. Jinými slovy pokud koncový bod vrátí 200, ale nevrátí zpět odpověď ověření programově, režim je převeden do režimu ručníověření. Pokud je GET na ověřovací adresu URL do 5 minut, ověření handshake se považuje za úspěšný.

> [!NOTE]
> Použití certifikátů podepsaných svým držitelem pro ověření není podporováno. Místo toho použijte podepsaný certifikát od certifikační autority .

### <a name="validation-details"></a>Podrobnosti ověření

* V době vytvoření/aktualizace odběru událostí event grid uspěje událost potvrzení události do cílového koncového bodu. 
* Událost obsahuje hodnotu záhlaví "aeg-event-type: SubscriptionValidation".
* Tělo události má stejné schéma jako ostatní události event gridu.
* Vlastnost eventType události je `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Vlastnost data události zahrnuje `validationCode` vlastnost s náhodně generovaným řetězcem. Například "validationCode: acb13...".
* Data události také `validationUrl` obsahuje vlastnost s adresou URL pro ruční ověření předplatného.
* Pole obsahuje pouze událost ověření. Další události jsou odesílány v samostatném požadavku po echo zpět ověřovací kód.
* Sady SDK SDK eventgrid dataplane mají třídy odpovídající datům událostí ověření předplatného a odpovědi na ověření předplatného.

Příklad SubscriptionValidationEvent je uveden v následujícím příkladu:

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

Chcete-li prokázat vlastnictví koncového bodu, echo zpět ověřovací kód ve vlastnosti validationResponse, jak je znázorněno v následujícím příkladu:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Je nutné vrátit stavový kód odpovědi HTTP 200 OK. Přijatý protokol HTTP 202 není rozpoznán jako platná odpověď na ověření odběru služby Event Grid. Požadavek http musí být dokončen do 30 sekund. Pokud operace neskončí do 30 sekund, bude operace zrušena a může být po 5 sekundách znovu. Pokud všechny pokusy nezdaří, pak bude považován za ověření handshake chyba.

Nebo můžete ručně ověřit odběr odesláním požadavku GET na ověřovací adresu URL. Odběr událostí zůstane ve stavu čekající na vyřízení, dokud nebude ověřeno. Ověřovací adresa URL používá port 553. Pokud pravidla brány firewall blokují port 553, může být nutné aktualizovat pravidla pro úspěšné ruční handshake.

Příklad zpracování ověření ověření předplatného handshake naleznete v [ukázce C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Kontrolní seznam

Během vytváření odběru událostí, pokud se zobrazí chybová zpráva, jako je například\/"Pokus o ověření zadaný koncový bod https: /your-endpoint-here se nezdařilo. Další podrobnosti naleznete\/na adrese https: /aka.ms/esvalidation", označuje, že došlo k selhání v ověření handshake. Chcete-li tuto chybu vyřešit, ověřte následující aspekty:

* Řídíte kód aplikace spuštěný v cílovém koncovém bodě? Například pokud píšete funkci Azure založené na aktivační události HTTP, máte přístup ke kódu aplikace k provedení změn?
* Pokud máte přístup ke kódu aplikace, implementujte mechanismus handshake založený na validačním kódu, jak je znázorněno na výše uvedené mase.

* Pokud nemáte přístup ke kódu aplikace (například pokud používáte službu jiného výrobce, která podporuje webhooky), můžete použít ruční mechanismus handshake. Ujistěte se, že používáte 2018-05-01-preview api verze nebo novější (nainstalovat rozšíření Rozhraní příkazového příkazu Event Grid Azure) pro příjem ověřovací url v události ověření. Chcete-li dokončit ruční ověření handshake, `validationUrl` získejte hodnotu vlastnosti a navštivte tuto adresu URL ve webovém prohlížeči. Pokud je ověření úspěšné, měli byste ve webovém prohlížeči zobrazit zprávu, že ověření je úspěšné. Uvidíte, že zřizování služby předplatného událostí je "Úspěšné". 

### <a name="event-delivery-security"></a>Zabezpečení doručení události

#### <a name="azure-ad"></a>Azure AD

Koncový bod webhooku můžete zabezpečit pomocí služby Azure Active Directory k ověření a autorizaci služby Event Grid k publikování událostí do koncových bodů. Budete muset vytvořit aplikaci Azure Active Directory, vytvořit princip role a služby ve vaší aplikaci autorizace Event Grid a nakonfigurovat odběr událostí pro použití aplikace Azure AD. [Přečtěte si, jak nakonfigurovat akusta pomocí programu Event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Parametry dotazu
Koncový bod webhooku můžete zabezpečit přidáním parametrů dotazu do adresy URL webhooku při vytváření odběru událostí. Nastavte jeden z těchto parametrů dotazu jako tajný klíč, například [přístupový token](https://en.wikipedia.org/wiki/Access_token). Webhook uvidíte tajný klíč k rozpoznání události přicházející z event gridu s platnými oprávněními. Event Grid bude obsahovat tyto parametry dotazu v každém doručení události do webhooku.

Při úpravách odběru událostí se parametry dotazu nezobrazí nebo nevrátí, pokud se v rozhraní [příkazového řádku](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)Azure nepoužije parametr [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) .

Nakonec je důležité si uvědomit, že Azure Event Grid podporuje jenom koncové body https webhooku.

## <a name="event-subscription"></a>Předplatné události

Chcete-li se přihlásit k odběru události, musíte prokázat, že máte přístup ke zdroji události a obslužné rutině. Prokázání, že vlastníte WebHook byl popsán v předchozí části. Pokud používáte obslužnou rutinu události, která není WebHook (například centrum událostí nebo úložiště fronty), budete potřebovat přístup pro zápis k tomuto prostředku. Tato kontrola oprávnění zabrání neoprávněnému uživateli v odesílání událostí do vašeho prostředku.

Musíte mít **oprávnění Microsoft.EventGrid/EventSubscriptions/Write** u prostředku, který je zdrojem události. Toto oprávnění potřebujete, protože píšete nové předplatné v oboru prostředku. Požadovaný prostředek se liší v závislosti na tom, zda se přihlašujete k odběru systémového tématu nebo vlastního tématu. Oba typy jsou popsány v této části.

### <a name="system-topics-azure-service-publishers"></a>Témata systému (vydavatelé služeb Azure)

Pro systémová témata potřebujete oprávnění k napsání nového odběru událostí v rozsahu prostředku, který událost publikuje. Formát prostředku je:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Chcete-li se například přihlásit k odběru události na účtu úložiště s názvem **myacct**, potřebujete oprávnění Microsoft.EventGrid/EventSubscriptions/Write pro:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Vlastní témata

Pro vlastní témata potřebujete oprávnění k napsání nového odběru událostí v rámci tématu mřížky událostí. Formát prostředku je:`/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Chcete-li se například přihlásit k odběru vlastního tématu s názvem **mytopic**, potřebujete oprávnění Microsoft.EventGrid/EventSubscriptions/Write pro:`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikování vlastního tématu

Vlastní témata používají buď sdílený přístupový podpis (SAS), nebo ověřování pomocí klíče. Doporučujeme SAS, ale ověřování pomocí klíče poskytuje jednoduché programování a je kompatibilní s mnoha existujícími vydavateli webhooku. 

Hodnotu ověřování zahrnete do hlavičky HTTP. Pro SAS použijte **aeg-sas-token** pro hodnotu záhlaví. Pro ověřování pomocí klíče použijte pro hodnotu záhlaví **aeg-sas-key.**

### <a name="key-authentication"></a>Ověřování klíčů

Ověřování pomocí klíče je nejjednodušší forma ověřování. Použijte formát:`aeg-sas-key: <your key>`

Například předáte klíč s:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny SAS

Tokeny SAS pro event grid zahrnují prostředek, čas vypršení platnosti a podpis. Formát tokenu SAS je: `r={resource}&e={expiration}&s={signature}`.

Prostředek je cesta k tématu mřížky událostí, do kterého odesíláte události. Platná cesta k prostředku je například:`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Vygenerujete podpis z klíče.

Například platná hodnota **aeg-sas-token** je:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

Následující příklad vytvoří token SAS pro použití s mřížkou událostí:

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

Všechny události nebo data zapsaná na disk službou Event Grid jsou šifrována klíčem spravovaným společností Microsoft, který zajišťuje, že je šifrován v klidovém stavu. Maximální doba, po kterou jsou události nebo data uchována, je navíc 24 hodin v souladu se [zásadami opakování mřížky událostí](delivery-and-retry.md). Event Grid automaticky odstraní všechny události nebo data po 24 hodinách, nebo čas události-k-live, podle toho, co je méně.

## <a name="next-steps"></a>Další kroky

* Úvod do mřížky událostí najdete v tématu [O mřížce událostí](overview.md)

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
ms.openlocfilehash: 0b7c5b42ac6291c6687337ba8d6a9d35830b9bda
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281012"
---
# <a name="authenticating-access-to-event-grid-resources"></a>Ověřování přístupu k prostředkům Event Grid

Azure Event Grid má tři typy ověřování:

* Doručování událostí Webhooku
* Odběry událostí
* Publikování vlastního tématu

## <a name="webhook-event-delivery"></a>Doručování událostí Webhooku

Webhooky jsou jedním z mnoha způsoby příjem událostí ze služby Azure Event Grid. Při vytvoření nové události je připraven, služby Event Grid odešle požadavek HTTP na konfigurovaný koncový bod s událostí v textu požadavku.

Stejně jako mnoho dalších služeb, které podporují webhooky služby Event Grid vyžaduje, abyste prokázali vlastnictví váš koncový bod Webhooku před spuštěním doručování událostí do tohoto koncového bodu. Tento požadavek uživatel se zlými úmysly zabraňuje zahlcení váš koncový bod s událostmi. Když použijete některou z níže uvedených tří služby Azure, infrastruktura Azure automaticky zpracovává toto ověření:

* Azure Logic Apps s [konektorem Event Grid](https://docs.microsoft.com/connectors/azureeventgrid/)
* Azure Automation prostřednictvím [Webhooku](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
* Azure Functions s [triggerem Event Grid](../azure-functions/functions-bindings-event-grid.md)

Pokud používáte jiný typ koncového bodu, jako například aktivační událost HTTP na základě funkcí Azure, musí koncový bod kódu účastnit handshake ověření pomocí služby Event Grid. Event Grid podporuje dva způsoby ověření předplatného.

1. **ValidationCode handshake (program)** : Pokud řídíte zdrojový kód pro koncový bod, doporučuje se tato metoda. V době vytvoření odběru událostí služby Event Grid odešle událost ověření předplatného do vašeho koncového bodu. Schéma této události je podobně jako ostatní události Event gridu. Datová část této události obsahuje vlastnost `validationCode`. Vaše aplikace ověřuje, že žádost o ověření pro předplatné Očekávaná událost a vrátí kód pro ověření do služby Event Grid. Tento mechanismus handshake je podporováno ve všech verzích služby Event Grid.

2. **ValidationURL handshake (ruční)** : v některých případech nebudete mít přístup ke zdrojovému kódu koncového bodu pro implementaci metody handshake ValidationCode. Například pokud používáte službu třetí strany (například [Zapier](https://zapier.com) nebo [IFTTT](https://ifttt.com/)), nemůžete programově reagovat pomocí ověřovacího kódu.

   Počínaje verzí 2018-05-01-preview, podporuje Služba Event Grid handshake ruční ověření. Pokud vytváříte odběr událostí pomocí sady SDK nebo nástroje používající rozhraní API verze 2018-05-01-Preview nebo novější, Event Grid v datové části události ověření předplatného odeslat vlastnost `validationUrl`. Dokončete signalizace najít tuto adresu URL v datech událostí a ručně odeslat požadavek GET. Můžete použít klienta REST nebo ve webovém prohlížeči.

   Zadaná adresa URL je platná 5 minut. Během této doby je stav zřizování odběru události `AwaitingManualAction`. Pokud nedokončíte ruční ověření během 5 minut, stav zřizování je nastaven na `Failed`. Budete muset vytvořit odběr události znovu před zahájením ruční ověření.

    Tento mechanismus ověřování také vyžaduje, aby koncový bod Webhooku vrátil stavový kód HTTP 200, aby věděl, že příspěvek pro událost ověření byl přijat předtím, než mohl být vložen do režimu ručního ověření. Jinými slovy, pokud koncový bod vrátí 200, ale nevrátí zpět odpověď na ověření programově, režim se převede do režimu ručního ověření. Pokud se v ověřovací adrese URL objeví během 5 minut, považuje se ověřovací metoda handshake za úspěšnou.

> [!NOTE]
> Použití certifikátů podepsaných svým držitelem k ověření se nepodporuje. Místo toho použijte podepsaný certifikát od certifikační autority (CA).

### <a name="validation-details"></a>Podrobnosti ověřování

* V době vytvoření/aktualizace předplatného událostí služby Event Grid odešle událost ověření odběru do cílového koncového bodu. 
* Událost obsahuje hodnotu hlavičky "Æg typu události: SubscriptionValidation".
* Text událost má stejné schéma jako ostatní události služby Event Grid.
* Vlastnost eventType události je `Microsoft.EventGrid.SubscriptionValidationEvent`.
* Vlastnost data události obsahuje vlastnost `validationCode` s náhodně generovaným řetězcem. Například "validationCode: acb13...".
* Data události také obsahují vlastnost `validationUrl` s adresou URL pro ruční ověření předplatného.
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
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
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

Je nutné vrátit stavový kód odpovědi HTTP 200 OK. Přijatý protokol HTTP 202 nebyl rozpoznán jako platná odpověď ověření předplatného Event Grid. Požadavek HTTP musí být dokončen do 30 sekund. Pokud operace nekončí do 30 sekund, operace se zruší a může se pokusit o její opakování po 5 sekundách. Pokud všechny pokusy selžou, bude zpracována jako chyba handshake ověření.

Nebo můžete ručně ověřit předplatné z odesláním požadavku GET na adresu URL pro ověření. Odběr událostí zůstane ve stavu čekající na vyřízení, dokud nebude ověření. Ověřovací adresa URL používá port 553. Pokud vaše pravidla brány firewall blokují port 553, může být potřeba aktualizovat pravidla pro úspěšnou ruční handshaki.

Příklad manipulace s ověřovací metodou handshake předplatného najdete v [ C# ukázce](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

### <a name="checklist"></a>Kontrolní seznam

Pokud se při vytváření odběru událostí zobrazuje chybová zpráva, například "pokus o ověření poskytnutého koncového bodu https:\//Your-Endpoint-here se nezdařila. Další podrobnosti najdete na adrese https:\//aka.ms/esvalidation ", což značí, že došlo k chybě ověřovací metody handshake. Chcete-li tuto chybu vyřešit, ověřte následující aspekty:

* Ovládáte kód aplikace spuštěný v cílovém koncovém bodu? Například pokud píšete triggeru HTTP na základě funkce Azure, máte přístup ke kódu aplikace provádět změny?
* Pokud máte přístup ke kódu aplikace, implementujte mechanismus handshake ValidationCode na základě, jak je znázorněno v příkladu výše.

* Pokud nemáte přístup ke kódu aplikace (například při použití služby třetí strany, která podporuje webhooky), můžete použít mechanismus ruční metodu handshake. Ujistěte se, že používáte verzi rozhraní API 2018-05-01-preview nebo novější (instalace rozšíření rozhraní příkazového řádku Azure Event Grid) pro získání validationUrl v událost ověření. Pokud chcete dokončit ruční ověření handshake, Získejte hodnotu vlastnosti `validationUrl` a navštivte tuto adresu URL ve webovém prohlížeči. Pokud je ověření úspěšné, měli byste vidět zprávu ve webovém prohlížeči, je ověřování úspěšné. Uvidíte, že stav zřizování odběr události je "bylo úspěšné". 

### <a name="event-delivery-security"></a>Zabezpečení doručování událostí

#### <a name="azure-ad"></a>Azure AD

Koncový bod Webhooku můžete zabezpečit pomocí Azure Active Directory k ověřování a autorizaci Event Grid k publikování událostí do koncových bodů. Budete muset vytvořit aplikaci Azure Active Directory, vytvořit zásadu role a služby v aplikaci pro autorizaci Event Grid a nakonfigurovat odběr událostí tak, aby používal aplikaci Azure AD. [Naučte se konfigurovat AAD pomocí Event Grid](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>Parametry dotazu
Váš koncový bod webhooku můžete zabezpečit přidáním parametrů dotazu na adresu URL webhooku, při vytváření odběru událostí. Nastavte jeden z těchto parametrů dotazu na tajný klíč, jako je [přístupový token](https://en.wikipedia.org/wiki/Access_token). Webhook můžete tajný kód rozpoznal, že události pocházejí ze služby Event Grid s platná oprávnění. Event Grid bude obsahovat tyto parametry dotazu v každé doručování událostí k webhooku.

Při úpravách odběru události se parametry dotazu nezobrazí ani nevrátí, pokud se v rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)používá parametr [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) .

A konečně je důležité si uvědomit, že služby Azure Event Grid podporuje jenom koncové body HTTPS webhooku.

## <a name="event-subscription"></a>Odběr události

K odběru události, musíte prokázat, že máte přístup ke zdroji události a obslužné rutiny. Prokázání, že vlastníte Webhooku byla popsané v předchozí části. Pokud používáte obslužnou rutinu události, která není Webhooku (například služby event hubu nebo služby queue storage), potřebujete přístup pro zápis do tohoto prostředku. Tato kontrola oprávnění zabrání neoprávněný uživatel možnost odesílat události do vašeho prostředku.

Musíte mít oprávnění **Microsoft. EventGrid/EventSubscriptions/Write** pro prostředek, který je zdrojem událostí. Budete potřebovat toto oprávnění, protože psaní si nové předplatné v rámci prostředku. Požadovaný prostředek se liší podle toho, jestli jste už přihlášení k odběru téma system nebo vlastního tématu. Oba typy jsou popsány v této části.

### <a name="system-topics-azure-service-publishers"></a>Témata systém (vydavateli služby Azure)

Témata systému budete potřebovat oprávnění k zápisu nového předplatného událostí v oboru prostředků publikování události. Formát prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Například pro přihlášení k odběru události v účtu úložiště s názvem **ucet**potřebujete oprávnění Microsoft. EventGrid/EventSubscriptions/Write: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Vlastní témata

Vlastní témata budete potřebovat oprávnění k zápisu nového předplatného událostí v oboru téma event gridu. Formát prostředku je: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Například pro přihlášení k odběru vlastního tématu s názvem **mytopic**potřebujete oprávnění Microsoft. EventGrid/EventSubscriptions/Write na: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="custom-topic-publishing"></a>Publikování vlastního tématu

Vlastní témata pomocí sdíleného přístupového podpisu (SAS) nebo ověřování pomocí klíče. Doporučujeme, abyste SAS, ale ověření pomocí klíče poskytuje jednoduché programování a je kompatibilní s mnoha existujících vydavatelů webhooku. 

Hodnota ověřování je zahrnout v hlavičce protokolu HTTP. Pro SAS použijte **AEG-SAS-token** pro hodnotu hlavičky. Pro ověřování pomocí klíče použijte **AEG-SAS-Key** pro hodnotu hlavičky.

### <a name="key-authentication"></a>Ověření pomocí klíče

Ověření pomocí klíče je nejjednodušší formu ověřování. Použijte formát: `aeg-sas-key: <your key>`

Například předáte klíč s:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny SAS

Tokeny SAS pro Event Grid zahrnují prostředek, čas vypršení platnosti a podpisu. Formát tokenu SAS je: `r={resource}&e={expiration}&s={signature}`.

Prostředek je cesta k tématu event gridu, do kterého odesíláte události. Platná cesta k prostředku je například: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Generování podpisu z klíče.

Platná hodnota **AEG-SAS-token** je například:

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

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Všechny události nebo data zapsaná na disk službou Event Grid službou jsou šifrovány klíčem spravovaným společností Microsoft, který zajišťuje, aby byl zašifrovaný v klidovém stavu. Kromě toho maximální doba, po kterou jsou události nebo data uchovávány, je 24 hodin v dodržování [zásad Event Grid opakování](delivery-and-retry.md). Event Grid budou automaticky odstraňovat všechny události nebo data po 24 hodinách nebo na hodnotu TTL (Time to Live), podle toho, co je méně.

## <a name="next-steps"></a>Další kroky

* Úvod do Event Grid najdete v tématu [o Event Grid](overview.md)

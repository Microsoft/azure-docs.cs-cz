---
title: Azure Event Grid zabezpečení a ověřování
description: Tento článek popisuje různé způsoby ověřování přístupu k prostředkům Event Grid (Webhook, odběry, vlastní témata).
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: spelluru
ms.openlocfilehash: d028367b82e8529d5260c086f2e4afa609582b00
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100235"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Ověřování přístupu k prostředkům Azure Event Grid
Tento článek poskytuje informace o následujících scénářích:  

- Ověřování klientů, kteří publikují události pro Azure Event Grid témat pomocí sdíleného přístupového podpisu (SAS) nebo klíče. 
- Zabezpečte koncový bod Webhooku, který se používá k přijímání událostí z Event Grid pomocí Azure Active Directory (Azure AD) nebo sdíleného tajného klíče.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>Ověřování klientů publikování pomocí SAS nebo klíče
Vlastní témata používají buď sdílený přístupový podpis (SAS), nebo ověřování pomocí klíče. Doporučujeme použít SAS, ale ověřování pomocí klíče poskytuje jednoduché programování a je kompatibilní s mnoha existujícími vydavateli webhooků.

Do hlavičky HTTP zadáte hodnotu ověřování. Pro SAS použijte **AEG-SAS-token** pro hodnotu hlavičky. Pro ověřování pomocí klíče použijte **AEG-SAS-Key** pro hodnotu hlavičky.

### <a name="key-authentication"></a>Ověřování klíčů

Ověřování klíčem je nejjednodušší forma ověřování. Použijte formát: `aeg-sas-key: <your key>` v záhlaví zprávy.

Například můžete předat klíč pomocí:

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Můžete také zadat `aeg-sas-key` jako parametr dotazu. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Tokeny SAS

Tokeny SAS pro Event Grid zahrnují prostředek, čas vypršení platnosti a podpis. Formát tokenu SAS je: `r={resource}&e={expiration}&s={signature}` .

Prostředek je cesta k tématu Event gridu, do kterého odesíláte události. Například platná cesta prostředku je: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01` . Pokud chcete zobrazit všechny podporované verze rozhraní API, přečtěte si téma [typy prostředků Microsoft. EventGrid](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

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

## <a name="use-system-assigned-identities-for-event-delivery"></a>Použití identit přiřazených systémem pro doručování událostí
Pro téma nebo doménu můžete povolit spravovanou identitu přiřazenou systémem a tuto identitu použít k přeposílání událostí do podporovaných cílů, jako jsou Service Bus fronty a témata, centra událostí a účty úložiště.

Postupujte takto: 

1. Vytvořte téma nebo doménu s identitou přiřazenou systémem, nebo aktualizujte existující téma nebo doménu, aby bylo možné identitu povolit. 
1. Přidejte identitu do příslušné role (například Service Bus odesílatel dat) v cíli (například Service Bus Queue).
1. Při vytváření odběrů událostí povolte použití identity k doručování událostí do cíle. 

Podrobné pokyny najdete v tématu [doručování událostí se spravovanou identitou](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Ověřování doručování událostí do koncových bodů Webhooku
Následující části popisují, jak ověřit doručování událostí do koncových bodů Webhooku. Je nutné použít ověřovací mechanismus handshake bez ohledu na použitou metodu. Podrobnosti najdete v tématu věnovaném [doručování událostí Webhooku](webhook-event-delivery.md) . 


### <a name="using-azure-active-directory-azure-ad"></a>Použití Azure Active Directory (Azure AD)
Koncový bod Webhooku, který se používá k přijímání událostí z Event Grid, můžete zabezpečit pomocí Azure AD. Budete muset vytvořit aplikaci Azure AD, vytvořit roli a instanční objekt v aplikaci s autorizací Event Grid a nakonfigurovat odběr událostí tak, aby používal aplikaci Azure AD. Naučte se, jak [nakonfigurovat Azure Active Directory s](secure-webhook-delivery.md)využitím Event Grid.

### <a name="using-client-secret-as-a-query-parameter"></a>Použití tajného klíče klienta jako parametru dotazu
Koncový bod Webhooku můžete také zabezpečit přidáním parametrů dotazu do cílové adresy URL Webhooku zadané jako součást vytvoření odběru události. Nastavte jeden z parametrů dotazu na tajný klíč klienta, jako je [přístupový token](https://en.wikipedia.org/wiki/Access_token) nebo sdílený tajný klíč. Služba Event Grid zahrnuje všechny parametry dotazu v každé žádosti o doručení události Webhooku. Služba Webhooku může načíst a ověřit tajný klíč. Pokud se tajný klíč klienta aktualizuje, musí se taky aktualizovat předplatné událostí. Aby nedocházelo k chybám při obnově v tajnosti, zajistěte, aby Webhook přijal staré i nové tajné klíče po omezené době před aktualizací odběru událostí novým tajným klíčem. 

Vzhledem k tomu, že parametry dotazu můžou obsahovat klientské tajné klíče, jsou zpracovávány s mimořádnou opatrností. Jsou uložené jako šifrované a nejsou k dispozici pro obsluhu služeb. Neprotokolují se jako součást protokolů/trasování služby. Při načítání vlastností odběru událostí nejsou ve výchozím nastavení vráceny cílové parametry dotazu. Příklad: parametr [--include-Full-Endpoint-URL](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) se používá v rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

Další informace o doručování událostí do webhooků najdete v tématu [doručování událostí Webhooku](webhook-event-delivery.md) .

> [!IMPORTANT]
Azure Event Grid podporuje pouze koncové body Webhooku **protokolu HTTPS** . 

## <a name="next-steps"></a>Další kroky

- Úvod do Event Grid najdete v tématu [o Event Grid](overview.md)

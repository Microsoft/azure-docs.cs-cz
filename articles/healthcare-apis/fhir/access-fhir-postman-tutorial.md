---
title: Server po FHIR v Azure – Azure API pro FHIR
description: V tomto kurzu Vás provedeme kroky potřebnými k použití metody post pro přístup k serveru FHIR. Post je užitečné pro ladění aplikací, které přistupují k rozhraním API.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 03/26/2021
ms.openlocfilehash: 59847f745037acec47415489cdf61d119a7807af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936270"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Přístup k rozhraní API Azure pro FHIR s využitím post

Klientská aplikace může získat přístup k rozhraní Azure API pro FHIR prostřednictvím [REST API](https://www.hl7.org/fhir/http.html). Chcete-li odesílat žádosti, zobrazovat odpovědi a ladit aplikaci při sestavování, použijte nástroj pro testování rozhraní API podle vašeho výběru. V tomto kurzu Vás provedeme postupem přístupu k serveru FHIR pomocí [metody post](https://www.getpostman.com/). 

## <a name="prerequisites"></a>Požadavky

- Koncový bod FHIR v Azure. 

  Pokud chcete nasadit rozhraní API Azure pro FHIR (spravované služby), můžete použít [Azure Portal](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md)nebo rozhraní příkazového [řádku Azure](fhir-paas-cli-quickstart.md).

- Registrovaná [aplikace důvěrného klienta](register-confidential-azure-ad-client-app.md) pro přístup ke službě FHIR.
- Pro přístup ke službě FHIR jste udělili oprávnění k aplikaci důvěrného klienta, například "FHIR data Přispěvatel". Další informace najdete v tématu [Konfigurace služby Azure RBAC pro FHIR](./configure-azure-rbac.md).
- Po instalaci. 
    
  Další informace o nástroji pro odesílání najdete v tématu [Začínáme s post](https://www.getpostman.com).

## <a name="fhir-server-and-authentication-details"></a>FHIR Server a podrobnosti ověřování

Chcete-li použít metodu post, jsou vyžadovány následující parametry ověřování:

- Adresa URL vašeho serveru FHIR, například `https://MYACCOUNT.azurehealthcareapis.com`

- Poskytovatel identity `Authority` pro váš server FHIR, například `https://login.microsoftonline.com/{TENANT-ID}`

- Nakonfigurovaná `audience` , což je obvykle adresa URL serveru FHIR, například `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` nebo `https://azurehealthcareapis.com` .

- `client_id`ID aplikace v [důvěrné klientské aplikaci](register-confidential-azure-ad-client-app.md) , která se používá pro přístup ke službě FHIR.

- `client_secret`Tajný klíč aplikace v důvěrné klientské aplikaci.

Nakonec byste měli ověřit, že `https://www.getpostman.com/oauth2/callback` je registrovaná adresa URL odpovědi pro vaši klientskou aplikaci.

## <a name="connect-to-fhir-server"></a>Připojení k serveru FHIR

Otevřete post a pak výběrem **načíst** vytvořte žádost na `https://fhir-server-url/metadata` .

![Příkaz schopnosti metadat pro odeslání](media/tutorial-postman/postman-metadata.png)

Adresa URL metadat pro Azure API pro FHIR je `https://MYACCOUNT.azurehealthcareapis.com/metadata` . 

V tomto příkladu je adresa URL serveru FHIR `https://fhirdocsmsft.azurewebsites.net` a příkaz Možnosti serveru je k dispozici na adrese `https://fhirdocsmsft.azurewebsites.net/metadata` . Tento koncový bod je přístupný bez ověřování.

Pokud se pokusíte o přístup k prostředkům s omezeným přístupem, dojde k odpovědi ověřování se nezdařilo.

![Ověřování se nezdařilo](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Získání přístupového tokenu
Vyberte **Get New Access Token** (Získat nový přístupový token).

Pokud chcete získat platný přístupový token, vyberte **autorizace** a v rozevírací nabídce **typ** vyberte **OAuth 2,0** .

![Nastavte OAuth 2,0](media/tutorial-postman/postman-select-oauth2.png)

Vyberte **Get New Access Token** (Získat nový přístupový token).

![Požádat o nový přístupový token](media/tutorial-postman/postman-request-token.png)

V dialogovém okně **Načíst nový přístupový token** zadejte následující podrobnosti:

| Pole                 | Příklad hodnoty                                                                                                   | Komentář                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Název tokenu            | MYTOKEN                                                                                                         | Název, který zvolíte          |
| Typ udělení            | Autorizační kód                                                                                              |                            |
| Adresa URL zpětného volání          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| Ověřovací adresa URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` je `https://MYACCOUNT.azurehealthcareapis.com` pro Azure API pro FHIR |
| Adresa URL přístupového tokenu      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| ID klienta             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | ID aplikace             |
| Tajný klíč klienta         | `XXXXXXXX`                                                                                                      | Tajný klíč klienta          |
| Obor | `<Leave Blank>` | Obor se nepoužívá. Proto může být ponecháno prázdné.  
| Stav                 | `1234`     | [Stav](https://learning.postman.com/docs/sending-requests/authorization/) je neprůhledná hodnota, která zabraňuje padělání požadavků mezi weby. Je volitelný a může mít libovolnou hodnotu, například ' 1234 '.                           |
| Ověření klienta | Poslat přihlašovací údaje klienta v těle                                                                                 |                 

Vyberte **token žádosti** , který bude provedený pomocí Azure Active Directoryho toku ověřování a token se vrátí do post. Pokud dojde k chybě ověřování, přečtěte si další podrobnosti v konzole pro odesílání. **Poznámka**: na pásu karet vyberte **Zobrazit** a pak vyberte **Zobrazit konzolu pro publikování**. Klávesová zkratka pro konzolu pro odesílání je **ALT + CTRL + C**.

Posuňte se dolů, abyste zobrazili obrazovku vráceného tokenu, a pak vyberte **použít token**.

![Použít token](media/tutorial-postman/postman-use-token.png)

Pokud chcete zobrazit nově vyplněný token, podívejte se do pole **přístupový token** . Pokud vyberete **Odeslat** a zopakuje `Patient` hledání prostředků, vrátí se **stav** `200 OK` . Vrácený stav `200 OK` indikuje úspěšný požadavek HTTP.

![200 OK](media/tutorial-postman/postman-200-OK.png)

V příkladech *hledání pacienta* nejsou v databázi žádné pacienty, aby byl výsledek hledání prázdný.

Přístupový token můžete zkontrolovat pomocí nástroje, jako je [JWT.MS](https://jwt.ms). Příklad obsahu je uveden níže.

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

V situacích řešení potíží se ověřováním, že máte správnou cílovou skupinu ( `aud` deklarace identity), je dobrým místem, kde začít. Pokud váš token pochází ze správného vystavitele ( `iss` deklarace identity) a má správnou cílovou skupinu ( `aud` deklarace identity), ale stále nemůžete získat přístup k rozhraní FHIR API, je pravděpodobný, že uživatel nebo instanční objekt ( `oid` deklarace identity) nemá přístup k rovině dat FHIR. K přiřazení rolí roviny dat uživatelům doporučujeme použít [řízení přístupu na základě role Azure (Azure RBAC)](configure-azure-rbac.md) . Pokud pro vaši rovinu dat používáte externího sekundárního tenanta Azure Active Directory, budete muset [nakonfigurovat místní RBAC pro přiřazení FHIR](configure-local-rbac.md) .

Je také možné získat token pro [rozhraní Azure API pro FHIR pomocí Azure CLI](get-healthcare-apis-access-token-cli.md). Pokud používáte token získaný v rozhraní příkazového řádku Azure CLI, měli byste použít *token nosiče* typu autorizace. Přímo vložte token.

## <a name="inserting-a-patient"></a>Vkládání pacienta

Pomocí platného přístupového tokenu teď můžete vložit nového pacienta. V části autor změňte metodu tak, že vyberete **post** a v těle žádosti přidáte následující dokument JSON.

[!code-json[](../samples/sample-patient.json)]

Vyberte **Odeslat** a určete tak, že pacient byl úspěšně vytvořen.

![Snímek obrazovky, který ukazuje, že pacient byl úspěšně vytvořen.](media/tutorial-postman/postman-patient-created.png)

Pokud hledání v pacientech zopakujete, měli byste teď vidět záznam pacienta.

![Vytvořený pacient](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste získali rozhraní Azure API pro FHIR pomocí post. Další informace o funkcích rozhraní API Azure pro FHIR najdete v tématu.
 
>[!div class="nextstepaction"]
>[Podporované funkce](fhir-features-supported.md)

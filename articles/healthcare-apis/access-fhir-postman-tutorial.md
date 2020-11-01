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
ms.date: 02/07/2019
ms.openlocfilehash: 37086a925d65a80f219c8e0a3358c834a2691701
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145646"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Přístup k rozhraní API Azure pro FHIR s využitím post

Klientská aplikace by měla přístup k rozhraní FHIR API prostřednictvím [REST API](https://www.hl7.org/fhir/http.html). Můžete také při sestavování aplikací pracovat přímo se serverem FHIR, například pro účely ladění. V tomto kurzu Vás provedeme kroky potřebnými k použití [metody post](https://www.getpostman.com/) pro přístup k serveru FHIR. Publikování je nástroj, který se často používá pro ladění při sestavování aplikací, které přistupují k rozhraním API.

## <a name="prerequisites"></a>Předpoklady

- Koncový bod FHIR v Azure. Můžete ho nastavit pomocí spravovaného rozhraní API Azure pro FHIR nebo open source serveru FHIR pro Azure. Nastavte spravované rozhraní API Azure pro FHIR pomocí [Azure Portal](fhir-paas-portal-quickstart.md), [PowerShellu](fhir-paas-powershell-quickstart.md)nebo rozhraní příkazového [řádku Azure CLI](fhir-paas-cli-quickstart.md).
- [Klientská aplikace](register-confidential-azure-ad-client-app.md) , kterou budete používat pro přístup ke službě FHIR
- Po instalaci. Můžete si ho stáhnout z [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>FHIR Server a podrobnosti ověřování

Aby bylo možné použít metodu post, jsou potřeba následující podrobnosti:

- Adresa URL vašeho serveru FHIR, například `https://MYACCOUNT.azurehealthcareapis.com`
- Poskytovatel identity `Authority` pro váš server FHIR, například `https://login.microsoftonline.com/{TENANT-ID}`
- Nakonfigurované `audience` . To je obvykle adresa URL serveru FHIR, např. `https://MYACCOUNT.azurehealthcareapis.com` nebo pouze `https://azurehealthcareapis.com` .
- `client_id`(Nebo ID aplikace) [klientské aplikace](register-confidential-azure-ad-client-app.md) , kterou budete používat pro přístup ke službě FHIR.
- `client_secret`(Nebo tajný klíč aplikace) klientské aplikace.

Nakonec byste měli ověřit, že `https://www.getpostman.com/oauth2/callback` je registrovaná adresa URL odpovědi pro vaši klientskou aplikaci.

## <a name="connect-to-fhir-server"></a>Připojení k serveru FHIR

Pomocí post proveďte `GET` požadavek na `https://fhir-server-url/metadata` :

![Příkaz schopnosti metadat pro odeslání](media/tutorial-postman/postman-metadata.png)

Adresa URL metadat pro Azure API pro FHIR je `https://MYACCOUNT.azurehealthcareapis.com/metadata` . V tomto příkladu je adresa URL serveru FHIR `https://fhirdocsmsft.azurewebsites.net` a příkaz Možnosti serveru je k dispozici na adrese `https://fhirdocsmsft.azurewebsites.net/metadata` . Tento koncový bod by měl být přístupný bez ověřování.

Pokud se pokusíte o přístup k prostředkům s omezeným přístupem, měla by se zobrazit odpověď "ověření se nezdařilo:

![Ověřování se nezdařilo](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Získání přístupového tokenu

Pokud chcete získat platný přístupový token, vyberte "Authorization" a vyberte typ "OAuth 2,0":

![Nastavte OAuth 2,0](media/tutorial-postman/postman-select-oauth2.png)

Stiskněte tlačítko získat nový přístupový token a zobrazí se dialogové okno:

![Požádat o nový přístupový token](media/tutorial-postman/postman-request-token.png)

Budete potřebovat několik podrobností:

| Pole                 | Příklad hodnoty                                                                                                   | Komentář                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Název tokenu            | MYTOKEN                                                                                                         | Název, který zvolíte          |
| Typ udělení            | Autorizační kód                                                                                              |                            |
| Adresa URL zpětného volání          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Ověřovací adresa URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` je `https://MYACCOUNT.azurehealthcareapis.com` pro Azure API pro FHIR |
| Adresa URL přístupového tokenu      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| ID klienta             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | ID aplikace             |
| Tajný klíč klienta         | `XXXXXXXX`                                                                                                        | Tajný klíč klienta          |
| Obor | `<Leave Blank>` |
| Stav                 | `1234`                                                                                                            |                            |
| Ověření klienta | Poslat přihlašovací údaje klienta v těle                                                                                 |                 

Zajděte si "token žádosti" a budete provedeni pomocí Azure Active Directoryho toku ověřování a token se vrátí do post. Pokud narazíte na problémy, otevřete konzolu pro odesílání (z položky nabídky Zobrazit a >zobrazit na konzole pro publikování).

Posuňte se dolů na obrazovku vráceného tokenu a stiskněte tlačítko použít token:

![Použít token](media/tutorial-postman/postman-use-token.png)

Token by se teď měl naplnit v poli přístupový token a můžete vybrat tokeny z části dostupné tokeny. Pokud znovu odešlete `Patient` vyhledávání prostředků, měli byste získat stav `200 OK` :

![200 OK](media/tutorial-postman/postman-200-OK.png)

V tomto případě nejsou v databázi žádné pacienty a hledání je prázdné.

Pokud zkontrolujete přístupový token pomocí nástroje [https://jwt.ms](https://jwt.ms) , například, měl by se zobrazit obsah podobný tomuto:

```jsonc
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

V situacích řešení potíží se ověřováním, že máte správnou cílovou skupinu ( `aud` deklarace identity), je dobrým místem, kde začít. Pokud váš token pochází ze správného vystavitele ( `iss` deklarace identity) a má správnou cílovou skupinu ( `aud` deklarace identity), ale stále nemůžete získat přístup k rozhraní FHIR API, je pravděpodobný, že uživatel nebo instanční objekt ( `oid` deklarace identity) nemá přístup k rovině dat FHIR. K přiřazení rolí roviny dat uživatelům doporučujeme [použít řízení přístupu na základě role Azure (Azure RBAC)](configure-azure-rbac.md) . Pokud pro svou rovinu dat používáte externího sekundárního tenanta Azure Active Directory, budete muset [nakonfigurovat místní přiřazení RBAC](configure-local-rbac.md).

Je také možné [získat token pro rozhraní API Azure pro FHIR pomocí Azure CLI](get-healthcare-apis-access-token-cli.md). Pokud používáte token získaný v rozhraní příkazového řádku Azure CLI, měli byste použít typ ověřování "nosný token" a přímo ho vložit.

## <a name="inserting-a-patient"></a>Vkládání pacienta

Teď máte platný přístupový token. Můžete vložit nový pacient. Přepněte na metodu "POST" a do těla žádosti přidejte následující dokument JSON:

[!code-json[](samples/sample-patient.json)]

Stiskněte Odeslat a měli byste vidět, že se pacient úspěšně vytvořil:

![Snímek obrazovky, který ukazuje, že pacient byl úspěšně vytvořen.](media/tutorial-postman/postman-patient-created.png)

Pokud hledání v pacientech zopakujete, měli byste teď vidět záznam pacienta:

![Vytvořený pacient](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste získali rozhraní FHIR API pomocí post. Přečtěte si o podporovaných funkcích rozhraní API v části věnované podporovaným funkcím.
 
>[!div class="nextstepaction"]
>[Podporované funkce](fhir-features-supported.md)

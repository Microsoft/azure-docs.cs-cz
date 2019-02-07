---
title: Kurz pro Azure – rozhraní API služby Azure pro FHIR postman FHIR serveru
description: Tento článek popisuje, jak získat přístup k API FHIR pomocí nástroje Postman.
services: healthcare-apis
ms.service: healthcare-apis
ms.topic: tutorial
ms.reviewer: dseven
ms.author: mihansen
author: hansenms
ms.date: 02/07/2019
ms.openlocfilehash: 7009fa175e19ad484c83ec3a988d6c33b67fefcd
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823375"
---
# <a name="tutorial-access-fhir-api-with-postman"></a>Kurz: Přístup k rozhraní API FHIR pomocí nástroje Postman

Klientská aplikace bude přístup k API FHIR prostřednictvím [rozhraní REST API](https://www.hl7.org/fhir/http.html). Můžete také pracovat přímo se serverem FHIR, jako například vytvářet aplikace, pro účely ladění. V tomto kurzu provedeme jednotlivými kroky nutné k využití [Postman](https://www.getpostman.com/) pro přístup k serveru FHIR. Postman je nástroj často používaný pro ladění při tvorbě aplikací, které přístup k rozhraním API.

## <a name="prerequisites"></a>Požadavky

- Koncový bod FHIR v Azure. Který lze nastavit pomocí rozhraní API služby spravované Azure pro FHIR nebo otevřít FHIR zdrojového serveru pro Azure. Nastavení rozhraní API služby spravované Azure pro FHIR pomocí [webu Azure portal](fhir-paas-portal-quickstart.md), [PowerShell](fhir-paas-powershell-quickstart.md), nebo [rozhraní příkazového řádku Azure](fhir-paas-cli-quickstart.md).
- Nainstalované nástroje postman. Můžete je získat z [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>FHIR serveru a ověřovací údaje

Aby bylo možné použít nástroj Postman, budete potřebovat znát následující podrobnosti:

- Vaše FHIR adresa URL serveru, třeba `https://MYFHIRSERVICE.azurewebsites.net` nebo `https://MYACCOUNT.azurehealthcareapis.com`
- Zprostředkovatel identity `Authority` FHIR serveru, například `https://login.microsoftonline.com/{TENANT-ID}`
- Nakonfigurované `audience`. Pro rozhraní API služby Azure pro FHIR. to je `https://azurehealthcareapis.com` a pro opensourcový server FHIR pro Azure, se nastavuje v [registrace aplikace Azure AD prostředků](register-resource-azure-ad-client-app.md).
- `client_id` (Nebo ID aplikace) z [klientská aplikace](register-confidential-azure-ad-client-app.md) budete používat pro přístup ke službě FHIR.
- `client_secret` (Nebo tajný klíč aplikace) klientské aplikace.

A konečně, měli byste zkontrolovat, který `https://www.getpostman.com/oauth2/callback` je registrovaný odpověď URL pro klientské aplikace.

## <a name="connect-to-fhir-server"></a>Připojení k serveru FHIR

Použít nástroj Postman, proveďte `GET` žádat o `https://fhir-server-url/metadata`:

![Postman Metadata funkce – příkaz](media/tutorial-postman/postman-metadata.png)

V tomto příkladu je adresa URL serveru FHIR `https://fhirdocsmsft.azurewebsites.net` a možnosti příkazu serveru je k dispozici na `https://fhirdocsmsft.azurewebsites.net/metadata`. Tohoto koncového bodu musí být přístupný bez ověřování.

Při pokusu o přístup k prostředkům s omezeným přístupem, byste měli pak při reakci na "Ověření se nezdařilo":

![Ověřování se nezdařilo.](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Získání přístupového tokenu

Pokud chcete získat platný přístupový token, vybrat "Autorizace" a vyberte typ "OAuth 2.0":

![Nastavení OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Přístupů "získat nový přístupový Token" a dialogové okno se zobrazí:

![Požádat o nový přístupový Token](media/tutorial-postman/postman-request-token.png)

Budete muset některé podrobnosti:
| Pole                 | Příklad hodnoty                                                                                                   | Poznámka                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Název tokenu            | MYTOKEN                                                                                                         | Název zvoleném          |
| Typ udělení            | Autorizační kód                                                                                              |                            |
| Adresa URL pro zpětné volání          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Ověřovací adresa URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` je `https://azurehealthcareapis.com` pro rozhraní API služby Azure pro FHIR a `https://MYFHIRSERVICE.azurewebsites.net` OSS FHIR serveru |
| Adresa URL přístupového tokenu      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| ID klienta             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | ID aplikace             |
| Tajný kód klienta         | `XXXXXXXX`                                                                                                        | Klíč tajného kódu klienta.          |
| Stav                 | `1234`                                                                                                            |                            |
| Ověření klienta | Odeslat přihlašovací údaje pro klienta v textu                                                                                 |                 

Přístupů "požádat o Token" a provedeme tok ověřování Azure Active Directory a vrátí se token k Postman. Pokud narazíte na problémy otevřete konzolu nástroje Postman (z nabídky položky na zobrazení -> Konzola zobrazit Postman").

Přejděte dolů na vráceného tokenu obrazovce a klepněte na možnost "Použití tokenu":

![Použít Token](media/tutorial-postman/postman-use-token.png)

Token, který by měl nyní dosazeny "Přístupový Token" pole a vyberete tokeny od "Dostupné tokeny". Pokud je "Odeslat" znovu opakovat `Patient` vyhledávání prostředků, měli byste získat stav `200 OK`:

![200 OK](media/tutorial-postman/postman-200-OK.png)

V tomto případě v databázi nejsou žádné pacientů a vyhledávání je prázdný.

Je-li si prohlédnout přístupový token pomocí nástroje jako [ https://jwt.ms ](https://jwt.ms), měla by se zobrazit obsah, jako jsou:

```json
{
  "aud": "https://azurehealthcareapis.com",
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

Při řešení potíží situacích, ověření, že máte správné cílové skupině (`aud` deklarace identity) je dobrým začátkem. Spravované rozhraní API Azure pro účely FHIR [ID objektů identity](find-identity-object-ids.md) k omezení přístupu ke službě. Ujistěte se, že `oid` deklarací identity tokenu obsahuje ID objektu ze seznamu ID povolených objektů.

## <a name="inserting-a-patient"></a>Vkládání pacienta

Teď, když máte platným přístupovým tokenem. Můžete vložit nového pacienta. Přejděte do metody "POST" a přidejte následující dokument JSON v textu požadavku:

[!code-json[](samples/sample-patient.json)]

Spuštění "Odeslat" a měli byste vidět, že se úspěšně vytvořil pacienta:

![Pacient vytvořili](media/tutorial-postman/postman-patient-created.png)

Pokud o pacientech hledání zopakujete, měli byste vidět pacientů záznam:

![Pacient vytvořili](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu ke kterým jste FHIR API pomocí nástroje postman. Přečtěte si o podporovaných funkcích rozhraní API v Microsoft FHIR serveru pro Azure.
 
>[!div class="nextstepaction"]
>[Podporované funkce FHIR](fhir-features-supported.md)
---
title: Konfigurace přihlášení pomocí Apple (Preview)
description: Naučte se konfigurovat přihlašování pomocí Apple jako poskytovatele identity pro vaše App Service nebo Azure Functions aplikace.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603161"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Konfigurace App Service nebo Azure Functions aplikace pro přihlášení pomocí poskytovatele Apple Provider (Preview)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure App Service nebo Azure Functions k použití funkce přihlásit se pomocí Apple jako poskytovatele ověřování. 

K dokončení postupu v tomto článku musíte být zaregistrovaní v programu Apple Developer. Pokud se chcete zaregistrovat v programu Apple Developer, navštivte [Developer.Apple.com/programs/enroll](https://developer.apple.com/programs/enroll/).

> [!CAUTION]
> Když se přihlásíte pomocí Apple, zakážete tím správu App Service funkcí ověřování/autorizace pro vaši aplikaci prostřednictvím některých klientů, jako je například Azure Portal, Azure CLI a Azure PowerShell. Tato funkce spoléhá na nové prostředí API, které ve verzi Preview ještě není ve všech prostředích správy k discentrálnímu účtu.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Vytvoření aplikace na portálu pro vývojáře Apple
Na portálu pro vývojáře Apple budete muset vytvořit ID aplikace a ID služby.

1. Na portálu pro vývojáře Apple přejít na **certifikáty, identifikátory & profily**.
2. Na kartě **identifikátory** vyberte tlačítko **(+)** .
3. Na stránce **zaregistrovat nový identifikátor** zvolte **ID aplikace** a vyberte **pokračovat**. (ID aplikací zahrnují aspoň jedno ID služby.) ![Registrace nového identifikátoru aplikace na portálu pro vývojáře Apple](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. Na stránce **zaregistrovat ID aplikace** zadejte popis a ID sady a vyberte možnost **Přihlásit se pomocí Apple** ze seznamu možností. Potom vyberte **Pokračovat**. Poznamenejte si **předponu ID aplikace (ID týmu)** z tohoto kroku, budete ji potřebovat později.
![Konfigurace nového identifikátoru aplikace na portálu pro vývojáře Apple](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Zkontrolujte informace o registraci aplikace a vyberte **Registrovat**.
6. Znovu na kartě **identifikátory** vyberte tlačítko **(+)** .
![Vytvoření nového identifikátoru služby na portálu pro vývojáře Apple](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. Na stránce **zaregistrovat nový identifikátor** zvolte **ID služeb** a vyberte **pokračovat**.
![Registrace nového identifikátoru služby na portálu pro vývojáře Apple](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. Na stránce **zaregistrovat ID služby** zadejte popis a identifikátor. Popis se zobrazí uživateli na obrazovce pro vyjádření souhlasu. Identifikátor bude vaše ID klienta, které se používá při konfiguraci poskytovatele Apple s vaší aplikací App Service. Pak vyberte **Konfigurovat**.
![Zadání popisu a identifikátoru](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. V automaticky otevíraném okně nastavte ID primární aplikace na ID aplikace, které jste vytvořili dříve. V části doména zadejte doménu vaší aplikace. Pro návratovou adresu URL použijte adresu URL `<app-url>/.auth/login/apple/callback` . Například, `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Pak vyberte **Přidat** a **Uložit**.
![Zadání domény a návratové adresy URL pro registraci](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Zkontrolujte informace o registraci služby a vyberte **Uložit**.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Generování tajného klíče klienta
Apple vyžaduje, aby vývojáři aplikací vytvořili a podepsali token JWT jako hodnotu tajného klíče klienta. Pokud chcete tento tajný klíč vygenerovat, nejdřív na portálu pro vývojáře Apple vygenerujte a stáhněte privátní klíč eliptické křivky. Pak použijte tento klíč k [podepsání JWT](#sign-the-client-secret-jwt) s [určitou datovou částí](#structure-the-client-secret-jwt).

### <a name="create-and-download-the-private-key"></a>Vytvoření a stažení privátního klíče
1. Na kartě **klíče** na portálu pro vývojáře Apple zvolte **vytvořit klíč** nebo vyberte tlačítko **(+)** .
2. Na stránce **zaregistrovat novou klávesu** zadejte název klíče, zaškrtněte políčko vedle pole **Přihlásit se pomocí Apple** a vyberte **Konfigurovat**.
3. Na stránce **konfigurovat klíč** připojte klíč k primárnímu ID aplikace, které jste vytvořili dříve, a vyberte **Uložit**.
4. Dokončete vytvoření klíče tak, že potvrdíte informace a vyberete **pokračovat** a pak zkontrolujete informace a vyberete **zaregistrovat**.
5. Na stránce **Stáhnout klíč** Stáhněte klíč. Stáhne se jako `.p8` soubor (PKCS # 8) – k podepsání tokenu JWT tajného klíče klienta použijete obsah souboru.

### <a name="structure-the-client-secret-jwt"></a>Strukturování klientského tajného tokenu JWT
Apple vyžaduje, aby měl tajný klíč klienta kódování Base64 tokenu JWT. Dekódový token JWT by měl mít strukturované datové části jako v tomto příkladu:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **Sub**: ID klienta Apple (také ID služby)
- **ISS**: vaše ID týmu Apple Developer
- **AUD**: společnost Apple přijímá token, takže se jedná o cílovou skupinu.
- **exp**: za **NBF** není víc než šest měsíců.

Verze výše uvedené datové části v kódování Base64 vypadá takto: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Poznámka: Apple nepřijímá tajný klíč klienta JWTs s datem vypršení platnosti delšími než šest měsíců od data vytvoření (nebo NBF). To znamená, že budete muset svůj tajný klíč klienta otočit minimálně každých šest měsíců._

Další informace o generování a ověřování tokenů najdete v [dokumentaci pro vývojáře od společnosti Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens). 

### <a name="sign-the-client-secret-jwt"></a>Podepsat token JWT tajného klienta
`.p8`Soubor, který jste předtím stáhli, použijete k podepsání tokenu JWT tajného klienta. Tento soubor je [soubor PCKS. 8](https://en.wikipedia.org/wiki/PKCS_8) , který obsahuje privátní podpisový klíč ve formátu PEM. Existuje mnoho knihoven, které mohou vytvořit a podepsat token JWT za vás. 

Existují různé druhy Open Source knihoven, které jsou k dispozici online pro vytváření a podepisování tokenů JWT. Další informace o generování tokenů JWT naleznete v tématu jwt.io. Například jedním ze způsobů generování tajného klíče klienta je import [balíčku NuGet Microsoft. IdentityModel. Tokens](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) a spuštění malého množství kódu jazyka C# zobrazeného níže.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**: vaše ID týmu Apple Developer
- **ClientID**: ID klienta Apple (také ID služby)
- **p8key**: klíč formátu PEM – klíč lze získat otevřením `.p8` souboru v textovém editoru a zkopírováním všeho mezi `-----BEGIN PRIVATE KEY-----` i `-----END PRIVATE KEY-----` bez konců řádků.
- **keyId**: ID staženého klíče

Vrácený token je hodnota tajného klíče klienta, kterou použijete ke konfiguraci poskytovatele Apple.

> [!IMPORTANT]
> Tajný kód klienta je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte v klientské aplikaci.
>

Přidejte tajný klíč klienta jako [nastavení aplikace](./configure-common.md#configure-app-settings) pro aplikaci s použitím názvu nastavení dle vašeho výběru. Poznamenejte si tento název pro pozdější účely.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Přidání informací o poskytovateli do aplikace

> [!NOTE]
> Požadovaná konfigurace je ve formátu nového rozhraní API, který je aktuálně podporovaný jenom [konfigurací založenou na souborech (Preview)](.\app-service-authentication-how-to.md#config-file). Pomocí takového souboru budete muset postupovat podle následujících kroků.

V této části se dozvíte, jak aktualizovat konfiguraci, aby zahrnovala vaše nové IDP. Následuje příklad konfigurace.

1. V rámci `identityProviders` objektu přidejte objekt, `apple` Pokud ještě neexistuje.
2. Přiřaďte objekt k tomuto klíči s `registration` objektem, který je v něm, a volitelně `login` objekt:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. V rámci `registration` objektu nastavte na `clientId` ID klienta, které jste shromáždili.
    
    b. V rámci `registration` objektu nastavte `clientSecretSettingName` na název nastavení aplikace, kam jste uložili tajný klíč klienta.
    
    c. V rámci `login` objektu se můžete rozhodnout nastavit `scopes` pole tak, aby obsahovalo seznam oborů používaných při ověřování pomocí Applu, například "Name" a "e-mail". Pokud jsou obory nakonfigurovány, budou na obrazovce pro vyjádření souhlasu explicitně požadovány, když se uživatelé poprvé přihlásí.

Po nastavení této konfigurace jste připraveni použít svého poskytovatele Apple pro ověřování ve vaší aplikaci.

Kompletní konfigurace může vypadat jako v následujícím příkladu (kde APPLE_GENERATED_CLIENT_SECRET nastavení odkazuje na nastavení aplikace obsahující generovaný token JWT):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

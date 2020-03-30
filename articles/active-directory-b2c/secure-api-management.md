---
title: Zabezpečení rozhraní API Azure pomocí Azure Active Directory B2C
description: Zjistěte, jak používat přístupové tokeny vydané službou Azure Active Directory B2C k zabezpečení koncového bodu rozhraní API pro správu rozhraní Azure API.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186926"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Zabezpečení rozhraní API Azure pomocí Azure AD B2C

Zjistěte, jak omezit přístup k rozhraní API Azure (APIM) api na klienty, kteří se ověřili pomocí Azure Active Directory B2C (Azure AD B2C). Postupujte podle kroků v tomto článku k vytvoření a testování příchozí zásady v APIM, který omezuje přístup pouze na ty požadavky, které obsahují platný přístupový token vydaný Azure AD B2C.

## <a name="prerequisites"></a>Požadavky

Před pokračováním v krocích v tomto článku potřebujete následující prostředky:

* [Klient Azure AD B2C](tutorial-create-tenant.md)
* [Aplikace registrovaná](tutorial-register-applications.md) ve vašem tenantovi
* [Toky uživatelů vytvořené](tutorial-create-user-flows.md) ve vašem tenantovi
* [Publikovaná rozhraní API](../api-management/import-and-publish.md) ve správě rozhraní Azure API
* [Pošťák](https://www.getpostman.com/) pro testování zabezpečeného přístupu (volitelné)

## <a name="get-azure-ad-b2c-application-id"></a>Získání ID aplikace Azure AD B2C

Když zabezpečíte rozhraní API ve správě rozhraní Azure API pomocí Azure AD B2C, potřebujete několik hodnot pro [příchozí zásady,](../api-management/api-management-howto-policies.md) které vytvoříte v APIM. Nejprve zaznamenejte ID aplikace, kterou jste dříve vytvořili v tenantovi Azure AD B2C. Pokud používáte aplikaci, kterou jste vytvořili v požadavcích, použijte ID aplikace pro *webbapp1*.

K získání ID aplikace můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. V části **Správa**vyberte **Aplikace**.
1. Zaznamenejte hodnotu ve sloupci **ID aplikace** pro *webapp1* nebo jinou aplikaci, kterou jste dříve vytvořili.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (náhled)** a pak vyberte kartu **Vlastní aplikace.**
1. Zaznamenejte hodnotu ve **sloupci ID aplikace (klienta)** pro *webapp1* nebo jinou aplikaci, kterou jste dříve vytvořili.

* * *

## <a name="get-token-issuer-endpoint"></a>Získat koncový bod vystavitetokenu

Dále získejte známou adresu URL konfigurace pro jeden z vašich toků uživatelů Azure AD B2C. Potřebujete také identifikátor URI koncového bodu vystavitele tokenu, který chcete podporovat ve správě rozhraní Azure API.

1. Přejděte do svého klienta Azure AD B2C na [webu Azure Portal](https://portal.azure.com).
1. V části **Zásady**vyberte **Toky uživatelů (zásady).**
1. Vyberte existující zásadu, například *B2C_1_signupsignin1*, a pak vyberte **Spustit tok uživatele**.
1. Zaznamenejte adresu URL v hypertextovém odkazu zobrazeném pod nadpisem **Spustit tok uživatele** v horní části stránky. Tato adresa URL je openid připojit dobře známý zjišťování koncový bod pro tok uživatele a použít jej v další části při konfiguraci zásady příchozí ve správě rozhraní API Azure.

    ![Známý hypertextový odkaz URI na stránce Spustit nyní na webu Azure Portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Vyberte hypertextový odkaz a přejděte na dobře známou konfigurační stránku OpenID Connect.
1. Na stránce, která se otevře `issuer` v prohlížeči, zaznamenejte hodnotu, například:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Tuto hodnotu použijete v další části při konfiguraci rozhraní API ve správě rozhraní AZURE API.

Nyní byste měli mít dvě adresy URL zaznamenané pro použití v další části: OpenID Connect dobře známé adresy URL koncového bodu konfigurace a identifikátor URI vystavitela. Například:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Konfigurace příchozích zásad ve správě rozhraní Azure API

Teď jste připraveni přidat příchozí zásady ve správě rozhraní Azure API, která ověřuje volání rozhraní API. Přidáním [zásady ověření JWT,](../api-management/api-management-access-restriction-policies.md#ValidateJWT) která ověří publikum a vystavittele v přístupovém tokenu, můžete zajistit, že jsou přijímána pouze volání rozhraní API s platným tokenem.

1. Přejděte na instanci Azure API Management na [webu Azure Portal](https://portal.azure.com).
1. Vyberte **Rozhraní API**.
1. Vyberte rozhraní API, které chcete zabezpečit pomocí Azure AD B2C.
1. Vyberte kartu **Návrh**.
1. V části **Příchozí** ** \< / ** zpracování vyberte, chcete-li otevřít editor kódu zásad.
1. Umístěte následující `<validate-jwt>` značku `<inbound>` do zásady.

    1. Aktualizujte `url` hodnotu `<openid-config>` v prvku známou konfigurační adresou URL vaší zásady.
    1. Aktualizujte `<audience>` prvek pomocí ID aplikace aplikace, kterou jste vytvořili dříve ve vašem tenantovi B2C (například *webapp1*).
    1. Aktualizujte `<issuer>` prvek s koncovým bodem vystavitela tokenu, který jste zaznamenali dříve.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Ověření zabezpečeného přístupu rozhraní API

Chcete-li zajistit, aby k vašemu rozhraní API měli přístup jenom ověření volajících, můžete ověřit konfiguraci správy rozhraní AZURE API voláním rozhraní API pomocí [Postman](https://www.getpostman.com/).

Chcete-li volat rozhraní API, potřebujete jak přístupový token vydaný Azure AD B2C, tak klíč předplatného APIM.

### <a name="get-an-access-token"></a>Získání přístupového tokenu

Nejprve potřebujete token vydaný Azure AD B2C `Authorization` pro použití v záhlaví v Postman. Můžete získat pomocí **spustit nyní** funkce vašeho sign-up/přihlášení uživatelského toku, které byste měli vytvořit jako jeden z předpokladů.

1. Přejděte do svého klienta Azure AD B2C na [webu Azure Portal](https://portal.azure.com).
1. V části **Zásady**vyberte **Toky uživatelů (zásady).**
1. Vyberte existující tok uživatele registrace/přihlášení, například *B2C_1_signupsignin1*.
1. V **aplikaci**vyberte *webapp1*.
1. V případě adresy `https://jwt.ms`URL **odpovědi**zvolte .
1. Vyberte **spustit tok uživatele**.

    ![Spuštění stránky toku uživatelů pro registraci toku uživatelů na webu Azure Portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Dokončete proces přihlašování. Měli byste být `https://jwt.ms`přesměrováni na .
1. Zaznamenejte kódovku hodnotu tokenu zobrazenou ve vašem prohlížeči. Tuto hodnotu tokenu použijete pro hlavičku Autorizace v Pořužince.

    ![Hodnota kódovaného tokenu zobrazená na jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Klíč předplatného získání rozhraní API

Klientská aplikace (v tomto případě Postman), která volá publikované rozhraní API, musí obsahovat platný klíč předplatného správy rozhraní API ve svých požadavcích HTTP pro rozhraní API. Chcete-li získat klíč předplatného, který chcete zahrnout do požadavku HTTP Postman:

1. Přejděte na instanci služby Azure API Management na [webu Azure Portal](https://portal.azure.com).
1. Vyberte **Předplatná**.
1. Vyberte tři tečky pro **produkt: Neomezeně**, pak vyberte **Zobrazit/skrýt klávesy**.
1. Zaznamenejte **primární klíč** pro produkt. Tento klíč použijete `Ocp-Apim-Subscription-Key` pro záhlaví v požadavku HTTP v Pořužině.

![Stránka klíče předplatného s možností Zobrazit nebo skrýt klíče na webu Azure Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testování zabezpečeného volání rozhraní API

Se zaznamenaným přístupovým tokenem a klíčem předplatného APIM jste teď připraveni otestovat, jestli jste správně nakonfigurovali zabezpečený přístup k rozhraní API.

1. Vytvořte `GET` nový požadavek v [Pošťákovi](https://www.getpostman.com/). Pro adresu URL požadavku zadejte koncový bod seznamu reproduktorů rozhraní API, které jste publikovali jako jeden z předpokladů. Například:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Dále přidejte následující záhlaví:

    | Klíč | Hodnota |
    | --- | ----- |
    | `Authorization` | Kódovaná hodnota tokenu, kterou jste `Bearer ` zaznamenali dříve, s předponou (včetně mezery za "Nosič") |
    | `Ocp-Apim-Subscription-Key` | Klíč předplatného APIM, který jste zaznamenali dříve |

    Adresa URL požadavku **GET** a **záhlaví** by se měla jevit podobně jako:

    ![Pošťák uI zobrazující adresu URL požadavku GET a záhlaví](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Vyberte tlačítko **Odeslat** v Pořžovni, abyste požadavek provedli. Pokud jste vše nakonfigurovali správně, měla by vám být nabídnuta odpověď JSON se sbírkou konferenčních řečníků (zde zkrácená):

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testování nezabezpečeného volání rozhraní API

Nyní, když jste provedli úspěšný požadavek, otestujte případ selhání, abyste zajistili, že volání vašeho rozhraní API s *neplatným* tokenem budou odmítnuta podle očekávání. Jedním ze způsobů, jak provést test je přidat nebo změnit několik `GET` znaků v hodnotě tokenu, pak provést stejný požadavek jako dříve.

1. Přidejte několik znaků k hodnotě tokenu, abyste simulovali neplatný token. Například přidejte "NEPLATNÉ" k hodnotě tokenu:

    ![Záhlaví oddíl postman ui zobrazující NEPLATNÉ přidáno do tokenu](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Chcete-li požadavek provést, vyberte tlačítko **Odeslat.** S neplatným tokenem je `401` očekávaným výsledkem neoprávněný stavový kód:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Pokud se `401` zobrazí stavový kód, ověřili jste, že úspěšné požadavky na rozhraní API pro správu rozhraní Azure API můžou úspěšně žádat jenom volající s platným přístupovým tokenem vydaným Azure AD B2C.

## <a name="support-multiple-applications-and-issuers"></a>Podpora více aplikací a vydavatelů

Několik aplikací obvykle pracovat s jedním rozhraním REST API. Chcete-li povolit rozhraní API přijímat tokeny určené pro více `<audiences>` aplikací, přidejte jejich ID aplikace do prvku v příchozí zásady APIM.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Podobně pro podporu více vystavitelů tokenů, přidejte jejich identifikátory URI koncového `<issuers>` bodu do prvku v zásadách příchozí APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrace do b2clogin.com

Pokud máte rozhraní APIM API, které ověřuje tokeny vydané staršíkoncový `login.microsoftonline.com` bod, měli byste migrovat rozhraní API a aplikace, které volat používat tokeny vydané [b2clogin.com](b2clogin.md).

Tento obecný proces můžete provést fázovanou migraci:

1. Přidejte podporu v příchozí chodnících APIM pro tokeny vydané b2clogin.com i login.microsoftonline.com.
1. Aktualizujte aplikace jeden po druhém získat tokeny z koncového bodu b2clogin.com.
1. Jakmile všechny vaše aplikace správně získávají tokeny z b2clogin.com, odeberte podporu pro login.microsoftonline.com vydané tokeny z rozhraní API.

Následující příklad vstupní zásady APIM ukazuje, jak přijímat tokeny vydané b2clogin.com i login.microsoftonline.com. Kromě toho podporuje požadavky rozhraní API ze dvou aplikací.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Další kroky

Další podrobnosti o zásadách správy rozhraní API Azure najdete v tématu [referenční index zásad APIM](../api-management/api-management-policies.md).

Informace o migraci webových rozhraní API založených na OWIN a jejich aplikacích, které mají b2clogin.com, naleznete v [aplikaci Migrate a Web API založené na OWIN do b2clogin.com](multiple-token-endpoints.md).

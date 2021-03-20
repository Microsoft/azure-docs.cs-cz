---
title: Zabezpečení rozhraní API služby Azure API Management pomocí Azure Active Directory B2C
description: Naučte se používat přístupové tokeny vydané Azure Active Directory B2C k zabezpečení koncového bodu rozhraní API Azure API Management.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 60b7bb33dfbf29b7e448887ce992d03009133b2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953484"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Zabezpečení rozhraní API služby Azure API Management pomocí Azure AD B2C

Přečtěte si, jak omezit přístup k rozhraní API Azure API Management (APIM) na klienty, kteří jsou ověření pomocí Azure Active Directory B2C (Azure AD B2C). Podle kroků v tomto článku vytvořte a otestujte příchozí zásady v APIM, které omezují přístup jenom na ty požadavky, které zahrnují platný přístupový token vydaný Azure AD B2C.

## <a name="prerequisites"></a>Předpoklady

Než budete pokračovat v krocích v tomto článku, budete potřebovat následující prostředky:

* [Tenant Azure AD B2C](tutorial-create-tenant.md)
* [Aplikace zaregistrovaná](tutorial-register-applications.md) ve vašem tenantovi
* [Toky uživatelů vytvořené](tutorial-create-user-flows.md) ve vašem tenantovi
* [Publikované rozhraní API](../api-management/import-and-publish.md) v Azure API Management
* [Poslat](https://www.getpostman.com/) za účelem testování zabezpečeného přístupu (volitelné)

## <a name="get-azure-ad-b2c-application-id"></a>Získat ID aplikace Azure AD B2C

Když Zabezpečete rozhraní API v Azure API Management s Azure AD B2C, potřebujete pro [příchozí zásadu](../api-management/api-management-howto-policies.md) , kterou vytvoříte v APIM, několik hodnot. Nejdřív si poznamenejte ID aplikace, kterou jste dříve vytvořili ve svém tenantovi Azure AD B2C. Pokud používáte aplikaci, kterou jste vytvořili v části požadavky, použijte ID aplikace pro *webbapp1*.

K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte kartu **vlastněné aplikace** .
1. Poznamenejte si hodnotu ve sloupci **ID aplikace (klienta)** pro *WebApp1* nebo jinou aplikaci, kterou jste vytvořili dříve.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **aplikace (starší verze)**.
1. Poznamenejte si hodnotu ve sloupci **ID aplikace** pro *WebApp1* nebo jinou aplikaci, kterou jste vytvořili dříve.

* * *

## <a name="get-token-issuer-endpoint"></a>Získat koncový bod vydavatele tokenu

Dále Získejte dobře známou adresu URL konfigurace pro některý z vašich Azure AD B2Cch uživatelských toků. Také potřebujete identifikátor URI koncového bodu vystavitele tokenu, který chcete podporovat v Azure API Management.

1. V [Azure Portal](https://portal.azure.com)přejděte na svého tenanta Azure AD B2C.
1. V části **zásady** vyberte **toky uživatelů**.
1. Vyberte existující zásadu, například *B2C_1_signupsignin1*, a pak vyberte **Spustit tok uživatele**.
1. Poznamenejte si adresu URL v hypertextovém odkazu zobrazenou pod nadpisem **tok spouštěného uživatele** v horní části stránky. Tato adresa URL je známý koncový bod zjišťování OpenID Connect pro tok uživatele a v další části ho použijete při konfiguraci příchozí zásady v Azure API Management.

    ![Dobře známý hypertextový odkaz URI na stránce spustit nyní na Azure Portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Výběrem hypertextového odkazu přejděte na stránku dobře známou konfiguraci OpenID Connect.
1. Na stránce, která se otevře v prohlížeči, zaznamenejte `issuer` hodnotu například:

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Tuto hodnotu použijete v další části při konfiguraci rozhraní API v Azure API Management.

Teď byste měli mít zaznamenané dvě adresy URL pro použití v další části: OpenID Connected a adresa URL koncového bodu konfigurace a identifikátor URI vystavitele. Například:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Konfigurace příchozí zásady v Azure API Management

Nyní jste připraveni přidat příchozí zásadu v Azure API Management, která ověřuje volání rozhraní API. Přidáním ověřovací zásady [JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) , která ověřuje cílovou skupinu a vystavitele v přístupovém tokenu, můžete zajistit, že budou přijata jenom volání API s platným tokenem.

1. V [Azure Portal](https://portal.azure.com)přejděte do instance služby Azure API Management.
1. Vyberte **Rozhraní API**.
1. Vyberte rozhraní API, které chcete zabezpečit pomocí Azure AD B2C.
1. Vyberte kartu **Návrh**.
1. V části **příchozí zpracování** vyberte **\</\>** otevřít Editor kódu zásad.
1. Do zásady umístěte následující `<validate-jwt>` značku `<inbound>` .

    1. Aktualizujte `url` hodnotu v `<openid-config>` prvku o dobře známou adresu URL konfigurace vaší zásady.
    1. Aktualizujte `<audience>` element s ID aplikace aplikace, kterou jste vytvořili dříve v TENANTOVI B2C (například *WebApp1*).
    1. Aktualizujte `<issuer>` element pomocí koncového bodu vystavitele tokenu, který jste si poznamenali

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Ověření zabezpečeného přístupu k rozhraní API

Aby bylo zajištěno, že přístup k vašemu rozhraní API budou mít přístup pouze ověření volající, můžete ověřit konfiguraci služby Azure API Management voláním rozhraní API pomocí metody [post](https://www.getpostman.com/).

Pro volání rozhraní API potřebujete přístupový token vydaný Azure AD B2C a klíč předplatného APIM.

### <a name="get-an-access-token"></a>Získání přístupového tokenu

Nejdřív potřebujete token vydaný Azure AD B2C, který se má použít v `Authorization` hlavičce v poli post. Můžete ji získat pomocí funkce **Spustit nyní** v uživatelském toku registrace/přihlášení, kterou byste měli vytvořit jako jeden z požadovaných součástí.

1. V [Azure Portal](https://portal.azure.com)přejděte na svého tenanta Azure AD B2C.
1. V části **zásady** vyberte **toky uživatelů**.
1. Vyberte existující tok uživatelů registrace nebo přihlašování, například *B2C_1_signupsignin1*.
1. V případě **aplikace** vyberte *WebApp1*.
1. V možnosti **Adresa URL odpovědi** vyberte `https://jwt.ms` .
1. Vyberte **Spustit tok uživatele**.

    ![Spustit stránku Flow uživatele pro registraci uživatelského toku přihlášení v Azure Portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Dokončete proces přihlašování. Měli byste být přesměrováni na `https://jwt.ms` .
1. Poznamenejte si hodnotu kódovaného tokenu, která se zobrazí v prohlížeči. Tuto hodnotu tokenu použijete pro autorizační hlavičku v poli post.

    ![Hodnota kódovaného tokenu zobrazená na jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Získat klíč předplatného rozhraní API

Klientská aplikace (v tomto případě), která volá publikované rozhraní API, musí ve svých požadavcích HTTP do rozhraní API zahrnovat platný klíč předplatného API Management. Získání klíče předplatného, který se má zahrnout do žádosti POST HTTP:

1. V [Azure Portal](https://portal.azure.com)přejděte do instance služby Azure API Management.
1. Vyberte **Předplatná**.
1. Vyberte tři tečky pro **produkt: neomezeno** a pak vyberte **Zobrazit/skrýt klíče**.
1. Zaznamenejte **primární klíč** pro daný produkt. Tento klíč použijete pro `Ocp-Apim-Subscription-Key` hlavičku v žádosti HTTP v poli post.

![Stránka klíče předplatného s vybranými možnostmi Zobrazit/skrýt klíče v Azure Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Otestování zabezpečeného volání rozhraní API

Po nahrání přístupového tokenu a klíče předplatného APIM jste teď připraveni otestovat, jestli jste správně nakonfigurovali zabezpečený přístup k rozhraní API.

1. Vytvoří novou `GET` žádost v [post](https://www.getpostman.com/). V poli Adresa URL požadavku Zadejte koncový bod seznamu mluvčího rozhraní API, které jste publikovali jako jeden z požadovaných součástí. Například:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Dále přidejte následující hlavičky:

    | Klíč | Hodnota |
    | --- | ----- |
    | `Authorization` | Hodnota kódovaného tokenu, kterou jste si poznamenali dříve, s předponou `Bearer ` (uveďte místo za "nosič") |
    | `Ocp-Apim-Subscription-Key` | APIM klíč předplatného, který jste si dříve poznamenal |

    Adresa URL požadavku **Get** a **záhlaví** by se měly zobrazit podobně jako:

    ![Uživatelské rozhraní, které zobrazuje adresu URL a záhlaví žádosti o získání](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Kliknutím na tlačítko Odeslat v poli **poslat** žádost spustíte. Pokud jste všechno nakonfigurovali správně, měli byste se dodávat s odpovědí JSON s kolekcí konferenčních mluvčích (tady se zkrátí):

    ```json
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

### <a name="test-an-insecure-api-call"></a>Test nezabezpečeného volání rozhraní API

Teď, když jste udělali úspěšnou žádost, otestujte případ selhání, abyste zajistili, že volání rozhraní API s *neplatným* tokenem se odmítnou podle očekávání. Jedním ze způsobů, jak provést test, je přidat nebo změnit několik znaků v hodnotě tokenu a pak spustit stejný `GET` požadavek jako předtím.

1. Přidejte do hodnoty tokenu několik znaků k simulaci neplatného tokenu. Například přidejte do hodnoty tokenu "INVALID":

    ![Část s hlavičkou uživatelského rozhraní, která zobrazuje neplatné přidané tokeny](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Kliknutím na tlačítko **Odeslat** žádost spustíte. S neplatným tokenem je očekávaný výsledek `401` neoprávněný stavový kód:

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Pokud se zobrazí `401` stavový kód, ověřili jste, že úspěšné požadavky na rozhraní API Azure API Management můžou provádět jenom volající s platným přístupovým tokenem vydaným Azure AD B2C.

## <a name="support-multiple-applications-and-issuers"></a>Podpora více aplikací a vystavitelů

Několik aplikací obvykle komunikuje s jedním REST API. Pokud chcete povolit rozhraní API pro přijímání tokenů, které jsou určené pro víc aplikací, přidejte jejich ID do `<audiences>` prvku ve APIM příchozích zásadách.

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Podobně pokud chcete podporovat více vystavitelů tokenů, přidejte jejich identifikátory URI koncového bodu do `<issuers>` elementu v zásadách příchozího APIM.

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrace na b2clogin.com

Pokud máte rozhraní APIM API, které ověřuje tokeny vydané starším `login.microsoftonline.com` koncovým bodem, měli byste migrovat rozhraní API a aplikace, které ho volají, aby používaly tokeny vydané nástrojem [b2clogin.com](b2clogin.md).

Postup při dvoufázové migraci můžete provést pomocí tohoto obecného procesu:

1. Přidejte podporu do své příchozí zásady APIM pro tokeny vydané b2clogin.com a login.microsoftonline.com.
1. Aktualizujte své aplikace po jednom, abyste získali tokeny z koncového bodu b2clogin.com.
1. Jakmile budou všechny vaše aplikace správně získávat tokeny z b2clogin.com, odeberte podporu pro tokeny vydané login.microsoftonline.com z rozhraní API.

Následující příklad APIM příchozí zásady znázorňuje, jak přijímat tokeny vydané b2clogin.com a login.microsoftonline.com. Kromě toho podporuje požadavky rozhraní API ze dvou aplikací.

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
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

Další podrobnosti o zásadách Azure API Management najdete v [referenčním indexu zásad APIM](../api-management/api-management-policies.md).

Informace o migraci webových rozhraní API založeného na OWIN a jejich aplikacích na b2clogin.com v [migraci webového rozhraní API založeného na Owin do b2clogin.comu](multiple-token-endpoints.md)najdete v.
---
title: Zabezpečení rozhraní API služby Azure API Management pomocí Azure Active Directory B2C
description: Naučte se používat přístupové tokeny vydané Azure Active Directory B2C k zabezpečení koncového bodu rozhraní API Azure API Management.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 47aeafdc2ffb8ca686a2b08243aaae3e743c12c2
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173557"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Zabezpečení rozhraní API služby Azure API Management pomocí Azure AD B2C

Naučte se, jak omezit přístup k rozhraní API Azure API Management (APIM) na klienty, kteří jsou ověření pomocí Azure Active Directory B2C (Azure AD B2C). Podle kroků v tomto článku vytvořte a otestujte příchozí zásady v APIM, které omezují přístup jenom na ty požadavky, které zahrnují platný přístupový token vydaný Azure AD B2C.

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat v krocích v tomto článku, budete potřebovat následující prostředky:

* [Tenant Azure AD B2C](tutorial-create-tenant.md)
* [Aplikace](tutorial-register-applications.md) zaregistrovaná ve vašem tenantovi
* [Toky uživatelů vytvořené](tutorial-create-user-flows.md) ve vašem tenantovi
* [Publikované rozhraní API](../api-management/import-and-publish.md) v Azure API Management
* [Poslat](https://www.getpostman.com/) za účelem testování zabezpečeného přístupu (volitelné)

## <a name="get-azure-ad-b2c-application-id"></a>Získat ID aplikace Azure AD B2C

Když Zabezpečete rozhraní API v Azure API Management s Azure AD B2C, potřebujete pro [příchozí zásadu](../api-management/api-management-howto-policies.md) , kterou vytvoříte v APIM, několik hodnot. Nejdřív si poznamenejte ID aplikace, kterou jste dříve vytvořili ve svém tenantovi Azure AD B2C. Pokud používáte aplikaci, kterou jste vytvořili v části požadavky, použijte ID aplikace pro *webbapp1*.

1. V [Azure Portal](https://portal.azure.com)přejděte na svého tenanta Azure AD B2C.
1. V části **Spravovat**vyberte **aplikace**.
1. Poznamenejte si hodnotu **ID aplikace** pro *WebApp1* nebo jinou aplikaci, kterou jste vytvořili dříve.

  ![Umístění ID aplikace B2C aplikace v Azure Portal](media/secure-apim-with-b2c-token/portal-02-app-id.png)

## <a name="get-token-issuer-endpoint"></a>Získat koncový bod vydavatele tokenu

Dále Získejte dobře známou adresu URL konfigurace pro některý z vašich Azure AD B2Cch uživatelských toků. Také potřebujete identifikátor URI koncového bodu vystavitele tokenu, který chcete podporovat v Azure API Management.

1. V [Azure Portal](https://portal.azure.com)přejděte na svého tenanta Azure AD B2C.
1. V části **zásady**vyberte **toky uživatelů (zásady)** .
1. Vyberte existující zásadu, třeba *B2C_1_signupsignin1*, a pak vyberte **Spustit tok uživatele**.
1. Poznamenejte si adresu URL v hypertextovém odkazu zobrazenou pod nadpisem **tok spouštěného uživatele** v horní části stránky. Tato adresa URL je známý koncový bod zjišťování OpenID Connect pro tok uživatele a v další části ho použijete při konfiguraci příchozí zásady v Azure API Management.

    ![Dobře známý hypertextový odkaz URI na stránce spustit nyní na Azure Portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Výběrem hypertextového odkazu přejděte na stránku dobře známou konfiguraci OpenID Connect.
1. Na stránce, která se otevře v prohlížeči, zaznamenejte `issuer` hodnotu například:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Tuto hodnotu použijete v další části při konfiguraci rozhraní API v Azure API Management.

Teď byste měli mít zaznamenané dvě adresy URL pro použití v další části: OpenID Connected a adresa URL koncového bodu konfigurace a identifikátor URI vystavitele. Příklad:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Konfigurace příchozí zásady v Azure API Management

Nyní jste připraveni přidat příchozí zásadu v Azure API Management, která ověřuje volání rozhraní API. Přidáním ověřovací zásady [JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) , která ověřuje cílovou skupinu a vystavitele v přístupovém tokenu, můžete zajistit, že budou přijata jenom volání API s platným tokenem.

1. V [Azure Portal](https://portal.azure.com)přejděte do instance služby Azure API Management.
1. Vyberte **Rozhraní API**.
1. Vyberte rozhraní API, které chcete zabezpečit pomocí Azure AD B2C.
1. Vyberte kartu **Návrh**.
1. V části **příchozí zpracování**vyberte **\< / otevřítEditorkóduzásad\>** .
1. Do `<inbound>` zásady umístěte `<validate-jwt>` následující značku.

    1. Aktualizujte `<openid-config>` hodnotu v prvku o dobře známou adresu URL konfigurace vaší zásady. `url`
    1. Aktualizujte element s ID aplikace aplikace, kterou jste vytvořili dříve v tenantovi B2C (například WebApp1). `<audience>`
    1. Aktualizujte `<issuer>` element pomocí koncového bodu vystavitele tokenu, který jste si poznamenali

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

## <a name="validate-secure-api-access"></a>Ověření zabezpečeného přístupu k rozhraní API

Aby bylo zajištěno, že přístup k vašemu rozhraní API budou mít přístup pouze ověření volající, můžete ověřit konfiguraci služby Azure API Management voláním rozhraní API pomocí metody [post](https://www.getpostman.com/).

Pro volání rozhraní API potřebujete přístupový token vydaný Azure AD B2C a klíč předplatného APIM.

### <a name="get-an-access-token"></a>Získání přístupového tokenu

Nejdřív potřebujete token vydaný Azure AD B2C, který se má použít v `Authorization` hlavičce v poli post. Můžete ji získat pomocí funkce **Spustit nyní** v uživatelském toku registrace/přihlášení, kterou byste měli vytvořit jako jeden z požadovaných součástí.

1. V [Azure Portal](https://portal.azure.com)přejděte na svého tenanta Azure AD B2C.
1. V části **zásady**vyberte **toky uživatelů (zásady)** .
1. Vyberte existující tok uživatele pro registraci nebo přihlášení, například *B2C_1_signupsignin1*.
1. V případě **aplikace**vyberte *WebApp1*.
1. V možnosti **Adresa URL odpovědi**vyberte `https://jwt.ms`.
1. Vyberte **Spustit tok uživatele**.

    ![Spustit stránku Flow uživatele pro registraci uživatelského toku přihlášení v Azure Portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Dokončete proces přihlašování. Měli byste být přesměrováni `https://jwt.ms`na.
1. Poznamenejte si hodnotu kódovaného tokenu, která se zobrazí v prohlížeči. Tuto hodnotu tokenu použijete pro autorizační hlavičku v poli post.

    ![Hodnota kódovaného tokenu zobrazená na jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Získat klíč předplatného rozhraní API

Klientská aplikace (v tomto případě), která volá publikované rozhraní API, musí ve svých požadavcích HTTP do rozhraní API zahrnovat platný klíč předplatného API Management. Získání klíče předplatného, který se má zahrnout do žádosti POST HTTP:

1. V [Azure Portal](https://portal.azure.com)přejděte do instance služby Azure API Management.
1. Vyberte **Předplatná**.
1. Vyberte tři tečky **pro produkt: Neomezeno**a pak vyberte **Zobrazit/skrýt klíče**.
1. Zaznamenejte **primární klíč** pro daný produkt. Tento klíč použijete pro hlavičku `Ocp-Apim-Subscription-Key` v žádosti HTTP v poli post.

![Stránka klíče předplatného s vybranými možnostmi Zobrazit/skrýt klíče v Azure Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Otestování zabezpečeného volání rozhraní API

Po nahrání přístupového tokenu a klíče předplatného APIM jste teď připraveni otestovat, jestli jste správně nakonfigurovali zabezpečený přístup k rozhraní API.

1. Vytvoří novou `GET` žádost v [post](https://www.getpostman.com/). V poli Adresa URL požadavku Zadejte koncový bod seznamu mluvčího rozhraní API, které jste publikovali jako jeden z požadovaných součástí. Příklad:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Dále přidejte následující hlavičky:

    | Klíč | Value |
    | --- | ----- |
    | `Authorization` | Hodnota kódovaného tokenu, kterou jste si poznamenali dříve, s `Bearer ` předponou (uveďte místo za "nosič") |
    | `Ocp-Apim-Subscription-Key` | APIM klíč předplatného, který jste si dříve poznamenal |

    Adresa URL požadavku **Get** a **záhlaví** by se měly zobrazit podobně jako:

    ![Uživatelské rozhraní, které zobrazuje adresu URL a záhlaví žádosti o získání](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Kliknutím na tlačítko Odeslat v poli **poslat** žádost spustíte. Pokud jste všechno nakonfigurovali správně, měli byste se dodávat s odpovědí JSON s kolekcí konferenčních mluvčích (tady se zkrátí):

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

### <a name="test-an-insecure-api-call"></a>Test nezabezpečeného volání rozhraní API

Teď, když jste udělali úspěšnou žádost, otestujte případ selhání, abyste zajistili, že volání rozhraní API s neplatným tokenem se odmítnou podle očekávání. Jedním ze způsobů, jak provést test, je přidat nebo změnit několik znaků v hodnotě tokenu a pak spustit stejný `GET` požadavek jako předtím.

1. Přidejte do hodnoty tokenu několik znaků k simulaci neplatného tokenu. Například přidejte do hodnoty tokenu "INVALID":

    ![Část s hlavičkou uživatelského rozhraní, která zobrazuje neplatné přidané tokeny](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Kliknutím na tlačítko **Odeslat** žádost spustíte. S neplatným tokenem je `401` očekávaný výsledek neoprávněný stavový kód:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Pokud se `401` zobrazí stavový kód, ověřili jste, že úspěšné požadavky na rozhraní API Azure API Management můžou provádět jenom volající s platným přístupovým tokenem vydaným Azure AD B2C.

## <a name="support-multiple-applications-and-issuers"></a>Podpora více aplikací a vystavitelů

Několik aplikací obvykle komunikuje s jedním REST API. Chcete-li povolit volání rozhraní API více aplikacím, přidejte jejich ID do `<audiences>` prvku v zásadách příchozího APIM.

```XML
<!-- Accept requests from multiple applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Podobně pokud chcete podporovat více vystavitelů tokenů, přidejte jejich identifikátory URI `<audiences>` koncového bodu do elementu v zásadách příchozího APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
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

## <a name="next-steps"></a>Další postup

Další podrobnosti o zásadách Azure API Management najdete v referenčním [indexu zásad APIM](../api-management/api-management-policies.md).

Informace o migraci webových rozhraní API založeného na OWIN a jejich aplikacích na b2clogin.com v [migraci webového rozhraní API založeného na Owin do b2clogin.comu](multiple-token-endpoints.md)najdete v.

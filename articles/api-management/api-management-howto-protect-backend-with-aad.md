---
title: Ochrana rozhraní API pomocí standardu OAuth 2.0 s Azure Active Directory a API Management | Microsoft Docs
description: Zjistěte, jak k ochraně back-end rozhraní API webové s Azure Active Directory a API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: f5662a4082487137dfd642cc3264a90f8ab19054
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Ochrana rozhraní API pomocí standardu OAuth 2.0 s Azure Active Directory a API Management

Tento průvodce vám ukáže, jak nakonfigurovat instanci Azure API Management k ochraně rozhraní API pomocí Azure Active Directory (Azure AD) protokol OAuth 2.0. 

## <a name="prerequisites"></a>Požadavky
Chcete-li postupujte podle kroků v tomto článku, budete potřebovat:
* Instance služby API Management
* Rozhraní API publikovanému, který používá instanci služby API Management
* Klient služby Azure AD

## <a name="overview"></a>Přehled

Zde je stručný přehled kroků:

1. Zaregistrujte aplikaci (back-end aplikace) ve službě Azure AD představují rozhraní API.
2. Jiná aplikace (klientské aplikace) zaregistrujte ve službě Azure AD představují klientská aplikace, které potřebuje volat rozhraní API.
3. Ve službě Azure AD udělte oprávnění, aby klientská aplikace volat back-end aplikace.
4. Konfigurace konzole pro vývojáře používat autorizace uživatelů OAuth 2.0.
5. Přidat **ověření jwt** zásad k ověření tokenu OAuth pro každý příchozí požadavek.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Zaregistrovat aplikaci ve službě Azure AD představují rozhraní API

Pokud chcete ochránit rozhraní API s Azure AD, prvním krokem je zaregistrovat aplikaci ve službě Azure AD, který představuje rozhraní API. 

1. Přejděte ke klientovi Azure AD a potom vyhledejte **registrace aplikace**.

2. Vyberte **Registrace nové aplikace**. 

3. Zadejte název aplikace. (V tomto příkladu je název `backend-app`.)  

4. Zvolte **webovou aplikaci nebo API** jako **typ aplikace**. 

5. Pro **přihlašovací adresa URL**, můžete použít `https://localhost` jako zástupný znak.

6. Vyberte **Vytvořit**.

Při vytvoření aplikace, poznamenejte si **ID aplikace**, pro použití v následném kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrace jiné aplikace ve službě Azure AD představují klientské aplikace

Každou klientskou aplikaci, která volá rozhraní API musí být registrován jako aplikace ve službě Azure AD i. V tomto příkladu je ukázková aplikace klienta konzole pro vývojáře v portálu pro vývojáře API Management. Zde je postup registrace jiná aplikace ve službě Azure AD představují konzole pro vývojáře.

1. Vyberte **Registrace nové aplikace**. 

2. Zadejte název aplikace. (V tomto příkladu je název `client-app`.)

3. Zvolte **webovou aplikaci nebo API** jako **typ aplikace**.  

4. Pro **přihlašovací adresa URL**, můžete použít `https://localhost` jako zástupný symbol nebo adresu URL pomocí přihlášení instanci služby API Management. (V tomto příkladu se adresa URL je `https://contoso5.portal.azure-api.net/signin`.)

5. Vyberte **Vytvořit**.

Při vytvoření aplikace, poznamenejte si **ID aplikace**, pro použití v následném kroku. 

Teď vytvořte tajný klíč klienta pro tuto aplikaci, pro použití v následném kroku.

1. Vyberte **nastavení** znovu a přejděte na **klíče**.

2. V části **hesla**, poskytovat **klíče popis**. Zvolte, kdy by měl klíč vyprší a vyberte **Uložit**.

Poznamenejte si hodnotu klíče. 

## <a name="grant-permissions-in-azure-ad"></a>Udělení oprávnění ve službě Azure AD

Teď, když jste si zaregistrovali dvě aplikace k reprezentaci rozhraní API a konzole pro vývojáře, budete muset udělit oprávnění, aby klientská aplikace volat back-end aplikace.  

1. Přejděte do **registrace aplikace**. 

2. Vyberte `client-app`a přejděte na **nastavení**.

3. Vyberte **požadovaná oprávnění** > **přidat**.

4. Vyberte **vybrat rozhraní API**a vyhledejte řetězec `backend-app`.

5. V části **delegovaná oprávnění**, vyberte `Access backend-app`. 

6. Vyberte **vyberte**a potom vyberte **provádí**. 

> [!NOTE]
> Pokud **Azure Active Directory** není uveden v seznamu oprávnění k ostatním aplikacím, vyberte **přidat** přidat ze seznamu.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Povolení autorizace OAuth 2.0 uživatelů v konzole pro vývojáře

V tomto okamžiku jste vytvořili aplikace ve službě Azure AD a mít udělena příslušná oprávnění pro povolit klientské aplikace volat back-end aplikace. 

V tomto příkladu je konzole pro vývojáře klientské aplikace. Následující kroky popisují postup povolení autorizace OAuth 2.0 uživatelů v konzole pro vývojáře. 

1. Přejděte k vaší instanci API Management.

2. Vyberte **OAuth 2.0** > **přidat**.

3. Zadejte **zobrazovaný název** a **popis**.

4. Pro **adresa URL stránky registrace klienta**, zadejte hodnotu zástupného symbolu, jako například `http://localhost`. **Adresa URL stránky registrace klienta** odkazuje na stránku, uživatelé můžete vytvořit a nakonfigurovat svoje vlastní účty pro zprostředkovatelů OAuth 2.0, které to podporují. V tomto příkladu uživatele není vytvořit a nakonfigurovat svoje vlastní účty, tak místo toho použít zástupný znak.

5. Pro **typy udělení autorizace**, vyberte **autorizační kód**.

6. Zadejte **adresu URL koncového bodu autorizace** a **adresu URL koncového bodu Token**. Načtení těchto hodnot z **koncové body** stránky v klientovi služby Azure AD. Vyhledejte **registrace aplikace** stránku znovu a vyberte **koncové body**.

7. Kopírování **koncový bod autorizace OAuth 2.0**a vložte ji do **adresu URL koncového bodu autorizace** textové pole.

8. Kopírování **koncový bod tokenu OAuth 2.0**a vložte ji do **adresu URL koncového bodu Token** textové pole. Kromě vkládání v tokenu koncový bod, přidejte text parametr s názvem **prostředků**. Hodnota tohoto parametru, použijte **ID aplikace** back-end aplikace.

9. Potom zadejte pověření klienta. Jedná se o pověření pro klientské aplikace.

10. Pro **ID klienta**, použijte **ID aplikace** pro klientské aplikace.

11. Pro **tajný klíč klienta**, dříve použít klíč, který jste vytvořili pro klientské aplikace. 

12. Okamžitě následující tajný klíč klienta je **redirect_url** pro autorizaci kód udělit typu. Poznamenejte si adresu URL.

13. Vyberte **Vytvořit**.

14. Přejděte zpět **nastavení** stránku vaší klientské aplikace.

15. Vyberte **adresy URL odpovědí**a vložte **redirect_url** v prvním řádku. V tomto příkladu nahrazen `https://localhost` s adresou URL v prvním řádku.  

Teď, když jste nakonfigurovali serveru ověřování OAuth 2.0, v konzole pro vývojáře můžete získat přístupové tokeny z Azure AD. 

Dalším krokem je povolit autorizace uživatelů OAuth 2.0 pro vaše rozhraní API. To umožňuje konzole pro vývojáře vědět, že je nutné získat přístupový token jménem uživatele, před provedením volání rozhraní API.

1. Přejděte k vaší instanci API Management a přejděte na **rozhraní API**.

2. Vyberte rozhraní API, které chcete chránit. V tomto příkladu použijete `Echo API`.

3. Přejděte na **nastavení**.

4. V části **zabezpečení**, zvolte **OAuth 2.0**a vyberte server OAuth 2.0, který jste dříve nakonfigurovali. 

5. Vyberte **Uložit**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Úspěšně volat rozhraní API z portálu pro vývojáře

Teď, když je zapnutá autorizace OAuth 2.0 uživatelů `Echo API`, konzole pro vývojáře získá přístupový token jménem uživatele, před voláním rozhraní API.

1. Přejděte na všechny operace v rámci `Echo API` v vývojáře portálu a vyberte možnost **vyzkoušet**. Přináší konzole pro vývojáře.

2. Poznámka: novou položku v **autorizace** části odpovídající serveru ověřování, který jste právě přidali.

3. Vyberte **autorizační kód** z autorizaci rozevíracího seznamu, zobrazí se výzva pro přihlášení ke klientovi Azure AD. Pokud jste již přihlášeni pomocí účtu, nemusí se vás.

4. Po úspěšném přihlášení se `Authorization` záhlaví se přidá na žádost, k tokenu přístupu z Azure AD. Toto je ukázkový token (kódováním Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Vyberte **odeslat**, a můžete úspěšně volat rozhraní API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurace zásad ověřování JWT předem autorizovat požadavků

Nyní když se uživatel pokusí o volání z konzole pro vývojáře, bude uživatel vyzván k přihlášení. Konzole pro vývojáře získá přístupový token jménem uživatele.

Ale co když někdo volání rozhraní API bez token nebo se neplatný token? Například můžete stále volat rozhraní API i v případě, že odstraníte `Authorization` záhlaví. Důvodem je, že rozhraní API správy neověřuje přístupový token v tomto okamžiku. Jednoduše předává `Authorization` hlavičky k rozhraní API back-end.

Můžete použít [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) zásad předem autorizovat požadavků ve službě API Management, tím, že ověří přístupových tokenů každého příchozího požadavku. Pokud požadavek nemá platný token, API Management se zablokuje. Můžete například přidat následující zásady tak, aby `<inbound>` část zásady `Echo API`. Ověří deklarace cílové skupiny v přístupový token a vrátí chybovou zprávu, pokud token není platný. Informace o tom, jak nakonfigurovat zásady najdete v tématu [nastavit nebo upravit zásady](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>Sestavit aplikaci pro volání rozhraní API

V této příručce, můžete použít konzole pro vývojáře ve službě API Management jako ukázkovou aplikaci klienta k volání `Echo API` chráněn OAuth 2.0. Další informace o tom, jak sestavit aplikaci a implementovat OAuth 2.0, najdete v části [ukázky kódu Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Další postup
* Další informace o [Azure Active Directory a OAuth2.0](../active-directory/develop/active-directory-authentication-scenarios.md).
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o službě API Management.
* Další způsoby zabezpečení služby back-end, najdete v části [vzájemného ověření certifikátů](api-management-howto-mutual-certificates.md).

* [Vytvoření instance API Management](get-started-create-service-instance.md).

* [Správa vašeho prvního rozhraní API](import-and-publish.md).

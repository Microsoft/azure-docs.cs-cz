---
title: Ochrana rozhraní API s použitím OAuth 2.0 s Azure Active Directory a API Management | Dokumentace Microsoftu
description: Zjistěte, jak k ochraně webového rozhraní API back-endu. s Azure Active Directory a API Management.
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
ms.openlocfilehash: 06350d30999cb056babbd001f98a6c3a5fdbac6c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576990"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Ochrana rozhraní API s použitím OAuth 2.0 s Azure Active Directory a API Management

Tato příručka ukazuje, jak konfigurovat vaše instance Azure API Management k ochraně rozhraní API s použitím protokolu OAuth 2.0 se službou Azure Active Directory (Azure AD). 

## <a name="prerequisites"></a>Požadavky
Chcete-li postupovat podle kroků v tomto článku, budete potřebovat:
* Instance služby API Management
* Publikování rozhraní API, která používá instanci API Management
* Klient služby Azure AD

## <a name="overview"></a>Přehled

Tady je stručný přehled kroků:

1. Registrace aplikace (back-end app) ve službě Azure AD k reprezentaci rozhraní API.
2. Registrace jiné aplikace (klientská aplikace) ve službě Azure AD k reprezentaci klientskou aplikaci, kterou je potřeba volat rozhraní API.
3. Ve službě Azure AD udělte oprávnění, aby klientská aplikace volat back-end app.
4. Konfigurace v konzole pro vývojáře pomocí autorizaci uživatelů OAuth 2.0.
5. Přidat **ověření jwt** zásad ověřit token OAuth pro všechny příchozí požadavky.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrace aplikace ve službě Azure AD k reprezentaci rozhraní API

Pokud chcete chránit rozhraní API s využitím Azure AD, prvním krokem je registrace aplikace ve službě Azure AD, který představuje rozhraní API. 

1. Přejděte do svého tenanta Azure AD a potom vyhledejte **registrace aplikací**.

2. Vyberte **Registrace nové aplikace**. 

3. Zadejte název aplikace. (V tomto příkladu je název `backend-app`.)  

4. Zvolte **webovou aplikaci nebo API** jako **typ aplikace**. 

5. Pro **přihlašovací adresa URL**, můžete použít `https://localhost` jako zástupný symbol.

6. Vyberte **Vytvořit**.

Když se aplikace, poznamenejte si, **ID aplikace**, pro použití v následném kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Jiná aplikace zaregistrovat ve službě Azure AD k reprezentaci klientské aplikace

Každá klientská aplikace, která volá rozhraní API musí být registrován jako aplikaci v Azure AD také. V tomto příkladu je ukázková aplikace klientské konzole pro vývojáře na portálu pro vývojáře API Management. Tady je postup při registraci jiná aplikace ve službě Azure AD k reprezentaci konzole pro vývojáře.

1. Vyberte **Registrace nové aplikace**. 

2. Zadejte název aplikace. (V tomto příkladu je název `client-app`.)

3. Zvolte **webovou aplikaci nebo API** jako **typ aplikace**.  

4. Pro **přihlašovací adresa URL**, můžete použít `https://localhost` jako zástupný symbol, nebo použijte přihlašovací adresu URL vaší instance služby API Management. (V tomto příkladu je adresa URL `https://contoso5.portal.azure-api.net/signin`.)

5. Vyberte **Vytvořit**.

Když se aplikace, poznamenejte si, **ID aplikace**, pro použití v následném kroku. 

Teď vytvořte tajný kód klienta pro tuto aplikaci pro použití v následném kroku.

1. Vyberte **nastavení** znovu a přejděte na **klíče**.

2. V části **hesla**, zadejte **Popis klíče**. Zvolte, kdy by měl klíč vyprší a vyberte **Uložit**.

Poznamenejte si hodnotu klíče. 

## <a name="grant-permissions-in-azure-ad"></a>Udělení oprávnění ve službě Azure AD

Teď, když jste se zaregistrovali k reprezentaci rozhraní API a vývojářské konzole dvě aplikace, budete muset udělit oprávnění, aby klientská aplikace volat back-end app.  

1. Přejděte do **registrace aplikací**. 

2. Vyberte `client-app`a přejděte na **nastavení**.

3. Vyberte **požadovaná oprávnění** > **přidat**.

4. Vyberte **vyberte rozhraní API**a vyhledejte `backend-app`.

5. V části **delegovaná oprávnění**vyberte `Access backend-app`. 

6. Vyberte **vyberte**a pak vyberte **provádí**. 

> [!NOTE]
> Pokud **Azure Active Directory** není uveden v seznamu oprávnění k ostatním aplikacím, vyberte **přidat** přidat ze seznamu.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Povolit autorizaci uživatelů OAuth 2.0 v konzole pro vývojáře

V tomto okamžiku jste vytvořili vaší aplikace ve službě Azure AD a mít udělena správná oprávnění, aby klientská aplikace volat back-end app. 

V tomto příkladu je konzola pro vývojáře klientskou aplikaci. Následující kroky popisují, jak povolit autorizaci uživatelů OAuth 2.0 v konzole pro vývojáře. 

1. Přejděte k vaší instanci API Management.

2. Vyberte **OAuth 2.0** > **přidat**.

3. Zadejte **zobrazovaný název** a **popis**.

4. Pro **adresa URL stránky pro registraci klienta**, zadejte hodnotu zástupného symbolu, jako například `http://localhost`. **Adresa URL stránky pro registraci klienta** odkazuje na stránku, která uživatelům umožňuje vytvořit a nakonfigurovat své účty služby pro zprostředkovatele OAuth 2.0, které to podporují. V tomto příkladu uživatelé není vytvořit a nakonfigurovat svoje vlastní účty, tak, aby místo toho použijte zástupný symbol.

5. Pro **typy udělení autorizace**vyberte **autorizační kód**.

6. Zadejte **adresu URL koncového bodu autorizace** a **adresu URL koncového bodu Token**. Načtení z těchto hodnot **koncové body** stránky ve vašem tenantovi Azure AD. Přejděte **registrace aplikací** stránku znovu a vyberte **koncové body**.

7. Kopírovat **koncový bod autorizace OAuth 2.0**a vložte ho do **adresu URL koncového bodu autorizace** textového pole.

8. Kopírovat **koncový bod tokenu OAuth 2.0**a vložte ho do **adresu URL koncového bodu Token** textového pole. Kromě vkládání do koncového bodu tokenu, přidejte parametr těla zprávy s názvem **prostředků**. Hodnota tohoto parametru použijte **ID aplikace** pro back endové aplikace.

9. Dále určete pověření klienta. Toto jsou přihlašovací údaje pro klientskou aplikaci.

10. Pro **ID klienta**, použijte **ID aplikace** pro klientskou aplikaci.

11. Pro **tajný kód klienta**, použijte klíč, který jste vytvořili dříve pro klientskou aplikaci. 

12. Okamžitě po tajný kód klienta je **redirect_url** pro povolení kód typ udělení oprávnění. Tuto adresu URL si poznamenejte.

13. Vyberte **Vytvořit**.

14. Přejděte zpět **nastavení** stránky vaší klientské aplikace.

15. Vyberte **adresy URL odpovědí**a vložte **redirect_url** v prvním řádku. V tomto příkladu můžete nahradit `https://localhost` pomocí adresy URL v prvním řádku.  

Teď, když jste nakonfigurovali server autorizace OAuth 2.0, můžete konzolu pro vývojáře získání přístupových tokenů z Azure AD. 

Dalším krokem je povolit autorizaci uživatelů OAuth 2.0 pro vaše rozhraní API. To umožňuje konzole pro vývojáře vědět, že je nutné získat přístupový token jménem uživatele, před provedením volání do rozhraní API.

1. Přejděte k vaší instanci API Management a přejděte na **rozhraní API**.

2. Vyberte rozhraní API, které chcete chránit. V tomto příkladu použijete `Echo API`.

3. Přejděte na **nastavení**.

4. V části **zabezpečení**, zvolte **OAuth 2.0**a vyberte server OAuth 2.0, který jste nakonfigurovali v předchozích krocích. 

5. Vyberte **Uložit**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Úspěšně volat rozhraní API z portálu pro vývojáře

Teď, když je zapnutá autorizaci uživatelů OAuth 2.0 `Echo API`, vývojářské konzole získá přístupový token jménem uživatele, před voláním rozhraní API.

1. Přejděte na jakékoli operace v rámci `Echo API` Developer portal a vyberte **vyzkoušet**. Přináší konzole pro vývojáře.

2. Poznámka: Nová položka v **autorizace** část odpovídající k autorizačnímu serveru, kterou jste právě přidali.

3. Vyberte **autorizační kód** z povolení rozevíracího seznamu, zobrazí se výzva k přihlášení k tenantovi Azure AD. Pokud už jste přihlášení pomocí účtu, nemusí se výzva.

4. Po úspěšném přihlášení se `Authorization` záhlaví je přidáno k žádosti, pomocí přístupového tokenu z Azure AD. Následuje ukázkový token (základ64 kódovaný):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Vyberte **odeslat**, a je možné úspěšně volat rozhraní API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurace zásad ověřování tokenů JWT má předběžně autorizovat požadavků

V tomto okamžiku když se uživatel pokusí provést volání v konzole pro vývojáře, bude uživatel vyzván k přihlášení. Konzole pro vývojáře získá přístupový token jménem uživatele.

Ale co když někdo volá rozhraní API bez tokenu nebo s neplatným tokenem? Například můžete stále volat rozhraní API i v případě, že odstraníte `Authorization` záhlaví. Důvodem je, že API Management neověřuje přístupového tokenu v tomto okamžiku. Jednoduše předává `Authorization` hlavičky k back endového rozhraní API.

Můžete použít [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) zásad má předběžně autorizovat požadavků ve službě API Management, tak přístupové tokeny jednotlivých příchozích požadavků. Pokud požadavek nemá platný token, API Management se zablokuje. Například můžete přidat následující zásady tak, aby `<inbound>` část zásady `Echo API`. Kontroluje cílovou skupinu deklarací identity v tokenu přístupu a vrátí chybovou zprávu, pokud token není platný. Informace o tom, jak nakonfigurovat zásady, najdete v části [nastavení nebo úprava zásad](set-edit-policies.md).

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

## <a name="build-an-application-to-call-the-api"></a>Vytvoření aplikace pro volání rozhraní API

V této příručce, můžete použít konzole pro vývojáře ve službě API Management jako ukázková klientská aplikace k volání `Echo API` chráněn OAuth 2.0. Další informace o tom, jak sestavit aplikaci a implementovat OAuth 2.0, naleznete v tématu [ukázky kódu Azure Active Directory](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>Další postup
* Další informace o [Azure Active Directory a OAuth 2.0](../active-directory/develop/authentication-scenarios.md).
* Podívejte se na informace [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o službě API Management.
* Další způsoby, jak zabezpečit vaše back-end služby, najdete v části [vzájemného ověření certifikátů](api-management-howto-mutual-certificates.md).

* [Vytvoření instance služby API Management](get-started-create-service-instance.md).

* [Správa vašeho prvního rozhraní API](import-and-publish.md).

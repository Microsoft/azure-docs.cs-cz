---
title: Ochrana rozhraní API pomocí OAuth 2.0 se správou AAD a rozhraní API
titleSuffix: Azure API Management
description: Zjistěte, jak chránit back-end webového rozhraní API pomocí Azure Active Directory a správy rozhraní API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/21/2019
ms.author: apimpm
ms.openlocfilehash: 300f44daeeea5e8a774575dabcb00686906bb5de
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804363"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Ochrana rozhraní API pomocí OAuth 2.0 s využitím služeb Azure Active Directory a API Management

Tato příručka ukazuje, jak nakonfigurovat instanci Azure API Management pro ochranu rozhraní API pomocí protokolu OAuth 2.0 s Azure Active Directory (Azure AD). 

> [!NOTE]
> Tato funkce je **dostupná**na úrovních Vývojář , **Standard** a **Premium** ve správě rozhraní API.

## <a name="prerequisites"></a>Požadavky
Chcete-li postupovat podle kroků v tomto článku, musíte mít:
* Instance správy rozhraní API
* Publikované rozhraní API, které používá instanci správy rozhraní API
* Klient Azure AD

## <a name="overview"></a>Přehled

Zde je stručný přehled kroků:

1. Zaregistrujte aplikaci (back-endovou aplikaci) ve službě Azure AD, která bude představovat rozhraní API.
2. Zaregistrujte jinou aplikaci (klientskou aplikaci) ve službě Azure AD, která bude představovat klientskou aplikaci, která potřebuje volat rozhraní API.
3. Ve službě Azure AD udělte oprávnění, která umožní klientské aplikaci volat back-endovou aplikaci.
4. Nakonfigurujte vývojářskou konzolu tak, aby volala rozhraní API pomocí autorizace uživatele OAuth 2.0.
5. Přidejte zásadu **validate-jwt** k ověření tokenu OAuth pro každý příchozí požadavek.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrace aplikace ve službě Azure AD představující rozhraní API

Chcete-li chránit rozhraní API s Azure AD, prvním krokem je registrace aplikace ve službě Azure AD, která představuje rozhraní API. 

1. Přejděte na [portál Azure](https://portal.azure.com) a zaregistrujte svou aplikaci. Vyhledejte a vyberte **registrace aplikací**.

1. Vyberte **možnost Nová registrace**. 

1. Po zobrazení **stránky Registrovat přihlášku** zadejte registrační údaje vaší přihlášky: 
    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například *back-endové aplikace*. 
    - V části **Podporované typy účtů** vyberte možnost, která vyhovuje vašemu scénáři. 

1. Ponechejte oddíl **IDENTIFIKÁTOR URI přesměrování** prázdný.

1. Výběrem možnosti **Registrovat** aplikaci vytvořte. 

1. Na stránce **Přehled** aplikace najděte hodnotu **ID aplikace (klienta)** a zaznamenejte ji na později.

1. Vyberte **Vystavit rozhraní API** a nastavte identifikátor URI **ID aplikace** s výchozí hodnotou. Zaznamenejte tuto hodnotu na později.

1. Chcete-li zobrazit **stránku Přidat obor,** vyberte tlačítko **Přidat obor.** Pak vytvořte nový obor, který je podporován `Files.Read`rozhraním API (například ). Nakonec vyberte tlačítko **Přidat obor** a vytvořte obor. Tento krok opakujte a přidejte všechny obory podporované rozhraním API.

1. Při vytvoření oborů si je poznamenejte pro použití v následujícím kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrace jiné aplikace ve službě Azure AD představující klientskou aplikaci

Každá klientská aplikace, která volá rozhraní API, musí být registrována jako aplikace také ve službě Azure AD. V tomto příkladu je klientskou aplikací konzola pro vývojáře na portálu pro vývojáře pro správu rozhraní API. Tady je postup, jak zaregistrovat jinou aplikaci ve službě Azure AD, která bude reprezentovat konzolu pro vývojáře.

1. Přejděte na [portál Azure](https://portal.azure.com) a zaregistrujte svou aplikaci. Vyhledejte a vyberte **registrace aplikací**.

1. Vyberte **možnost Nová registrace**.

1. Po zobrazení **stránky Registrovat přihlášku** zadejte registrační údaje vaší přihlášky: 
    - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například *klient-app*. 
    - V části **Podporované typy účtů** vyberte Účty v **libovolném organizačním adresáři (libovolný adresář Azure AD – víceklientský)**. 

1. V části **Identifikátor URI** `Web` přesměrování vyberte `https://contoso5.portal.azure-api.net/signin`a zadejte adresu URL .

1. Výběrem možnosti **Registrovat** aplikaci vytvořte. 

1. Na stránce **Přehled** aplikace najděte hodnotu **ID aplikace (klienta)** a zaznamenejte ji na později.

Nyní vytvořte tajný klíč klienta pro tuto aplikaci pro použití v následujícím kroku.

1. Ze seznamu stránek pro klientskou aplikaci vyberte **Certifikáty & tajných kódů**a **vyberte Nový tajný klíč klienta**.

1. V části **Přidat tajný klíč klienta**zadejte **popis**. Zvolte, kdy má platnost klíče vypršet, a vyberte **Přidat**.

Při vytvoření tajného klíče si poznamenejte hodnotu klíče pro použití v následujícím kroku. 

## <a name="grant-permissions-in-azure-ad"></a>Udělení oprávnění ve službě Azure AD

Nyní, když jste zaregistrovali dvě aplikace představující rozhraní API a vývojářskou konzolu, musíte udělit oprávnění, která umožní klientské aplikaci volat back-endovou aplikaci.  

1. Přejděte na [portál Azure](https://portal.azure.com) a udělte oprávnění klientské aplikaci. Vyhledejte a vyberte **registrace aplikací**.

1. Vyberte si klientskou aplikaci. V seznamu stránek aplikace pak vyberte **oprávnění rozhraní API**.

1. Vyberte **přidat oprávnění**.

1. V **části Vyberte rozhraní API**vyberte Moje rozhraní **API**a pak vyhledejte a vyberte back-endovou aplikaci.

1. V části **Delegovaná oprávnění**vyberte příslušná oprávnění pro back-endovou aplikaci a pak vyberte **Přidat oprávnění**.

1. Volitelně na stránce **oprávnění rozhraní API** vyberte **udělit souhlas správce pro \<vaše jméno klienta>** udělit souhlas jménem všech uživatelů v tomto adresáři. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Povolení autorizace uživatele OAuth 2.0 v konzole pro vývojáře

V tomto okamžiku jste vytvořili aplikace ve službě Azure AD a udělili správná oprávnění, která umožňují klientské aplikaci volat back-endovou aplikaci. 

V tomto příkladu je vývojářská konzola klientská aplikace. Následující kroky popisují, jak povolit autorizaci uživatele OAuth 2.0 v konzole pro vývojáře. 

1. Na webu Azure Portal přejděte na instanci správy rozhraní API.

1. Vyberte **Možnost OAuth 2.0** > **Přidat**.

1. Zadejte **zobrazovaný název** a **popis**.

1. Pro **adresu URL registrační stránky klienta**zadejte `http://localhost`zástupnou hodnotu, například . **Adresa URL registrační stránky klienta** odkazuje na stránku, kterou mohou uživatelé použít k vytvoření a konfiguraci vlastních účtů pro poskytovatele OAuth 2.0, kteří to podporují. V tomto příkladu uživatelé nevytvářejí a nekonfigurují své vlastní účty, takže místo toho použijete zástupný symbol.

1. U **typů udělení autorizace**vyberte **autorizační kód**.

1. Zadejte **adresu URL koncového bodu autorizace** a **adresu URL koncového tokenu**. Načíst tyto hodnoty ze stránky **Koncové body** ve vašem tenantovi Azure AD. Přejděte znovu na stránku **Registrace aplikací** a vyberte **Koncové body**.


1. Zkopírujte **koncový bod autorizace OAuth 2.0**a vložte jej do textového pole **Url koncového bodu autorizace.** V části Metoda žádosti o autorizaci vyberte **možnost POST.**

1. Zkopírujte **koncový bod tokenu OAuth 2.0**a vložte jej do textového pole **URL koncového bodu tokenu.** 

    >[!IMPORTANT]
    > Můžete použít koncové body **v1** nebo **v2.** V závislosti na zvolené verzi se však bude následující krok lišit. Doporučujeme používat koncové body v2. 

1. Pokud používáte koncové body **v1,** přidejte parametr tělesa s názvem **resource**. Pro hodnotu tohoto parametru použijte **ID aplikace** back-endové aplikace. 

1. Pokud používáte koncové body **v2,** použijte obor, který jste vytvořili pro back-endovou aplikaci v poli **Výchozí obor.** Také nezapomeňte nastavit hodnotu vlastnosti [`accessTokenAcceptedVersion`](/azure/active-directory/develop/reference-app-manifest#accesstokenacceptedversion-attribute) `2` v [manifestu aplikace](/azure/active-directory/develop/reference-app-manifest).

1. Dále zadejte pověření klienta. Jedná se o pověření pro klientskou aplikaci.

1. Pro **ID klienta**použijte **ID aplikace** klient-app.

1. Pro **tajný klíč klienta**použijte klíč, který jste vytvořili pro klientskou aplikaci dříve. 

1. Bezprostředně následující tajný klíč klienta je **redirect_url** pro typ udělení autorizačního kódu. Poznamenejte si tuto adresu URL.

1. Vyberte **Vytvořit**.

1. Vraťte se do klientské aplikace a vyberte **Ověřování**.

1. V části **Přesměrovat identifikátory URI**vyberte typ jako **web**, vložte **redirect_url** do pole **Přesměrovat identifikátor URI**a uložte jej.

Teď, když jste nakonfigurovali autorizační server OAuth 2.0, může konzole pro vývojáře získat přístupové tokeny z Azure AD. 

Dalším krokem je povolení autorizace uživatele OAuth 2.0 pro vaše rozhraní API. To umožňuje konzole pro vývojáře vědět, že potřebuje získat přístupový token jménem uživatele, před voláním do vašeho rozhraní API.

1. Přejděte na instanci správy rozhraní API a přejděte na **rozhraní API**.

2. Vyberte rozhraní API, které chcete chránit. Můžete například použít `Echo API`.

3. Přejděte do **nastavení**.

4. V části **Zabezpečení**zvolte **OAuth 2.0**a vyberte server OAuth 2.0, který jste nakonfigurovali dříve. 

5. Vyberte **Uložit**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Úspěšně volání rozhraní API z portálu pro vývojáře

> [!NOTE]
> Tato část se nevztahuje na úroveň **Consumption,** která nepodporuje portál pro vývojáře.

Nyní, když je ve vašem rozhraní API povolena autorizace uživatele OAuth 2.0, získá konzola pro vývojáře přístupový token jménem uživatele před voláním rozhraní API.

1. Přejděte na libovolnou operaci v rozhraní API na portálu pro vývojáře a vyberte **Try it**. Tím se dostanete do konzole pro vývojáře.

2. Poznámka: nová položka v části **Autorizace** odpovídající autorizačnímu serveru, který jste právě přidali.

3. V rozevíracím seznamu autorizace vyberte **Autorizační kód** a budete vyzváni k přihlášení k tenantovi Azure AD. Pokud jste již přihlášeni pomocí účtu, nemusí být výzva.

4. Po úspěšném `Authorization` přihlášení se do požadavku přidá záhlaví s přístupovým tokenem z Azure AD. Následuje ukázkový token (zakódovaný Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Vyberte **Odeslat**a můžete rozhraní API úspěšně volat.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurace zásad y wwt ověření pro předběžnou autorizaci požadavků

V tomto okamžiku, když se uživatel pokusí o volání z vývojářské konzole, uživatel je vyzván k přihlášení. Konzole pro vývojáře získá přístupový token jménem uživatele a zahrnuje token v požadavku na rozhraní API.

Co když však někdo zavolá vaše rozhraní API bez tokenu nebo s neplatným tokenem? Například zkuste volat rozhraní API `Authorization` bez záhlaví, volání bude stále procházet. Důvodem je, že správa rozhraní API neověřuje přístupový token v tomto okamžiku. Jednoduše předá `Authorization` záhlaví back-endapi.

Zásady [Ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) můžete použít k předběžné autorizaci požadavků ve správě rozhraní API ověřením přístupových tokenů každého příchozího požadavku. Pokud požadavek nemá platný token, správa rozhraní API jej zablokuje. Můžete například přidat následující `<inbound>` zásady do `Echo API`oddílu zásad v oblasti . Zkontroluje deklaraci cílové skupiny v přístupovém tokenu a vrátí chybovou zprávu, pokud token není platný. Informace o konfiguraci zásad naleznete v tématu [Nastavení nebo úpravy zásad](set-edit-policies.md).

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
> [!NOTE]
> Tato `openid-config` adresa URL odpovídá koncovému bodu v1. Pro koncový `openid-config`bod v2 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`použijte .

## <a name="build-an-application-to-call-the-api"></a>Vytvoření aplikace pro volání rozhraní API

V této příručce jste použili konzolu pro vývojáře `Echo API` ve správě rozhraní API jako ukázkovou klientskou aplikaci k volání chráněného oAuth 2.0. Další informace o tom, jak vytvořit aplikaci a implementovat OAuth 2.0, najdete v [tématu Ukázky kódu služby Azure Active Directory](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Další kroky
* Další informace o [Azure Active Directory a OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o správě rozhraní API.
* Další způsoby zabezpečení back-endové služby naleznete v [tématu Vzájemné ověřování certifikátů](api-management-howto-mutual-certificates.md).

* [Vytvořte instanci služby Správa rozhraní API](get-started-create-service-instance.md).

* [Spravujte své první rozhraní API](import-and-publish.md).

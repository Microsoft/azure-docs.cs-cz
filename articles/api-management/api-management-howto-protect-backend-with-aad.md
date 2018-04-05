---
title: Ochrana rozhraní API OAuth 2.0 pomocí Azure Active Directory a API Management | Microsoft Docs
description: Zjistěte, jak k ochraně back-endu webového rozhraní API pomocí Azure Active Directory a API Management.
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
ms.openlocfilehash: 2c05407d761a8848f9e032aa219960cd7ea6fa93
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Ochrana rozhraní API pomocí Azure Active Directory a API Management OAuth 2.0

Tento průvodce vám ukáže, jak nakonfigurovat instanci aplikace API Management (APIM) k ochraně rozhraní API pomocí protokolu OAuth 2.0 s Azure Active Directory (AAD). 

## <a name="prerequisite"></a>Požadavek
Chcete-li postupujte podle kroků v tomto článku, budete potřebovat:
* APIM instance
* Rozhraní API publikovanému pomocí APIM instance
* Klient služby Azure AD

## <a name="overview"></a>Přehled

Tento průvodce vám ukáže, jak chránit rozhraní API s OAuth 2.0 v APIM. V tomto článku se používá Azure AD jako serveru ověřování (OAuth Server). Dole je rychlý přehled kroků:

1. Zaregistrovat aplikaci (back-end aplikace) ve službě Azure AD představují rozhraní API
2. Registrace jiné aplikace (klientské aplikace) ve službě Azure AD představují klientská aplikace, které potřebuje volat rozhraní API
3. Ve službě Azure AD udělte oprávnění, aby klientská aplikace volat back-end aplikace
4. Konfigurace konzole pro vývojáře používat autorizace uživatelů OAuth 2.0
5. Přidání zásad ověřit jwt k ověření tokenu OAuth pro každého příchozího požadavku

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Zaregistrovat aplikaci ve službě Azure AD představují rozhraní API

Pokud chcete ochránit rozhraní API s Azure AD, prvním krokem je zaregistrovat aplikaci ve službě Azure AD, který představuje rozhraní API. 

Přejděte ke klientovi Azure AD a potom přejděte na **registrace aplikace**.

Vyberte **nové registrace aplikace**. 

Zadejte název aplikace. V tomto příkladu `backend-app` se používá.  

Zvolte **webovou aplikaci nebo API** jako **typ aplikace**. 

Pro **přihlašovací adresa URL**, můžete použít `https://localhost` jako zástupný znak.

Klikněte na **vytvořit**.

Po vytvoření aplikace, poznamenejte si **ID aplikace** pro použití v následném kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrace jiné aplikace ve službě Azure AD představují klientské aplikace

Každá aplikace klienta, které potřebuje volat rozhraní API musí být registrován jako aplikace ve službě Azure AD i. V této příručce použijeme konzole pro vývojáře v portálu pro vývojáře APIM jako vzorku klientskou aplikaci. 

Je potřeba zaregistrovat ve službě Azure AD představují konzole pro vývojáře jiná aplikace.

Klikněte na **nové registrace aplikace** znovu. 

Zadejte název aplikace a zvolte **webovou aplikaci nebo API** jako **typ aplikace**. V tomto příkladu `client-app` se používá.  

Pro **přihlašovací adresa URL**, můžete použít `https://localhost` jako zástupný symbol nebo použijte adresu URL přihlašovací vaší APIM instance. V tomto příkladu `https://contoso5.portal.azure-api.net/signin` se používá.

Klikněte na **vytvořit**.

Po vytvoření aplikace, poznamenejte si **ID aplikace** pro použití v následném kroku. 

Nyní potřebujeme vytvořit sdílený tajný klíč klienta pro tuto aplikaci pro použití v následném kroku.

Klikněte na **nastavení** znovu, přejděte na **klíče**.

V části **hesla**, poskytovat **klíče popis**, zvolte, kdy by měl klíč vyprší a klikněte na **Uložit**.

Poznamenejte si hodnotu klíče. 

## <a name="grant-permissions-in-aad"></a>Udělení oprávnění v AAD

Nyní zaregistrovali dvě aplikace k reprezentaci rozhraní API (back-end aplikace) a konzole pro vývojáře (klienta aplikace), musíme udělit oprávnění, aby klientská aplikace volat back-end aplikace.  

Přejděte na **registrace aplikace** znovu. 

Klikněte na `client-app` a přejděte na **nastavení**.

Klikněte na **požadovaná oprávnění** a potom **přidat**.

Klikněte na **vybrat rozhraní API** a vyhledejte řetězec `backend-app`.

Zkontrolujte `Access backend-app` pod **delegovaná oprávnění**. 

Klikněte na **vyberte** a potom **provádí**. 

> [!NOTE]
> Pokud **Windows** **Azure Active Directory** nejsou uvedené v části oprávnění k ostatním aplikacím, klikněte na tlačítko **přidat** a přidejte ji ze seznamu.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Povolení autorizace OAuth 2.0 uživatelů v konzole pro vývojáře

V tomto okamžiku jste vytvořili naše aplikace ve službě Azure AD a mít udělena příslušná oprávnění pro povolit klientské aplikace volat back-end aplikace. 

V této příručce použijeme konzole pro vývojáře jako klientské aplikace. Následující kroky popisují, jak povolit autorizace uživatelů OAuth 2.0 v konzole pro vývojáře 

Přejděte k vaší instanci APIM.

Klikněte na **OAuth 2.0** a potom **přidat**.

Zadejte **zobrazovaný název** a **popis**.

Registrace klienta stránce adresu URL, ** zadejte hodnotu zástupného symbolu, jako `http://localhost`.  **Adresa URL stránky registrace klienta** odkazuje na stránku, uživatelé můžete vytvořit a nakonfigurovat svoje vlastní účty zprostředkovatelů OAuth 2.0, které podporují správu uživatelské účty. V tomto příkladu uživatele není vytvořit a nakonfigurovat svoje vlastní účty, tak se používá zástupný symbol.

Zkontrolujte **autorizační kód** jako **typy udělení autorizace**.

Potom zadejte **adresu URL koncového bodu autorizace** a **adresu URL koncového bodu Token**.

Tyto hodnoty lze získat z **koncové body** stránky v klientovi služby Azure AD. Chcete-li získat přístup k koncových bodů, přejděte na **registrace aplikace** stránku znovu a klikněte na **koncové body**.

Kopírování **koncový bod autorizace OAuth 2.0** a vložte ji do **adresu URL koncového bodu autorizace** textové pole.

Kopírování **koncový bod tokenu OAuth 2.0** a vložte ji do **adresu URL koncového bodu Token** textové pole.

Kromě vkládání v koncový bod token, přidáte další text parametr s názvem **prostředků** a používat hodnotu **ID aplikace** back-end aplikace.

Potom zadejte pověření klienta. Jedná se o pověření pro klientské aplikace.

Pro **Id klienta**, použijte **ID aplikace** pro klientské aplikace.

Pro **tajný klíč klienta**, dříve použít klíč, který jste vytvořili pro klientské aplikace. 

Okamžitě následující tajný klíč klienta je **redirect_url** pro autorizaci kód udělit typu.

Poznamenejte si adresu URL.

Klikněte na **vytvořit**.

Přejděte zpět **nastavení** stránku vaší klientské aplikace.

Klikněte na **adresy URL odpovědí** a vložte **redirect_url** v prvním řádku. V tomto příkladu jsme nahradit `https://localhost` s adresou URL v prvním řádku.  

Nyní jsme nakonfigurovali serveru autorizace OAuth 2.0, konzole pro vývojáře měli být schopní získat přístupové tokeny z Azure AD. 

Dalším krokem je povolit autorizace uživatelů OAuth 2.0 pro naše rozhraní API, tak, aby konzole pro vývojáře zná ho musí získat přístupový token jménem uživatele před prováděním volání do našich API.

Přejděte k vaší instanci APIM, přejděte na **rozhraní API**.

Klikněte na rozhraní API, které chcete chránit. V tomto příkladu použijeme `Echo API`.

Přejděte na **nastavení**.

V části **zabezpečení**, zvolte **OAuth 2.0** a vyberte server OAuth 2.0 jsme nakonfigurovali dříve. 

Klikněte na **Uložit**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Úspěšně volat rozhraní API z portálu pro vývojáře

Teď, když je zapnutá autorizace OAuth 2.0 uživatelů `Echo API`, konzole pro vývojáře se získat přístupový token jménem uživatele před voláním rozhraní API.

Přejděte na všechny operace v rámci `Echo API` na portál pro vývojáře a klikněte na **vyzkoušet**, který se nám převést na konzole pro vývojáře.

Poznámka: novou položku v **autorizace** části odpovídající serveru ověřování, který jste právě přidali.

Vyberte **autorizační kód** z autorizaci rozevíracího seznamu a jste vyzváni k přihlášení ke klientovi Azure AD. Pokud jste již přihlášeni pomocí účtu, nemusíte být vyzváni.

Po úspěšném přihlášení se `Authorization` záhlaví budou přidány do požadavek s přístupový token ze služby Azure AD. 

Ukázkový token pro vypadá takto: níže je kódováním Base64.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

Klikněte na tlačítko **odeslat** a mělo by být možné úspěšně volat rozhraní API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurace zásad ověřování JWT předem autorizovat požadavků

Nyní když se uživatel pokusí o volání z konzole pro vývojáře, uživatel se vyzve k přihlášení a konzole pro vývojáře získá jménem uživatele tokenu přístupu. Všechno funguje podle očekávání. Co když však někdo volá našem rozhraní API bez token nebo se neplatný token? Například můžete zkusit odstranit `Authorization` záhlaví a zjistí, stále možné volat rozhraní API. Důvodem je, protože APIM nelze ověřit Token přístupu v tomto okamžiku. Jednoduše předává `Auhtorization` hlavičky k back-end rozhraní API.

Můžeme použít [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) zásad předem autorizovat požadavků v APIM ověřením přístupových tokenů každého příchozího požadavku. Pokud požadavek nemá platný token, je blokována API Management a není předají back-end. Například nyní můžete přidat níže zásady `<inbound>` část zásady `Echo API`. Kterou kontroluje deklarace cílové skupiny v přístupový token a vrátí chybovou zprávu, pokud token není platný. Informace o tom, jak nakonfigurovat zásady najdete v tématu [nastavit nebo upravit zásady](set-edit-policies.md).

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

V této příručce, použili jsme konzole pro vývojáře v APIM jako ukázkovou aplikaci klienta k volání `Echo API` chráněn OAuth 2.0. Další informace o tom, jak sestavit aplikaci a implementovat toku OAuth 2.0, najdete v tématu [ukázky kódu Azure Active Directory](../active-directory/develop/active-directory-code-samples.md).

## <a name="next-steps"></a>Další postup
* Další informace o [Azure Active Directory a OAuth2.0](../active-directory/develop/active-directory-authentication-scenarios.md)
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o službě API Management.
* Další způsoby zabezpečení back-end službu, naleznete v části [vzájemného ověření certifikátů](api-management-howto-mutual-certificates.md).

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md

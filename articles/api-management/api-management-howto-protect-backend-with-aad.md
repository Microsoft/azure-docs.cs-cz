---
title: Ochrana rozhraní API pomocí OAuth 2,0 s AAD a API Management
titleSuffix: Azure API Management
description: Přečtěte si, jak chránit back-end webového rozhraní API pomocí Azure Active Directory a API Management.
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
ms.openlocfilehash: 36b4b597ae70642fee8726555ea71b5164c13cca
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889383"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Chraňte rozhraní API pomocí OAuth 2,0 s Azure Active Directory a API Management

V této příručce se dozvíte, jak nakonfigurovat instanci Azure API Management k ochraně rozhraní API pomocí protokolu OAuth 2,0 s Azure Active Directory (Azure AD). 

> [!NOTE]
> Tato funkce je dostupná v úrovních **Developer**, **Standard** a **Premium** API Management.

## <a name="prerequisites"></a>Požadavky
Pokud chcete postupovat podle kroků v tomto článku, musíte mít:
* Instance API Management
* Publikované rozhraní API, které používá instanci API Management
* Tenant Azure AD

## <a name="overview"></a>Přehled

Tady je rychlý přehled kroků:

1. Zaregistrujte aplikaci (back-end-App) ve službě Azure AD, která bude představovat rozhraní API.
2. Zaregistrujte do služby Azure AD jinou aplikaci (klientskou aplikaci), která představuje klientskou aplikaci, která vyžaduje volání rozhraní API.
3. V Azure AD udělte oprávnění, aby klientská aplikace mohla volat back-end aplikaci.
4. Nakonfigurujte konzolu pro vývojáře, aby volala rozhraní API pomocí autorizace uživatele OAuth 2,0.
5. Přidejte zásadu **Validate-JWT** k ověření tokenu OAuth pro každý příchozí požadavek.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Registrace aplikace ve službě Azure AD, která představuje rozhraní API

Aby bylo možné chránit rozhraní API pomocí Azure AD, prvním krokem je registrace aplikace v Azure AD, která představuje rozhraní API. 

1. Pro registraci aplikace použijte [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **registrace rozhraní API**.

1. Vyberte **Nová registrace**. 

1. Když se zobrazí **stránka Registrace aplikace**, zadejte registrační informace vaší aplikace: 
    - V části **název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, jako je například *back-end aplikace*. 
    - V části **podporované typy účtů** vyberte možnost, která vyhovuje vašemu scénáři. 

1. Část **identifikátor URI přesměrování** nechejte prázdná.

1. Výběrem možnosti **Registrovat** aplikaci vytvořte. 

1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití.

1. Vyberte **zveřejnit rozhraní API** a nastavte **identifikátor URI ID aplikace** na výchozí hodnotu. Poznamenejte si tuto hodnotu pro pozdější.

1. Kliknutím na tlačítko **Přidat obor** zobrazíte stránku **Přidat obor** . Pak vytvořte nový obor podporovaný rozhraním API (například `Files.Read`). Nakonec pro vytvoření oboru vyberte tlačítko **Přidat obor** . Opakováním tohoto kroku přidejte všechny obory podporované vaším rozhraním API.

1. Když se vytvoří obory, poznamenejte si je, aby je bylo potřeba použít v dalším kroku. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Registrace jiné aplikace ve službě Azure AD, která představuje klientskou aplikaci

Každá klientská aplikace, která volá rozhraní API, musí být registrovaná taky jako aplikace v Azure AD. V tomto příkladu je klientská aplikace konzolou pro vývojáře na portálu API Management Developer Portal. Tady je postup, jak zaregistrovat jinou aplikaci ve službě Azure AD, která bude reprezentovat konzolu pro vývojáře.

1. Pro registraci aplikace použijte [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **registrace rozhraní API**.

1. Vyberte **Nová registrace**.

1. Když se zobrazí **stránka Registrace aplikace**, zadejte registrační informace vaší aplikace: 
    - V části **název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, jako je například *klientská aplikace*. 
    - V části **podporované typy účtů** vyberte **účty v jakémkoli adresáři organizace (libovolný adresář služby Azure AD – víceklientské tenant)** . 

1. V části **identifikátor URI pro přesměrování** vyberte `Web` a zadejte adresu URL `https://contoso5.portal.azure-api.net/signin`.

1. Výběrem možnosti **Registrovat** aplikaci vytvořte. 

1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití.

Teď pro tuto aplikaci vytvořte tajný klíč klienta, který se použije v dalším kroku.

1. V seznamu stránek klientské aplikace vyberte **certifikáty & tajných**kódů a vyberte **nový tajný klíč klienta**.

1. V části **Přidat tajný klíč klienta**zadejte **Popis**. Zvolte, kdy má klíč vypršet, a vyberte **Přidat**.

Po vytvoření tajného klíče si poznamenejte klíčovou hodnotu pro použití v dalším kroku. 

## <a name="grant-permissions-in-azure-ad"></a>Udělení oprávnění ve službě Azure AD

Teď, když jste zaregistrovali dvě aplikace, které představují rozhraní API a konzolu pro vývojáře, musíte udělit oprávnění, aby klientská aplikace mohla volat back-end aplikaci.  

1. Chcete-li udělit oprávnění k klientské aplikaci, použijte [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **registrace rozhraní API**.

1. Vyberte klientskou aplikaci. Pak v seznamu stránek aplikace vyberte **oprávnění rozhraní API**.

1. Vyberte **Přidat oprávnění**.

1. V části **Vybrat rozhraní API**vyberte **Moje rozhraní API**a pak vyhledejte a vyberte back-end aplikaci.

1. V části **delegovaná oprávnění**vyberte požadovaná oprávnění pro back-end aplikaci a pak vyberte **Přidat oprávnění**.

1. Volitelně můžete na stránce **oprávnění rozhraní API** vybrat **udělit souhlas správce pro \<název-tenanta >** udělit souhlas jménem všech uživatelů v tomto adresáři. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Povolení autorizace uživatele OAuth 2,0 v konzole pro vývojáře

V tuto chvíli jste vytvořili aplikace v Azure AD a udělili jste patřičná oprávnění, aby klientská aplikace mohla volat back-end aplikaci. 

V tomto příkladu je konzole pro vývojáře klientská aplikace. Následující kroky popisují, jak povolit autorizaci uživatelů OAuth 2,0 v konzole pro vývojáře. 

1. V Azure Portal přejděte k instanci API Management.

1. Vyberte **OAuth 2,0** > **Přidat**.

1. Zadejte **Zobrazovaný název** a **Popis**.

1. V poli **Adresa URL registrační stránky klienta**zadejte hodnotu zástupného symbolu, například `http://localhost`. **Adresa URL stránky pro registraci klienta** odkazuje na stránku, kterou můžou uživatelé použít k vytvoření a konfiguraci vlastních účtů pro poskytovatele OAuth 2,0, kteří to podporují. V tomto příkladu uživatelé nevytvářejí a nekonfigurují vlastní účty, takže místo toho použijete zástupný symbol.

1. V případě **autorizačních typů udělte**vyberte **autorizační kód**.

1. Zadejte **adresu URL koncového bodu autorizace** a **adresu URL koncového bodu tokenu**. Tyto hodnoty načtěte ze stránky **koncových bodů** v TENANTOVI Azure AD. Přejděte na stránku **Registrace aplikací** znovu a vyberte **koncové body**.


1. Zkopírujte **koncový bod autorizace OAuth 2,0**a vložte ho do textového pole **Adresa URL koncového bodu autorizace** . V části Metoda žádosti o autorizaci vyberte **post** .

1. Zkopírujte **koncový bod tokenu OAuth 2,0**a vložte ho do textového pole **Adresa URL koncového bodu tokenu** . 

    >[!IMPORTANT]
    > Můžete použít koncové body **v1** nebo **v2** . V závislosti na zvolené verzi ale bude tento krok jiný. Doporučujeme používat koncové body v2. 

1. Použijete-li koncové body **v1** , přidejte parametr body s názvem **Resource**. Pro hodnotu tohoto parametru použijte **ID aplikace** back-endové aplikace. 

1. Pokud používáte koncové body **v2** , použijte obor, který jste vytvořili pro back-end aplikaci, do pole **výchozí obor** .

1. Dále zadejte pověření klienta. Toto jsou přihlašovací údaje klienta aplikace.

1. Pro **ID klienta**použijte **ID aplikace** klienta aplikace.

1. Pro **tajný klíč klienta**použijte klíč, který jste vytvořili dříve pro klienta aplikace. 

1. Hned za tajný klíč klienta je **redirect_url** typu udělení autorizačního kódu. Tuto adresu URL si poznamenejte.

1. Vyberte **Vytvořit**.

1. Vraťte se do svého klienta – aplikace a vyberte **ověřování**.

1. V části **identifikátory URI pro přesměrování**vyberte typ jako **Web**, do pole **identifikátor URI pro přesměrování**vložte **redirect_url** a pak ho uložte.

Teď, když jste nakonfigurovali autorizační Server OAuth 2,0, může konzola pro vývojáře získat přístupové tokeny ze služby Azure AD. 

Dalším krokem je povolení autorizace uživatele OAuth 2,0 pro vaše rozhraní API. To umožňuje konzole vývojářů zjistit, že před voláním rozhraní API potřebuje získat přístupový token jménem uživatele.

1. Přejděte k instanci API Management a přejděte na **rozhraní API**.

2. Vyberte rozhraní API, které chcete chránit. Můžete například použít `Echo API`.

3. Přejděte na **nastavení**.

4. V části **zabezpečení**zvolte **OAuth 2,0**a vyberte server OAuth 2,0, který jste nakonfigurovali dříve. 

5. Vyberte **Uložit**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Úspěšné volání rozhraní API z portálu pro vývojáře

> [!NOTE]
> Tato část se nevztahuje na úroveň **spotřeby** , která nepodporuje portál pro vývojáře.

Když je teď v rozhraní API povolená autorizace uživatele OAuth 2,0, před voláním rozhraní API Konzola pro vývojáře obdrží přístupový token jménem uživatele.

1. Přejděte na libovolnou operaci v rozhraní API na portálu pro vývojáře a vyberte **vyzkoušet**. Tím se vám zobrazí Konzola pro vývojáře.

2. Poznamenejte si novou položku v sekci **autorizace** , která odpovídá autorizačnímu serveru, který jste právě přidali.

3. V rozevíracím seznamu autorizace vyberte **autorizační kód** a zobrazí se výzva, abyste se přihlásili do TENANTA Azure AD. Pokud jste již k účtu přihlášeni, pravděpodobně nebudete vyzváni.

4. Po úspěšném přihlášení se do žádosti přidá hlavička `Authorization` s přístupovým tokenem z Azure AD. Následuje ukázkový token (kódovaný v kódování Base64):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. Vyberte **Odeslat**a můžete volat rozhraní API úspěšně.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurace ověřovacích zásad JWT pro předběžné ověření požadavků

V tomto okamžiku, když se uživatel pokusí uskutečnit volání z konzole pro vývojáře, se uživateli zobrazí výzva k přihlášení. Konzola pro vývojáře získá přístupový token jménem uživatele a zahrne do žádosti o rozhraní API token.

Ale co když někdo volá vaše rozhraní API bez tokenu nebo s neplatným tokenem? Například zkuste volat rozhraní API bez hlavičky `Authorization`, volání bude dál probíhat. Důvodem je, že API Management v tomto okamžiku neověřuje přístupový token. Jednoduše předá hlavičku `Authorization` back-endu rozhraní API.

Pomocí zásady [ověřit token JWT](api-management-access-restriction-policies.md#ValidateJWT) můžete předem autorizovat žádosti v API Management tím, že ověříte přístupové tokeny každého příchozího požadavku. Pokud požadavek nemá platný token, API Management ho zablokuje. Přidejte například následující zásadu do části `<inbound>` zásady `Echo API`. Kontroluje deklaraci identity cílové skupiny v přístupovém tokenu a pokud token není platný, vrátí chybovou zprávu. Informace o tom, jak nakonfigurovat zásady, najdete v tématu [nastavení nebo úprava zásad](set-edit-policies.md).

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID URI of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>Sestavení aplikace pro volání rozhraní API

V této příručce jste jako ukázkovou klientskou aplikaci použili v nástroji API Management konzolu pro vývojáře, která volá `Echo API` chráněnou OAuth 2,0. Další informace o tom, jak sestavit aplikaci a implementovat OAuth 2,0, najdete v tématu [Azure Active Directory Samples Code](../active-directory/develop/sample-v1-code.md).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [Azure Active Directory a OAuth 2.0](../active-directory/develop/authentication-scenarios.md).
* Podívejte se na další [videa](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o API Management.
* Další způsoby zabezpečení back-endové služby najdete v tématu [vzájemné ověřování certifikátů](api-management-howto-mutual-certificates.md).

* [Vytvořte instanci služby API Management](get-started-create-service-instance.md).

* [Spravujte své první rozhraní API](import-and-publish.md).

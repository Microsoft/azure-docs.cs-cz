---
title: 'Kurz: registrace jednostránkové aplikace'
titleSuffix: Azure AD B2C
description: V tomto kurzu se dozvíte, jak pomocí Azure Portal zaregistrovat jednostránkovou aplikaci (SPA) v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2eaf1dce432821dcfc693dc69dcf975a3d8be8d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503857"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>Kurz: registrace jednostránkové aplikace (SPA) v Azure Active Directory B2C

Než můžou vaše [aplikace](application-types.md) pracovat s Azure Active Directory B2C (Azure AD B2C), musí být zaregistrované v tenantovi, které spravujete. V tomto kurzu se dozvíte, jak zaregistrovat jednostránkovou aplikaci ("SPA") pomocí Azure Portal.

## <a name="overview-of-authentication-options"></a>Přehled možností ověřování

Mnohé moderní webové aplikace jsou sestavené jako jednostránkové aplikace na straně klienta ("jednostránkové"). Vývojáři si je zapisují pomocí JavaScriptu nebo architektury SPA, jako je například úhlová, Vue a reakce. Tyto aplikace běží ve webovém prohlížeči a mají různé charakteristiky ověřování než tradiční webové aplikace na straně serveru.

Azure AD B2C poskytuje **dvě** možnosti, jak povolit jednostránkové aplikace pro přihlašování uživatelů a získat tokeny pro přístup k back-endové službě nebo k webovým rozhraním API:

### <a name="authorization-code-flow-with-pkce"></a>Tok autorizačního kódu (s PKCE)
- [Tok autorizačního kódu OAuth 2,0 (s PKCE)](./authorization-code-flow.md). Tok autorizačního kódu umožňuje aplikaci výměnu autorizačního kódu pro tokeny **ID** , které reprezentují ověřeného uživatele a **přístupové** tokeny potřebné pro volání chráněných rozhraní API. Kromě toho vrací **aktualizační** tokeny, které poskytují dlouhodobý přístup k prostředkům jménem uživatelů bez nutnosti interakce s těmito uživateli. 

Toto je **doporučený** postup. Pokud máte s omezenými obnovovacími tokeny, pomůže vaše aplikace přizpůsobovat se na [moderní omezení ochrany osobních údajů souborů cookie v prohlížeči](../active-directory/develop/reference-third-party-cookies-spas.md), jako je

Pokud chcete využít tento tok, vaše aplikace může používat knihovnu ověřování, která ho podporuje, například [MSAL.js 2. x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa). 

![Jednostránkové aplikace – ověřování](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>Implicitní tok udělení
- [Implicitní tok OAuth 2,0](implicit-flow-single-page-application.md). Některá rozhraní, například [MSAL.js 1. x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp), podporují pouze implicitní tok udělení. Tok implicitního udělení umožňuje aplikaci získat **ID** a **přístupové** tokeny. Na rozdíl od toku autorizačního kódu nevrátí tok implicitního udělení **obnovovací token**. 

![Jednostránkové aplikace – implicitní](./media/tutorial-single-page-app/spa-app.svg)

Tento tok ověřování nezahrnuje scénáře aplikací, které používají rozhraní JavaScript pro různé platformy, jako jsou například elektronicky a reagují – nativní. Tyto scénáře vyžadují další možnosti pro interakci s nativními platformami.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud jste ještě nevytvořili vlastního [tenanta Azure AD B2C](tutorial-create-tenant.md), vytvořte ho hned teď. Můžete použít stávajícího klienta Azure AD B2C.

## <a name="register-the-spa-application"></a>Registrace aplikace SPA

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací**a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *spaapp1*.
1. V části **podporované typy účtů**vyberte **účty v jakémkoli zprostředkovateli identity nebo organizačním adresáři (pro ověřování uživatelů pomocí toků uživatelů)** .
1. V části **identifikátor URI pro přesměrování**vyberte **JEDNOSTRÁNKOVÉ aplikace (Spa)** a potom `https://jwt.ms` do textového pole Adresa URL zadejte.

    Identifikátor URI přesměrování je koncový bod, na který se uživatel pošle prostřednictvím autorizačního serveru (Azure AD B2C, v tomto případě) po dokončení jeho interakce s uživatelem a ke kterému se po úspěšné autorizaci pošle přístupový token nebo autorizační kód. V produkční aplikaci je obvykle veřejně přístupný koncový bod, ve kterém je vaše aplikace spuštěná, třeba `https://contoso.com/auth-response` . Pro účely testování, jako je tento kurz, můžete ho nastavit na `https://jwt.ms` , webovou aplikaci ve vlastnictví společnosti Microsoft, která zobrazuje Dekódovatelné obsah tokenu (obsah tokenu nikdy nezůstane v prohlížeči). Během vývoje aplikace můžete přidat koncový bod, ve kterém vaše aplikace naslouchá místně, například `http://localhost:5000` . V registrovaných aplikacích můžete kdykoli přidat a změnit identifikátory URI pro přesměrování.

    Pro identifikátory URI přesměrování platí následující omezení:

    * `https`Pokud nepoužíváte, musí adresa URL odpovědi začínat schématem `localhost` .
    * Adresa URL odpovědi rozlišuje velká a malá písmena. Jeho velikost se musí shodovat s písmenem adresy URL vaší běžící aplikace. Například pokud vaše aplikace obsahuje jako součást cesty `.../abc/response-oidc` , nezadávejte `.../ABC/response-oidc` v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje cesty jako rozlišování velkých a malých písmen, mohou být soubory cookie přidružené k `.../abc/response-oidc` vyloučeny při přesměrování na neshodnou `.../ABC/response-oidc` adresu URL.

1. V části **oprávnění**zaškrtněte políčko *udělit souhlas správcům oprávnění OpenID a offline_access* .
1. Vyberte **Zaregistrovat**.


## <a name="enable-the-implicit-flow"></a>Povolit implicitní tok
Pokud používáte implicitní tok, musíte povolit tok implicitního udělení v registraci aplikace.

1. V nabídce vlevo v části **Spravovat**vyberte **ověřování**.
1. V části **implicitní udělení**vyberte zaškrtávací políčka **přístupové tokeny** i **tokeny ID** .
1. Vyberte **Uložit**.

## <a name="migrate-from-the-implicit-flow"></a>Migrace z implicitního toku

Pokud máte existující aplikaci, která používá implicitní tok, doporučujeme migrovat na použití toku autorizačního kódu pomocí rozhraní, které ho podporuje, například [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

Když všechny vaše produkční aplikace s jednou stránkou reprezentované registrací aplikace používají tok autorizačního kódu, zakažte nastavení implicitního toku udělení. 

1. V nabídce vlevo v části **Spravovat**vyberte **ověřování**.
1. V části **implicitní udělení**zrušte zaškrtnutí políček **přístupové tokeny** i **tokeny ID** .
1. Vyberte **Uložit**.

Pokud necháte implicitní tok povolený (zaškrtnuto), můžou aplikace, které používají implicitní tok, fungovat i nadále.

* * *

## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte, jak vytvořit toky uživatelů, aby se uživatelé mohli zaregistrovat, přihlašovat a spravovat své profily.

> [!div class="nextstepaction"]
> [Vytváření toků uživatelů v Azure Active Directory B2C >](tutorial-create-user-flows.md)

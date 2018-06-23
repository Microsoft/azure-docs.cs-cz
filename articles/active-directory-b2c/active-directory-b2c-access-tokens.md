---
title: Požaduje přístupových tokenů v Azure Active Directory B2C | Microsoft Docs
description: Tento článek vám ukáže, jak nastavit klientskou aplikaci a získat přístupový token.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/09/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2da5d05767dddfe653e0a0ba65a21b7e755b3828
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330714"
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: Požaduje přístupové tokeny

Přístupový token (označené jako **přístup\_tokenu** v odpovědi z Azure AD B2C) je formulář tokenu zabezpečení, který může klient používat pro přístup k prostředkům, které jsou zabezpečeny [serveru ověřování](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), jako jsou webové rozhraní API. Přístupové tokeny, jsou reprezentovány jako [Jwt](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) a obsahují informace o server určený prostředků a udělí oprávnění k serveru. Při volání metody server prostředků, přístupový token musí být přítomné v žádosti HTTP.

Tento článek popisuje postup konfigurace klientské aplikace a webové rozhraní API, aby bylo možné získat **přístup\_tokenu**.

> [!NOTE]
> **Webové rozhraní API řetězy (On-Behalf-Of) nepodporuje Azure AD B2C.**
>
> Mnoho architektur zahrnuje webové rozhraní API, které potřebuje volat podřízené webové rozhraní API, i zabezpečené pomocí Azure AD B2C. Tento scénář je častý u nativních klientů, které mají webové rozhraní API back-end, které zavolá online službu Microsoftu, jako je například Azure AD Graph API.
>
> Tento scénář zřetězených webových rozhraní API může být podporován pomocí udělení přihlašovacích údajů nosiče OAuth 2.0 JWT, označováno také jako tok On-Behalf-Of. Tok On-Behalf-Of však není aktuálně implementována v Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Webové rozhraní API pro registraci a publikování oprávnění

Před vyžádáním token přístupu, musíte nejprve registrovat webového rozhraní API a publikovat oprávnění (oborům), která můžete do klientské aplikace.

### <a name="register-a-web-api"></a>Registrace webové rozhraní API

1. V nabídce Funkce Azure AD B2C na portálu Azure, klikněte na tlačítko **aplikace**.
1. Klikněte na tlačítko **+ přidat** v horní nabídce.
1. Zadejte **Název** aplikace, který popíše aplikaci uživatelům. Můžete například zadat "API společnosti Contoso".
1. Přepněte přepínač **Zahrnout webovou aplikaci / webové rozhraní API** na **Ano**.
1. Zadejte hodnotu pro libovolný **adresy URL odpovědí**. Zadejte například `https://localhost:44316/`. Hodnota není důležité, protože rozhraní API by neměl být přijetí tokenu přímo z Azure AD B2C.
1. Zadejte **Identifikátor URI ID aplikace**. To je identifikátor použitý pro vaše webového rozhraní API. Například 'poznámky, zadejte do pole. **Identifikátor ID URI aplikace** by pak bylo `https://{tenantName}.onmicrosoft.com/notes`.
1. Pro registraci aplikace klikněte na **Vytvořit**.
1. Klikněte na aplikaci, kterou jste právě vytvořili, a poznamenejte si globálně jedinečné**ID klienta aplikace**, které použijete později ve svém kódu.

### <a name="publishing-permissions"></a>Publikování oprávnění

Obory, které jsou obdobou oprávnění, jsou nezbytné, pokud vaše aplikace je volání rozhraní API. Některé příklady oborů jsou "číst" nebo "zápis". Předpokládejme, že chcete web nebo nativní aplikaci "číst" z rozhraní API. Vaše aplikace by volání Azure AD B2C a žádosti o token přístupu, která poskytuje přístup k oboru "číst". Aby Azure AD B2C pro vydávání takový token přístupu musí udělit oprávnění ke "čtení" z rozhraní API pro konkrétní aplikace. K tomuto účelu potřebuje vaše rozhraní API nejprve publikovat oboru "číst".

1. V rámci Azure AD B2C **aplikace** nabídce otevřete rozhraní API webové aplikace ("API společnosti Contoso").
1. Klikněte na **Publikované obory**. Tady můžete definovat oprávnění (obory), která lze udělit ostatním aplikacím.
1. Přidat **hodnoty oboru** podle potřeby (například "číst"). Ve výchozím nastavení bude definován obor „user_impersonation“. To můžete ignorovat, pokud chcete. Zadejte popis oboru ve **název oboru** sloupce.
1. Klikněte na **Uložit**.

> [!IMPORTANT]
> **Název oboru** je popis **hodnota oboru**. Pokud používáte oboru, nezapomeňte použít **hodnota oboru**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Udělit nativní nebo webové aplikaci oprávnění k webovému rozhraní API

Po rozhraní API je nakonfigurována pro publikování obory, klientská aplikace musí být udělena tyto obory prostřednictvím portálu Azure.

1. Přejděte na **aplikace** nabídky v nabídce Funkce Azure AD B2C.
1. Registrace aplikace klienta ([webové aplikace](active-directory-b2c-app-registration.md#register-a-web-app) nebo [nativního klienta](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) Pokud již nemáte. Použijete-li tato příručka jako počáteční bod, budete muset zaregistrovat klientské aplikace.
1. Klikněte na **přístup pomocí rozhraní API**.
1. Klikněte na **Přidat**.
1. Vyberte webového rozhraní API a chcete udělit obory (oprávnění).
1. Klikněte na **OK**.

> [!NOTE]
> Azure AD B2C nezeptá vašeho klienta aplikace uživatelům na jejich souhlasu. Místo toho jsou poskytovány všechny souhlasu správce, na základě oprávnění nakonfigurovaná mezi aplikací popsaných výše. Pokud odvolán udělit oprávnění pro aplikaci, všichni uživatelé, kteří byli schopní získat tato oprávnění už nebude moci učinit.

## <a name="requesting-a-token"></a>Požadování tokenu

Žádosti o token přístupu, klientská aplikace musí určovat požadované oprávnění v **oboru** parametr požadavku. Například zadejte **hodnota oboru** "číst" pro rozhraní API, který má **identifikátor ID URI aplikace** z `https://contoso.onmicrosoft.com/notes`, oboru by `https://contoso.onmicrosoft.com/notes/read`. Dole je příklad autorizační kód požadavku na `/authorize` koncový bod.

> [!NOTE]
> Vlastní domény nejsou aktuálně podporované společně s přístupových tokenů. Je nutné použít doménu tenantName.onmicrosoft.com v adrese URL žádosti.

```
https://login.microsoftonline.com/tfp/<tenantName>.onmicrosoft.com/<yourPolicyId>/oauth2/v2.0/authorize?client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Chcete-li získat více oprávnění ve stejném požadavku, můžete přidat více položek v jedné **oboru** parametr, oddělené mezerami. Příklad:

Adresa URL dekódovat:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

Kódovaná adresou URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Může požádat o další obory (oprávnění) pro prostředek, než co je povolen pro klientské aplikace. Pokud to tento případ, volání bude úspěšné, pokud alespoň jeden je povoleno. Výsledná **přístup\_tokenu** bude mít svůj požadavek "spojovací bod služby" zahrnovat jenom oprávnění, které byly úspěšně uděleno.

> [!NOTE] 
> Nepodporujeme vyžadování oprávnění pro dva různé webové prostředky ve stejném požadavku. Tento druh požadavek selže.

### <a name="special-cases"></a>Zvláštní případy

Standardní OpenID Connect určuje několik hodnot speciální "obor". Následující zvláštní obory představují oprávnění k "získat přístup na profil uživatele":

* **openid**: to požadavky tokenu ID
* **offline\_přístup**: to požadavky obnovovací token (pomocí [tok ověřování kódu](active-directory-b2c-reference-oauth-code.md)).

Pokud `response_type` parametr `/authorize` žádost obsahuje `token`, `scope` parametr musí obsahovat alespoň jeden prostředek oboru (jiné než `openid` a `offline_access`), bude udělen. Jinak `/authorize` požadavku se ukončí se selháním.

## <a name="the-returned-token"></a>Vrácený tokenu

V úspěšně minted **přístup\_tokenu** (buď z `/authorize` nebo `/token` koncového bodu), bude k dispozici následující deklarace identity:

| Název | Deklarovat | Popis |
| --- | --- | --- |
|Cílová skupina |`aud` |**ID aplikace** jednoho prostředku, který token uděluje přístup k. |
|Rozsah |`scp` |Oprávnění udělená k prostředku. Více udělená oprávnění budou oddělte mezerou. |
|Autorizovaný strany |`azp` |**ID aplikace** klientské aplikace, která žádost iniciovala. |

Pokud vaše rozhraní API obdrží **přístup\_tokenu**, musí [ověřit token](active-directory-b2c-reference-tokens.md) prokázat, že token pravý a že má správné deklarace identity.

Snažíme se vždy otevřený a názory a návrhy! Pokud máte jakékoli problémy s tímto tématem, prosím zveřejnit na Stack Overflow pomocí značky [, azure ad b2c,](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Pro žádosti o funkce, přidejte je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Další postup

* Sestavení webového rozhraní API pomocí [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Sestavení webového rozhraní API pomocí [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)

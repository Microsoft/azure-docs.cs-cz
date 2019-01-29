---
title: Žádosti o tokeny přístupu v Azure Active Directory B2C | Dokumentace Microsoftu
description: Tento článek vám ukáže postup nastavení klienta aplikace a získání přístupového tokenu.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 86c62c021c6668783b3f843a908f4b17845f8c72
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172982"
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C: Žádá se o přístupové tokeny.

Přístupový token (označené jako **přístup\_token** v odpovědi z Azure AD B2C) je forma tokenu zabezpečení, který může klient používat pro přístup k prostředkům, které jsou zabezpečené pomocí [autorizační server](active-directory-b2c-reference-protocols.md), jako je například webové rozhraní API. Přístupové tokeny jsou reprezentovány ve formě [tokeny Jwt](active-directory-b2c-reference-tokens.md) a obsahují informace o serveru zamýšleného prostředku a udělená oprávnění k serveru. Při volání serveru prostředku, musí být přístupový token v požadavku HTTP.

Tento článek popisuje, jak nakonfigurovat klientskou aplikaci a aby bylo možné získat rozhraní web API **přístup\_token**.

> [!NOTE]
> **Webové rozhraní API řetězy (On-Behalf-Of) není podporována službou Azure AD B2C.**
>
> Mnoho architektur zahrnuje webové rozhraní API, které potřebuje volat podřízené webové rozhraní API, obě jsou zabezpečené pomocí Azure AD B2C. Tento scénář je častý u nativních klientů, které mají webového rozhraní API back-end, který pak zavolá online službu Microsoftu, jako je například Azure AD Graph API.
>
> Tento scénář zřetězených webových rozhraní API může být podporován pomocí udělení přihlašovacích údajů nosiče OAuth 2.0 JWT, jinak známé jako tok On-Behalf-Of. Nicméně tok On-Behalf-Of není aktuálně implementován v Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Registrace webové rozhraní API a publikujte oprávnění

Než požádáte o přístupový token, musíte nejprve zaregistrovat webové rozhraní API a publikujte oprávnění (obory), která můžete do klientské aplikace.

### <a name="register-a-web-api"></a>Registrace webové rozhraní API

1. V nabídce funkcí Azure AD B2C na webu Azure portal, klikněte na **aplikací**.
2. Klikněte na tlačítko **+ přidat** v horní nabídce.
3. Zadejte **Název** aplikace, který popíše aplikaci uživatelům. Můžete například zadat "Contoso API".
4. Přepněte přepínač **Zahrnout webovou aplikaci / webové rozhraní API** na **Ano**.
5. Zadejte libovolnou hodnotu pro **adresy URL odpovědí**. Zadejte například `https://localhost:44316/`. Hodnota není důležitá, protože rozhraní API by neměl přijímají token přímo z Azure AD B2C.
6. Zadejte **Identifikátor URI ID aplikace**. To je identifikátor použitý pro vaše webového rozhraní API. Zadejte například "notes" v poli. **Identifikátor ID URI aplikace** pak budou `https://{tenantName}.onmicrosoft.com/notes`.
7. Pro registraci aplikace klikněte na **Vytvořit**.
8. Klikněte na aplikaci, kterou jste právě vytvořili, a poznamenejte si globálně jedinečné**ID klienta aplikace**, které použijete později ve svém kódu.

### <a name="publishing-permissions"></a>Publikování oprávnění

Obory, které jsou obdobou oprávnění, jsou nezbytné, pokud vaše aplikace volá rozhraní API. Některé příklady obory jsou "čtení" nebo "zápis". Předpokládejme, že chcete svou webovou nebo nativní aplikaci "číst" z rozhraní API. Vaše aplikace by volání Azure AD B2C a žádost o přístupový token, který poskytuje přístup k oboru "čtení". Pro Azure AD B2C ke generování těchto přístupový token, aplikace musí být udělena oprávnění ke "čtení" z konkrétního rozhraní API. K tomuto účelu potřebuje vaše rozhraní API nejprve publikovat obor "číst".

1. V rámci Azure AD B2C **aplikací** nabídce otevřít webového rozhraní API aplikace ("Contoso API").
2. Klikněte na **Publikované obory**. Tady můžete definovat oprávnění (obory), která lze udělit ostatním aplikacím.
3. Přidat **hodnoty oboru** podle potřeby (například "číst"). Ve výchozím nastavení bude definován obor „user_impersonation“. To můžete ignorovat, pokud chcete. Zadejte popis tohoto oboru ve **název oboru** sloupce.
4. Klikněte na **Uložit**.

> [!IMPORTANT]
> **Název oboru** je popis **hodnota rozsahu**. Při použití oboru, je nutné použít **hodnota rozsahu**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Udělit nativní nebo webové aplikaci oprávnění k webovému rozhraní API

Jakmile rozhraní API je nakonfigurována pro publikování obory, klientská aplikace potřebuje mít tyto obory prostřednictvím webu Azure portal.

1. Přejděte **aplikací** nabídky v nabídce funkcí Azure AD B2C.
2. Registrace aplikace klienta ([webovou aplikaci](active-directory-b2c-app-registration.md) nebo [nativního klienta](active-directory-b2c-app-registration.md)) Pokud již nemáte. Pokud jsou podle těchto pokynů jako výchozí bod, budete muset zaregistrovat klientské aplikace.
3. Klikněte na **přístup přes rozhraní API**.
4. Klikněte na **Přidat**.
5. Vyberte vaše webové rozhraní API a obory (oprávnění), které chcete udělit.
6. Klikněte na **OK**.

> [!NOTE]
> Azure AD B2C nevyžaduje váš klient uživatele aplikace pro svůj souhlas. Místo toho všechny souhlasu poskytuje správu, na základě oprávnění nakonfigurovaná mezi aplikacemi je popsáno výše. Udělení oprávnění pro aplikaci odvolá, všichni uživatelé, kteří byli schopní získat oprávnění se už by mohl Uděláte to tak.

## <a name="requesting-a-token"></a>Vyžádání tokenu

Při žádání o přístupový token, klientská aplikace potřebuje k určení požadovaných oprávnění v **oboru** parametr žádosti. Například, chcete-li určit **hodnota rozsahu** "čtení" pro rozhraní API, která má **identifikátor ID URI aplikace** z `https://contoso.onmicrosoft.com/notes`, oboru by `https://contoso.onmicrosoft.com/notes/read`. Tady je příklad autorizační kód požadavku pro `/authorize` koncového bodu.

> [!NOTE]
> Vlastní domény nejsou v současné době podporuje spolu s přístupové tokeny. Je nutné použít tenantName.onmicrosoft.com domény v adrese URL požadavku.

```
https://<tenantName>.b2clogin.com/tfp/<tenantName>.onmicrosoft.com/<yourPolicyId>/oauth2/v2.0/authorize?client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

Chcete-li získat více oprávnění u stejné žádosti, můžete přidat více položek v jedné **oboru** parametr, oddělené mezerami. Příklad:

Dekódovat. adresa URL:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

Kódování URL:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

Může vyžadovat další obory (oprávnění) pro určitý prostředek, než jaké jsou udělena pro klientské aplikace. Pokud tomu tak, že volání bude úspěšné, pokud aspoň jedno oprávnění jsou udělena. Výsledná **přístup\_token** bude mít jeho deklarace "spojovací bod služby" vyplní pouze oprávnění, které byly úspěšně udělen.

> [!NOTE] 
> Nepodporujeme o oprávnění na dvě různé webové prostředky ve stejném požadavku. Tento druh požadavek selže.

### <a name="special-cases"></a>Zvláštní případy

Standard OpenID Connect určuje několik hodnot speciální "rozsah". Následující speciální obory představují oprávnění "přistupovat k profilu uživatele":

* **openid**: To vyžaduje ID token
* **offline\_access**: To vyžaduje obnovovací token (pomocí [toku kódu autorizace](active-directory-b2c-reference-oauth-code.md)).

Pokud `response_type` parametr `/authorize` požadavek zahrnuje `token`, `scope` parametr musí obsahovat nejméně jeden prostředek oboru (jiné než `openid` a `offline_access`), která budou udělena. V opačném případě `/authorize` požadavek bude ukončena s chybou.

## <a name="the-returned-token"></a>Vrácený token

V úspěšně minted **přístup\_token** (buď z `/authorize` nebo `/token` koncového bodu), bude k dispozici následující deklarace identity:

| Name | Deklarovat | Popis |
| --- | --- | --- |
|Cílová skupina |`aud` |**ID aplikace** jednoho prostředku, který token uděluje přístup pro. |
|Rozsah |`scp` |Oprávnění udělená k prostředku. Více udělená oprávnění oddělené mezerou. |
|Autorizované stran |`azp` |**ID aplikace** klientské aplikace, který inicioval žádost. |

Pokud vaše rozhraní API přijímá **přístup\_token**, musí být [token ověří](active-directory-b2c-reference-tokens.md) prokázat, že token, který je platná a má správný deklarací identity.

Máme vždy otevřít na názory a návrhy! Pokud máte případné potíže s tímto tématem, zveřejněte ji prosím na webu Stack Overflow pomocí značky [azure-ad-b2c](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Pro žádosti o funkce, přidejte je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Další postup

* Sestavení webového rozhraní API pomocí [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Sestavení webového rozhraní API pomocí [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)

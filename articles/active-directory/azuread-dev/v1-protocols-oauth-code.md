---
title: Princip toku autorizačního kódu OAuth 2,0 ve službě Azure AD
description: Tento článek popisuje, jak pomocí zpráv HTTP autorizovat přístup k webovým aplikacím a webovým rozhraním API ve vašem tenantovi pomocí Azure Active Directory a OAuth 2,0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5f987ab15201e4c4dabf147ac468184881e9ed17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85551645"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autorizace přístupu k webovým aplikacím Azure Active Directory s využitím toku poskytování kódů OAuth 2.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Pokud neoznámíte serveru, který prostředek chcete volat, neaktivuje Server zásady podmíněného přístupu pro daný prostředek. Takže pokud chcete mít Trigger MFA, budete muset do své adresy URL zahrnout prostředek. 
>

Azure Active Directory (Azure AD) používá standard OAuth 2.0 za účelem umožnění autorizace přístupu k webovým aplikacím a webovým rozhraním API v tenantovi Azure AD. Tato příručka je nezávislá na jazyce a popisuje, jak odesílat a přijímat zprávy HTTP bez použití žádné z našich [Open Source knihoven](active-directory-authentication-libraries.md).

Tok autorizačního kódu OAuth 2,0 je popsaný v [části 4,1 specifikace oauth 2,0](https://tools.ietf.org/html/rfc6749#section-4.1). Používá se k ověřování a autorizaci ve většině typů aplikací, včetně webových aplikací a nativně nainstalovaných aplikací.

## <a name="register-your-application-with-your-ad-tenant"></a>Registrace aplikace pomocí tenanta AD
Nejdřív svou aplikaci zaregistrujete u svého tenanta Azure Active Directory (Azure AD). Pro svou aplikaci tak získáte ID a umožníte jí přijímat tokeny.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
   
1. Zvolte svého tenanta Azure AD tak, že vyberete svůj účet v pravém horním rohu stránky a potom vyberete možnost navigace v **adresáři přepínače** a pak vyberete příslušného tenanta. 
   - Tento krok přeskočte, pokud máte ve svém účtu jenom jednoho tenanta Azure AD, nebo pokud jste už vybrali příslušného tenanta Azure AD.
   
1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.
   
1. V nabídce **Azure Active Directory** vlevo vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
   
1. Postupujte podle zobrazených pokynů a vytvořte novou aplikaci. Nezáleží na tom, jestli se jedná o aplikaci webové aplikace nebo veřejného klienta (mobilní & Desktop) pro tento kurz, ale pokud byste chtěli mít konkrétní příklady pro webové aplikace nebo veřejné klientské aplikace, podívejte se na naše [rychlé starty](v1-overview.md).
   
   - **Název** je název aplikace, který aplikaci popisuje koncovým uživatelům.
   - V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
   - Zadejte **identifikátor URI pro přesměrování**. U webových aplikací se jedná o základní adresu URL vaší aplikace, kde se uživatelé můžou přihlásit.  Například, `http://localhost:12345`. U veřejného klienta (Mobile & Desktop) Azure AD používá k vracení odpovědí na tokeny. Zadejte hodnotu specifickou pro vaši aplikaci.  Například, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Po dokončení registrace vám Azure AD přiřadí aplikaci jedinečný identifikátor klienta ( **ID aplikace**). Tuto hodnotu budete potřebovat v dalších částech, proto je zkopírujte ze stránky aplikace.
   
1. Chcete-li najít aplikaci v Azure Portal, vyberte možnost **Registrace aplikací** a pak vyberte možnost **Zobrazit všechny aplikace**.

## <a name="oauth-20-authorization-flow"></a>Autorizační tok OAuth 2,0

V nejvyšší úrovni má celý tok autorizace pro aplikaci podobný bit jako tento:

![Tok kódu ověřování OAuth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Vyžádání autorizačního kódu

Tok autorizačního kódu začíná klientem, který uživatele přesměruje na `/authorize` koncový bod. V této žádosti klient indikuje oprávnění, která potřebuje získat od uživatele. Koncový bod autorizace OAuth 2,0 pro vašeho tenanta můžete získat tak, že v Azure Portal vyberete **Registrace aplikací > koncových bodů** .

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parametr | Typ | Popis |
| --- | --- | --- |
| tenant |vyžadováno |`{tenant}`Hodnotu v cestě k požadavku lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou identifikátory klientů, například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` pro tokeny nezávislé na tenantovi. |
| client_id |vyžadováno |ID aplikace přiřazené vaší aplikaci, když ji zaregistrujete ve službě Azure AD. Najdete ho na webu Azure Portal. Klikněte na tlačítko **Azure Active Directory** na bočním panelu služby klikněte na položku **Registrace aplikací** a vyberte aplikaci. |
| response_type |vyžadováno |Musí zahrnovat `code` tok autorizačního kódu. |
| redirect_uri |doporučil |Redirect_uri vaší aplikace, ve které vaše aplikace může odesílat a přijímat odpovědi na ověřování. Musí přesně odpovídat jednomu z redirect_uris, který jste zaregistrovali na portálu, s výjimkou musí být zakódovaný URL. Pro nativní & mobilní aplikace byste měli použít výchozí hodnotu `https://login.microsoftonline.com/common/oauth2/nativeclient` . |
| response_mode |optional |Určuje metodu, která se má použít k odeslání výsledného tokenu zpátky do vaší aplikace. Může být `query` , `fragment` , nebo `form_post` . `query` poskytuje kód jako parametr řetězce dotazu v identifikátoru URI přesměrování. Pokud požadujete token ID pomocí implicitního toku, nemůžete použít, `query` jak je uvedeno ve [specifikaci OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Pokud požadujete pouze kód, můžete použít `query` , `fragment` nebo `form_post` . `form_post` provede příspěvek obsahující kód pro identifikátor URI přesměrování. Výchozím nastavením je `query` tok kódu.  |
| state |doporučil |Hodnota obsažená v požadavku, která je také vrácena v odpovědi tokenu. Náhodně vygenerovaná jedinečná hodnota se obvykle používá k [prevenci útoků proti padělání požadavků mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se používá také ke kódování informací o stavu uživatele v aplikaci před tím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| prostředek | doporučil |Identifikátor URI ID aplikace cílového webového rozhraní API (zabezpečeného prostředku) Identifikátor URI ID aplikace najdete tak, že na webu Azure Portal kliknete **Azure Active Directory**, kliknete na **Registrace aplikací**, otevřete stránku **Nastavení** aplikace a pak kliknete na **vlastnosti**. Může to být také externí prostředek, jako `https://graph.microsoft.com` . Tato možnost je vyžadována v jednom z požadavků na autorizaci nebo token. Aby se zajistilo méně výzev k ověřování, umístěte ho do autorizační žádosti, aby se zajistilo, že uživatel obdrží souhlas. |
| scope | **přeskočen** | V případě aplikací v1 Azure AD musí být obory staticky nakonfigurované na webu Azure Portal v **Nastavení** aplikace, **požadovaná oprávnění**. |
| výzv |optional |Určete typ interakce uživatele, která je povinná.<p> Platné hodnoty jsou: <p> *přihlášení*: uživatel by měl být vyzván k opětovnému ověření. <p> *select_account*: uživateli se zobrazí výzva k výběru účtu a přerušení jednotného přihlašování. Uživatel může vybrat existující přihlášený účet, zadat jejich přihlašovací údaje k zadanému účtu nebo zvolit možnost použít jiný účet zcela. <p> *souhlas*: bylo uděleno souhlasu uživatele, ale je nutné ho aktualizovat. Uživatel by měl být vyzván k vyjádření souhlasu. <p> *admin_consent*: Správce by měl být vyzváni k vyjádření souhlasu jménem všech uživatelů v jejich organizaci. |
| login_hint |optional |Dá se použít k předvyplnění pole uživatelské jméno a e-mailová adresa přihlašovací stránky pro uživatele, pokud znáte své uživatelské jméno předem. Aplikace často používají tento parametr během opakovaného ověřování, který už z předchozího přihlášení extrahuje uživatelské jméno, a to pomocí `preferred_username` deklarace identity. |
| domain_hint |optional |Poskytuje nápovědu týkající se tenanta nebo domény, které by měl uživatel použít k přihlášení. Hodnota domain_hint je registrovanou doménou pro tenanta. Pokud je tenant federované do místního adresáře, AAD přesměruje na zadaný federační server tenanta. |
| code_challenge_method | doporučil    | Metoda použitá k zakódování `code_verifier` pro `code_challenge` parametr. Může to být jedna z `plain` nebo `S256` . Pokud je vyloučený, předpokládá se, že je v `code_challenge` případě zahrnutí prostý text `code_challenge` . Azure AAD v 1.0 podporuje i `plain` `S256` . Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | doporučil    | Slouží k zabezpečení autorizačního kódu prostřednictvím kontrolního klíče pro výměnu kódu (PKCE) z nativního nebo veřejného klienta. Požadováno `code_challenge_method` , pokud je zahrnuto. Další informace najdete v [dokumentu RFC PKCE](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Pokud je uživatel součástí organizace, může správce organizace udělit souhlas nebo odmítnutí jménem uživatele nebo povolit souhlas uživatele. Uživateli je dána možnost udělit souhlas pouze v případě, že ho povolují správce.
>
>

V tomto okamžiku se uživateli zobrazí výzva k zadání přihlašovacích údajů a souhlasu s oprávněními požadovanými aplikací na webu Azure Portal. Jakmile se uživatel ověří a udělí souhlas, služba Azure AD pošle odpověď vaší aplikaci na `redirect_uri` adrese vaší žádosti s kódem.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď může vypadat takto:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametr | Popis |
| --- | --- |
| admin_consent |Hodnota je true, pokud správce souhlasí s výzvou k žádosti o souhlas. |
| kód |Autorizační kód, který požadovaná aplikace požaduje. Aplikace může použít autorizační kód k vyžádání přístupového tokenu pro cílový prostředek. |
| session_state |Jedinečná hodnota, která identifikuje aktuální relaci uživatele. Tato hodnota je identifikátor GUID, ale měla by být zpracována jako neprůhledná hodnota, která je předána bez vyhodnocení. |
| state |Pokud je parametr State zahrnut v žádosti, v odpovědi by se měla objevit stejná hodnota. Je dobrým zvykem, že aplikace ověřuje, že hodnoty stavu v žádosti a odpovědi jsou stejné před použitím odpovědi. To pomáhá detekovat [útoky proti falšování (CSRF) mezi lokalitami](https://tools.ietf.org/html/rfc6749#section-10.12) proti klientovi. |

### <a name="error-response"></a>Chybová odezva
Odpovědi na chyby mohou být také odeslány do `redirect_uri` , aby je aplikace mohla vhodně zpracovat.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Hodnota kódu chyby definovaná v sekci 5,2 [autorizačního rozhraní OAuth 2,0](https://tools.ietf.org/html/rfc6749). Následující tabulka popisuje kódy chyb, které Azure AD vrátí. |
| error_description |Podrobnější popis chyby. Tato zpráva není zamýšlená jako uživatelsky přívětivý koncový uživatel. |
| state |Hodnota stavu je náhodně generovaná neznovu použitá hodnota, která se pošle v žádosti a vrátí se v reakci na ochranu proti útokům přes CSRF (mezi lokalitami). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Chybové kódy pro chyby koncového bodu autorizace
Následující tabulka popisuje různé chybové kódy, které mohou být vráceny v `error` parametru chybové odpovědi.

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící požadovaný parametr. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| unauthorized_client |Klientská aplikace nemá oprávnění vyžadovat autorizační kód. |K tomu obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| access_denied |Vlastník prostředku zamítl souhlas. |Klientská aplikace může uživatele informovat, že nemůže pokračovat, dokud se uživatel nesouhlasí. |
| unsupported_response_type |Autorizační Server v žádosti nepodporuje typ odpovědi. |Opravte a odešlete požadavek znovu. Jedná se o chybu vývoje, která se obvykle zachycuje při počátečním testování. |
| server_error |Na serveru došlo k neočekávané chybě. |Opakujte požadavek. Tyto chyby mohou být způsobeny dočasnými podmínkami. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné chyby. |
| temporarily_unavailable |Server je dočasně zaneprázdněný pro zpracování žádosti. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| invalid_resource |Cílový prostředek není platný, protože neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurovaný. |To znamená, že pokud tento prostředek existuje, není v tenantovi nakonfigurovaný. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Vyžádání přístupového tokenu pomocí autorizačního kódu
Teď, když jste získali autorizační kód a udělili mu oprávnění pro přístup k požadovanému prostředku, můžete tento kód uplatnit odesláním požadavku POST do `/token` koncového bodu:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parametr | Typ | Popis |
| --- | --- | --- |
| tenant |vyžadováno |`{tenant}`Hodnotu v cestě k požadavku lze použít k řízení, kdo se může přihlásit k aplikaci. Povolené hodnoty jsou identifikátory klientů, například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` pro tokeny nezávislé na tenantovi. |
| client_id |vyžadováno |ID aplikace přiřazené vaší aplikaci, když ji zaregistrujete ve službě Azure AD. Najdete ho v Azure Portal. ID aplikace se zobrazí v nastavení registrace aplikace. |
| grant_type |vyžadováno |Musí být `authorization_code` pro tok autorizačního kódu. |
| kód |vyžadováno |Rozhraní `authorization_code` , které jste získali v předchozí části |
| redirect_uri |vyžadováno | `redirect_uri`Registrováno v klientské aplikaci. |
| client_secret |vyžadováno pro webové aplikace, není povoleno pro veřejné klienty. |Tajný klíč aplikace, který jste vytvořili na webu Azure Portal pro vaši aplikaci v části **klíče** Nedá se použít v nativní aplikaci (veřejném klientovi), protože client_secrets nemůže být spolehlivě uložená na zařízeních. Vyžaduje se pro webové aplikace a webová rozhraní API (všechny důvěrné klienty), které mají možnost `client_secret` bezpečně ukládat na straně serveru. Client_secret by měl být před odesláním zakódovaný na adrese URL. |
| prostředek | doporučil |Identifikátor URI ID aplikace cílového webového rozhraní API (zabezpečeného prostředku) Identifikátor URI ID aplikace najdete tak, že na webu Azure Portal kliknete **Azure Active Directory**, kliknete na **Registrace aplikací**, otevřete stránku **Nastavení** aplikace a pak kliknete na **vlastnosti**. Může to být také externí prostředek, jako `https://graph.microsoft.com` . Tato možnost je vyžadována v jednom z požadavků na autorizaci nebo token. Aby se zajistilo méně výzev k ověřování, umístěte ho do autorizační žádosti, aby se zajistilo, že uživatel obdrží souhlas. V žádosti o autorizaci i v žádosti o token se musí parametry prostředku shodovat. | 
| code_verifier | optional | Stejný code_verifier, který byl použit k získání authorization_code. Vyžaduje se, pokud se v žádosti o udělení autorizačního kódu použil PKCE. Další informace najdete v [dokumentu RFC pro PKCE](https://tools.ietf.org/html/rfc7636) .   |

Identifikátor URI ID aplikace najdete tak, že na webu Azure Portal kliknete **Azure Active Directory**, kliknete na **Registrace aplikací**, otevřete stránku **Nastavení** aplikace a pak kliknete na **vlastnosti**.

### <a name="successful-response"></a>Úspěšná odpověď
Služba Azure AD vrací [přístupový token](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) po úspěšné odpovědi. Aby se minimalizovala síťová volání z klientské aplikace a jejich přidružená latence, měla by klientská aplikace získat přístupové tokeny do mezipaměti pro dobu životnosti tokenu, která je určená v odpovědi OAuth 2,0. Chcete-li zjistit životnost tokenu, použijte `expires_in` buď `expires_on` hodnoty parametru nebo.

Pokud prostředek webového rozhraní API vrátí `invalid_token` kód chyby, může to znamenat, že prostředek zjistil, že platnost tokenu vypršela. Pokud se časy klienta a prostředku liší (označované jako "časový rozvrh"), může prostředek zvážit vypršení platnosti tokenu před vymazáním tokenu z mezipaměti klienta. Pokud k tomu dojde, vymažte token z mezipaměti, a to i v případě, že je stále v rámci vypočtené doby života.

Úspěšná odpověď může vypadat takto:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parametr | Popis |
| --- | --- |
| access_token |Požadovaný přístupový token  Toto je neprůhledný řetězec – závisí na tom, co prostředek očekává, a není určen pro zobrazení klienta. Aplikace může tento token použít k ověření zabezpečeného prostředku, jako je například webové rozhraní API. |
| token_type |Určuje hodnotu typu tokenu. Jediným typem, který podporuje Azure AD, je nosič. Další informace o nosných tokenech najdete v tématu [autorizační rozhraní OAuth 2.0: použití nosných tokenů (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |
| expires_on |Čas vypršení platnosti přístupového tokenu. Datum se reprezentuje jako počet sekund od roku 1970-01-01T0:0: 0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení doby života tokenů uložených v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace webového rozhraní API (zabezpečeného prostředku) |
| scope |Oprávnění k zosobnění udělená klientské aplikaci. Výchozí oprávnění je `user_impersonation` . Vlastník zabezpečeného prostředku může registrovat další hodnoty ve službě Azure AD. |
| refresh_token |Obnovovací token OAuth 2,0. Aplikace může tento token použít k získání dalších přístupových tokenů po vypršení platnosti aktuálního přístupového tokenu. Aktualizační tokeny jsou dlouhodobé a dají se použít k uchování přístupu k prostředkům po delší dobu. |
| id_token |Nepodepsaný JSON Web Token (JWT) představující [token ID](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Aplikace může base64Url dekódovat segmentů tohoto tokenu, aby vyžádala informace o uživateli, který se přihlásil. Aplikace může hodnoty ukládat do mezipaměti a zobrazovat je, ale nemělo by je spoléhat na jakékoli autorizace nebo hranice zabezpečení. |

Další informace o webových tokenech JSON najdete v článku [specifikace pro verzi JWT IETF](https://go.microsoft.com/fwlink/?LinkId=392344).   Další informace o najdete v `id_tokens` tématu [tok OpenID Connect v 1.0](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Chybová odezva
Chyby koncového bodu vystavení tokenu jsou kódy chyb HTTP, protože klient volá koncový bod vystavení tokenu přímo. Kromě stavového kódu HTTP vrátí koncový bod vystavení tokenu Azure AD také dokument JSON s objekty, které popisují chybu.

Ukázková chybová odpověď by mohla vypadat takto:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |
| error_codes |Seznam chybových kódů specifických pro službu STS, které mohou být užitečné při diagnostice. |
| časové razítko |Čas, kdy došlo k chybě. |
| trace_id |Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice. |
| correlation_id |Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice napříč komponentami. |

#### <a name="http-status-codes"></a>Stavové kódy HTTP
V následující tabulce jsou uvedeny stavové kódy HTTP, které vrátí koncový bod vystavení tokenu. V některých případech je kód chyby dostačující pro popis odpovědi, ale v případě, že dojde k chybám, je nutné analyzovat přiložený dokument JSON a prohlédnout si jeho kód chyby.

| Kód HTTP | Popis |
| --- | --- |
| 400 |Výchozí kód HTTP. Používá se ve většině případů a je typicky způsoben chybnou žádostí. Opravte a odešlete požadavek znovu. |
| 401 |Ověření se nezdařilo. V žádosti například chybí parametr client_secret. |
| 403 |Autorizace se nezdařila. Uživatel například nemá oprávnění pro přístup k prostředku. |
| 500 |Ve službě došlo k vnitřní chybě. Opakujte požadavek. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Chybové kódy pro chyby koncového bodu tokenu
| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící požadovaný parametr. |Opravte a znovu odešlete žádost. |
| invalid_grant |Autorizační kód je neplatný nebo vypršela jeho platnost. |Vyzkoušejte si nový požadavek na `/authorize` koncový bod. |
| unauthorized_client |Ověřený klient nemá oprávnění použít tento typ autorizačního udělení. |K tomu obvykle dochází, když klientská aplikace není registrovaná v Azure AD nebo není přidaná do tenanta Azure AD uživatele. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| invalid_client |Ověření klienta se nezdařilo. |Pověření klienta nejsou platná. Chcete-li opravit, správce aplikace aktualizuje pověření. |
| unsupported_grant_type |Autorizační Server nepodporuje typ udělení autorizace. |Změňte typ udělení v žádosti. Tento typ chyby by měl nastat pouze během vývoje a zjištěn při počátečním testování. |
| invalid_resource |Cílový prostředek není platný, protože neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurovaný. |To znamená, že pokud tento prostředek existuje, není v tenantovi nakonfigurovaný. Aplikace může uživatele vyzvat k instalaci aplikace a jejímu přidání do Azure AD. |
| interaction_required |Požadavek vyžaduje zásah uživatele. Například je vyžadován další krok ověřování. | Místo neinteraktivního požadavku opakujte akci s interaktivní žádostí o autorizaci pro stejný prostředek. |
| temporarily_unavailable |Server je dočasně zaneprázdněný pro zpracování žádosti. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |

## <a name="use-the-access-token-to-access-the-resource"></a>Přístup k prostředku pomocí přístupového tokenu
Teď, když jste úspěšně získali `access_token` , můžete použít token v žádosti do webových rozhraní API, a to tak, že ho zahrnete do `Authorization` hlavičky. Specifikace [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) vysvětluje, jak použít tokeny nosiče v požadavcích http pro přístup k chráněným prostředkům.

### <a name="sample-request"></a>Ukázková žádost
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Chybová odezva
Zabezpečené prostředky, které implementují specifikace RFC 6750, vystavují stavové kódy HTTP. Pokud žádost nezahrnuje přihlašovací údaje pro ověření nebo token chybí, odpověď obsahuje `WWW-Authenticate` hlavičku. Pokud požadavek neproběhne úspěšně, server prostředků odpoví kódem stavu HTTP a kódem chyby.

Následuje příklad neúspěšné odpovědi, pokud požadavek klienta nezahrnuje nosný token:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parametry chyby
| Parametr | Popis |
| --- | --- |
| authorization_uri |Identifikátor URI (fyzický koncový bod) autorizačního serveru. Tato hodnota se používá také jako vyhledávací klíč k získání dalších informací o serveru z koncového bodu zjišťování. <p><p> Klient musí ověřit, zda je autorizační Server důvěryhodný. Když je prostředek chráněný službou Azure AD, stačí ověřit, jestli adresa URL začíná `https://login.microsoftonline.com` nebo má jiný název hostitele, který Azure AD podporuje. Prostředek specifický pro klienta by měl vždycky vracet identifikátor URI autorizace specifický pro klienta. |
| error |Hodnota kódu chyby definovaná v sekci 5,2 [autorizačního rozhraní OAuth 2,0](https://tools.ietf.org/html/rfc6749). |
| error_description |Podrobnější popis chyby. Tato zpráva není zamýšlená jako uživatelsky přívětivý koncový uživatel. |
| resource_id |Vrátí jedinečný identifikátor prostředku. Klientská aplikace může tento identifikátor použít jako hodnotu `resource` parametru při žádosti o token pro prostředek. <p><p> Je důležité, aby klientská aplikace ověřila tuto hodnotu, jinak by škodlivá služba mohla být schopna přimět útok **zvýšení oprávnění** . <p><p> Doporučenou strategií pro předcházení útokům je ověření, že `resource_id` odpovídá základu adresy URL webového rozhraní API, ke které přistupujete. Například, pokud `https://service.contoso.com/data` je k dispozici, `resource_id` může být `https://service.contoso.com/` . Klientská aplikace musí odmítnout odpověď začínající `resource_id` základní adresou URL, pokud neexistuje spolehlivý alternativní způsob, jak ID ověřit. |

#### <a name="bearer-scheme-error-codes"></a>Kódy chyb schémat nosiče
Specifikace RFC 6750 definuje následující chyby pro prostředky, které používají hlavičku WWW-Authenticate a schéma nosiče v odpovědi.

| Stavový kód HTTP | Kód chyby | Popis | Akce klienta |
| --- | --- | --- | --- |
| 400 |invalid_request |Požadavek nemá správný formát. Například může chybět parametr nebo použít stejný parametr dvakrát. |Opravte chybu a opakujte požadavek. Tento typ chyby by měl nastat pouze během vývoje a byl zjištěn při počátečním testování. |
| 401 |invalid_token |Přístupový token chybí, není platný nebo je odvolán. Hodnota parametru error_description poskytuje další podrobnosti. |Vyžádejte si nový token od autorizačního serveru. Pokud nový token neuspěje, došlo k neočekávané chybě. Odešle uživateli chybovou zprávu a pak to zkuste znovu po náhodném zpoždění. |
| 403 |insufficient_scope |Přístupový token neobsahuje oprávnění k zosobnění vyžadované pro přístup k prostředku. |Odešlete do koncového bodu autorizace novou žádost o autorizaci. Pokud odpověď obsahuje parametr oboru, použijte hodnotu oboru v požadavku na prostředek. |
| 403 |insufficient_access |Předmět tokenu nemá oprávnění, která jsou vyžadována pro přístup k prostředku. |Vyzvat uživatele k použití jiného účtu nebo k vyžádání oprávnění k zadanému prostředku. |

## <a name="refreshing-the-access-tokens"></a>Aktualizace přístupových tokenů

Přístupové tokeny jsou krátkodobé a po uplynutí jejich platnosti musí být obnoveny, aby bylo možné pokračovat v přístupu k prostředkům. Můžete aktualizovat `access_token` tím, že odešlete další `POST` požadavek na `/token` koncový bod, ale tentokrát `refresh_token` místo toho poskytne `code` .  Aktualizační tokeny jsou platné pro všechny prostředky, na které váš klient již udělil souhlas s přístupem `resource=https://graph.microsoft.com` . pro vyžádání nového přístupového tokenu pro lze použít obnovovací token vydaný na žádost pro `resource=https://contoso.com/api` . 

Aktualizační tokeny nemají zadané životnosti. Obvykle jsou životnosti aktualizačních tokenů poměrně dlouhé. V některých případech ale platnost tokenů aktualizace vyprší, odvolají se nebo nemají dostatečná oprávnění pro požadovanou akci. Vaše aplikace musí očekávat a zpracovat chyby vrácené koncovým bodem vystavení tokenu správně.

Když obdržíte odpověď s chybou aktualizačního tokenu, zahodíte aktuální obnovovací token a vyžádáte si nový autorizační kód nebo přístupový token. Zejména při použití obnovovacího tokenu v toku udělení autorizačního kódu, pokud obdržíte odpověď s `interaction_required` `invalid_grant` kódy chyb, zahodíte obnovovací token a vyžádáte nový autorizační kód.

Vzorový požadavek na koncový bod pro **konkrétního klienta** (můžete také použít **společný** koncový bod) k získání nového přístupového tokenu pomocí obnovovacího tokenu, který vypadá takto:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď tokenu bude vypadat takto:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parametr | Popis |
| --- | --- |
| token_type |Typ tokenu. Jediná podporovaná hodnota je **nosič**. |
| expires_in |Zbývající doba životnosti tokenu v sekundách. Typická hodnota je 3600 (jedna hodina). |
| expires_on |Datum a čas, kdy vyprší platnost tokenu. Datum se reprezentuje jako počet sekund od roku 1970-01-01T0:0: 0Z UTC až do doby vypršení platnosti. |
| prostředek |Identifikuje zabezpečený prostředek, ke kterému se přístupový token dá použít pro přístup. |
| scope |Oprávnění k zosobnění udělená nativní klientské aplikaci. Výchozí oprávnění je **user_impersonation**. Vlastník cílového prostředku může registrovat alternativní hodnoty ve službě Azure AD. |
| access_token |Nový přístupový token, který byl vyžádán. |
| refresh_token |Nový refresh_token OAuth 2,0, který se dá použít k vyžádání nových přístupových tokenů, když vyprší platnost této odpovědi. |

### <a name="error-response"></a>Chybová odezva
Ukázková chybová odpověď by mohla vypadat takto:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parametr | Popis |
| --- | --- |
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která může vývojářům pomáhat najít hlavní příčinu chyby ověřování. |
| error_codes |Seznam chybových kódů specifických pro službu STS, které mohou být užitečné při diagnostice. |
| časové razítko |Čas, kdy došlo k chybě. |
| trace_id |Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice. |
| correlation_id |Jedinečný identifikátor pro požadavek, který může pomáhat při diagnostice napříč komponentami. |

Popis chybových kódů a doporučené akce klienta najdete v tématu [kódy chyb pro chyby koncového bodu tokenu](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Další kroky
Další informace o koncovém bodu Azure AD v 1.0 a o tom, jak přidat ověřování a autorizaci k webovým aplikacím a webovým rozhraním API, najdete v tématu [ukázkové aplikace](sample-v1-code.md).

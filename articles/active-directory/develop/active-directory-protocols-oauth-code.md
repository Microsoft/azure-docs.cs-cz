---
title: Pochopení toku kódu autorizace OAuth 2.0 ve službě Azure AD
description: Tento článek popisuje, jak používat zprávy protokolu HTTP k autorizaci přístupu k webové aplikace a webové rozhraní API ve vašem klientovi pomocí Azure Active Directory a OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 93de62a21ca1d3b8c88715fc9207a583920ac33e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autorizace přístupu k Azure Active Directory webových aplikací pomocí toku OAuth 2.0 kód grant
Azure Active Directory (Azure AD) používá OAuth 2.0 umožnit autorizace přístupu k webové aplikace a webové rozhraní API v klientovi služby Azure AD. Tento průvodce je závislý na jazyce a popisuje, jak odesílat a přijímat zprávy HTTP bez použití některé z našich [knihovny open-source](active-directory-authentication-libraries.md).

Toku kódu autorizace OAuth 2.0 je popsaná v [části 4.1 specifikace OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). To se používá k provedení ověřování a autorizace ve většině typy aplikací, včetně webových aplikací a nativně nainstalované aplikace.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Tok ověřování OAuth 2.0
Na vysoké úrovni tok celý autorizace pro aplikaci trochu vypadat třeba takto:

![Ověřování kódu toku OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Žádost o autorizační kód
Tok autorizačního kódu začíná klienta odkazovat uživatele `/authorize` koncový bod. V této žádosti o klient naznačuje oprávnění, které potřebuje získat od uživatele. Koncový bod autorizace OAuth 2.0 pro vašeho klienta můžete získat tak, že vyberete **registrace aplikace > Koncové body** na portálu Azure.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A%12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parametr |  | Popis |
| --- | --- | --- |
| tenant |povinné |`{tenant}` Hodnotu v cestě požadavku slouží k řízení, kdo může přihlásit k aplikaci. Povolené hodnoty jsou identifikátory klienta, například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` pro tokeny nezávislé na klienta |
| client_id |povinné |ID aplikace přiřazené vaší aplikaci při registraci s Azure AD. Tento nástroj naleznete na webu Azure Portal. Klikněte na tlačítko **Azure Active Directory** na bočním panelu služby, klikněte na tlačítko **registrace aplikace**a zvolte aplikaci. |
| response_type |povinné |Musí zahrnovat `code` pro tok autorizačního kódu. |
| redirect_uri |Doporučená |O položku redirect_uri vaší aplikace, kde můžete odesílat a přijímat aplikace odpovědi ověřování. Se musí přesně shodovat s jedním redirect_uris, které jste zaregistrovali na portálu, s výjimkou musí být kódovaná adresou url. Pro nativní & mobilní aplikace, by měl použít výchozí hodnotu `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |Doporučená |Určuje metodu, která se má použít k odeslání výsledný token zpět do vaší aplikace. Může být `query` nebo `form_post`. `query` poskytuje kód jako parametr řetězce dotazu na vaše URI přesměrování při `form_post` provede metodu POST SMĚŘUJÍCÍ obsahující kód váš identifikátor URI přesměrování. |
| state |Doporučená |Hodnota součástí požadavek, který je také vrácený v odpovědi tokenu. Náhodně generované jedinečné hodnoty se obvykle používá u [prevence útoků padělání požadavku posílaného mezi weby](http://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci, než k žádosti o ověření, například stránky nebo zobrazení, které byly na. |
| prostředek | Doporučená |Identifikátor URI aplikace ID cílové webové rozhraní API (zabezpečené prostředků). Chcete-li najít identifikátor ID URI aplikace na portálu Azure, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**, otevřít aplikaci **nastavení** stránky a pak klikněte na  **Vlastnosti**. Může být také jako externí prostředek `https://graph.microsoft.com`. To je nutné v jednom autorizace nebo žádostí o token. K zajištění méně ověřování výzvy je uložit žádost o ověření a zda je souhlasu přijat od uživatele. |
| scope | **Ignorovat** | Pro aplikace Azure AD v1, musí být staticky konfigurované obory v portálu Azure v rámci aplikace **nastavení**, **požadovaných oprávnění**. |
| řádku |nepovinné |Označuje typ interakci s uživatelem, který je vyžadován.<p> Platné hodnoty jsou: <p> *přihlášení*: uživatel měla zobrazit výzva k novému ověření. <p> *souhlas*: souhlas uživatele byla udělena, ale je třeba aktualizovat. Uživatel měla zobrazit výzva k souhlas. <p> *admin_consent*: Správce měla zobrazit výzva k souhlas jménem všichni uživatelé v organizaci |
| login_hint |nepovinné |Slouží k předem vyplnit pole uživatelské jméno nebo e-mailové adresy na stránce přihlášení pro uživatele, pokud znáte svoje uživatelské jméno předem. Tento parametr použijte často aplikace během opětovné ověření, uživatelské jméno s již extrahovat z předchozí přihlášení pomocí `preferred_username` deklarací identity. |
| domain_hint |nepovinné |Poskytuje informace o klientovi nebo doménu, která uživatel by měl použít pro přihlášení. Hodnota domain_hint je registrované domény pro klienta. Pokud klient federovaný na místní adresář, AAD přesměruje na federační server zadaného klienta. |
| code_challenge_method | nepovinné    | Metoda použitá ke kódování `code_verifier` pro `code_challenge` parametr. Může být jedna z `plain` nebo `S256`. Pokud vyloučená, `code_challenge` bude ve formátu prostého textu, pokud hodnota `code_challenge` je součástí. Azure verze 1.0 AAD podporuje obě `plain` a `S256`. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | nepovinné    | Používá k zabezpečení uděluje autorizační kód prostřednictvím ověření klíče pro Exchange kódu (PKCE) od klienta nativní nebo veřejné. Požadováno pokud `code_challenge_method` je součástí. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Pokud uživatel je součástí organizace, správce organizace souhlas nebo odmítnout jménem uživatele nebo povolit uživatele o souhlas. Uživatel je zadána možnost souhlas jenom v případě, že správce povolí ho.
>
>

V tomto okamžiku je uživatel vyzván k zadání přihlašovacích údajů a souhlas oprávnění požadovaná aplikace na portálu Azure. Jakmile se uživatel ověří a uděluje souhlas, Azure AD odešle odpověď do vaší aplikace na `redirect_uri` adres ve vaší žádosti s kódem.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď může vypadat například takto:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametr | Popis |
| --- | --- |
| admin_consent |Hodnota je True, pokud správce souhlas výzvu žádost o souhlas. |
| Kód |Autorizační kód, který požadované aplikace. Aplikace můžete autorizační kód vyžádat token přístupu pro cílový prostředek. |
| session_state |Jedinečná hodnota, která identifikuje aktuální uživatelskou relaci. Tato hodnota je identifikátor GUID, ale má být považována za neprůhledné hodnoty, které jsou předávány bez prozkoumání. |
| state |Pokud parametr stavu je obsažena v žádosti o stejnou hodnotu by se měla objevit v odpovědi. Je vhodné pro aplikaci do ověřte, zda jsou hodnoty stavu v požadavku a odpovědi identické před použitím odpovědi. To pomůže zjistit [webů požadavku padělání (proti útokům CSRF) před útoky](https://tools.ietf.org/html/rfc6749#section-10.12) proti klienta. |

### <a name="error-response"></a>Chybové odpovědi
Chybové odpovědi se taky může odeslat do `redirect_uri` tak, aby aplikace můžete správně zpracovat.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |K chybě kódu hodnota definovaná v části 5.2 z [Framework autorizace OAuth 2.0](http://tools.ietf.org/html/rfc6749). V další tabulce jsou kódy chyb, které vrátí Azure AD. |
| error_description |Podrobnější popis chyby. Tato zpráva neměla být popisný koncového uživatele. |
| state |Hodnota stavu je náhodně generované hodnoty znovu použít, které se odesílají v požadavku a vrátí se v reakci na zabránit útokům (proti útokům CSRF) padělání požadavku posílaného mezi weby. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kódy chyb pro chyb koncový bod autorizace
Následující tabulka popisuje různé kódy chyb, které mohou být vráceny v `error` parametr odpovědi na chybu.

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící povinný parametr. |Opravte a odešlete požadavek znovu. To je chyba vývoj a je obvykle zachycena během počáteční testování. |
| unauthorized_client |Klientská aplikace nemá oprávnění k vyžádání autorizační kód. |K tomu obvykle dojde, pokud klientská aplikace není registrovaný ve službě Azure AD nebo nebyla přidána do klienta Azure AD pro uživatele. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD. |
| ACCESS_DENIED |Vlastník prostředku odepřen souhlasu |Klientská aplikace může upozornit uživatele, který nemůže pokračovat, pokud uživatel souhlasí. |
| unsupported_response_type |Autorizace serveru nepodporuje typ odpovědi v požadavku. |Opravte a odešlete požadavek znovu. To je chyba vývoj a je obvykle zachycena během počáteční testování. |
| server_error |Serveru došlo k neočekávané chybě. |Opakujte žádost. Tyto chyby může být důsledkem dočasné stavy. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď se zpožďuje kvůli dočasné chybě. |
| temporarily_unavailable |Server je dočasně zaneprázdněn pro zpracování požadavku. |Opakujte žádost. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď se zpožďuje kvůli dočasné podmínce. |
| invalid_resource |Cílový prostředek je neplatný, protože neexistuje, Azure AD ji nemůže najít, nebo není správně nakonfigurována. |To znamená, že k prostředku, pokud existuje, není nakonfigurované v klientovi. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Pomocí autorizační kód žádosti o token přístupu
Teď, když jste získali autorizační kód a bylo uděleno oprávnění uživatelem, můžete uplatnit kód pro token přístupu k požadovaného prostředku odesíláním požadavek POST do `/token` koncový bod:

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

| Parametr |  | Popis |
| --- | --- | --- |
| tenant |povinné |`{tenant}` Hodnotu v cestě požadavku slouží k řízení, kdo může přihlásit k aplikaci. Povolené hodnoty jsou identifikátory klienta, například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` pro tokeny nezávislé na klienta |
| client_id |povinné |Id aplikace přiřazené vaší aplikaci při registraci s Azure AD. Tento nástroj naleznete na webu Azure portal. Id aplikace se zobrazí v nastavení registrace aplikace. |
| grant_type |povinné |Musí být `authorization_code` pro tok autorizačního kódu. |
| Kód |povinné |`authorization_code` Kterou jste získali v předchozí části |
| redirect_uri |povinné |Stejné `redirect_uri` hodnotu, která byla použita k získání `authorization_code`. |
| tajný klíč client_secret |vyžaduje se pro webové aplikace, není povoleno pro veřejné klienty |Tajný klíč aplikace, kterou jste vytvořili na portálu Azure pro aplikaci v části **klíče**. Nelze se použít v nativní aplikaci (veřejné klienta), protože client_secrets nelze uložit spolehlivě na zařízení. Je vyžadováno pro webové aplikace a webové rozhraní API (všechny důvěrné klientů), které mají možnost ukládat `client_secret` bezpečně na straně serveru. |
| prostředek | Doporučená |Identifikátor URI aplikace ID cílové webové rozhraní API (zabezpečené prostředků). Chcete-li najít identifikátor ID URI aplikace na portálu Azure, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**, otevřít aplikaci **nastavení** stránky a pak klikněte na  **Vlastnosti**. Může být také jako externí prostředek `https://graph.microsoft.com`. To je nutné v jednom autorizace nebo žádostí o token. K zajištění méně ověřování výzvy je uložit žádost o ověření a zda je souhlasu přijat od uživatele. Pokud se v žádost o ověření a požadavku na token prostředku ' parametry se musí shodovat. | 
| code_verifier | nepovinné | Stejné code_verifier, který se používá k získání authorization_code. Vyžaduje, pokud PKCE byl použit v žádosti o udělení autorizační kód. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Chcete-li najít identifikátor ID URI aplikace na portálu Azure, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikace**, otevřít aplikaci **nastavení** stránky a pak klikněte na  **Vlastnosti**.

### <a name="successful-response"></a>Úspěšná odpověď
Azure AD vrátí přístupový token při úspěšné odpovědi. Chcete-li minimalizovat síťový volání z klientské aplikace a jejich přidružené latence, by měl klientská aplikace mezipaměti přístupové tokeny pro dobu životnosti tokenu, který je uveden v odpovědi OAuth 2.0. Chcete-li určit dobu životnosti tokenu, použijte buď `expires_in` nebo `expires_on` hodnoty parametrů.

Webové rozhraní API prostředků vrátí-li `invalid_token` kódu chyby to může znamenat, že prostředek bylo zjištěno, že vypršela platnost tokenu. Pokud jsou časy hodiny klienta a prostředků různých (známé jako "zkosení čas"), zvažte prostředek token, který má být platnost předtím, než se vymaže tokenu z mezipaměti klienta. Pokud k tomu dojde, zrušte tokenu z mezipaměti, i když je stále v rámci počítané celé jeho životnosti.

Úspěšná odpověď může vypadat například takto:

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
| access_token |Požadovaný přístupový token jako podepsaný JSON Web Token (JWT). Aplikace můžete používat tento token k ověření zabezpečeným prostředkům, jako jsou webové rozhraní API. |
| token_type |Určuje hodnotu typ tokenu. Pouze typ, který podporuje Azure AD je nosiče. Další informace o nosné tokeny najdete v tématu [OAuth2.0 autorizace Framework: použití tokenů nosiče (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Jak dlouho přístupový token je platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je reprezentován jako počet sekund od pod hodnotou 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby platnosti tokenů v mezipaměti. |
| prostředek |Identifikátor URI aplikace ID webové rozhraní API (zabezpečené prostředků). |
| scope |Zosobnění oprávnění udělená aplikaci klienta. Výchozí oprávnění je `user_impersonation`. Vlastníkem prostředku zabezpečené můžete zaregistrovat další hodnoty ve službě Azure AD. |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token získat další přístupové tokeny po vypršení platnosti aktuální přístupový token. Aktualizujte je dlouhodobé tokeny a slouží k přístupu k prostředkům uchovávány dlouhou dobu. |
| id_token |Nepodepsané JSON Web Token (JWT). Base64Url může aplikace dekódovat segmentů tento token pro vyžadování informací o uživateli, který přihlášení. Aplikace můžete hodnoty do mezipaměti a jejich zobrazení, ale na ně neměli spoléhat pro žádné hranice zabezpečení nebo autorizace. |

### <a name="jwt-token-claims"></a>Deklarace tokenů JWT
Token JWT v hodnotě `id_token` parametr lze dekódovat do následující deklarací identity:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Další informace o webových tokenů JSON najdete v tématu [specifikaci JWT IETF koncept](http://go.microsoft.com/fwlink/?LinkId=392344). Další informace o typech tokenů a deklarací identity, najdete v tématu [podporované tokenu a typy deklarací identity](active-directory-token-and-claims.md)

`id_token` Parametr obsahuje následující typy deklarací identity:

| Typ deklarace identity | Popis |
| --- | --- |
| oblast |Cílová skupina tokenu. Pokud je token vydán pro klientskou aplikaci, cílová skupina je `client_id` klienta. |
| Exp |Čas vypršení platnosti. Čas, kdy vyprší platnost tokenu. Token, který má být platný, aktuální datum a čas musí být menší než nebo rovno `exp` hodnotu. Čas je reprezentován jako počet sekund od 1. ledna 1970 (pod hodnotou 1970-01-01T0:0:0Z) UTC až do okamžiku vypršení platnosti tokenu.|
| family_name |Uživatelské poslední jméno nebo příjmení. Aplikace můžete zobrazit tuto hodnotu. |
| given_name |Křestní jméno uživatele. Aplikace můžete zobrazit tuto hodnotu. |
| IAT |Vydané v čase. Čas, kdy byl vydán token JWT. Čas je reprezentován jako počet sekund od 1. ledna 1970 (pod hodnotou 1970-01-01T0:0:0Z) UTC až do okamžiku byl token vydán. |
| iss |Identifikuje vydavatel tokenu |
| nbf |Neplatný před čas. Čas, kdy začne platit token. Pro token, který má být platný aktuální datum a čas musí být větší než nebo rovna hodnotě Nbf. Čas je reprezentován jako počet sekund od 1. ledna 1970 (pod hodnotou 1970-01-01T0:0:0Z) UTC až do okamžiku byl token vydán. |
| OID |Identifikátor objektu (ID) objektu uživatele ve službě Azure AD. |
| Sub – |Identifikátor tokenu subjektu. Toto je trvalé a nezměnitelné identifikátor pro uživatele, který popisuje token. Použijte tuto hodnotu v ukládání do mezipaměti logiku. |
| TID |Identifikátor klienta (ID) klienta Azure AD, která vydala token. |
| unique_name |Jedinečný identifikátor, který lze zobrazit uživateli. Je to obvykle hlavní název uživatele (UPN). |
| hlavní název uživatele |Hlavní název uživatele uživatele. |
| ver |Verze. Verze token JWT, obvykle 1.0. |

### <a name="error-response"></a>Chybové odpovědi
Koncový bod chyby vystavování tokenů jsou kódy chyb protokolu HTTP, protože klient zavolá koncový bod vystavování tokenů přímo. Kromě stavový kód protokolu HTTP na koncový bod Azure AD vystavování tokenů také vrátí hodnotu dokumentu JSON s objekty, které popisují chybu.

Ukázková chyba odpověď může vypadat například takto:

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
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování. |
| error_codes |Seznam tokenů zabezpečení specifické chybové kódy, které vám můžou pomoct při diagnostiky. |
| časové razítko |Čas, kdy se stala chyba. |
| trace_id |Jedinečný identifikátor pro požadavek, který vám můžou pomoct při diagnostiky. |
| correlation_id |Jedinečný identifikátor pro požadavek, který vám můžou pomoct při diagnostiky mezi komponentami. |

#### <a name="http-status-codes"></a>Stavové kódy HTTP
Následující tabulka uvádí stavové kódy HTTP, které vrací koncový bod vystavování tokenů. V některých případech kód chyby je dostačující k popisu odpovědi, ale pokud nejsou chyby, budete muset analyzovat doprovodné dokumentu JSON a zkontrolujte jeho kód chyby.

| Kód HTTP | Popis |
| --- | --- |
| 400 |Výchozí kód HTTP. Používá se ve většině případů a obvykle kvůli chybně vytvořený požadavek. Opravte a odešlete požadavek znovu. |
| 401 |Ověření se nezdařilo. Například v požadavku chybí parametr tajný klíč client_secret. |
| 403 |Ověření se nepovedlo. Například uživatel nemá oprávnění pro přístup k prostředku. |
| 500 |Došlo k vnitřní chybě na službu. Opakujte žádost. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Kódy chyb pro koncový bod tokenu chyby
| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící povinný parametr. |Opravte a odešlete požadavek znovu |
| invalid_grant |Autorizační kód je neplatná nebo skončila jeho platnost. |Zkuste nový požadavek `/authorize` koncový bod |
| unauthorized_client |Ověřený klient nemá oprávnění používat tento typ udělení autorizace. |K tomu obvykle dojde, pokud klientská aplikace není registrovaný ve službě Azure AD nebo nebyla přidána do klienta Azure AD pro uživatele. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD. |
| invalid_client |Ověření klienta se nezdařilo. |Pověření klienta nejsou platné. Pokud chcete vyřešit, Správce aplikací aktualizuje přihlašovací údaje. |
| unsupported_grant_type |Autorizace serveru nepodporuje typ udělení autorizace. |Změňte typ udělení v požadavku. Tento typ chyby provedeno pouze během vývoje a zjistit během počáteční testování. |
| invalid_resource |Cílový prostředek je neplatný, protože neexistuje, Azure AD ji nemůže najít, nebo není správně nakonfigurována. |To znamená, že k prostředku, pokud existuje, není nakonfigurované v klientovi. Aplikace můžete vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Azure AD. |
| interaction_required |Požadavek vyžaduje interakci uživatele. Na další ověřování krok je třeba požadovaný. | Místo neinteraktivní žádost opakujte akci s požadavek interaktivní autorizace pro stejný prostředek. |
| temporarily_unavailable |Server je dočasně zaneprázdněn pro zpracování požadavku. |Opakujte žádost. Klientská aplikace mohou vysvětlit pro uživatele, že odpověď se zpožďuje kvůli dočasné podmínce. |

## <a name="use-the-access-token-to-access-the-resource"></a>Použití tokenu přístupu pro přístup k prostředku
Teď, když jste byla úspěšně načtena `access_token`, můžete token v žádostech o k webovým rozhraním API, včetně jeho `Authorization` záhlaví. [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) specifikace vysvětluje, jak používat nosné tokeny v požadavcích HTTP pro přístup k chráněným prostředkům.

### <a name="sample-request"></a>Ukázková žádost
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Chybové odpovědi
Zabezpečené prostředky, které implementují stavové kódy HTTP problém RFC 6750. Pokud žádost neobsahuje pověření pro ověření nebo chybí token, odpověď obsahuje `WWW-Authenticate` záhlaví. Pokud se požadavek nezdaří, server prostředků odpoví stavový kód HTTP a chybový kód.

Následuje příklad úspěšné odpovědi když žádost klienta nezahrnuje token nosiče:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parametry chyby
| Parametr | Popis |
| --- | --- |
| authorization_uri |Identifikátor URI (fyzické koncový bod) serveru ověřování. Tato hodnota se také používá jako klíč vyhledávání získat další informace o serveru z koncového bodu zjišťování. <p><p> Klient musíte ověřit, že server ověřování je důvěryhodný. Když prostředek je chráněný službou Azure AD, je dostačující k ověření, že adresa URL začíná https://login.microsoftonline.com nebo jiný název hostitele, který podporuje Azure AD. Prostředek konkrétního klienta by měl vrátit vždy identifikátor URI autorizace konkrétního klienta. |
| error |K chybě kódu hodnota definovaná v části 5.2 z [Framework autorizace OAuth 2.0](http://tools.ietf.org/html/rfc6749). |
| error_description |Podrobnější popis chyby. Tato zpráva neměla být popisný koncového uživatele. |
| ID_prostředku |Vrací jedinečný identifikátor prostředku. Klientská aplikace můžete použít tento identifikátor jako hodnotu `resource` parametr při požadavku na token pro prostředek. <p><p> Je důležité pro klientskou aplikaci ověřit tuto hodnotu, jinak může být škodlivý služby moci vyvolat **zvýšení oprávnění** útoku <p><p> Pro zabránění útoku doporučujeme ověřit, jestli `resource_id` odpovídá základní webové adresy URL rozhraní API, která přistupuje. Například pokud https://service.contoso.com/data se přistupuje, `resource_id` může být htttps://service.contoso.com/. Klientská aplikace musí odmítnout `resource_id` , nemá na začátku základní adresu URL Pokud nejsou spolehlivé alternativní způsob, jak ověřit id. |

#### <a name="bearer-scheme-error-codes"></a>Kódy chyb nosiče schéma
Specifikace RFC 6750 definuje následující chyby pro prostředky, které používají hlavička WWW-Authenticate a schéma nosiče v odpovědi.

| Kód stavu HTTP | Kód chyby | Popis | Akce klienta |
| --- | --- | --- | --- |
| 400 |invalid_request |Požadavek není ve správném formátu. Například je může chybí parametr nebo pomocí stejný parametr dvakrát. |Opravte chybu a opakujte žádost. Tento typ chyby provedeno pouze během vývoje a zjistit počáteční testování v. |
| 401 |invalid_token |Přístupový token chybí, je neplatný nebo je odvolané. Hodnota parametru error_description poskytuje další podrobnosti. |Požádat o nový token ze serveru ověřování. Pokud se nezdaří, nový token došlo k neočekávané chybě. Odeslat chybovou zprávu pro uživatele a zkuste to znovu za náhodné zpoždění. |
| 403 |insufficient_scope |Přístupový token neobsahuje zosobnění oprávnění požadovaná pro přístup k prostředku. |Poslat novou žádost o autorizaci koncového bodu autorizace. Pokud odpověď obsahuje parametr rozsahu, použijte hodnotu oboru v požadavku na prostředek. |
| 403 |insufficient_access |Předmět tokenu nemá oprávnění, která jsou potřebná pro přístup k prostředku. |Dotázat se uživatele, použijte jiný účet nebo požádejte o oprávnění k zadaný prostředek. |

## <a name="refreshing-the-access-tokens"></a>Aktualizace přístupových tokenů
Přístupové tokeny jsou krátkodobou a je třeba aktualizovat po vypršení jejich platnosti chcete-li pokračovat, přístup k prostředkům. Můžete obnovit `access_token` odesláním jiné `POST` žádost o `/token` koncový bod, ale tato poskytnutí času `refresh_token` místo `code`.

Aktualizujte tokeny nemají zadaný životnosti. Doba života tokeny obnovení jsou obvykle poměrně dlouho. Ale v některých případech tokeny obnovení vyprší, byly odvolány nebo nemají dostatečná oprávnění pro požadovanou akci. Aplikace musí očekávat a zpracování chyby vrácené v koncovém bodě vystavování tokenů správně.

Jakmile se zobrazí odpověď se chyba tokenu aktualizace, zahodit aktuální obnovovací token a nové autorizační kód požadavku nebo přístupový token. Zejména při použití aktualizace tokenu v toku, udělení autorizačního kódu, pokud se zobrazí odpověď se `interaction_required` nebo `invalid_grant` kódy chyb, zrušíte tokenu obnovení a požádejte o nový kód autorizace.

Ukázková žádost na **konkrétního klienta** koncový bod (můžete také **běžné** koncového bodu) získat přístup k nové tokenu pomocí tokenu obnovení vypadá takto:

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
Úspěšné odpovědi tokenu bude vypadat jako:

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
| token_type |Typ tokenu. Jedinou podporovanou hodnotou je **nosiče**. |
| expires_in |Zbývající dobu životnosti tokenu v sekundách. Typické hodnota je 3600 (jedna hodina). |
| expires_on |Datum a čas, kdy vyprší platnost tokenu. Datum je reprezentován jako počet sekund od pod hodnotou 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. |
| prostředek |Identifikuje zabezpečené prostředek, který může být přístupový token používané k přístupu. |
| scope |Zosobnění oprávnění udělená nativní klientskou aplikaci. Výchozí oprávnění je **user_impersonation**. Vlastník cílového prostředku můžete zaregistrovat alternativní hodnoty ve službě Azure AD. |
| access_token |Nový přístupový token, který byl vyžádán. |
| refresh_token |Nový refresh_token OAuth 2.0, který slouží k vyžádání nové přístupové tokeny, když vyprší platnost v této odpovědi. |

### <a name="error-response"></a>Chybové odpovědi
Ukázková chyba odpověď může vypadat například takto:

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
| error |Řetězec kódu chyby, který můžete použít ke klasifikaci typů chyb, ke kterým došlo a slouží k reagovat na chyby. |
| error_description |Konkrétní chybová zpráva, která může pomoci vývojář určit hlavní příčinu chyby ověřování. |
| error_codes |Seznam tokenů zabezpečení specifické chybové kódy, které vám můžou pomoct při diagnostiky. |
| časové razítko |Čas, kdy se stala chyba. |
| trace_id |Jedinečný identifikátor pro požadavek, který vám můžou pomoct při diagnostiky. |
| correlation_id |Jedinečný identifikátor pro požadavek, který vám můžou pomoct při diagnostiky mezi komponentami. |

Popis kódy chyb a klienta doporučenou akci najdete v tématu [kódy chyb pro koncový bod tokenu chyby](#error-codes-for-token-endpoint-errors).

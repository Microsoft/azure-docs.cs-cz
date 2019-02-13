---
title: Pochopení toku kódu autorizace OAuth 2.0 ve službě Azure AD
description: Tento článek popisuje, jak používat zprávy HTTP k autorizaci přístupu k webovým aplikacím a webovým rozhraním API ve vašem tenantovi pomocí služby Azure Active Directory a OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbde6eda2bd532b1a26a58e4ca82c9b5fab4e6c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188592"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autorizace přístupu k webovým aplikacím služby Azure Active Directory pomocí toku přidělení kódu OAuth 2.0

Azure Active Directory (Azure AD) používá standard OAuth 2.0 k umožnění autorizace přístupu k webovým aplikacím a webovým rozhraním API ve vašem tenantovi Azure AD. Tato příručka je nezávislá na jazyce a popisuje, jak posílat a přijímat zprávy HTTP bez použití našich [knihovny open-source](active-directory-authentication-libraries.md).

Tok autorizačního kódu OAuth 2.0 je popsaný v [části 4.1 specifikace OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Používá se k provedení ověřování a autorizace ve většině typů aplikací, včetně webových aplikací a nativně nainstalované aplikace.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Tok ověřování OAuth 2.0

Na vysoké úrovni tok celý autorizaci pro aplikace vypadá trochu takto:

![Tok kódu ověřování OAuth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Žádost o autorizační kód

Tok autorizačního kódu začíná klienta směruje uživatele `/authorize` koncového bodu. V této žádosti klient naznačuje oprávnění, která je potřeba získat od uživatele. Koncový bod autorizace OAuth 2.0 pro vašeho tenanta můžete získat tak, že vyberete **registrace aplikací > Koncové body** na webu Azure Portal.

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

| Parametr |  | Popis |
| --- | --- | --- |
| tenant |povinné |`{tenant}` Hodnota v cestě požadavku je možné řídit, kdo se můžete přihlásit do aplikace. Povolené hodnoty jsou identifikátory klientů, třeba `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` tokeny nezávislé na tenanta |
| client_id |povinné |ID aplikace přiřazené vaší aplikaci, když je zaregistrován ve službě Azure AD. To můžete najít na webu Azure Portal. Klikněte na tlačítko **Azure Active Directory** na bočním panelu služby klikněte na tlačítko **registrace aplikací**a zvolte aplikaci. |
| response_type |povinné |Musí zahrnovat `code` pro tok autorizačního kódu. |
| redirect_uri |Doporučené |Redirect_uri vaší aplikace, kde můžete odesílat a přijímat aplikací pro žádosti o ověření. Musí odpovídat přesně jeden z redirect_uris, které jste zaregistrovali na portálu, s tím rozdílem, musí být kódování url. Pro nativní a mobilní aplikace, byste měli použít výchozí hodnotu `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |nepovinné |Určuje metodu, která se má použít k odeslání výsledný token zpátky do vaší aplikace. Může být `query`, `fragment`, nebo `form_post`. `query` poskytuje kód jako parametru řetězce dotazu na váš identifikátor URI pro přesměrování. Pokud se požaduje token ID pomocí implicitního toku, nemůžete použít `query` podle [OpenID specifikace](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Pokud požadujete přesně takový kód, můžete použít `query`, `fragment`, nebo `form_post`. `form_post` provede příspěvek, který obsahuje kód, který váš identifikátor URI pro přesměrování. Výchozí hodnota je `query` pro tok kódu.  |
| state |Doporučené |Hodnota v požadavku, který je také vrácen v odpovědi tokenu. Náhodně generované jedinečná hodnota se obvykle používá pro [prevence útoků proti padělání žádosti více webů](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |
| prostředek | Doporučené |Identifikátor URI ID aplikace cílového webového rozhraní API (zabezpečeným prostředkům). Identifikátor ID URI aplikace najdete na webu Azure Portal klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**, otevřete aplikaci **nastavení** stránce a potom klikněte na  **Vlastnosti**. Může být také externí prostředek jako `https://graph.microsoft.com`. Vyžaduje se v jednom autorizace nebo žádostí o token. K zajištění méně ověřování výzvy umístěte žádost o autorizaci, ujistěte se, že přijetí souhlasu uživatele. |
| scope | **ignored** | Pro aplikace Azure AD v1, musí být staticky nakonfigurován obory na webu Azure Portal v části aplikace **nastavení**, **požadovaná oprávnění**. |
| řádek |nepovinné |Označuje typ interakce s uživatelem, který je požadován.<p> Platné hodnoty jsou: <p> *Přihlášení*: Uživatel by měl vyzván donutit k. <p> *select_account*: Bude uživatel vyzván k výběru účtu, přerušení na jednotné přihlašování. Uživatel může vybrat existující účet přihlášeného, zadejte své přihlašovací údaje pro účet zapamatovaných nebo můžete použít jiný účet úplně se vynechá. <p> *Souhlas*: Souhlas uživatele bylo uděleno, ale je potřeba aktualizovat. Uživatel by měl výzva k vyjádření souhlasu. <p> *admin_consent*: Správce by měl být vyzváni k udělit souhlas jménem všech uživatelů v organizaci |
| login_hint |nepovinné |Umožňuje předem vyplnit pole uživatelské jméno nebo e-mailová adresa stránky přihlášení pro uživatele, pokud znáte svoje uživatelské jméno předem. Tento parametr použijte, často aplikace během opětovné ověření, uživatelské jméno s již extrahovat z předchozí přihlášení pomocí `preferred_username` deklarací identity. |
| domain_hint |nepovinné |Poskytuje informace o tenantovi nebo doménu, kterou uživatel by měl používat k přihlášení. Hodnota domain_hint je registrované domény pro tenanta. Pokud tenanta je Federovaná do místního adresáře, AAD se přesměruje na federační server zadaného klienta. |
| code_challenge_method | Doporučené    | Metoda použitá pro kódování `code_verifier` pro `code_challenge` parametru. Může být jedna z `plain` nebo `S256`. Pokud vyloučeny, `code_challenge` předpokládá, že je možné ve formátu prostého textu, pokud `code_challenge` je součástí. Azure AAD v1.0 podporuje obě `plain` a `S256`. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | Doporučené    | Používá k zabezpečení udělení autorizace kódu pomocí testování klíče pro výměnu kód (PKCE) od klienta nativní nebo veřejné. Požadováno pokud `code_challenge_method` je součástí. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Pokud uživatel je součástí organizace, správce organizace souhlas nebo odmítnout jménem uživatele nebo povolit o souhlas. Uživatel dostane možnost pro vyjádření souhlasu jenom v případě, že to umožňuje správci.
>
>

V tomto okamžiku je uživatel vyzván k zadání přihlašovacích údajů a souhlas s oprávnění požadovaná aplikací na webu Azure Portal. Jakmile se uživatel ověří a udělí svůj souhlas, Azure AD odešle odpověď na vaši aplikaci na `redirect_uri` adres ve vaší žádosti s kódem.

### <a name="successful-response"></a>Úspěšné odpovědi
Úspěšné odpovědi by mohl vypadat takto:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametr | Popis |
| --- | --- |
| admin_consent |Hodnota je True, pokud na příkazový řádek žádost o souhlas souhlas správce. |
| kód |Autorizační kód, který aplikace požaduje. Aplikace můžete požádat o přístupový token pro cílový prostředek autorizační kód. |
| session_state |Jedinečnou hodnotu, která identifikuje aktuální relaci uživatele. Tato hodnota je identifikátor GUID, ale mají být považována za neprůhledná hodnota, která je předána bez zkoumání. |
| state |Pokud parametr stavu je zahrnutý v požadavku, by se zobrazit stejnou hodnotu v odpovědi. Je vhodné pro aplikace, hodnoty stavu v požadavku a odpovědi jsou identické před použitím odpovědi. To pomáhá detekovat [útoků proti padělání žádosti mezi weby (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) proti klienta. |

### <a name="error-response"></a>Odpověď na chybu
Chybové odpovědi může také být odeslán `redirect_uri` tak, aby aplikace můžete odpovídajícím způsobem zpracovat.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Chybová hodnota kódu definované v části 5.2 [Framework autorizace OAuth 2.0](https://tools.ietf.org/html/rfc6749). Následující tabulka popisuje kódy chyb, které vrátí Azure AD. |
| error_description |Podrobnější popis chyby. Tato zpráva není určen jako koncový uživatel zařízení. |
| state |Hodnota stavu je náhodně generované-znovu použít hodnotu, která se odesílají v požadavku a vrátila v reakci na padělání (CSRF) útokům podvržení žádosti. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Kódy chyb pro chyby koncový bod autorizace
Následující tabulka popisuje různé kódy chyb, které mohou být vráceny v `error` parametr odpovědi na chybu.

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, jako je například chybí povinný parametr. |Opravte a odešlete požadavek znovu. Toto je vývoj chyba a obvykle zachycuje se během počáteční testování. |
| unauthorized_client |Klientská aplikace není povoleno požádat o autorizační kód. |K tomu obvykle dojde, když klientská aplikace není zaregistrovaný ve službě Azure AD nebo se nepřidal do tenanta služby Azure AD daného uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |
| access_denied |Odepřen souhlas vlastníka prostředku |Klientská aplikace může upozornit uživatele, který nemůže pokračovat, dokud uživatel souhlasí. |
| unsupported_response_type |Autorizační server nepodporuje typ odpovědi v požadavku. |Opravte a odešlete požadavek znovu. Toto je vývoj chyba a obvykle zachycuje se během počáteční testování. |
| server_error |Na serveru došlo k neočekávané chybě. |Zkuste požadavek. Tyto chyby můžou být výsledkem dočasné situace. Klientská aplikace může vysvětlit uživatelům, že odpověď se zpožďuje kvůli dočasné chybě. |
| temporarily_unavailable |Server je dočasně příliš zaneprázdněn a nemůže žádost zpracovat. |Zkuste požadavek. Klientská aplikace může vysvětlit uživateli, odpověď se zpožďuje kvůli dočasnému chybovému stavu. |
| invalid_resource |Cílový prostředek je neplatný, protože buď neexistuje, Azure AD nelze najít nebo není správně nakonfigurovaný. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Můžete požádat o přístupový token autorizační kód
Teď, když jste získali autorizační kód a udělil oprávnění uživatele, může uplatnit kód pro přístupový token u požadovaného prostředku odesláním požadavkům POST odeslaných `/token` koncový bod:

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
| tenant |povinné |`{tenant}` Hodnota v cestě požadavku je možné řídit, kdo se můžete přihlásit do aplikace. Povolené hodnoty jsou identifikátory klientů, třeba `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` nebo `common` tokeny nezávislé na tenanta |
| client_id |povinné |Id aplikace přiřazené vaší aplikaci, když je zaregistrován ve službě Azure AD. To můžete najít na webu Azure Portal. Id aplikace se zobrazí v nastavení registrace aplikací. |
| grant_type |povinné |Musí být `authorization_code` pro tok autorizačního kódu. |
| kód |povinné |`authorization_code` , Kterou jste získali v předchozí části |
| redirect_uri |povinné |Stejné `redirect_uri` hodnotu, která byla použita k získání `authorization_code`. |
| client_secret |vyžaduje se pro webové aplikace, není povolena pro veřejné klienty |Tajný klíč aplikace, kterou jste vytvořili na webu Azure Portal pro vaši aplikaci v rámci **klíče**. Nelze se použít v nativní aplikaci (veřejným klientem), protože client_secrets nemůže být spolehlivě uložená na zařízeních. Vyžaduje se pro webové aplikace a webová rozhraní API (všechny důvěrní klienti), které mají možnost ukládat `client_secret` bezpečně na straně serveru. Hodnotu client_secret by měl být kódovaná adresou URL před odesláním. |
| prostředek | Doporučené |Identifikátor URI ID aplikace cílového webového rozhraní API (zabezpečeným prostředkům). Identifikátor ID URI aplikace najdete na webu Azure Portal klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**, otevřete aplikaci **nastavení** stránce a potom klikněte na  **Vlastnosti**. Může být také externí prostředek jako `https://graph.microsoft.com`. Vyžaduje se v jednom autorizace nebo žádostí o token. K zajištění méně ověřování výzvy umístěte žádost o autorizaci, ujistěte se, že přijetí souhlasu uživatele. Pokud žádost o autorizaci a token požadavku, prostředek ' parametry musí odpovídat. | 
| code_verifier | nepovinné | Stejné code_verifier, který se používá k získání authorization_code. Povinné, pokud PKCE použila žádost o udělení autorizace kódu. Další informace najdete v tématu [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Identifikátor ID URI aplikace najdete na webu Azure Portal klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **registrace aplikací**, otevřete aplikaci **nastavení** stránce a potom klikněte na  **Vlastnosti**.

### <a name="successful-response"></a>Úspěšné odpovědi
Azure AD vrátí [přístupový token](access-tokens.md) po úspěšné odpovědi. Chcete-li minimalizovat síťová volání z klientské aplikace a jejich přidružené latence, by měla klientská aplikace mezipaměti přístupové tokeny po celou dobu životnosti tokenu, který je zadán v odpovědi OAuth 2.0. Chcete-li určit dobu životnosti tokenu, použijte buď `expires_in` nebo `expires_on` hodnoty parametrů.

Pokud webové rozhraní API prostředku vrátí `invalid_token` kód chyby: to může znamenat, že prostředek bylo zjištěno, že platnost tokenu vypršela. Pokud klienta a prostředků hodiny doby jsou různé (označují se termínem "zkosení čas"), prostředek zvážit token, který má být vypršelo dřív, než se vymaže tokenu z mezipaměti klienta. Pokud k tomu dojde, zrušte tokenu z mezipaměti, i v případě, že je stále v rámci svého počítané životního cyklu.

Úspěšné odpovědi by mohl vypadat takto:

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
| access_token |Požadovaná [přístupový token](access-tokens.md) jako podepsané JSON Web Token (JWT). Aplikace můžete používat tento token k ověření k zabezpečeným prostředkům, jako je například webové rozhraní API. |
| token_type |Určuje hodnotu pro typ tokenu. Jediný typ, který podporuje Azure AD je nosiče. Další informace o nosných tokenů najdete v tématu [Framework autorizace OAuth 2.0: Použití tokenu nosiče (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. Tato hodnota se používá k určení doby života tokenů v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace webového rozhraní API (zabezpečeným prostředkům). |
| scope |Zosobnění oprávnění udělená do klientské aplikace. Výchozí oprávnění je `user_impersonation`. Vlastník prostředku zabezpečené můžete zaregistrovat další hodnoty ve službě Azure AD. |
| refresh_token |Aktualizace tokenu OAuth 2.0. Aplikace můžete používat tento token se po vypršení platnosti přístupového tokenu aktuální získat dodatečné přístupové tokeny. Aktualizace jsou dlouhodobé tokeny a slouží k přístupu k prostředkům uchovávat po dlouhou dobu. |
| id_token |Bez znaménka představující JSON Web Token (JWT) [ID token](id-tokens.md). Base64Url aplikace může dekódovat segmenty tento token na žádost o informace o uživateli, který přihlášení. Aplikaci můžete ukládat do mezipaměti hodnoty a jejich zobrazení, ale na ně neměli spoléhat pro povolení nebo hranice zabezpečení. |

Další informace o webových tokenů JSON najdete v článku [specifikace konceptu JWT IETF](https://go.microsoft.com/fwlink/?LinkId=392344).   Další informace o `id_tokens`, najdete v článku [v1.0 tok OpenID Connect](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Odpověď na chybu
Koncový bod chyby vystavování tokenů jsou kódy chyb protokolu HTTP, protože klient zavolá koncový bod vystavování tokenů přímo. Kromě stavový kód HTTP vrátí koncový bod vystavení tokenu Azure AD také dokument JSON s objekty, které popisují chybu.

Ukázková chybová odpověď může vypadat například takto:

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
| error |Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| error_description |Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |
| error_codes |Seznam kódy chyb specifické pro službu tokenů zabezpečení, které vám můžou pomoct při diagnostiky. |
| časové razítko |Čas, kdy došlo k chybě. |
| trace_id |Jedinečný identifikátor pro požadavek, který vám pomůže v diagnostice. |
| correlation_id |Jedinečný identifikátor pro požadavek, který vám pomůže v diagnostice mezi komponentami. |

#### <a name="http-status-codes"></a>Stavové kódy HTTP
Následující tabulka uvádí stavové kódy HTTP, která vrací koncový bod vystavování tokenů. V některých případech kód chyby je dostačující k popisu odpověď, ale pokud chyby existují, budete muset analyzovat související dokument JSON a zkontrolujte jeho kód chyby.

| Kód HTTP | Popis |
| --- | --- |
| 400 |Výchozí kód protokolu HTTP. Ve většině případů použít a je chybně vytvořený požadavek. Opravte a odešlete požadavek znovu. |
| 401 |Ověření se nezdařilo. Například v požadavku chybí hodnota client_secret parametr. |
| 403 |Ověření se nepovedlo. Například uživatel nemá oprávnění k přístupu k prostředku. |
| 500 |Na službu došlo k vnitřní chybě. Zkuste požadavek. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Kódy chyb pro koncový bod tokenu chyby
| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, jako je například chybí povinný parametr. |Opravte a odešlete požadavek znovu |
| invalid_grant |Autorizační kód je neplatná nebo vypršela platnost. |Vyzkoušejte nový požadavek `/authorize` koncového bodu |
| unauthorized_client |Ověřený klient nemá oprávnění používat tento typ udělení autorizace. |K tomu obvykle dojde, když klientská aplikace není zaregistrovaný ve službě Azure AD nebo se nepřidal do tenanta služby Azure AD daného uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |
| invalid_client |Ověření klienta se nezdařilo. |Pověření klienta nejsou platné. Pokud chcete vyřešit, správce aplikace aktualizuje přihlašovací údaje. |
| unsupported_grant_type |Autorizační server nepodporuje typ udělení autorizace. |Změňte typ udělení v požadavku. Tento typ chyby se budou objevovat pouze během vývoje a zjistili při počátečním testování. |
| invalid_resource |Cílový prostředek je neplatný, protože buď neexistuje, Azure AD nelze najít nebo není správně nakonfigurovaný. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a jeho přidání do služby Azure AD. |
| interaction_required |Požadavek vyžaduje zásah uživatele. Například další ověřovací krok je nutný. | Místo žádost o neinteraktivním přístupu zkuste to znovu s interaktivní žádost o autorizaci pro stejný prostředek. |
| temporarily_unavailable |Server je dočasně příliš zaneprázdněn a nemůže žádost zpracovat. |Zkuste požadavek. Klientská aplikace může vysvětlit uživateli, odpověď se zpožďuje kvůli dočasnému chybovému stavu. |

## <a name="use-the-access-token-to-access-the-resource"></a>Použití přístupového tokenu pro přístup k prostředku
Teď, když jste úspěšně získala `access_token`, můžete token v žádosti k webovým rozhraním API, včetně v `Authorization` záhlaví. [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) specifikace vysvětluje, jak pomocí nosných tokenů v požadavcích HTTP pro přístup k chráněným prostředkům.

### <a name="sample-request"></a>Ukázková žádost
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Odpověď na chybu
Zabezpečené prostředky, které implementují stavové kódy HTTP RFC 6750 problém. Pokud žádost neobsahuje přihlašovací údaje pro ověření nebo chybí token, odpověď obsahuje `WWW-Authenticate` záhlaví. Pokud se požadavek nezdaří, server prostředků odpoví kód stavu protokolu HTTP, kód chyby.

Následuje příklad úspěšné odpovědi HTTP při požadavku klienta neobsahuje nosný token:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Chyba parametrů
| Parametr | Popis |
| --- | --- |
| authorization_uri |Identifikátor URI (fyzický koncový bod) autorizačního serveru. Tato hodnota se také používá jako klíč vyhledávání zobrazíte další informace o serveru z koncového bodu zjišťování. <p><p> Klienta musíte ověřit, že autorizační server je důvěryhodný. Když prostředek je chráněný službou Azure AD, je dostačující k ověření, že adresa URL začíná https://login.microsoftonline.com nebo jiný název hostitele, který podporuje Azure AD. Prostředek specifickým pro tenanta by měl vždy vrátit specifickým pro tenanta autorizace identifikátoru URI. |
| error |Chybová hodnota kódu definované v části 5.2 [Framework autorizace OAuth 2.0](https://tools.ietf.org/html/rfc6749). |
| error_description |Podrobnější popis chyby. Tato zpráva není určen jako koncový uživatel zařízení. |
| resource_id |Vrací jedinečný identifikátor prostředku. Klientská aplikace může jako hodnotu pomocí tohoto identifikátoru `resource` parametr při požadavku na token prostředku. <p><p> Je důležité pro klientskou aplikaci, chcete-li ověřit tuto hodnotu, jinak může být škodlivý služby moct zahájit **zvýšení úrovně oprávnění** útoku <p><p> K zamezení útoku doporučujeme ověřit, jestli `resource_id` odpovídá základní webové adresy URL rozhraní API, ke kterému přistupujete. Například pokud https://service.contoso.com/data je přistupováno, `resource_id` může být htttps://service.contoso.com/. Klientská aplikace musí odmítnout `resource_id` , který nezačíná základní adresa URL není spolehlivé alternativní způsob, jak ověřit id. |

#### <a name="bearer-scheme-error-codes"></a>Kódy chyb nosného schéma
Specifikaci RFC 6750 definuje následující chyby pro prostředky, které používají hlavičky WWW-Authenticate a schéma nosiče v odpovědi.

| Kód stavu HTTP | Kód chyby | Popis | Akce klienta |
| --- | --- | --- | --- |
| 400 |invalid_request |Žádost není ve správném formátu. Například to může být chybějící parametr nebo pomocí stejný parametr dvakrát. |Opravte chybu a opakujte žádost. Tento typ chyby se budou objevovat pouze během vývoje a zjistili při počátečním testování. |
| 401 |invalid_token |Přístupový token chybí, je neplatný nebo se odvolal. Hodnota parametru error_description poskytuje další podrobnosti. |Požádat o nový token od autorizačního serveru. Pokud se nezdaří, nový token došlo k neočekávané chybě. Odešlete chybovou zprávu pro uživatele a zkuste to znovu po náhodné zpoždění. |
| 403 |insufficient_scope |Přístupový token neobsahuje zosobnění oprávnění požadovaná pro přístup k prostředku. |Poslat novou žádost o autorizaci na koncový bod autorizace. Pokud odpověď neobsahuje parametr oboru, použijte hodnotu rozsahu v žádosti o prostředku. |
| 403 |insufficient_access |Předmět token, který nemá oprávnění požadovaná pro přístup k prostředku. |Vyzvat uživatele, použijte jiný účet nebo požádejte o oprávnění pro zadaný prostředek. |

## <a name="refreshing-the-access-tokens"></a>Aktualizaci přístupových tokenů

Jsou krátkodobé přístupové tokeny a musí být aktualizovány po vypršení platnosti i nadále přístup k prostředkům. Můžete aktualizovat `access_token` odesláním jiného `POST` požádat o `/token` koncového bodu, ale tato doba poskytování `refresh_token` místo `code`.  Obnovovací tokeny jsou platné pro všechny prostředky, které váš klient už je zadaná souhlas s přístupem k – díky tomu se obnovovací token vydaný na vyžádání pro `resource=https://graph.microsoft.com` umožňuje požádat o nový přístupový token pro `resource=https://contoso.com/api`. 

Aktualizovat tokeny nemají zadaný životnosti. Životní cyklus obnovovací tokeny jsou obvykle poměrně dlouho. Ale v některých případech tokeny obnovení vyprší, byly odvolány nebo nemají dostatečná oprávnění pro požadovanou akci. Vaše aplikace potřebuje očekávají a zpracování chyby vrácené systémem koncový bod vystavování tokenů správně.

Když obdržíte odpověď s chybou obnovovací token, zrušit aktuální obnovovací token a požádat o nový kód pro ověření nebo přístup k tokenu. Zejména při použití aktualizace token v toku udělení autorizačního kódu, pokud se zobrazí odpověď s `interaction_required` nebo `invalid_grant` kódy chyb, zrušíte aktualizační token a požádat o nový kód pro ověření.

Ukázkový požadavek na **specifickým pro tenanta** koncový bod (můžete také použít **běžné** koncový bod) Chcete-li získat nový přístupový token pomocí obnovovacího tokenu vypadá takto:

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

### <a name="successful-response"></a>Úspěšné odpovědi
Úspěšné odpovědi tokenu bude vypadat takto:

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
| expires_in |Zbývající dobu životnosti tokenu v sekundách. Typická hodnota je 3 600 (jedna hodina). |
| expires_on |Datum a čas, kdy vyprší platnost tokenu. Datum je vyjádřena jako počet sekund od 1970-01-01T0:0:0Z UTC až do okamžiku vypršení platnosti. |
| prostředek |Identifikuje zabezpečeným prostředkům, které mohou být přístupový token slouží k přístupu. |
| scope |Zosobnění oprávnění udělená nativní klientskou aplikaci. Výchozí oprávnění je **user_impersonation**. Vlastník cílový prostředek můžete alternativní hodnoty registru ve službě Azure AD. |
| access_token |Nový přístupový token, který byl požadován. |
| refresh_token |Nový refresh_token OAuth 2.0, který slouží k vyžádání nových přístupových tokenů, když vyprší platnost v této odpovědi. |

### <a name="error-response"></a>Odpověď na chybu
Ukázková chybová odpověď může vypadat například takto:

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
| error |Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| error_description |Určité chybové zprávě, který vám pomůže vývojář zjistit původní příčinu chyby ověřování. |
| error_codes |Seznam kódy chyb specifické pro službu tokenů zabezpečení, které vám můžou pomoct při diagnostiky. |
| časové razítko |Čas, kdy došlo k chybě. |
| trace_id |Jedinečný identifikátor pro požadavek, který vám pomůže v diagnostice. |
| correlation_id |Jedinečný identifikátor pro požadavek, který vám pomůže v diagnostice mezi komponentami. |

Popis kódy chyb a akci doporučenou klientskou, naleznete v tématu [kódy chyb pro koncový bod tokenu chyby](#error-codes-for-token-endpoint-errors).

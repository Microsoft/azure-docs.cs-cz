---
title: Principy toku autorizačního kódu OAuth 2.0 ve službě Azure AD
description: Tento článek popisuje, jak pomocí zpráv HTTP autorizovat přístup k webovým aplikacím a webovým rozhraním API ve vašem tenantovi pomocí Služby Azure Active Directory a OAuth 2.0.
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
ms.openlocfilehash: 3ec7cf5a45ce31cde923dce521636589cfcda786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154453"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autorizace přístupu k webovým aplikacím Azure Active Directory s využitím toku poskytování kódů OAuth 2.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Pokud serveru nesdělíte, jaký prostředek chcete volat, server neaktivuje zásady podmíněného přístupu pro tento prostředek. Takže chcete-li mít aktivační událost Vícefaktorové finanční autority, budete muset do adresy URL zahrnout prostředek. 
>

Azure Active Directory (Azure AD) používá standard OAuth 2.0 za účelem umožnění autorizace přístupu k webovým aplikacím a webovým rozhraním API v tenantovi Azure AD. Tato příručka je jazykově nezávislá a popisuje, jak odesílat a přijímat zprávy HTTP bez použití některé z našich [open-source knihoven](active-directory-authentication-libraries.md).

Tok autorizačního kódu OAuth 2.0 je popsán v [bodu 4.1 specifikace OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Používá se k ověřování a autorizaci ve většině typů aplikací, včetně webových aplikací a nativně nainstalovaných aplikací.

## <a name="register-your-application-with-your-ad-tenant"></a>Registrace aplikace pomocí tenanta AD
Nejprve zaregistrujte aplikaci u svého klienta Azure Active Directory (Azure AD). Pro svou aplikaci tak získáte ID a umožníte jí přijímat tokeny.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
   
1. Vyberte si klienta Azure AD výběrem účtu v pravém horním rohu stránky, následovaným výběrem navigace **Přepnout adresář** a pak výběrem příslušného klienta. 
   - Přeskočte tento krok, pokud máte jenom jednoho klienta Azure AD pod vaším účtem nebo pokud jste už vybrali příslušného klienta Azure AD.
   
1. Na webu Azure Portal vyhledejte a vyberte **Službu Azure Active Directory**.
   
1. V levé nabídce **Služby Azure Active Directory** vyberte **Registrace aplikací**a pak vyberte **Nová registrace**.
   
1. Postupujte podle zobrazených výzev a vytvořte novou aplikaci. Nezáleží na tom, jestli se jedná o webovou aplikaci nebo veřejnou klientskou (mobilní & desktop) aplikaci pro tento kurz, ale pokud chcete konkrétní příklady pro webové aplikace nebo veřejné klientské aplikace, podívejte se na naše [rychlé starty](v1-overview.md).
   
   - **Název** je název aplikace, který aplikaci popisuje koncovým uživatelům.
   - V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft**.
   - Zadejte **identifikátor URI přesměrování**. Pro webové aplikace se jedná o základní adresu URL vaší aplikace, kde se uživatelé mohou přihlásit.  Například, `http://localhost:12345`. Pro veřejného klienta (mobilní & desktop), Azure AD používá k vrácení odpovědi tokenu. Zadejte hodnotu specifickou pro vaši aplikaci.  Například, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Po dokončení registrace azure ad přiřadí vaší aplikaci jedinečný identifikátor klienta **(ID aplikace).** Tuto hodnotu potřebujete v následujících částech, proto ji zkopírujte ze stránky aplikace.
   
1. Pokud chcete najít aplikaci na webu Azure Portal, vyberte **Registrace aplikací**a pak vyberte Zobrazit **všechny aplikace**.

## <a name="oauth-20-authorization-flow"></a>Tok autorizace OAuth 2.0

Na vysoké úrovni celý tok autorizace pro aplikaci vypadá trochu takto:

![Tok kódu OAuth Auth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Žádost o autorizační kód

Tok autorizačního kódu začíná tím, že `/authorize` klient nasměruje uživatele do koncového bodu. V tomto požadavku klient označuje oprávnění, která potřebuje získat od uživatele. Koncový bod autorizace OAuth 2.0 pro vašeho tenanta můžete získat výběrem **registrací aplikací > koncových bodů** na webu Azure Portal.

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
| Nájemce |Požadovaný |Hodnotu `{tenant}` v cestě požadavku lze použít k řízení, kdo se může přihlásit do aplikace. Povolené hodnoty jsou identifikátory klienta, `contoso.onmicrosoft.com` `common` například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo tokeny nezávislé na tenantovi |
| client_id |Požadovaný |ID aplikace přiřazené vaší aplikaci, když jste ji zaregistrovali ve službě Azure AD. Najdete to na webu Azure Portal. Na postranním panelu služeb klikněte na **Služby AD Active Directory,** klikněte na **Registrace aplikací**a zvolte aplikaci. |
| response_type |Požadovaný |Musí `code` obsahovat tok autorizačního kódu. |
| redirect_uri |Doporučené |Redirect_uri vaší aplikace, kde mohou být odpovědi na ověření odesílány a přijímány vaší aplikací. Musí přesně odpovídat jednomu z redirect_uris, které jste zaregistrovali na portálu, s tím rozdílem, že musí být kódována adresou URL. Pro nativní & mobilní aplikace byste `https://login.microsoftonline.com/common/oauth2/nativeclient`měli použít výchozí hodnotu aplikace . |
| response_mode |optional |Určuje metodu, která by měla být použita k odeslání výsledného tokenu zpět do vaší aplikace. Může `query`být `fragment`, `form_post`, nebo . `query`poskytuje kód jako parametr řetězce dotazu na identifikátorURI přesměrování. Pokud požadujete token ID pomocí implicitního toku, nemůžete použít, `query` jak je uvedeno ve [specifikaci OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Pokud požadujete pouze kód, můžete použít `query` `fragment`, `form_post`nebo . `form_post`spustí post obsahující kód pro přesměrování URI. Výchozí hodnota `query` je pro tok kódu.  |
| state |Doporučené |Hodnota zahrnutá v požadavku, která je také vrácena v odpovědi tokenu. Náhodně generovaná jedinečná hodnota se obvykle používá k [zabránění útokům padělání požadavků na příčce webu](https://tools.ietf.org/html/rfc6749#section-10.12). Stav se také používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se nachází. |
| prostředek | Doporučené |Identifikátor URI ID aplikace cílového webového rozhraní API (zabezpečeného prostředku). Pokud chcete identifikátor URI ID aplikace najít, klikněte na webu Azure Portal na **Azure Active Directory**, klikněte na Registrace **aplikací**, otevřete stránku **Nastavení** aplikace a potom klikněte na **Vlastnosti**. Může se také jedná `https://graph.microsoft.com`o externí zdroj, jako je . To je vyžadováno v jednom z požadavků autorizace nebo tokenu. Chcete-li zajistit, aby se do žádosti o autorizaci umístilo méně dotazů na ověření, aby bylo zajištěno, že uživatel obdrží souhlas. |
| scope | **Ignorovány** | Pro aplikace v1 Azure AD musí být obory staticky nakonfigurované na webu Azure Portal v rámci **nastavení**aplikací , **požadovaná oprávnění**. |
| Výzva |optional |Označte typ požadované interakce s uživatelem.<p> Platné hodnoty jsou: <p> *přihlášení*: Uživatel by měl být vyzván k opětovnému ověření. <p> *select_account*: Uživatel je vyzván k výběru účtu, přerušení jednotného přihlášení. Uživatel si může vybrat existující přihlašovací účet, zadat své přihlašovací údaje k zapamatovanému účtu nebo zvolit použití jiného účtu úplně. <p> *souhlas*: Souhlas uživatele byl udělen, ale musí být aktualizován. Uživatel by měl být vyzván k souhlasu. <p> *admin_consent*: Správce by měl být vyzván k souhlasu jménem všech uživatelů v jejich organizaci. |
| login_hint |optional |Lze použít k předběžnému vyplnění pole uživatelského jména/e-mailové adresy přihlašovací stránky uživatele, pokud znáte jeho uživatelské jméno předem. Aplikace často používají tento parametr při opětovném ověřování, protože již extrahovaly uživatelské jméno z předchozího přihlášení pomocí `preferred_username` deklarace. |
| domain_hint |optional |Obsahuje nápovědu o tenantovi nebo doméně, kterou by měl uživatel použít k přihlášení. Hodnota domain_hint je registrovaná doména pro klienta. Pokud je klient federován do místního adresáře, aad přesměruje na zadaný federační server klienta. |
| code_challenge_method | Doporučené    | Metoda použitá `code_verifier` ke kódování `code_challenge` parametru. Může být `plain` jeden `S256`z nebo . Je-li `code_challenge` vyloučeno, předpokládá se, že je prostý text, pokud `code_challenge` je zahrnuta. Azure AAD v1.0 `plain` `S256`podporuje obě a . Další informace naleznete v [pkce RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | Doporučené    | Používá se k zabezpečení udělení autorizačního kódu prostřednictvím kódu Proof Key for Code Exchange (PKCE) od nativního nebo veřejného klienta. Povinné, `code_challenge_method` pokud je součástí dodávky. Další informace naleznete v [pkce RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Pokud je uživatel součástí organizace, může správce organizace jménem uživatele souhlasit nebo odmítnout nebo uživateli povolit souhlas. Uživatel má možnost souhlasu pouze tehdy, když to správce povolí.
>
>

V tomto okamžiku je uživatel požádán o zadání svých přihlašovacích údajů a souhlasu s oprávněními požadovanými aplikací na webu Azure Portal. Jakmile uživatel ověří a udělí souhlas, Azure AD `redirect_uri` odešle odpověď na vaši aplikaci na adresu v žádosti s kódem.

### <a name="successful-response"></a>Úspěšná odpověď
Úspěšná odpověď by mohla vypadat takto:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametr | Popis |
| --- | --- |
| admin_consent |Hodnota je True, pokud správce souhlasil s výzvou žádosti o souhlas. |
| kód |Autorizační kód, který aplikace požadovala. Aplikace může použít autorizační kód k vyžádání přístupového tokenu pro cílový prostředek. |
| session_state |Jedinečná hodnota, která identifikuje aktuální relaci uživatele. Tato hodnota je identifikátor GUID, ale by měl být považován za neprůhlednou hodnotu, která je předána bez vyšetření. |
| state |Pokud je parametr stavu zahrnut a požadavek, měla by se v odpovědi zobrazit stejná hodnota. Je vhodné pro aplikaci ověřit, že hodnoty stavu v požadavku a odpovědi jsou identické před použitím odpovědi. To pomáhá detekovat [útoky na server y forgery (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) proti klientovi. |

### <a name="error-response"></a>Odpověď na chybu
Chybové odpovědi mohou být `redirect_uri` také odeslány tak, aby aplikace může zpracovat odpovídajícím způsobem.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametr | Popis |
| --- | --- |
| error |Hodnota kódu chyby definovaná v části 5.2 [autorizačního rámce OAuth 2.0](https://tools.ietf.org/html/rfc6749). V další tabulce jsou popsány kódy chyb, které azure ad vrátí. |
| error_description |Podrobnější popis chyby. Tato zpráva není určena jako koncová uživatelská zpráva. |
| state |Hodnota stavu je náhodně vygenerovaná neopakovaně hodnota, která je odeslána v požadavku a vrácena v odpovědi, aby se zabránilo útokům padělání požadavků na příčce webu (CSRF). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Chybové kódy chyb pro chyby autorizačního koncového bodu
Následující tabulka popisuje různé kódy chyb, které `error` mohou být vráceny v parametru chybové odpovědi.

| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící požadovaný parametr. |Opravte a znovu odešlete požadavek. Toto je chyba vývoje a je obvykle zachycena během počátečního testování. |
| unauthorized_client |Klientská aplikace není oprávněna požadovat autorizační kód. |K tomu obvykle dochází, když klientská aplikace není registrovaná ve službě Azure AD nebo není přidána do klienta Azure AD uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |
| access_denied |Vlastník prostředku odepřel souhlas |Klientská aplikace může upozornit uživatele, že nemůže pokračovat, pokud uživatel nesouhlasí. |
| unsupported_response_type |Autorizační server nepodporuje typ odpovědi v požadavku. |Opravte a znovu odešlete požadavek. Toto je chyba vývoje a je obvykle zachycena během počátečního testování. |
| server_error |Na serveru došlo k neočekávané chybě. |Opakujte požadavek. Tyto chyby mohou být důsledkem dočasných podmínek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné chyby. |
| temporarily_unavailable |Server je dočasně příliš zaneprázdněn pro zpracování požadavku. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |
| invalid_resource |Cílový prostředek je neplatný, protože neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurován. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Použití autorizačního kódu k vyžádání přístupového tokenu
Nyní, když jste získali autorizační kód a uživatel vám udělil oprávnění, můžete kód pro přístupový token k `/token` požadovanému prostředku uplatnit odesláním požadavku POST do koncového bodu:

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
| Nájemce |Požadovaný |Hodnotu `{tenant}` v cestě požadavku lze použít k řízení, kdo se může přihlásit do aplikace. Povolené hodnoty jsou identifikátory klienta, `contoso.onmicrosoft.com` `common` například `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo tokeny nezávislé na tenantovi |
| client_id |Požadovaný |Id aplikace přiřazené k vaší aplikaci, když jste ji zaregistrovali ve službě Azure AD. Najdete to na webu Azure Portal. Id aplikace se zobrazí v nastavení registrace aplikace. |
| grant_type |Požadovaný |Musí `authorization_code` být pro tok autorizačního kódu. |
| kód |Požadovaný |Které `authorization_code` jste získali v předchozí části |
| redirect_uri |Požadovaný | Registrováno `redirect_uri`v klientské aplikaci. |
| client_secret |vyžadováno pro webové aplikace, není povoleno pro veřejné klienty |Tajný klíč aplikace, který jste vytvořili na portálu Azure Pro vaši aplikaci v části **Klíče**. Nelze jej použít v nativní aplikaci (veřejný klient), protože client_secrets nelze spolehlivě uložit na zařízeních. Je vyžadována pro webové aplikace a webová api (všichni důvěrní klienti), které mají možnost `client_secret` bezpečně ukládat na straně serveru. client_secret Před odesláním by měl být kódován adresa URL. |
| prostředek | Doporučené |Identifikátor URI ID aplikace cílového webového rozhraní API (zabezpečeného prostředku). Pokud chcete identifikátor URI ID aplikace najít, klikněte na webu Azure Portal na **Azure Active Directory**, klikněte na Registrace **aplikací**, otevřete stránku **Nastavení** aplikace a potom klikněte na **Vlastnosti**. Může se také jedná `https://graph.microsoft.com`o externí zdroj, jako je . To je vyžadováno v jednom z požadavků autorizace nebo tokenu. Chcete-li zajistit, aby se do žádosti o autorizaci umístilo méně dotazů na ověření, aby bylo zajištěno, že uživatel obdrží souhlas. Pokud v žádosti o autorizaci a požadavek tokenu, musí odpovídat parametry prostředku. | 
| code_verifier | optional | Stejný code_verifier, který byl použit k získání authorization_code. Povinné, pokud byla pkce použita v žádosti o udělení autorizačního kódu. Další informace naleznete v [pkce RFC](https://tools.ietf.org/html/rfc7636)   |

Pokud chcete identifikátor URI ID aplikace najít, klikněte na webu Azure Portal na **Azure Active Directory**, klikněte na Registrace **aplikací**, otevřete stránku **Nastavení** aplikace a potom klikněte na **Vlastnosti**.

### <a name="successful-response"></a>Úspěšná odpověď
Azure AD vrátí [přístupový token](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) na úspěšnou odpověď. Chcete-li minimalizovat síťová volání z klientské aplikace a jejich přidružené latence, klientská aplikace by měla ukládat do mezipaměti přístupové tokeny pro životnost tokenu, která je zadána v odpovědi OAuth 2.0. Chcete-li určit životnost tokenu, použijte hodnoty parametru `expires_in` nebo. `expires_on`

Pokud prostředek webového `invalid_token` rozhraní API vrátí kód chyby, může to znamenat, že prostředek určil, že platnost tokenu vypršela. Pokud jsou hodiny klienta a prostředku odlišné (označované jako "zkosení času"), může prostředek považovat token za ukončený před vymazáním tokenu z mezipaměti klienta. Pokud k tomu dojde, zrušte token z mezipaměti, i v případě, že je stále v rámci své vypočítané životnosti.

Úspěšná odpověď by mohla vypadat takto:

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
| access_token |Požadovaný přístupový token.  Toto je neprůhledný řetězec - závisí na tom, co prostředek očekává příjem a není určen pro klienta podívat. Aplikace můžete použít tento token k ověření zabezpečeného prostředku, jako je například webové rozhraní API. |
| token_type |Označuje hodnotu typu tokenu. Jediný typ, který podporuje Azure AD je Nosič. Další informace o žetonech nosičů naleznete v [tématu OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Jak dlouho je přístupový token platný (v sekundách). |
| expires_on |Čas, kdy vyprší platnost přístupového tokenu. Datum je reprezentováno jako počet sekund od 1970-01-01T0:0:0Z UTC až do doby vypršení platnosti. Tato hodnota se používá k určení životnosti tokenů uložených v mezipaměti. |
| prostředek |Identifikátor URI ID aplikace webového rozhraní API (zabezpečený prostředek). |
| scope |Oprávnění zosobnění udělená klientské aplikaci. Výchozí oprávnění `user_impersonation`je . Vlastník zabezpečeného prostředku můžete zaregistrovat další hodnoty ve službě Azure AD. |
| refresh_token |Obnovovací token OAuth 2.0. Aplikace můžete použít tento token získat další přístupové tokeny po vypršení platnosti aktuálního přístupového tokenu. Obnovovací tokeny jsou dlouhodobé a lze je použít k zachování přístupu k prostředkům po delší dobu. |
| id_token |Nepodepsaný webový token JSON (JWT) představující [token ID](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Aplikace může base64Url dekódovat segmenty tohoto tokenu požadovat informace o uživateli, který se přihlásil. Aplikace může ukládat hodnoty do mezipaměti a zobrazovat je, ale neměla by se na ně spoléhat pro žádné hranice autorizace nebo zabezpečení. |

Další informace o webových tokenech JSON naleznete v [návrhu specifikace JWT IETF](https://go.microsoft.com/fwlink/?LinkId=392344).   Další informace `id_tokens`naleznete v [tématu tok v1.0 OpenID Connect](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Odpověď na chybu
Chyby koncového bodu vystavování tokenu jsou kódy chyb HTTP, protože klient přímo volá koncový bod vystavování tokenu. Kromě stavového kódu HTTP koncový bod vystavování tokenu Azure AD také vrátí dokument JSON s objekty, které popisují chybu.

Ukázková odpověď na chybu může vypadat takto:

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
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která může vývojáři pomoci identifikovat hlavní příčinu chyby ověřování. |
| error_codes |Seznam kódů chyb specifických pro STS, které mohou pomoci při diagnostice. |
| časové razítko |Čas, kdy došlo k chybě. |
| trace_id |Jedinečný identifikátor požadavku, který může pomoci při diagnostice. |
| correlation_id |Jedinečný identifikátor požadavku, který může pomoci při diagnostice napříč součástmi. |

#### <a name="http-status-codes"></a>Stavové kódy HTTP
V následující tabulce jsou uvedeny stavové kódy HTTP, které koncový bod vystavování tokenu vrátí. V některých případech je kód chyby dostatečný k popisu odpovědi, ale pokud existují chyby, je třeba analyzovat doprovodný dokument JSON a zkontrolovat jeho kód chyby.

| Kód HTTP | Popis |
| --- | --- |
| 400 |Výchozí kód HTTP. Používá se ve většině případů a je obvykle z důvodu poškozený požadavek. Opravte a znovu odešlete požadavek. |
| 401 |Ověření se nezdařilo. Například požadavek chybí parametr client_secret. |
| 403 |Autorizace se nezdařila. Uživatel například nemá oprávnění k přístupu k prostředku. |
| 500 |Ve službě došlo k vnitřní chybě. Opakujte požadavek. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Kódy chyb pro chyby koncového bodu tokenu
| Kód chyby | Popis | Akce klienta |
| --- | --- | --- |
| invalid_request |Chyba protokolu, například chybějící požadovaný parametr. |Oprava a opětovné odeslání žádosti |
| invalid_grant |Autorizační kód je neplatný nebo vypršela jeho platnost. |Zkuste nový požadavek `/authorize` na koncový bod |
| unauthorized_client |Ověřený klient není oprávněn používat tento typ udělení autorizace. |K tomu obvykle dochází, když klientská aplikace není registrovaná ve službě Azure AD nebo není přidána do klienta Azure AD uživatele. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |
| invalid_client |Ověření klienta se nezdařilo. |Pověření klienta nejsou platná. Chcete-li opravit, správce aplikace aktualizuje pověření. |
| unsupported_grant_type |Autorizační server nepodporuje typ udělení autorizace. |Změňte typ grantu v žádosti. K tomuto typu chyby by mělo dojít pouze během vývoje a být zjištěna během počátečního testování. |
| invalid_resource |Cílový prostředek je neplatný, protože neexistuje, Azure AD ho nemůže najít nebo není správně nakonfigurován. |To znamená, že prostředek, pokud existuje, nebyl nakonfigurován v tenantovi. Aplikace může vyzvat uživatele s pokyny pro instalaci aplikace a její přidání do Služby Azure AD. |
| interaction_required |Požadavek vyžaduje interakci uživatele. Je například vyžadován další krok ověřování. | Místo neinteraktivního požadavku opakujte akci s interaktivní žádostí o autorizaci pro stejný prostředek. |
| temporarily_unavailable |Server je dočasně příliš zaneprázdněn pro zpracování požadavku. |Opakujte požadavek. Klientská aplikace může vysvětlit uživateli, že jeho odpověď je zpožděna z důvodu dočasné podmínky. |

## <a name="use-the-access-token-to-access-the-resource"></a>Použití přístupového tokenu pro přístup k prostředku
Nyní, když jste úspěšně `access_token`získali , můžete použít token v požadavcích na webová api, jeho zahrnutím do `Authorization` záhlaví. Specifikace [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) vysvětluje, jak používat nosné tokeny v požadavcích HTTP pro přístup k chráněným prostředkům.

### <a name="sample-request"></a>Požadavek na ukázku
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Odpověď na chybu
Zabezpečené prostředky, které implementují rfc 6750 vydávat kódy stavu HTTP. Pokud požadavek neobsahuje ověřovací pověření nebo chybí token, `WWW-Authenticate` odpověď obsahuje záhlaví. Pokud se požadavek nezdaří, server prostředků odpoví stavovým kódem HTTP a kódem chyby.

Následuje příklad neúspěšné odpovědi, pokud požadavek klienta neobsahuje token nosiče:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parametry chyby
| Parametr | Popis |
| --- | --- |
| authorization_uri |Identifikátor URI (fyzický koncový bod) autorizačního serveru. Tato hodnota se také používá jako vyhledávací klíč získat další informace o serveru z koncového bodu zjišťování. <p><p> Klient musí ověřit, zda je autorizační server důvěryhodný. Když je prostředek chráněný službou Azure AD, stačí ověřit, `https://login.microsoftonline.com` že adresa URL začíná nebo jiným názvem hostitele, který podporuje Azure AD. Prostředek specifický pro klienta by měl vždy vrátit identifikátor URI autorizace specifický pro klienta. |
| error |Hodnota kódu chyby definovaná v části 5.2 [autorizačního rámce OAuth 2.0](https://tools.ietf.org/html/rfc6749). |
| error_description |Podrobnější popis chyby. Tato zpráva není určena jako koncová uživatelská zpráva. |
| resource_id |Vrátí jedinečný identifikátor prostředku. Klientská aplikace můžete použít tento identifikátor `resource` jako hodnotu parametru, když požaduje token pro prostředek. <p><p> Je důležité, aby klientská aplikace tuto hodnotu ověřila, jinak by škodlivá služba mohla vyvolat útok **zvýšení oprávnění.** <p><p> Doporučená strategie pro zabránění útoku `resource_id` je ověřit, že odpovídá základu webové adresy URL rozhraní API, které jsou přístupné. Například pokud `https://service.contoso.com/data` je přístupná, `resource_id` může `https://service.contoso.com/`být . Klientská aplikace musí `resource_id` odmítnout, která nezačíná základní adresou URL, pokud neexistuje spolehlivý alternativní způsob ověření id. |

#### <a name="bearer-scheme-error-codes"></a>Chybové kódy schématu nosiče
Specifikace RFC 6750 definuje následující chyby pro prostředky, které v odpovědi používají hlavičku WWW-Authenticate a schéma nosiče.

| Stavový kód HTTP | Kód chyby | Popis | Akce klienta |
| --- | --- | --- | --- |
| 400 |invalid_request |Požadavek není ve správném formátu. Může například chybět parametr nebo použít stejný parametr dvakrát. |Opravte chybu a opakujte požadavek. K tomuto typu chyby by mělo dojít pouze během vývoje a být zjištěny v počátečním testování. |
| 401 |invalid_token |Přístupový token chybí, je neplatný nebo je odvolán. Hodnota parametru error_description poskytuje další podrobnosti. |Požádejte o nový token z autorizačního serveru. Pokud se nový token nezdaří, došlo k neočekávané chybě. Odešlete uživateli chybovou zprávu a opakujte akci po náhodných zpožděních. |
| 403 |insufficient_scope |Přístupový token neobsahuje oprávnění zosobnění potřebná pro přístup k prostředku. |Odešlete nový požadavek na autorizaci do koncového bodu autorizace. Pokud odpověď obsahuje parametr oboru, použijte hodnotu oboru v požadavku na prostředek. |
| 403 |insufficient_access |Předmět tokenu nemá oprávnění, která jsou vyžadována pro přístup k prostředku. |Vyzvat uživatele k použití jiného účtu nebo k vyžádání oprávnění k určenému prostředku. |

## <a name="refreshing-the-access-tokens"></a>Aktualizace přístupových tokenů

Přístupové tokeny jsou krátkodobé a musí být aktualizovány po vypršení jejich platnosti pokračovat v přístupu k prostředkům. Můžete aktualizovat `access_token` odesláním jiného `POST` požadavku `/token` do koncového bodu, `refresh_token` ale tentokrát `code`poskytuje místo .  Obnovovací tokeny jsou platné pro všechny prostředky, ke kterým již váš klient získal souhlas `resource=https://graph.microsoft.com` s přístupem – proto lze `resource=https://contoso.com/api`aktualizovat token vydaný na žádost o aplikaci pro aplikaci . 

Obnovovací tokeny nemají zadané doby životnosti. Obvykle životnost impoziumů obnovovacítokeny jsou obvykle poměrně dlouhé. V některých případech však platnost tokenů aktualizace vyprší, jsou odvolány nebo postrádají dostatečná oprávnění pro požadovanou akci. Vaše aplikace musí očekávat a zpracovávat chyby vrácené koncovým bodem vystavování tokenu správně.

Když obdržíte odpověď s chybou obnovovacího tokenu, zahoďte aktuální obnovovací token a požádejte o nový autorizační kód nebo přístupový token. Zejména při použití obnovovacího tokenu v toku udělení autorizačního kódu, pokud obdržíte odpověď s kódy chyb `interaction_required` nebo, `invalid_grant` zahoďte obnovovací token a požádejte o nový autorizační kód.

Ukázkový požadavek na koncový bod **specifický pro klienta** (můžete také použít **společný** koncový bod) k získání nového přístupového tokenu pomocí obnovovacího tokenu vypadá takto:

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
| token_type |Typ tokenu. Jedinou podporovanou hodnotou je **nosič**. |
| expires_in |Zbývající životnost tokenu v sekundách. Typická hodnota je 3600 (jedna hodina). |
| expires_on |Datum a čas, kdy vyprší platnost tokenu. Datum je reprezentováno jako počet sekund od 1970-01-01T0:0:0Z UTC až do doby vypršení platnosti. |
| prostředek |Identifikuje zabezpečený prostředek, ke kterému lze získat přístupový token. |
| scope |Oprávnění zosobnění udělená nativní klientské aplikaci. Výchozí oprávnění je **user_impersonation**. Vlastník cílového prostředku můžete zaregistrovat alternativní hodnoty ve službě Azure AD. |
| access_token |Nový přístupový token, který byl požadován. |
| refresh_token |Nový OAuth 2.0 refresh_token, který lze použít k vyžádání nových přístupových tokenů, když vyprší platnost v této odpovědi. |

### <a name="error-response"></a>Odpověď na chybu
Ukázková odpověď na chybu může vypadat takto:

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
| error |Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| error_description |Konkrétní chybová zpráva, která může vývojáři pomoci identifikovat hlavní příčinu chyby ověřování. |
| error_codes |Seznam kódů chyb specifických pro STS, které mohou pomoci při diagnostice. |
| časové razítko |Čas, kdy došlo k chybě. |
| trace_id |Jedinečný identifikátor požadavku, který může pomoci při diagnostice. |
| correlation_id |Jedinečný identifikátor požadavku, který může pomoci při diagnostice napříč součástmi. |

Popis kódů chyb a doporučenou akci klienta naleznete v [tématu Chybové kódy pro chyby koncového bodu tokenu](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Další kroky
Další informace o koncovém bodu Azure AD v1.0 a o tom, jak přidat ověřování a autorizaci do webových aplikací a webových api, najdete v [tématu ukázkové aplikace](sample-v1-code.md).

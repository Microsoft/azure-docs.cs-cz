---
title: Obory platformy Microsoft identity, oprávnění & souhlasu
description: Přečtěte si o autorizaci na koncovém bodu Microsoft Identity Platform, včetně oborů, oprávnění a souhlasu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 570314bcaedb86cc593846ffc1d6846d1d2fe335
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090183"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Oprávnění a souhlas na platformě Microsoft identity

Aplikace, které se integrují s platformou Microsoft identity, se řídí autorizačním modelem, který poskytuje uživatelům a správcům kontrolu nad tím, jak se data dají získat. Implementace autorizačního modelu byla aktualizována na platformě Microsoft identity. Tím se změní způsob, jakým aplikace musí spolupracovat s platformou Microsoft identity. Tento článek se zabývá základními koncepty tohoto autorizačního modelu, včetně oborů, oprávnění a souhlasu.

## <a name="scopes-and-permissions"></a>Obory a oprávnění

Platforma Microsoft Identity implementuje autorizační protokol [OAuth 2,0](active-directory-v2-protocols.md) . OAuth 2,0 je metoda, přes kterou má aplikace třetí strany přístup k prostředkům hostovaným na webu jménem uživatele. Jakýkoli prostředek hostovaný na webu, který se integruje s platformou Microsoft identity, má identifikátor prostředku nebo identifikátor *URI ID aplikace*. 

Tady je několik příkladů prostředků hostovaných na webu Microsoftu:

* Microsoft Graph: `https://graph.microsoft.com`
* Rozhraní API pro Microsoft 365 poštu: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Totéž platí pro všechny prostředky třetích stran, které jsou integrované s platformou Microsoft identity. Kterýkoli z těchto prostředků taky může definovat sadu oprávnění, která se dají použít k rozdělení funkcí tohoto prostředku do menších bloků dat. Například [Microsoft Graph](https://graph.microsoft.com) má definovaná oprávnění k provádění následujících úloh mimo jiné:

* Čtení kalendáře uživatele
* Zápis do kalendáře uživatele
* Odeslat e-mail jako uživatel

Vzhledem k těmto typům definicí oprávnění má prostředek jemně odstupňovanou kontrolu nad jeho daty a způsob zpřístupnění funkcí rozhraní API. Aplikace třetí strany může požádat o tato oprávnění od uživatelů a správců, kteří musí žádost schválit, aby mohla aplikace přistupovat k datům nebo jednat jménem uživatele. 

Když je funkce prostředku narozdělená do malých sad oprávnění, dají se aplikace třetích stran sestavit tak, aby požadovaly jenom ta oprávnění, která potřebují k provádění svých funkcí. Uživatelé a správci můžou zjistit, k jakým datům může aplikace přistupovat. A můžou být větší jistotu, že se aplikace nechová se škodlivým záměrem. Vývojáři by měli vždy dodržovat princip nejnižších oprávnění a vyžadovat pouze oprávnění, která potřebují k fungování svých aplikací.

V případě OAuth 2,0 se tyto typy sad oprávnění nazývají *obory*. Jsou také často označována jako *oprávnění*. Na platformě Microsoft identity je oprávnění reprezentované jako řetězcová hodnota. V příkladu Microsoft Graph tady je hodnota řetězce pro každé oprávnění:

* Čtení kalendáře uživatele pomocí `Calendars.Read`
* Zápis do kalendáře uživatele pomocí `Calendars.ReadWrite`
* Odeslat e-mail jako uživatel pomocí `Mail.Send`

Aplikace tyto oprávnění nejčastěji vyžádá zadáním oborů v požadavcích na koncový bod autorizace platformy Microsoft Identity Platform. Některá oprávnění s vysokou úrovní oprávnění je ale možné udělit jenom prostřednictvím souhlasu správce. Mohou být vyžádány nebo uděleny pomocí [koncového bodu souhlasu správce](#admin-restricted-permissions). Pokračujte ve čtení a získejte další informace.

## <a name="permission-types"></a>Typy oprávnění

Platforma Microsoft identity podporuje dva typy oprávnění: *delegovaná oprávnění* a *oprávnění aplikací*.

* **Delegovaná oprávnění** se používají v aplikacích, které mají přihlášeného uživatele k dispozici. Pro tyto aplikace buď uživatel nebo správce souhlasí s oprávněními, která aplikace požaduje. Aplikace je delegována oprávnění, aby fungovala jako přihlášený uživatel při volání cílového prostředku. 

    Některá delegovaná oprávnění můžou být odsouhlasená nesprávci. Některá oprávnění s vysokou úrovní oprávnění ale vyžadují [souhlas správce](#admin-restricted-permissions). Informace o tom, které role správce můžou souhlasit s delegovanými oprávněními, najdete v tématu [oprávnění role správce v Azure Active Directory (Azure AD)](../roles/permissions-reference.md).

* **Oprávnění aplikací** se používají v aplikacích, které se spouštějí bez přihlášeného uživatele, například aplikací, které běží jako služby nebo procesy na pozadí. Pouze [Správce může udělit souhlas s](#requesting-consent-for-an-entire-tenant) oprávněními aplikace.

_Skutečná oprávnění_ jsou oprávnění, která má vaše aplikace při zpracování požadavků na cílový prostředek. Je důležité porozumět rozdílu mezi delegovanými oprávněními a oprávněními aplikace, které vaše aplikace uděluje, a efektivními oprávněními, ke kterým je aplikace udělena, když volá cílový prostředek.

- V případě delegovaných oprávnění jsou _efektivní oprávnění_ vaší aplikace průsečíkem delegovaných oprávnění, kterým byla aplikace udělena (odsouhlasená), a oprávnění aktuálně přihlášeného uživatele. Aplikace nemůže mít nikdy více oprávnění než přihlášený uživatel. 

   V rámci organizací může být oprávnění přihlášeného uživatele určena zásadami nebo členstvím v jedné nebo více rolích správce. Informace o tom, které role správce můžou souhlasit s delegovanými oprávněními, najdete v tématu [oprávnění role správce ve službě Azure AD](../roles/permissions-reference.md).

   Předpokládejme například, že vaše aplikace získala oprávnění User. prohledatelné _. všechna_ delegovaná oprávnění. Toto oprávnění vaší aplikaci výslovně uděluje oprávnění ke čtení a aktualizaci profilu každého uživatele v organizaci. Pokud je přihlášený uživatel globálním správcem, může vaše aplikace aktualizovat profil každého uživatele v organizaci. Pokud ale přihlášený uživatel nemá roli správce, může aplikace aktualizovat jenom profil přihlášeného uživatele. Nedokáže aktualizovat profily jiných uživatelů v organizaci, protože uživatel, ke kterému má oprávnění jednat jménem, nemá tato oprávnění.

- Pro oprávnění aplikací jsou _skutečná oprávnění_ vaší aplikace plná úroveň oprávnění, která jsou odvozena oprávněním. Například aplikace s uživatelským rozhraním. pro čtení _. všechna_ oprávnění aplikace mohou aktualizovat profil každého uživatele v organizaci.

## <a name="openid-connect-scopes"></a>Obory OpenID Connect

Implementace Microsoft Identity Platform OpenID Connect má několik dobře definovaných oborů, které jsou také hostovány na Microsoft Graph: `openid` ,, a `email` `profile` `offline_access` . `address` `phone` Obory a OpenID Connect nejsou podporovány.

Pokud požadujete obory OpenID Connect a token, získáte token pro volání [koncového bodu UserInfo](userinfo.md).

### <a name="openid"></a>OpenID

Pokud se aplikace přihlásí pomocí [OpenID Connect](active-directory-v2-protocols.md), musí požádat o `openid` obor. `openid`Obor se zobrazí na stránce pro vyjádření souhlasu pracovního účtu jako **přihlašovací** oprávnění. Na stránce osobní účet Microsoft souhlasu se zobrazí jako **zobrazení vašeho profilu a připojení k aplikacím a službám pomocí oprávnění účet Microsoft** . 

Pomocí tohoto oprávnění může aplikace získat jedinečný identifikátor uživatele ve formě `sub` deklarace identity. Oprávnění také dává aplikaci přístup ke koncovému bodu UserInfo. `openid`Obor lze použít na koncovém bodu tokenu platformy Microsoft identity a získat tokeny ID. Aplikace může tyto tokeny použít k ověřování.

### <a name="email"></a>e-mail

`email`Obor lze použít s `openid` oborem a dalšími obory. Umožňuje aplikaci přístup k primární e-mailové adrese uživatele ve formě `email` deklarace identity. 

`email`Deklarace identity je obsažená v tokenu, pouze pokud je k uživatelskému účtu přidružena e-mailová adresa, která není vždy v případě případu. Pokud aplikace používá `email` rozsah, aplikace musí být schopná zpracovat případ, ve kterém `email` v tokenu neexistuje žádná deklarace identity.

### <a name="profile"></a>profil

`profile`Obor lze použít s `openid` oborem a jakýmkoli jiným oborem. Poskytuje aplikaci přístup k velkým množství informací o uživateli. K informacím, ke kterým může přistupovat, patří, ale nejsou omezené na jméno, příjmení, upřednostňované uživatelské jméno a ID objektu uživatele. 

Úplný seznam `profile` deklarací dostupných v `id_tokens` parametru pro konkrétního uživatele naleznete v [ `id_tokens` odkazu](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` Rozsah](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) poskytuje aplikaci přístup k prostředkům jménem uživatele po delší dobu. Na stránce s vyjádřením souhlasu se tento obor zobrazí jako **Správa přístupu k datům, ke kterým jste udělili** oprávnění k přístupu. 

Když uživatel `offline_access` obor schválí, vaše aplikace může přijímat aktualizační tokeny z koncového bodu tokenu platformy Microsoft identity. Aktualizační tokeny jsou dlouhodobě dlouhodobé. Vaše aplikace může získat nové přístupové tokeny, jejichž platnost brzy vyprší.

> [!NOTE]
> Toto oprávnění se aktuálně zobrazuje na všech stránkách souhlasu, a to i u toků, které neposkytují obnovovací token (například [implicitní tok](v2-oauth2-implicit-grant-flow.md)). Tato instalace řeší scénáře, ve kterých může klient začít v rámci implicitního toku a pak přejít na tok kódu, kde je očekáván obnovovací token.

Na platformě Microsoft identity (požadavky odeslané na koncový bod v 2.0) musí vaše aplikace explicitně požádat o `offline_access` obor, aby přijímala obnovovací tokeny. Takže když uplatníte autorizační kód v [toku autorizačního kódu OAuth 2,0](active-directory-v2-protocols.md), dostanete jenom přístupový token z `/token` koncového bodu. 

Přístupový token je po krátkou dobu platný. Obvykle vyprší za jednu hodinu. V tomto okamžiku potřebuje vaše aplikace přesměrovat uživatele zpátky na `/authorize` koncový bod, aby získal nový autorizační kód. V závislosti na typu aplikace může uživatel po tomto přesměrování znovu zadat své přihlašovací údaje nebo odsouhlasit oprávnění.

Další informace o tom, jak získat a použít obnovovací tokeny, najdete v referenčních informacích k [protokolu platformy Microsoft Identity Platform](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Žádost o souhlas jednotlivého uživatele

V žádosti o autorizaci [OpenID Connect nebo OAuth 2,0](active-directory-v2-protocols.md) může aplikace požádat o oprávnění, která potřebuje, pomocí `scope` parametru dotazu. Když se například uživatel přihlásí k aplikaci, aplikace pošle požadavek podobný následujícímu příkladu. (Pro čitelnost se přidaly zalomení řádků.)

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope`Parametr je seznam oddělený mezerami delegovaná oprávnění, která aplikace požaduje. Každé oprávnění je určeno připojením hodnoty oprávnění k identifikátoru prostředku (identifikátor URI ID aplikace). V příkladu žádosti potřebuje aplikace oprávnění ke čtení kalendáře uživatele a odeslání pošty jako uživatel.

Jakmile uživatel zadá své přihlašovací údaje, platforma identity Microsoftu zkontroluje odpovídající záznam *souhlasu uživatele*. Pokud uživatel nesouhlasí s některým z požadovaných oprávnění v minulosti a pokud správce nesouhlasí s těmito oprávněními jménem celé organizace, Microsoft Identity platforme vyzve uživatele, aby udělil požadovaná oprávnění.

V tuto chvíli jsou oprávnění `offline_access` ("udržovat přístup k datům, ke kterým jste udělili přístup k oprávněním) a `user.read` (" přihlašovat a číst váš profil ") automaticky zahrnutá do původního souhlasu aplikace.  Tato oprávnění se obecně vyžadují pro správné fungování aplikací. `offline_access`Oprávnění dává aplikaci přístup k aktualizaci tokenů, které jsou důležité pro nativní aplikace a webové aplikace. `user.read`Oprávnění poskytuje přístup k `sub` deklaraci identity. Umožňuje klientovi nebo aplikaci správně identifikovat uživatele v čase a přistupovat k základní informacím o uživateli.

![Ukázkový snímek obrazovky, který zobrazuje souhlas pracovního účtu.](./media/v2-permissions-and-consent/work_account_consent.png)

Pokud uživatel žádost o oprávnění schválí, je zaznamenán souhlas. Uživatel se nemusí později po přihlášení k aplikaci odsouhlasit.

## <a name="requesting-consent-for-an-entire-tenant"></a>Žádost o souhlas pro celého tenanta

Když organizace koupí licenci nebo předplatného pro aplikaci, organizace často chce aktivně nastavit aplikaci tak, aby ji mohli používat všichni členové organizace. V rámci tohoto procesu může správce udělit souhlas, že aplikace bude jednat jménem libovolného uživatele v tenantovi. Pokud správce udělí souhlas pro celého tenanta, uživatelům organizace se nezobrazí stránka pro souhlas aplikace.

Aby bylo možné požádat o souhlas s delegovanými oprávněními pro všechny uživatele v tenantovi, může vaše aplikace používat koncový bod souhlasu správce.

Navíc musí aplikace požádat o oprávnění aplikace pomocí koncového bodu souhlasu správce.

## <a name="admin-restricted-permissions"></a>Omezená oprávnění správce

Některá oprávnění s vysokým oprávněním v prostředcích Microsoftu je možné nastavit na hodnotu *omezené správcem*. Tady je několik příkladů těchto typů oprávnění:

* Čtení úplných profilů všech uživatelů pomocí `User.Read.All`
* Zápis dat do adresáře organizace pomocí `Directory.ReadWrite.All`
* Čtení všech skupin v adresáři organizace pomocí `Groups.Read.All`

I když uživatel příjemce může udělit aplikaci přístup k tomuto druhu dat, uživatelé organizace nemůžou udělit přístup ke stejné sadě citlivých firemních dat. Pokud vaše aplikace požaduje přístup k některé z těchto oprávnění od uživatele organizace, obdrží chybová zpráva s oznámením, že nemají oprávnění k souhlasu s oprávněními vaší aplikace.

Pokud vaše aplikace vyžaduje obor pro oprávnění omezená správcem, správce organizace musí tyto obory vyjádřit jménem uživatelů organizace. Abyste se vyhnuli zobrazování výzev uživatelům, kteří si žádají o souhlas s oprávněním, která nemůžou udělit, může vaše aplikace používat koncový bod souhlasu správce. Koncový bod souhlasu správce je popsaný v následující části.

Pokud aplikace požaduje delegovaná oprávnění s vysokou úrovní oprávnění a správce udělí tato oprávnění prostřednictvím koncového bodu souhlasu správce, je udělen souhlas pro všechny uživatele v tenantovi.

Pokud aplikace požaduje oprávnění aplikace a správce udělí tato oprávnění prostřednictvím koncového bodu souhlasu správce, není tato podpora prováděna jménem konkrétního uživatele. Místo toho je klientským aplikacím uděleno oprávnění *přímo*. Tyto typy oprávnění jsou používány pouze službami démon a jinými neinteraktivními aplikacemi, které běží na pozadí.

## <a name="using-the-admin-consent-endpoint"></a>Použití koncového bodu souhlasu správce

Po použití koncového bodu souhlasu správce k udělení souhlasu správce jste hotovi. Uživatelé nemusejí provádět žádné další akce. Po udělení souhlasu správce můžou uživatelé získat přístupový token prostřednictvím typického toku ověřování. Výsledný přístupový token má odsouhlasená oprávnění.

Když globální správce používá vaši aplikaci a přesměruje se na koncový bod autorizace, detekuje platforma Microsoftu roli uživatele. Žádá se o to, že globální správce chce pro požadovaná oprávnění souhlasit jménem celého tenanta. Místo toho můžete pomocí vyhrazeného koncového bodu souhlasu správce proaktivně požádat správce o udělení oprávnění jménem celého tenanta. Tento koncový bod je také nutný pro vyžádání oprávnění aplikace. Oprávnění aplikace nelze požadovat pomocí koncového bodu autorizace.

Pokud budete postupovat podle těchto kroků, může vaše aplikace požádat o oprávnění pro všechny uživatele v tenantovi, včetně rozsahů s omezeným oprávněním správce. Tato operace má vysoké oprávnění. Operaci použijte pouze v případě potřeby pro váš scénář.

Ukázku kódu, který implementuje postup, najdete v [ukázce obory s omezením pro správu](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) na GitHubu.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Vyžádání oprávnění na portálu pro registraci aplikací

V portálu pro registraci aplikací můžou aplikace vypisovat požadovaná oprávnění, včetně delegovaných oprávnění i oprávnění aplikace. Tato instalace umožňuje použití `/.default` oboru a možnosti **souhlasu správce udělování** Azure Portal.  

Obecně platí, že oprávnění by měla být staticky definována pro danou aplikaci. Měly by být nadmnožinou oprávnění, která bude aplikace požadovat dynamicky nebo přírůstkově.

> [!NOTE]
>Oprávnění aplikace lze požadovat pouze pomocí [`/.default`](#the-default-scope) . Takže pokud vaše aplikace potřebuje oprávnění aplikace, ujistěte se, že jsou uvedena na portálu pro registraci aplikací.

Konfigurace seznamu staticky vyžádaného oprávnění pro aplikaci:

1. V prostředí rychlý Start pro <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure Portal registrace aplikací <span class="docon docon-navigate-external x-hidden-focus"></span> </a> můžete přejít do své aplikace.
1. Vyberte aplikaci, nebo pokud jste to ještě neudělali, [vytvořte aplikaci](quickstart-register-app.md) .
1. Na stránce **Přehled** aplikace v části **Spravovat** vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**.
1. V seznamu dostupných rozhraní API vyberte **Microsoft Graph** . Pak přidejte oprávnění, která vaše aplikace vyžaduje.
1. Vyberte **Přidat oprávnění**.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Doporučeno: podepsat uživatele do aplikace

Při vytváření aplikace, která používá koncový bod souhlasu správce, obvykle aplikace potřebuje stránku nebo zobrazení, ve kterém může správce schválit oprávnění aplikace. Tato stránka může být:

* Součást toku registrace aplikace.
* Součást nastavení aplikace
* Vyhrazený tok "Connect". 

V mnoha případech má aplikace smysl zobrazit toto zobrazení "připojit" až po přihlášení uživatele pomocí pracovní účet Microsoft nebo školní účet Microsoft.

Když uživatele podepíšete do vaší aplikace, můžete určit organizaci, ke které správce patří, a potom požádat o schválení potřebných oprávnění. I když tento krok není nezbytně nezbytný, může vám usnadnit vytváření intuitivnějšího prostředí pro uživatele vaší organizace. 

Pokud chcete uživatele podepsat, postupujte podle [kurzů Microsoft Identity Platform Protocol](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Vyžádání oprávnění od správce adresáře

Až budete připraveni požádat o oprávnění od správce vaší organizace, můžete uživatele přesměrovat na koncový bod souhlasu správce Microsoft Identity Platform.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parametr        | Podmínka        | Popis                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Povinné | Tenant adresáře, ze kterého chcete požádat o oprávnění. Dá se zadat ve formátu GUID nebo popisného názvu. Nebo na ni lze obecně odkazovat pomocí organizací, jak je vidět v příkladu. Nepoužívejte "Common", protože osobní účty nemůžou poskytovat souhlas správce, s výjimkou kontextu tenanta. Aby byla zajištěna nejlepší kompatibilita s osobními účty, které spravují klienty, použijte ID tenanta, pokud je to možné. |
| `client_id` | Vyžadováno | ID aplikace (klienta), které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `redirect_uri` | Vyžadováno |Identifikátor URI přesměrování, kde má být odeslána odpověď pro zpracování vaší aplikace. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu pro registraci aplikací. |
| `state` | Doporučeno | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Použijte stav ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
|`scope`        | Vyžadováno        | Definuje sadu oprávnění, kterou aplikace požaduje. Rozsahy můžou být statické (using [`/.default`](#the-default-scope) ) nebo dynamické.  Tato sada může zahrnovat obory OpenID Connect ( `openid` , `profile` , `email` ). Pokud potřebujete oprávnění aplikace, musíte použít `/.default` k vyžádání staticky nakonfigurovaného seznamu oprávnění.  |


V tomto okamžiku Azure AD vyžaduje, aby se k dokončení žádosti přihlásil správce tenanta. Správce se vyzve ke schválení všech oprávnění, která jste si vyžádali v `scope` parametru.  Pokud jste použili statickou hodnotu ( `/.default` ), bude fungovat jako koncový bod souhlasu správce v 1.0 a souhlas se žádostí pro všechny obory nalezené v požadovaných oprávněních pro danou aplikaci.

#### <a name="successful-response"></a>Úspěšná odpověď

Pokud správce schválí oprávnění pro vaši aplikaci, úspěšná odpověď vypadá takto:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- |
| `tenant` | Tenant adresáře, který vaší aplikaci udělil požadovaná oprávnění, ve formátu identifikátoru GUID. |
| `state` | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| `admin_consent` | Bude nastaveno na `True` . |

#### <a name="error-response"></a>Chybová odezva

Pokud správce neschválí oprávnění pro vaši aplikaci, neúspěšná odpověď vypadá takto:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým došlo. Dá se použít i k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojářům pomáhat určit hlavní příčinu chyby. |

Jakmile obdržíte úspěšnou odpověď z koncového bodu souhlasu správce, vaše aplikace získala požadovaná oprávnění. V dalším kroku si můžete vyžádat token pro prostředek, který chcete.

## <a name="using-permissions"></a>Používání oprávnění

Poté, co uživatel souhlasí s oprávněním pro vaši aplikaci, může vaše aplikace získat přístupové tokeny, které reprezentují oprávnění aplikace pro přístup k prostředku v určité kapacitě. Přístupový token se dá použít jenom pro jeden prostředek. Ale kódovaný v rámci přístupového tokenu je každé oprávnění, které vaší aplikaci bylo uděleno pro daný prostředek. K získání přístupového tokenu může vaše aplikace vytvořit požadavek na koncový bod tokenu platformy Microsoft Identity Platform, například:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

V požadavcích HTTP na prostředek můžete použít výsledný přístupový token. Spolehlivě indikuje prostředek, který má vaše aplikace správné oprávnění provést konkrétní úlohu.

Další informace o protokolu OAuth 2,0 a o tom, jak získat přístupové tokeny, najdete v referenčních informacích k [protokolu služby Microsoft Identity Platform Endpoint](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Rozsah/.Default

Obor můžete použít `/.default` k usnadnění migrace vašich aplikací z koncového bodu v 1.0 na koncový bod Microsoft Identity Platform. `/.default`Obor je integrovaný pro každou aplikaci, která odkazuje na statický seznam oprávnění nakonfigurovaných v registraci aplikace. 

`scope`Hodnota `https://graph.microsoft.com/.default` je funkčně stejná jako `resource=https://graph.microsoft.com` u koncového bodu v 1.0. Když v žádosti zadáte `https://graph.microsoft.com/.default` obor, aplikace požaduje přístupový token, který obsahuje obory pro každé Microsoft Graph oprávnění, které jste vybrali pro aplikaci na portálu pro registraci aplikací. Rozsah je vytvořen pomocí identifikátoru URI prostředku a `/.default` . Takže pokud je identifikátor URI prostředku `https://contosoApp.com` , je požadovaný rozsah `https://contosoApp.com/.default` .  V případech, kdy je nutné pro správné vyžádání tokenu použít druhé lomítko, přečtěte si [část o koncových lomítek](#trailing-slash-and-default).

`/.default`Obor lze použít v jakémkoli toku OAuth 2,0. Je to ale nezbytné v toku [toku](v2-oauth2-on-behalf-of-flow.md) a [přihlašovacích údajů klienta](v2-oauth2-client-creds-grant-flow.md). Budete ho potřebovat také při použití koncového bodu souhlasu správce v2 k vyžádání oprávnění aplikace.

Klienti nemůžou kombinovat statický ( `/.default` ) souhlas a dynamické svolení v jednom požadavku. Výsledkem je, že dojde `scope=https://graph.microsoft.com/.default+mail.read` k chybě, protože kombinuje typy oborů.

### <a name="default-and-consent"></a>/.Default a souhlas

`/.default`Obor spouští také chování koncového bodu v 1.0 `prompt=consent` . Žádá o souhlas pro všechna oprávnění, která aplikace zaregistruje, bez ohledu na prostředek. Pokud je součástí žádosti, `/.default` obor vrátí token, který obsahuje obory požadovaného prostředku.

### <a name="default-when-the-user-has-already-given-consent"></a>/.Default, pokud už uživatel udělil souhlas.

`/.default`Obor je funkčně shodný s chováním `resource` koncového bodu v 1.0 orientovaném na verzi 1.0. Přináší také chování souhlasu koncového bodu verze 1.0. To znamená, že `/.default` vyvolá výzvu k vyjádření souhlasu pouze v případě, že uživatel neudělil oprávnění mezi klientem a prostředkem. 

Pokud existuje nějaký takový souhlas, vrácený token obsahuje všechny obory, které uživatel udělil pro daný prostředek. Pokud však nebylo uděleno žádné oprávnění nebo byl `prompt=consent` zadán parametr, zobrazí se výzva k zadání souhlasu pro všechny obory, které klientská aplikace zaregistrovala.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Příklad 1: uživatel nebo správce tenanta má udělená oprávnění.

V tomto příkladu udělil uživatel nebo správce tenanta `mail.read` `user.read` oprávnění Microsoft Graph a k klientovi. 

Pokud klient požaduje `scope=https://graph.microsoft.com/.default` , nezobrazí se žádná výzva k vyjádření souhlasu bez ohledu na obsah registrovaných oprávnění klientské aplikace pro Microsoft Graph. Vrácený token obsahuje obory `mail.read` a `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Příklad 2: uživatel nemá udělená oprávnění mezi klientem a prostředkem.

V tomto příkladu uživatel neudělil souhlas mezi klientem a Microsoft Graph. Klient se zaregistroval pro oprávnění `user.read` a `contacts.read` . Také se zaregistroval pro obor Azure Key Vault `https://vault.azure.net/user_impersonation` . 

Když klient požádá o token pro `scope=https://graph.microsoft.com/.default` , uživateli se zobrazí stránka s souhlasem pro `user.read` obor, `contacts.read` Rozsah a `user_impersonation` obory Key Vault. Vrácený token obsahuje pouze `user.read` `contacts.read` rozsahy a. Dá se použít jenom proti Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Příklad 3: uživatel souhlasí a klient požaduje více oborů.

V tomto příkladu už uživatel souhlasil s `mail.read` klientem. Klient se zaregistroval pro `contacts.read` obor. 

Když klient požádá o token pomocí `scope=https://graph.microsoft.com/.default` žádosti a vyžádá si souhlas prostřednictvím `prompt=consent` , uživatel uvidí stránku pro vyjádření souhlasu pro všechna (a jenom) oprávnění, která byla aplikace zaregistrovaná. `contacts.read`Obor je na stránce souhlasu, ale `mail.read` není. Vrácený token je pro Microsoft Graph. Obsahuje `mail.read` a `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Použití oboru/.default s klientem

V některých případech může klient požádat o vlastní `/.default` obor. Následující příklad ukazuje tento scénář.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Tento příklad kódu vytvoří stránku souhlasu pro všechna registrovaná oprávnění, pokud jsou předchozí popisy souhlasu a `/.default` vztahují se k tomuto scénáři. Pak kód vrátí `id_token` místo přístupového tokenu.  

Toto chování vychází ze starších klientů, kteří přecházejí ze služby Azure AD Authentication Library (ADAL) do knihovny Microsoft Authentication Library (MSAL). Tuto instalaci *by neměli* používat noví klienti, kteří cílí na platformu Microsoft identity.

### <a name="client-credentials-grant-flow-and-default"></a>Přihlašovací údaje klienta udělují tok a/.Default  

Dalším použitím `/.default` je požádat o oprávnění aplikace (nebo *rolí*) v neinteraktivní aplikaci, jako je například aplikace typu démon, která používá tok udělení [přihlašovacích údajů klienta](v2-oauth2-client-creds-grant-flow.md) k volání webového rozhraní API.

Pokud chcete vytvořit oprávnění aplikace (role) pro webové rozhraní API, přečtěte si téma [Přidání rolí aplikace v aplikaci](howto-add-app-roles-in-azure-ad-apps.md).

Žádosti o přihlašovací údaje klienta v klientské aplikaci *musí* zahrnovat `scope={resource}/.default` . Zde `{resource}` je webové rozhraní API, které vaše aplikace zamýšlí volat. Vydání žádosti o přihlašovací údaje klienta pomocí individuálních oprávnění aplikace (role) *není podporováno.* Všechna oprávnění aplikace (role) udělená pro toto webové rozhraní API jsou součástí vráceného přístupového tokenu.

Pokud chcete udělit přístup k oprávněním aplikace, která definujete, včetně udělení souhlasu správce pro aplikaci, přečtěte si téma [Konfigurace klientské aplikace pro přístup k webovému rozhraní API](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Koncové lomítko a/.Default

Některé identifikátory URI prostředků mají koncové lomítko, například `https://contoso.com/` na rozdíl od `https://contoso.com` . Koncové lomítko může způsobit problémy s ověřením tokenu. K problémům dochází hlavně v případě, že je požadován token pro Azure Resource Manager ( `https://management.azure.com/` ). V tomto případě koncové lomítko v identifikátoru URI prostředku znamená, že při požadavku na token musí být znak lomítka přítomen.  Takže pokud požadujete token pro `https://management.azure.com/` a použití `/.default` , musíte požádat `https://management.azure.com//.default` (Všimněte si dvojitých lomítek!). Obecně platí, že pokud ověříte, že se token vystavuje, a pokud je token zamítnut rozhraním API, které by ho měl přijmout, zvažte přidání druhého lomítka a pokus o opakování. 

## <a name="troubleshooting-permissions-and-consent"></a>Řešení potíží s oprávněními a souhlasem

Postup řešení potíží naleznete v tématu [Neočekávaná chyba při provádění souhlasu s aplikací](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Další kroky

* [Tokeny ID na platformě Microsoft identity](id-tokens.md)
* [Přístupové tokeny na platformě Microsoft identity](access-tokens.md)

---
title: Obory, oprávnění a souhlas platformy Microsoft identity
description: Přečtěte si o autorizaci na koncovém bodu Microsoft Identity Platform, včetně oborů, oprávnění a souhlasu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: f1c35fc80a4ab5b293a974b8f2901716e65f32b1
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705686"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Oprávnění a souhlas v koncovém bodu Microsoft Identity Platform

Aplikace, které se integrují se sadou Microsoft Identity Platform, se řídí autorizačním modelem, který poskytuje uživatelům a správcům kontrolu nad tím, jak budou data dostupná. Implementace autorizačního modelu se aktualizovala na koncovém bodu Microsoft Identity Platform a mění, jak musí aplikace spolupracovat s platformou Microsoft identity. Tento článek se zabývá základními koncepty tohoto autorizačního modelu, včetně oborů, oprávnění a souhlasu.

## <a name="scopes-and-permissions"></a>Obory a oprávnění

Platforma Microsoft Identity implementuje autorizační protokol [OAuth 2,0](active-directory-v2-protocols.md) . OAuth 2,0 je metoda, přes kterou má aplikace třetí strany přístup k prostředkům hostovaným na webu jménem uživatele. Jakýkoli prostředek hostovaný na webu, který se integruje s platformou Microsoft identity, má identifikátor prostředku nebo identifikátor *URI ID aplikace*. Mezi prostředky hostované na webu od Microsoftu patří například:

* Microsoft Graph: `https://graph.microsoft.com`
* Rozhraní API pro Microsoft 365 poštu: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

> [!NOTE]
> Důrazně doporučujeme použít Microsoft Graph místo rozhraní API pro Microsoft 365 poštu atd.

Totéž platí pro všechny prostředky třetích stran, které jsou integrované s platformou Microsoft identity. Kterýkoli z těchto prostředků taky může definovat sadu oprávnění, která se dají použít k rozdělení funkcí tohoto prostředku do menších bloků dat. Například [Microsoft Graph](https://graph.microsoft.com) má definovaná oprávnění k provádění následujících úloh mimo jiné:

* Čtení kalendáře uživatele
* Zápis do kalendáře uživatele
* Odeslat e-mail jako uživatel

Definováním těchto typů oprávnění prostředek má jemně odstupňovanou kontrolu nad daty a způsobu zveřejnění funkcí rozhraní API. Aplikace třetí strany může požádat o tato oprávnění od uživatelů a správců, kteří musí žádost schválit, aby mohla aplikace přistupovat k datům nebo jednat jménem uživatele. Díky tomu, že se funkce prostředku přenášejí do menších sad oprávnění, je možné vytvářet aplikace třetích stran tak, aby požadovaly jenom specifická oprávnění, která potřebují ke své funkci. Uživatelé a správci můžou přesně zjistit, k jakým datům má aplikace přístup, a můžou být větší jistotu, že se nechová se škodlivým záměrem. Vývojáři by měli vždy dodržovat koncept s nejnižšími oprávněními, přičemž budou klást pouze oprávnění, která potřebují k fungování svých aplikací.

V případě OAuth 2,0 se tyto typy oprávnění nazývají *obory*. Jsou také často označována jako *oprávnění*. Oprávnění je reprezentované na platformě Microsoft Identity jako řetězcová hodnota. Pokračování v Microsoft Graph příkladu je hodnota řetězce pro každé oprávnění:

* Čtení kalendáře uživatele pomocí `Calendars.Read`
* Zápis do kalendáře uživatele pomocí `Calendars.ReadWrite`
* Odeslat e-mail jako uživatel pomocí `Mail.Send`

Aplikace tyto oprávnění nejčastěji vyžádá zadáním oborů v požadavcích na koncový bod autorizace platformy Microsoft Identity Platform. Určitá oprávnění s vysokou úrovní oprávnění je však možné udělit pouze prostřednictvím souhlasu správce a žádosti nebo uděleny pomocí [koncového bodu souhlasu správce](v2-permissions-and-consent.md#admin-restricted-permissions). Přečtěte si další informace.

## <a name="permission-types"></a>Typy oprávnění

Platforma Microsoft Identity Platform podporuje dva typy oprávnění: **delegovaná oprávnění** a **oprávnění aplikací**.

* **Delegovaná oprávnění** se používají v aplikacích, které mají přihlášeného uživatele k dispozici. Pro tyto aplikace buď uživatel nebo správce souhlasí s oprávněními, která aplikace požaduje, a aplikace je delegovaná oprávnění, aby fungovala jako přihlášený uživatel při volání cílového prostředku. Některá delegovaná oprávnění mohou být odsouhlasena uživateli bez oprávnění správce, ale některá z vyšších privilegovaných oprávnění vyžadují [souhlas správce](v2-permissions-and-consent.md#admin-restricted-permissions). Informace o tom, které role správce můžou souhlasit s delegovanými oprávněními, najdete v tématu [oprávnění role správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Oprávnění aplikací** se používají v aplikacích, které se spouštějí bez přihlášeného uživatele. například aplikace, které běží jako služby nebo procesy na pozadí.  Oprávnění aplikace může pouze [zasílat správce](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Skutečná oprávnění_ jsou oprávnění, která aplikace bude mít při provádění požadavků na cílový prostředek. Je důležité pochopit rozdíl mezi delegovanými a aplikačními oprávněními, ke kterým je vaše aplikace udělena, a jejím efektivním oprávněním při volání cílového prostředku.

- U delegovaných oprávnění budou _skutečná oprávnění_ aplikace udělena s nejnižšími privilegovanými oprávněními, kterým byla aplikace udělena (prostřednictvím souhlasu), a oprávněními aktuálně přihlášeného uživatele. Aplikace nemůže mít nikdy více oprávnění než přihlášený uživatel. V rámci organizací je možné oprávnění přihlášeného uživatele určit pomocí zásady nebo členství v jedné nebo několika rolích správce. Informace o tom, které role správce můžou souhlasit s delegovanými oprávněními, najdete v tématu [oprávnění role správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Předpokládejme například, že vaše aplikace získala oprávnění User. prohledatelné _. všechna_ delegovaná oprávnění. Toto oprávnění vaší aplikaci výslovně uděluje oprávnění ke čtení a aktualizaci profilu každého uživatele v organizaci. Pokud je přihlášený uživatel globální správce, vaše aplikace bude moct aktualizovat profil každého uživatele v organizaci. Pokud ale přihlášený uživatel není v roli správce, bude moct aplikace aktualizovat jenom profil přihlášeného uživatele. Nebude moct aktualizovat profily ostatních uživatelů v organizaci, protože uživatel, jehož jménem má aplikace oprávnění jednat, tato oprávnění nemá.

- V případě oprávnění aplikace budou _skutečná oprávnění_ vaší aplikace úplnou úrovní oprávnění, která jsou odvozena z oprávnění. Například aplikace s uživatelským rozhraním. pro čtení _. všechna_ oprávnění aplikace mohou aktualizovat profil každého uživatele v organizaci.

## <a name="openid-connect-scopes"></a>Obory OpenID Connect

Implementace Microsoft Identity Platform OpenID Connect má několik dobře definovaných oborů, které jsou také hostovány na Microsoft Graph: `openid` , `email` , a `profile` `offline_access` . `address` `phone` Obory a OpenID Connect nejsou podporovány.

Požadavek na OIDC obory a token vám poskytne token pro volání [koncového bodu UserInfo](userinfo.md).

### <a name="openid"></a>OpenID

Pokud aplikace provede přihlášení pomocí [OpenID Connect](active-directory-v2-protocols.md), musí požádat o `openid` obor. `openid`Obor se zobrazí na stránce s informacemi o pracovním účtu jako s oprávněním přihlásit se a na stránce osobní účet Microsoft souhlasu jako "zobrazit váš profil a připojit se k aplikacím a službám pomocí oprávnění účet Microsoft". S tímto oprávněním může aplikace získat jedinečný identifikátor uživatele ve formě `sub` deklarace identity. Také umožňuje aplikaci přístup ke koncovému bodu UserInfo. `openid`Obor se dá použít na koncovém bodu tokenu platformy Microsoft identity a získat tokeny ID, které může aplikace použít k ověřování.

### <a name="email"></a>e-mail

`email`Obor lze použít s `openid` oborem a všemi ostatními. Umožňuje aplikaci přístup k primární e-mailové adrese uživatele ve formě `email` deklarace identity. `email`Deklarace identity je obsažená v tokenu, pouze pokud je k uživatelskému účtu přidružena e-mailová adresa, která není vždy v případě případu. Pokud tento obor používáte `email` , vaše aplikace by měla být připravená na zpracování případu, ve kterém `email` deklarace identity v tokenu neexistuje.

### <a name="profile"></a>profil

`profile`Obor lze použít s `openid` oborem a všemi ostatními. Poskytuje aplikaci přístup k podstatnému množství informací o uživateli. K informacím, ke kterým může přistupovat, patří, ale nejsou omezené na jméno, příjmení, upřednostňované uživatelské jméno a ID objektu uživatele. Úplný seznam deklarací identity profilu dostupných v parametru id_tokens pro konkrétního uživatele najdete v [ `id_tokens` referenčních informacích](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` Rozsah](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) poskytuje aplikaci přístup k prostředkům jménem uživatele po delší dobu. Na stránce souhlasu se tento obor zobrazí jako "Správa přístupu k datům, ke kterým jste udělili oprávnění k přístupu. Když uživatel `offline_access` obor schválí, vaše aplikace může přijímat aktualizační tokeny z koncového bodu tokenu platformy Microsoft identity. Aktualizační tokeny jsou dlouhodobě dlouhodobé. Vaše aplikace může získat nové přístupové tokeny, jejichž platnost brzy vyprší.

> [!NOTE]
> Toto oprávnění se zobrazí na všech obrazovkách souhlasu ještě dnes, a to i pro toky, které neposkytují obnovovací token ( [implicitní tok](v2-oauth2-implicit-grant-flow.md)).  Tato možnost se zabývá scénáři, kdy může klient začít v rámci implicitního toku, a pak přejít na tok kódu, kde je očekáván obnovovací token.

Na platformě Microsoft identity (požadavky odeslané na koncový bod v 2.0) musí vaše aplikace explicitně požádat o `offline_access` obor, aby přijímala obnovovací tokeny. To znamená, že při uplatnění autorizačního kódu v [toku autorizačního kódu OAuth 2,0](active-directory-v2-protocols.md)obdržíte jenom přístupový token z `/token` koncového bodu. Přístupový token je po krátkou dobu platný. Platnost přístupového tokenu obvykle vyprší za jednu hodinu. V tomto okamžiku potřebuje vaše aplikace přesměrovat uživatele zpátky na `/authorize` koncový bod, aby získal nový autorizační kód. V závislosti na typu aplikace může uživatel po tomto přesměrování znovu zadat své přihlašovací údaje nebo odsouhlasit oprávnění.

Další informace o tom, jak získat a použít obnovovací tokeny, najdete v referenčních informacích k [protokolu platformy Microsoft Identity Platform](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Žádost o souhlas jednotlivého uživatele

V žádosti o autorizaci [OpenID Connect nebo OAuth 2,0](active-directory-v2-protocols.md) může aplikace požádat o oprávnění, která potřebuje, pomocí `scope` parametru dotazu. Když se například uživatel přihlásí k aplikaci, aplikace pošle požadavek podobný následujícímu příkladu (s přidáním konců řádků k čitelnosti):

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

Jakmile uživatel zadá své přihlašovací údaje, koncový bod platformy Microsoft Identity ověří odpovídající záznam *souhlasu uživatele*. Pokud uživatel nesouhlasí s žádným z požadovaných oprávnění v minulosti ani správce nesouhlasí s těmito oprávněními jménem celé organizace, koncový bod platformy Microsoft Identity žádá uživatele, aby udělil požadovaná oprávnění.

> [!NOTE]
>V tuto chvíli `offline_access` jsou oprávnění ("zachovat přístup k datům, ke kterým jste udělili přístup") a `user.read` ("přihlášení a čtení vašeho profilu") automaticky zahrnutá do původního souhlasu aplikace.  Tato oprávnění se obecně vyžadují pro správné fungování aplikací – `offline_access` poskytuje aplikaci přístup k aktualizacím tokenů, kritickým pro nativní a webové aplikace, a současně `user.read` poskytuje přístup k `sub` deklaraci identity, což klientovi nebo aplikaci umožní správně identifikovat uživatele v čase a získat přístup k informacím základní uživatele.

![Ukázkový snímek obrazovky, který zobrazuje souhlas pracovního účtu](./media/v2-permissions-and-consent/work_account_consent.png)

Když uživatel žádost o oprávnění schválí, zaznamená se souhlas a uživatel se nebude muset odsouhlasit znovu při dalších přihlášeních k aplikaci.

## <a name="requesting-consent-for-an-entire-tenant"></a>Žádost o souhlas pro celého tenanta

V případě, že organizace koupí licenci nebo předplatné aplikace, chce organizace aktivně nastavit aplikaci pro použití všemi členy organizace. V rámci tohoto procesu může správce udělit souhlas, že aplikace bude jednat jménem libovolného uživatele v tenantovi. Pokud správce udělí souhlas pro celého tenanta, uživatelům organizace se nezobrazí stránka pro souhlas aplikace.

Aby bylo možné požádat o souhlas s delegovanými oprávněními pro všechny uživatele v tenantovi, může vaše aplikace používat koncový bod souhlasu správce.

Navíc musí aplikace požádat o oprávnění aplikace pomocí koncového bodu souhlasu správce.

## <a name="admin-restricted-permissions"></a>Omezená oprávnění správce

Některá oprávnění s vysokou úrovní oprávnění v ekosystému Microsoftu je možné nastavit jako *omezené na správu*. Mezi příklady těchto typů oprávnění patří následující:

* Čtení úplných profilů všech uživatelů pomocí `User.Read.All`
* Zápis dat do adresáře organizace pomocí `Directory.ReadWrite.All`
* Čtení všech skupin v adresáři organizace pomocí `Groups.Read.All`

I když uživatel příjemce může udělit aplikaci přístup k tomuto druhu dat, uživatelům organizace se omezuje udělení přístupu ke stejné sadě citlivých firemních dat. Pokud vaše aplikace požaduje přístup k některé z těchto oprávnění od uživatele organizace, obdrží chybová zpráva s oznámením, že nemají oprávnění k souhlasu s oprávněními vaší aplikace.

Pokud vaše aplikace vyžaduje přístup k oborům s omezeným přístupem pro organizace, měli byste je požádat přímo od správce společnosti, ale taky pomocí koncového bodu souhlasu správce, který je popsaný dál.

Pokud aplikace požaduje delegovaná oprávnění s vysokou úrovní oprávnění a správce udělí tato oprávnění prostřednictvím koncového bodu souhlasu správce, je udělen souhlas pro všechny uživatele v tenantovi.

Pokud aplikace požaduje oprávnění aplikace a správce udělí tato oprávnění prostřednictvím koncového bodu souhlasu správce, není tato podpora prováděna jménem konkrétního uživatele. Místo toho je klientským aplikacím uděleno oprávnění *přímo*. Tyto typy oprávnění jsou používány pouze službami démon a jinými neinteraktivními aplikacemi, které běží na pozadí.

## <a name="using-the-admin-consent-endpoint"></a>Použití koncového bodu souhlasu správce

> [!NOTE]
> Poznámka: po udělení souhlasu správce pomocí koncového bodu souhlasu správce jste dokončili udělení souhlasu správce a uživatelé nepotřebují provádět žádné další další akce. Po udělení souhlasu správce můžou uživatelé získat přístupový token prostřednictvím obvyklého toku ověřování a výsledný přístupový token bude mít odsouhlasená oprávnění.

Když správce společnosti používá vaši aplikaci a přesměruje se na koncový bod autorizace, detekuje platforma Microsoftu identitu uživatele a požádá, jestli chce jménem celého tenanta udělit oprávnění, která jste si vyžádali. Existuje však i vyhrazený koncový bod souhlasu správce, který můžete použít, pokud chcete proaktivně požádat správce o udělení oprávnění jménem celého tenanta. Použití tohoto koncového bodu je také nezbytné pro vyžadování oprávnění aplikace (které nelze požadovat pomocí koncového bodu autorizace).

Pokud budete postupovat podle těchto kroků, může vaše aplikace požádat o oprávnění pro všechny uživatele v tenantovi, včetně rozsahů s omezeným oprávněním správce. Jedná se o operaci s vysokým oprávněním a měla by být provedena pouze v případě potřeby pro váš scénář.

Ukázku kódu, který implementuje postup, najdete v [ukázce obory s omezením pro správu](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Vyžádání oprávnění na portálu pro registraci aplikací

Aplikace jsou schopné poznamenat, jaká oprávnění vyžadují (delegované i aplikace) na portálu pro registraci aplikací.  To umožňuje použití `/.default` oboru a možnosti "udělení souhlasu správce" Azure Portal.  Obecně je vhodné zajistit, aby byla oprávnění staticky definovaná pro danou aplikaci nadmnožinou oprávnění, která bude žádající dynamicky a přírůstkově.

> [!NOTE]
>Oprávnění aplikace je možné požadovat jenom prostřednictvím použití [`/.default`](#the-default-scope) , takže pokud vaše aplikace potřebuje oprávnění aplikace, ujistěte se, že jsou uvedená na portálu pro registraci aplikací.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Konfigurace seznamu staticky vyžádaného oprávnění pro aplikaci

1. Pokud jste to ještě neudělali, můžete přejít do aplikace v prostředí [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) nebo [vytvořit aplikaci](quickstart-register-app.md) .
2. Vyhledejte část **oprávnění rozhraní API** a v rámci oprávnění API klikněte na Přidat oprávnění.
3. V seznamu dostupných rozhraní API vyberte **Microsoft Graph** a pak přidejte oprávnění, která vaše aplikace vyžaduje.
3. **Uložte** registraci aplikace.

### <a name="recommended-sign-the-user-into-your-app"></a>Doporučeno: podepsat uživatele do aplikace

Při vytváření aplikace, která používá koncový bod souhlasu správce, obvykle aplikace potřebuje stránku nebo zobrazení, ve kterém může správce schválit oprávnění aplikace. Tato stránka může být součástí toku registrace aplikace, části nastavení aplikace nebo může být vyhrazeným tokem "Connect". V mnoha případech má aplikace smysl zobrazit toto zobrazení "připojit" až po přihlášení uživatele pomocí pracovní nebo školní účet Microsoft.

Když uživatele podepíšete do své aplikace, můžete určit organizaci, ke které správce patří, a teprve potom požádat o schválení potřebných oprávnění. I když není nezbytně nutné, může vám to usnadnit vytváření intuitivnějšího prostředí pro uživatele vaší organizace. Pokud chcete uživatele podepsat, postupujte podle [kurzů Microsoft Identity Platform Protocol](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Vyžádání oprávnění od správce adresáře

Až budete připraveni požádat o oprávnění od správce vaší organizace, můžete uživatele přesměrovat na *koncový bod souhlasu správce*Microsoft Identity Platform.

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


| Parametr        | Stav        | Popis                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Povinné | Tenant adresáře, ze kterého chcete požádat o oprávnění. Dá se poskytnout ve formátu GUID nebo popisného názvu nebo obecně odkazovaného v rámci organizací, jak je vidět v příkladu. Nepoužívejte "Common", protože osobní účty nemůžou poskytovat souhlas správce, s výjimkou kontextu tenanta. Aby se zajistila nejlepší kompatibilita s osobními účty, které spravují klienty, použijte ID tenanta, pokud je to možné. |
| `client_id` | Vyžadováno | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `redirect_uri` | Vyžadováno |Identifikátor URI přesměrování, kde má být odeslána odpověď pro zpracování vaší aplikace. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu pro registraci aplikací. |
| `state` | Doporučeno | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Použijte stav ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
|`scope`        | Vyžadováno        | Definuje sadu oprávnění, kterou aplikace požaduje. Může se jednat o statické (pomocí [`/.default`](#the-default-scope) ) nebo dynamické obory.  To může zahrnovat obory OIDC ( `openid` , `profile` , `email` ). Pokud potřebujete oprávnění aplikace, musíte použít `/.default` k vyžádání staticky nakonfigurovaného seznamu oprávnění.  |


V tomto okamžiku Azure AD vyžaduje, aby se k dokončení žádosti přihlásil správce tenanta. Správce se vyzve ke schválení všech oprávnění, která jste si vyžádali v `scope` parametru.  Pokud jste použili statickou `/.default` hodnotu (), bude fungovat jako koncový bod souhlasu správce v 1.0 a souhlas se žádostí pro všechny obory nalezené v požadovaných oprávněních pro danou aplikaci.

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
| `error` | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojářům pomáhat určit hlavní příčinu chyby. |

Jakmile obdržíte úspěšnou odpověď z koncového bodu souhlasu správce, vaše aplikace získala požadovaná oprávnění. V dalším kroku si můžete vyžádat token pro prostředek, který chcete.

## <a name="using-permissions"></a>Používání oprávnění

Poté, co uživatel souhlasí s oprávněním pro vaši aplikaci, může vaše aplikace získat přístupové tokeny, které reprezentují oprávnění aplikace pro přístup k prostředku v určité kapacitě. Přístupový token se dá použít jenom pro jeden prostředek, ale kódovaný v rámci přístupového tokenu je každé oprávnění, které jste aplikaci udělili pro daný prostředek. K získání přístupového tokenu může vaše aplikace vytvořit požadavek na koncový bod tokenu platformy Microsoft Identity Platform, například:

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

V požadavcích HTTP na prostředek můžete použít výsledný přístupový token. Spolehlivě indikuje prostředek, který má vaše aplikace správné oprávnění k provedení konkrétního úkolu.

Další informace o protokolu OAuth 2,0 a o tom, jak získat přístupové tokeny, najdete v referenčních informacích k [protokolu služby Microsoft Identity Platform Endpoint](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Rozsah/.Default

Obor můžete použít `/.default` k usnadnění migrace vašich aplikací z koncového bodu v 1.0 na koncový bod Microsoft Identity Platform. Toto je vestavěný obor pro každou aplikaci, který odkazuje na statický seznam oprávnění nakonfigurovaných v registraci aplikace. `scope`Hodnota `https://graph.microsoft.com/.default` je funkčně stejná jako v koncových bodech v 1.0 `resource=https://graph.microsoft.com` – konkrétně vyžaduje token s obory na Microsoft Graph, pro které byla aplikace registrována v Azure Portal.  Je vytvořen pomocí identifikátoru URI prostředku + `/.default` (např. Pokud je identifikátor URI prostředku `https://contosoApp.com` , pak požadovaný rozsah `https://contosoApp.com/.default` ).  Přečtěte si [část o koncových lomítekch](#trailing-slash-and-default) pro případy, kdy je nutné pro správné vyžádání tokenu použít druhé lomítko.

Obor/.Default se dá použít v jakémkoli toku OAuth 2,0, ale je nutný v toku spouštěném [jménem](v2-oauth2-on-behalf-of-flow.md) a v [toku přihlašovacích údajů klienta](v2-oauth2-client-creds-grant-flow.md)a také při použití koncového bodu souhlasu správce v2 k vyžádání oprávnění aplikace.

> [!NOTE]
> Klienti nemůžou `/.default` v jednom požadavku kombinovat statické () a dynamické svolení. Proto `scope=https://graph.microsoft.com/.default+mail.read` dojde k chybě z důvodu kombinace typů oborů.

### <a name="default-and-consent"></a>/.Default a souhlas

`/.default`Obor spouští také chování koncového bodu v 1.0 `prompt=consent` . Žádá o souhlas všech oprávnění zaregistrovaných aplikací bez ohledu na prostředek. Pokud je součástí žádosti, `/.default` obor vrátí token, který obsahuje obory požadovaného prostředku.

### <a name="default-when-the-user-has-already-given-consent"></a>/.Default, pokud už uživatel udělil souhlas.

Vzhledem `/.default` k tomu, že funkce je funkčně shodná s `resource` chováním koncového bodu v 1.0 orientovaném na verzi 1.0, se s ním přinese i chování souhlasu koncového bodu verze 1.0. Konkrétně `/.default` pouze aktivuje se výzva k vyjádření souhlasu, pokud uživatel neudělí žádné oprávnění mezi klientem a prostředkem. Pokud takový souhlas existuje, vrátí se token obsahující všechny obory udělené uživatelem pro daný prostředek. Pokud však nebyla udělena žádná oprávnění nebo byl `prompt=consent` zadán parametr, zobrazí se výzva k vyjádření souhlasu pro všechny obory registrované klientskou aplikací.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Příklad 1: uživatel nebo správce tenanta má udělená oprávnění.

V tomto příkladu má uživatel (nebo správce klienta) uděleno klientovi oprávnění Microsoft Graph `mail.read` a `user.read` . Pokud klient odešle požadavek na, nezobrazí `scope=https://graph.microsoft.com/.default` se žádná výzva k vyjádření souhlasu bez ohledu na obsah klientských aplikací registrovaných oprávnění pro Microsoft Graph. Token by byl vrácen obsahující obory `mail.read` a `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Příklad 2: uživatel nemá udělená oprávnění mezi klientem a prostředkem.

V tomto příkladu neexistuje žádný souhlas uživatele mezi klientem a Microsoft Graph. Klient se zaregistroval pro `user.read` `contacts.read` oprávnění a i obor Azure Key Vault `https://vault.azure.net/user_impersonation` . Když klient požádá o token pro `scope=https://graph.microsoft.com/.default` , zobrazí se uživateli obrazovka pro vyjádření souhlasu `user.read` , `contacts.read` a Key Vault `user_impersonation` rozsahy. Vrácený token bude obsahovat jenom `user.read` `contacts.read` obory a a dá se použít jenom pro Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Příklad 3: uživatel souhlasí a klient požaduje další obory.

V tomto příkladu už uživatel souhlasil s `mail.read` klientem. Klient se zaregistroval pro `contacts.read` obor v registraci. Když klient vytvoří požadavek na token pomocí `scope=https://graph.microsoft.com/.default` žádosti a vyžádá si souhlas prostřednictvím `prompt=consent` , zobrazí se uživateli obrazovka pro vyjádření souhlasu pro všechna (a jenom) oprávnění zaregistrovaná aplikací. `contacts.read` Tato akce bude k dispozici na obrazovce pro vyjádření souhlasu, ale `mail.read` ne. Vrácený token bude pro Microsoft Graph a bude obsahovat `mail.read` a `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>Použití oboru/.default s klientem

Zvláštní případ `/.default` oboru existuje, kde klient požaduje svůj vlastní `/.default` obor. Následující příklad ukazuje tento scénář.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Tím vznikne obrazovka pro vyjádření souhlasu pro všechna registrovaná oprávnění (Pokud je k dispozici na základě výše uvedeného popisu souhlasu a `/.default` ), vrátí id_token místo přístupového tokenu.  Toto chování existuje u některých starších klientů, kteří přesunují z ADAL na MSAL a **neměly by** je používat noví klienti, kteří cílí na koncový bod Microsoft Identity Platform.

### <a name="trailing-slash-and-default"></a>Koncové lomítko a/.Default

Některé identifikátory URI prostředků mají koncové lomítko ( `https://contoso.com/` na rozdíl od `https://contoso.com` ), což může způsobit problémy s ověřováním tokenu.  K tomu může dojít hlavně při žádosti o token pro správu prostředků Azure ( `https://management.azure.com/` ), který má koncové lomítko na svůj identifikátor URI prostředku a vyžaduje, aby byl k dispozici při žádosti o token.  Proto se při požadavku na token pro `https://management.azure.com/` a použití `/.default` vyžaduje, abyste `https://management.azure.com//.default` poznamenali dvojité lomítko.

Obecně – Pokud jste ověřili, že se token vystavuje, a token se zamítl rozhraním API, které by ho měl přijmout, zvažte přidání druhého lomítka a opakování. K tomu dochází, protože přihlašovací server emituje token s cílovou skupinou, která odpovídá identifikátorům URI v `scope` parametru – s `/.default` odebraným z konce.  Pokud dojde k odebrání koncového lomítka, přihlašovací server pořád zpracuje požadavek a ověří ho proti identifikátoru URI prostředku, i když se už neshoduje. to je nestandardní a nemělo by se spoléhat na vaši aplikaci.

## <a name="troubleshooting-permissions-and-consent"></a>Řešení potíží s oprávněními a souhlasem

Pokud se vám nebo uživatelům vaší aplikace zobrazuje neočekávané chyby během procesu souhlasu, přečtěte si tento článek, kde najdete postup řešení potíží: [Neočekávaná chyba při provádění souhlasu s aplikací](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

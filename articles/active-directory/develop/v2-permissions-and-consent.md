---
title: Obory, oprávnění a souhlas platformy identit y microsoftu | Dokumenty společnosti Microsoft
description: Popis autorizace v koncovém bodě platformy identit microsoftu, včetně oborů, oprávnění a souhlasu.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: f4b51641ed6bd7317060b567cf839775be426ac8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050057"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Oprávnění a souhlas v koncovém bodě platformy identit Microsoftu

Aplikace, které se integrují s platformou identit microsoftu, se řídí modelem autorizace, který uživatelům a správcům umožňuje kontrolu nad přístupem k datům. Implementace autorizačního modelu byla aktualizována v koncovém bodě platformy identit microsoftu a mění způsob, jakým musí aplikace pracovat s platformou identit microsoftu. Tento článek popisuje základní koncepty tohoto autorizačního modelu, včetně oborů, oprávnění a souhlasu.

> [!NOTE]
> Koncový bod platformy identit y Microsoft nepodporuje všechny scénáře a funkce. Chcete-li zjistit, zda byste měli používat koncový bod platformy identit společnosti Microsoft, přečtěte si o [omezení platformy identit společnosti Microsoft](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Obory a oprávnění

Platforma identit společnosti Microsoft implementuje autorizační protokol [OAuth 2.0.](active-directory-v2-protocols.md) OAuth 2.0 je metoda, pomocí které může aplikace třetí strany přistupovat k webovým prostředkům jménem uživatele. Jakýkoli prostředek hostovaný ve webu, který se integruje s platformou identit microsoftu, má identifikátor prostředku nebo *identifikátor URI ID aplikace*. Mezi webové hostované prostředky společnosti Microsoft patří například:

* Microsoft Graph:`https://graph.microsoft.com`
* Rozhraní Api pro poštu Office 365:`https://outlook.office.com`
* Trezor klíčů Azure:`https://vault.azure.net`

> [!NOTE]
> Důrazně doporučujeme používat Microsoft Graph místo Office 365 Mail API, atd.

Totéž platí pro všechny prostředky třetích stran, které byly integrovány s platformou identit microsoftu. Některý z těchto prostředků také můžete definovat sadu oprávnění, které lze rozdělit funkce tohoto prostředku na menší bloky dat. Jako příklad je, [že aplikace Microsoft Graph](https://graph.microsoft.com) definovala oprávnění k následujícím úkolům, mimo jiné:

* Čtení kalendáře uživatele
* Zápis do kalendáře uživatele
* Poslat poštu jako uživatel

Definováním těchto typů oprávnění má prostředek jemně odstupňovanou kontrolu nad svými daty a tím, jak je funkce rozhraní API vystavena. Aplikace třetí strany může tato oprávnění požadovat od uživatelů a správců, kteří musí žádost schválit, aby aplikace mohla přistupovat k datům nebo jednat jménem uživatele. Rozdělením funkce prostředku do menších sad oprávnění lze aplikace jiných výrobců sestavit tak, aby požadovaly pouze konkrétní oprávnění, která potřebují k provádění své funkce. Uživatelé a správci mohou přesně vědět, k jakým datům má aplikace přístup, a mohou si být jistější, že se nechová se zlými úmysly. Vývojáři by měli vždy dodržovat koncept nejnižších oprávnění a žádat pouze o oprávnění, která potřebují k tomu, aby jejich aplikace fungovaly.

V OAuth 2.0 se tyto typy oprávnění nazývají *obory*. Oni jsou také často označovány jako *oprávnění*. Oprávnění je reprezentováno v platformě identit microsoftu jako řetězcová hodnota. Pokračování v příkladu Microsoft Graph, hodnota řetězce pro každé oprávnění je:

* Čtení kalendáře uživatele pomocí`Calendars.Read`
* Zápis do kalendáře uživatele pomocí`Calendars.ReadWrite`
* Odeslat poštu jako uživatel pomocí`Mail.Send`

Aplikace nejčastěji požaduje tato oprávnění zadáním oborů v požadavcích na koncový bod autorizovat platformu identit Microsoftu. Určitá oprávnění s vysokými oprávněními však mohou být udělena pouze na základě souhlasu správce a požadovaná/udělená pomocí [koncového bodu souhlasu správce](v2-permissions-and-consent.md#admin-restricted-permissions). Čtěte dál a dozvíte se více.

## <a name="permission-types"></a>Typy oprávnění

Platforma identit společnosti Microsoft podporuje dva typy oprávnění: **delegovaná oprávnění** a **oprávnění aplikací**.

* **Delegovaná oprávnění** jsou používána aplikacemi, které mají přihlášeného uživatele. U těchto aplikací uživatel nebo správce souhlasí s oprávněními, která aplikace požaduje, a aplikaci je delegováno oprávnění k tomu, aby při volání cílového prostředku jednala jako přihlášený uživatel. Některá delegovaná oprávnění mohou být schválena uživateli, kteří nejsou správci, ale některá oprávnění s vyššími oprávněními vyžadují [souhlas správce](v2-permissions-and-consent.md#admin-restricted-permissions). Informace o tom, které role správce mohou souhlasit s delegovanými oprávněními, najdete [v tématu Oprávnění rolí správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Oprávnění aplikací** používají aplikace, které běží bez přihlášeného uživatele. například aplikace, které běží jako služby na pozadí nebo daemons.  Oprávnění aplikace může [souhlasit](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)pouze správce .

_Účinná oprávnění_ jsou oprávnění, která bude mít vaše aplikace při vytváření požadavků na cílový prostředek. Je důležité pochopit rozdíl mezi delegovanými oprávněními a oprávněními aplikace, která je vaší aplikaci udělena, a jejími efektivními oprávněními při volání cílového prostředku.

- U delegovaných oprávnění budou _efektivní oprávnění_ vaší aplikace nejméně privilegovaným průsečíkem delegovaných oprávnění, která aplikaci udělili (prostřednictvím souhlasu) a oprávnění aktuálně přihlášeného uživatele. Aplikace nemůže mít nikdy více oprávnění než přihlášený uživatel. V rámci organizací je možné oprávnění přihlášeného uživatele určit pomocí zásady nebo členství v jedné nebo několika rolích správce. Informace o tom, které role správce mohou souhlasit s delegovanými oprávněními, najdete [v tématu Oprávnění rolí správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Předpokládejme například, že vaší aplikaci bylo uděleno oprávnění _uživateli.ReadWrite.All._ Toto oprávnění vaší aplikaci výslovně uděluje oprávnění ke čtení a aktualizaci profilu každého uživatele v organizaci. Pokud je přihlášený uživatel globální správce, vaše aplikace bude moct aktualizovat profil každého uživatele v organizaci. Pokud však přihlášený uživatel není v roli správce, bude aplikace moct aktualizovat pouze profil přihlášeného uživatele. Nebude moct aktualizovat profily ostatních uživatelů v organizaci, protože uživatel, jehož jménem má aplikace oprávnění jednat, tato oprávnění nemá.
  
- U oprávnění k aplikacím budou _efektivní oprávnění_ vaší aplikace úplná úroveň oprávnění vyplývajících z oprávnění. Například aplikace, která má _Oprávnění aplikace User.ReadWrite.All,_ může aktualizovat profil každého uživatele v organizaci. 

## <a name="openid-connect-scopes"></a>Obory OpenID Connect

Implementace platformy identit společnosti Microsoft OpenID Connect má několik dobře definovaných oborů, `profile`které `offline_access`se nevztahují na konkrétní prostředek: `openid`, `email`, a . `address` Obory `phone` a obory OpenID Connect nejsou podporovány.

### <a name="openid"></a>Openid

Pokud aplikace provádí přihlášení pomocí [OpenID Connect](active-directory-v2-protocols.md), `openid` musí požádat o obor. Obor `openid` se na stránce s souhlasem pracovního účtu zobrazuje jako oprávnění Přihlásit se k vašemu účtu a na stránce s souhlasem osobního účtu Microsoft jako oprávnění Zobrazit svůj profil a připojit se k aplikacím a službám pomocí účtu Microsoft. S tímto oprávněním může aplikace získat jedinečný identifikátor uživatele `sub` ve formě deklarace identity. Poskytuje také aplikaci přístup ke koncovému bodu UserInfo. Obor `openid` lze použít na koncovém bodě tokenu platformy identity Microsoft u získání tokenů ID, které může aplikace použít k ověřování.

### <a name="email"></a>e-mail

Obor `email` lze použít s `openid` rozsahem a všechny ostatní. Poskytuje aplikaci přístup k primární e-mailové adrese uživatele `email` ve formě deklarace. Deklarace `email` je zahrnuta v tokenu pouze v případě, že e-mailová adresa je přidružena k uživatelskému účtu, což není vždy případ. Pokud používá `email` obor, vaše aplikace by měla být připravena pro zpracování případu, ve kterém `email` deklarace neexistuje v tokenu.

### <a name="profile"></a>profil

Obor `profile` lze použít s `openid` rozsahem a všechny ostatní. Poskytuje aplikaci přístup k podstatnému množství informací o uživateli. Informace, ke kterých má přístup, zahrnují mimo jiné křestní jméno uživatele, příjmení, upřednostňované uživatelské jméno a ID objektu. Úplný seznam deklarací profilu, které jsou k dispozici v [ `id_tokens` ](id-tokens.md)id_tokens parametru pro konkrétního uživatele, naleznete v odkazu .

### <a name="offline_access"></a>offline_access

[ `offline_access` Obor](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) poskytuje aplikaci přístup k prostředkům jménem uživatele po delší dobu. Na stránce souhlasu se tento obor zobrazí jako oprávnění "Udržovat přístup k datům, ke které jste mu udělili přístup". Když uživatel schválí `offline_access` obor, vaše aplikace může přijímat obnovovací tokeny z koncového bodu tokenu platformy identity Microsoftu. Obnovovací tokeny jsou dlouhodobé. Vaše aplikace může získat nové přístupové tokeny, jakmile vyprší platnost starších.

> [!NOTE]
> Toto oprávnění se zobrazí na všech obrazovkách souhlasu dnes, a to i pro toky, které neposkytují obnovovací token [(implicitní tok).](v2-oauth2-implicit-grant-flow.md)  Toto je na pokrytí scénářů, kde klient může začít v rámci implicitní tok a potom přejít na tok kódu, kde se očekává, že token aktualizace.

Na platformě identit microsoftu (požadavky na koncový bod v2.0) `offline_access` musí vaše aplikace explicitně požádat o obor, aby bylo nutné přijímat obnovovací tokeny. To znamená, že když uplatníte autorizační kód v [toku autorizačního kódu OAuth 2.0](active-directory-v2-protocols.md), obdržíte pouze přístupový token z koncového `/token` bodu. Přístupový token je platný krátkou dobu. Platnost přístupového tokenu obvykle vyprší za jednu hodinu. V tomto okamžiku vaše aplikace potřebuje přesměrovat `/authorize` uživatele zpět do koncového bodu získat nový autorizační kód. Během tohoto přesměrování, v závislosti na typu aplikace, může být nutné, aby uživatel znovu zadat své přihlašovací údaje nebo znovu souhlas s oprávněními. 

Další informace o tom, jak získat a používat tokeny aktualizace, naleznete v [odkazu na protokol platformy identit společnosti Microsoft](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Žádost o souhlas jednotlivých uživatelů

V žádosti o autorizaci [OpenID Connect nebo OAuth 2.0](active-directory-v2-protocols.md) může `scope` aplikace požádat o potřebná oprávnění pomocí parametru dotazu. Pokud se například uživatel přihlásí k aplikaci, odešle žádost jako v následujícím příkladu (s přidanými zalomení řádků pro čitelnost):

```
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

Parametr `scope` je prostorově oddělený seznam delegovaných oprávnění, které aplikace požaduje. Každé oprávnění je označeno připojením hodnoty oprávnění k identifikátoru prostředku (identifikátor URI ID aplikace). V příkladu požadavku aplikace potřebuje oprávnění ke čtení kalendáře uživatele a odesílání pošty jako uživatel.

Poté, co uživatel zadá svá pověření, koncový bod platformy identit microsoftu zkontroluje odpovídající záznam *souhlasu uživatele*. Pokud uživatel v minulosti nesouhlasil s žádným z požadovaných oprávnění, ani správce nesouhlasil s těmito oprávněními jménem celé organizace, koncový bod platformy identit společnosti Microsoft požádá uživatele o udělení požadovaných oprávnění.

> [!NOTE]
>V tomto okamžiku `offline_access` jsou oprávnění ("Udržovat přístup k datům, ke kterým jste mu udělili přístup") a `user.read` ("Přihlásit se a přečíst si svůj profil") automaticky zahrnuta do počátečního souhlasu s aplikací.  Tato oprávnění jsou obecně vyžadována `offline_access` pro správné funkce aplikace – poskytuje aplikaci přístup `user.read` k obnovovacím tokenům, které jsou důležité pro nativní a webové aplikace, zatímco poskytuje přístup k `sub` deklaraci identity, což umožňuje klientovi nebo aplikaci správně identifikovat uživatele v průběhu času a přistupovat k základním informacím o uživateli.  

![Příklad snímku obrazovky s souhlasem pracovního účtu](./media/v2-permissions-and-consent/work_account_consent.png)

Když uživatel schválí žádost o oprávnění, je zaznamenán souhlas a uživatel nemusí znovu souhlasit při následném přihlášení k aplikaci.

## <a name="requesting-consent-for-an-entire-tenant"></a>Žádost o souhlas pro celého nájemce

Často, když organizace zakoupí licenci nebo předplatné pro aplikaci, organizace chce proaktivně nastavit aplikaci pro použití všemi členy organizace. V rámci tohoto procesu může správce udělit souhlas s tím, aby aplikace jednala jménem libovolného uživatele v tenantovi. Pokud správce udělí souhlas pro celého tenanta, uživatelé organizace neuvidí stránku souhlasu pro aplikaci.

Chcete-li požádat o souhlas s delegovanými oprávněními pro všechny uživatele v tenantovi, může vaše aplikace použít koncový bod souhlasu správce.

Kromě toho aplikace musí použít koncový bod souhlasu správce požádat o oprávnění aplikací.

## <a name="admin-restricted-permissions"></a>Oprávnění s omezeným přístupem správce

Některá oprávnění s vysokými oprávněními v ekosystému Microsoftu lze nastavit na *omezenou službu správce*. Příklady těchto typů oprávnění zahrnují následující:

* Přečtěte si všechny profily uživatele pomocí`User.Read.All`
* Zápis dat do adresáře organizace pomocí`Directory.ReadWrite.All`
* Čtení všech skupin v adresáři organizace pomocí`Groups.Read.All`

Přestože uživatel příjemce může udělit aplikaci přístup k tomuto druhu dat, uživatelé organizace jsou omezeny v udělení přístupu ke stejné sadě citlivých dat společnosti. Pokud vaše aplikace požaduje přístup k jednomu z těchto oprávnění od uživatele organizace, uživatel obdrží chybovou zprávu, že není oprávněn souhlasit s oprávněními vaší aplikace.

Pokud vaše aplikace vyžaduje přístup k oborům omezeným na správu pro organizace, měli byste si je vyžádat přímo od správce společnosti, a to také pomocí koncového bodu souhlasu správce, který je popsán dále.

Pokud aplikace požaduje oprávnění s vysokými oprávněními a správce tato oprávnění udělí prostřednictvím koncového bodu souhlasu správce, je souhlas udělen všem uživatelům v tenantovi.

Pokud aplikace požaduje oprávnění aplikace a správce uděluje tato oprávnění prostřednictvím koncového bodu souhlasu správce, toto udělení se neprovádí jménem žádného konkrétního uživatele. Místo toho je klientské aplikaci udělena oprávnění *přímo*. Tyto typy oprávnění používají pouze služby daemon a jiné neinteraktivní aplikace, které běží na pozadí.

## <a name="using-the-admin-consent-endpoint"></a>Použití koncového bodu souhlasu správce

> [!NOTE] 
> Vezměte prosím na vědomí, že po udělení souhlasu správce pomocí koncového bodu souhlasu správce jste dokončili udělení souhlasu správce a uživatelé nemusí provádět žádné další akce. Po udělení souhlasu správce mohou uživatelé získat přístupový token prostřednictvím typického toku ověřování a výsledný přístupový token bude mít oprávnění s souhlasem. 

Pokud správce společnosti používá vaši aplikaci a je přesměrován na autorizovaný koncový bod, platforma identit společnosti Microsoft zjistí roli uživatele a zeptá se ho, zda chce jménem celého klienta souhlasit s požadovanými oprávněními. Existuje však také vyhrazený koncový bod souhlasu správce, který můžete použít, pokud chcete proaktivně požádat správce o oprávnění jménem celého klienta. Použití tohoto koncového bodu je také nezbytné pro vyžádání oprávnění aplikací (které nelze požadovat pomocí autorizovat koncový bod).

Pokud budete postupovat podle těchto kroků, vaše aplikace může požádat o oprávnění pro všechny uživatele v tenantovi, včetně oborů s omezeným přístupem správce. Toto je operace s vysokými oprávněními a měla by být provedena pouze v případě potřeby pro váš scénář.

Chcete-li zobrazit ukázku kódu, který implementuje kroky, naleznete v [části obory s omezeným přístupem správce](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Vyžádání oprávnění na portálu pro registraci aplikací

Aplikace si můžou na portálu pro registraci aplikací všimnout, jaká oprávnění vyžadují (delegovaná i aplikační).  To umožňuje použití `/.default` oboru a možnost "Udělit souhlas správce" portálu Azure.  Obecně je osvědčeným postupem zajistit, aby oprávnění staticky definovaná pro danou aplikaci byla nadmnožinou oprávnění, která bude dynamicky/postupně vyžadovat.

> [!NOTE]
>Oprávnění aplikací lze požadovat jenom pomocí [`/.default`](#the-default-scope) – takže pokud vaše aplikace potřebuje oprávnění k aplikaci, ujistěte se, že jsou uvedena na portálu pro registraci aplikací.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Konfigurace seznamu staticky požadovaných oprávnění pro aplikaci

1. Přejděte do aplikace na [webu Azure Portal –](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí pro registrace aplikací nebo [vytvořte aplikaci,](quickstart-register-app.md) pokud jste tak ještě neučinili.
2. Vyhledejte oddíl **Oprávnění rozhraní API** a v rámci oprávnění rozhraní API klikněte na Přidat oprávnění.
3. V seznamu dostupných rozhraní API vyberte **Microsoft Graph** a přidejte oprávnění, která vaše aplikace vyžaduje.
3. **Uložte** registraci aplikace.

### <a name="recommended-sign-the-user-into-your-app"></a>Doporučené: Přihlášení uživatele do aplikace

Obvykle při vytváření aplikace, která používá koncový bod souhlasu správce, aplikace potřebuje stránku nebo zobrazení, ve kterém správce může schválit oprávnění aplikace. Tato stránka může být součástí toku registrace aplikace, součástí nastavení aplikace nebo může být vyhrazený tok "připojení". V mnoha případech má smysl, aby aplikace zobrazila toto zobrazení "připojení" až poté, co se uživatel přihlásí pomocí pracovního nebo školního účtu Microsoft.

Když uživatele přihlásíte do aplikace, můžete určit organizaci, do které správce patří, než ho požádáte o schválení potřebných oprávnění. I když to není nezbytně nutné, může vám pomoci vytvořit intuitivnější prostředí pro uživatele vaší organizace. Chcete-li uživatele přihlásit, postupujte podle [našich výukových programů protokolu platformy identit společnosti Microsoft](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Vyžádání oprávnění od správce adresáře

Až budete připraveni požádat o oprávnění správce vaší organizace, můžete uživatele přesměrovat na *koncový bod souhlasu správce*platformy identit Microsoftu .

```
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
| `tenant` | Požaduje se | Klient adresáře, od kterého chcete požádat o oprávnění. Může být poskytnuta ve formátu GUID nebo popisný název nebo obecně odkazuje s organizacemi, jak je vidět v příkladu. Nepoužívejte "společné", protože osobní účty nemohou poskytnout souhlas správce s výjimkou v kontextu klienta. Chcete-li zajistit nejlepší kompatibilitu s osobními účty, které spravují klienty, použijte ID klienta, pokud je to možné. |
| `client_id` | Požaduje se | **ID aplikace (klienta),** které je k vaší aplikaci přiřazené [na portálu Azure – možnosti registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `redirect_uri` | Požaduje se |Identifikátor URI přesměrování, kam chcete odeslat odpověď, aby ji vaše aplikace zpracovat. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu pro registraci aplikací. |
| `state` | Doporučené | Hodnota zahrnutá v požadavku, která bude také vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Pomocí stavu můžete zakódovat informace o stavu uživatele v aplikaci před tím, než došlo k požadavku na ověření, například stránku nebo zobrazení, na kterých se používali. |
|`scope`        | Požaduje se        | Definuje sadu oprávnění požadovaných aplikací. To může být statické [`/.default`](#the-default-scope)(pomocí) nebo dynamické obory.  To může zahrnovat obory`openid`OIDC ( , `profile`, `email`). Pokud potřebujete oprávnění aplikace, `/.default` musíte použít k vyžádání staticky nakonfigurovaného seznamu oprávnění.  | 


V tomto okamžiku Azure AD vyžaduje správce klienta k přihlášení k dokončení požadavku. Správce je požádán o schválení všech oprávnění, `scope` která jste požadovali v parametru.  Pokud jste použili statickou (`/.default`) hodnotu, bude fungovat jako koncový bod souhlasu správce v1.0 a požádá o souhlas pro všechny obory nalezené v požadovaných oprávněních pro aplikaci.

#### <a name="successful-response"></a>Úspěšná odpověď

Pokud správce schválí oprávnění pro vaši aplikaci, úspěšná odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- |
| `tenant` | Klient adresáře, který vaší aplikaci udělil požadovaná oprávnění ve formátu GUID. |
| `state` | Hodnota zahrnutá v požadavku, která bude také vrácena v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci před došlo k požadavku na ověření, jako je například stránka nebo zobrazení, na které se nachází. |
| `admin_consent` | Bude nastavena `True`na . |

#### <a name="error-response"></a>Odpověď na chybu

Pokud správce neschválí oprávnění pro vaši aplikaci, neúspěšná odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dochází, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může pomoci vývojáři identifikovat hlavní příčinu chyby. |

Po úspěšné odpovědi od koncového bodu souhlasu správce získala vaše aplikace požadovaná oprávnění. Dále můžete požádat o token pro prostředek, který chcete.

## <a name="using-permissions"></a>Použití oprávnění

Poté, co uživatel odsouhlasí oprávnění pro vaši aplikaci, může vaše aplikace získat přístupové tokeny, které představují oprávnění vaší aplikace k přístupu k prostředku v určité kapacitě. Přístupový token lze použít pouze pro jeden prostředek, ale kódované uvnitř přístupového tokenu je každé oprávnění, které vaše aplikace byla udělena pro tento prostředek. Chcete-li získat přístupový token, vaše aplikace může požádat o koncový bod tokenu platformy identit microsoftu, například takto:

```
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

Výsledný přístupový token můžete použít v požadavcích HTTP k prostředku. Spolehlivě označuje prostředek, že vaše aplikace má správné oprávnění k provedení určitého úkolu. 

Další informace o protokolu OAuth 2.0 a o tom, jak získat přístupové tokeny, naleznete v [odkazu na koncový bod platformy identity společnosti Microsoft](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Výchozí obor /.

`/.default` Pomocí oboru můžete použít k migraci aplikací z koncového bodu v1.0 do koncového bodu platformy identit Microsoftu. Toto je předdefinovaný obor pro každou aplikaci, která odkazuje na statický seznam oprávnění nakonfigurovaných při registraci aplikace. Hodnota `scope` `https://graph.microsoft.com/.default` je funkčně stejná jako koncové body `resource=https://graph.microsoft.com` v1.0 – konkrétně požaduje token s obory v Microsoft Graphu, pro které se aplikace zaregistrovala na webu Azure Portal.  Je vytvořen pomocí identifikátoru `/.default` URI + prostředku (například `https://contosoApp.com`pokud je identifikátor URI `https://contosoApp.com/.default`prostředku , pak by požadovaný obor byl ).  Naleznete [v části koncové lomítka](#trailing-slash-and-default) pro případy, kdy je nutné zahrnout druhé lomítko správně požádat o token.

Obor /.default lze použít v libovolném toku OAuth 2.0, ale je nezbytné v [toku toku on-behalf-of](v2-oauth2-on-behalf-of-flow.md) a [tok pověření klienta](v2-oauth2-client-creds-grant-flow.md), stejně jako při použití koncového bodu souhlasu správce v2 požádat o oprávnění aplikace.  

> [!NOTE]
> Klienti nemohou kombinovat statický`/.default`( ) a dynamický souhlas v jednom požadavku. Výsledkem `scope=https://graph.microsoft.com/.default+mail.read` tedy bude chyba z důvodu kombinace typů oboru.

### <a name="default-and-consent"></a>/.default a souhlas

Obor `/.default` aktivuje chování koncového bodu v1.0 `prompt=consent` také. Požaduje souhlas pro všechna oprávnění registrovaná aplikací bez ohledu na prostředek. Pokud je zahrnuta jako `/.default` součást požadavku, obor vrátí token, který obsahuje obory pro požadovaný prostředek.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default, pokud uživatel již udělil souhlas

Protože `/.default` je funkčně `resource`identický s chováním koncového bodu -centric v1.0, přináší s sebou také chování souhlasu koncového bodu v1.0. Konkrétně spustí `/.default` výzvu k souhlasu pouze v případě, že uživatel neudělil mezi klientem a zdrojem žádné oprávnění. Pokud takový souhlas existuje, bude vrácen token obsahující všechny obory udělené uživatelem pro tento prostředek. Pokud však nebylo uděleno žádné `prompt=consent` oprávnění nebo byl parametr poskytnut, zobrazí se výzva k souhlasu pro všechny obory registrované klientskou aplikací.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Příklad 1: Uživatel nebo správce klienta udělil oprávnění.

V tomto příkladu uživatel (nebo správce klienta) udělil `mail.read` klientovi oprávnění Microsoft Graph a `user.read`. Pokud klient podá `scope=https://graph.microsoft.com/.default`žádost , zobrazí se žádná výzva k udělení souhlasu bez ohledu na obsah registrovaných oprávnění klientských aplikací pro aplikaci Microsoft Graph. Token by být vrácena obsahující `mail.read` `user.read`obory a .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Příklad 2: Uživatel neudělil oprávnění mezi klientem a prostředkem.

V tomto příkladu neexistuje žádný souhlas pro uživatele mezi klientem a Microsoft Graph. Klient se zaregistroval `user.read` pro `contacts.read` oprávnění a, stejně jako `https://vault.azure.net/user_impersonation`obor Azure Key Vault . Když klient požaduje token `scope=https://graph.microsoft.com/.default`pro , uživateli se zobrazí `user.read` `contacts.read`obrazovka souhlasu `user_impersonation` pro obory , a trezor klíčů. Vrácený token bude `user.read` mít `contacts.read` pouze obory a bude použitelný pouze proti Microsoft Graphu.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Příklad 3: Uživatel souhlasil a klient požaduje další obory

V tomto příkladu uživatel již `mail.read` souhlasil pro klienta. Klient se zaregistroval `contacts.read` pro obor ve své registraci. Když klient požádá o použití `scope=https://graph.microsoft.com/.default` tokenu a `prompt=consent`požádá o souhlas prostřednictvím aplikace , zobrazí se uživateli obrazovka souhlasu pro všechna (a pouze) oprávnění registrovaná aplikací. `contacts.read`bude na obrazovce souhlasu, `mail.read` ale nebude. Vrácený token bude pro microsoft `mail.read` graph `contacts.read`a bude obsahovat a .

### <a name="using-the-default-scope-with-the-client"></a>Použití oboru /.default s klientem

Zvláštní případ oboru `/.default` existuje, pokud klient požaduje `/.default` vlastní obor. Následující příklad ukazuje tento scénář.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Tím se vytvoří obrazovka souhlasu pro všechna registrovaná oprávnění (pokud `/.default`je to možné na základě výše uvedených popisů souhlasu a ), pak vrátí id_token, nikoli přístupový token.  Toto chování existuje pro některé starší klienty, kteří se stěhují z ADAL do MSAL a **neměli** by být používáni novými klienty, kteří cílí na koncový bod platformy identit y Microsoft.  

### <a name="trailing-slash-and-default"></a>Koncové lomítko a /.default

Některé identifikátory URI prostředků mají`https://contoso.com/` koncové lomítko ( na `https://contoso.com`rozdíl od ), což může způsobit problémy s ověřením tokenu.  K tomu může dojít především při požadování`https://management.azure.com/`tokenu pro Azure Resource Management ( ), který má koncové lomítko na jejich uri prostředků a vyžaduje, aby byl přítomen při požadavku tokenu.  Proto při žádosti o `https://management.azure.com/` token `/.default`pro a `https://management.azure.com//.default` pomocí , musíte požádat - poznamenejte si dvojité lomítko! 

Obecně – pokud jste ověřili, že token je vydáván a token je odmítnut rozhraní API, které by měly přijmout, zvažte přidání druhé lomítko a zkuste to znovu. K tomu dochází, protože přihlašovací server vydává token s `scope` cílovou `/.default` skupinou odpovídající identifikátory URI v parametru - s odebrány z konce.  Pokud se tím odebere koncové lomítko, přihlašovací server stále zpracovává požadavek a ověřuje jej proti uri prostředku, i když již neodpovídají - to je nestandardní a nemělby se spoléhat na vaše aplikace.  

## <a name="troubleshooting-permissions-and-consent"></a>Poradce při potížích s oprávněními a souhlasem

Pokud se vy nebo uživatelé vaší aplikace během procesu souhlasu zobrazují neočekávané chyby, naleznete v tomto článku kroky řešení potíží: [Neočekávaná chyba při provádění souhlasu s aplikací](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

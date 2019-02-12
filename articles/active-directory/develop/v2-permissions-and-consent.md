---
title: Azure Active Directory v2.0 obory, oprávnění a vyjádření souhlasu | Dokumentace Microsoftu
description: Popis autorizace v bodu Azure AD v2.0, včetně oborů, oprávnění a vyjádření souhlasu.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: celested
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfc1ba6250a2d246c2dcf9a0128097b64896732d
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098506"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Oprávnění a souhlas v koncovém bodu Azure Active Directory v2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Aplikace, které se integrují s platformou identity Microsoft podle modelu autorizace, který dává uživatelům a správcům kontrolu nad jak data přístupná. Implementace modelu autorizace byl aktualizován na koncový bod verze 2.0 a změní se jak musí aplikaci komunikovat s platformou identity Microsoft. Tento článek se týká koncepcích tohoto modelu autorizace, včetně oborů, oprávnění a vyjádření souhlasu.

> [!NOTE]
> Koncový bod v2.0 nepodporuje všechny scénáře a funkce. Pokud chcete zjistit, zda by měl použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Obory a oprávnění

Microsoft identity platform implementuje [OAuth 2.0](active-directory-v2-protocols.md) autorizační protokol. OAuth 2.0 je metoda, pomocí kterého aplikace třetí strany mají přístup k hostované webové prostředkům jménem uživatele. Některé webové hostované z prostředků, která se integruje s platformou identity Microsoft mají identifikátor prostředku nebo *identifikátor URI ID aplikace*. Například některé z hostovaných webových prostředků od Microsoftu patří:

* Microsoft Graph: `https://graph.microsoft.com`
* Rozhraní API Office 365 e-mailu: `https://outlook.office.com`
* Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> Důrazně doporučujeme použít Microsoft Graph místo Azure AD Graph API e-mailu Office 365, atd.

Totéž platí pro všechny prostředky třetích stran, které integrovaly s platformou identity Microsoft. Některý z těchto prostředků také definovat sadu oprávnění, která je možné rozdělit do menších bloků funkcí tohoto prostředku. Jako příklad [Microsoft Graphu](https://graph.microsoft.com) definoval oprávnění provést následující úkoly, mimo jiné:

* Číst kalendář uživatele
* Zápis do kalendáře uživatele
* Odesílání pošty jménem uživatele

Definováním těchto typů oprávnění prostředek má detailní kontrolu nad jeho data a jak rozhraní API funkce jsou dostupné. Aplikace třetí strany můžete požádat tato oprávnění uživatelům a správcům, kteří musí schválit žádost o dříve, než aplikace můžete získat přístup k datům nebo jednat jménem uživatele. Podle bloků funkce prostředku do menších sady oprávnění, aplikace třetích stran se dají s žádostí o pouze konkrétní oprávnění, které potřebují k provedení jejich funkce. Uživatelé a správci můžou znáte přesně jaká data aplikace má přístup k a je možné si větší jistotu, že se nechová se zlými úmysly. Vývojáři by měla vždy dodržováním konceptu nejnižších oprávnění žádá o oprávnění, které potřebují pro své aplikace fungovat.

Tyto typy oprávnění OAuth 2.0, se nazývají *obory*. Se také často jednoduše označovány jako *oprávnění*. Oprávnění je vyjádřena v platformě Microsoft identity jako hodnotu řetězce. Budete pokračovat s ukázkou Microsoft Graphu, Řetězcová hodnota pro každé oprávnění je:

* Číst kalendář uživatele pomocí `Calendars.Read`
* Zápis do kalendáře uživatele s použitím `Calendars.ReadWrite`
* Odesílat poštu jménem uživatele pomocí podle `Mail.Send`

Aplikace požádá o nejčastěji, že tyto oprávnění tak, že zadáte obory v požadavcích na v2.0 zajistí autorizaci koncového bodu. Ale určitá oprávnění vysoká oprávnění lze udělit pouze prostřednictvím souhlasu správce a obecně požádal/udělit pomocí [koncový bod souhlas správce](v2-permissions-and-consent.md#admin-restricted-scopes). Pokud se chcete dozvědět víc, čtěte dál.

## <a name="permission-types"></a>Typy oprávnění

Platforma identit Microsoft podporuje dva typy oprávnění: **delegovaná oprávnění** a **oprávnění aplikace**.

* **Delegovaná oprávnění** jsou používány aplikací, které mají přihlášeného uživatele k dispozici. U těchto aplikací uživatel nebo správce souhlasí s oprávněními, žádosti o aplikace a aplikace je delegovaná oprávnění tak, aby fungoval jako přihlášený uživatel při volání cílový prostředek. Některé delegovaná oprávnění lze vyjádřit souhlas uživatelé bez oprávnění správce, ale některá oprávnění vyšší úrovní oprávnění vyžadují [souhlas správce](v2-permissions-and-consent.md#admin-restricted-scopes). Informace o tom, které správce rolí můžou udělit souhlas s delegovaná oprávnění, najdete v článku [oprávnění role správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Oprávnění aplikace** aplikací používají, na kterých běží bez přihlášeného uživatele k dispozici, například aplikace, na kterých běží jako služby na pozadí nebo procesy démon.  Oprávnění aplikace může být pouze [schválená správcem](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Skutečná oprávnění_ jsou oprávnění, která vaše aplikace bude mít při zasílání požadavků na cílový prostředek. Je důležité pochopit rozdíl mezi delegované a oprávnění aplikací, kterým je uděleno oprávnění aplikace a jeho skutečná oprávnění, při volání cílový prostředek.

- Delegovaná oprávnění _efektivní oprávnění_ vaší aplikace bude nejnižším oprávněním průnik delegovaná oprávnění aplikace získala (prostřednictvím souhlas) a pověření aktuálně přihlášeného uživatele. Aplikace nemůže mít nikdy více oprávnění než přihlášený uživatel. V rámci organizací je možné oprávnění přihlášeného uživatele určit pomocí zásady nebo členství v jedné nebo několika rolích správce. Informace o tom, které správce rolí můžou udělit souhlas s delegovaná oprávnění, najdete v článku [oprávnění role správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).
  Předpokládejme například, udělil vaší aplikace _User.ReadWrite.All_ delegovaná oprávnění. Toto oprávnění vaší aplikaci výslovně uděluje oprávnění ke čtení a aktualizaci profilu každého uživatele v organizaci. Pokud je přihlášený uživatel globální správce, vaše aplikace bude moct aktualizovat profil každého uživatele v organizaci. Pokud však přihlášený uživatel není v roli správce, vaše aplikace bude moct aktualizovat pouze profil přihlášeného uživatele. Nebude moct aktualizovat profily ostatních uživatelů v organizaci, protože uživatel, jehož jménem má aplikace oprávnění jednat, tato oprávnění nemá.
  
- Pro oprávnění aplikací _efektivní oprávnění_ vaší aplikace bude úrovni úplná oprávnění odvozené od oprávnění. Například aplikace, který má _User.ReadWrite.All_ oprávnění k aplikaci můžete aktualizovat profil každého uživatele v organizaci. 

## <a name="openid-connect-scopes"></a>Obory OpenID Connect

Implementace verze 2.0, OpenID Connect obsahuje několik jasně definované obory, které se nevztahují na konkrétní prostředek: `openid`, `email`, `profile`, a `offline_access`. `address` a `phone` OpenID Connect obory nejsou podporovány.

### <a name="openid"></a>openid

Pokud aplikace provádí přihlášení s použitím [OpenID Connect](active-directory-v2-protocols.md), musíte požádat o `openid` oboru. `openid` Oboru se zobrazí na stránce pracovní účet souhlasu jako "Přihlášení" oprávnění a na stránce osobní souhlasu účet Microsoft jako oprávnění "Zobrazit svůj profil a připojit se k aplikacím a službám, které používají váš účet Microsoft". Aplikace s tímto oprávněním může přijímat jedinečný identifikátor pro uživatele v podobě `sub` deklarací identity. Poskytuje aplikaci přístup ke koncovému bodu informací o uživateli. `openid` Obor je možné na koncový bod tokenu v2.0 získat tokeny typu ID, které je možné použít k zabezpečení volání HTTP mezi různými součástmi aplikace.

### <a name="email"></a>e-mail

`email` Rozsahu jde použít s `openid` obor a všechny ostatní. Poskytuje přístup k aplikaci primární e-mailovou adresu uživatele v podobě `email` deklarací identity. `email` Deklarací identity je součástí token pouze v případě, že uživatelský účet, který není vždy případ přidružen e-mailovou adresu. Pokud se používá `email` oboru, vaše aplikace by měla být připravena ke zpracování případ, ve kterém `email` deklarace identity v tokenu neexistuje.

### <a name="profile"></a>profil

`profile` Rozsahu jde použít s `openid` obor a všechny ostatní. Poskytuje přístup k aplikaci k vyžadovat značné množství informací o uživateli. Obsahuje informace, které má přístup, ale není omezena pouze na uživatele křestní jméno, příjmení, upřednostňované uživatelské jméno a ID objektu. Úplný seznam profilu deklarací, který je k dispozici v parametru id_tokens pro konkrétního uživatele, najdete v článku [ `id_tokens` odkaz](id-tokens.md).

### <a name="offlineaccess"></a>offline_access

[ `offline_access` Oboru](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) poskytuje aplikaci přístup k prostředkům jménem uživatele po delší dobu. Stránka pro odsouhlasení podmínek zobrazí tento obor oprávnění "Spravovat přístup k datům, které ho jste udělili přístup". Pokud uživatel potvrdí `offline_access` oboru, vaše aplikace může přijímat tokeny obnovení z koncového bodu v2.0 tokenu. Obnovovací tokeny jsou s dlouhým poločasem rozpadu. Aplikace můžete získat nové přístupové tokeny, protože platnost starších ty.

Pokud vaše aplikace nebude vyžadovat explicitně `offline_access` oboru, nezíská obnovovací tokeny. To znamená, že když uplatníte autorizační kód v [tok autorizačního kódu OAuth 2.0](active-directory-v2-protocols.md), zobrazí se pouze přístupového tokenu z `/token` koncového bodu. Přístupový token je platný po krátkou dobu. Obvykle vyprší platnost přístupového tokenu v jedné hodiny. AT, že bod, vaše aplikace potřebuje k přesměruje uživatele zpět `/authorize` koncový bod pro získání nové autorizační kód. Během toto přesměrování, v závislosti na typu aplikace může uživatel muset znovu zadat své přihlašovací údaje nebo znovu souhlas oprávnění.  Všimněte si, že při `offline_access` oboru je automaticky vyžadovaný serverem, klient musí stále žádosti za účelem přijímání obnovovací tokeny. 

Další informace o tom, jak získat a použít obnovovací tokeny, najdete v článku [referenci na protokol v2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Žádost o souhlas jednotlivé uživatele

V [OpenID Connect nebo OAuth 2.0](active-directory-v2-protocols.md) žádost o autorizaci, aplikaci můžete požádat o oprávnění, je nutné pomocí `scope` parametr dotazu. Když se uživatel přihlásí do aplikace, aplikace odešle požadavek jako v následujícím příkladu (pomocí konců řádků přidány pro čitelnost):

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

`scope` Parametr je místo oddělený seznam delegovaná oprávnění, které žádá o aplikaci. Každé oprávnění je indikován připojení hodnota oprávnění k prostředku identifikátor (URI ID aplikace). V tomto příkladu požadavek aplikace potřebuje oprávnění číst kalendář uživatele a odesílat poštu jménem uživatele.

Poté, co uživatel zadá své přihlašovací údaje, koncový bod v2.0 vyhledá odpovídající záznam *souhlasu uživatele*. Pokud uživatel nevyjádřil k některé z požadovaných oprávnění v minulosti, ani správce vyjádřil souhlas se tato oprávnění jménem celé organizace, koncový bod v2.0 žádá uživatele, udělit požadovaná oprávnění.

> [!NOTE]
> V tuto chvíli `offline_access` ("spravovat přístup k datům se jste udělili přístup") a `user.read` ("přihlášení a čtení vašeho profilu") oprávnění jsou automaticky obsažené v počátečním spuštění aplikace.  Tato oprávnění jsou obvykle požadovány pro správné funkce - `offline_access` poskytuje aplikaci přístup k obnovovacích tokenů, kritické pro nativní a webové aplikace, zatímco `user.read` poskytuje přístup k `sub` deklarace identity, povolení klienta nebo aplikaci tak, aby správně Identifikujte uživatele přes čas a přístup k základní uživatelské informace.  

![Pracovní účet souhlas](./media/v2-permissions-and-consent/work_account_consent.png)

Když uživatel schválí žádost o oprávnění, zaznamenán vyjádření souhlasu a uživatel nebude muset znovu vyjádřili souhlas na následné přihlášení k aplikaci.

## <a name="requesting-consent-for-an-entire-tenant"></a>Žádost o souhlas pro celého tenanta

Často když organizace zakoupí licence nebo předplatné pro aplikace, organizace chce proaktivně nastavení aplikace používají všechny členy vaší organizace. Jako součást tohoto procesu Správce může udělit souhlas pro aplikaci jednat jménem všech uživatelů v tenantovi. Pokud správce uděluje souhlas pro celého tenanta, uživatelé vaší organizace nezobrazí stránka pro odsouhlasení podmínek pro aplikaci.

Požádat o souhlas pro delegovaná oprávnění pro všechny uživatele v tenantovi, může vaše aplikace využívat koncový bod souhlas správce.

Aplikace musí navíc používat koncový bod souhlas správce s žádostí o oprávnění aplikace.

## <a name="admin-restricted-permissions"></a>Oprávnění správce s omezením

Některé vysokými oprávněními v ekosystému Microsoft může být nastaveno na *správce s omezením*. Mezi tyto druhy oprávnění, která patří:

* Čtení úplných profilů všech uživatelů s použitím `User.Read.All`
* Zápis dat do adresáře vaší organizace pomocí `Directory.ReadWrite.All`
* Čtení všech skupin v adresáři vaší organizace pomocí `Groups.Read.All`

I když uživatel příjemce může aplikaci udělit přístup k tomuto typu dat, organizační uživatelům zakázáno udělení přístupu k stejnou sadu důvěrných dat společnosti. Pokud vaše aplikace požaduje přístup k jednomu z těchto oprávnění od uživatele v organizaci, uživatel dostane chybovou zprávu s upozorněním, že nemáte oprávnění vyjádřit souhlas s oprávněními vaší aplikace.

Pokud vaše aplikace vyžaduje přístup k správce s omezením obory pro organizace, které by měl požádat o je přímo ze Správce společnosti, také pomocí koncového bodu souhlas správce, je popsáno dále.

Pokud aplikace požaduje vysokou úrovní oprávnění delegovaná oprávnění a správce udělí oprávnění prostřednictvím koncového bodu souhlas správce, je udělit souhlas pro všechny uživatele v tenantovi.

Pokud aplikace požaduje oprávnění aplikací a správce udělí, že tato oprávnění prostřednictvím Správce souhlasit koncový bod, není Hotovo tomuto grantu jménem žádné konkrétní uživatele. Místo toho klientská aplikace jsou udělena oprávnění *přímo*. Tyto typy oprávnění se obecně používají pouze tak, že démon, služeb a jiných neinteraktivní aplikace, které běží na pozadí.

## <a name="using-the-admin-consent-endpoint"></a>Pomocí koncového bodu souhlasu správce

Když správce společnosti používá vaše aplikace a směřuje na koncový bod authorize, platforma identit Microsoft rozpozná role uživatele a požádejte ho, pokud se chcete udělit souhlas jménem celého tenanta oprávnění, které jste si vyžádali. Existuje ale také vyhrazený správce souhlasu koncovým bodem, který můžete použít, pokud byste chtěli proaktivně požadavku, že správce udělí oprávnění jménem celého tenanta. Pomocí tohoto koncového bodu je také nutné pro požadování oprávnění aplikací (které nelze požadovat pomocí koncového bodu authorize).

Pokud budete postupovat podle těchto kroků, aplikaci požádat o oprávnění pro všechny uživatele v tenantovi, včetně oborů správce s omezením. To je operace s vysokou úrovní oprávnění a lze provádět pouze v případě potřeby pro váš scénář.

Vzorový kód, který implementuje kroky najdete v tématu [správce s omezením obory ukázka](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Požádat o oprávnění v portálu pro registraci aplikace

Souhlas správce nepřijímá parametr oboru, takže žádná oprávnění žádá musí být staticky definovaná v registrace vaší aplikace. Obecně je osvědčeným postupem je staticky definovaných pro danou aplikaci oprávnění musí být nadmnožinou oprávnění, že bude vyžadovat dynamicky/postupně.

Konfigurace seznamu staticky požadovaná oprávnění pro aplikaci: 
1. Přejděte na svoji aplikaci [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo [vytvořit aplikaci](quickstart-v2-register-an-app.md) Pokud jste tak již neučinili.
2. Vyhledejte **oprávnění Microsoft Graphu** a pak přidejte oprávnění, která vaše aplikace vyžaduje.
3. **Uložit** registraci aplikace.

### <a name="recommended-sign-the-user-into-your-app"></a>Doporučené: Přihlášení uživatele do vaší aplikace

Obvykle když vytvoříte aplikaci, která používá koncový bod souhlas správce, aplikace musí stránku nebo zobrazení, ve kterém může správce schválení oprávnění aplikace. Tato stránka může být součástí registrace toku aplikace, součást aplikace nastavení, nebo může být vyhrazený tok "připojení". V mnoha případech je vhodné pro aplikace, aby to předvedli "připojení" Zobrazit pouze poté, co uživatel má přihlášení pomocí pracovního nebo školního účtu Microsoft.

Při přihlášení uživatele do vaší aplikace, můžete určit organizaci, do které patří správce před s žádostí o schválení potřebná oprávnění. Ačkoli to není nezbytně nutné, pomůže vám vytvořit intuitivnější prostředí pro vaše organizace uživatele. Přihlášení uživatele, postupujte podle našich [v2.0 protokol kurzy](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Požádat o oprávnění od správce adresáře

Jakmile budete připraveni k žádosti o oprávnění od správce vaší organizace, můžete přesměruje uživatele v2.0 *koncový bod souhlas správce*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Podmínka | Popis |
| --- | --- | --- |
| `tenant` | Požaduje se | Tenantu Active directory, kterou chcete požádat o oprávnění. Můžete zadat ve formátu popisný název nebo identifikátor GUID nebo obecně odkazovaný adresou `common` jak je znázorněno v příkladu. |
| `client_id` | Požaduje se | ID aplikace (klient), který [portál pro registraci aplikací](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) nebo [nový portál pro registraci (preview) aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) má přiřazené vaší aplikaci. |
| `redirect_uri` | Požaduje se |Identifikátor URI pro přesměrování místo, kam chcete odpověď k odeslání pro vaši aplikaci ke zpracování. Musí odpovídat přesně jeden z identifikátorů URI, které jste zaregistrovali v portálu pro registraci aplikace pro přesměrování. |
| `state` | Doporučené | Hodnota v požadavku, která se také vrátit v odpovědi tokenu. Může být řetězec s žádný obsah, který chcete. Použijte ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na stav. |

V tuto chvíli Azure AD vyžaduje správce tenantů pro přihlášení k dokončení požadavku. Správce se zobrazí výzva ke schválení všechna oprávnění, které jste si vyžádali pro vaši aplikaci v portálu pro registraci aplikace.

#### <a name="successful-response"></a>Úspěšné odpovědi

Pokud správce schválí oprávnění pro vaši aplikaci, úspěšné odpovědi vypadá takto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- | --- |
| `tenant` | Tenantu Active directory, která vaše aplikace oprávnění je požadováno, ve formátu GUID. |
| `state` | Hodnota v požadavku, která se také vrátit v odpovědi tokenu. Může být řetězec s žádný obsah, který chcete. Stav se používá ke kódování informace o stavu uživatele v aplikaci předtím, než požadavek na ověření došlo k chybě, například stránky nebo zobrazení, které byly na. |
| `admin_consent` | Bude nastavena na `True`. |

#### <a name="error-response"></a>Odpověď na chybu

Pokud správce neschvaluje oprávnění pro vaši aplikaci, neúspěšnou odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- | --- |
| `error` | Řetězec kódu chyby, která slouží ke klasifikaci typy chyb, ke kterým dochází a je možné reagovat na chyby. |
| `error_description` | Určité chybové zprávě, které vývojář může pomoci zjistit původní příčinu chyby. |

Po přijetí úspěšné odpovědi z koncového bodu souhlas správce vaší aplikace získala oprávnění, která byla požadována. V dalším kroku můžete požádat o token pro zdroj, který chcete.

## <a name="using-permissions"></a>Pomocí oprávnění

Jakmile uživatel souhlasí s oprávněními pro vaši aplikaci, vaše aplikace můžete získat přístupové tokeny, které představují vaše aplikace oprávnění pro přístup k prostředku nějakým způsobem. Přístupový token lze použít pouze pro jeden prostředek, ale kódovaný uvnitř přístupový token je každé oprávnění, která udělil vaší aplikace pro daný prostředek. K získání přístupového tokenu, můžete aplikace vytvořte žádost na token koncovým bodem v2.0, následujícím způsobem:

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

Výsledný token přístupu můžete použít v požadavcích HTTP k prostředku. Spolehlivě znamená k prostředku, že vaše aplikace má správná oprávnění k provedení určitého úkolu. 

Další informace o protokolu OAuth 2.0 a jak získat přístupové tokeny, najdete v článku [referenci na protokol koncového bodu v2.0](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Obor /.default

Můžete použít `/.default` nastavit obor na pomoc s migrací aplikací z verze 1.0 koncového bodu na koncový bod verze 2.0. Toto je předdefinovaný obor pro každou aplikaci, která odkazuje na statický seznam oprávnění konfigurovaná v registraci aplikace. A `scope` hodnotu `https://graph.microsoft.com/.default` je funkčně stejný jako koncové body v1.0 `resource=https://graph.microsoft.com` – konkrétně žádá token s obory v Microsoft Graphu, který má aplikaci zaregistrovali na webu Azure Portal.

Obor /.default lze použít v toku OAuth 2.0, ale je obzvláště nutné v [](v2-oauth2-on-behalf-of-flow.md) tok On-Behalf-Of a [údajů klienta, které tok](v2-oauth2-client-creds-grant-flow.md).  

> [!NOTE]
> Klienty nelze kombinovat statické (`/.default`) a dynamické souhlas v jedné žádosti. Proto `scope=https://graph.microsoft.com/.default+mail.read` způsobí chybu z důvodu kombinaci typů oboru.

### <a name="default-and-consent"></a>/.default a vyjádření souhlasu

`/.default` Oboru aktivuje v1.0 chování koncového bodu pro `prompt=consent` také. Požaduje souhlas pro všechna oprávnění zaregistrované aplikací bez ohledu na prostředek. Pokud zahrnutý jako součást požadavku, `/.default` oboru vrátí token, který obsahuje obory konkrétně požadovaný prostředek.

### <a name="default-when-the-user-has-already-given-consent"></a>Pokud již uživatel udělil souhlas /.default

Protože `/.default` je funkčně stejný jako `resource`-chování koncového bodu na střed v1.0, přináší s nimi souhlas chování koncového bodu v1.0 také. Konkrétně `/.default` výzva k povolení spuštění pouze aktivuje, pokud oprávnění udělil mezi klientem a prostředek uživatele. Existuje takový souhlas, bude se vrátil token obsahující všechny obory poskytnuté uživatelem pro daný prostředek. Nicméně, pokud byla poskytnuta žádná oprávnění, nebo `prompt=consent` byl poskytnut parametr, zobrazí se výzva k povolení spuštění pro všechny obory registrovaných klientské aplikace. 

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Příklad 1: Uživatel nebo správce tenanta, udělil oprávnění

Uživatel (nebo správce tenanta) udělil klientovi oprávnění Microsoft Graphu `mail.read` a `user.read`. Pokud klient požádá o `scope=https://graph.microsoft.com/.default`, pak žádná výzva k povolení spuštění se zobrazí bez ohledu na obsah klientské aplikace registrovaná oprávnění pro Microsoft Graph. Token by vrátila obsahující obory `mail.read` a `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Příklad 2: Uživatel neudělil oprávnění mezi klientem a prostředek

Bez souhlasu pro uživatele existuje mezi klientem a Microsoft Graph. Klient je zaregistrován pro `user.read` a `contacts.read` oprávnění, stejně jako obor Azure Key Vault `https://vault.azure.net/user_impersonation`. Když klient požádá o token pro `scope=https://graph.microsoft.com/.default`, uživateli se zobrazí obrazovka pro vyjádření souhlasu pro `user.read`, `contacts.read`a službu Key Vault `user_impersonation` obory. Vrátí token, který bude mít jenom `user.read` a `contacts.read` obory v něm.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Příklad 3: Uživatelem a klient požádá o další obory

Uživatel už souhlasil se `mail.read` pro klienta. Klient je zaregistrován pro `contacts.read` oboru v jeho registraci. Když klient odešle požadavek tokenu pomocí `scope=https://graph.microsoft.com/.default` a žádosti o souhlas prostřednictvím `prompt=consent`, uživateli se zobrazí obrazovka pro vyjádření souhlasu pro pouze a všechna oprávnění zaregistrované aplikací. `contacts.read` bude k dispozici v obrazovkami pro vyjádření souhlasu, ale `mail.read` se tak nestane. Vrátí token, který bude pro Microsoft Graph a bude obsahovat `mail.read` a `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Použití oboru /.default s klientem

Zvláštním případem `/.default` oboru existuje, kde klient požádá o vlastní `/.default` oboru. Následující příklad ukazuje tento scénář.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Tímto se vytvoří obrazovka pro vyjádření souhlasu pro všechna registrovaná oprávnění (Pokud příslušné podle výše uvedené popisy vyjádření souhlasu a `/.default`), pak vrátí tokentu id_token, nikoli přístupový token.  Toto chování pro některé starší verze klientů přesouvání z ADAL do MSAL existuje a by se nemělo používat nové klienty, které cílí na koncový bod verze 2.0.  

## <a name="troubleshooting-permissions-and-consent"></a>Řešení potíží s oprávnění a souhlas

Pokud vy nebo uživatelé vaší aplikace se zobrazují neočekávaných chyb během procesu souhlasu, najdete v článku řešení potíží: [Neočekávaná chyba při povolování spuštění aplikace](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

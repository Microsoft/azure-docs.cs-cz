---
title: Obory platformy Microsoft identity, oprávnění a vyjádření souhlasu | Microsoft Docs
description: Popis autorizace na koncovém bodu Microsoft Identity Platform, včetně oborů, oprávnění a souhlasu
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
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b4aa4fbff4e1b89b87dd05e0547db8e14ae5835
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927141"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Oprávnění a souhlas koncového bodu Microsoft Identity Platform

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Aplikace, které se integrují se sadou Microsoft Identity Platform, se řídí autorizačním modelem, který poskytuje uživatelům a správcům kontrolu nad tím, jak budou data dostupná. Implementace autorizačního modelu se aktualizovala na koncovém bodu Microsoft Identity Platform a mění, jak musí aplikace spolupracovat s platformou Microsoft identity. Tento článek se zabývá základními koncepty tohoto autorizačního modelu, včetně oborů, oprávnění a souhlasu.

> [!NOTE]
> Koncový bod platformy Microsoft Identity Platform nepodporuje všechny scénáře a funkce. Pokud chcete zjistit, jestli byste měli použít koncový bod platformy Microsoft identity, přečtěte si informace o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Obory a oprávnění

Platforma Microsoft Identity implementuje autorizační protokol [OAuth 2,0](active-directory-v2-protocols.md) . OAuth 2,0 je metoda, přes kterou má aplikace třetí strany přístup k prostředkům hostovaným na webu jménem uživatele. Jakýkoli prostředek hostovaný na webu, který se integruje s platformou Microsoft identity, má identifikátor prostředku nebo identifikátor *URI ID aplikace*. Mezi prostředky hostované na webu od Microsoftu patří například:

* Microsoft Graph: `https://graph.microsoft.com`
* Rozhraní API pro poštu Office 365: `https://outlook.office.com`
* Graf Azure AD: `https://graph.windows.net`

> [!NOTE]
> Důrazně doporučujeme použít Microsoft Graph místo Azure AD Graph, Office 365 mail API atd.

Totéž platí pro všechny prostředky třetích stran, které jsou integrované s platformou Microsoft identity. Kterýkoli z těchto prostředků taky může definovat sadu oprávnění, která se dají použít k rozdělení funkcí tohoto prostředku do menších bloků dat. Například [Microsoft Graph](https://graph.microsoft.com) má definovaná oprávnění k provádění následujících úloh mimo jiné:

* Čtení kalendáře uživatele
* Zápis do kalendáře uživatele
* Odeslat e-mail jako uživatel

Definováním těchto typů oprávnění prostředek má jemně odstupňovanou kontrolu nad daty a způsobu zveřejnění funkcí rozhraní API. Aplikace třetí strany může požádat o tato oprávnění od uživatelů a správců, kteří musí žádost schválit, aby mohla aplikace přistupovat k datům nebo jednat jménem uživatele. Díky tomu, že se funkce prostředku přenášejí do menších sad oprávnění, je možné vytvářet aplikace třetích stran tak, aby požadovaly jenom specifická oprávnění, která potřebují ke své funkci. Uživatelé a správci můžou přesně zjistit, k jakým datům má aplikace přístup, a můžou být větší jistotu, že se nechová se škodlivým záměrem. Vývojáři by měli vždy dodržovat koncept s nejnižšími oprávněními, přičemž budou klást pouze oprávnění, která potřebují k fungování svých aplikací.

V případě OAuth 2,0 se tyto typy oprávnění nazývají *obory*. Jsou také často označována jako *oprávnění*. Oprávnění je reprezentované na platformě Microsoft Identity jako řetězcová hodnota. Pokračování v Microsoft Graph příkladu je hodnota řetězce pro každé oprávnění:

* Čtení kalendáře uživatele pomocí `Calendars.Read`
* Zápis do kalendáře uživatele pomocí `Calendars.ReadWrite`
* Odeslat poštu jako uživatel pomocí `Mail.Send`

Aplikace tyto oprávnění nejčastěji vyžádá zadáním oborů v požadavcích na koncový bod autorizace platformy Microsoft Identity Platform. Určitá oprávnění s vysokou úrovní oprávnění je však možné udělit pouze prostřednictvím souhlasu správce a žádosti nebo uděleny pomocí [koncového bodu souhlasu správce](v2-permissions-and-consent.md#admin-restricted-permissions). Pokud se chcete dozvědět víc, čtěte dál.

## <a name="permission-types"></a>Typy oprávnění

Platforma Microsoft Identity Platform podporuje dva typy oprávnění: **delegovaná oprávnění** a **oprávnění aplikací**.

* **Delegovaná oprávnění** se používají v aplikacích, které mají přihlášeného uživatele k dispozici. Pro tyto aplikace buď uživatel nebo správce souhlasí s oprávněními, která aplikace požaduje, a aplikace je delegovaná oprávnění, aby fungovala jako přihlášený uživatel při volání cílového prostředku. Některá delegovaná oprávnění mohou být odsouhlasena uživateli bez oprávnění správce, ale některá z vyšších privilegovaných oprávnění vyžadují [souhlas správce](v2-permissions-and-consent.md#admin-restricted-permissions). Informace o tom, které role správce můžou souhlasit s delegovanými oprávněními, najdete v tématu [oprávnění role správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Oprávnění aplikací** se používají v aplikacích, které se spouštějí bez přihlášeného uživatele. například aplikace, které běží jako služby nebo procesy na pozadí.  Oprávnění aplikace může pouze [zasílat správce](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Skutečná oprávnění_ jsou oprávnění, která aplikace bude mít při provádění požadavků na cílový prostředek. Je důležité pochopit rozdíl mezi delegovanými a aplikačními oprávněními, ke kterým je vaše aplikace udělena, a jejím efektivním oprávněním při volání cílového prostředku.

- U delegovaných oprávnění budou _skutečná oprávnění_ aplikace udělena s nejnižšími privilegovanými oprávněními, kterým byla aplikace udělena (prostřednictvím souhlasu), a oprávněními aktuálně přihlášeného uživatele. Aplikace nemůže mít nikdy více oprávnění než přihlášený uživatel. V rámci organizací je možné oprávnění přihlášeného uživatele určit pomocí zásady nebo členství v jedné nebo několika rolích správce. Informace o tom, které role správce můžou souhlasit s delegovanými oprávněními, najdete v tématu [oprávnění role správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Předpokládejme například, že vaše aplikace získala oprávnění User. prohledatelné _. všechna_ delegovaná oprávnění. Toto oprávnění vaší aplikaci výslovně uděluje oprávnění ke čtení a aktualizaci profilu každého uživatele v organizaci. Pokud je přihlášený uživatel globální správce, vaše aplikace bude moct aktualizovat profil každého uživatele v organizaci. Pokud ale přihlášený uživatel není v roli správce, bude moct aplikace aktualizovat jenom profil přihlášeného uživatele. Nebude moct aktualizovat profily ostatních uživatelů v organizaci, protože uživatel, jehož jménem má aplikace oprávnění jednat, tato oprávnění nemá.
  
- V případě oprávnění aplikace budou _skutečná oprávnění_ vaší aplikace úplnou úrovní oprávnění, která jsou odvozena z oprávnění. Například aplikace s uživatelským rozhraním. pro čtení _. všechna_ oprávnění aplikace mohou aktualizovat profil každého uživatele v organizaci. 

## <a name="openid-connect-scopes"></a>Obory OpenID Connect

Implementace Microsoft Identity Platform OpenID Connect obsahuje několik dobře definovaných oborů, které neplatí pro konkrétní prostředek: `openid`, `email`, `profile`a `offline_access`. Obory `address` a `phone` OpenID Connect nejsou podporovány.

### <a name="openid"></a>OpenID

Pokud aplikace provede přihlášení pomocí [OpenID Connect](active-directory-v2-protocols.md), musí požádat o obor `openid`. Obor `openid` se zobrazí na stránce s informacemi o pracovním účtu jako s oprávněním přihlásit se a na stránce osobní účet Microsoft pro vyjádření souhlasu jako "zobrazení vašeho profilu a připojení k aplikacím a službám pomocí účet Microsoft". S tímto oprávněním může aplikace získat jedinečný identifikátor pro uživatele ve formě deklarace `sub`. Také umožňuje aplikaci přístup ke koncovému bodu UserInfo. Obor `openid` lze použít na koncovém bodu tokenu platformy Microsoft identity a získat tokeny ID, které může aplikace použít k ověřování.

### <a name="email"></a>e-mail

Obor `email` lze použít s oborem `openid` a všemi ostatními. Umožňuje aplikaci přístup k primární e-mailové adrese uživatele ve formě deklarace identity `email`. Deklarace `email` je obsažena v tokenu, pouze pokud je k uživatelskému účtu přidružena e-mailová adresa, která není vždy v případě případu. Pokud používá obor `email`, vaše aplikace by měla být připravená na zpracování případu, ve kterém deklarace identity `email` v tokenu neexistuje.

### <a name="profile"></a>profilu

Obor `profile` lze použít s oborem `openid` a všemi ostatními. Poskytuje aplikaci přístup k podstatnému množství informací o uživateli. K informacím, ke kterým může přistupovat, patří, ale nejsou omezené na jméno, příjmení, upřednostňované uživatelské jméno a ID objektu uživatele. Úplný seznam deklarací identity, které jsou dostupné v parametru id_tokens pro konkrétního uživatele, najdete v [referenčních informacích k`id_tokens`](id-tokens.md).

### <a name="offline_access"></a>offline_access

[Obor`offline_access`](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) poskytuje vaší aplikaci přístup k prostředkům jménem uživatele po delší dobu. Na stránce souhlasu se tento obor zobrazí jako "Správa přístupu k datům, ke kterým jste udělili oprávnění k přístupu. Když uživatel schválí `offline_access` rozsah, vaše aplikace může přijímat aktualizační tokeny z koncového bodu tokenu platformy Microsoft identity. Aktualizační tokeny jsou dlouhodobě dlouhodobé. Vaše aplikace může získat nové přístupové tokeny, jejichž platnost brzy vyprší.

Pokud vaše aplikace explicitně nepožaduje obor `offline_access`, neobdrží aktualizační tokeny. To znamená, že při uplatnění autorizačního kódu v [toku autorizačního kódu OAuth 2,0](active-directory-v2-protocols.md)obdržíte pouze přístupový token z `/token`ho koncového bodu. Přístupový token je po krátkou dobu platný. Platnost přístupového tokenu obvykle vyprší za jednu hodinu. V tomto okamžiku potřebuje vaše aplikace přesměrovat uživatele zpátky na `/authorize` koncový bod a získat nový autorizační kód. V závislosti na typu aplikace může uživatel po tomto přesměrování znovu zadat své přihlašovací údaje nebo odsouhlasit oprávnění. I když je `offline_access` rozsah automaticky vyžádaný serverem, musí si ho klient ještě vyžádat, aby mohl získat obnovovací tokeny.

Další informace o tom, jak získat a použít obnovovací tokeny, najdete v referenčních informacích k [protokolu platformy Microsoft Identity Platform](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Žádost o souhlas jednotlivého uživatele

V žádosti o autorizaci [OpenID Connect nebo OAuth 2,0](active-directory-v2-protocols.md) může aplikace požádat o oprávnění, která potřebuje, pomocí parametru dotazu `scope`. Když se například uživatel přihlásí k aplikaci, aplikace pošle požadavek podobný následujícímu příkladu (s přidáním konců řádků k čitelnosti):

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

Parametr `scope` je čárkami oddělený seznam delegovaných oprávnění, která aplikace požaduje. Každé oprávnění je určeno připojením hodnoty oprávnění k identifikátoru prostředku (identifikátor URI ID aplikace). V příkladu žádosti potřebuje aplikace oprávnění ke čtení kalendáře uživatele a odeslání pošty jako uživatel.

Jakmile uživatel zadá své přihlašovací údaje, koncový bod platformy Microsoft Identity ověří odpovídající záznam *souhlasu uživatele*. Pokud uživatel nesouhlasí s žádným z požadovaných oprávnění v minulosti ani správce nesouhlasí s těmito oprávněními jménem celé organizace, koncový bod platformy Microsoft Identity žádá uživatele, aby udělil požadovaná oprávnění.

> [!NOTE]
> V tuto chvíli `offline_access` ("udržovat přístup k datům, ke kterým jste udělili přístup") a `user.read` ("přihlášení a čtení vašeho profilu") jsou automaticky zahrnutá do počátečního souhlasu aplikace.  Tato oprávnění se obecně vyžadují pro správnou funkčnost aplikace – `offline_access` poskytuje aplikaci přístup k aktualizaci tokenů, kritickým pro nativní a webové aplikace, zatímco `user.read` poskytuje přístup k `sub` deklaraci identity, což klientovi nebo aplikaci umožní správně identifikovat uživatele. v průběhu času a přístup k informacím o uživatelích základní.  

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
* Přečtěte si všechny skupiny v adresáři organizace pomocí `Groups.Read.All`

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

Souhlas správce nepřijímá parametr oboru, takže požadovaná oprávnění musí být staticky definována v registraci aplikace. Obecně je vhodné zajistit, aby byla oprávnění staticky definovaná pro danou aplikaci nadmnožinou oprávnění, která bude žádající dynamicky a přírůstkově.

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


| Parametr     | Podmínka     | Popis                                                                               |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Požaduje se | Tenant adresáře, ze kterého chcete požádat o oprávnění. Dá se poskytnout ve formátu GUID nebo popisného názvu nebo obecně odkazované pomocí `common`, jak je vidět v příkladu. |
| `client_id` | Požaduje se | **ID aplikace (klienta)** , které [Azure Portal – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí přiřazené k vaší aplikaci. |
| `redirect_uri` | Požaduje se |Identifikátor URI přesměrování, kde má být odeslána odpověď pro zpracování vaší aplikace. Musí přesně odpovídat jednomu z identifikátorů URI přesměrování, které jste zaregistrovali na portálu pro registraci aplikací. |
| `state` | Doporučené | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Použijte stav ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
|`scope`        | Požaduje se      | Definuje sadu oprávnění, kterou aplikace požaduje. Může se jednat o statickou hodnotu (pomocí/.Default) nebo dynamické obory.  To může zahrnovat OIDC obory (`openid`, `profile`, `email`). | 


V tomto okamžiku Azure AD vyžaduje, aby se k dokončení žádosti přihlásil správce tenanta. Správce se vyzve ke schválení všech oprávnění, která jste si vyžádali v parametru `scope`.  Pokud jste použili statickou (`/.default`) hodnotu, bude fungovat jako koncový bod souhlasu správce v 1.0 a žádost o souhlas pro všechny obory nalezené v požadovaných oprávněních pro danou aplikaci.

#### <a name="successful-response"></a>Úspěšná odpověď

Pokud správce schválí oprávnění pro vaši aplikaci, úspěšná odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Popis |
| --- | --- |
| `tenant` | Tenant adresáře, který vaší aplikaci udělil požadovaná oprávnění, ve formátu identifikátoru GUID. |
| `state` | Hodnota obsažená v požadavku, která se také vrátí v odpovědi tokenu. Může to být řetězec libovolného obsahu, který chcete. Stav se používá ke kódování informací o stavu uživatele v aplikaci předtím, než došlo k žádosti o ověření, jako je například stránka nebo zobrazení, na kterých se nachází. |
| `admin_consent` | Bude nastaveno na `True`. |

#### <a name="error-response"></a>Chybová odezva

Pokud správce neschválí oprávnění pro vaši aplikaci, neúspěšná odpověď vypadá takto:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Popis |
| --- | --- |
| `error` | Řetězec kódu chyby, který lze použít ke klasifikaci typů chyb, ke kterým dojde, a lze jej použít k reakci na chyby. |
| `error_description` | Konkrétní chybová zpráva, která může vývojářům pomáhat určit hlavní příčinu chyby. |

Jakmile obdržíte úspěšnou odpověď z koncového bodu souhlasu správce, vaše aplikace získala požadovaná oprávnění. V dalším kroku si můžete vyžádat token pro prostředek, který chcete.

## <a name="using-permissions"></a>Používání oprávnění

Poté, co uživatel souhlasí s oprávněním pro vaši aplikaci, může vaše aplikace získat přístupové tokeny, které reprezentují oprávnění aplikace pro přístup k prostředku v určité kapacitě. Přístupový token se dá použít jenom pro jeden prostředek, ale kódovaný v rámci přístupového tokenu je každé oprávnění, které jste aplikaci udělili pro daný prostředek. K získání přístupového tokenu může vaše aplikace vytvořit požadavek na koncový bod tokenu platformy Microsoft Identity Platform, například:

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

V požadavcích HTTP na prostředek můžete použít výsledný přístupový token. Spolehlivě indikuje prostředek, který má vaše aplikace správné oprávnění k provedení konkrétního úkolu. 

Další informace o protokolu OAuth 2,0 a o tom, jak získat přístupové tokeny, najdete v referenčních informacích k [protokolu služby Microsoft Identity Platform Endpoint](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Rozsah/.Default

Obor `/.default` můžete použít k usnadnění migrace vašich aplikací z koncového bodu v 1.0 na koncový bod Microsoft Identity Platform. Toto je vestavěný obor pro každou aplikaci, který odkazuje na statický seznam oprávnění nakonfigurovaných v registraci aplikace. Hodnota `scope` `https://graph.microsoft.com/.default` je funkčně stejná jako v koncových bodech v 1.0 `resource=https://graph.microsoft.com` – konkrétně vyžaduje token s obory na Microsoft Graph, které aplikace zaregistrovala v Azure Portal.

Obor/.Default se dá použít v jakémkoli toku OAuth 2,0, ale je nutný v toku spouštěného [za běhu a](v2-oauth2-on-behalf-of-flow.md) [přihlašovacích údajů klienta](v2-oauth2-client-creds-grant-flow.md).  

> [!NOTE]
> Klienti nemůžou v jednom požadavku kombinovat statické (`/.default`) a dynamické svolení. Proto `scope=https://graph.microsoft.com/.default+mail.read` z důvodu kombinace typů oborů dojde k chybě.

### <a name="default-and-consent"></a>/.Default a souhlas

Obor `/.default` spouští také chování koncového bodu v 1.0 pro `prompt=consent`. Žádá o souhlas všech oprávnění zaregistrovaných aplikací bez ohledu na prostředek. Pokud je součástí žádosti, obor `/.default` vrátí token, který obsahuje obory požadovaného prostředku.

### <a name="default-when-the-user-has-already-given-consent"></a>/.Default, pokud už uživatel udělil souhlas.

Vzhledem k tomu, že `/.default` je funkčně totožné s chováním koncového bodu v 1.0 `resource`orientovaném v 1.0, je k němu přidružen i chování souhlasu koncového bodu verze 1.0. Konkrétně Pokud uživatel neudělí žádné oprávnění mezi klientem a prostředkem, `/.default` spustí pouze výzvu k zadání souhlasu. Pokud takový souhlas existuje, vrátí se token obsahující všechny obory udělené uživatelem pro daný prostředek. Pokud však nebyla udělena žádná oprávnění nebo byl zadán parametr `prompt=consent`, zobrazí se výzva k zadání souhlasu pro všechny obory registrované klientskou aplikací.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Příklad 1: uživatel nebo správce tenanta má udělená oprávnění.

Uživatel (nebo správce klienta) udělil klientovi oprávnění Microsoft Graph `mail.read` a `user.read`. Pokud klient vytvoří žádost o `scope=https://graph.microsoft.com/.default`, nezobrazí se žádná výzva k vyjádření souhlasu bez ohledu na obsah klientských aplikací registrovaných oprávnění pro Microsoft Graph. Vrátí se token obsahující obory `mail.read` a `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Příklad 2: uživatel nemá udělená oprávnění mezi klientem a prostředkem.

Mezi klientem a Microsoft Graph neexistuje žádný souhlas uživatele. Klient zaregistroval pro `user.read` a `contacts.read` oprávnění a také obor Azure Key Vault `https://vault.azure.net/user_impersonation`. Když klient požádá o token pro `scope=https://graph.microsoft.com/.default`, zobrazí se uživateli obrazovka pro `user.read`, `contacts.read`a obory Key Vault `user_impersonation`. Vrácený token bude obsahovat pouze obory `user.read` a `contacts.read`.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Příklad 3: uživatel souhlasí a klient požaduje další obory.

Uživatel již souhlasil s `mail.read` pro klienta. Klient se zaregistroval pro obor `contacts.read` v jeho registraci. Když klient vytvoří požadavek na token pomocí `scope=https://graph.microsoft.com/.default` a požádá o souhlas prostřednictvím `prompt=consent`, zobrazí se uživateli obrazovka pro vyjádření souhlasu jenom se všemi oprávněními registrovanými aplikací. `contacts.read` se zobrazí na obrazovce pro vyjádření souhlasu, ale `mail.read` to nepůjde. Vrácený token bude pro Microsoft Graph a bude obsahovat `mail.read` a `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Použití oboru/.default s klientem

Zvláštní případ `/.default` oboru existuje, kde klient požaduje svůj vlastní obor `/.default`. Následující příklad ukazuje tento scénář.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Tím se vytvoří obrazovka pro vyjádření souhlasu pro všechna registrovaná oprávnění (Pokud je k dispozici na základě výše uvedeného popisu souhlasu a `/.default`), pak vrátí id_token místo přístupového tokenu.  Toto chování existuje u některých starších klientů, kteří přesunují z ADAL na MSAL a neměly by je používat noví klienti, kteří cílí na koncový bod Microsoft Identity Platform.  

## <a name="troubleshooting-permissions-and-consent"></a>Řešení potíží s oprávněními a souhlasem

Pokud se vám nebo uživatelům vaší aplikace zobrazuje neočekávané chyby během procesu souhlasu, přečtěte si tento článek, kde najdete postup řešení potíží: [Neočekávaná chyba při provádění souhlasu s aplikací](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

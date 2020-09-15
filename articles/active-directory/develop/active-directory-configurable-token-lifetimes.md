---
title: Konfigurovatelné životnosti tokenů
titleSuffix: Microsoft identity platform
description: Naučte se nastavit životnost pro tokeny vydané platformou Microsoft identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: bbe4328d797f740e124d4944aee889d471393200
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085599"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Konfigurovatelné životnosti tokenů v platformě Microsoft Identity Platform (Preview)

Můžete zadat dobu života tokenu vydaného Microsoft Identity Platform. Životnost tokenů je možné nastavit u všech aplikací ve vaší organizaci, u aplikace pro více tenantů nebo pro konkrétní objekt služby ve vaší organizaci. 
> Všimněte si, že v současné době nepodporujeme konfiguraci životností tokenů pro spravované objekty služby identity.

> [!IMPORTANT]
> Po slyšení od zákazníků ve verzi Preview jsme implementovali [Možnosti správy relace ověřování](https://go.microsoft.com/fwlink/?linkid=2083106) ve službě Azure AD podmíněný přístup. Tuto novou funkci můžete použít ke konfiguraci životností tokenů aktualizace nastavením frekvence přihlášení. Po 30. května 2020 nebude moct žádný nový tenant použít konfigurovatelné zásady životnosti tokenů ke konfiguraci relace a obnovení tokenů. K vyřazení dojde během několika měsíců, což znamená, že přestanou dodržovat existující relaci a aktualizovat zásady tokenů. Po vyřazení můžete i po vyřazení nakonfigurovat životnosti přístupového tokenu.

V Azure AD představuje objekt zásad sadu pravidel, která se vynutila pro jednotlivé aplikace nebo pro všechny aplikace v organizaci. Každý typ zásad má jedinečnou strukturu se sadou vlastností, které jsou aplikovány na objekty, ke kterým jsou přiřazeny.

Zásady můžete určit jako výchozí zásady pro vaši organizaci. Zásada se použije u libovolné aplikace v organizaci, pokud není přepsána zásadou s vyšší prioritou. Můžete také přiřadit zásadu konkrétním aplikacím. Pořadí priority se liší podle typu zásad.

> [!NOTE]
> Konfigurovatelné zásady životnosti tokenů platí jenom pro mobilní a desktopové klienty, kteří mají přístup k prostředkům SharePointu Online a OneDrive pro firmy, a nevztahují se na relace webového prohlížeče.
> Pokud chcete spravovat životnost relací webového prohlížeče pro SharePoint Online a OneDrive pro firmy, použijte funkci [Životnost relace podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md) . Další informace o konfiguraci časových limitů nečinných relací najdete v [blogu k SharePointu Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) .

## <a name="token-types"></a>Typy tokenů

Můžete nastavit zásady životnosti tokenů pro aktualizační tokeny, přístupové tokeny, tokeny SAML, tokeny relace a tokeny ID.

### <a name="access-tokens"></a>Přístupové tokeny

Klienti používají přístupové tokeny pro přístup k chráněnému prostředku. Přístupový token se dá použít jenom pro konkrétní kombinaci uživatele, klienta a prostředku. Přístupové tokeny nejde odvolat a jsou platné, dokud nevyprší jejich platnost. Škodlivý objekt actor, který získal přístupový token, ho může použít pro rozsah své životnosti. Úprava životnosti přístupového tokenu je kompromis mezi zvýšením výkonu systému a zvýšením doby, po kterou klient zachovává přístup po zakázání účtu uživatele. Vylepšený výkon systému se dosahuje snížením počtu pokusů, kolikrát klient potřebuje získat nový přístupový token.  Výchozí hodnota je 1 hodina – po 1 hodině musí klient použít obnovovací token (obvykle v tichém režimu) získat nový obnovovací token a přístupový token. 

### <a name="saml-tokens"></a>Tokeny SAML

Tokeny SAML používá mnoho webových aplikací SAAS, které se získávají pomocí koncového bodu protokolu typu Saml2 v systému Azure Active Directory. Využívají je i aplikace, které používají WS-Federation. Výchozí doba platnosti tokenu je 1 hodina. V perspektivě aplikace je doba platnosti tokenu určena hodnotou NotOnOrAfter `<conditions …>` elementu v tokenu. Po ukončení doby platnosti tokenu musí klient iniciovat nový požadavek na ověření, který se často splní bez interaktivního přihlášení v důsledku tokenu relace jednotného přihlašování (SSO).

Hodnotu NotOnOrAfter lze změnit pomocí `AccessTokenLifetime` parametru v `TokenLifetimePolicy` . Nastaví se na životní cyklus nakonfigurovanou v zásadě, pokud existuje, a koeficient pro zešikmení s hodinami 5 minut.

Všimněte si, že NotOnOrAfter potvrzení předmětu zadané v `<SubjectConfirmationData>` elementu není ovlivněné konfigurací životnosti tokenu. 

### <a name="refresh-tokens"></a>Aktualizovat tokeny

Když klient získá přístupový token pro přístup k chráněnému prostředku, klient obdrží také obnovovací token. Obnovovací token slouží k získání nových párů tokenů přístupu a aktualizace po vypršení platnosti aktuálního přístupového tokenu. Obnovovací token je vázán na kombinaci uživatele a klienta. Obnovovací token můžete kdykoli [odvolat](access-tokens.md#token-revocation)a platnost tokenu se kontroluje při každém použití tokenu.  Při použití k načtení nových přístupových tokenů se nepovedlo aktualizovat tokeny – doporučujeme ale při získání nového tokenu bezpečně odstranit starý. 

Je důležité rozlišovat mezi důvěrnými a veřejnými klienty, protože to ovlivňuje, jak se dají použít dlouhotrvající tokeny aktualizace. Další informace o různých typech klientů najdete v [dokumentu RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Životnost tokenů s důvěrnými aktualizačními tokeny klienta
Důvěrné klienty jsou aplikace, které můžou bezpečně ukládat heslo klienta (tajné). Mohou prokázat, že požadavky pocházejí z zabezpečené klientské aplikace a nikoli ze škodlivého objektu actor. Webová aplikace je například důvěrný klient, protože může ukládat tajný klíč klienta na webový server. Nezveřejňuje se. Vzhledem k tomu, že jsou tyto toky bezpečnější, je výchozí doba platnosti obnovovacích tokenů vydaných pro tyto toky `until-revoked` , nedá se změnit pomocí zásad a nebude se odvolat při dobrovolných resetování hesla.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Životnost tokenů s obnovovacími tokeny veřejného klienta

Veřejné klienty nemohou bezpečně ukládat heslo klienta (tajný kód). Například aplikace pro iOS/Android nemůže odvažovat tajný klíč od vlastníka prostředku, takže se považuje za veřejného klienta. Můžete nastavit zásady pro prostředky, aby se zabránilo aktualizačním tokenům z veřejných klientů starších než v zadaném období, od získání nového páru přístupů nebo obnovovacího tokenu. (K tomu je potřeba použít vlastnost maximální neaktivní čas tokenu aktualizace ( `MaxInactiveTime` ).) Pomocí zásad můžete také nastavit dobu, po kterou se obnovovací tokeny už nepřijímají. (Chcete-li to provést, použijte vlastnost maximální stáří obnovovacího tokenu.) Dobu platnosti obnovovacího tokenu můžete upravit tak, aby bylo možné řídit, kdy a jak často musí uživatel při použití veřejné klientské aplikace znovu zadat přihlašovací údaje.

> [!NOTE]
> Vlastnost maximální stáří představuje dobu, po kterou lze použít jeden token. 

### <a name="id-tokens"></a>Tokeny ID
Tokeny ID jsou předány webům a nativním klientům. Tokeny ID obsahují informace o profilu uživatele. Token ID je vázán na určitou kombinaci uživatele a klienta. Tokeny ID se považují za platné, dokud nevyprší jejich platnost. Obvykle webová aplikace odpovídá době životnosti relace uživatele v aplikaci až po dobu životnosti tokenu ID vydaného pro uživatele. Můžete upravit životnost tokenu ID, abyste mohli určit, jak často webová aplikace vyprší aplikační relaci, a jak často vyžaduje, aby se uživatel znovu ověřil s platformou Microsoft identity (ať už v tichém nebo interaktivním).

### <a name="single-sign-on-session-tokens"></a>Tokeny relace jednotného přihlašování
Když se uživatel ověřuje pomocí platformy Microsoft Identity Platform, je k relaci jednotného přihlašování (SSO) s prohlížečem uživatele a platformou Microsoft identity. Token jednotného přihlašování ve formě souboru cookie představuje tuto relaci. Token relace jednotného přihlašování není vázán na konkrétní prostředek nebo klientskou aplikaci. Tokeny relace jednotného přihlašování se dají odvolat a jejich platnost se kontroluje pokaždé, když se používají.

Platforma Microsoft Identity Platform používá dva druhy tokenů relace jednotného přihlašování: trvalá a netrvalá. Tokeny trvalé relace jsou v prohlížeči uloženy jako trvalé soubory cookie. Netrvalé tokeny relací se ukládají jako soubory cookie relace. (Soubory cookie relace budou zničeny při zavření prohlížeče.) Obvykle je uložen netrvalý token relace. Když ale při ověřování uživatel vybere zaškrtávací políčko **zůstat přihlášeni** , je uložený token trvalé relace.

Netrvalá tokeny relace mají dobu životnosti 24 hodin. Trvalé tokeny mají životnost 90 dnů. Kdykoliv se token relace jednotného přihlašování používá v rámci období platnosti, doba platnosti se prodlouží o dalších 24 hodin nebo 90 dnů v závislosti na typu tokenu. Pokud se token relace jednotného přihlašování v rámci období platnosti nepoužívá, je považován za neplatný a už není přijatý.

Pomocí zásad můžete nastavit čas, kdy byl první token relace vydán, za který už token relace není přijatý. (Chcete-li to provést, použijte vlastnost maximální stáří tokenu relace.) Životnost tokenu relace můžete upravit tak, aby bylo možné řídit, kdy a jak často musí uživatel při použití webové aplikace znovu zadat přihlašovací údaje.

### <a name="token-lifetime-policy-properties"></a>Vlastnosti zásad životnosti tokenů
Zásada životního cyklu tokenu je typ objektu zásad, který obsahuje pravidla životnosti tokenů. Pomocí vlastností zásady můžete řídit zadané životnosti tokenů. Pokud nejsou nastavené žádné zásady, systém vyhodnotí výchozí hodnotu doby života.

### <a name="configurable-token-lifetime-properties"></a>Konfigurovatelné vlastnosti životnosti tokenů
| Vlastnost | Řetězec vlastnosti zásad | Ovlivňuje | Výchozí | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Doba života přístupového tokenu |AccessTokenLifetime<sup>2</sup> |Přístupové tokeny, tokeny ID, tokeny typu Saml2 |1 hodina |10 minut |1 den |
| Maximální neaktivní čas obnovovacího tokenu |MaxInactiveTime |Aktualizovat tokeny |90 dnů |10 minut |90 dnů |
| Maximální stáří tokenu obnovení jednoho faktoru |MaxAgeSingleFactor |Aktualizovat tokeny (pro všechny uživatele) |Do-neodvolán |10 minut |Do-odvolání<sup>1</sup> |
| Maximální stáří tokenu pro Multi-Factor Refresh |MaxAgeMultiFactor |Aktualizovat tokeny (pro všechny uživatele) |Do-neodvolán |10 minut |Do-odvolání<sup>1</sup> |
| Maximální stáří tokenu relace s jedním faktorem |MaxAgeSessionSingleFactor |Tokeny relace (trvalé a netrvalé) |Do-neodvolán |10 minut |180 dnů<sup>1</sup> |
| Maximální stáří tokenu relace Multi-Factor |MaxAgeSessionMultiFactor |Tokeny relace (trvalé a netrvalé) |Do-neodvolán |10 minut |180 dnů<sup>1</sup> |

* <sup>1</sup>365 dní je maximální explicitní délka, kterou lze pro tyto atributy nastavit.
* <sup>2</sup> . Chcete-li zajistit, aby webový klient Microsoft Teams funguje, doporučujeme, abyste pro Microsoft Teams AccessTokenLifetime více než 15 minut.

### <a name="exceptions"></a>Výjimky
| Vlastnost | Ovlivňuje | Výchozí |
| --- | --- | --- |
| Maximální stáří obnovovacího tokenu (vydané pro federované uživatele, kteří mají nedostatečné informace o odvolání<sup>1</sup>) |Aktualizovat tokeny (vydané pro federované uživatele, kteří mají nedostatečné informace o odvolání<sup>1</sup>) |12 hodin |
| Maximální neaktivní čas obnovovacího tokenu (vydaný pro důvěrné klienty) |Aktualizovat tokeny (vydané pro důvěrné klienty) |90 dnů |
| Maximální stáří obnovovacího tokenu (vydané pro důvěrné klienty) |Aktualizovat tokeny (vydané pro důvěrné klienty) |Do-neodvolán |

* <sup>1</sup> federované uživatele, kteří mají nedostatečné informace o odvolání, zahrnují všechny uživatele, kteří nemají atribut "LastPasswordChangeTimestamp" synchronizovaný. Těmto uživatelům se doručí krátký maximální stáří, protože AAD nemůže ověřit, jestli se mají odvolat tokeny, které jsou svázané se starým přihlašovacím údaji (třeba s heslem, které se změnilo), a musí se vrátit častěji, aby se zajistilo, že uživatel a přidružené tokeny jsou pořád v dobrém umístění. Aby bylo možné toto prostředí zlepšit, Správci klientů musí zajistit, aby synchronizoval tento atribut "LastPasswordChangeTimestamp" (dá se nastavit v objektu User pomocí PowerShellu nebo prostřednictvím AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Vyhodnocení zásad a stanovení priorit
Můžete vytvořit a následně přiřadit zásady životnosti tokenů ke konkrétní aplikaci, vaší organizaci a objektům služby. U konkrétní aplikace může platit více zásad. Zásada životního cyklu tokenu, která se projeví, se řídí těmito pravidly:

* Pokud se zásada explicitně přiřadí instančnímu objektu, vynutila se.
* Pokud instančnímu objektu explicitně nepřiřazuje žádné zásady, vynutila se zásada explicitně přiřazená nadřazené organizaci instančního objektu.
* Pokud se instančnímu objektu nebo organizaci explicitně nepřiřazuje žádné zásady, vynutila se zásada přiřazená aplikaci.
* Pokud není přiřazená žádná zásada k instančnímu objektu, organizaci nebo objektu aplikace, vynutily se výchozí hodnoty. (Viz tabulka v části [konfigurovatelné vlastnosti životnosti tokenu](#configurable-token-lifetime-properties).)

Další informace o vztahu mezi objekty aplikace a instančními objekty služby naleznete v tématu [Application and Service Principal Objects in Azure Active Directory](app-objects-and-service-principals.md).

Platnost tokenu je vyhodnocena v době, kdy je token použit. Zásady s nejvyšší prioritou u aplikace, ke které se přistupoval, se projeví.

Všechny časové rozsahy, které jsou zde použity, jsou formátovány podle objektu [TimeSpan](/dotnet/api/system.timespan) jazyka C# – D. hh: mm: ss.  Tedy 80 dní a 30 minut `80.00:30:00` .  Úvodní znak D se může vyřadit, pokud je nula, takže 90 minut `00:90:00` .  

> [!NOTE]
> Tady je příklad scénáře.
>
> Uživatel chce přistupovat ke dvěma webovým aplikacím: webové aplikace a a webová aplikace B.
> 
> Skutečnosti
> * Obě webové aplikace jsou ve stejné nadřazené organizaci.
> * Zásada životnosti tokenu 1 s tokenem relace je maximální stáří osmi hodin nastavené jako výchozí hodnota nadřazené organizace.
> * Webová aplikace A je běžné použití webové aplikace a není propojena s žádnými zásadami.
> * Webová aplikace B se používá pro vysoce citlivé procesy. Jeho instanční objekt je propojený se zásadami životnosti tokenu 2, který má token relace maximální stáří 30 minut.
>
> V 12:00 PM uživatel spustí novou relaci prohlížeče a pokusí se získat přístup k webové aplikaci A. Uživatel se přesměruje na platformu Microsoft identity a zobrazí se výzva k přihlášení. Tím se vytvoří soubor cookie s tokenem relace v prohlížeči. Uživatel je přesměrován zpět na webovou aplikaci A s tokenem ID, který umožňuje uživateli přístup k aplikaci.
>
> V 12:15 PM se uživatel pokusí získat přístup k webové aplikaci B. Prohlížeč přesměruje na platformu Microsoft identity, která detekuje soubor cookie relace. Instanční objekt webové aplikace B je propojen se zásadami životnosti tokenu 2, ale je také součástí nadřazené organizace s výchozí zásadou životnosti tokenů 1. Zásada životnosti tokenu 2 se projeví, protože zásady propojené s instančními objekty mají vyšší prioritu než výchozí zásady organizace. Token relace byl původně vydaný během posledních 30 minut, takže se považuje za platný. Uživatel je přesměrován zpět na webovou aplikaci B s tokenem ID, který jim udělí přístup.
>
> V 1:00 PM se uživatel pokusí získat přístup k webové aplikaci A. Uživatel se přesměruje na platformu Microsoft identity. Webová aplikace A není propojená s žádnými zásadami, ale vzhledem k tomu, že je v organizaci s výchozí zásadou životností tokenu 1, uplatní se tato zásada. Zjistil se soubor cookie relace, který byl původně vydaný během posledních osmi hodin. Uživatel je tiše přesměrován zpět na webovou aplikaci A s novým tokenem ID. Uživatel není vyžadován k ověření.
>
> Hned potom se uživatel pokusí získat přístup k webové aplikaci B. Uživatel se přesměruje na platformu Microsoft identity. Stejně jako dřív se uplatní zásady životnosti tokenu 2. Vzhledem k tomu, že byl token vydán před více než 30 minutami, bude uživatel vyzván k opětovnému zadání přihlašovacích údajů pro přihlášení. Branding – vydávají se nové tokeny relace a tokeny ID. Uživatel pak může získat přístup k webové aplikaci B.
>
>

## <a name="configurable-policy-property-details"></a>Podrobnosti vlastnosti konfigurovatelné zásady
### <a name="access-token-lifetime"></a>Doba života přístupového tokenu
**Řetězec:** AccessTokenLifetime

**Má vliv na:** Přístupové tokeny, tokeny ID, tokeny SAML

**Shrnutí:** Tato zásada řídí, jak dlouho je přístup a tokeny ID pro tento prostředek považovány za platné. Omezení vlastnosti životnosti přístupového tokenu snižuje riziko zneužití přístupového tokenu nebo tokenu ID škodlivým objektem actor po delší dobu. (Tyto tokeny nejde odvolat.) Při obchodování je nepříznivě ovlivněn výkon, protože tokeny musí být nahrazeny častěji.

### <a name="refresh-token-max-inactive-time"></a>Maximální neaktivní čas obnovovacího tokenu
**Řetězec:** MaxInactiveTime

**Má vliv na:** Aktualizovat tokeny

**Shrnutí:** Tato zásada řídí, jak může být starý obnovovací token předtím, než ho klient přestane používat k načtení nového páru přístupu nebo obnovovacího tokenu při pokusu o přístup k tomuto prostředku. Vzhledem k tomu, že při použití aktualizačního tokenu se obvykle vrátí nový obnovovací token, tato zásada zabrání přístupu, pokud se klient pokusí o přístup k jakémukoli prostředku pomocí aktuálního obnovovacího tokenu během zadaného časového období.

Tato zásada vynutí, aby uživatelé, kteří nebyli aktivní na svém klientovi, mohli znovu ověřit a načíst nový obnovovací token.

Vlastnost maximálního neaktivního času obnovovacího tokenu musí být nastavená na nižší hodnotu, než je maximální stáří tokenu s jedním koeficientem a maximální stáří tokenu pro aktualizaci vícefaktorového obnovení.

### <a name="single-factor-refresh-token-max-age"></a>Maximální stáří tokenu obnovení jednoho faktoru
**Řetězec:** MaxAgeSingleFactor

**Má vliv na:** Aktualizovat tokeny

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel pomocí obnovovacího tokenu získat nový pár přístupů nebo obnovovacího tokenu po úspěšném ověření pomocí jenom jednoho faktoru. Po ověření a přijetí nového obnovovacího tokenu může uživatel použít tok obnovovacího tokenu po určenou dobu. (To platí, pokud je aktuální obnovovací token neodvolán a nepoužívá se déle než při neaktivním čase.) V tomto okamžiku se uživateli vynutí znovu ověřit, aby získal nový obnovovací token.

Snížení maximálního stáří nutí uživatele ověřovat častěji. Vzhledem k tomu, že vícefaktorové ověřování je považováno za méně bezpečné než ověřování Multi-Factor Authentication, doporučujeme tuto vlastnost nastavit na hodnotu, která je větší nebo rovna hodnotě vlastnosti maximální stáří tokenu Multi-Factor Refresh.

### <a name="multi-factor-refresh-token-max-age"></a>Maximální stáří tokenu pro Multi-Factor Refresh
**Řetězec:** MaxAgeMultiFactor

**Má vliv na:** Aktualizovat tokeny

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel pomocí obnovovacího tokenu získat nový pár přístupů nebo obnovovacího tokenu po úspěšném ověření pomocí několika faktorů. Po ověření a přijetí nového obnovovacího tokenu může uživatel použít tok obnovovacího tokenu po určenou dobu. (To platí, pokud aktuální obnovovací token není odvolán a nepoužívá se déle než při neaktivním čase.) V tomto okamžiku je vynuceno opětovné ověření uživatelů, aby bylo možné získat nový obnovovací token.

Snížení maximálního stáří nutí uživatele ověřovat častěji. Vzhledem k tomu, že vícefaktorové ověřování je považováno za méně bezpečné než ověřování Multi-Factor Authentication, doporučujeme nastavit tuto vlastnost na hodnotu, která je větší nebo rovna hodnotě vlastnosti maximální stáří tokenu aktualizace Single-Factor.

### <a name="single-factor-session-token-max-age"></a>Maximální stáří tokenu relace s jedním faktorem
**Řetězec:** MaxAgeSessionSingleFactor

**Má vliv na:** Tokeny relace (trvalé a netrvalé)

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel použít token relace k získání nového ID a tokenu relace po jeho posledním ověření, a to pomocí jediného faktoru. Po ověření a přijetí nového tokenu relace může uživatel použít tok tokenu relace v zadaném časovém období. (Tato hodnota je true, pokud aktuální token relace není odvolán a nevypršela jeho platnost.) Po uplynutí zadaného časového období se uživatel nuceně znovu ověří a získá nový token relace.

Snížení maximálního stáří nutí uživatele ověřovat častěji. Vzhledem k tomu, že vícefaktorové ověřování je považováno za méně bezpečné než ověřování Multi-Factor Authentication, doporučujeme tuto vlastnost nastavit na hodnotu, která je rovna nebo menší než maximální stáří tokenu relace Multi-Factor.

### <a name="multi-factor-session-token-max-age"></a>Maximální stáří tokenu relace Multi-Factor
**Řetězec:** MaxAgeSessionMultiFactor

**Má vliv na:** Tokeny relace (trvalé a netrvalé)

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel použít token relace k získání nového ID a tokenu relace po jeho posledním ověření pomocí více faktorů. Po ověření a přijetí nového tokenu relace může uživatel použít tok tokenu relace v zadaném časovém období. (Tato hodnota je true, pokud aktuální token relace není odvolán a nevypršela jeho platnost.) Po uplynutí zadaného časového období se uživatel nuceně znovu ověří a získá nový token relace.

Snížení maximálního stáří nutí uživatele ověřovat častěji. Vzhledem k tomu, že vícefaktorové ověřování je považováno za méně bezpečné než ověřování Multi-Factor Authentication, doporučujeme tuto vlastnost nastavit na hodnotu, která je větší nebo rovna hodnotě vlastnosti maximální stáří tokenu relace s jedním faktorem.

## <a name="example-token-lifetime-policies"></a>Příklady zásad životnosti tokenů
V Azure AD můžete vytvářet a spravovat životnosti tokenů pro aplikace, instanční objekty a vaši celkovou organizaci. V této části si projdeme několik běžných scénářů zásad, které vám pomůžou stanovit nová pravidla pro:

* Živostnost tokenu
* Maximální neaktivní čas tokenu
* Maximální stáří tokenu

V příkladech se můžete dozvědět, jak:

* Správa výchozích zásad organizace
* Vytvoření zásady pro webové přihlašování
* Vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API
* Správa pokročilých zásad

### <a name="prerequisites"></a>Předpoklady
V následujících příkladech můžete vytvořit, aktualizovat, propojit a odstranit zásady pro aplikace, instanční objekty a celou organizaci. Pokud s Azure AD teprve začínáte, doporučujeme vám seznámit se s tím, [Jak získat tenanta Azure AD](quickstart-create-new-tenant.md) , než budete pokračovat v těchto příkladech.  

Začněte tím, že provedete následující kroky:

1. Stáhněte si nejnovější [verzi modulu Azure AD PowerShell Public Preview](https://www.powershellgallery.com/packages/AzureADPreview).
2. Spuštěním `Connect` příkazu se přihlaste ke svému účtu správce Azure AD. Spusťte tento příkaz pokaždé, když spustíte novou relaci.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Chcete-li zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte následující příkaz. Spusťte tento příkaz po většině operací v následujících scénářích. Spuštění příkazu vám také pomůže získat * * * * vašich zásad.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Příklad: Správa výchozích zásad organizace
V tomto příkladu vytvoříte zásadu, která umožňuje uživatelům méně často se přihlašovat napříč celou organizací. Provedete to tak, že vytvoříte zásadu životnosti tokenů pro tokeny aktualizace s jedním faktorem, který se používá v rámci vaší organizace. Zásady se aplikují na všechny aplikace ve vaší organizaci a na všechny instanční objekty, u kterých ještě není nastavená žádná sada zásad.

1. Vytvořte zásady životnosti tokenů.

    1. Nastavte token pro obnovení s jedním faktorem na "dokud neodvolávání". Platnost tokenu nevyprší, dokud nebude přístup odvolán. Vytvořte následující definici zásady:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Chcete-li vytvořit zásadu, spusťte následující příkaz:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Chcete-li odebrat jakékoli prázdné znaky, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Pokud chcete zobrazit novou zásadu a získat **objectID**této zásady, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Aktualizujte zásady.

    Můžete se rozhodnout, že první zásada, kterou jste nastavili v tomto příkladu, není tak striktní, jak vaše služba vyžaduje. Pokud chcete nastavit vypršení platnosti tokenu jednorázového obnovení do dvou dnů, spusťte následující příkaz:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Příklad: vytvoření zásady pro webové přihlašování

V tomto příkladu vytvoříte zásadu, která vyžaduje, aby se uživatelé ve vaší webové aplikaci ověřovali častěji. Tato zásada nastavuje dobu života tokenů přístupu a ID a maximální stáří tokenu relace Multi-Factor Service k instančnímu objektu vaší webové aplikace.

1. Vytvořte zásady životnosti tokenů.

    Tato zásada, pro webové přihlašování, nastavuje dobu života tokenu přístupu/ID a maximální stáří tokenu relace s jedním faktorem na dvě hodiny.

    1. Chcete-li vytvořit zásadu, spusťte tento příkaz:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Chcete-li zobrazit nové zásady a získat **objectID**zásad, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat **objectID** objektu služby.

    1. Pomocí rutiny [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) můžete zobrazit všechny instanční objekty vaší organizace nebo jeden instanční objekt.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. V případě objektu služby spusťte následující příkaz:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Příklad: vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API
V tomto příkladu vytvoříte zásadu, která vyžaduje, aby se uživatelé neověřovali méně často. Tato zásada také prodlouží dobu, po kterou může uživatel být neaktivní, než se uživatel musí znovu ověřit. Zásady se aplikují na webové rozhraní API. Když nativní aplikace požaduje webové rozhraní API jako prostředek, použije se tato zásada.

1. Vytvořte zásady životnosti tokenů.

    1. Pokud chcete vytvořit striktní zásadu pro webové rozhraní API, spusťte následující příkaz:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Pokud chcete zobrazit nové zásady, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Přiřaďte zásady k webovému rozhraní API. Také je nutné získat **identifikátor objectID** vaší aplikace. Pomocí rutiny [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) Najděte **identifikátor objectID**vaší aplikace nebo použijte [Azure Portal](https://portal.azure.com/).

    Získejte **objectID** vaší aplikace a přiřaďte zásady:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Příklad: Správa rozšířených zásad
V tomto příkladu vytvoříte několik zásad, abyste se dozvěděli, jak systém priorit funguje. Naučíte se také, jak spravovat víc zásad, které se aplikují na několik objektů.

1. Vytvořte zásady životnosti tokenů.

    1. Chcete-li vytvořit výchozí zásadu organizace, která nastaví životnost tokenu aktualizace Single-Factor na 30 dní, spusťte následující příkaz:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Pokud chcete zobrazit nové zásady, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Přiřaďte zásadu k instančnímu objektu.

    Nyní máte zásadu, která se vztahuje na celou organizaci. Je možné, že budete chtít zachovat tuto 30denní zásadu pro určitý instanční objekt, ale změnit výchozí zásadu organizace na horní limit "dokud neodvolán".

    1. Pokud chcete zobrazit všechny instanční objekty vaší organizace, použijte rutinu [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. V případě objektu služby spusťte následující příkaz:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Nastavte `IsOrganizationDefault` příznak na false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Vytvořit nové výchozí zásady pro organizaci:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Teď máte k dispozici původní zásady propojené s vaším instančním objektem a nové zásady se nastaví jako výchozí zásada vaší organizace. Je důležité si uvědomit, že zásady použité u instančních objektů mají přednost před výchozími zásadami organizace.

## <a name="cmdlet-reference"></a>Reference k rutinám

### <a name="manage-policies"></a>Správa zásad

Ke správě zásad můžete použít následující rutiny.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Vytvoří novou zásadu.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Pole dokument JSON, které obsahuje všechna pravidla zásad. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Řetězec názvu zásady |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Pokud je nastaveno na true, nastaví zásady jako výchozí zásady organizace. Pokud má hodnotu false, neprovede žádnou akci. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ zásady U životností tokenů vždy používejte "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> Volitelné |Nastaví alternativní ID pro zásadu. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Načte všechny zásady Azure AD nebo zadané zásady.

```powershell
Get-AzureADPolicy
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> Volitelné |**ObjectID (ID)** zásady, kterou chcete. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Načte všechny aplikace a instanční objekty, které jsou propojené se zásadou.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** zásady, kterou chcete. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aktualizuje existující zásady.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** zásady, kterou chcete. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Řetězec názvu zásady |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> Volitelné |Pole dokument JSON, které obsahuje všechna pravidla zásad. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> Volitelné |Pokud je nastaveno na true, nastaví zásady jako výchozí zásady organizace. Pokud má hodnotu false, neprovede žádnou akci. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> Volitelné |Typ zásady U životností tokenů vždy používejte "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> Volitelné |Nastaví alternativní ID pro zásadu. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Odstraní zadané zásady.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** zásady, kterou chcete. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Zásady použití
Pro zásady použití můžete použít následující rutiny.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Propojí zadané zásady s aplikací.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** aplikace | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**Identifikátor objectID** zásady | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Získá zásadu, která je přiřazena aplikaci.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** aplikace | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Odebere zásadu z aplikace.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** aplikace | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**Identifikátor objectID** zásady | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Zásady instančních objektů
Pro zásady instančního objektu můžete použít následující rutiny.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Propojí zadané zásady s instančním objektem.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** aplikace | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**Identifikátor objectID** zásady | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Načte všechny zásady propojené se zadaným objektem služby.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** aplikace | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Odebere zásadu ze zadaného instančního objektu.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** aplikace | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**Identifikátor objectID** zásady | `-PolicyId <ObjectId of Policy>` |

## <a name="license-requirements"></a>Licenční požadavky

Použití této funkce vyžaduje licenci Azure AD Premium P1. Správnou licenci pro vaše požadavky najdete v tématu [porovnání všeobecně dostupných funkcí edice Free a Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Zákazníci s [licencemi Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mají taky přístup k funkcím podmíněného přístupu.

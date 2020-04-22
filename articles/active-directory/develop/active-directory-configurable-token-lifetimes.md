---
title: Konfigurovatelné životnosti tokenů Azure AD
titleSuffix: Microsoft identity platform
description: Zjistěte, jak nastavit životnost tokenů vydaných službou Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f4138c4ae24ae599d4058c9fd06c33b69657fe38
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680074"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Konfigurovatelné životnosti tokenů ve službě Azure Active Directory (Preview)

U tokenu vydaného službou Azure Active Directory (Azure AD) je možné určit dobu životnosti. Životnost tokenů je možné nastavit u všech aplikací ve vaší organizaci, u aplikace pro více tenantů nebo pro konkrétní objekt služby ve vaší organizaci.

> [!IMPORTANT]
> Po vyslechnutí od zákazníků během předběžné verze jsme implementovali [možnosti správy relací ověřování](https://go.microsoft.com/fwlink/?linkid=2083106) v podmíněném přístupu Azure AD. Tuto novou funkci můžete použít ke konfiguraci životnosti obnovovacího tokenu nastavením frekvence přihlášení. května 2020 nebudete moci ke konfiguraci tokenů relace a aktualizace tokenů použít konfigurovatelné zásady životnosti tokenů. Po vyřazení můžete stále konfigurovat životnost přístupového tokenu.

Ve službě Azure AD představuje objekt zásad sadu pravidel, která jsou vynucena v jednotlivých aplikacích nebo ve všech aplikacích v organizaci. Každý typ zásadmá jedinečnou strukturu se sadou vlastností, které jsou použity na objekty, ke kterým jsou přiřazeny.

Zásadu můžete určit jako výchozí zásadu pro vaši organizaci. Zásada je použita pro všechny aplikace v organizaci, pokud není přepsána zásadou s vyšší prioritou. Můžete také přiřadit zásadu konkrétním aplikacím. Pořadí priorit se liší podle typu zásady.

> [!NOTE]
> Zásady životnosti konfigurovatelných tokenů platí jenom pro mobilní a desktopové klienty, kteří přistupují k prostředkům SharePointu Online a OneDrivu pro firmy, a nevztahuje se na relace webového prohlížeče.
> Pokud chcete spravovat dobu trvání relací webového prohlížeče pro SharePoint Online a OneDrive pro firmy, použijte funkci [životnosti relace podmíněného přístupu.](../conditional-access/howto-conditional-access-session-lifetime.md) Další informace o konfiguraci limitů nevyužitých relací najdete v [blogu SharePointu Online.](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)

## <a name="token-types"></a>Typy tokenů

Můžete nastavit zásady životnosti tokenů pro obnovovací tokeny, přístupové tokeny, tokeny SAML, tokeny relace a tokeny ID.

### <a name="access-tokens"></a>Přístupové tokeny

Klienti používají přístupové tokeny pro přístup k chráněnému prostředku. Přístupový token lze použít pouze pro určitou kombinaci uživatele, klienta a prostředku. Přístupové tokeny nelze odvolat a jsou platné až do vypršení jejich platnosti. Škodlivý objekt actor, který získal přístupový token, jej může použít po dobu jeho životnosti. Úprava životnosti přístupového tokenu je kompromisem mezi zlepšením výkonu systému a prodloužením doby, po kterou si klient zachová přístup po zakázání uživatelského účtu. Zlepšení výkonu systému je dosaženo snížením počtu, kolikrát klient potřebuje získat nový přístupový token.  Výchozí hodnota je 1 hodina - po 1 hodině musí klient použít obnovovací token k získání nového obnovovacího tokenu a přístupového tokenu. 

### <a name="saml-tokens"></a>Tokeny SAML

Tokeny SAML používají mnoho webových aplikací SAAS a získávají se pomocí koncového bodu protokolu SAML2 služby Azure Active Directory. Jsou také spotřebovány aplikacemi používajícími WS-Federation. Výchozí doba života tokenu je 1 hodina. Z pohledu aplikace je doba platnosti tokenu určena hodnotou NotOnOrAfter `<conditions …>` prvku v tokenu. Po uplynutí doby platnosti tokenu musí klient iniciovat nový požadavek na ověření, který bude často splněn bez interaktivního přihlášení v důsledku tokenu relace jednotného přihlášení (SSO).

Hodnota NotOnOrAfter lze změnit pomocí `AccessTokenLifetime` parametru `TokenLifetimePolicy`v . Bude nastavena na životnost nakonfigurované v zásadě, pokud existuje, plus hodiny zkosení faktor pět minut.

Všimněte si, že potvrzení subjektu `<SubjectConfirmationData>` NotOnOrAfter zadané v elementu není ovlivněno konfigurací Token Lifetime. 

### <a name="refresh-tokens"></a>Aktualizovat tokeny

Když klient získá přístupový token pro přístup k chráněnému prostředku, klient také obdrží obnovovací token. Obnovovací token se používá k získání nových párů tokenů přístupu a aktualizace, když vyprší platnost aktuálního přístupového tokenu. Obnovovací token je vázán na kombinaci uživatele a klienta. Obnovovací token lze [kdykoli odvolat](access-tokens.md#token-revocation)a platnost tokenu je kontrolována při každém použití tokenu.  Obnovovací tokeny nejsou odvolány při použití k načtení nových přístupových tokenů – je však osvědčeným postupem bezpečně odstranit starý token při získávání nového. 

Je důležité rozlišovat mezi důvěrnými klienty a veřejnými klienty, protože to má vliv na to, jak dlouho lze použít tokeny aktualizace. Další informace o různých typech klientů naleznete v [tématu RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Životnost tokenů s důvěrnými tokeny aktualizace klienta
Důvěrní klienti jsou aplikace, které mohou bezpečně uložit heslo klienta (tajný klíč). Mohou prokázat, že požadavky pocházejí ze zabezpečené klientské aplikace a nikoli z objektu actor se zlými úmysly. Například webová aplikace je důvěrný klient, protože může ukládat tajný klíč klienta na webový server. Není vystavena. Vzhledem k tomu, že tyto toky jsou bezpečnější, výchozí `until-revoked`životnost iontové tokeny aktualizace vydané pro tyto toky je , nelze změnit pomocí zásad a nebude odvolán na dobrovolné resetování hesla.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Životnost tokenů s tokeny aktualizace veřejného klienta

Veřejní klienti nemohou bezpečně uložit heslo klienta (tajný klíč). Například aplikace pro iOS/Android nemůže zamlžovat tajný klíč od vlastníka prostředku, takže je považován za veřejného klienta. Můžete nastavit zásady pro prostředky, aby se zabránilo obnovení tokeny z veřejných klientů starších než zadané období od získání nového páru tokenů přístupu/aktualizace. (Chcete-li to provést, použijte vlastnost Maximální`MaxInactiveTime`neaktivní čas obnovovacího tokenu ( ).) Zásady můžete také použít k nastavení období, po jehož uplynutí již nejsou přijímány tokeny aktualizace. (Chcete-li to provést, použijte vlastnost Maximální stáří obnovovacího tokenu.) Můžete upravit životnost obnovovacího tokenu pro řízení, kdy a jak často je uživatel povinen znovu zadat pověření, namísto tichého opětovného ověření při použití aplikace veřejného klienta.

> [!NOTE]
> Max Age Vlastnost je doba, po kterou lze použít jeden token. 

### <a name="id-tokens"></a>Tokeny ID
ID tokeny jsou předávány webům a nativním klientům. ID tokeny obsahují informace o profilu uživatele. Token ID je vázán na určitou kombinaci uživatele a klienta. ID tokeny jsou považovány za platné až do vypršení jejich platnosti. Webová aplikace obvykle odpovídá životnosti relace uživatele v aplikaci k životnosti tokenu ID vydaného pro uživatele. Můžete upravit životnost tokenu ID řídit, jak často webové aplikace vyprší relace aplikace a jak často vyžaduje, aby uživatel znovu ověřit pomocí Azure AD (buď tiše nebo interaktivně).

### <a name="single-sign-on-session-tokens"></a>Tokeny relace jednotného přihlášení
Když se uživatel ověří pomocí služby Azure AD, vytvoří se relace jednotného přihlašování (SSO) pomocí prohlížeče uživatele a služby Azure AD. Token spřiho moc ve formě souboru cookie představuje tuto relaci. Token relace s přihlašování není vázán na konkrétní prostředek/klientskou aplikaci. Tokeny relace přihlašování lze odvolat a jejich platnost je kontrolována při každém použití.

Azure AD používá dva druhy tokenů relace přizpůsobování služby zabezpečení: trvalé a trvalé. Tokeny trvalé relace jsou prohlížečem uloženy jako trvalé soubory cookie. Tokeny netrvalé relace jsou uloženy jako soubory cookie relace. (Soubory cookie relace jsou zničeny při zavření prohlížeče.) Token netrvalé relace je obvykle uložen. Ale když uživatel vybere zaškrtávací políčko **Zachovat přihlášení** během ověřování, je uložen trvalý token relace.

Netrvalé tokeny relace mají životnost 24 hodin. Trvalé tokeny mají životnost 180 dní. Kdykoli se během doby platnosti použije token relace spřiho platby, prodlužuje se doba platnosti o dalších 24 hodin nebo 180 dní v závislosti na typu tokenu. Pokud token relace s přihlašování není použit v rámci jeho doby platnosti, je považován za vypršela a již není přijat.

Zásadu můžete použít k nastavení času po vydání tokenu první relace, po jehož uplynutí již není token relace přijat. (Chcete-li to provést, použijte vlastnost Maximální věk tokenu relace.) Můžete upravit životnost tokenu relace řídit, kdy a jak často je uživatel povinen znovu zadat pověření, namísto tiše ověřené, při použití webové aplikace.

### <a name="token-lifetime-policy-properties"></a>Vlastnosti zásad y životnosti tokenu
Zásada životnosti tokenu je typ objektu zásad, který obsahuje pravidla životnosti tokenu. Pomocí vlastností zásady můžete řídit zadanou životnost tokenu. Pokud není nastavena žádná zásada, systém vynucuje výchozí hodnotu životnosti.

### <a name="configurable-token-lifetime-properties"></a>Konfigurovatelné vlastnosti životnosti tokenu
| Vlastnost | Řetězec vlastnosti zásad | Ovlivňuje | Výchozí | Minimální | Maximum |
| --- | --- | --- | --- | --- | --- |
| Životnost přístupového tokenu |AccessTokenLifetime<sup>2</sup> |Přístupové tokeny, ID tokeny, tokeny SAML2 |1 hodina |10 minut |1 den |
| Maximální neaktivní čas obnovovacího tokenu |Maximální neaktivní čas |Aktualizovat tokeny |90 dnů |10 minut |90 dnů |
| Maximální stáří obnovovacího tokenu s jedním faktorem |MaxAgeSingleFactor |Aktualizovat tokeny (pro všechny uživatele) |Dokud nebyla odvolána |10 minut |Dokud nebyl a)<sup>1</sup> |
| Maximální stáří obnovovacího tokenu s více faktory |MaxAgeMultiFactor |Aktualizovat tokeny (pro všechny uživatele) |Dokud nebyla odvolána |10 minut |Dokud nebyl a)<sup>1</sup> |
| Maximální stáří tokenu relace s jedním faktorem |MaxAgeSessionSingleFactor |Tokeny relace (trvalé a trvalé) |Dokud nebyla odvolána |10 minut |Dokud nebyl a)<sup>1</sup> |
| Maximální stáří tokenu relace s více faktory |MaxAgeSessionMultiFactor |Tokeny relace (trvalé a trvalé) |Dokud nebyla odvolána |10 minut |Dokud nebyl a)<sup>1</sup> |

* <sup>1</sup>365 dní je maximální explicitní délka, kterou lze nastavit pro tyto atributy.
* <sup>2.</sup> Chcete-li zajistit, aby webový klient Microsoft Teams fungoval, doporučujeme zachovat službu AccessTokenLifetime na více než 15 minut pro microsoft teams.

### <a name="exceptions"></a>Výjimky
| Vlastnost | Ovlivňuje | Výchozí |
| --- | --- | --- |
| Maximální stáří obnovovacího tokenu (vydáno pro federované uživatele, kteří nemají dostatečné informace o odvolání<sup>1</sup>) |Obnovovací tokeny (vydané pro federované uživatele, kteří nemají dostatečné informace o odvolání<sup>1</sup>) |12 hodin |
| Maximální neaktivní čas obnovovacího tokenu (vydan pro důvěrné klienty) |Obnovovací tokeny (vydané pro důvěrné klienty) |90 dnů |
| Maximální věk obnovovacího tokenu (vydanpro důvěrné klienty) |Obnovovací tokeny (vydané pro důvěrné klienty) |Dokud nebyla odvolána |

* <sup>1.</sup> Federovaní uživatelé, kteří nemají dostatečné informace o odvolání, zahrnují všechny uživatele, kteří nemají synchronizován atribut LastPasswordChangeTimestamp. Tito uživatelé jsou uvedeny tento krátký maximální stáří, protože AAD není schopen ověřit, kdy odvolat tokeny, které jsou vázány na staré pověření (například heslo, které bylo změněno) a musí vrátit se změnami častěji zajistit, že uživatel a přidružené tokeny jsou stále v dobrém stavu. Chcete-li zlepšit toto prostředí, musí správci tenanta zajistit, aby synchronizovali atribut LastPasswordChangeTimestamp (to lze nastavit u objektu uživatele pomocí prostředí Powershell nebo prostřednictvím aplikace AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Hodnocení politiky a stanovení priorit
Můžete vytvořit a pak přiřadit zásady životnosti tokenu konkrétní aplikaci, vaší organizaci a instančním objektům. Pro konkrétní aplikaci může platit více zásad. Zásady životnosti tokenu, které se projeví, se řídí těmito pravidly:

* Pokud je zásada explicitně přiřazena instančnímu objektu, je vynucena.
* Pokud není objektu zabezpečení služby explicitně přiřazena žádná zásada, je vynucena zásada explicitně přiřazená nadřazené organizaci instančního objektu.
* Pokud není explicitně přiřazena žádná zásada instančnímu objektu nebo organizaci, je vynucena zásada přiřazená k aplikaci.
* Pokud nebyla přiřazena žádná zásada instančnímu objektu, organizaci nebo aplikačnímu objektu, budou vynuceny výchozí hodnoty. (Viz tabulka v [konfigurovatelných vlastnostech životnosti tokenu](#configurable-token-lifetime-properties).)

Další informace o vztahu mezi aplikačními objekty a objekty instančního objektu naleznete [v tématu Application and service principal objects in Azure Active Directory](app-objects-and-service-principals.md).

Platnost tokenu je vyhodnocena v době použití tokenu. Zásady s nejvyšší prioritou v aplikaci, která je přístupná, se projeví.

Všechny časové rozsahy, které jsou zde použity, jsou formátovány podle objektu C# [TimeSpan](/dotnet/api/system.timespan) - D.HH:MM:SS.  Takže 80 dní a 30 minut by bylo `80.00:30:00`.  Vedoucí D může být vynechán, pokud nula, takže 90 minut by `00:90:00`bylo .  

> [!NOTE]
> Zde je příklad scénáře.
>
> Uživatel chce získat přístup ke dvěma webovým aplikacím: webové aplikaci A a webové aplikaci B.
> 
> Faktory:
> * Obě webové aplikace jsou ve stejné nadřazené organizaci.
> * Zásada životnosti tokenu 1 s maximálním stářím tokenu relace osm hodin je nastavena jako výchozí hodnota nadřazené organizace.
> * Webová aplikace A je webová aplikace s běžným používáním a není propojena s žádnými zásadami.
> * Webová aplikace B se používá pro vysoce citlivé procesy. Jeho instanční objekt je propojen s zásadou životnosti tokenu 2, která má maximální stáří tokenu relace 30 minut.
>
> Ve 12:00 uživatel spustí novou relaci prohlížeče a pokusí se o přístup k webové aplikaci A. Uživatel je přesměrován na Azure AD a je požádán o přihlášení. Tím se vytvoří soubor cookie, který má token relace v prohlížeči. Uživatel je přesměrován zpět na webovou aplikaci A s tokenem ID, který umožňuje uživateli přístup k aplikaci.
>
> Ve 12:15 se uživatel pokusí o přístup k webové aplikaci B. Prohlížeč přesměruje na Azure AD, který zjistí, že soubor cookie relace. Instanční objekt webové aplikace B je propojen se zásadami životnosti tokenu 2, ale je také součástí nadřazené organizace s výchozím zásadami životnosti tokenu 1. Zásady životnosti tokenu 2 se projeví, protože zásady propojené s instančními objekty mají vyšší prioritu než výchozí zásady organizace. Token relace byl původně vydán během posledních 30 minut, takže je považován za platný. Uživatel je přesměrován zpět na webovou aplikaci B s tokenem ID, který mu uděluje přístup.
>
> V 13:00 se uživatel pokusí o přístup k webové aplikaci A. Uživatel je přesměrován na Azure AD. Webová aplikace A není propojena s žádnými zásadami, ale protože je v organizaci s výchozí zásadou životnosti tokenu 1, tato zásada se projeví. Soubor cookie relace, který byl původně vydán během posledních osmi hodin, je zjištěn. Uživatel je tiše přesměrován zpět na webovou aplikaci A s novým tokenem ID. Uživatel není povinen ověřit.
>
> Ihned poté se uživatel pokusí o přístup k webové aplikaci B. Uživatel je přesměrován na Azure AD. Stejně jako dříve se projeví zásady životnosti tokenu 2. Vzhledem k tomu, že token byl vydán před více než 30 minutami, uživatel je vyzván k opětovnému zadání přihlašovacích údajů. Zcela nový token relace a token ID jsou vydány. Uživatel pak může přistupovat k webové aplikaci B.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurovatelné podrobnosti o vlastnostech zásad
### <a name="access-token-lifetime"></a>Životnost přístupového tokenu
**Řetězec:** AccessTokenLifetime

**Ovlivňuje:** Přístupové tokeny, ID tokeny, tokeny SAML

**Shrnutí:** Tato zásada určuje, jak dlouho přístup a ID tokeny pro tento prostředek jsou považovány za platné. Snížení vlastnosti Životnost přístupového tokenu snižuje riziko použití přístupového tokenu nebo tokenu ID škodlivým objektem actor po delší dobu. (Tyto tokeny nelze odvolat.) Kompromis je, že výkon je nepříznivě ovlivněna, protože tokeny musí být nahrazeny častěji.

### <a name="refresh-token-max-inactive-time"></a>Maximální neaktivní čas obnovovacího tokenu
**Řetězec:** Maximální neaktivní čas

**Ovlivňuje:** Aktualizovat tokeny

**Shrnutí:** Tato zásada určuje, jak starý může být obnovovací token, než jej klient nebude moci použít k načtení nového páru tokenů přístupu a aktualizace při pokusu o přístup k tomuto prostředku. Vzhledem k tomu, že nový obnovovací token je obvykle vrácen při použití obnovovacího tokenu, tato zásada zabrání přístupu, pokud se klient pokusí o přístup k libovolnému prostředku pomocí aktuálního obnovovacího tokenu během zadaného časového období.

Tato zásada vynutí uživatelům, kteří nebyli aktivní na svém klientovi, aby znovu ověřili, aby načetli nový obnovovací token.

Vlastnost Aktualizovat token max neaktivní čas musí být nastavena na nižší hodnotu, než je maximální stáří tokenu s jedním faktorem a vlastnosti Maximální stáří obnovovacího tokenu s více faktory.

### <a name="single-factor-refresh-token-max-age"></a>Maximální stáří obnovovacího tokenu s jedním faktorem
**Řetězec:** MaxAgeSingleFactor

**Ovlivňuje:** Aktualizovat tokeny

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel použít obnovovací token k získání nového páru tokenů přístupu a aktualizace poté, co byl naposledy úspěšně ověřen pomocí pouze jednoho faktoru. Poté, co uživatel ověří a obdrží nový obnovovací token, může uživatel použít tok obnovovacího tokenu pro zadané časové období. (To platí, dokud není odvolán aktuální obnovovací token a není ponechán nepoužívaný déle než neaktivní čas.) V tomto okamžiku je uživatel nucen znovu ověřit přijímat nový obnovovací token.

Snížení maximálního věku nutí uživatele k ověřování častěji. Vzhledem k tomu, že jednofaktorové ověřování je považováno za méně zabezpečené než vícefaktorové ověřování, doporučujeme nastavit tuto vlastnost na hodnotu, která je stejná nebo menší než vlastnost Max Age tokenu aktualizace více faktorů.

### <a name="multi-factor-refresh-token-max-age"></a>Maximální stáří obnovovacího tokenu s více faktory
**Řetězec:** MaxAgeMultiFactor

**Ovlivňuje:** Aktualizovat tokeny

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel použít obnovovací token k získání nového páru tokenů přístupu a aktualizace poté, co byl naposledy úspěšně ověřen pomocí více faktorů. Poté, co uživatel ověří a obdrží nový obnovovací token, může uživatel použít tok obnovovacího tokenu pro zadané časové období. (To platí, dokud není odvolán aktuální obnovovací token a není nepoužívaný déle než neaktivní čas.) V tomto okamžiku jsou uživatelé nuceni znovu ověřit přijímat nový obnovovací token.

Snížení maximálního věku nutí uživatele k ověřování častěji. Vzhledem k tomu, že jednofaktorové ověřování je považováno za méně zabezpečené než vícefaktorové ověřování, doporučujeme nastavit tuto vlastnost na hodnotu, která je stejná nebo větší než vlastnost Max Age tokenu aktualizace s jedním faktorem.

### <a name="single-factor-session-token-max-age"></a>Maximální stáří tokenu relace s jedním faktorem
**Řetězec:** MaxAgeSessionSingleFactor

**Ovlivňuje:** Tokeny relace (trvalé a trvalé)

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel používat token relace k získání nového ID a tokenu relace poté, co byly naposledy úspěšně ověřeny pomocí pouze jednoho faktoru. Poté, co uživatel ověří a obdrží nový token relace, může uživatel použít tok tokenu relace pro zadané časové období. (To platí, dokud není odvolán aktuální token relace a jeho platnost nevypršela.) Po uplynutí zadaného časového období je uživatel nucen znovu ověřit, aby obdržel nový token relace.

Snížení maximálního věku nutí uživatele k ověřování častěji. Vzhledem k tomu, že jednofaktorové ověřování je považováno za méně zabezpečené než vícefaktorové ověřování, doporučujeme nastavit tuto vlastnost na hodnotu, která je rovna nebo menší než vlastnost Max Age tokenu relace s více faktory.

### <a name="multi-factor-session-token-max-age"></a>Maximální stáří tokenu relace s více faktory
**Řetězec:** MaxAgeSessionMultiFactor

**Ovlivňuje:** Tokeny relace (trvalé a trvalé)

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel používat token relace k získání nového ID a tokenu relace po posledním úspěšném ověření pomocí více faktorů. Poté, co uživatel ověří a obdrží nový token relace, může uživatel použít tok tokenu relace pro zadané časové období. (To platí, dokud není odvolán aktuální token relace a jeho platnost nevypršela.) Po uplynutí zadaného časového období je uživatel nucen znovu ověřit, aby obdržel nový token relace.

Snížení maximálního věku nutí uživatele k ověřování častěji. Vzhledem k tomu, že jednofaktorové ověřování je považováno za méně zabezpečené než vícefaktorové ověřování, doporučujeme nastavit tuto vlastnost na hodnotu, která je stejná nebo větší než vlastnost Maximální stáří tokenu relace s jedním faktorem.

## <a name="example-token-lifetime-policies"></a>Příklad zásady životnosti tokenu
Mnoho scénářů je možné ve službě Azure AD, když můžete vytvořit a spravovat životnost tokenů pro aplikace, instanční objekty a celkovou organizaci. V této části procházíme několika běžnými scénáři zásad, které vám mohou pomoci zavést nová pravidla pro:

* Živostnost tokenu
* Maximální neaktivní čas tokenu
* Maximální věk tokenu

V příkladech se můžete dozvědět, jak:

* Správa výchozích zásad organizace
* Vytvoření zásady pro webové přihlášení
* Vytvoření zásad pro nativní aplikaci, která volá webové rozhraní API
* Správa pokročilých zásad

### <a name="prerequisites"></a>Požadavky
V následujících příkladech vytvoříte, aktualizujete, propojíte a odstraníte zásady pro aplikace, instanční objekty a celkovou organizaci. Pokud jste novým Azure AD, doporučujeme vám, abyste se dozvěděli, [jak získat klienta Azure AD,](quickstart-create-new-tenant.md) než budete pokračovat v těchto příkladech.  

Chcete-li začít, postupujte takto:

1. Stáhněte si nejnovější [verzi Azure AD PowerShell Module Public Preview](https://www.powershellgallery.com/packages/AzureADPreview).
2. Spusťte `Connect` příkaz pro přihlášení k účtu správce Azure AD. Spusťte tento příkaz při každém spuštění nové relace.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Chcete-li zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte následující příkaz. Spusťte tento příkaz po většině operací v následujících scénářích. Spuštění příkazu také vám pomůže získat ** ** vašich zásad.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Příklad: Správa výchozích zásad organizace
V tomto příkladu vytvoříte zásadu, která umožňuje přihlášení uživatelů méně často v celé organizaci. Chcete-li to provést, vytvořte zásadu životnosti tokenu pro tokeny aktualizace s jedním faktorem, která se použije v celé organizaci. Zásady se použijí pro každou aplikaci ve vaší organizaci a pro každý instanční objekt, který ještě nemá sadu zásad.

1. Vytvořte zásadu životnosti tokenu.

    1. Nastavte jednofaktorový obnovovací token na "dokud-revoked." Platnost tokenu nevyprší, dokud není přístup odvolán. Vytvořte následující definici zásad:

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

    1. Chcete-li odstranit všechny prázdné znaky, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Chcete-li zobrazit nové zásady a získat **zásady ObjectId**, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Aktualizujte zásadu.

    Můžete se rozhodnout, že první zásady nastavené v tomto příkladu nejsou tak přísné, jak vyžaduje vaše služba. Chcete-li nastavit platnost jednofaktorového obnovovacího tokenu za dva dny, spusťte následující příkaz:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Příklad: Vytvoření zásady pro webové přihlášení

V tomto příkladu vytvoříte zásadu, která vyžaduje, aby se uživatelé ověřovali častěji ve vaší webové aplikaci. Tato zásada nastaví životnost tokenů přístupu/ID a maximální stáří vícefaktorového tokenu relace na instanční objekt webové aplikace.

1. Vytvořte zásadu životnosti tokenu.

    Tato zásada pro webové přihlašování nastaví životnost tokenu přístupu/ID a maximální stáří tokenu relace s jedním faktorem na dvě hodiny.

    1. Chcete-li vytvořit zásadu, spusťte tento příkaz:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Chcete-li zobrazit novou zásadu a získat zásadu **ObjectId**, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Přiřaďte zásadu k instančnímu objektu. Také je nutné získat **ObjectId** instančního objektu.

    1. Pomocí rutiny [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) zobrazíte všechny instanční objekty vaší organizace nebo jeden instanční objekt.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Pokud máte instanční objekt, spusťte následující příkaz:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Příklad: Vytvoření zásadpro nativní aplikaci, která volá webové rozhraní API
V tomto příkladu vytvoříte zásadu, která vyžaduje, aby se uživatelé ověřovali méně často. Zásada také prodlužuje dobu, po kterou může být uživatel neaktivní, než se uživatel musí znovu ověřit. Zásada se použije pro webové rozhraní API. Když nativní aplikace požaduje webové rozhraní API jako prostředek, použije se tato zásada.

1. Vytvořte zásadu životnosti tokenu.

    1. Chcete-li vytvořit přísné zásady pro webové rozhraní API, spusťte následující příkaz:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Chcete-li zobrazit novou zásadu, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Přiřaďte zásady k webovému rozhraní API. Také je nutné získat **ObjectId** vaší aplikace. Pomocí rutiny [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) vyhledejte objekt **ObjectId**aplikace nebo použijte [portál Azure](https://portal.azure.com/).

    Získejte **ObjectId** aplikace a přiřadit zásady:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Příklad: Správa upřesňujících zásad
V tomto příkladu vytvoříte několik zásad, které se dozvíte, jak funguje systém priorit. Dozvíte se také, jak spravovat více zásad, které jsou použity pro několik objektů.

1. Vytvořte zásadu životnosti tokenu.

    1. Chcete-li vytvořit výchozí zásadu organizace, která nastaví životnost jednofaktorového obnovovacího tokenu na 30 dní, spusťte následující příkaz:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Chcete-li zobrazit novou zásadu, spusťte následující příkaz:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Přiřaďte zásadu instančnímu objektu.

    Nyní máte zásady, které platí pro celou organizaci. Můžete chtít zachovat tuto 30denní zásadu pro konkrétní instanční objekt, ale změnit výchozí zásady organizace na horní limit "do odvolání".

    1. Chcete-li zobrazit všechny instanční objekty vaší organizace, použijte rutinu [Get-AzureADServicePrincipal.](/powershell/module/azuread/get-azureadserviceprincipal)

    1. Pokud máte instanční objekt, spusťte následující příkaz:

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

1. Vytvořte novou výchozí zásadu organizace:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Nyní máte původní zásady propojené s instančním objektem a nové zásady jsou nastaveny jako výchozí zásady vaší organizace. Je důležité si uvědomit, že zásady použité na instanční objekty mají přednost před výchozími zásadami organizace.

## <a name="cmdlet-reference"></a>Reference k rutinám

### <a name="manage-policies"></a>Správa zásad

Ke správě zásad můžete použít následující rutiny.

#### <a name="new-azureadpolicy"></a>Nová azureadpolicy

Vytvoří novou zásadu.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Pole stringified JSON, který obsahuje všechna pravidla zásady. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Řetězec názvu zásady. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Pokud true, nastaví zásady jako výchozí zásady organizace. Pokud je false, nedělá nic. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ zásady. Pro životnost tokenu vždy použijte "TokenLifetimePolicy." | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Nepovinné] |Nastaví alternativní ID pro zásadu. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Zásady získání AzureAD
Získá všechny zásady Azure AD nebo zadané zásady.

```powershell
Get-AzureADPolicy
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code>[Nepovinné] |**ObjectId (ID)** požadované zásady. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObjectObject Get-AzureaDPolicyAppliedObject Object Get-AzureaDPolicyAppliedObject
Získá všechny aplikace a instanční objekty, které jsou propojeny se zásadou.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** požadované zásady. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Zásady nastavení AzureaD
Aktualizuje existující zásady.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** požadované zásady. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Řetězec názvu zásady. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Nepovinné] |Pole stringified JSON, který obsahuje všechna pravidla zásady. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Nepovinné] |Pokud true, nastaví zásady jako výchozí zásady organizace. Pokud je false, nedělá nic. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Nepovinné] |Typ zásady. Pro životnost tokenu vždy použijte "TokenLifetimePolicy." |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Nepovinné] |Nastaví alternativní ID pro zásadu. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Odebrat azureadpolicy
Odstraní zadanou zásadu.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** požadované zásady. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Zásady použití
Pro zásady aplikace můžete použít následující rutiny.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Zásady add-AzureADApplicationPolicy
Propojuje zadanou zásadu s aplikací.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** zásady. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Zásady získání azureadaplikací
Získá zásadu, která je přiřazena k aplikaci.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikace. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Odebrat azureadaplikační zásady
Odebere zásadu z aplikace.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** zásady. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Zásady zaregistrovaný objekt service
Následující rutiny můžete použít pro zásady instančního objektu.

#### <a name="add-azureadserviceprincipalpolicy"></a>Zásady zásady umělá vykonavatela služby Přidání AzureaD
Propojuje zadanou zásadu s instančním objektem.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** zásady. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Zásady hlavní hospo-nastavení azuread
Získá všechny zásady propojené se zadaným instančním objektem.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikace. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Odebrat zásady hlavní ho správě AzureAD
Odebere zásadu ze zadaného instančního objektu.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametry | Popis | Příklad |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** zásady. | `-PolicyId <ObjectId of Policy>` |

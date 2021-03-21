---
title: Konfigurovatelné životnosti tokenů
titleSuffix: Microsoft identity platform
description: Naučte se, jak nastavit životnost pro přístup, SAML a tokeny ID vydávané platformou Microsoft identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 374e8bb61886a78289fcf44ce9582df325a4e64c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549035"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Konfigurovatelné životnosti tokenů na platformě Microsoft identity (verze Preview)

Můžete zadat dobu života přístupu, ID nebo tokenu SAML vydaného platformou Microsoft identity. Životnost tokenů je možné nastavit u všech aplikací ve vaší organizaci, u aplikace pro více tenantů nebo pro konkrétní objekt služby ve vaší organizaci. V současné době ale nepodporujeme konfiguraci životností tokenů pro [spravované objekty služby identity](../managed-identities-azure-resources/overview.md).

V Azure AD představuje objekt zásad sadu pravidel, která se vynutila pro jednotlivé aplikace nebo pro všechny aplikace v organizaci. Každý typ zásad má jedinečnou strukturu se sadou vlastností, které jsou aplikovány na objekty, ke kterým jsou přiřazeny.

Zásady můžete určit jako výchozí zásady pro vaši organizaci. Zásada se použije u libovolné aplikace v organizaci, pokud není přepsána zásadou s vyšší prioritou. Můžete také přiřadit zásadu konkrétním aplikacím. Pořadí priority se liší podle typu zásad.

Příklady si můžete přečíst v tématu [Příklady konfigurace životnosti tokenů](configure-token-lifetimes.md).

> [!NOTE]
> Konfigurovatelné zásady životnosti tokenů platí jenom pro mobilní a desktopové klienty, kteří mají přístup k prostředkům SharePointu Online a OneDrive pro firmy, a nevztahují se na relace webového prohlížeče.
> Pokud chcete spravovat životnost relací webového prohlížeče pro SharePoint Online a OneDrive pro firmy, použijte funkci [Životnost relace podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md) . Další informace o konfiguraci časových limitů nečinných relací najdete v [blogu k SharePointu Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) .

## <a name="license-requirements"></a>Licenční požadavky

Použití této funkce vyžaduje licenci Azure AD Premium P1. Správnou licenci pro vaše požadavky najdete v tématu [porovnání všeobecně dostupných funkcí edice Free a Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Zákazníci s [licencemi Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) mají taky přístup k funkcím podmíněného přístupu.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Zásady životnosti tokenů pro přístup, SAML a tokeny ID

Můžete nastavit zásady životnosti tokenů pro přístupové tokeny, tokeny SAML a tokeny ID. 

### <a name="access-tokens"></a>Přístupové tokeny

Klienti používají přístupové tokeny pro přístup k chráněnému prostředku. Přístupový token se dá použít jenom pro konkrétní kombinaci uživatele, klienta a prostředku. Přístupové tokeny nejde odvolat a jsou platné, dokud nevyprší jejich platnost. Škodlivý objekt actor, který získal přístupový token, ho může použít pro rozsah své životnosti. Úprava životnosti přístupového tokenu je kompromis mezi zvýšením výkonu systému a zvýšením doby, po kterou klient zachovává přístup po zakázání účtu uživatele. Vylepšený výkon systému se dosahuje snížením počtu pokusů, kolikrát klient potřebuje získat nový přístupový token.  Výchozí hodnota je 1 hodina – po 1 hodině musí klient použít obnovovací token (obvykle v tichém režimu) získat nový obnovovací token a přístupový token.

### <a name="saml-tokens"></a>Tokeny SAML

Tokeny SAML jsou používány mnoha webovými SaaS aplikacemi a jsou získány pomocí koncového bodu protokolu typu Saml2 v systému Azure Active Directory. Využívají je i aplikace, které používají WS-Federation. Výchozí doba platnosti tokenu je 1 hodina. V perspektivě aplikace je doba platnosti tokenu určena hodnotou NotOnOrAfter `<conditions …>` elementu v tokenu. Po ukončení doby platnosti tokenu musí klient iniciovat nový požadavek na ověření, který se často splní bez interaktivního přihlášení v důsledku tokenu relace jednotného přihlašování (SSO).

Hodnotu NotOnOrAfter lze změnit pomocí `AccessTokenLifetime` parametru v `TokenLifetimePolicy` . Nastaví se na životní cyklus nakonfigurovanou v zásadě, pokud existuje, a koeficient pro zešikmení s hodinami 5 minut.

Potvrzení předmětu NotOnOrAfter zadané v elementu není `<SubjectConfirmationData>` ovlivněné konfigurací životnosti tokenu. 

### <a name="id-tokens"></a>Tokeny ID

Tokeny ID jsou předány webům a nativním klientům. Tokeny ID obsahují informace o profilu uživatele. Token ID je vázán na určitou kombinaci uživatele a klienta. Tokeny ID se považují za platné, dokud nevyprší jejich platnost. Obvykle webová aplikace odpovídá době životnosti relace uživatele v aplikaci až po dobu životnosti tokenu ID vydaného pro uživatele. Můžete upravit životnost tokenu ID, abyste mohli určit, jak často webová aplikace vyprší aplikační relaci, a jak často je potřeba, aby se uživatel znovu ověřil s platformou Microsoft identity (ať už v tichém nebo interaktivním).

### <a name="token-lifetime-policy-properties"></a>Vlastnosti zásad životnosti tokenů

Zásada životního cyklu tokenu je typ objektu zásad, který obsahuje pravidla životnosti tokenů. Tato zásada určuje, jak dlouho jsou tokeny pro přístup, SAML a ID pro tento prostředek považovány za platné. Pokud nejsou nastavené žádné zásady, systém vyhodnotí výchozí hodnotu doby života. 

Omezení vlastnosti životnosti přístupového tokenu snižuje riziko zneužití přístupového tokenu nebo tokenu ID škodlivým objektem actor po delší dobu. (Tyto tokeny nejde odvolat.) Při obchodování je nepříznivě ovlivněn výkon, protože tokeny musí být nahrazeny častěji.

Příklad najdete v tématu [Vytvoření zásady pro web signing-in](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

| Vlastnost | Řetězec vlastnosti zásad | Ovlivňuje | Výchozí | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Doba života přístupového tokenu |AccessTokenLifetime |Přístupové tokeny, tokeny ID, tokeny typu Saml2 |1 hodina |10 minut |1 den |

> [!NOTE]
> Chcete-li zajistit, aby webový klient Microsoft Teams funguje, doporučujeme, abyste pro Microsoft Teams AccessTokenLifetime více než 15 minut.

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Zásady životnosti tokenů pro aktualizační tokeny a tokeny relací

Nemůžete nastavit zásady životnosti tokenů pro aktualizační tokeny a tokeny relací.

> [!IMPORTANT]
> Od 30. ledna 2021 nemůžete konfigurovat aktualizace a životnosti tokenů relace. Azure Active Directory už v existujících zásadách nedodržuje aktualizace a konfiguraci tokenu relace.  Nové tokeny vydané po vypršení platnosti existujících tokenů jsou teď nastavené na [výchozí konfiguraci](#configurable-token-lifetime-properties-after-the-retirement). Po dokončení aktualizace a vyřazení konfigurace tokenu relace můžete i nadále konfigurovat přístup, SAML a životnosti tokenů ID.
>
> Doba života existujícího tokenu se nezmění. Po vypršení platnosti se na základě výchozí hodnoty vygeneruje nový token.
>
> Pokud potřebujete pokračovat v definování časového období, než se uživateli zobrazí výzva k opětovnému přihlášení, nakonfigurujte četnost přihlášení v podmíněném přístupu. Pokud chcete získat další informace o podmíněném přístupu, přečtěte si téma [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md).

:::image type="content" source="./media/active-directory-configurable-token-lifetimes/roadmap.svg" alt-text="Informace o vyřazení":::

### <a name="refresh-tokens"></a>Aktualizovat tokeny

Když klient získá přístupový token pro přístup k chráněnému prostředku, klient obdrží také obnovovací token. Obnovovací token slouží k získání nových párů tokenů přístupu a aktualizace po vypršení platnosti aktuálního přístupového tokenu. Obnovovací token je vázán na kombinaci uživatele a klienta. Obnovovací token můžete kdykoli [odvolat](access-tokens.md#token-revocation)a platnost tokenu se kontroluje při každém použití tokenu.  Při použití k načtení nových přístupových tokenů se nepovedlo aktualizovat tokeny – doporučujeme ale při získání nového tokenu bezpečně odstranit starý.

Je důležité rozlišovat mezi důvěrnými a veřejnými klienty, protože to ovlivňuje, jak se dají použít dlouhotrvající tokeny aktualizace. Další informace o různých typech klientů najdete v [dokumentu RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Životnost tokenů s důvěrnými aktualizačními tokeny klienta
Důvěrné klienty jsou aplikace, které můžou bezpečně ukládat heslo klienta (tajné). Mohou prokázat, že požadavky pocházejí z zabezpečené klientské aplikace a nikoli ze škodlivého objektu actor. Webová aplikace je například důvěrný klient, protože může ukládat tajný klíč klienta na webový server. Nezveřejňuje se. Vzhledem k tomu, že jsou tyto toky bezpečnější, je výchozí doba platnosti obnovovacích tokenů vydaných pro tyto toky `until-revoked` , nedá se změnit pomocí zásad a nebude se odvolat při dobrovolných resetování hesla.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Životnost tokenů s obnovovacími tokeny veřejného klienta

Veřejné klienty nemohou bezpečně ukládat heslo klienta (tajný kód). Například aplikace pro iOS/Android nemůže odvažovat tajný klíč od vlastníka prostředku, takže se považuje za veřejného klienta. Můžete nastavit zásady pro prostředky, aby se zabránilo aktualizačním tokenům z veřejných klientů starších než v zadaném období, od získání nového páru přístupů nebo obnovovacího tokenu. Chcete-li to provést, použijte [vlastnost Max neaktivního času obnovovacího tokenu](#refresh-token-max-inactive-time) ( `MaxInactiveTime` ). Pomocí zásad můžete také nastavit dobu, po kterou se obnovovací tokeny už nepřijímají. Chcete-li to provést, použijte vlastnost [maximální stáří tokenu aktualizace s jedním koeficientem](#single-factor-session-token-max-age) nebo [token relace ve více faktorech](#multi-factor-refresh-token-max-age) . Dobu platnosti obnovovacího tokenu můžete upravit tak, aby bylo možné řídit, kdy a jak často musí uživatel při použití veřejné klientské aplikace znovu zadat přihlašovací údaje.

Vlastnost maximální stáří představuje dobu, po kterou lze použít jeden token. 

### <a name="single-sign-on-session-tokens"></a>Tokeny relace jednotného přihlašování
Když se uživatel s platformou Microsoft Identity ověřuje, vytvoří se v prohlížeči uživatele a platformě Microsoft Identity jediná přihlašovací relace (SSO). Token jednotného přihlašování ve formě souboru cookie představuje tuto relaci. Token relace jednotného přihlašování není vázán na konkrétní prostředek nebo klientskou aplikaci. Tokeny relace jednotného přihlašování se dají odvolat a jejich platnost se kontroluje pokaždé, když se používají.

Platforma Microsoft Identity Platform používá dva druhy tokenů relace jednotného přihlašování: trvalá a netrvalá. Tokeny trvalé relace jsou v prohlížeči uloženy jako trvalé soubory cookie. Netrvalé tokeny relací se ukládají jako soubory cookie relace. (Soubory cookie relace budou zničeny při zavření prohlížeče.) Obvykle je uložen netrvalý token relace. Když ale při ověřování uživatel vybere zaškrtávací políčko **zůstat přihlášeni** , je uložený token trvalé relace.

Netrvalá tokeny relace mají dobu životnosti 24 hodin. Trvalé tokeny mají životnost 90 dnů. Kdykoliv se token relace jednotného přihlašování používá v rámci období platnosti, doba platnosti se prodlouží o dalších 24 hodin nebo 90 dnů v závislosti na typu tokenu. Pokud se token relace jednotného přihlašování v rámci období platnosti nepoužívá, je považován za neplatný a už není přijatý.

Pomocí zásad můžete nastavit čas, kdy byl první token relace vydán, za který už token relace není přijatý. (Chcete-li to provést, použijte vlastnost maximální stáří tokenu relace.) Životnost tokenu relace můžete upravit tak, aby bylo možné řídit, kdy a jak často musí uživatel při použití webové aplikace znovu zadat přihlašovací údaje.

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Vlastnosti zásad životního cyklu pro obnovení a token relace
Zásada životního cyklu tokenu je typ objektu zásad, který obsahuje pravidla životnosti tokenů. Pomocí vlastností zásady můžete řídit zadané životnosti tokenů. Pokud nejsou nastavené žádné zásady, systém vyhodnotí výchozí hodnotu doby života.

#### <a name="configurable-token-lifetime-properties"></a>Konfigurovatelné vlastnosti životnosti tokenů
| Vlastnost | Řetězec vlastnosti zásad | Ovlivňuje | Výchozí | Minimum | Maximum |
| --- | --- | --- | --- | --- | --- |
| Maximální neaktivní čas obnovovacího tokenu |MaxInactiveTime |Aktualizovat tokeny |90 dnů |10 minut |90 dnů |
| Maximální stáří tokenu Single-Factor aktualizace |MaxAgeSingleFactor |Aktualizovat tokeny (pro všechny uživatele) |Do-neodvolán |10 minut |Do-odvolání<sup>1</sup> |
| Maximální stáří tokenu pro Multi-Factor Refresh |MaxAgeMultiFactor |Aktualizovat tokeny (pro všechny uživatele) | Do-neodvolán |10 minut |180 dnů<sup>1</sup> |
| Maximální stáří tokenu relace Single-Factor |MaxAgeSessionSingleFactor |Tokeny relace (trvalé a netrvalé) |Do-neodvolán |10 minut |Do-odvolání<sup>1</sup> |
| Maximální stáří tokenu relace Multi-Factor |MaxAgeSessionMultiFactor |Tokeny relace (trvalé a netrvalé) | Do-neodvolán |10 minut | 180 dnů<sup>1</sup> |

* <sup>1</sup>365 dní je maximální explicitní délka, kterou lze pro tyto atributy nastavit.

#### <a name="exceptions"></a>Výjimky
| Vlastnost | Ovlivňuje | Výchozí |
| --- | --- | --- |
| Maximální stáří obnovovacího tokenu (vydané pro federované uživatele, kteří mají nedostatečné informace o odvolání<sup>1</sup>) |Aktualizovat tokeny (vydané pro federované uživatele, kteří mají nedostatečné informace o odvolání<sup>1</sup>) |12 hodin |
| Maximální neaktivní čas obnovovacího tokenu (vydaný pro důvěrné klienty) |Aktualizovat tokeny (vydané pro důvěrné klienty) |90 dnů |
| Maximální stáří obnovovacího tokenu (vydané pro důvěrné klienty) |Aktualizovat tokeny (vydané pro důvěrné klienty) |Do-neodvolán |

* <sup>1</sup> federované uživatele, kteří mají nedostatečné informace o odvolání, zahrnují všechny uživatele, kteří nemají atribut "LastPasswordChangeTimestamp" synchronizovaný. Těmto uživatelům se doručí krátké maximální stáří, protože Azure Active Directory nedokáže ověřit, kdy odvolat tokeny, které jsou svázané s původními přihlašovacími údaji (například s heslem, které se změnily), a musí se vrátit častěji, aby bylo zajištěno, že uživatel a přidružené tokeny jsou stále v dobrém umístění. Aby bylo možné toto prostředí zlepšit, Správci klientů musí zajistit, aby synchronizoval tento atribut "LastPasswordChangeTimestamp" (dá se nastavit v objektu User pomocí PowerShellu nebo prostřednictvím AADSync).

### <a name="configurable-policy-property-details"></a>Podrobnosti vlastnosti konfigurovatelné zásady

#### <a name="refresh-token-max-inactive-time"></a>Maximální neaktivní čas obnovovacího tokenu
**Řetězec:** MaxInactiveTime

**Má vliv na:** Aktualizovat tokeny

**Shrnutí:** Tato zásada řídí, jak může být starý obnovovací token předtím, než ho klient přestane používat k načtení nového páru přístupu nebo obnovovacího tokenu při pokusu o přístup k tomuto prostředku. Vzhledem k tomu, že při použití aktualizačního tokenu se obvykle vrátí nový obnovovací token, tato zásada zabrání přístupu, pokud se klient pokusí o přístup k jakémukoli prostředku pomocí aktuálního obnovovacího tokenu během zadaného časového období.

Tato zásada vynutí, aby uživatelé, kteří nebyli aktivní na svém klientovi, mohli znovu ověřit a načíst nový obnovovací token.

Vlastnost maximálního neaktivního času obnovovacího tokenu musí být nastavená na nižší hodnotu než maximální stáří tokenu Single-Factor a maximální stáří tokenu pro službu Multi-Factor Refresh.

Příklad najdete v tématu [Vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="single-factor-refresh-token-max-age"></a>Maximální stáří tokenu Single-Factor aktualizace
**Řetězec:** MaxAgeSingleFactor

**Má vliv na:** Aktualizovat tokeny

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel pomocí obnovovacího tokenu získat nový pár přístupů nebo obnovovacího tokenu po úspěšném ověření pomocí jenom jednoho faktoru. Po ověření a přijetí nového obnovovacího tokenu může uživatel použít tok obnovovacího tokenu po určenou dobu. (To platí, pokud je aktuální obnovovací token neodvolán a nepoužívá se déle než při neaktivním čase.) V tomto okamžiku se uživateli vynutí znovu ověřit, aby získal nový obnovovací token.

Snížení maximálního stáří nutí uživatele ověřovat častěji. Vzhledem k tomu, že vícefaktorové ověřování je považováno za méně bezpečné než ověřování Multi-Factor Authentication, doporučujeme tuto vlastnost nastavit na hodnotu, která je větší nebo rovna hodnotě vlastnosti maximální stáří tokenu Multi-Factor Refresh.

Příklad najdete v tématu [Vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="multi-factor-refresh-token-max-age"></a>Maximální stáří tokenu pro Multi-Factor Refresh
**Řetězec:** MaxAgeMultiFactor

**Má vliv na:** Aktualizovat tokeny

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel pomocí obnovovacího tokenu získat nový pár přístupů nebo obnovovacího tokenu po úspěšném ověření pomocí několika faktorů. Po ověření a přijetí nového obnovovacího tokenu může uživatel použít tok obnovovacího tokenu po určenou dobu. (To platí, pokud aktuální obnovovací token není odvolán a nepoužívá se déle než při neaktivním čase.) V tomto okamžiku je vynuceno opětovné ověření uživatelů, aby bylo možné získat nový obnovovací token.

Snížení maximálního stáří nutí uživatele ověřovat častěji. Vzhledem k tomu, že vícefaktorové ověřování je považováno za méně bezpečné než ověřování Multi-Factor Authentication, doporučujeme tuto vlastnost nastavit na hodnotu, která je větší nebo rovna hodnotě vlastnosti maximální stáří tokenu Single-Factor aktualizace.

Příklad najdete v tématu [Vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="single-factor-session-token-max-age"></a>Maximální stáří tokenu relace Single-Factor
**Řetězec:** MaxAgeSessionSingleFactor

**Má vliv na:** Tokeny relace (trvalé a netrvalé)

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel použít token relace k získání nového ID a tokenu relace po jeho posledním ověření, a to pomocí jediného faktoru. Po ověření a přijetí nového tokenu relace může uživatel použít tok tokenu relace v zadaném časovém období. (Tato hodnota je true, pokud aktuální token relace není odvolán a nevypršela jeho platnost.) Po uplynutí zadaného časového období se uživatel nuceně znovu ověří a získá nový token relace.

Snížení maximálního stáří nutí uživatele ověřovat častěji. Vzhledem k tomu, že vícefaktorové ověřování je považováno za méně bezpečné než ověřování Multi-Factor Authentication, doporučujeme tuto vlastnost nastavit na hodnotu, která je rovna nebo menší než maximální stáří tokenu relace Multi-Factor.

Příklad najdete v tématu [Vytvoření zásady pro web signing-in](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

#### <a name="multi-factor-session-token-max-age"></a>Maximální stáří tokenu relace Multi-Factor
**Řetězec:** MaxAgeSessionMultiFactor

**Má vliv na:** Tokeny relace (trvalé a netrvalé)

**Shrnutí:** Tato zásada určuje, jak dlouho může uživatel použít token relace k získání nového ID a tokenu relace po jeho posledním ověření pomocí více faktorů. Po ověření a přijetí nového tokenu relace může uživatel použít tok tokenu relace v zadaném časovém období. (Tato hodnota je true, pokud aktuální token relace není odvolán a nevypršela jeho platnost.) Po uplynutí zadaného časového období se uživatel nuceně znovu ověří a získá nový token relace.

Snížení maximálního stáří nutí uživatele ověřovat častěji. Vzhledem k tomu, že vícefaktorové ověřování je považováno za méně bezpečné než ověřování Multi-Factor Authentication, doporučujeme tuto vlastnost nastavit na hodnotu, která je větší nebo rovna hodnotě vlastnosti Single-Factor maximální stáří tokenu relace.

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>Konfigurovatelné vlastnosti životnosti tokenů po vyřazení
Aktualizace a konfigurace tokenu relace jsou ovlivněny následujícími vlastnostmi a jejich nastavenými hodnotami. Po vyřazení aktualizace a konfigurace tokenu relace od 30. ledna 2021 budou služby Azure AD dodržovat jenom výchozí hodnoty popsané níže. Pokud se rozhodnete nepoužívat podmíněný přístup ke správě četnosti přihlašování, vaše tokeny aktualizace a relace budou nastavené na výchozí konfiguraci v tomto datu a už nebudete moct měnit jejich životnost.  

|Vlastnost   |Řetězec vlastnosti zásad    |Ovlivňuje |Výchozí |
|----------|-----------|------------|------------|
|Doba života přístupového tokenu |AccessTokenLifetime |Přístupové tokeny, tokeny ID, tokeny typu Saml2 |1 hodina |
|Maximální neaktivní čas obnovovacího tokenu |MaxInactiveTime  |Aktualizovat tokeny |90 dnů  |
|Maximální stáří tokenu Single-Factor aktualizace  |MaxAgeSingleFactor  |Aktualizovat tokeny (pro všechny uživatele)  |Do-neodvolán  |
|Maximální stáří tokenu pro Multi-Factor Refresh  |MaxAgeMultiFactor  |Aktualizovat tokeny (pro všechny uživatele) |Do-neodvolán  |
|Maximální stáří tokenu relace Single-Factor  |MaxAgeSessionSingleFactor |Tokeny relace (trvalé a netrvalé)  |Do-neodvolán |
|Maximální stáří tokenu relace Multi-Factor  |MaxAgeSessionMultiFactor  |Tokeny relace (trvalé a netrvalé)  |Do-neodvolán |

K vyhledání zásad, které budou ovlivněny vyřazením, můžete použít PowerShell.  Pomocí [rutin PowerShellu](configure-token-lifetimes.md#get-started) zobrazíte všechny zásady vytvořené ve vaší organizaci, nebo zjistíte, které aplikace a instanční objekty jsou propojené s konkrétní zásadou.

## <a name="policy-evaluation-and-prioritization"></a>Vyhodnocení zásad a stanovení priorit
Můžete vytvořit a následně přiřadit zásady životnosti tokenů ke konkrétní aplikaci, vaší organizaci a objektům služby. U konkrétní aplikace může platit více zásad. Zásada životního cyklu tokenu, která se projeví, se řídí těmito pravidly:

* Pokud se zásada explicitně přiřadí instančnímu objektu, vynutila se.
* Pokud instančnímu objektu explicitně nepřiřazuje žádné zásady, vynutila se zásada explicitně přiřazená nadřazené organizaci instančního objektu.
* Pokud se instančnímu objektu nebo organizaci explicitně nepřiřazuje žádné zásady, vynutila se zásada přiřazená aplikaci.
* Pokud není přiřazená žádná zásada k instančnímu objektu, organizaci nebo objektu aplikace, vynutily se výchozí hodnoty. (Viz tabulka v části [konfigurovatelné vlastnosti životnosti tokenu](#configurable-token-lifetime-properties-after-the-retirement).)

Další informace o vztahu mezi objekty aplikace a instančními objekty služby naleznete v tématu [Application and Service Principal Objects in Azure Active Directory](app-objects-and-service-principals.md).

Platnost tokenu je vyhodnocena v době, kdy je token použit. Zásady s nejvyšší prioritou u aplikace, ke které se přistupoval, se projeví.

Všechny časové rozsahy, které jsou zde použity, jsou formátovány podle objektu [TimeSpan](/dotnet/api/system.timespan) jazyka C# – D. hh: mm: ss.  Tedy 80 dní a 30 minut `80.00:30:00` .  Úvodní znak D se může vyřadit, pokud je nula, takže 90 minut `00:90:00` .  

### <a name="example-scenario"></a>Ukázkový scénář

Uživatel chce přistupovat ke dvěma webovým aplikacím: webové aplikace a a webová aplikace B.

Skutečnosti
* Obě webové aplikace jsou ve stejné nadřazené organizaci.
* Zásada životnosti tokenu 1 s tokenem relace je maximální stáří osmi hodin nastavené jako výchozí hodnota nadřazené organizace.
* Webová aplikace A je běžné použití webové aplikace a není propojena s žádnými zásadami.
* Webová aplikace B se používá pro vysoce citlivé procesy. Jeho instanční objekt je propojený se zásadami životnosti tokenu 2, který má token relace maximální stáří 30 minut.

V 12:00 PM uživatel spustí novou relaci prohlížeče a pokusí se získat přístup k webové aplikaci A. Uživatel se přesměruje na platformu Microsoft identity a zobrazí se výzva k přihlášení. Tím se vytvoří soubor cookie s tokenem relace v prohlížeči. Uživatel je přesměrován zpět na webovou aplikaci A s tokenem ID, který umožňuje uživateli přístup k aplikaci.

V 12:15 PM se uživatel pokusí získat přístup k webové aplikaci B. Prohlížeč přesměruje na platformu Microsoft identity, která detekuje soubor cookie relace. Instanční objekt webové aplikace B je propojen se zásadami životnosti tokenu 2, ale je také součástí nadřazené organizace s výchozí zásadou životnosti tokenů 1. Zásada životnosti tokenu 2 se projeví, protože zásady propojené s instančními objekty mají vyšší prioritu než výchozí zásady organizace. Token relace byl původně vydaný během posledních 30 minut, takže se považuje za platný. Uživatel je přesměrován zpět na webovou aplikaci B s tokenem ID, který jim udělí přístup.

V 1:00 PM se uživatel pokusí získat přístup k webové aplikaci A. Uživatel se přesměruje na platformu Microsoft identity. Webová aplikace A není propojená s žádnými zásadami, ale vzhledem k tomu, že je v organizaci s výchozí zásadou životností tokenu 1, uplatní se tato zásada. Zjistil se soubor cookie relace, který byl původně vydaný během posledních osmi hodin. Uživatel je tiše přesměrován zpět na webovou aplikaci A s novým tokenem ID. Uživatel není vyžadován k ověření.

Hned potom se uživatel pokusí získat přístup k webové aplikaci B. Uživatel se přesměruje na platformu Microsoft identity. Stejně jako dřív se uplatní zásady životnosti tokenu 2. Vzhledem k tomu, že byl token vydán před více než 30 minutami, bude uživatel vyzván k opětovnému zadání přihlašovacích údajů pro přihlášení. Branding – vydávají se nové tokeny relace a tokeny ID. Uživatel pak může získat přístup k webové aplikaci B.

## <a name="cmdlet-reference"></a>Reference k rutinám

Jedná se o rutiny v [modulu Azure Active Directory PowerShell pro graf Preview](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals).

### <a name="manage-policies"></a>Správa zásad

Ke správě zásad můžete použít následující rutiny.

| Rutina | Popis | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Vytvoří novou zásadu. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Načte všechny zásady Azure AD nebo zadané zásady. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Načte všechny aplikace a instanční objekty, které jsou propojené se zásadou. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Aktualizuje existující zásady. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Odstraní zadané zásady. |

### <a name="application-policies"></a>Zásady použití
Pro zásady použití můžete použít následující rutiny.</br></br>

| Rutina | Popis | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Propojí zadané zásady s aplikací. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Získá zásadu, která je přiřazena aplikaci. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Odebere zásadu z aplikace. |

### <a name="service-principal-policies"></a>Zásady instančních objektů
Pro zásady instančního objektu můžete použít následující rutiny.

| Rutina | Popis | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Propojí zadané zásady s instančním objektem. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Načte všechny zásady propojené se zadaným objektem služby.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Odebere zásadu ze zadaného instančního objektu.|

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc, přečtěte si [Příklady konfigurace životnosti tokenů](configure-token-lifetimes.md).

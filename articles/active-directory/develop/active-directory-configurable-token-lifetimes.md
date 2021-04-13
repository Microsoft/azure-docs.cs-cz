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
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363967"
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

Klienti používají přístupové tokeny pro přístup k chráněnému prostředku. Přístupový token se dá použít jenom pro konkrétní kombinaci uživatele, klienta a prostředku. Přístupové tokeny nejde odvolat a jsou platné, dokud nevyprší jejich platnost. Škodlivý objekt actor, který získal přístupový token, ho může použít pro rozsah své životnosti. Úprava životnosti přístupového tokenu je kompromis mezi zvýšením výkonu systému a zvýšením doby, po kterou klient zachovává přístup po zakázání účtu uživatele. Vylepšený výkon systému se dosahuje snížením počtu pokusů, kolikrát klient potřebuje získat nový přístupový token.  Výchozí hodnota se liší v závislosti na klientské aplikaci požadující token. Například klienti s podporou testování nepřetržitého přístupu (CAE), kteří vyjednávají s relacemi CAE, uvidí dlouhou životnost tokenu (až 28 hodin). Po vypršení platnosti tokenu musí klient použít obnovovací token (obvykle v tichém režimu) k získání nového obnovovacího tokenu a přístupového tokenu.

### <a name="saml-tokens"></a>Tokeny SAML

Tokeny SAML jsou používány mnoha webovými SaaS aplikacemi a jsou získány pomocí koncového bodu protokolu typu Saml2 v systému Azure Active Directory. Využívají je i aplikace, které používají WS-Federation. Výchozí doba platnosti tokenu je 1 hodina. V perspektivě aplikace je doba platnosti tokenu určena hodnotou NotOnOrAfter `<conditions …>` elementu v tokenu. Po ukončení doby platnosti tokenu musí klient iniciovat nový požadavek na ověření, který se často splní bez interaktivního přihlášení v důsledku tokenu relace jednotného přihlašování (SSO).

Hodnotu NotOnOrAfter lze změnit pomocí `AccessTokenLifetime` parametru v `TokenLifetimePolicy` . Nastaví se na životní cyklus nakonfigurovanou v zásadě, pokud existuje, a koeficient pro zešikmení s hodinami 5 minut.

Potvrzení předmětu NotOnOrAfter zadané v elementu není `<SubjectConfirmationData>` ovlivněné konfigurací životnosti tokenu. 

### <a name="id-tokens"></a>Tokeny ID

Tokeny ID jsou předány webům a nativním klientům. Tokeny ID obsahují informace o profilu uživatele. Token ID je vázán na určitou kombinaci uživatele a klienta. Tokeny ID se považují za platné, dokud nevyprší jejich platnost. Obvykle webová aplikace odpovídá době životnosti relace uživatele v aplikaci až po dobu životnosti tokenu ID vydaného pro uživatele. Můžete upravit životnost tokenu ID, abyste mohli určit, jak často webová aplikace vyprší aplikační relaci, a jak často je potřeba, aby se uživatel znovu ověřil s platformou Microsoft identity (ať už v tichém nebo interaktivním).

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Zásady životnosti tokenů pro aktualizační tokeny a tokeny relací

Nemůžete nastavit zásady životnosti tokenů pro aktualizační tokeny a tokeny relací.

> [!IMPORTANT]
> Od 30. ledna 2021 nemůžete konfigurovat aktualizace a životnosti tokenů relace. Azure Active Directory už v existujících zásadách nedodržuje aktualizace a konfiguraci tokenu relace.  Nové tokeny vydané po vypršení platnosti existujících tokenů jsou teď nastavené na [výchozí konfiguraci](#configurable-token-lifetime-properties). Po dokončení aktualizace a vyřazení konfigurace tokenu relace můžete i nadále konfigurovat přístup, SAML a životnosti tokenů ID.
>
> Doba života existujícího tokenu se nezmění. Po vypršení platnosti se na základě výchozí hodnoty vygeneruje nový token.
>
> Pokud potřebujete pokračovat v definování časového období, než se uživateli zobrazí výzva k opětovnému přihlášení, nakonfigurujte četnost přihlášení v podmíněném přístupu. Pokud chcete získat další informace o podmíněném přístupu, přečtěte si téma [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="configurable-token-lifetime-properties"></a>Konfigurovatelné vlastnosti životnosti tokenů
Zásada životního cyklu tokenu je typ objektu zásad, který obsahuje pravidla životnosti tokenů. Tato zásada určuje, jak dlouho jsou tokeny pro přístup, SAML a ID pro tento prostředek považovány za platné. Zásady životnosti tokenů nelze nastavit pro tokeny aktualizace a relace. Pokud nejsou nastavené žádné zásady, systém vyhodnotí výchozí hodnotu doby života.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>Vlastnosti zásad životního cyklu přístupu, ID a typu Saml2 tokenu

Omezení vlastnosti životnosti přístupového tokenu snižuje riziko zneužití přístupového tokenu nebo tokenu ID škodlivým objektem actor po delší dobu. (Tyto tokeny nejde odvolat.) Při obchodování je nepříznivě ovlivněn výkon, protože tokeny musí být nahrazeny častěji.

Příklad najdete v tématu [Vytvoření zásady pro web signing-in](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

Přístup, ID a konfigurace tokenu typu Saml2 jsou ovlivněné následujícími vlastnostmi a jejich nastavenými hodnotami:

- **Vlastnost**: doba platnosti přístupového tokenu
- **Řetězec vlastnosti zásad**: AccessTokenLifetime
- **Ovlivňuje**: přístupové tokeny, tokeny ID, TOKENy typu Saml2
- **Výchozí**:
    - Přístupové tokeny: liší se v závislosti na klientské aplikaci požadující token. Například klienti s podporou testování nepřetržitého přístupu (CAE), kteří vyjednávají s relacemi CAE, uvidí dlouhou životnost tokenu (až 28 hodin).
    - Tokeny ID, tokeny typu Saml2:1 hodina
- **Minimálně**: 10 minut
- **Maximum**: 1 den

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Vlastnosti zásad životního cyklu pro obnovení a token relace

Aktualizace a konfigurace tokenu relace jsou ovlivněny následujícími vlastnostmi a jejich nastavenými hodnotami. Po vyřazení aktualizace a konfigurace tokenu relace od 30. ledna 2021 budou služby Azure AD dodržovat jenom výchozí hodnoty popsané níže. Pokud se rozhodnete nepoužívat [podmíněný přístup](../conditional-access/howto-conditional-access-session-lifetime.md) ke správě četnosti přihlašování, vaše tokeny aktualizace a relace budou nastavené na výchozí konfiguraci v tomto datu a už nebudete moct měnit jejich životnost.  

|Vlastnost   |Řetězec vlastnosti zásad    |Ovlivňuje |Výchozí |
|----------|-----------|------------|------------|
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
* Pokud není přiřazená žádná zásada k instančnímu objektu, organizaci nebo objektu aplikace, vynutily se výchozí hodnoty. (Viz tabulka v části [konfigurovatelné vlastnosti životnosti tokenu](#configurable-token-lifetime-properties).)

Další informace o vztahu mezi objekty aplikace a instančními objekty služby naleznete v tématu [Application and Service Principal Objects in Azure Active Directory](app-objects-and-service-principals.md).

Platnost tokenu je vyhodnocena v době, kdy je token použit. Zásady s nejvyšší prioritou u aplikace, ke které se přistupoval, se projeví.

Všechny časové rozsahy, které jsou zde použity, jsou formátovány podle objektu [TimeSpan](/dotnet/api/system.timespan) jazyka C# – D. hh: mm: ss.  Tedy 80 dní a 30 minut `80.00:30:00` .  Úvodní znak D se může vyřadit, pokud je nula, takže 90 minut `00:90:00` .  

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

---
title: Konfigurace automatické akcelerace přihlášení pomocí zjišťování domovské sféry
description: Naučte se konfigurovat zásady zjišťování domovské sféry pro Azure Active Directory ověřování pro federované uživatele, včetně automatické akcelerace a pomocných parametrů domény.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: kenwith
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a21b6f5e7d2976bda0efd37577b7cca90469aea
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686440"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurace chování přihlášení Azure Active Directory pro aplikace pomocí zásad zjišťování domovské sféry

Tento článek popisuje, jak nakonfigurovat Azure Active Directory chování ověřování pro federované uživatele pomocí zásad zjišťování domovské sféry (HRD).  Zahrnuje použití automatické akcelerace k přeskočení obrazovky zadávání uživatelského jména a automatické přeposílání uživatelů na koncové body federovaného přihlášení.  Společnost Microsoft už nedoporučuje konfigurovat automatické urychlení, protože může bránit použití silnějších metod ověřování, jako je FIDO a brání spolupráci.

## <a name="home-realm-discovery"></a>Zjišťování domovské sféry

Zjišťování domovské sféry (HRD) je proces, který umožňuje Azure Active Directory (Azure AD) určit zprostředkovatele identity ("IdP"), který uživatel potřebuje k ověření v době přihlášení.  Když se uživatel přihlásí k tenantovi služby Azure AD za účelem přístupu k prostředku nebo na stránku Common Sign-in Azure AD, zadejte uživatelské jméno (UPN). Azure AD používá ke zjištění, kde se uživatel musí přihlásit.

Uživatel bude přicházet k jednomu z následujících zprostředkovatelů identity, kteří budou ověřeni:

- Domovský tenant uživatele (může být stejný tenant jako prostředek, ke kterému se uživatel pokouší získat přístup).

- účet Microsoft.  Uživatel je host v tenantovi prostředků, který používá pro ověřování účet uživatele.

- Místní zprostředkovatel identity, například Active Directory Federation Services (AD FS) (AD FS).

- Jiný zprostředkovatel identity, který je federovaný s klientem služby Azure AD.

## <a name="auto-acceleration"></a>Automatická akcelerace

Některé organizace konfigurují domény ve svém Azure Active Directory tenantovi tak, aby se federovat pomocí jiného IdP, jako je například AD FS pro ověřování uživatelů.  

Když se uživatel přihlásí do aplikace, nejprve se zobrazí na přihlašovací stránce služby Azure AD. Po zadání hlavního názvu uživatele (UPN), pokud jsou v federované doméně, se následně přijímají na přihlašovací stránku IdP, která slouží k této doméně. Za určitých okolností můžou správci chtít při přihlašování ke konkrétním aplikacím směrovat uživatele na přihlašovací stránku.

V důsledku toho mohou uživatelé přeskočit úvodní stránku Azure Active Directory. Tento proces se označuje jako "Automatická akcelerace přihlášení".

V případech, kdy je tenant federované na jiný IdP pro přihlášení, automatické urychlení usnadňuje přihlašování uživatelů.  Automatickou akceleraci můžete nakonfigurovat pro jednotlivé aplikace.

>[!NOTE]
>Pokud nakonfigurujete aplikaci pro automatickou akceleraci, uživatelé nemůžou používat spravované přihlašovací údaje (například FIDO) a uživatelé typu Host se nemůžou přihlásit. Pokud zadáte uživateli přímý přístup k federovanému IdP, neexistuje žádný způsob, jak se vrátit na přihlašovací stránku Azure Active Directory. Uživatelé typu Host, kteří se můžou potřebovat směrovat na jiné klienty nebo externí IdP, jako je účet Microsoft, se k této aplikaci nemůžou přihlásit, protože přeskočí krok zjišťování domovské sféry.  

Existují tři způsoby, jak nastavit automatickou akceleraci pro federované IdP:

- Použijte [pomocný parametr domény](#domain-hints) pro žádosti o ověření pro aplikaci.
- Nakonfigurujte zásady zjišťování domovské sféry, aby se [vynutila Automatická akcelerace](#home-realm-discovery-policy-for-auto-acceleration).
- Nakonfigurujte zásady zjišťování pro domovskou sféru tak, aby [ignorovaly doporučení domény](prevent-domain-hints-with-home-realm-discovery.md) pro konkrétní aplikace nebo pro určité domény.

### <a name="domain-hints"></a>Pomocné parametry domény

Pomocné parametry domény jsou direktivy, které jsou zahrnuté v žádosti o ověření z aplikace. Dají se použít k urychlení uživatele na přihlašovací stránku federovaného IdP. Nebo je můžou použít víceklientská aplikace k urychlení uživatele přímo na přihlašovací stránku služby Azure AD, která je označená pro svého tenanta.  

Například aplikace "largeapp.com" může svým zákazníkům umožnit přístup k aplikaci na vlastní adrese URL "contoso.largeapp.com". Aplikace může také v žádosti o ověření zahrnovat pomocný parametr domény pro contoso.com.

Syntaxe pomocného parametru domény se liší v závislosti na používaném protokolu a obvykle se konfiguruje v aplikaci.

**WS-Federation**: WH = contoso. com v řetězci dotazu.

**SAML**: buď žádost o ověření SAML, která obsahuje pomocný parametr domény, nebo řetězec dotazu WH = contoso. com.

**Otevřené ID Connect**: řetězec dotazu domain_hint = contoso. com.

Ve výchozím nastavení se Azure AD pokusí přesměrovat přihlášení na IdP, které je nakonfigurované pro doménu, pokud jsou splněné **obě** následující podmínky:

- Pomocný parametr domény je součástí žádosti o ověření z aplikace **a**
- Tenant je federovaný s touto doménou.

Pokud pomocný parametr domény neodkazuje na ověřenou federované doménu, ignoruje se.

Další informace o automatické akceleraci pomocí pomocných parametrů domény, které jsou podporované nástrojem Azure Active Directory, najdete na [blogu Enterprise mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Je-li v žádosti o ověření zahrnut pomocný parametr domény a [měl by být dodržen](#home-realm-discovery-policy-to-prevent-auto-acceleration), jeho přítomnost Přepisuje automatickou akceleraci, která je nastavena pro aplikaci v zásadách hrd.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Zásady zjišťování domovské sféry pro automatické akcelerace

Některé aplikace neposkytují způsob konfigurace vysílané žádosti o ověření. V těchto případech není možné pomocí pomocných parametrů domény řídit automatickou akceleraci. Automatická akcelerace se dá nakonfigurovat pomocí zásad zjišťování domovské sféry, aby se dosáhlo stejného chování.  

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Zásada zjišťování domovské sféry, která zabraňuje automatické akceleraci

Některé aplikace Microsoftu a SaaS automaticky zahrnují domain_hints (například `https://outlook.com/contoso.com` výsledky žádosti o přihlášení s `&domain_hint=contoso.com` připojením), které můžou rušit zavedení spravovaných přihlašovacích údajů, jako je Fido.  Pomocí [zásad zjišťování domovské sféry](https://docs.microsoft.com/graph/api/resources/homeRealmDiscoveryPolicy) můžete v rámci zavedení spravovaných přihlašovacích údajů ignorovat doporučení k doméně z určitých aplikací nebo pro určité domény.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Povolit přímé ROPC ověřování federovaných uživatelů pro starší verze aplikací

Osvědčeným postupem je použití knihoven AAD a interaktivního přihlašování pro aplikace k ověřování uživatelů. Knihovny se budou starat o toky federovaného uživatele.  Někdy starší aplikace, zejména ty, které používají ROPC, odesílají uživatelské jméno a heslo přímo do služby Azure AD a nejsou zapsány pro pochopení federace. Neprovádí zjišťování domovské sféry a nekomunikuje se správným federovaným koncovým bodem pro ověření uživatele. Pokud se rozhodnete, můžete pomocí zásad HRD povolit konkrétní starší verze aplikací, které odesílají přihlašovací údaje uživatelského jména a hesla pomocí příkazu ROPC, aby se ověřily přímo pomocí Azure Active Directory. Je nutné povolit synchronizaci hodnot hash hesel.

> [!IMPORTANT]
> Přímé ověřování Povolte jenom v případě, že je zapnutá synchronizace hodnot hash hesel a víte, že je v pořádku ověřování této aplikace bez jakýchkoli zásad implementovaných v místních IdP. Pokud vypnete synchronizaci hodnot hash hesel nebo zrušíte synchronizaci adresářů s AD Connect z jakéhokoli důvodu, měli byste tyto zásady odebrat, abyste zabránili možnosti přímého ověřování pomocí zastaralé hodnoty hash hesla.

## <a name="set-hrd-policy"></a>Nastavení zásad HRD

Existují tři kroky pro nastavení zásad HRD pro aplikaci pro automatické urychlení přihlašování a přímé cloudové aplikace:

1. Vytvořte zásady HRD.

2. Vyhledejte instanční objekt, ke kterému chcete zásadu připojit.

3. Připojte zásadu k instančnímu objektu.

Zásady se projeví jenom pro konkrétní aplikaci, když jsou připojené k instančnímu objektu.

V jednom okamžiku může být aktivní jenom jedna zásada HRD (instanční objekt).  

K vytváření a správě zásad HRD můžete použít rutiny prostředí PowerShell pro Azure Active Directory.

Následuje příklad definice zásady HRD:

 ```JSON
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
    }
   }
```

Typ zásady je "[HomeRealmDiscoveryPolicy](https://docs.microsoft.com/graph/api/resources/homeRealmDiscoveryPolicy)".

**AccelerateToFederatedDomain** je nepovinný. Pokud má **AccelerateToFederatedDomain** hodnotu false, zásada nemá žádný vliv na automatickou akceleraci. Pokud má **AccelerateToFederatedDomain** hodnotu true a v tenantovi je jenom jedna ověřená a federované doména, budou se uživatelé přihlašovat přímo k federovanému IDP. Pokud má hodnotu true a v tenantovi je víc než jedna ověřená doména, musí se zadat **PreferredDomain** .

**PreferredDomain** je nepovinný. **PreferredDomain** by měl označovat doménu, ke které se má zrychlit. Tuto možnost můžete vynechat, pokud má tenant jenom jednu federované domény.  Pokud je vynecháno a existuje více než jedna ověřená federované doména, zásada nemá žádný vliv.

 Je-li zadán parametr **PreferredDomain** , musí odpovídat ověřené federované doméně pro tenanta. Všichni uživatelé aplikace musí být schopni se přihlásit k této doméně – uživatelé, kteří se nemůžou přihlašovat v federované doméně, budou mít přesahy a nemůžou dokončit přihlášení.

**AllowCloudPasswordValidation** je nepovinný. Pokud má **AllowCloudPasswordValidation** hodnotu true, aplikace může ověřit federovaného uživatele tím, že prezentuje přihlašovací údaje uživatelského jména a hesla přímo do koncového bodu tokenu Azure Active Directory. To funguje jenom v případě, že je povolená synchronizace hodnot hash hesel.

Kromě toho existují dvě možnosti HRD na úrovni tenanta, které nejsou uvedené výše:

- **AlternateIdLogin** je nepovinný.  Pokud je tato možnost povolená, [umožní uživatelům přihlásit se pomocí e-mailových adres místo svého hlavního názvu uživatele](../authentication/howto-authentication-use-email-signin.md) na přihlašovací stránce služby Azure AD.  Alternativní ID spoléhají na to, že uživatel se automaticky nezrychluje na federované IDP.

- **DomainHintPolicy** je volitelný složitý objekt, který [ *brání* pomocným parametrům domény z automatického zrychlení uživatelů na federované domény](prevent-domain-hints-with-home-realm-discovery.md). Toto nastavení v rámci tenanta se používá k zajištění, že aplikace, které odesílají pomocné parametry domény, nebrání uživatelům v přihlašování pomocí přihlašovacích údajů spravovaných v cloudu.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorita a vyhodnocení zásad HRD

Zásady HRD se dají vytvářet a pak je přiřazovat konkrétním organizacím a instančním objektům. To znamená, že je možné použít pro konkrétní aplikaci více zásad, takže Azure AD musí rozhodnout, který z nich má přednost. Sada pravidel určuje, které zásady HRD (z mnoha použitých) se uplatní:

- Pokud je v žádosti o ověření k dispozici pomocný parametr domény, je zkontrolována zásada HRD pro tenanta (sada zásad nastavená jako výchozí) a zjistí, jestli se [mají ignorovat doporučení domény](prevent-domain-hints-with-home-realm-discovery.md). Pokud jsou pomocné parametry domény povolené, použije se chování zadané v parametru doména.

- V opačném případě platí, že pokud je zásada explicitně přiřazená k instančnímu objektu, vynutila se.

- Pokud není k dispozici žádné doporučení k doméně a k instančnímu objektu se explicitně nepřiřazuje žádné zásady, vynutila se zásada, která je explicitně přiřazená nadřazené organizaci instančního objektu.

- Pokud není k dispozici žádná zásada domény a k instančnímu objektu nebo organizaci nebyly přiřazeny žádné zásady, použije se výchozí chování HRD.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Kurz pro nastavení zásad HRD pro aplikaci

Pomocí rutin Azure AD PowerShellu projdeme několik scénářů, mezi které patří:

- Nastavení zásad HRD pro automatické urychlení aplikace v tenantovi s jednou federované doménou.

- Nastavení zásad HRD pro automatické urychlení aplikace na jednu z několika domén, které jsou ověřené pro vašeho tenanta.

- Nastavují se zásady HRD, které umožní, aby se starší verze aplikace nastavila přímé ověřování uživatelského jména a hesla, aby Azure Active Directory pro federovaného uživatele.

- Seznam aplikací, pro které je nakonfigurovaná zásada

### <a name="prerequisites"></a>Požadavky

V následujících příkladech můžete vytvořit, aktualizovat, propojit a odstranit zásady pro instanční objekty služby ve službě Azure AD.

1. Začněte stažením nejnovější rutiny Azure AD PowerShellu ve verzi Preview.

2. Po stažení rutin služby Azure AD PowerShell spusťte příkaz připojit a přihlaste se k Azure AD pomocí účtu správce:

    ``` powershell
    Connect-AzureAD -Confirm
    ```

3. Spuštěním následujícího příkazu zobrazíte všechny zásady ve vaší organizaci:

    ``` powershell
    Get-AzureADPolicy
    ```

Pokud se nic nevrátí, znamená to, že ve vašem tenantovi nejsou vytvořené žádné zásady.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Příklad: nastavení zásad HRD pro aplikaci

V tomto příkladu vytvoříte zásadu, která je přiřazena k aplikaci buď:

- Automaticky zrychluje uživatele na AD FS přihlašovací obrazovku, když se přihlásí k aplikaci, když je ve vašem tenantovi jedna doména.
- Automaticky zrychluje uživatele na AD FS přihlašovací obrazovku. ve vašem tenantovi je více než jedna federované doména.
- Povoluje neinteraktivní přihlášení uživatelského jména a hesla přímo Azure Active Directory pro federované uživatele pro aplikace, ke kterým je zásada přiřazená.

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1: vytvoření zásady HRD

Následující zásady automaticky zrychlují uživatele na AD FS přihlašovací obrazovku, když se přihlásí k aplikaci, když je ve vašem tenantovi jedna doména.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Následující zásady automaticky zrychlují uživatele na AD FS přihlašovací obrazovku. ve vašem tenantovi je více než jedna federované doména. Pokud máte více než jednu federované domény, která ověřuje uživatele pro aplikace, je nutné zadat doménu pro automatické urychlení.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Pokud chcete vytvořit zásadu, která povolí ověřování pomocí uživatelského jména a hesla pro federované uživatele přímo s Azure Active Directory pro konkrétní aplikace, spusťte následující příkaz:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Pokud chcete zobrazit novou zásadu a získat její **objectID**, spusťte následující příkaz:

``` powershell
Get-AzureADPolicy
```

Pokud chcete zásady HRD použít po jejím vytvoření, můžete ji přiřadit k několika instančním objektům aplikace.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2: Vyhledání instančního objektu, ke kterému se má zásada přiřadit

Potřebujete **ID** objektu služby, ke kterému chcete zásadu přiřadit. Existuje několik způsobů, jak najít **objectID** objektů služby.

Můžete použít portál nebo můžete zadat dotaz na [Microsoft Graph](/graph/api/resources/serviceprincipal?view=graph-rest-beta). Můžete také přejít na [Nástroj Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) a přihlásit se k účtu služby Azure AD, abyste viděli všechny instanční objekty vaší organizace.

Vzhledem k tomu, že používáte PowerShell, můžete použít následující rutinu pro výpis objektů služby a jejich ID.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3: přiřazení zásad k objektu služby

Až budete mít **objectID** instančního objektu aplikace, pro kterou chcete nakonfigurovat automatickou akceleraci, spusťte následující příkaz. Tento příkaz přidruží zásadu HRD, kterou jste vytvořili v kroku 1, k instančnímu objektu, který jste našli v kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Tento příkaz můžete opakovat pro každý instanční objekt, ke kterému chcete zásadu přidat.

V případě, že aplikace už má přiřazenou zásadu HomeRealmDiscovery, nebudete moct přidat druhou.  V takovém případě změňte definici zásady zjišťování domovské sféry, která je přiřazena aplikaci pro přidání dalších parametrů.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Krok 4: ověření, k jakým instančním objektům služby se vaše zásada HRD přiřadí

Pokud chcete zjistit, které aplikace mají nakonfigurované zásady HRD, použijte rutinu **Get-AzureADPolicyAppliedObject** . Předejte mu **objectID** zásad, které chcete kontrolovat.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

#### <a name="step-5-youre-done"></a>Krok 5: jste hotovi!

Vyzkoušejte aplikaci a ověřte, že nová zásada funguje.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Příklad: seznam aplikací, pro které je nakonfigurovaná zásada HRD

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1: vypsání všech zásad, které byly vytvořeny ve vaší organizaci

``` powershell
Get-AzureADPolicy
```

Poznamenejte si **identifikátor** objektu zásad, pro který chcete vypsat přiřazení.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2: seznam objektů služby, ke kterým je zásada přiřazena  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Příklad: odebrání zásad HRD z aplikace

#### <a name="step-1-get-the-objectid"></a>Krok 1: získání ObjectID

Použijte předchozí příklad k získání **ID objectID** zásady a k objektu služby Application Service, ze kterého ho chcete odebrat.

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Krok 2: odebrání přiřazení zásad z instančního objektu služby  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 3: odstranění seznamu objektů služby, ke kterým je zásada přiřazena, najdete v části odebrání.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak ověřování funguje v Azure AD, najdete v tématu [scénáře ověřování pro Azure AD](../develop/authentication-vs-authorization.md).
- Další informace o jednotném přihlašování uživatelů najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md).
- Přehled veškerého obsahu souvisejícího s vývojářem najdete na [platformě Microsoft Identity](../develop/v2-overview.md) .

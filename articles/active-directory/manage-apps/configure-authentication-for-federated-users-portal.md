---
title: Konfigurace automatické akcelerace přihlášení pomocí zjišťování domovské sféry
description: Přečtěte si, jak nakonfigurovat zásady zjišťování domovské sféry pro ověřování azure active directory pro federované uživatele, včetně automatické akcelerace a rad při změně zabezpečení.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60bfc964ffc394b3f79c9d279158003f383b7331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943440"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurace chování přihlášení služby Azure Active Directory pro aplikaci pomocí zásad zjišťování domovské sféry

Tento článek obsahuje úvod ke konfiguraci chování ověřování služby Azure Active Directory pro federované uživatele. Zahrnuje konfiguraci omezení automatické akcelerace a ověřování pro uživatele ve federovaných doménách.

## <a name="home-realm-discovery"></a>Zjišťování domovské sféry
Zjišťování domovské sféry (HRD) je proces, který umožňuje Azure Active Directory (Azure AD) určit, kde uživatel potřebuje k ověření v době přihlášení.  Když se uživatel přihlásí k tenantovi Azure AD pro přístup k prostředku nebo na společnou přihlašovací stránku Azure AD, zadají uživatelské jméno (UPN). Azure AD používá, aby zjistil, kde se uživatel potřebuje k přihlášení. 

Uživatel může být nutné převést do jednoho z následujících umístění, které mají být ověřeny:

- Domácí tenant uživatele (může být stejný klient jako prostředek, ke kterému se uživatel pokouší získat přístup). 

- účet Microsoft.  Uživatel je hostem v tenantovi prostředku.

-  Místní zprostředkovatel identity, jako je služba AD FS (AD FS).

- Další zprostředkovatel identity, který je federovaný s tenantem Azure AD.

## <a name="auto-acceleration"></a>Automatická akcelerace 
Některé organizace konfigurují domény ve svém tenantovi služby Azure Active Directory tak, aby federaci s jiným idp, jako je například AD FS pro ověřování uživatelů.  

Když se uživatel přihlásí do aplikace, zobrazí se mu přihlašovací stránka Azure AD. Poté, co zadali svůj hlavní název domény, pokud jsou ve federované doméně, jsou pak převeeny na přihlašovací stránku idp sloužící tuto doménu. Za určitých okolností mohou správci chtít uživatele při přihlašování ke konkrétním aplikacím nasměrovat na přihlašovací stránku. 

V důsledku toho mohou uživatelé přeskočit počáteční stránku služby Azure Active Directory. Tento proces se označuje jako "automatické akcelerace přihlášení".

V případech, kdy je klient federován k jinému idp pro přihlášení, automatické akcelerace umožňuje přihlášení uživatele efektivnější.  Automatickou akceleraci můžete nakonfigurovat pro jednotlivé aplikace.

>[!NOTE]
>Pokud nakonfigurujete aplikaci pro automatickou akceleraci, uživatelé typu Host se nemohou přihlásit. Pokud vezmete uživatele přímo na federovaného idp pro ověřování, neexistuje žádný způsob, jak pro ně vrátit na přihlašovací stránku služby Azure Active Directory. Uživatelé typu Host, kteří mohou potřebovat přesměrovat na jiné klienty nebo externího zástupce uživatele internetu, například účet Microsoft, se k této aplikaci nemohou přihlásit, protože přeskakují krok Zjišťování domovské sféry.  

Automatické akcelerace lze řídit dvěma způsoby, jak řídit federované holokaužek:   

- Použijte nápovědu domény na žádosti o ověření pro aplikaci. 
- Nakonfigurujte zásadu zjišťování domovské sféry tak, aby umožňovala automatickou akceleraci.

### <a name="domain-hints"></a>Rady při doméně    
Rady při psaní domény jsou direktivy, které jsou zahrnuty v požadavku na ověření z aplikace. Lze je použít k urychlení uživatele na federované přihlašovací stránce IdP. Nebo je může použít víceklientská aplikace k urychlení uživatele přímo na značkovou přihlašovací stránku Azure AD pro svého tenanta.  

Například aplikace "largeapp.com" může umožnit svým zákazníkům přístup k aplikaci na vlastní adresu URL "contoso.largeapp.com". Aplikace může také obsahovat nápovědu k doméně contoso.com v žádosti o ověření. 

Syntaxe nápovědy domény se liší v závislosti na použitém protokolu a obvykle se nakonfiguruje v aplikaci.

**WS-Federation**: whr=contoso.com v řetězci dotazu.

**SAML**: Požadavek na ověření SAML, který obsahuje nápovědu domény, nebo řetězec dotazu whr=contoso.com.

**Otevřít ID Připojení**: Řetězec dotazu domain_hint=contoso.com. 

Pokud je nápověda domény zahrnuta v požadavku na ověření z aplikace a klient je federován s tou doménou, Azure AD se pokusí přesměrovat přihlášení k IdP, který je nakonfigurovaný pro tuto doménu. 

Pokud nápověda domény neodkazuje na ověřenou federovovovnou doménu, je ignorována a je vyvoláno normální zjišťování domovské sféry.

Další informace o automatické akceleraci pomocí nápovědy k doméně, které jsou podporované službou Azure Active Directory, naleznete v [blogu Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Pokud je nápověda domény zahrnuta v požadavku na ověření, jeho přítomnost přepíše automatické zrychlení, které je nastaveno pro aplikaci v zásadách HRD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Zásady zjišťování domovské sféry pro automatickou akceleraci
Některé aplikace neposkytují způsob konfigurace požadavku na ověření, který vyzařují. V těchto případech není možné použít rady při psaní domény k řízení automatické akcelerace. Automatické zrychlení lze nakonfigurovat pomocí zásad k dosažení stejného chování.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Povolení přímého ověřování pro starší aplikace
Osvědčeným postupem je, aby aplikace používaly knihovny AAD a interaktivní přihlášení k ověřování uživatelů. Knihovny se starají o federované toky uživatelů.  Někdy starší aplikace nejsou zapsány k pochopení federace. Neprovádějí zjišťování domovské sféry a nepracují se správným federovaným koncovým bodem k ověření uživatele. Pokud se rozhodnete, můžete pomocí zásad HRD povolit konkrétním starším aplikacím, které odesílají přihlašovací údaje uživatelského jména a hesla, k ověření přímo pomocí služby Azure Active Directory. Synchronizace hash hesel musí být povolena. 

> [!IMPORTANT]
> Přímé ověřování povolte pouze v případě, že máte zapnutou synchronizaci hash hesel a víte, že ověření této aplikace je v pořádku bez jakýchkoli zásad implementovaných místním idp. Pokud z nějakého důvodu vypnete synchronizaci hodnot hash hesel nebo synchronizaci adresářů se službou AD Connect, měli byste tuto zásadu odebrat, abyste zabránili možnosti přímého ověřování pomocí zastaralého nastavení hash hesla.

## <a name="set-hrd-policy"></a>Nastavit zásadu HRD
Existují tři kroky k nastavení zásad HRD pro aplikaci pro federované automatické akceleraci přihlášení nebo přímé cloudové aplikace:

1. Vytvořte zásadu HRD.

2. Vyhledejte instanční objekt, ke kterému chcete připojit zásadu.

3. Připojte zásadu k instančnímu objektu. 

Zásady se projeví pouze pro konkrétní aplikaci, pokud jsou připojeny k instančnímu objektu. 

Pouze jedna zásada HRD může být aktivní na instančním objektu současně.  

Rutiny prostředí Azure Active Directory PowerShell můžete použít k vytvoření a správě zásad HRD.

Následuje příklad definice politiky HRD:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

Typ zásady je "HomeRealmDiscoveryPolicy."

**AccelerateToFederatedDomain** je volitelné. Pokud **AccelerateToFederatedDomain** je false, zásady nemá žádný vliv na automatické akceleraci. Pokud **AccelerateToFederatedDomain** je true a je pouze jedna ověřená a federované domény v tenantovi, pak uživatelé budou převzít přímo do federované IdP pro přihlášení. Pokud je true a je více než jedna ověřená doména v tenantovi, **preferreddomain** musí být zadán.

**PreferredDomain** je volitelné. **PreferredDomain** by měla označovat doménu, do které chcete zrychlit. Může být vynechán, pokud má klient pouze jednu federovovnou doménu.  Pokud je vynechán a existuje více než jedna ověřená federovaná doména, zásada nemá žádný vliv.

 Pokud je **zadána preferreddomain,** musí odpovídat ověřené, federované domény pro klienta. K této doméně se musí přihlásit všichni uživatelé aplikace.

**AllowCloudPasswordValidation** je volitelné. Pokud **je funkce AllowCloudPasswordValidation** pravdivá, je aplikace oprávněna ověřit federovaného uživatele tak, že předloží přihlašovací údaje uživatelského jména a hesla přímo koncovému bodu tokenu služby Azure Active Directory. To funguje pouze v případě, že je povolena synchronizace hash hesel.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorita a hodnocení politik hrd
Zásady HRD lze vytvořit a poté přiřadit konkrétním organizacím a instančním objektům. To znamená, že je možné pro více zásad použít pro konkrétní aplikaci. Zásada HRD, která nabývá účinnosti, se řídí těmito pravidly:


- Pokud je v žádosti o ověření přítomna nápověda domény, budou všechny zásady HRD ignorovány pro automatickou akceleraci. Chování, které je určeno nápovědou domény se používá.

- V opačném případě pokud je zásada explicitně přiřazena instančnímu objektu, je vynucena. 

- Pokud neexistuje žádná nápověda domény a žádné zásady je explicitně přiřazena instanční objekt, zásady, které je explicitně přiřazena k nadřazené organizaci instančního objektu je vynucena. 

- Pokud neexistuje žádná nápověda domény a nebyla přiřazena žádná zásada instančnímu objektu nebo organizaci, použije se výchozí chování HRD.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Kurz pro nastavení zásad HRD pro aplikaci 
Budeme používat Rutiny Prostředí Azure AD PowerShell projít několik scénářů, včetně:


- Nastavení zásad hrd pro automatickou akceleraci aplikace v tenantovi s jednou federovovodou.

- Nastavení zásad hrd pro automatickou akceleraci aplikace do jedné z několika domén, které jsou ověřeny pro vašeho tenanta.

- Nastavení zásad HRD, které umožní starší aplikaci provádět přímé ověřování uživatelským jménem a heslem ve službě Azure Active Directory pro federovaného uživatele.

- Seznam aplikací, pro které je zásada nakonfigurována.


### <a name="prerequisites"></a>Požadavky
V následujících příkladech vytvoříte, aktualizujete, propojíte a odstraníte zásady na objektech služeb aplikace ve službě Azure AD.

1.  Chcete-li začít, stáhněte si nejnovější náhled rutiny prostředí Azure AD PowerShell. 

2.  Po stažení rutin prostředí Azure AD PowerShell spusťte příkaz Připojit a přihlaste se k Azure AD pomocí svého účtu správce:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Spuštěním následujícího příkazu zobrazíte všechny zásady ve vaší organizaci:

    ``` powershell
    Get-AzureADPolicy
    ```

Pokud se nic nevrátí, znamená to, že máte žádné zásady vytvořené ve vašem tenantovi.

### <a name="example-set-hrd-policy-for-an-application"></a>Příklad: Nastavit zásadu HRD pro aplikaci 

V tomto příkladu vytvoříte zásadu, která při přiřazení k aplikaci buď: 
- Automaticky urychluje uživatele na přihlašovací obrazovku služby AD FS, když se přihlašují k aplikaci, když je ve vašem tenantovi jedna doména. 
- Automaticky urychluje uživatele na přihlašovací obrazovku služby AD FS, ve vašem tenantovi je více než jedna federovaná doména.
- Povolí neinteraktivní uživatelské jméno nebo heslo, které se přihlašuje přímo do služby Azure Active Directory pro federované uživatele pro aplikace, ke kterým je zásada přiřazena.

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1: Vytvoření zásady HRD

Následující zásady automaticky urychluje uživatele na přihlašovací obrazovku služby AD FS při přihlašování k aplikaci, když je ve vašem tenantovi jedna doména.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Následující zásady automaticky urychluje uživatele na přihlašovací obrazovku služby AD FS, ve vašem tenantovi je více než jedna federovaná doména. Pokud máte více než jednu federovovovnu, která ověřuje uživatele pro aplikace, je třeba zadat doménu, aby se automaticky zrychlila.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Chcete-li vytvořit zásadu umožňující ověřování pomocí uživatelského jména a hesla pro federované uživatele přímo pomocí služby Azure Active Directory pro konkrétní aplikace, spusťte následující příkaz:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Chcete-li zobrazit novou zásadu a získat její **ObjektID**, spusťte následující příkaz:

``` powershell
Get-AzureADPolicy
```


Chcete-li použít zásadu HRD po vytvoření, můžete ji přiřadit k více zaregistrovaným objektům služby aplikace.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2: Vyhledejte instanční objekt, ke kterému chcete přiřadit zásadu  
Potřebujete **ObjectID** instančních objektů, ke kterým chcete přiřadit zásady. Existuje několik způsobů, jak najít **ObjectID** instančních objektů.    

Můžete použít portál nebo se můžete dotazovat [na aplikaci Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta). Můžete taky přejít na [nástroj Průzkumník a](https://developer.microsoft.com/graph/graph-explorer) přihlásit se ke svému účtu Azure AD a zobrazit všechny instanční objekty vaší organizace. 

Vzhledem k tomu, že používáte prostředí PowerShell, můžete pomocí následující rutiny vypsat instanční objekty a jejich ID.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3: Přiřazení zásady k instančnímu objektu service  
Po **objectid** instančního objektu aplikace, pro které chcete nakonfigurovat automatické akcelerace, spusťte následující příkaz. Tento příkaz přidruží zásadu HRD, kterou jste vytvořili v kroku 1, k instančnímu objektu, který jste namístě našli v kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Tento příkaz můžete opakovat pro každý instanční objekt, ke kterému chcete přidat zásadu.

V případě, že aplikace již má přiřazenou zásadu HomeRealmDiscovery, nebudete moci přidat druhou.  V takovém případě změňte definici zásady zjišťování domovské sféry, která je přiřazena aplikaci, a přidejte další parametry.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Krok 4: Zkontrolujte, ke kterým zaregistrovaným objektům aplikačního servisu je přiřazena vaše zásada HRD.
Chcete-li zkontrolovat, které aplikace mají nakonfigurovanou zásadu HRD, použijte rutinu **Get-AzureADPolicyAppliedObject.** Předajte **objekt Ové ID** zásady, které chcete zkontrolovat.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Krok 5: Jste hotovi!
Zkuste aplikaci zkontrolovat, zda nová zásada funguje.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Příklad: Seznam aplikací, pro které je nakonfigurována zásada HRD

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1: Seznam všech zásad, které byly vytvořeny ve vaší organizaci 

``` powershell
Get-AzureADPolicy
```

Všimněte si **ObjektID** zásady, pro které chcete seznam přiřazení.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2: Seznam instančních objektů, ke kterým je zásada přiřazena  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Příklad: Odebrání zásady HRD pro aplikaci
#### <a name="step-1-get-the-objectid"></a>Krok 1: Získání ID objectid
Použijte předchozí příklad získat **ObjectID** zásady a objektu zabezpečení aplikace, ze kterého chcete odebrat. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Krok 2: Odebrání přiřazení zásady z instančního objektu služby aplikace  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 3: Kontrola odebrání výpisem instančních objektů, ke kterým je zásada přiřazena 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak ověřování funguje ve službě Azure AD, najdete v [tématu scénáře ověřování pro Azure AD](../develop/authentication-scenarios.md).
- Další informace o jednotném přihlašování uživatelů najdete [v tématu Jednotné přihlašování k aplikacím ve službě Azure Active Directory](what-is-single-sign-on.md).
- Přehled veškerého obsahu souvisejícího s vývojáři najdete na [platformě microsoftidentity.](../develop/v2-overview.md)

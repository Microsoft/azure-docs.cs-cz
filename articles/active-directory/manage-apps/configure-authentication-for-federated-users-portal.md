---
title: Konfigurace přihlašování automatického zrychlení pro aplikaci s využitím zásad zjišťování domovské sféry | Dokumentace Microsoftu
description: Vysvětluje, jaké tenanta služby Azure AD je a jak spravovat službu Azure pomocí Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: barbkess
ms.openlocfilehash: f24be44b00f9c4e789e8d4797f6a0516dcfe940f
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494053"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurace přihlašování Azure Active Directory v chování aplikace s použitím zásad zjišťování domovské sféry

Následující dokument obsahuje úvod do konfigurace chování ověřování Azure Active Directory pro federované uživatele.   Zabývá se konfigurace automatického zrychlení a ověřování omezení pro uživatelé ve federovaných doménách.

## <a name="home-realm-discovery"></a>Zjišťování domovské sféry
Zjišťování domovské sféry (HRD) je proces, který umožňuje Azure Active Directory (Azure AD) k určení, kdy uživatel potřebuje ověřit v době přihlášení.  Když se uživatel přihlásí do tenanta služby Azure AD pro přístup k prostředku nebo k Azure AD běžné přihlašovací stránky, jejich zadejte uživatelské jméno (UPN). Azure AD, který používá ke zjišťování, ve kterém uživatel musí přihlásit. 

Uživatel může být nutné přejdete k ověření na jednu z následujících umístění:

- Domovském tenantovi uživatele (může být stejném tenantovi jako prostředek, který uživatel se pokouší o přístup k). 

- Účet Microsoft.  Uživatel je Host v prostředku tenanta.

-  Místní zprostředkovatele identity jako je Active Directory Federation Services (AD FS).

- Jiného zprostředkovatele identity, která se federuje s tenantem Azure AD.

## <a name="auto-acceleration"></a>Automatického zrychlení 
Některé organizace konfigurace domén v rámci jejich tenanta Azure Active Directory, kterou chcete Federovat s jiného zprostředkovatele identity, jako je například služba AD FS pro ověřování uživatelů.  

Když se uživatel přihlásí do aplikace, zobrazí se první jim přihlašovací stránku Azure AD. Po jejich zadali jejich hlavní název uživatele, pokud jsou do federované domény bude přesměrován na stránku přihlášení zprostředkovatele identity obsluhující tuto doménu. Za určitých okolností může být vhodné správci přesměrování uživatelů na přihlašovací stránku, když jste přihlášení k určité aplikace. 

Uživatelé díky tomu můžete přeskočit počáteční stránky Azure Active Directory. Tento proces se označuje jako "přihlášení automatického zrychlení."

V případech, kde je Federovaná tenanta do jiného zprostředkovatele identity pro přihlášení automatického zrychlení díky uživatele přihlásit více přímočarý.  Můžete nakonfigurovat automatického zrychlení pro jednotlivé aplikace.

>[!NOTE]
>Pokud nakonfigurujete aplikaci pro automatického zrychlení, nelze přihlašují uživatelé typu Host. Pokud je uživatel provést přímo na federované zprostředkovatele identity pro ověřování, neexistuje žádný způsob, jak pro ně chcete vrátit na přihlašovací stránku služby Azure Active Directory. Uživatele typu Host, kteří může být nutné k přesměrováni na jiných tenantů nebo externího zprostředkovatele identity jako je například účet Microsoft, přihlásit k dané aplikaci vzhledem k tomu, že se přeskakuje se krok zjišťování domovské sféry.  

Existují dva způsoby, jak řídit automatického zrychlení k federované identity:   

- Pomocí pomocného parametru domény na žádosti o ověření pro aplikaci. 
- Konfigurace zjišťování domovské sféry zásady pro povolení automatického zrychlení.

### <a name="domain-hints"></a>Pomocné parametry domény    
Pomocné parametry domény jsou direktivy, které jsou zahrnuty v žádosti o ověření z aplikace. Umožňuje zrychlit uživatele na federované IdP přihlašovací stránku. Nebo můžete být používají v aplikaci s více tenanty zrychlí uživatele přímo obchodní značku služby Azure AD přihlašovací stránku pro svého tenanta.  

Například může aplikace "largeapp.com" umožnit svým zákazníkům přístup k aplikaci na vlastní adresu URL "contoso.largeapp.com." Aplikace může taky obsahovat nápovědu domény contoso.com v žádosti o ověření. 

Pomocný parametr syntaxe domény se liší v závislosti na protokol, který se používá a je typicky nakonfigurován v aplikaci.

**WS-Federation**: whr=contoso.com v řetězci dotazu.

**SAML**: buď ověření požadavku SAML, který obsahuje nápovědu domény nebo whr=contoso.com řetězec dotazu.

**Otevřete připojení ID**: domain_hint=contoso.com řetězec dotazu. 

Pokud nápovědu domény je zahrnutá v žádosti o ověření z aplikace a tenanta je Federovaná pomocí dané domény, Azure AD se pokusí přesměrování přihlášení pro zprostředkovatele identity, který je nakonfigurovaný pro danou doménu. 

Pokud ověřený federovanou doménu neodkazuje v pomocném parametru domény, je ignorována a je vyvolána normální funkcí zjišťování domovské sféry.

Další informace o použití pomocných parametrů domény, které jsou podporovány službou Azure Active Directory automatického zrychlení, najdete v článku [blog Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Pokud požadavek na ověření je součástí nápovědu domény, přepíše jeho přítomnost automatického zrychlení, který je nastaven pro aplikaci v zásad HRD.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Domovské sféry zjišťování zásady pro automatického zrychlení
Některé aplikace se neposkytuje způsob, jak konfigurovat, které vysílají požadavek na ověření. V takových případech není možné použít k řízení automatického zrychlení pomocné parametry domény. Automatického zrychlení lze nakonfigurovat prostřednictvím zásad, abyste dosáhli stejné chování.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Povolení přímé ověřování pro starší verze aplikací
Osvědčeným postupem je pro aplikace, aby používaly knihovny AAD a interaktivního přihlášení k ověřování uživatelů. Tyto knihovny postará o toky federovaného uživatele.  Někdy starší aplikace, které nejsou zapsány na pochopení federace. Se neprovádí zjišťování domovské sféry a nemají možnost zasahovat správné federovaný koncový bod pro ověření uživatele. Pokud budete chtít, můžete použít zásad HRD povolit konkrétní starších aplikací, které odesílají pověření uživatelského jména a hesla k ověřování přímo s Azure Active Directory. Musí být povolena synchronizace hodnot Hash hesel. 

> [!IMPORTANT]
> Pokud máte zapnuta synchronizace hodnot Hash hesel a víte, že je možné ověřit tuto aplikaci bez všechny zásady implementované svého zprostředkovatele identity v místním pouze povolte přímé ověřování. Pokud vypnout synchronizace hodnot Hash hesel nebo vypnout synchronizaci adresářů službou AD Connect z jakéhokoli důvodu, měli byste odebrat tuto zásadu, která zabrání možnost přímého ověřování s použitím hodnoty hash hesla zastaralé.

## <a name="set-hrd-policy"></a>Nastavení zásad HRD
Existují tři kroky pro nastavení zásad HRD na aplikaci pro federované přihlašování automatického zrychlení nebo přímé cloudových aplikací:

1. Vytvoření zásad HRD.

2. Vyhledejte objekt služby, ke kterému chcete připojit zásady.

3. Připojte zásady instančnímu objektu služby. 

Zásady pouze se projeví u konkrétní aplikace, když jsou připojeny k objektu služby. 

Pouze jeden zásad HRD může být aktivní na instančního objektu v daný okamžik.  

Rutiny Microsoft Azure Active Directory Graph API přímo nebo Azure Active Directory PowerShell můžete použít k vytváření a správě zásad HRD.

Rozhraní Graph API, která zpracovává zásady je popsáno v [týkající se zásad](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) článku na webu MSDN.

Tady je příklad definici zásad HRD:
    
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

Typ zásad je "HomeRealmDiscoveryPolicy."

**AccelerateToFederatedDomain** je volitelný. Pokud **AccelerateToFederatedDomain** má hodnotu false, zásady nemá žádný vliv na automatického zrychlení. Pokud **AccelerateToFederatedDomain** je nastavena hodnota true a existuje pouze jedna ověření a federované domény v tenantovi, pak uživatelé budete přesměrováni přímo na federované zprostředkovatele identity pro přihlášení. Pokud je true a v rámci tenanta, je více než jedna ověřená doména **PreferredDomain** musí být zadán.

**PreferredDomain** je volitelný. **PreferredDomain** by měla zobrazovat domény, ke kterému chcete urychlit. Můžete vynechat, pokud tenant má pouze jeden federovanou doménu.  Pokud ji vynecháte a existuje více než jeden ověřit federované domény, zásady nemá žádný vliv.

 Pokud **PreferredDomain** není zadána, musí se shodovat ověřené federované domény pro tenanta. Všichni uživatelé aplikace musí být schopni se přihlásit k této doméně.

**AllowCloudPasswordValidation** je volitelný. Pokud **AllowCloudPasswordValidation** má hodnotu true, pak aplikace je povolené ověřování federovaného uživatele tím, že předloží pověření uživatelského jména a hesla přímo na koncový bod tokenu Azure Active Directory. Toto funguje, pouze pokud je povolená synchronizace hodnot Hash hesel.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Priorita a vyhodnocení zásad HRD
HRD zásady se dají vytvořit a potom přiřazena do konkrétních organizací a instanční objekty. To znamená, že je možné pro více zásad, které chcete použít pro konkrétní aplikaci. Zásad HRD, projeví se řídí následujícími pravidly:


- Pokud je k dispozici v žádosti o ověření nápovědu domény, je ignorován všech zásad HRD pro automatického zrychlení. Chování, které je zadaném v pomocném parametru domény se používá.

- V opačném případě zásady je explicitně přiřazeny instančnímu objektu služby, ho se nevynutí. 

- Pokud není žádná nápověda domény a není explicitně přiřazena žádná zásada instančnímu objektu služby, zásadu, která je explicitně přiřazená k organizaci nadřazeného objektu služby, které je vynucuje. 

- Pokud není žádná nápověda domény a žádná zásada byla přiřazena objektu služby nebo organizaci, použije se výchozí chování HRD.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Nastavení zásad HRD pro aplikace v tomto kurzu 
Uděláme, pomocí rutin Powershellu pro Azure AD si projít několik scénářů, včetně:


- Nastavení zásad HRD provedete automatického zrychlení pro aplikaci v tenantovi se jednotné federované domény.

- Nastavení zásad HRD provedete automatického zrychlení aplikace do jedné z několika domén, která jsou ověřena pro vašeho tenanta.

- Nastavení zásad HRD povolit starší verze aplikace pro přesměrování ověřování uživatelského jména a hesla ke službě Azure Active Directory pro federované uživatele.

- Seznam aplikací, pro které je nakonfigurované zásady.


### <a name="prerequisites"></a>Požadavky
V následujících příkladech vytvářet, aktualizovat, propojení a odstranit zásady instančních objektů aplikace ve službě Azure AD.

1.  Pokud chcete začít, stáhněte si nejnovější verze preview rutin Azure AD PowerShell. 

2.  Po stažení rutin Azure AD PowerShell, spusťte příkaz připojit k přihlášení do služby Azure AD pomocí účtu správce:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Spuštěním následujícího příkazu zobrazte všechny zásady ve vaší organizaci:

    ``` powershell
    Get-AzureADPolicy
    ```

Když se nic nevrátí, znamená to, že nemáte žádné zásady vytvořené ve vašem tenantovi.

### <a name="example-set-hrd-policy-for-an-application"></a>Příklad: Nastavení zásad HRD pro aplikaci 

V tomto příkladu vytvoříte zásadu, která po přiřazení k aplikaci buď: 
- Auto zrychluje uživatele na přihlašovací obrazovku AD FS při jsou při jedné domény ve vašem tenantovi přihlašování k aplikaci. 
- Automatické zrychluje uživatelů AD FS přihlašovací obrazovka existuje je více než jeden federované domény ve vašem tenantovi.
- Umožňuje znaménko jako neinteraktivní uživatelského jména a hesla v přímo do Azure Active Directory u federovaných uživatelů pro aplikace, které je přiřazeno zásady.

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1: Vytvoření zásad HRD

Tyto zásady automaticky zrychluje uživatele na přihlašovací obrazovku AD FS při jsou při jedné domény ve vašem tenantovi přihlašování k aplikaci.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Tyto zásady automaticky zrychluje uživatelů AD FS přihlašovací obrazovka je více než jeden federované domény ve vašem tenantovi. Pokud máte více než jeden federovanou doménu, která ověřuje uživatele pro aplikace, třeba zadat doménu, ke zrychlení automaticky.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Chcete-li vytvořit zásadu pro povolení ověřování uživatelského jména a hesla pro federované uživatele přímo se službou Azure Active Directory pro určité aplikace, spusťte následující příkaz:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Nové zásady a získat jeho **ObjectID**, spusťte následující příkaz:

``` powershell
Get-AzureADPolicy
```


Použití zásad HRD až po jeho vytvoření, ji můžete přiřadit k více instančních objektů aplikace.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2: Vyhledání instanční objekt služby, ke kterému chcete přiřadit zásady  
Je nutné **ObjectID** objektů služby, u kterých chcete zásady přiřadit. Existuje několik způsobů, jak najít **ObjectID** objektů služby.    

Můžete na portálu, nebo můžete dát dotaz na [Microsoft Graphu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Můžete také přejít na [Graph Exploreru nástroj](https://developer.microsoft.com/graph/graph-explorer) a přihlaste se ke svému účtu Azure AD budete moct zobrazit všechna firemní instančních objektů. Vzhledem k tomu, že používáte PowerShell, můžete seznam instanční objekty a jejich ID rutinu rutinu get-AzureADServicePrincipal.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3: Přiřaďte zásady instančního objektu služby  
Až budete mít **ObjectID** objektu služby aplikace, pro kterou chcete provést konfiguraci automatického zrychlení, spusťte následující příkaz. Tento příkaz přidruží zásad HRD, kterou jste vytvořili v kroku 1 se instanční objekt, který jste vyhledali v kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Tento příkaz můžete opakovat pro každý instanční objekt služby, ke kterému chcete přidat zásady.

V případě, kde aplikace už má přiřazené zásady HomeRealmDiscovery nebudete moct přidat druhý.  V takovém případě změňte definici zásady zjišťování domovské sféry, který je přiřazen do aplikace přidat další parametry.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Krok 4: Kontrola které instančních objektů aplikace je přiřazená vašich zásad HRD
Ke kontrole aplikací, které mají nakonfigurované zásady HRD, použijte **Get-AzureADPolicyAppliedObject** rutiny. Předejte ji **ObjectID** zásad, které chcete zkontrolovat.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Krok 5: Je to!
Zkuste aplikace a zjistit, že nové zásady fungují.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Příklad: Zobrazení seznamu aplikací, pro které HRD jsou nakonfigurované zásady

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1: Seznam všech zásad, které byly vytvořeny ve vaší organizaci 

``` powershell
Get-AzureADPolicy
```

Poznámka: **ObjectID** zásad, které chcete seznam přiřazení.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2: Seznam objektů služby, ke kterým je přiřazené zásady  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Příklad: Odeberte zásadu HRD pro aplikaci
#### <a name="step-1-get-the-objectid"></a>Krok 1: Získání ObjectID
V předchozím příkladu můžete získat **ObjectID** zásad a u aplikace instanční objekt, ze kterého chcete odebrat. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Krok 2: Odebrání přiřazení zásad z instančního objektu aplikace  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 3: Kontrola odebrání uvedením objektů služby, ke kterým je přiřazené zásady 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Další postup
- Další informace o tom, jak funguje ověřování ve službě Azure AD najdete v tématu [scénáře ověřování pro službu Azure AD](../develop/authentication-scenarios.md).
- Další informace o uživateli jednotného přihlašování najdete v tématu [přístup k aplikaci a jednotné přihlašování s Azure Active Directory](configure-single-sign-on-portal.md).
- Přejděte [příručky pro vývojáře Active Directory](../develop/azure-ad-developers-guide.md) získáte přehled o veškerý obsah týkající se vývojářů.

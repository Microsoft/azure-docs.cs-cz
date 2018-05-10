---
title: Konfigurace přihlášení automaticky zrychlení pro aplikace pomocí zásad zjišťování domovské sféry | Microsoft Docs
description: Vysvětluje, co klient služby Azure AD je a jak spravovat službu Azure pomocí Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: barbkess
ms.openlocfilehash: 5df12f905595c9b3e8caa8f372b9ba7b54672f81
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurace přihlášení automaticky zrychlení pro aplikace s použitím zásad zjišťování domovské sféry

Následující dokument obsahuje úvodní informace o zjišťování domovské sféry a automaticky akcelerace.

## <a name="home-realm-discovery"></a>Zjišťování domovské sféry
Zjišťování domovské sféry (HRD) je proces, který umožňuje Azure Active Directory (Azure AD) k určení, během přihlášení, kde uživatel potřebuje k ověření.  Když se uživatel přihlásí do klienta služby Azure AD pro přístup k prostředkům, nebo na Azure AD běžné přihlašovací stránku, jejich zadejte uživatelské jméno (UPN). Azure AD, využívá k vyhledávání, kde uživatel musí přihlásit. 

Uživatel může být nutné přesměrováni na jednu z následujících umístění ověřovány:

- Domácí klient uživatele (může být stejném tenantovi jako prostředek, který uživatel se pokouší o přístup k). 

- Účet Microsoft.  Uživatel je hostovaný v klientovi prostředků.

- Jiný zprostředkovatel identity, který je Federovaná pomocí klienta Azure AD.

-  Zprostředkovatele identity místní například Active Directory Federation Services (AD FS).

## <a name="auto-acceleration"></a>Akcelerace automaticky 
Některé organizace nakonfigurovat jejich klienta Azure Active Directory vytvořit federaci s jinou IdP, například služby AD FS pro ověření uživatele.  

V těchto případech když se uživatel přihlásí do aplikace, nejprve nejprve zobrazí se přihlašovací stránku služby Azure AD. Po jejich zadali jejich UPN, jsou pak přesměrováni na přihlašovací stránce deklarací identity. Za určitých okolností může správce chtít nasměrovat uživatele na přihlašovací stránku, když jste přihlášení k určité aplikace. 

To znamená, že uživatelé, můžete přeskočit úvodní stránky Azure Active Directory. Tento proces se označuje jako "přihlášení automaticky zrychlení."

V případech, kde je federovaného klienta k jiné poskytovatelů identity pro přihlášení automaticky akcelerace díky uživatele přihlásit více zjednodušený.  Můžete nakonfigurovat automatické akcelerace pro jednotlivé aplikace.

>[!NOTE]
>Pokud nakonfigurujete aplikaci pro automatické akcelerace, nemůžete se přihlásit uživatele typu Host. Pokud pořídíte uživatele přímo k federované poskytovatelů identity pro ověřování, neexistuje žádný způsob, jak pro ně nelze vrátit zpět na přihlašovací stránku služby Azure Active Directory. Host uživatele, kteří mohou potřebovat přesměrováni na jiných klientů nebo externí deklarací identity, jako je například účet Microsoft, nemůžete se přihlásit k dané aplikaci vzhledem k tomu, že jste přeskočení tohoto kroku zjišťování domovské sféry.  

Existují dva způsoby, jak řídit automaticky akcelerace k federované IdP:   

- Použijte pomocný parametr domény na žádosti o ověření pro aplikaci. 
- Konfigurace zjišťování domovské sféry zásadu k zapnutí automatického akcelerace.

## <a name="domain-hints"></a>Pomocné parametry domény 
Pomocné parametry domény jsou direktivy, které jsou zahrnuty v žádosti o ověření z aplikace. Můžete se používají k urychlit uživatele na jejich federované IdP přihlašovací stránku. Nebo můžete používají víceklientské aplikace pro urychlení uživatele rovnou na partnerské Azure AD přihlašovací stránka pro svého tenanta.  

Například může aplikace "largeapp.com" povolit zákazníkům pro přístup k aplikaci na vlastní adresu URL "contoso.largeapp.com." Aplikace také mohou zahrnovat nápovědu domény contoso.com v žádosti o ověření. 

Pomocný parametr syntaxi domény se liší v závislosti na protokol, který se používá a je typicky nakonfigurován v aplikaci.

**WS-Federation**: whr=contoso.com v řetězci dotazu.

**SAML**: buď SAML ověřování požadavek, který obsahuje nápovědu domény nebo whr=contoso.com řetězec dotazu.

**Otevřete připojení ID**: domain_hint=contoso.com řetězec dotazu. 

Pokud nápovědu domény je zahrnuta v žádosti o ověření z aplikace a klienta je Federovaná pomocí dané domény, pokusí se Azure AD přihlášení přesměrovat na deklarací identity, který je nakonfigurován pro tuto doménu. 

Pokud pomocný parametr domény neodkazuje na ověřené federované domény, je ignorováno a je vyvolána normální zjišťování domovské sféry.

Další informace o automatické zrychlení použití pomocných parametrů domény, které jsou podporovány službou Azure Active Directory najdete v tématu [Enterprise Mobility and Security blog](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Pokud požadavek na ověření je součástí domény nápovědu, přednost před jeho přítomnosti žádné zásady připojení, které jsou nastaveny pro aplikaci.

## <a name="home-realm-discovery-policy"></a>Domovské sféry zjišťování zásad
Některé aplikace neposkytují způsob, jak nakonfigurovat požadavek na ověření, které se budou posílat. V těchto případech je možné použít k řízení automaticky akcelerace pomocné parametry domény. Auto akcelerace je možné nakonfigurovat přes zásadu, abyste dosáhli stejné chování.  

### <a name="set-hrd-policy"></a>Nastavení zásad HRD
Existují tři kroky nastavení přihlášení automaticky akcelerace aplikace:


1. Vytváření zásad HRD pro akcelerace automaticky.

2. Vyhledání objektu služby, ke kterému chcete připojit zásady.

3. Zásady se připojuje k instanční objekt. Zásady pravděpodobně byl vytvořen v klientovi, ale nemají nijak neprojeví, dokud jsou připojené k entitě. 

Zásadu HRD lze připojit k hlavní název služby a v jednom okamžiku může být pouze jedna zásada HRD aktivní na danou entitu.  

Microsoft Azure Active Directory Graph API přímo nebo Azure Active Directory PowerShell rutiny můžete nastavit automatické akcelerace pomocí HRD zásad.

Rozhraní Graph API, které pracují se zásady je podrobněji popsaná [operací na zásady](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) článku na webu MSDN.

Tady je příklad HRD definice zásady:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

Typ zásad se "HomeRealmDiscoveryPolicy."

Pokud **AccelerateToFederatedDomain** je nastavena hodnota false, zásada nemá žádný vliv.

**PreferredDomain** by měl být uveden domény, ke kterému chcete zrychlit. Můžete vynechat, pokud klient má jenom jeden federovanou doménu.  Pokud je tento parametr vynechán a je více než jeden ověřit federovanou doménu, zásada nemá žádný vliv.

Pokud **PreferredDomain** není zadaný, musí se shodovat ověřené, federované domény pro klienta. Všichni uživatelé aplikace musí umět Přihlaste se k této doméně.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritu a vyhodnocení zásad HRD
HRD zásady můžete vytvořit a posléze přiřazeny k určité organizace a objekty služby. To znamená, že je možné pro víc zásad, které chcete použít pro konkrétní aplikaci. Zásady HRD, projeví se řídí následujícími pravidly:


- Pokud je k dispozici v žádosti o ověření domény nápovědu, se ignoruje žádné zásady připojení. Chování, které je zadaném v pomocném parametru domény se používá.

- Jinak Pokud zásady je explicitně přiřazen k objektu služby, vynuceno. 

- Pokud je pomocný parametr žádné domény a objekt služby je explicitně přiřazeny žádné zásady, je vyžadována zásadu, která je explicitně přiřazená k organizaci nadřazeného objektu služby. 

- Pokud neexistuje žádné pomocný parametr domény a byla přiřazena žádná zásada instanční objekt nebo organizace, použije se výchozí chování HRD.

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>Kurz pro nastavení přihlášení automaticky akcelerace aplikace pomocí zásad HRD
Pomocí rutin prostředí Azure AD PowerShell vás provede několik scénářů, včetně:


- Nastavení akcelerace automaticky pro aplikaci pro klienta s jednou doménou federované.

- Nastavení akcelerace automaticky pro aplikaci na jednu z několika domén, které je ověřeno pro vašeho klienta.

- Výpis aplikace, pro které je nakonfigurované zásady.

### <a name="prerequisites"></a>Požadavky
V následujících příkladech vytvářet, aktualizovat, propojení a odstranit zásady na objekty služby aplikace ve službě Azure AD.

1.  Pokud chcete začít, stáhněte si nejnovější verze preview rutiny Azure AD PowerShell. 

2.  Po stažení rutiny Azure AD PowerShell, spusťte příkaz připojit k přihlášení do služby Azure AD pomocí vašeho účtu správce:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Spusťte následující příkaz zobrazíte všechny zásady ve vaší organizaci:

    ``` powershell
    Get-AzureADPolicy
    ```

Když se nic nevrátí, znamená to, že nemáte žádné zásady vytvořené v klientovi.

### <a name="example-set-auto-acceleration-for-an-application"></a>Příklad: Nastavit automatické akcelerace pro aplikaci 
V tomto příkladu vytvoříte zásadu, která automaticky zrychluje uživatele na přihlašovací obrazovku AD FS při jsou přihlášení k aplikaci. Uživatelé se můžete přihlásit do služby AD FS bez nutnosti nejprve zadejte uživatelské jméno na přihlašovací stránce služby Azure AD. 

#### <a name="step-1-create-an-hrd-policy"></a>Krok 1: Vytvoření zásady HRD
``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Pokud máte jeden federované domény, který ověřuje uživatele pro aplikace, budete muset vytvořit jednu zásadu HRD.  

Chcete zobrazit nové zásady a získat jeho **ObjectID**, spusťte následující příkaz:

``` powershell
Get-AzureADPolicy
```


Pokud chcete povolit automatické akcelerace až budete mít zásadu HRD, je možné ji přiřadit několik hlavních objektů aplikace služby.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Krok 2: Vyhledejte hlavní název služby ke kterému chcete přiřadit zásady  
Je nutné **ObjectID** objektů služby, ke kterým chcete přiřadit zásady. Existuje několik způsobů, jak najít **ObjectID** objektů služby.    

Můžete použít na portálu, nebo se můžete dotazovat [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Můžete také přejít na [nástroji Průzkumník grafu](https://graphexplorer.cloudapp.net/) a přihlaste se ke svému účtu Azure AD zobrazíte objekty služby všechny vaší organizace. Vzhledem k tomu, že používáte prostředí PowerShell, můžete použít rutinu get-AzureADServicePrincipal seznam instančních objektech a jejich ID.

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Krok 3: Přiřaďte zásady instančního objektu  
Až budete mít **ObjectID** objektu služby aplikace, pro kterou chcete provést konfiguraci automatického akcelerace spusťte následující příkaz. Tento příkaz přidruží HRD zásadu, kterou jste vytvořili v kroku 1 s objekt služby, kterou jste vyhledali v kroku 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Tento příkaz můžete opakovat pro každý instanční objekt, do které chcete přidat zásadu.

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>Krok 4: Zkontrolujte, jaké objekty služby aplikace vaší automaticky akcelerace zásady přiřazeny ke
Ke kontrole aplikací, které mají nakonfigurované zásady automaticky akcelerace, použijte **Get-AzureADPolicyAppliedObject** rutiny. Předejte ji **ObjectID** zásady, které chcete zkontrolovat.

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Krok 5: Hotovo!
Zkuste aplikace a zjistit, zda je funkční nové zásady.

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>Příklad: Seznam aplikací, pro které je nakonfigurované zásady protokolu auto akcelerace

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Krok 1: Seznam všech zásad, které byly vytvořeny ve vaší organizaci 

``` powershell
Get-AzureADPolicy
```

Poznámka: **ObjectID** zásad, který chcete seznam přiřazení pro.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 2: Seznam objekty služby, ke kterým je přiřazen zásady  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>Příklad: Odebrání zásad protokolu auto akcelerace pro aplikace
#### <a name="step-1-get-the-objectid"></a>Krok 1: Získání ObjectID
Získat pomocí předchozího příkladu **ObjectID** zásady, a u hlavní služba aplikace, ze kterého chcete odebrat. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Krok 2: Odebrání přiřazení zásady instančního aplikace  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Krok 3: Kontrola odebrání tak, že uvedete objekty služby, ke kterým je přiřazen zásady 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>Další postup
- Další informace o fungování ověřování ve službě Azure AD najdete v tématu [scénáře ověřování pro Azure AD](develop/active-directory-authentication-scenarios.md).
- Další informace o uživatele jednotné přihlašování najdete v tématu [přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).
- Přejděte [Příručka pro vývojáře Active Directory](develop/active-directory-developers-guide.md) přehled veškerý obsah pro vývojáře.

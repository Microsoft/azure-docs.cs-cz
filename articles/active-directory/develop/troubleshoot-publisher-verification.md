---
title: Řešení potíží s ověřováním vydavatele – Microsoft Identity Platform | Azure
description: Popisuje řešení potíží s ověřováním vydavatele pro platformu Microsoft Identity voláním Microsoft Graph rozhraní API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: fd49e922e5952f5a7c4b7f477dd33d6518010428
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088319"
---
# <a name="troubleshoot-publisher-verification"></a>Řešení potíží s ověřením vydavatele
Pokud se vám nedaří dokončit proces nebo došlo k neočekávanému chování při [ověřování vydavatele](publisher-verification-overview.md), měli byste začít následujícím způsobem, pokud obdržíte chyby nebo neočekávané chování: 

1. Zkontrolujte [požadavky](publisher-verification-overview.md#requirements) a zajistěte, aby byly splněné.

1. Přečtěte si pokyny, jak [Označit aplikaci jako Publisher jako ověřený](mark-app-as-publisher-verified.md) a zda byly všechny kroky úspěšně provedeny.

1. Projděte si seznam [běžných problémů](#common-issues).

1. Reprodukování požadavku pomocí [Průzkumníka graphu](#making-microsoft-graph-api-calls) získáte další informace a vyloučíte všechny problémy v uživatelském rozhraní.

## <a name="common-issues"></a>Běžné problémy
Níže jsou uvedeny některé běžné problémy, ke kterým může dojít během procesu. 

- **Nevím, Microsoft Partner Network ID (MPN ID), nebo neznáte, že je primárním kontaktem na účet** 
    1. Přejít na [stránku registrace MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Přihlaste se pomocí uživatelského účtu v primárním tenantovi Azure AD organizace. 
    1. Pokud účet MPN již existuje, bude rozpoznán a bude přidán k účtu. 
    1. Přejděte na [stránku profil partnera](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , kde se zobrazí kontakt s ID MPN a primárním účtem.

- **Nevím, kdo má globální správce Azure AD (označovaný taky jako správce společnosti nebo správce tenanta), jak je najít? K čemu správce aplikace nebo správce cloudové aplikace?**
    1. Přihlaste se k [portálu Azure AD](https://aad.portal.azure.com) pomocí uživatelského účtu v primárním tenantovi vaší organizace.
    1. Přejít ke [správě rolí](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Klikněte na požadovanou roli správce.
    1. Zobrazí se seznam uživatelů s přiřazenou rolí.

- Nevím **, kdo jsou správci (e) pro svůj účet MPN** Přejděte na [stránku správy uživatelů MPN](https://partner.microsoft.com/pcv/users) a vyfiltrujte si seznam uživatelů, abyste viděli, kteří uživatelé jsou v různých rolích správce.

- **Zobrazuje se chyba oznamující, že ID programu MPN je neplatné nebo ke kterému nemám přístup.**
    1. Přejít na svůj [partnerský profil](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) a ověřit, zda: 
        - ID MPN je správné. 
        - Neexistují žádné chyby nebo se zobrazuje zpráva "čeká na vyřízení akce" a stav ověření v souladu s platným obchodním profilem a informacemi o partnerovi znamená "autorizováno" nebo "úspěch".
    1. Přejdete na [stránku správy tenanta MPN](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) a ověřte, že je tenant, ve kterém je aplikace registrovaná, a že se k němu přihlašujete pomocí uživatelského účtu, který je na seznamu přidružených tenantů. Pokud potřebujete přidat dalšího tenanta, postupujte podle pokynů uvedených [tady](https://docs.microsoft.com/partner-center/multi-tenant-account). Uvědomte si prosím, že všem globálním správcům každého tenanta, kterého přidáte, budou udělena globální oprávnění správce na účtu partnerského centra.
    1. Navštivte [stránku Správa uživatelů MPN](https://partner.microsoft.com/pcv/users) a potvrďte uživatele, ke kterému se přihlašujete, protože je to buď globální správce, správce programu MPN nebo správce účtů. Pokud potřebujete přidat uživatele k roli v partnerském centru, postupujte podle pokynů uvedených [tady](https://docs.microsoft.com/partner-center/create-user-accounts-and-set-permissions).

- **Když se přihlásím k portálu Azure AD, nevidím žádné registrované aplikace. Proč?** 
    Registrace aplikací mohla být vytvořena pomocí jiného uživatelského účtu v tomto tenantovi, účtu osobní/příjemce nebo v jiném tenantovi. Ujistěte se, že jste přihlášeni pomocí správného účtu v tenantovi, ve kterém byly vytvořeny registrace aplikací.

- **Zobrazuje se chyba související se službou Multi-Factor Authentication. Co mám dělat?** 
    Ujistěte se prosím, že je pro uživatele, se kterým se přihlašujete, zapnuté služby [Multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) a v tomto scénáři se vyžaduje. Vícefaktorové ověřování může být například:
    - Vždy vyžadováno pro uživatele, se kterým se přihlašujete
    - [Vyžaduje se pro správu Azure](../conditional-access/howto-conditional-access-policy-azure-management.md).
    - [Vyžaduje se pro typ Správce](../conditional-access/howto-conditional-access-policy-admin-mfa.md) , se kterým se přihlašujete.

## <a name="making-microsoft-graph-api-calls"></a>Vytváření volání rozhraní API Microsoft Graph 

Pokud máte problém, ale nemůžete pochopit, proč na základě toho, co vidíte v uživatelském rozhraní, může být užitečné provést další řešení potíží pomocí Microsoft Graph volání k provádění stejných operací, které můžete provádět na portálu pro registraci aplikací.

Nejjednodušší způsob, jak tyto požadavky udělat, je použití [Průzkumníka graphu](https://developer.microsoft.com/graph/graph-explorer). Můžete také zvážit další možnosti, jako je použití [post](https://www.postman.com/), nebo použití PowerShellu k [vyvolání webové žádosti](/powershell/module/microsoft.powershell.utility/invoke-webrequest).  

Pomocí Microsoft Graph můžete nastavit a zrušit ověření vydavatele aplikace a po provedení jedné z těchto operací zkontrolovat výsledek. Výsledek se může zobrazit jak pro [aplikační](/graph/api/resources/application) objekt, který odpovídá registraci vaší aplikace, a jakýmkoli [instančním objektům](/graph/api/resources/serviceprincipal) , které z této aplikace vytvořily instance. Další informace o vztahu mezi těmito objekty naleznete v tématu: [aplikace a objekty zabezpečení služby v Azure Active Directory](app-objects-and-service-principals.md).  

Tady jsou příklady některých užitečných požadavků:  

### <a name="set-verified-publisher"></a>Nastavit ověřeného vydavatele 

Žádost

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Odpověď 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* je vaše ID MPN. 

### <a name="unset-verified-publisher"></a>Zrušit stav ověřeného vydavatele 

Požadavek:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Odpověď 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Získat ověřené informace o vydavateli z aplikace 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Získat informace o ověřeném vydavateli z instančního objektu 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Odkaz na chybu 

Následuje seznam možných chybových kódů, které můžete obdržet, při řešení potíží s Microsoft Graph nebo v průběhu procesu na portálu pro registraci aplikací.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

Zadané ID MPN ( <MPNID> ) neexistuje nebo k němu nemáte přístup. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

Zadané ID MPN ( <MPNID> ) je neplatné. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

Zadané ID MPN ( <MPNID> ) je neplatné. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

ID MPN ( <MPNID> ), které jste zadali, nedokončilo proces dozvíte ČSFD. Tento proces dokončete v partnerském centru a zkuste to znovu. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

Zadané ID MPN ( <MPNID> ) je neplatné. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

Zadané ID MPN ( <MPNID> ) je neplatné. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="applicationnotfound"></a>ApplicationNotFound  

Cílovou aplikaci ( <AppId> ) nelze nalézt. Zadejte platné ID aplikace a zkuste to znovu. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Tato funkce není v tenantovi Azure AD B2C podporována. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Tato funkce není podporována v tenantovi ověřeném e-mailem. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

Cílová aplikace ( \<AppId\> ) musí mít sadu domény vydavatele. Nastavte doménu vydavatele a zkuste to znovu.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

Doména vydavatele cílové aplikace () se <publisherDomain> neshoduje s doménou použitou k ověřování e-mailů v partnerském centru ( <pcDomain> ). Zajistěte, aby tyto domény odpovídaly, a zkuste to znovu. 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

Nemáte oprávnění k nastavení vlastnosti ověřený vydavatel v aplikaci ( <AppId> ). 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

ID MPN nebylo v textu žádosti zadáno nebo typ obsahu požadavku nebyl "Application/JSON". 

### <a name="msanotsupported"></a>MSANotSupported  

Tato funkce není u zákaznických účtů Microsoftu podporována. Podporují se jenom aplikace zaregistrované ve službě Azure AD pro uživatele Azure AD. 

## <a name="next-steps"></a>Další kroky

Pokud jste zkontrolovali všechny předchozí informace a stále dochází k chybě z Microsoft Graph, Shromážděte co nejvíc z následujících informací, které souvisejí s chybnou žádostí a [kontaktujte podporu Microsoftu](developer-support-help-options.md#open-a-support-request).

- Timestamp 
- CorrelationId 
- ObjectID nebo UserPrincipalName přihlášeného uživatele 
- Identifikátor ObjectId cílové aplikace
- Identifikátor AppId cílové aplikace
- TenantId, kde je aplikace zaregistrovaná
- ID MPN
- Probíhají žádosti REST 
- Kód chyby a zpráva vracené 

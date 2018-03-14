---
title: "Aplikace, oprávnění a vyjádření souhlasu v Azure Active Directory | Dokumentace Microsoftu"
description: "Azure AD Connect integruje vaše místní adresáře do služby Azure Active Directory. To umožní poskytovat společnou identitu pro aplikace Office 365, Azure a SaaS integrované s Azure AD."
keywords: "úvod k Azure AD, aplikace, co je Azure AD Connect, instalace active directory"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: d3e14c18d5e4cd77f4c68d8a5d9d0b915e896695
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2018
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Aplikace, oprávnění a vyjádření souhlasu v Azure Active Directory
V rámci Azure Active Directory můžete do svého adresáře přidávat aplikace.  Aplikace se mohou lišit v závislosti na typu aplikace.  Pokud chcete zobrazit aplikace na portálu, vyberte adresář a zvolte požadované aplikace.

> [!IMPORTANT]
> Společnost Microsoft doporučuje při správě služby Azure AD používat [centrum pro správu Azure AD](https://aad.portal.azure.com) na webu Azure Portal (místo webu Azure Portal, na který odkazuje tento článek).

## <a name="types-of-apps"></a>Typy aplikací

1. **Aplikace s jedním tenantem** </br>
    - **Aplikace s jedním tenantem** – Často označované jako obchodní aplikace. To je případ, kdy někdo v rámci vaší organizace vytvoří vlastní aplikaci a chce umožnit uživatelům v organizaci přihlásit se k ní.
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **Aplikace App Proxy** – Pokud zveřejňujete místní aplikaci pomocí Azure AD App Proxy, zaregistruje se ve vašem tenantovi aplikace s jedním tenantem (vedle služby App Proxy). Taková aplikace reprezentuje vaši místní aplikaci pro veškeré cloudové interakce (například ověřování). (App Proxy vyžaduje Azure AD Basic nebo vyšší.)


2. **Aplikace s více tenanty**
    - **Aplikace s více tenanty, se kterými mohou ostatní vyjádřit souhlas** – Podobné jako aplikace s jedním tenantem, které vyvíjí vaše organizace. Hlavní rozdíl (kromě samotné logiky aplikace) je, že uživatelé z jiných tenantů mohou také vyjádřit souhlas s aplikací a přihlašovat se k ní.</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **Aplikace s více tenanty vyvíjené ostatními, se kterými může Contoso vyjádřit souhlas**. (zkráceně odsouhlasené aplikace) Jedná se o pravý opak aplikací s více tenanty, které vyvíjí vaše organizace. Když některá jiná organizace vytvoří aplikaci s více tenanty, uživatelé vaší organizace s ní budou moci vyjádřit souhlas a přihlašovat se k ní.
    - **Vlastní aplikace Microsoftu** – Aplikace, které představují služby Microsoftu. Vyjádřením souhlasu se rozumí registrace do služby. Některé vlastní aplikace mohou obsahovat speciální uživatelské prostředí a logiku, které často slouží k vytváření zásad kolem přístupu k aplikaci.</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **Předem integrované aplikace** – Aplikace dostupné v Azure AD App Gallery, které můžete přidat do svého adresáře pro poskytování jednotného přihlašování k oblíbeným aplikacím SaaS (v některých případech i jejich zřizování).
    - **Jednotné přihlašování Azure AD**: Opravdové jednotné přihlašování pro aplikace, které lze integrovat s Azure AD, prostřednictvím podporovaného přihlašovacího protokolu, jako je SAML 2.0 nebo OpenID Connect. Průvodce vás provede procesem jeho nastavení.
    - **Jednotné přihlašování pomocí hesla**: Azure AD bezpečně ukládá přihlašovací údaje uživatele k aplikaci. Rozšíření prohlížeče Azure AD App Access následně přihlašovací údaje vkládá do přihlašovacího formuláře. Označuje se také jako ukládání hesel do trezoru.

## <a name="permissions"></a>Oprávnění

Při registraci aplikace uživatel, který registraci aplikace provádí (tedy vývojář), definuje, k jakým oprávněním a prostředkům potřebuje aplikace přístup. (Prostředky samotné jsou definované jako ostatní aplikace.) Například někdo, kdo vytváří aplikaci pro čtení pošty, by uvedl, že jeho aplikace požaduje oprávnění „Access mailboxes as the signed-in user“ (Přístup k poštovním schránkám jménem přihlášeného uživatele) v prostředku „Office 365 Exchange Online“:
    
![](media/active-directory-apps-permissions-consent/apps6.png)

Aby mohla jedna aplikace (klient) požadovat určitá oprávnění z jiné aplikace (prostředku), vývojář aplikace prostředku definuje existující oprávnění. V našem příkladu Microsoft, vlastník aplikace prostředku „Office 365 Exchange Online“, definoval oprávnění s názvem „Access mailboxes as the signed-in user“ (Přístup k poštovním schránkám jménem přihlášeného uživatele).

Při definování oprávnění musí vývojář aplikace definovat, jestli je možné s oprávněním vyjádřit souhlas, nebo jestli vyžaduje souhlas správce. Vývojáři tak mohou umožnit uživatelům samostatně vyjádřit souhlas s aplikacemi, které požadují méně citlivá oprávnění, a zároveň vyžadovat souhlas správce, pokud jde o citlivějšími oprávněními. Například aplikace prostředku Azure Active Directory je definována tak, že aby uživatelé mohli vyjadřovat souhlas s aplikacemi, vyžaduje omezená oprávnění pouze pro čtení.  Pro úplná oprávnění ke čtení a veškerá oprávnění k zápisu však vyžaduje souhlas správce.

Vzhledem k tomu, že se nativní klienti neověřují, aplikace definovaná jako nativní klientská aplikace může požadovat pouze delegovaná oprávnění. To znamená, že získání tokenu se musí vždy účastnit i skutečný uživatel. Webové aplikace a webová rozhraní API (důvěrní klienti) se musí při každém získání přístupového tokenu ověřovat pomocí Azure AD. To znamená, že mají také možnost požadovat oprávnění jen pro aplikace. Pokud se například jedna služba back-end potřebuje ověřit v jiné službě back-end. Aplikace vyžadující oprávnění jen pro aplikace vždy vyžadují souhlas správce.

Shrňme si to:



- Aplikace (klient) uvádí pro ostatní aplikace (prostředky), jaká oprávnění potřebuje.
- Aplikace (prostředek) uvádí pro ostatní aplikace (klienty), jaká oprávnění vystavuje.
- Oprávnění může být jen pro aplikace nebo delegované.
- Delegované oprávnění může být označeno jako „umožňuje souhlas uživatele“ nebo „vyžaduje souhlas správce“.
- Aplikace se může chovat jako klient (prohlášením, že potřebuje oprávnění k prostředku), jako prostředek (prohlášením, jaká oprávnění vystavuje), nebo jako obojí.

## <a name="controls"></a>Ovládací prvky

Následuje seznam ovládacích prvků správce, které jsou k dispozici pro toto chování.

Na webu Azure Portal v části **Spravovat**, **Uživatelské nastavení**.

![](media/active-directory-apps-permissions-consent/apps11.png)



- Můžete řídit, jestli uživatelé mohou souhlasit s aplikacemi:

Na webu Azure Portal vyberte **Uživatelé můžou povolit aplikacím přístup ke svým datům**.
![](media/active-directory-apps-permissions-consent/apps12.png)



- Můžete řídit, jestli uživatelé mohou registrovat vlastní aplikace LOB s jedním tenantem:

Na webu Azure Portal vyberte **Uživatelé můžou registrovat aplikace**.
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>I v případě, že uživatelům umožníte registrovat obchodní aplikace pro jednoho tenanta, existují omezení ohledně toho, co mohou registrovat.  
>Například vývojáři, kteří nejsou správci adresáře.
>
>- Uživatelé nemohou udělat z aplikace pro jednoho tenanta aplikaci pro více tenantů.
>- Při registraci obchodní aplikace pro jednoho tenanta uživatelé nemohou požadovat oprávnění jen pro aplikace k jiným aplikacím.
>- Při registraci obchodní aplikace pro jednoho tenanta uživatelé nemohou požadovat delegovaná oprávnění k jiným aplikacím, pokud taková oprávnění vyžadují souhlas správce.
>- Uživatelé nemohou provádět změny aplikací, pokud nejsou jejich vlastníky.


## <a name="example"></a>Příklad

Jako příklad si vezměme aplikaci FabrikamMail for Office 365, protože jste si všimli, že se k ní připojují uživatelé ve vašem tenantovi. FabrikamMail je aplikace pro čtení pošty pro Android vytvořená společností Fabrikam, Inc. Spadá do kategorie Aplikace s více tenanty vyvíjené ostatními, se kterými může Contoso vyjádřit souhlas.

Pokud je uživatelům dovoleno vyjadřovat souhlas, při prvním přihlášení se jim zobrazí dialogové okno pro vyjádření souhlasu: ![](media/active-directory-apps-permissions-consent/apps14.png)

„Access your mailboxes“ (Přístup k vašim poštovním schránkám) je řetězec souhlasu zobrazený uživateli pro oprávnění „Access mailboxes as the signed-in user“ (Přístup k poštovním schránkám jménem přihlášeného uživatele), které vystavuje Office 365 Exchange Online (tedy Exchange).

Oprávnění můžete zobrazit vyhledáním objektu ServicePricipal pro Exchange (prostředek), který se přidal, když jste se zaregistrovali k Office 365. Objekt ServicePrincipal si můžete představit jako instanci aplikace ve vašem tenantovi, která slouží k zaznamenávání různých možností a konfigurací.  Můžete to zobrazit pomocí rutiny prostředí PowerShell `Get-AzureADServicePrincipal`.

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

Souhlas začne platit, když uživatel klikne na Přijmout. Nejprve se v tenantovi vytvoří objekt ServicePrincipal pro aplikaci FabrikamMail for Office 365. Objekt ServicePrincipal vypadá nějak takto:

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

Souhlas s aplikací vytvoří propojení Oauth2PermissionGrant mezi následujícími položkami:
  
- objekt uživatele
- ServicePrincipalName (hlavní název služby – SPN) klientské aplikace
- ServicePrincipalName (hlavní název služby – SPN) aplikace prostředku
- oprávnění v aplikaci prostředku  

V případě FabrikamMail to vypadá přibližně takto:

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId** je ID instančního objektu aplikace FabrikamMail (který se právě vytvořil), **PrincipalId** je ID objektu uživatele (který vyjádřil souhlas), **ResourceId** je ID instančního objektu aplikace Exchange, Scope je oprávnění v aplikaci Exchange, se kterým se souhlasilo).

Pokud uživatelům není povoleno vyjadřovat souhlas, zobrazí se jim obrazovka s upozorněním, že je vyžadováno oprávnění.


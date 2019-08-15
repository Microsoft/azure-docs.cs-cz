---
title: Oprávnění role vlastního správce pro správu registrace aplikací – Azure Active Directory | Microsoft Docs
description: Oprávnění role vlastního správce pro delegování správy identit.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f31c5928273973a9089ae9ef1fd184cdb78bbb
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033338"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Podtypy registrace aplikace a oprávnění v Azure Active Directory

Tento článek obsahuje aktuálně dostupná oprávnění k registraci aplikací pro vlastní definice rolí v Azure Active Directory (Azure AD).

## <a name="single-tenant-v-multi-tenant-permissions"></a>Jeden tenant v. oprávnění pro více tenantů

Oprávnění vlastní role se liší pro aplikace s jedním a více klienty. Aplikace s jedním klientem jsou k dispozici pouze uživatelům v organizaci Azure AD, kde je aplikace zaregistrovaná. Víceklientské aplikace jsou dostupné všem organizacím Azure AD. Aplikace s jedním klientem jsou definované jako **podporované typy účtů** nastavené na účty pouze v tomto organizačním adresáři. V Graph API mají jednotlivé klientské aplikace vlastnost signInAudience nastavenou na hodnotu "AzureADMyOrg".

## <a name="application-registration-subtypes-and-permissions"></a>Podtypy a oprávnění registrace aplikace

V tématu [Přehled vlastních rolí](roles-custom-overview.md) najdete vysvětlení významu podtypu, oprávnění a sady vlastností obecných podmínek. Následující informace jsou specifické pro registraci aplikací.

### <a name="subtypes"></a>Podtypy

K dispozici je jen jedna podtype registrace aplikace: Applications. myOrganization. Například Microsoft. Directory/Applications. myOrganization/Basic/Update. Tento podtyp je nastaven na stránce **ověřování** pro konkrétní registraci aplikace a odpovídá nastavení vlastnosti signInAudience na "AzureADMyOrg" pomocí Graph API nebo PowerShellu. Podtyp omezuje oprávnění k registraci aplikací, které jsou označeny jako přístupné pouze pomocí účtů ve vaší organizaci (aplikace s jedním klientem).

Pomocí omezeného oprávnění můžete udělit oprávnění ke čtení nebo správě pro interní aplikace jenom bez udělení oprávnění ke čtení nebo správě pro aplikace přístupné pro účty v jiných organizacích.

Existují aplikace. myOrganization verze všech oprávnění ke čtení a aktualizaci a také oprávnění k odstranění. V tuto chvíli nejsou k dispozici žádné aplikace. myOrganization verze vytváření. Standardní oprávnění (například Microsoft. Directory/Applications/Basic/Update) udělují oprávnění ke čtení nebo správě pro všechny typy registrace aplikací.

![Deklarace aplikace s jedním tenantům nebo víceklientské aplikace](./media/roles-custom-available-permissions/supported-account-types.png)

Podrobnosti o následujících oprávněních pro vlastní role Preview jsou uvedené v části [dostupná oprávnění vlastní role v Azure Active Directory](roles-custom-available-permissions.md).

### <a name="create-and-delete"></a>Vytvořit a odstranit

Existují dvě oprávnění, která jsou k dispozici pro udělení možnosti vytvářet registrace aplikací, z nichž každá má jiné chování:

- **Microsoft. Directory/Applications/createAsOwner**: Když se tato oprávnění přiřadí, přiřadí se autor jako první vlastník vytvořené registrace aplikace a registrace vytvořené aplikace se bude počítat s kvótou vytvořených objektů tvůrce 250.
- **Microsoft. Directory/applicationPolicies/Create**: Když se tato oprávnění přiřadí, nepřidá se tvůrce jako první vlastník vytvořené registrace aplikace a registrace vytvořené aplikace se nebude počítat s kvótou vytvořených objektů tvůrce 250. Toto oprávnění používejte opatrně, protože nebrání žádnému neoprávněnému nabyvateli v vytváření registrací aplikací, dokud se nedosáhne kvóty na úrovni adresáře. Pokud jsou přiřazena obě oprávnění, má tato oprávnění přednost.

Pokud jsou přiřazena obě oprávnění, bude mít přednost oprávnění/CREATE. I když oprávnění/createAsOwner nepřidá tvůrce automaticky jako prvního vlastníka, můžou se vlastníci zadat během vytváření registrace aplikace při použití rozhraní Graph API nebo rutin PowerShellu.

Oprávnění CREATE udělte přístup k příkazu **New Registration** .

[Tato oprávnění udělují přístup k novému registračnímu portálu příkazu](./media/roles-create-custom/new-custom-role.png)

Existují dvě oprávnění, která jsou k dispozici pro udělení možnosti odstraňovat registrace aplikací:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Udělí možnost odstraňovat registrace aplikací bez ohledu na podtyp; To znamená jeden tenant i víceklientské aplikace.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. myOrganization/DELETE

Udělí možnost odstraňovat registrace aplikací omezené na ty, které jsou přístupné jenom účtům ve vaší organizaci nebo aplikacích s jedním klientem (myOrganization podtyp).

![Tato oprávnění udělují přístup k příkazu Odstranit registraci aplikace](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Při přiřazování role, která obsahuje oprávnění k vytvoření, je nutné přiřazení role provést v oboru adresáře. Oprávnění vytvořit přiřazené v oboru prostředku neuděluje možnost vytvářet registrace aplikací.

### <a name="read"></a>Čtení

Všichni členové členských uživatelů v organizaci můžou ve výchozím nastavení číst informace o registraci aplikace. Uživatelé typu Host a instanční objekty aplikace ale nemůžou. Pokud plánujete přiřadit roli uživateli nebo aplikaci typu Host, musíte zahrnout příslušná oprávnění ke čtení.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/Applications/allProperties/Read

Možnost číst všechny vlastnosti jednoho klienta a víceklientské aplikace mimo citlivé vlastnosti, jako jsou přihlašovací údaje.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. myOrganization/allProperties/Read

Udělí stejná oprávnění jako Microsoft. Directory/Applications/allProperties/Read, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsstandardread-grants-access-to-all-fields-on-the-application-registration-branding-page"></a>Microsoft. Directory/Applications/Standard/Read: Udělí přístup ke všem polím na stránce s informacemi o registraci aplikace.

![Toto oprávnění uděluje přístup k stránce s informacemi o registraci aplikace.](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. myOrganization/Standard/Read

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Standard/Read, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/aplikace/vlastníci/čtení

Uděluje možnost číst vlastníky v aplikacích pro jednoho tenanta a víceklientské aplikace. Uděluje přístup ke všem polím na stránce vlastníci registrace aplikace:

![Tato oprávnění udělují přístup k stránce vlastníci registrace aplikací.](./media/roles-custom-available-permissions/app-registration-owners.png)

Udělí přístup k následujícím vlastnostem v entitě aplikace:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppCreatedDateTime
- Aplikací
- ID aplikace
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

### <a name="update"></a>Aktualizace

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/allProperties/Update

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. myOrganization/allProperties/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/allProperties/Update, ale pouze pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Uděluje přístup ke všem polím na stránce ověřování registrace aplikace:

![Toto oprávnění uděluje přístup ke stránce ověřování registrace aplikace.](./media/roles-custom-available-permissions/supported-account-types.png)

Uděluje přístup k následujícím vlastnostem prostředku aplikace:

- AvailableToOtherTenants
- SignInAudience

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. myOrganization/publikum/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/publikum/Update, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Možnost aktualizace adres URL odpovědi, adresy URL pro odhlášení, implicitního toku a vlastností domény vydavatele v rámci jednoho tenanta a víceklientské aplikace. Udělí přístup ke všem polím na stránce ověřování registrace aplikace s výjimkou podporovaných typů účtů:

![Udělí přístup k ověřování registrace aplikace, ale ne k podporovaným typům účtů.](./media/roles-custom-available-permissions/supported-account-types.png)

 Uděluje přístup k následujícím vlastnostem prostředku aplikace:

- AcceptMappedClaims
- AccessTokenAcceptedVersion
- AddIns
- GroupMembershipClaims
- IsDeviceOnlyAuthSupported
- OAuth2LegacyUrlPathMatching
- OauthOidcResponsePolicyBitmap
- OptionalClaims
- OrgRestrictions
- PublicClient
- UseCustomTokenSigningKey

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. myOrganization/Authentication/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Authentication/Update, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Možnost aktualizace vlastností název, logo, adresa URL domovské stránky, adresa URL podmínek služby a vlastnosti adresy URL prohlášení o zásadách ochrany osobních údajů v aplikacích pro jednoho tenanta a víceklientské úrovni. Udělí přístup ke všem polím na stránce s informacemi o registraci aplikace:

![Toto oprávnění uděluje přístup k stránce s informacemi o registraci aplikace.](./media/roles-custom-available-permissions/app-registration-branding.png)

Uděluje přístup k následujícím vlastnostem prostředku aplikace:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- Aplikací
- ID aplikace
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. myOrganization/Basic/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Basic/Update, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Možnost aktualizace certifikátů a vlastností tajných kódů klienta v aplikacích s jedním klientem a víceklientské aplikace. Udělí přístup ke všem polím na stránce certifikáty pro registraci aplikací & tajných kódů:

![Toto oprávnění uděluje přístup k registračním certifikátům aplikací & stránce tajných klíčů.](./media/roles-custom-available-permissions/app-registration-secrets.png)

Uděluje přístup k následujícím vlastnostem prostředku aplikace:
- AsymmetricKey
- EncryptedSecretKey
- Popis
- SharedKeyReference
- TokenEncryptionKeyId

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. myOrganization/přihlašovací údaje/aktualizace

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Credentials/Update, ale jenom pro aplikace s jedním adresářem.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Možnost aktualizovat vlastnost Owner v jednom tenantovi a víceklientském tenantovi. Uděluje přístup ke všem polím na stránce vlastníci registrace aplikace:

![Tato oprávnění udělují přístup k stránce vlastníci registrace aplikací.](./media/roles-custom-available-permissions/app-registration-owners.png)

Uděluje přístup k následujícím vlastnostem prostředku aplikace:
- Vlastníci

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. myOrganization/Owners/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Owners/Update, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Možnost aktualizovat delegovaná oprávnění, oprávnění aplikace, autorizované klientské aplikace, požadovaná oprávnění a udělit vlastnosti souhlasu pro jeden tenant a víceklientské aplikace. Neuděluje možnost provádět souhlas. Uděluje přístup všem polím v rozhraní API pro registraci aplikace a zpřístupňuje stránky rozhraní API:

![Tato oprávnění udělují přístup ke stránce oprávnění rozhraní API pro registraci aplikace.](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Tato oprávnění udělují přístup k registraci aplikace zobrazit stránku rozhraní API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

Uděluje přístup k následujícím vlastnostem prostředku aplikace:

- AppIdentifierUri
- Nárok
- PreAuthorizedApplications
- RecordConsentConditions
- RequiredResourceAccess

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. myOrganization/oprávnění/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Permissions/Update, ale jenom pro aplikace s jedním klientem.

## <a name="required-license-plan"></a>Požadovaný licenční plán

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další postup

- Vytváření vlastních rolí pomocí [Azure Portal, Azure AD PowerShellu a Graph API](roles-create-custom.md)
- [Zobrazení přiřazení pro vlastní roli](roles-view-assignments.md)

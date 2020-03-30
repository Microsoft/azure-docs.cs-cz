---
title: Dostupná oprávnění role vlastního správce – Azure AD | Dokumenty společnosti Microsoft
description: Vlastní oprávnění role správce pro delegování správy identit.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025153"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Podtypy a oprávnění registrace aplikací ve službě Azure Active Directory

Tento článek obsahuje aktuálně dostupná oprávnění k registraci aplikací pro vlastní definice rolí ve službě Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Oprávnění pro správu aplikací s jedním adresářem

Při výběru oprávnění pro vlastní roli máte možnost udělit přístup ke správě pouze aplikací s jedním adresářem. Aplikace s jedním adresářem jsou k dispozici jenom pro uživatele v organizaci Azure AD, kde je aplikace registrována. Aplikace s jedním adresářem jsou definovány tak, že mají **podporované typy účtů** nastavené na "Účty pouze v tomto organizačním adresáři". V rozhraní API grafu mají aplikace s jedním adresářem vlastnost signInAudience nastavenou na "AzureADMyOrg.".

Chcete-li udělit přístup pouze ke správě aplikací s jedním adresářem, použijte níže uvedená oprávnění s podtypem **applications.myOrganization**. Například microsoft.directory/applications.myOrganization/basic/update.

Vysvětlení, co znamenají obecné termíny podtyp, oprávnění a sada vlastností, najdete v [přehledu vlastních rolí.](roles-custom-overview.md) Následující informace jsou specifické pro registrace žádostí.

### <a name="create-and-delete"></a>Vytvořit a odstranit

Existují dvě oprávnění k dispozici pro udělení možnosti vytvářet registrace aplikací, z nichž každé s jiným chováním:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>Microsoft.directory/applications/createAsOwner

Přiřazení tohoto oprávnění má za následek, že tvůrce bude přidán jako první vlastník registrace vytvořené aplikace a registrace vytvořené aplikace se započítá do kvóty 250 vytvořených objektů tvůrce.

#### <a name="microsoftdirectoryapplicationscreate"></a>Microsoft.directory/applications/create

Přiřazení tohoto oprávnění způsobí, že tvůrce nebude přidán jako první vlastník registrace vytvořené aplikace a registrace vytvořené aplikace se nezapočítá v kvótu 250 vytvořených objektů tvůrce. Toto oprávnění používejte opatrně, protože postupitele nic nebrání ve vytváření registrací aplikací, dokud nebude kvóta na úrovni adresáře přístupová. Pokud jsou přiřazena obě oprávnění, má toto oprávnění přednost.

Pokud jsou přiřazena obě oprávnění, oprávnění /create bude mít přednost. Ačkoli oprávnění /createAsOwner automaticky nepřidá tvůrce jako prvního vlastníka, vlastníci mohou být určeni při vytváření registrace aplikace při použití grafových api nebo rutin prostředí PowerShell.

Vytvořte oprávnění udělit přístup k příkazu **Nová registrace.**

[Tato oprávnění udělují přístup k příkazu Nový registrační portál.](./media/roles-create-custom/new-custom-role.png)

Pro udělení možnosti odstranění registrací aplikací jsou k dispozici dvě oprávnění:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Uděluje možnost odstranit registrace aplikací bez ohledu na podtyp; to znamená aplikace s jedním klientem i více klienty.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

Uděluje možnost odstranit registrace aplikací omezené na ty, které jsou přístupné pouze pro účty ve vaší organizaci nebo aplikace s jedním tenantem (podtyp myOrganization).

![Tato oprávnění udělují přístup k příkazu Odstranit registraci aplikace.](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Při přiřazování role, která obsahuje oprávnění k vytvoření, musí být přiřazení role provedeno v oboru adresáře. Oprávnění vytvořit přiřazené v oboru prostředků neuděluje možnost vytvářet registrace aplikací.

### <a name="read"></a>Čtení

Všichni členové uživatelé v organizaci mohou ve výchozím nastavení číst informace o registraci aplikace. Uživatelé typu Host a instanční objekty aplikačních služeb však nemohou. Pokud plánujete přiřadit roli uživateli typu Host nebo aplikaci, musíte zahrnout příslušná oprávnění ke čtení.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft.directory/applications/allProperties/read

Možnost číst všechny vlastnosti jednoklientských a víceklientských aplikací mimo vlastnosti, které nelze číst v žádné situaci, jako jsou pověření.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

Uděluje stejná oprávnění jako microsoft.directory/applications/allProperties/read, ale pouze pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Uděluje možnost číst vlastníky vlastnosti na jednotenanta a více klienta aplikace. Uděluje přístup ke všem polím na stránce vlastníků registrace aplikace:

![Tato oprávnění udělují přístup na stránku vlastníků registrace aplikací.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>Microsoft.directory/applications/standard/read

Uděluje přístup ke standardním vlastnostem registrace aplikace pro čtení. To zahrnuje vlastnosti napříč stránkami registrace aplikace.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

Uděluje stejná oprávnění jako microsoft.directory/applications/standard/read, ale pouze pro aplikace s jedním tenantem.

### <a name="update"></a>Aktualizace

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft.directory/applications/allProperties/update

Možnost aktualizovat všechny vlastnosti v aplikacích s jedním adresářem a více adresáři.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

Uděluje stejná oprávnění jako microsoft.directory/applications/allProperties/update, ale pouze pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Možnost aktualizovat vlastnost typu podporovaného účtu (signInAudience) v aplikacích s jedním adresářem a s více adresáři.

![Toto oprávnění uděluje přístup k vlastnosti typu podporovaného účtu pro registraci aplikace na stránce ověřování.](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

Uděluje stejná oprávnění jako microsoft.directory/applications/audience/update, ale pouze pro aplikace s jedním tenantem.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Možnost aktualizovat adresu URL odpovědi, odhlašovací adresu URL, implicitní tok a vlastnosti domény vydavatele v aplikacích s jedním klientem a více klienty. Uděluje přístup ke všem polím na stránce ověřování registrace aplikace s výjimkou podporovaných typů účtů:

![Uděluje přístup k ověřování registrace aplikací, ale nepodporovaným typům účtů.](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Uděluje stejná oprávnění jako microsoft.directory/applications/authentication/update, ale pouze pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft.directory/applications/basic/update

Možnost aktualizovat název, logo, adresu URL domovské stránky, adresu URL služby a vlastnosti adresy URL prohlášení o zásadách ochrany osobních údajů v aplikacích s jedním tenantem a více klienty. Uděluje přístup ke všem polím na stránce značky registrace aplikace:

![Toto oprávnění uděluje přístup na stránku značky registrace aplikací.](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

Uděluje stejná oprávnění jako microsoft.directory/applications/basic/update, ale pouze pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>Microsoft.directory/applications/credentials/update

Možnost aktualizovat certifikáty a vlastnosti tajných kódů klienta v aplikacích s jedním klientem a více klienty. Uděluje přístup ke všem polím na stránce & osvědčení o registraci aplikace:

![Toto oprávnění uděluje přístup k certifikátům registrace aplikací & stránce tajných kódů.](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

Uděluje stejná oprávnění jako microsoft.directory/applications/credentials/update, ale pouze pro aplikace s jedním adresářem.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>soubor microsoft.directory/applications/owners/update

Možnost aktualizovat vlastnost vlastníka na jednotenanta a více klientů. Uděluje přístup ke všem polím na stránce vlastníků registrace aplikace:

![Tato oprávnění udělují přístup na stránku vlastníků registrace aplikací.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

Uděluje stejná oprávnění jako microsoft.directory/applications/owners/update, ale pouze pro aplikace s jedním tenantem.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>soubor microsoft.directory/applications/permissions/update

Možnost aktualizovat delegovaná oprávnění, oprávnění aplikací, autorizované klientské aplikace, požadovaná oprávnění a udělit vlastnosti souhlasu v aplikacích s jedním klientem a více klienty. Neuděluje možnost souhlasu. Uděluje přístup ke všem polím v oprávněních rozhraní API pro registraci aplikací a vystavit stránky rozhraní API:

![Tato oprávnění udělují přístup ke stránce oprávnění rozhraní API pro registraci aplikací.](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Tato oprávnění udělují přístup k registraci aplikace Vystavit stránku rozhraní API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

Uděluje stejná oprávnění jako microsoft.directory/applications/permissions/update, ale pouze pro aplikace s jedním klientem.

## <a name="required-license-plan"></a>Požadovaný licenční plán

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další kroky

- Vytváření vlastních rolí pomocí [portálu Azure Portal, Azure AD PowerShell a rozhraní GRAPH API](roles-create-custom.md)
- [Zobrazení přiřazení pro vlastní roli](roles-view-assignments.md)

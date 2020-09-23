---
title: Oprávnění vlastní role pro registraci aplikací – Azure AD | Microsoft Docs
description: Delegovat oprávnění role vlastního správce pro správu registrací aplikací
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 624489033097c0da4d85488b7ae376c5e0f3a56b
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967687"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Oprávnění k registraci aplikací pro vlastní role v Azure Active Directory

Tento článek obsahuje aktuálně dostupná oprávnění k registraci aplikací pro vlastní definice rolí v Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-tenant-applications"></a>Oprávnění pro správu aplikací pro jednoho tenanta

Při volbě oprávnění pro vlastní roli máte možnost udělit přístup pro správu pouze jednoho klienta aplikace. aplikace s jedním klientem jsou k dispozici pouze uživatelům v organizaci Azure AD, kde je aplikace zaregistrovaná. aplikace s jedním klientem jsou definované jako **podporované typy účtů** nastavené na účty pouze v tomto organizačním adresáři. V Graph API mají jednotlivé klientské aplikace vlastnost signInAudience nastavenou na hodnotu "AzureADMyOrg".

Chcete-li udělit přístup ke správě pouze jednoho klienta aplikace, použijte níže uvedená oprávnění u podtypu **Applications. myOrganization**. Například Microsoft. Directory/Applications. myOrganization/Basic/Update.

V tématu [Přehled vlastních rolí](roles-custom-overview.md) najdete vysvětlení významu podtypu, oprávnění a sady vlastností obecných podmínek. Následující informace jsou specifické pro registraci aplikací.

### <a name="create-and-delete"></a>Vytvořit a odstranit

Existují dvě oprávnění, která jsou k dispozici pro udělení možnosti vytvářet registrace aplikací, z nichž každá má jiné chování:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>Microsoft. Directory/Applications/createAsOwner

Když se tato oprávnění přiřadí, přiřadí se autor jako první vlastník vytvořené registrace aplikace a registrace vytvořené aplikace se bude počítat s kvótou vytvořených objektů tvůrce 250.

#### <a name="microsoftdirectoryapplicationscreate"></a>Microsoft. Directory/aplikace/vytvořit

Když se tato oprávnění přiřadí, nepřidá se tvůrce jako první vlastník vytvořené registrace aplikace a registrace vytvořené aplikace se nebude počítat s kvótou vytvořených objektů tvůrce 250. Toto oprávnění používejte opatrně, protože nebrání žádnému neoprávněnému nabyvateli v vytváření registrací aplikací, dokud se nedosáhne kvóty na úrovni adresáře. Pokud jsou přiřazena obě oprávnění, má tato oprávnění přednost.

Pokud jsou přiřazena obě oprávnění, bude mít přednost oprávnění/CREATE. I když oprávnění/createAsOwner nepřidá tvůrce automaticky jako prvního vlastníka, můžou se vlastníci zadat během vytváření registrace aplikace při použití rozhraní Graph API nebo rutin PowerShellu.

Oprávnění CREATE udělte přístup k příkazu **New Registration** .

[Tato oprávnění udělují přístup k novému registračnímu portálu příkazu](./media/roles-create-custom/new-custom-role.png)

Existují dvě oprávnění, která jsou k dispozici pro udělení možnosti odstraňovat registrace aplikací:

#### <a name="microsoftdirectoryapplicationsdelete"></a>Microsoft. Directory/aplikace/odstranit

Udělí možnost odstraňovat registrace aplikací bez ohledu na podtyp; To znamená jeden tenant i víceklientské aplikace.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. myOrganization/DELETE

Udělí možnost odstraňovat registrace aplikací omezené na ty, které jsou přístupné jenom účtům ve vaší organizaci nebo aplikacích s jedním klientem (myOrganization podtyp).

![Tato oprávnění udělují přístup k příkazu Odstranit registraci aplikace](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Při přiřazování role, která obsahuje oprávnění k vytvoření, je nutné přiřazení role provést v oboru adresáře. Oprávnění vytvořit přiřazené v oboru prostředku neuděluje možnost vytvářet registrace aplikací.

### <a name="read"></a>Číst

Všichni členové členských uživatelů v organizaci můžou ve výchozím nastavení číst informace o registraci aplikace. Uživatelé typu Host a instanční objekty aplikace ale nemůžou. Pokud plánujete přiřadit roli uživateli nebo aplikaci typu Host, musíte zahrnout příslušná oprávnění ke čtení.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/Applications/allProperties/Read

Možnost číst všechny vlastnosti jednoho klienta a víceklientské aplikace mimo vlastnosti, které nelze číst v jakékoli situaci, jako jsou přihlašovací údaje.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. myOrganization/allProperties/Read

Udělí stejná oprávnění jako Microsoft. Directory/Applications/allProperties/Read, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/aplikace/vlastníci/čtení

Uděluje možnost číst vlastníky v aplikacích pro jednoho tenanta a víceklientské aplikace. Uděluje přístup ke všem polím na stránce vlastníci registrace aplikace:

![Tato oprávnění udělují přístup k stránce vlastníci registrace aplikací.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>Microsoft. Directory/Applications/Standard/Read

Udělí přístup ke čtení standardních vlastností registrace aplikace. To zahrnuje vlastnosti mezi stránkami pro registraci aplikací.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. myOrganization/Standard/Read

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Standard/Read, ale jenom pro aplikace s jedním klientem.

### <a name="update"></a>Aktualizace

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/allProperties/Update

Možnost aktualizovat všechny vlastnosti v aplikacích pro jednoho tenanta a víceklientské aplikace.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. myOrganization/allProperties/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/allProperties/Update, ale pouze pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>Microsoft. Directory/Applications/publikum/Update

Možnost aktualizovat vlastnost podporovaného typu účtu (signInAudience) v aplikacích s jedním klientem a víceklientské aplikace.

![Toto oprávnění uděluje přístup k vlastnosti typu účtu, který podporuje registraci aplikace na stránce ověřování.](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. myOrganization/publikum/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/publikum/Update, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>Microsoft. Directory/Applications/Authentication/Update

Možnost aktualizace adres URL odpovědi, adresy URL pro odhlášení, implicitního toku a vlastností domény vydavatele v rámci jednoho tenanta a víceklientské aplikace. Udělí přístup ke všem polím na stránce ověřování registrace aplikace s výjimkou podporovaných typů účtů:

![Udělí přístup k ověřování registrace aplikace, ale ne k podporovaným typům účtů.](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. myOrganization/Authentication/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Authentication/Update, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft. Directory/Applications/Basic/Update

Možnost aktualizace vlastností název, logo, adresa URL domovské stránky, adresa URL podmínek služby a vlastnosti adresy URL prohlášení o zásadách ochrany osobních údajů v aplikacích pro jednoho tenanta a víceklientské úrovni. Udělí přístup ke všem polím na stránce s informacemi o registraci aplikace:

![Toto oprávnění uděluje přístup k stránce s informacemi o registraci aplikace.](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. myOrganization/Basic/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Basic/Update, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace

Možnost aktualizace certifikátů a vlastností tajných kódů klienta v aplikacích s jedním klientem a víceklientské aplikace. Udělí přístup ke všem polím na stránce certifikáty pro registraci aplikací & tajných kódů:

![Toto oprávnění uděluje přístup k registračním certifikátům aplikací & stránce tajných klíčů.](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. myOrganization/přihlašovací údaje/aktualizace

Udělí stejná oprávnění jako Microsoft. Directory/Applications/přihlašovací údaje/aktualizace, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>Microsoft. Directory/aplikace/vlastníci/aktualizace

Možnost aktualizovat vlastnost Owner v jednom tenantovi a víceklientském tenantovi. Uděluje přístup ke všem polím na stránce vlastníci registrace aplikace:

![Tato oprávnění udělují přístup k stránce vlastníci registrace aplikací.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. myOrganization/Owners/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Owners/Update, ale jenom pro aplikace s jedním klientem.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>Microsoft. Directory/aplikace/oprávnění/aktualizace

Možnost aktualizovat delegovaná oprávnění, oprávnění aplikace, autorizované klientské aplikace, požadovaná oprávnění a udělit vlastnosti souhlasu pro jeden tenant a víceklientské aplikace. Neuděluje možnost provádět souhlas. Uděluje přístup všem polím v rozhraní API pro registraci aplikace a zpřístupňuje stránky rozhraní API:

![Tato oprávnění udělují přístup ke stránce oprávnění rozhraní API pro registraci aplikace.](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Tato oprávnění udělují přístup k registraci aplikace zobrazit stránku rozhraní API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. myOrganization/oprávnění/Update

Udělí stejná oprávnění jako Microsoft. Directory/Applications/Permissions/Update, ale jenom pro aplikace s jedním klientem.

## <a name="required-license-plan"></a>Požadovaný licenční plán

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další kroky

- Vytváření vlastních rolí pomocí [Azure Portal, Azure AD PowerShellu a Graph API](roles-create-custom.md)
- [Zobrazení přiřazení pro vlastní roli](roles-view-assignments.md)

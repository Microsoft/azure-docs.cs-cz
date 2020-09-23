---
title: Oprávnění aplikací pro vlastní role v Azure Active Directory | Microsoft Docs
description: Ukázková oprávnění podnikových aplikací pro vlastní role Azure AD v Azure Portal, PowerShellu nebo Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 1d196ea33eafbfae0d9db68588c0adb131a383f4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "91000954"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Oprávnění podnikových aplikací pro vlastní role v Azure Active Directory

Tento článek obsahuje aktuálně dostupná oprávnění podnikové aplikace pro definice vlastních rolí v Azure Active Directory (Azure AD). V tomto článku najdete seznam oprávnění pro některé běžné scénáře a úplný seznam oprávnění k podnikovým aplikacím. Oprávnění proxy aplikací nejsou v této verzi aktuálně zahrnuta.

## <a name="required-license-plan"></a>Požadovaný licenční plán

Použití této funkce vyžaduje licenci Azure AD Premium P1 pro vaši organizaci Azure AD. Pokud chcete najít správnou licenci pro vaše požadavky, přečtěte si [porovnání obecně dostupných funkcí edic Free, Basic a Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enterprise-application-permissions"></a>Oprávnění pro podnikovou aplikaci

Další informace o tom, jak tato oprávnění použít, najdete v tématu [přiřazení vlastních rolí pro správu podnikových aplikací](custom-enterprise-apps.md) .

### <a name="assigning-users-or-groups-to-an-application"></a>Přiřazení uživatelů nebo skupin k aplikaci

Delegování přiřazení uživatelů a skupin, které mají přístup k aplikacím jednotného přihlašování založené na SAML. Požadovaná oprávnění

- Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update

### <a name="creating-gallery-applications"></a>Vytváření aplikací galerie

Pro delegování vytváření aplikací Galerie Azure AD, jako je ServiceNow, F5, Salesforce, mimo jiné. Požadovaná oprávnění:

- Microsoft. Directory/applicationTemplates/instance

### <a name="configuring-basic-saml-urls"></a>Konfigurace základních adres URL SAML

Pro delegování aktualizace a čtení základních konfigurací SAML pro aplikace jednotného přihlašování založené na SAML. Požadovaná oprávnění:

- Microsoft. Directory/servicePrincipals/Authentication/Update
- Microsoft. Directory/Applications. myOrganization/Authentication/Update

### <a name="rolling-over-or-creating-signing-certs"></a>Převracení nebo vytváření podpisových certifikátů

Delegování správy podpisových certifikátů pro aplikace jednotného přihlašování založené na SAML. Jsou vyžadována oprávnění.

Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>Přihlašovací e-mailová adresa pro potvrzení vypršení platnosti aktualizace

Pro delegování aktualizace e-mailových adres oznámení o vypršení platnosti přihlášení pro aplikace jednotného přihlašování založené na SAML. Požadovaná oprávnění:

- Microsoft. Directory/Applications. myOrganization/Authentication/Update
- Microsoft. Directory/Applications. myOrganization/oprávnění/Update
- Microsoft. Directory/servicePrincipals/Authentication/Update
- Microsoft. Directory/servicePrincipals/Basic/Update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>Spravovat signaturu tokenu SAML a algoritmus přihlášení

K delegování aktualizace signatury tokenu SAML a algoritmu přihlášení pro aplikace jednotného přihlašování založené na SAML Požadovaná oprávnění:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. Directory/Applications/Authentication/Update
- Microsoft. Directory/servicePrincipals/policies/Update

### <a name="manage-user-attributes-and-claims"></a>Správa uživatelských atributů a deklarací identity

Pro delegování vytváření, odstraňování a aktualizace atributů a deklarací uživatelů pro aplikace jednotného přihlašování založené na SAML. Požadovaná oprávnění:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. Directory/Applications/Authentication/Update
- Microsoft. Directory/servicePrincipals/policies/Update

## <a name="app-provisioning-permissions"></a>Oprávnění k zřizování aplikací

Provádění všech operací zápisu, jako je Správa úlohy, schématu nebo přihlašovacích údajů prostřednictvím uživatelského rozhraní, bude také vyžadovat oprávnění číst k zobrazení stránky zřizování.

Nastavení oboru pro všechny uživatele a skupiny nebo přiřazení uživatelé a skupiny v současnosti vyžaduje oprávnění synchronizationJob a synchronizationCredentials.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Zapnout nebo znovu spustit úlohy zřizování

Chcete-li delegovat schopnost zapnout a vypnout úlohy zřizování a znovu je spustit. Požadovaná oprávnění:

- Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage  

### <a name="configure-the-provisioning-schema"></a>Konfigurace zřizovacího schématu  

Pro delegování aktualizací mapování atributů. Požadovaná oprávnění:

- Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Čtení nastavení zřizování přidružených k objektu aplikace

Chcete-li delegovat schopnost číst nastavení zřizování přidružená k objektu. Požadovaná oprávnění:

- Microsoft. Directory/aplikace/synchronizace/standardní/číst

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Čtení nastavení zřizování přidružených k vašemu instančnímu objektu

Chcete-li delegovat schopnost číst nastavení zřizování přidružená k vašemu instančnímu objektu. Požadovaná oprávnění:

- Microsoft. Directory/servicePrincipals/Synchronization/Standard/Read

### <a name="authorize-application-access-for-provisioning"></a>Autorizovat přístup k aplikaci pro zřizování  

Chcete-li delegovat schopnost povolit přístup k aplikaci pro zřizování. Příklad vstupního nosných tokenů OAuth Požadovaná oprávnění:

- Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage

## <a name="full-list-of-permissions"></a>Úplný seznam oprávnění

Oprávnění | Description
---------- | -----------
Microsoft. Directory/applicationPolicies/allProperties/Read | Načte všechny vlastnosti pro zásady použití.
Microsoft. Directory/applicationPolicies/allProperties/Update | Aktualizuje všechny vlastnosti pro zásady použití.
Microsoft. Directory/applicationPolicies/Basic/Update | Aktualizuje standardní vlastnosti zásad použití.
Microsoft. Directory/applicationPolicies/Create | Vytvořte zásady použití.
Microsoft. Directory/applicationPolicies/createAsOwner | Vytvořte zásady použití. Tvůrce se přidá jako první vlastník.
Microsoft. Directory/applicationPolicies/DELETE | Odstraňte zásady použití.
Microsoft. Directory/applicationPolicies/Owners/Read | Čtení vlastníků zásad použití
Microsoft. Directory/applicationPolicies/Owners/Update | Aktualizujte vlastnost Owner zásad použití.
Microsoft. Directory/applicationPolicies/policyAppliedTo/Read | Načte zásady aplikace použité na seznam objektů.
Microsoft. Directory/applicationPolicies/Standard/Read | Načte standardní vlastnosti zásad použití.
Microsoft. Directory/servicePrincipals/allProperties/allTasks | Vytvořit a odstranit servicePrincipals a číst a aktualizovat všechny vlastnosti v Azure Active Directory.
Microsoft. Directory/servicePrincipals/allProperties/Read | Načte všechny vlastnosti v servicePrincipals.
Microsoft. Directory/servicePrincipals/allProperties/Update | Aktualizuje všechny vlastnosti v servicePrincipals.
Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Read | Načte přiřazení rolí instančního objektu.
Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizuje přiřazení rolí instančního objektu.
Microsoft. Directory/servicePrincipals/appRoleAssignments/Read | Umožňuje číst přiřazení rolí přiřazená k instančním objektům.
Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizuje vlastnosti cílové skupiny u instančních objektů.
Microsoft. Directory/servicePrincipals/Authentication/Read |  
Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizuje vlastnosti ověřování u instančních objektů.
Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizuje základní vlastnosti objektů služby.
Microsoft. Directory/servicePrincipals/Create | Vytváření instančních objektů.
Microsoft. Directory/servicePrincipals/createAsOwner | Vytváření instančních objektů. Tvůrce se přidá jako první vlastník.
Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizuje vlastnosti přihlašovacích údajů u instančních objektů.
Microsoft. Directory/servicePrincipals/DELETE | Odstraňte objekty služby.
Microsoft. Directory/servicePrincipals/Disable | Zakažte objekty služby.
Microsoft. Directory/servicePrincipals/Enable | Povolit instanční objekty.
Microsoft. Directory/servicePrincipals/getPasswordSingleSignOnCredentials | Číst přihlašovací údaje jednotného přihlašování k objektům služby.
Microsoft. Directory/servicePrincipals/managePasswordSingleSignOnCredentials | Spravujte přihlašovací údaje jednotného přihlašování k heslům u instančních objektů.
Microsoft. Directory/servicePrincipals/oAuth2PermissionGrants/Read | Číst delegovaná oprávnění udělují instanční objekty.
Microsoft. Directory/servicePrincipals/Owners/Read | Čtení vlastníků u instančních objektů.
Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizuje vlastníky u instančních objektů.
Microsoft. Directory/servicePrincipals/oprávnění/aktualizace |  
Microsoft. Directory/servicePrincipals/policies/Read | Číst zásady pro instanční objekty.
Microsoft. Directory/servicePrincipals/policies/Update | Aktualizujte zásady u instančních objektů.
Microsoft. Directory/servicePrincipals/Standard/Read | Načte standardní vlastnosti objektů služby.
Microsoft. Directory/servicePrincipals/Synchronization/Standard/Read | Přečtěte si nastavení zřizování přidružená k vašemu instančnímu objektu.
Microsoft. Directory/servicePrincipals/tag/Update | Aktualizuje vlastnost Tags u instančních objektů.
Microsoft. Directory/applicationTemplates/instance | Vytvoří instanci aplikací galerie z šablon aplikací.
Microsoft. Directory/auditLogs/allProperties/Read | Čtení protokolů auditu.
Microsoft. Directory/signInReports/allProperties/Read | Čtení sestav přihlášení.
Microsoft. Directory/aplikace/synchronizace/standardní/číst | Přečtěte si nastavení zřizování přidružená k objektu aplikace.
Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Správa všech aspektů synchronizace úloh pro hlavní prostředky služby
Microsoft. Directory/servicePrincipals/Synchronization/Standard/Read | Čtení nastavení zřizování přidružených k instančním objektům
Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Správa všech aspektů synchronizace schématu pro hlavní prostředky služby
Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování.

## <a name="next-steps"></a>Další kroky

- [Vytváření vlastních rolí pomocí Azure Portal, Azure AD PowerShellu a Graph API](roles-create-custom.md)
- [Zobrazení přiřazení pro vlastní roli](roles-view-assignments.md)

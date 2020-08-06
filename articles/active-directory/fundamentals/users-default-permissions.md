---
title: Výchozí uživatelská oprávnění – Azure Active Directory | Microsoft Docs
description: Přečtěte si o různých uživatelských oprávněních, která jsou k dispozici v Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bae802d8aa9378155bcca0713992a8cc041ea1a9
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799017"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Jaká jsou výchozí uživatelská oprávnění v Azure Active Directory?
V Azure Active Directory (Azure AD) mají všichni uživatelé udělenou sadu výchozích oprávnění. Přístup uživatele se skládá z typu uživatel, [přiřazení jejich rolí](active-directory-users-assign-role-azure-portal.md)a jejich vlastnictví jednotlivých objektů. Tento článek popisuje tato výchozí oprávnění a obsahuje porovnání výchozích nastavení člena a uživatele typu host. Výchozí uživatelská oprávnění se dají změnit jenom v nastavení uživatele v Azure AD.

## <a name="member-and-guest-users"></a>Členové a uživatelé typu host
Sada výchozích oprávnění přijatá závisí na tom, jestli je uživatel nativním členem tenanta (členský uživatel), nebo jestli je uživatel převzatý z jiného adresáře jako host spolupráce B2B (uživatel typu Host). Další informace o přidávání uživatelů typu Host najdete v tématu [co je spolupráce Azure AD B2B](../b2b/what-is-b2b.md) .
* Členové můžou registrovat aplikace, spravovat vlastní profilovou fotku a číslo mobilního telefonu, změnit vlastní heslo a zvát hosty B2B. Kromě toho můžou uživatelé číst všechny informace v adresáři (s několika výjimkami). 
* Uživatelé typu Host mají omezená oprávnění k adresáři. Uživatelé typu host například nemůžou procházet informace z tenanta nad rámec informací o vlastním profilu. Uživatel typu host však může načíst informace o jiném uživateli tím, že zadá hlavní název uživatele (UPN) nebo identifikátor objectID. Uživatel typu Host může číst vlastnosti skupin, do kterých patří, včetně členství ve skupině, bez ohledu na **oprávnění uživatelů typu Host** , a to i v případě omezeného nastavení. Host nemůže zobrazit informace o ostatních objektech tenanta.

Výchozí oprávnění pro hosty jsou ve výchozím nastavení omezující. Hosty je možné přidat do rolí správce, které jim udělí úplná oprávnění ke čtení a zápisu obsažená v dané roli. K dispozici je ještě jedno omezení – možnost hostů zvát jiné hosty. Nastavením možnosti **Hosté můžou posílat pozvánky** na hodnotu **Ne** zabráníte hostům zvát jiné hosty. Informace o postupu najdete v tématu [Delegování pozvánek pro spolupráci B2B](../b2b/delegate-invitations.md). Pokud chcete uživatelům typu host udělit stejná oprávnění, jako mají členové ve výchozím nastavení, nastavte možnost **Oprávnění uživatelů typu host jsou omezená** na hodnotu **Ne**. Toto nastavení standardně udělí uživatelům typu host všechna uživatelská oprávnění členů a také povolí přidání hostů do rolí pro správu.

## <a name="compare-member-and-guest-default-permissions"></a>Porovnání výchozích oprávnění pro členy a hosty

**Oblast** | **Oprávnění člena** | **Uživatelská oprávnění hosta**
------------ | --------- | ----------
Uživatelé a kontakty | Čtení všech veřejných vlastností uživatelů a kontaktů<br>Zvaní hostů<br>Změna vlastního hesla<br>Správa vlastního čísla mobilního telefonu<br>Správa vlastní fotky<br>Zneplatnění vlastních obnovovacích tokenů | Čtení vlastních vlastností<br>Čtení zobrazovaného jména, e-mailu, přihlašovací jméno, fotografie, hlavní název uživatele a vlastnosti typu uživatele u dalších uživatelů a kontaktů<br>Změna vlastního hesla
Skupiny | Vytváření skupin zabezpečení<br>Vytváření skupin Office 365<br>Čtení všech vlastností skupin<br>Čtení neskrytých členství ve skupinách<br>Čtení skrytých členství ve skupinách Office 365 u připojené skupiny<br>Správa vlastností, vlastnictví a členství ve skupinách, které uživatel vlastní<br>Přidávání hostů do vlastněných skupin<br>Správa nastavení dynamického členství<br>Odstranění vlastněných skupin<br>Obnovení vlastněných skupin Office 365 | Čtení všech vlastností skupin<br>Čtení neskrytých členství ve skupinách<br>Čtení skrytých členství ve skupinách Office 365 u připojených skupin<br>Správa vlastněných skupin<br>Přidávání hostů do vlastněných skupin (pokud je to povoleno)<br>Odstranění vlastněných skupin<br>Obnovení vlastněných skupin Office 365<br>Umožňuje číst vlastnosti skupin, do kterých patří, včetně členství.
Aplikace | Registrace (vytvoření) nové aplikace<br>Čtení vlastností zaregistrovaných a podnikových aplikací<br>Správa vlastností aplikací, jejich přiřazení a přihlašovacích údajů u vlastněných aplikací<br>Vytvoření nebo odstranění hesla aplikace pro uživatele<br>Odstranění vlastněných aplikací<br>Obnovení vlastněných aplikací | Čtení vlastností zaregistrovaných a podnikových aplikací<br>Správa vlastností aplikací, jejich přiřazení a přihlašovacích údajů u vlastněných aplikací<br>Odstranění vlastněných aplikací<br>Obnovení vlastněných aplikací
Zařízení | Čtení všech vlastností zařízení<br>Správa všech vlastností vlastněných zařízení<br> | Žádná oprávnění<br>Odstranění vlastněných zařízení<br>
Adresář | Čtení všech informací o společnosti<br>Čtení všech domén<br>Čtení všech partnerských kontraktů | Čtení zobrazovaného názvu a ověřených domén
Role a obory | Čtení všech rolí pro správu a členství v nich<br>Čtení všech vlastností a členství jednotek pro správu | Žádná oprávnění 
Předplatná | Čtení všech předplatných<br>Povolení člena plánu služby | Žádná oprávnění
Zásady | Čtení všech vlastností zásad<br>Správa všech vlastností vlastněných zásad | Žádná oprávnění

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Omezení výchozích oprávnění pro členy

Výchozí oprávnění pro členy je možné omezit následujícími způsoby.

Oprávnění | Vysvětlení nastavení
---------- | ------------
Uživatelé můžou zaregistrovat aplikaci. | Nastavením této možnosti na možnost Ne znemožníte uživatelům vytvářet registrace aplikací. Tuto možnost je pak možné udělit konkrétním jednotlivcům, když je přidáte do role vývojář aplikace.
Umožňuje uživatelům připojit pracovní nebo školní účet pomocí LinkedInu. | Nastavením této možnosti na ne znemožníte uživatelům připojit svůj pracovní nebo školní účet ke svému účtu LinkedIn. Další informace najdete v tématu [připojení k účtu LinkedIn – sdílení dat a jejich souhlas](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Možnost vytvářet skupiny zabezpečení | Nastavení této možnosti na hodnotu Ne zabrání uživatelům vytvářet skupiny zabezpečení. Globální správci a správci uživatelů stále můžou vytvářet skupiny zabezpečení. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md).
Možnost vytvářet skupiny Office 365 | Nastavení této možnosti na hodnotu Ne zabrání uživatelům vytvářet skupiny Office 365. Nastavení této možnosti na hodnotu Někteří umožní vytvářet skupiny Office 365 vybrané skupině uživatelů. Globální správci a správci uživatelů budou stále moci vytvářet skupiny Office 365. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md).
Omezení přístupu k portálu pro správu Azure AD | Nastavením této možnosti na Ne umožníte, aby uživatelé bez oprávnění správce mohli číst a spravovat prostředky Azure AD pomocí portálu pro správu Azure AD. Hodnota Ano omezí přístup všech uživatelů bez oprávnění správce k žádným datům Azure AD na portálu pro správu. Důležité Poznámka: Toto nastavení neomezuje přístup k datům Azure AD pomocí PowerShellu nebo jiných klientů, jako je například Visual Studio. Pokud nastavíte Ano, udělíte konkrétnímu uživateli bez oprávnění správce možnost používat portál pro správu Azure AD, přiřazovat jakékoli administrativní role, jako je například role čtenáři adresáře. Tato role umožňuje čtení základních informací o adresáři, které členské uživatelé mají ve výchozím nastavení (hosté a instanční objekty nedělají).
Možnost číst ostatní uživatele | Toto nastavení je k dispozici pouze v PowerShellu. Nastavením tohoto příznaku $false znemožníte čtení informací o uživatelích z adresáře všem jiným uživatelům než správcům. Tento příznak nebrání čtení informací o uživateli v jiných službách Microsoftu, jako je Exchange Online. Toto nastavení je určeno pro zvláštní okolnosti a nastavení tohoto příznaku na $false se nedoporučuje.

## <a name="object-ownership"></a>Vlastnictví objektů

### <a name="application-registration-owner-permissions"></a>Oprávnění vlastníka registrace aplikace
Když uživatel zaregistruje aplikaci, automaticky se přidá jako vlastník této aplikace. Jako vlastník může spravovat metadata aplikace, například její název a oprávnění, která aplikace vyžaduje. Může spravovat také konfiguraci aplikace specifickou pro tenanta, jako je konfigurace jednotného přihlašování a přiřazení uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců můžou vlastníci spravovat pouze aplikace, které vlastní.

### <a name="enterprise-application-owner-permissions"></a>Oprávnění vlastníka podnikové aplikace
Když uživatel přidá novou podnikovou aplikaci, automaticky se přidá jako vlastník. Jako vlastník můžou spravovat konfiguraci aplikace specifickou pro tenanta, jako je například konfigurace jednotného přihlašování, zřizování a přiřazování uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců můžou vlastníci spravovat jenom aplikace, které vlastní.

### <a name="group-owner-permissions"></a>Oprávnění vlastníka skupiny
Když uživatel vytvoří skupinu, automaticky se přidá jako vlastník této skupiny. Jako vlastník mohou spravovat vlastnosti skupiny, jako je název, a také spravovat členství ve skupině. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců a správců uživatelů můžou vlastníci spravovat jenom skupiny, které vlastní. Pokud chcete přiřadit vlastníka skupiny, přečtěte si téma [Správa vlastníků skupiny](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Oprávnění vlastnictví
Následující tabulky popisují specifická oprávnění v Azure Active Directory členské uživatele mají nad vlastními objekty. Uživatel má tato oprávnění pouze pro objekty, které vlastní.

#### <a name="owned-application-registrations"></a>Vlastní registrace aplikací
Uživatelé mohou provádět následující akce při registraci vlastní aplikace.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Applications/publikum/Update | Aktualizuje vlastnost Applications. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/Applications/Authentication/Update | Aktualizuje vlastnost Applications. Authentication v Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Aktualizuje základní vlastnosti pro aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizuje vlastnost Applications. credentialss v Azure Active Directory. |
| Microsoft. Directory/aplikace/odstranit | Odstraní aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizuje vlastnost Applications. Owners v Azure Active Directory. |
| Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizuje vlastnost Applications. Permissions v Azure Active Directory. |
| Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/aplikace/obnovení | Obnovte aplikace v Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Vlastněné podnikové aplikace
Uživatelé můžou na vlastněných podnikových aplikacích provádět následující akce. Podniková aplikace se skládá z instančního objektu, jedné nebo více zásad použití a někdy aplikace ve stejném tenantovi jako instanční objekt.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/policies/Basic/Update | Aktualizuje základní vlastnosti zásad v Azure Active Directory. |
| Microsoft. Directory/policies/DELETE | Odstraňte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/Owners/Update | V Azure Active Directory vlastnost Update policies. Owners. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizuje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizuje vlastnost servicePrincipals. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizuje vlastnost servicePrincipals. Authentication v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizuje základní vlastnosti v servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizuje vlastnost servicePrincipals. credentialss v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/DELETE | Odstraní servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizuje vlastnost servicePrincipals. Owners v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizujte vlastnost servicePrincipals. Permissions v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/policies/Update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |

#### <a name="owned-devices"></a>Vlastněná zařízení
Uživatelé můžou na vlastněných zařízeních provádět následující akce.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Číst vlastnost Devices. bitLockerRecoveryKeys v Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Zakáže zařízení v Azure Active Directory. |

#### <a name="owned-groups"></a>Vlastněné skupiny
Uživatelé mohou provádět následující akce u vlastněných skupin.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Aktualizuje vlastnost groups. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Aktualizuje základní vlastnosti pro skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/DELETE | Odstraní skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/dynamicMembershipRule/Update | Aktualizuje vlastnost groups. dynamicMembershipRule v Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| Microsoft. Directory/skupiny/obnovení | Obnovte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizuje vlastnost groups. Settings v Azure Active Directory. |

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak přiřadit role správce Azure AD, najdete v tématu [přiřazení uživatele k rolím Správce v Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Správa uživatelů](add-users-azure-active-directory.md)

---
title: Výchozí uživatelská oprávnění – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Přečtěte si o různých uživatelských oprávněních dostupných ve službě Azure Active Directory.
services: active-directory
author: msaburnley
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
ms.openlocfilehash: 227230f2d6f46fae27e2cec69d99390f5054c7db
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366252"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Jaká jsou výchozí uživatelská oprávnění ve službě Azure Active Directory?
V Azure Active Directory (Azure AD) mají všichni uživatelé udělenou sadu výchozích oprávnění. Přístup uživatele se skládá z typu uživatele, jejich [přiřazení rolí](active-directory-users-assign-role-azure-portal.md)a jejich vlastnictví jednotlivých objektů. Tento článek popisuje tato výchozí oprávnění a obsahuje porovnání výchozích nastavení člena a uživatele typu host. Výchozí uživatelská oprávnění lze změnit jenom v uživatelských nastaveních ve službě Azure AD.

## <a name="member-and-guest-users"></a>Členové a uživatelé typu host
Sada přijatých výchozích oprávnění závisí na tom, zda je uživatel nativním členem tenanta (členský uživatel) nebo zda je uživatel přenesen z jiného adresáře jako host spolupráce B2B (hostovaný uživatel). Další informace o přidávání uživatelů typu Host najdete v [tématu Co je spolupráce Azure AD B2B?](../b2b/what-is-b2b.md)
* Členové můžou registrovat aplikace, spravovat vlastní profilovou fotku a číslo mobilního telefonu, změnit vlastní heslo a zvát hosty B2B. Kromě toho můžou uživatelé číst všechny informace v adresáři (s několika výjimkami). 
* Uživatelé typu Host mají omezená oprávnění adresáře. Uživatelé typu host například nemůžou procházet informace z tenanta nad rámec informací o vlastním profilu. Uživatel typu host však může načíst informace o jiném uživateli tím, že zadá hlavní název uživatele (UPN) nebo identifikátor objectID. Uživatel typu Host může číst vlastnosti skupin, do kterých patří, včetně členství ve skupině, bez ohledu na **to,** že oprávnění uživatelů typu Host mají omezené nastavení. Host nemůže zobrazit informace o jiných objektech klienta.

Výchozí oprávnění pro hosty jsou ve výchozím nastavení omezující. Hosty je možné přidat do rolí správce, které jim udělí úplná oprávnění ke čtení a zápisu obsažená v dané roli. K dispozici je ještě jedno omezení – možnost hostů zvát jiné hosty. Nastavením možnosti **Hosté můžou posílat pozvánky** na hodnotu **Ne** zabráníte hostům zvát jiné hosty. Informace o postupu najdete v tématu [Delegování pozvánek pro spolupráci B2B](../b2b/delegate-invitations.md). Pokud chcete uživatelům typu host udělit stejná oprávnění, jako mají členové ve výchozím nastavení, nastavte možnost **Oprávnění uživatelů typu host jsou omezená** na hodnotu **Ne**. Toto nastavení standardně udělí uživatelům typu host všechna uživatelská oprávnění členů a také povolí přidání hostů do rolí pro správu.

## <a name="compare-member-and-guest-default-permissions"></a>Porovnání výchozích oprávnění pro členy a hosty

**Oblast** | **Uživatelská oprávnění člena** | **Uživatelská oprávnění hosta**
------------ | --------- | ----------
Uživatelé a kontakty | Čtení všech veřejných vlastností uživatelů a kontaktů<br>Zvaní hostů<br>Změna vlastního hesla<br>Správa vlastního čísla mobilního telefonu<br>Správa vlastní fotky<br>Zneplatnění vlastních obnovovacích tokenů | Čtení vlastních vlastností<br>Čtení zobrazovaného jména, e-mailu, přihlašovacího jména, fotografie, hlavního uživatelského jména a vlastností typu uživatele ostatních uživatelů a kontaktů<br>Změna vlastního hesla
Skupiny | Vytváření skupin zabezpečení<br>Vytváření skupin Office 365<br>Čtení všech vlastností skupin<br>Čtení neskrytých členství ve skupinách<br>Čtení skrytých členství ve skupinách Office 365 u připojené skupiny<br>Správa vlastností, vlastnictví a členství ve skupinách, které uživatel vlastní<br>Přidávání hostů do vlastněných skupin<br>Správa nastavení dynamického členství<br>Odstranění vlastněných skupin<br>Obnovení vlastněných skupin Office 365 | Čtení všech vlastností skupin<br>Čtení neskrytých členství ve skupinách<br>Čtení skrytých členství ve skupinách Office 365 u připojených skupin<br>Správa vlastněných skupin<br>Přidávání hostů do vlastněných skupin (pokud je to povoleno)<br>Odstranění vlastněných skupin<br>Obnovení vlastněných skupin Office 365<br>Přečtěte si vlastnosti skupin, do kterých patří, včetně členství.
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
Uživatelé mohou zaregistrovat aplikaci | Nastavení této možnosti na ne zabrání uživatelům ve vytváření registrací aplikací. Možnost pak může být udělena zpět na konkrétní jednotlivce jejich přidáním do role vývojáře aplikací.
Povolit uživatelům připojit pracovní nebo školní účet s LinkedIn | Nastavení této možnosti na ne zabrání uživatelům v připojení jejich pracovního nebo školního účtu k účtu LinkedIn. Další informace naleznete v tématu [Sdílení dat a souhlas připojení k účtu LinkedIn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Možnost vytvářet skupiny zabezpečení | Nastavení této možnosti na hodnotu Ne zabrání uživatelům vytvářet skupiny zabezpečení. Globální správci a uživatelští správci mohou stále vytvářet skupiny zabezpečení. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md).
Možnost vytvářet skupiny Office 365 | Nastavení této možnosti na hodnotu Ne zabrání uživatelům vytvářet skupiny Office 365. Nastavení této možnosti na hodnotu Někteří umožní vytvářet skupiny Office 365 vybrané skupině uživatelů. Globální správci a uživatelští správci budou stále moct vytvářet skupiny Office 365. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md).
Omezení přístupu k portálu pro správu Azure AD | Nastavení této možnosti na ne umožňuje nesprávcům používat portál pro správu Azure AD ke čtení a správě prostředků Azure AD. Ano, omezuje všechny non-správci přístup k datům Azure AD na portálu pro správu. Důležité si uvědomit: toto nastavení neomezuje přístup k datům Azure AD pomocí PowerShellu nebo jiných klientů, jako je Visual Studio. Pokud je nastavena na Ano, udělit konkrétní uživatel bez správce možnost používat portál pro správu Azure AD přiřadit libovolné role pro správu, jako je například role Čtečky adresářů. Tato role umožňuje čtení základních informací o adresáři, které mají členové uživatelé ve výchozím nastavení (hosté a instanční objekty nemají).
Možnost číst ostatní uživatele | Toto nastavení je k dispozici pouze v PowerShellu. Nastavení tohoto příznaku na $false zabrání všem uživatelům, kteří nejsou správci, ve čtení informací o uživateli z adresáře. Tento příznak nebrání čtení informací o uživateli v jiných službách společnosti Microsoft, jako je Exchange Online. Toto nastavení je určeno pro zvláštní okolnosti a nastavení tohoto příznaku na $false se nedoporučuje.

## <a name="object-ownership"></a>Vlastnictví objektů

### <a name="application-registration-owner-permissions"></a>Oprávnění vlastníka registrace aplikace
Když uživatel zaregistruje aplikaci, automaticky se přidá jako vlastník této aplikace. Jako vlastník může spravovat metadata aplikace, například její název a oprávnění, která aplikace vyžaduje. Může spravovat také konfiguraci aplikace specifickou pro tenanta, jako je konfigurace jednotného přihlašování a přiřazení uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců můžou vlastníci spravovat pouze aplikace, které vlastní.

### <a name="enterprise-application-owner-permissions"></a>Oprávnění vlastníka podnikových aplikací
Když uživatel přidá novou podnikovou aplikaci, automaticky se přidá jako vlastník. Jako vlastník mohou spravovat konfiguraci aplikace specifickou pro klienta, jako je konfigurace připřihlašování, zřizování a přiřazení uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců mohou vlastníci spravovat pouze aplikace, které vlastní.

### <a name="group-owner-permissions"></a>Oprávnění vlastníka skupiny
Když uživatel vytvoří skupinu, automaticky se přidá jako vlastník této skupiny. Jako vlastník mohou spravovat vlastnosti skupiny, jako je název, stejně jako spravovat členství ve skupině. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců a správců uživatelů mohou vlastníci spravovat pouze skupiny, které vlastní. Pokud chcete přiřadit vlastníka skupiny, přečtěte si téma [Správa vlastníků skupiny](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Oprávnění k vlastnictví
Následující tabulky popisují konkrétní oprávnění v azure active directory členové uživatelé mají přes vlastněné objekty. Uživatel má tato oprávnění pouze u objektů, které vlastní.

#### <a name="owned-application-registrations"></a>Vlastněné registrace žádostí
Uživatelé mohou provádět následující akce u vlastněných registrací aplikací.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/applications/audience/update | Aktualizujte vlastnost applications.audience ve službě Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aktualizujte vlastnost applications.authentication ve službě Azure Active Directory. |
| Microsoft.directory/applications/basic/update | Aktualizujte základní vlastnosti aplikací ve službě Azure Active Directory. |
| Microsoft.directory/applications/credentials/update | Aktualizujte vlastnost applications.credentials ve službě Azure Active Directory. |
| microsoft.directory/applications/delete | Odstraňte aplikace ve službě Azure Active Directory. |
| soubor microsoft.directory/applications/owners/update | Aktualizujte vlastnost applications.owners ve službě Azure Active Directory. |
| soubor microsoft.directory/applications/permissions/update | Aktualizujte vlastnost applications.permissions ve službě Azure Active Directory. |
| Soubor Microsoft.directory/applications/policies/update | Aktualizujte vlastnost applications.policies ve službě Azure Active Directory. |
| Microsoft.directory/applications/restore | Obnovte aplikace ve službě Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Vlastněné podnikové aplikace
Uživatelé mohou provádět následující akce u vlastněných podnikových aplikací. Podniková aplikace se skládá z instančního objektu, jedné nebo více zásad aplikace a někdy aplikačního objektu ve stejném tenantovi jako instanční objekt.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/auditLogs/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs ve službě Azure Active Directory. |
| Soubor Microsoft.directory/policies/basic/update | Aktualizujte základní vlastnosti zásad ve službě Azure Active Directory. |
| Microsoft.directory/policies/delete | Odstraňte zásady ve službě Azure Active Directory. |
| Soubor Microsoft.directory/policies/owners/update | Aktualizujte vlastnost policies.owners ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizujte vlastnost servicePrincipals.appRoleAssignedTo ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignments/update | Aktualizujte vlastnost users.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/audience/update | Aktualizujte vlastnost servicePrincipals.audience ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/authentication/update | Aktualizujte vlastnost servicePrincipals.authentication ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/basic/update | Aktualizujte základní vlastnosti na servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/credentials/update | Aktualizujte vlastnost servicePrincipals.credentials ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/delete | Odstraňte servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/owners/update | Aktualizujte vlastnost servicePrincipals.owners ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/permissions/update | Aktualizujte vlastnost servicePrincipals.permissions ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/policies/update | Aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) ve službě SignInReports ve službě Azure Active Directory. |

#### <a name="owned-devices"></a>Vlastněná zařízení
Uživatelé mohou provádět následující akce na vlastněných zařízeních.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/devices/bitLockerRecoveryKeys/read | Přečtěte si vlastnost devices.bitLockerRecoveryKeys ve službě Azure Active Directory. |
| Microsoft.directory/devices/disable Microsoft.directory/devices/disable Microsoft.directory/devices/disable Microsoft. | Zakažte zařízení ve službě Azure Active Directory. |

#### <a name="owned-groups"></a>Vlastněné skupiny
Uživatelé mohou provádět následující akce u vlastněných skupin.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Aktualizujte vlastnost groups.appRoleAssignments ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/basic/update | Aktualizujte základní vlastnosti skupin ve službě Azure Active Directory. |
| Microsoft.directory/groups/delete | Odstraňte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/dynamicMembershipRule/update | Aktualizujte vlastnost groups.dynamicMembershipRule ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/members/update | Aktualizujte vlastnost groups.members ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/owners/update | Aktualizujte vlastnost groups.owners ve službě Azure Active Directory. |
| Microsoft.directory/groups/restore | Obnovte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/settings/update | Aktualizujte vlastnost groups.settings ve službě Azure Active Directory. |

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak přiřadit role správce Azure AD, najdete [v tématu Přiřazení uživatele k rolím správce ve službě Azure Active Directory.](active-directory-users-assign-role-azure-portal.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Spravovat uživatele](add-users-azure-active-directory.md)
